<properties
    pageTitle="Azure AD Connect plusieurs domaines"
    description="Ce document décrit la configuration et la configuration de plusieurs domaines de niveau supérieur avec Office 365 et Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Prise en charge de domaine multiples pour fédérer avec Azure Active Directory
La documentation suivante fournit des conseils sur l’utilisation de plusieurs domaines de niveau supérieur et des sous-domaines lors de la fédération avec Office 365 ou domaines Azure AD.

## <a name="multiple-top-level-domain-support"></a>Prise en charge de plusieurs domaines de niveau supérieur
Domaines de niveau supérieur avec Azure AD fédérer plusieurs, nécessite une configuration supplémentaire qui n’est pas nécessaire lors de la fédération avec un domaine de niveau supérieur.

Lorsqu’un domaine fédéré avec Azure AD, plusieurs propriétés sont définies sur le domaine dans Azure.  Une seule importantes est IssuerUri.  Il s’agit d’un URI qui est utilisé par Azure AD pour identifier le domaine le jeton est associé.  L’URI n’a pas besoin résoudre vers tout sauf qu’il doit être un URI valide.  Par défaut, Azure AD définit cette valeur à la valeur de l’identificateur de service de fédération dans vos locaux AD FS configuration.

>[AZURE.NOTE]L’identificateur de service de fédération est un URI qui identifie un service de fédération.  Le service de fédération est une instance de AD FS qui fonctionne en tant que le service de jeton de sécurité. 

Vous pouvez afficher IssuerUri à l’aide de la commande PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Un problème se pose lorsque vous souhaitez ajouter plusieurs domaines de niveau supérieur.  Par exemple, supposons que votre fédération configuration entre Azure AD et votre environnement local.  Pour ce document, j’utilise bmcontoso.com.  À présent que j’ai ajouté un domaine de niveau supérieur, deuxième, bmfabrikam.com.

![Domaines](./media/active-directory-multiple-domains/domains.png)

Lorsque vous essayez de faire passer notre domaine bmfabrikam.com soit fédéré, nous recevons une erreur.  La raison est, Azure AD comporte une contrainte qui n’autorise pas la propriété IssuerUri d’avoir la même valeur pour plusieurs domaines.  
  

![Erreur de fédération](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Paramètre de SupportMultipleDomain

Pour contourner ce problème, nous devons ajouter un autre IssuerUri qui peut être effectuée à l’aide de la `-SupportMultipleDomain` paramètre.  Ce paramètre est utilisé avec les applets de commande suivantes :
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Ce paramètre rend Azure AD configurer le IssuerUri afin qu’il soit basé sur le nom du domaine.  Il s’agit unique sur annuaires dans Azure Active Directory.  En utilisant le paramètre permet à la commande PowerShell effectuer correctement.

![Erreur de fédération](./media/active-directory-multiple-domains/convert.png)

Consultant les paramètres de notre nouveau domaine bmfabrikam.com, vous pouvez voir les rubriques suivantes :

![Erreur de fédération](./media/active-directory-multiple-domains/settings.png)

Notez que `-SupportMultipleDomain` ne change pas les autres points de terminaison qui sont toujours configurées pour qu’il pointe vers notre service de fédération sur adfs.bmcontoso.com.

Une autre chose qui `-SupportMultipleDomain` quoi consiste à s’assurer que le système AD FS inclut la valeur de l’émetteur correcte dans les jetons émis pour Azure AD. Pour cela, il prendre de la partie domaine des utilisateurs UPN et ce paramètre en tant que le domaine dans la IssuerUri, c'est-à-dire https://{upn suffixe} / adfs/services/approbation. 

Par conséquent lors de l’authentification à Azure Active Directory ou Office 365, l’élément IssuerUri dans le jeton d’utilisateur est utilisé pour localiser le domaine dans Azure Active Directory.  Si aucune correspondance ne peut pas être trouvée que l’authentification échouera. 

Par exemple, si un utilisateur de UPN est bsimon@bmcontoso.com, l’élément IssuerUri en les jeton AD FS de problèmes est défini sur http://bmcontoso.com/adfs/services/trust. Cela fera correspondre la configuration Azure AD et l’authentification réussit.

Voici la règle réclamer personnalisée qui mettent en œuvre, cette logique :

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Pour pouvoir utiliser le commutateur - SupportMultipleDomain lorsque vous tentez d’ajouter de nouveaux ou convertir déjà des domaines ajoutés, vous devez est configuré votre approbation fédérée pour prendre en charge à l’origine.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Comment mettre à jour l’approbation entre AD FS et Azure AD
Si vous n’avez pas paramétré l’approbation fédérée entre AD FS et votre instance d’Azure AD, vous devrez peut-être recréer cette approbation.  C’est parce que, lorsqu’il est à l’origine le programme d’installation sans le `-SupportMultipleDomain` paramètre, le IssuerUri est défini avec la valeur par défaut.  Dans la capture d’écran ci-dessous, vous pouvez voir que la IssuerUri est défini sur https://adfs.bmcontoso.com/adfs/services/trust.

C’est le cas présent, si vous avez ajouté un nouveau domaine dans le portail Azure AD et puis tentez de convertir à l’aide de `Convert-MsolDomaintoFederated -DomainName <your domain>`, nous obtenez l’erreur suivante.

![Erreur de fédération](./media/active-directory-multiple-domains/trust1.png)

Si vous essayez d’ajouter le `-SupportMultipleDomain` commutateur nous recevront l’erreur suivante :

![Erreur de fédération](./media/active-directory-multiple-domains/trust2.png)

Il vous suffit de l’exécution `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` sur le domaine d’origine également provoquera une erreur.

![Erreur de fédération](./media/active-directory-multiple-domains/trust3.png)

Suivez les étapes ci-dessous pour ajouter un domaine de niveau supérieur supplémentaire.  Si vous avez déjà ajouté un domaine et que vous n’avez pas utilisé le `-SupportMultipleDomain` paramètre Démarrer avec la procédure de suppression et la mise à jour de votre domaine d’origine.  Si vous n’avez pas ajouté un domaine de niveau supérieur encore vous pouvez commencer avec les étapes d’ajout d’un domaine à l’aide de PowerShell d’Azure AD Connect.

Procédez comme suit pour supprimer la gestion de la confidentialité Microsoft Online et mettre à jour votre domaine d’origine.

2.  Sur votre serveur de fédération AD FS ouvrir **AD FS Management.** 
2.  Sur la gauche, développer les **Relations d’approbation** et **Approbations des parties de confiance**
3.  Sur la droite, supprimez l’entrée de **Microsoft Office 365 identité plateforme** .
![Supprimer Microsoft en ligne](./media/active-directory-multiple-domains/trust4.png)
1.  Sur un ordinateur sur lequel est installé le [Module Azure Active Directory pour Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , exécutez la procédure suivante : `$cred=Get-Credential`.  
2.  Entrez le nom d’utilisateur et mot de passe d’un administrateur global pour le domaine AD Azure que vous utilisez la fédération avec
2.  Dans PowerShell Entrez`Connect-MsolService -Credential $cred`
4.  Dans PowerShell entrez `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Il s’agit du domaine d’origine.  À l’aide de domaines ci-dessus qu'il serait :`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Suivez les étapes suivantes pour ajouter le nouveau domaine de niveau supérieur à l’aide de PowerShell

1.  Sur un ordinateur sur lequel est installé le [Module Azure Active Directory pour Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , exécutez la procédure suivante : `$cred=Get-Credential`.  
2.  Entrez le nom d’utilisateur et mot de passe d’un administrateur global pour le domaine AD Azure que vous utilisez la fédération avec
2.  Dans PowerShell Entrez`Connect-MsolService -Credential $cred`
3.  Dans PowerShell Entrez`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilisez les étapes suivantes pour ajouter le nouveau domaine de niveau supérieur à l’aide d’Azure AD Connect.

1.  Barre de lancement Azure AD Connect à partir du bureau ou le menu Démarrer
2.  Cliquez sur « Ajouter un domaine AD Azure supplémentaire » ![ajouter un autre domaine AD Azure](./media/active-directory-multiple-domains/add1.png)
3.  Entrez votre annonce Azure et informations d’identification Active Directory
4.  Sélectionnez le domaine de second que vous souhaitez configurer pour la fédération.
![Ajouter un autre domaine AD Azure](./media/active-directory-multiple-domains/add2.png)
5.  Cliquez sur Installer


### <a name="verify-the-new-top-level-domain"></a>Vérifiez que le nouveau domaine de niveau supérieur
À l’aide de la commande PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`vous pouvez afficher le IssuerUri mis à jour.  La capture d’écran ci-dessous montre la fédération de paramètres ont été mis à jour sur notre http://bmcontoso.com/adfs/services/trust domaine d’origine

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Et la IssuerUri sur notre nouveau domaine a été défini pour https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Prise en charge des sous-domaines
Lorsque vous ajoutez un sous-domaine, en raison des domaines de manière Azure AD géré, il sera héritent des paramètres du parent.  Cela signifie que le IssuerUri doit faire correspondre les parents.

Si vous permet de dire par exemple que j’ai bmcontoso.com et puis ajoutez corp.bmcontoso.com.  Cela signifie que la IssuerUri pour un utilisateur travaillant dans corp.bmcontoso.com devront être **http://bmcontoso.com/adfs/services/trust.**  Alors que la règle standard implémentée ci-dessus pour Azure AD, génère un jeton avec un émetteur en tant que **http://corp.bmcontoso.com/adfs/services/trust.** valeur qui ne correspondront pas le domaine requise et l’authentification échouera.

### <a name="how-to-enable-support-for-sub-domains"></a>Comment activer la prise en charge des sous-domaines
Pour contourner ce problème la AD FS confiance approbation tiers pour Microsoft Online doit être mis à jour.  Pour ce faire, vous devez configurer une règle personnalisée réclamer afin qu’il supprime désactiver les sous-domaines à partir de suffixe UPN de l’utilisateur lors de la création de la valeur de l’émetteur personnalisée. 

La demande suivante sera procédez comme suit :

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Utilisez les étapes suivantes pour ajouter une déclaration personnalisée pour prendre en charge des sous-domaines.

1.  Gestion des ouvrir AD FS
2.  Cliquez avec le bouton droit sur la gestion de la confidentialité Microsoft Online RP et sélectionnez Modifier réclamer règles
3.  Sélectionnez la troisième règle réclamer et remplacez ![réclamer modifier](./media/active-directory-multiple-domains/sub1.png)
4.  Remplacer la demande en cours :
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    avec
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Remplacer réclamer](./media/active-directory-multiple-domains/sub2.png)
5.  Cliquez sur Ok.  Cliquez sur Appliquer.  Cliquez sur Ok.  Fermez la gestion des AD FS.

