<properties
    pageTitle="Active Directory Federation Services gestion et personnalisation avec Azure AD Connect | Microsoft Azure"
    description="Gestion des AD FS avec Azure AD Connect et la personnalisation d’AD FS connexion expérience utilisateur avec Azure AD Connect et PowerShell."
    keywords="AD FS, ADFS, AD FS gestion, AAD à vous connecter, connexion, se connecter, AD FS personnalisation, réparez fédération de gestion de la confidentialité, Office 365, partie de confiance"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Active Directory Federation Services gestion et personnalisation avec Azure AD Connect

Cette tâches Détails de l’article liés aux Services de fédération Active Directory (AD FS) qui peut être effectuée à l’aide de Microsoft Azure Active Directory se connecter, ainsi que d’autres tâches AD FS courantes qui peuvent être nécessaires pour terminer la configuration d’une batterie de serveurs AD FS.

| Rubrique | Il traite |
|:------|:-------------|
|**Gestion des AD FS**|
|[Réparer l’approbation](#repairthetrust)| La réparation de l’approbation de fédération avec Office 365 |
|[Ajouter un serveur AD FS](#addadfsserver) | Développer la batterie de serveurs AD FS en ajoutant un serveur AD FS|
|[Ajouter un serveur proxy AD FS d’application web](#addwapserver) | Développer la batterie de serveurs AD FS avec un serveur WAP supplémentaire|
|[Ajouter un domaine fédéré](#addfeddomain)| Ajout d’un domaine fédéré|
| **Personnalisation de FS AD**|
|[Ajouter un logo de société personnalisé ou d’une illustration](#customlogo)| Personnalisation d’une page de connexion AD FS avec un logo de société et illustration |
|[Ajouter une description de connexion](#addsignindescription) | Ajout d’une description de la page de connexion |
|[Modifier les règles de revendications AD FS](#modclaims) | Modification des revendications AD FS pour différents scénarios de fédération |

## <a name="ad-fs-management"></a>Gestion des AD FS

Azure AD Connect fournit diverses tâches liées à AD FS qui peuvent être effectuées à l’aide de l’Assistant Azure AD Connect avec intervention minimale de l’utilisateur. Une fois que vous avez terminé l’installation d’Azure AD Connect en exécutant l’Assistant, vous pouvez exécuter l’Assistant pour effectuer des tâches supplémentaires.

### Réparer l’approbation<a name=repairthetrust></a>

Azure AD Connect peut rechercher l’état actuel de l’approbation AD FS et Azure Active Directory et prendre des mesures appropriées pour réparer l’approbation. Suivez ces étapes pour réparer votre annonce Azure et gestion de la confidentialité AD FS.

1. Sélectionnez **réparation AAD et ADFS approuver** dans la liste des tâches supplémentaires.
![Réparer AAD et ADFS gestion de la confidentialité](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Dans la page **se connecter à Azure AD** , fournissent vos informations d’identification d’administrateur général pour Azure AD, puis cliquez sur **suivant**.
![Se connecter à Active Directory Azure](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Dans la page **informations d’identification d’accès à distance** , entrez les informations d’identification pour l’administrateur de domaine.
![Informations d’identification d’accès à distance](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Après avoir cliqué sur **suivant**, Azure AD Connect vérifie d’intégrité de certificat et afficher des problèmes.

    ![État de certificats](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    La page **prêt à configurer** affiche la liste des actions à effectuer pour réparer l’approbation.

    ![Êtes-vous prêt à configurer](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Cliquez sur **installer** pour réparer l’approbation.

>[AZURE.NOTE] Azure AD Connect peut uniquement réparer ou la législation sur les certificats sont auto-signé. Azure AD Connect ne peut pas réparer des certificats tiers.

### Ajouter un serveur AD FS<a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect nécessite le fichier de certificat PFX pour ajouter un serveur AD FS. Par conséquent, vous pouvez effectuer cette opération uniquement si vous avez configuré la batterie de serveurs AD FS à l’aide de Azure AD Connect.

1. Sélectionnez **déployer un serveur de fédération supplémentaires** , puis cliquez sur **suivant**.
![Serveur de fédération supplémentaires](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Dans la page **se connecter à Active Directory Azure** , entrez vos informations d’identification d’administrateur global Azure AD, puis cliquez sur **suivant**.
![Se connecter à Active Directory Azure](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Indiquer le domaine d’administrateur.
![Informations d’identification d’administrateur de domaine](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect vous demandera le mot de passe du fichier PFX que vous avez fourni lors de la configuration de votre nouvelle batterie de serveurs AD FS avec Azure AD Connect. Cliquez sur le **Mot de passe** pour fournir le mot de passe pour le fichier PFX.
![Mot de passe de certificat](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Spécifier le certificat SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Dans la page **AD FS serveurs** , entrez le nom du serveur ou l’adresse IP à ajouter à la batterie de serveurs AD FS.
![Serveurs AD FS](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Cliquez sur **suivant** et passez à la dernière page de **configurer** . Une fois que Azure AD Connect a terminé d’ajouter les serveurs à la batterie de serveurs AD FS, vous aurez l’option pour vérifier la connectivité.
![Êtes-vous prêt à configurer](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Installation terminée](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Ajouter un serveur proxy AD FS d’application web<a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect nécessite le fichier de certificat PFX pour ajouter un serveur proxy d’applications web. Par conséquent, vous ne pourrez pas effectuer cette opération uniquement si vous avez configuré la batterie de serveurs AD FS à l’aide de Azure AD Connect.

1. Sélectionnez **Déployer le Proxy d’Application Web** dans la liste des tâches disponibles.
![Déployer proxy de l’application web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Fournir les informations d’identification d’administrateur global Azure.
![Se connecter à Active Directory Azure](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. Dans la page **certificat SSL spécifier** , entrez le mot de passe du fichier PFX que vous avez fourni lors de la configuration de la batterie de serveurs AD FS avec Azure AD Connect.
![Mot de passe de certificat](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Spécifier le certificat SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Ajoutez le serveur à ajouter comme un proxy d’application web. Étant donné que le serveur proxy d’application web ne peut pas être joint au domaine, l’Assistant vous demande des informations d’identification d’administration sur le serveur qui est ajouté.
![Informations d’identification d’administration server](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. Dans la page **informations d’identification de la gestion de la confidentialité Proxy** , fournissent des informations d’identification d’administration pour configurer l’approbation de proxy et accéder au serveur principal dans la batterie de serveurs AD FS.
![Informations d’identification de la gestion de la confidentialité proxy](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. Dans la page **prêt à configurer** , l’Assistant affiche la liste des actions à effectuer.
![Êtes-vous prêt à configurer](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Cliquez sur **installer** pour terminer la configuration. Une fois la configuration terminée, l’Assistant vous donne l’option pour vérifier la connectivité aux serveurs. Cliquez sur **Vérifier** pour vérifier la connectivité.
![Installation terminée](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Ajouter un domaine fédéré<a name=addfeddomain></a>

Il est facile d’ajouter un domaine à être fédéré avec Azure Active Directory à l’aide de Azure AD Connect. Azure AD Connect ajoute le domaine pour la fédération et modifie les règles réclamer pour refléter correctement l’émetteur lorsque vous avez plusieurs domaines fédérés avec Azure AD.

1. Pour ajouter un domaine fédéré, sélectionnez la tâche **Ajouter un autre domaine Azure AD**.
![Autres domaines Azure Active Directory](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. Dans la page suivante de l’Assistant, fournissent les informations d’identification d’administrateur général pour Azure AD.
![Se connecter à Active Directory Azure](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. Dans la page **informations d’identification d’accès à distance** , fournissent des informations d’identification d’administrateur le domaine.
![Informations d’identification d’accès à distance](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Dans la page suivante, l’Assistant fournit une liste de domaines Azure AD avec lequel vous pouvez fédérer votre annuaire local. Sélectionnez le domaine à partir de la liste.
![Domaine AD Azure](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Après avoir choisi le domaine, l’Assistant vous fournira les informations nécessaires concernant davantage les actions que l’Assistant effectuera et l’impact de la configuration. Dans certains cas, si vous sélectionnez un domaine qui n’est pas encore vérifié dans Azure Active Directory, l’Assistant vous fournira d’informations pour vous aider à vérifier le domaine. Pour plus d’informations, voir [Ajouter votre nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md) .

5. Cliquez sur **suivant**, et la page **prêt à configurer** affiche la liste des actions effectuées à Azure AD Connect. Cliquez sur **installer** pour terminer la configuration.
![Êtes-vous prêt à configurer](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personnalisation de FS AD

Les sections suivantes fournissent des informations détaillées sur certains des tâches courantes que vous devrez peut-être effectuer lors de la personnalisation de votre page de connexion AD FS.

### Ajouter un logo de société personnalisé ou d’une illustration<a name=customlogo></a>

Pour modifier le logo de la société qui s’affiche dans la page de **connexion** , utilisez l’applet de commande Windows PowerShell suivante et de syntaxe.

> [AZURE.NOTE] Les dimensions recommandées pour le logo sont 260 x 35 @ 96 PPP avec une taille de fichier n’est supérieure à 10 Ko.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Le paramètre *TargetName* est obligatoire. Le thème par défaut qui est fourni avec AD FS est nommé par défaut.


### Ajouter une description de connexion<a name=addsignindescription></a>

Pour ajouter une description de la page se connecter à la **page de connexion**, utilisez l’applet de commande Windows PowerShell suivante et de syntaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modifier les règles de revendications AD FS<a name=modclaims></a>

AD FS prend en charge une langue réclamer riches que vous pouvez utiliser pour créer des règles de déclaration personnalisée. Pour plus d’informations, voir [Le rôle de la langue de règle réclamer](https://technet.microsoft.com/library/dd807118.aspx).

Les sections suivantes décrivent comment vous pouvez écrire des règles personnalisées pour certains scénarios relatifs à Azure AD et fédération AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID immuable conditionnelle sur une valeur étant présentes dans l’attribut

Azure AD Connect vous permet de spécifier un attribut à utiliser comme un point d’ancrage source lorsque les objets sont synchronisés avec Azure AD. Si la valeur de l’attribut personnalisé n’est pas vide, vous souhaiterez peut-être émettre un réclamer ID immuable. Vous pourriez par exemple, sélectionnez **ms-ds-consistencyguid** en tant que l’attribut pour le point d’ancrage source et souhaitez émettre **ImmutableID** comme **ms-ds-consistencyguid** au cas où l’attribut comporte une valeur. S’il n’existe aucune valeur par rapport à l’attribut, émettre **GUID d’objet** du code immuables.  Vous pouvez créer le jeu de règles de déclaration personnalisée comme décrit dans la section suivante.

**Règle 1 : Attributs de la requête**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Dans cette règle, vous interrogez les valeurs de **ms-ds-consistencyguid** et **GUID d’objet** pour l’utilisateur dans Active Directory. Modifier le nom du magasin à un nom approprié store dans votre déploiement AD FS. Également modifier le type de revendications à un type de revendications appropriée pour votre fédération, telle que définie pour **GUID d’objet** et **ms-ds-consistencyguid**.

En outre, à l’aide de **Ajouter** et pas le **problème**, évitez d’ajouter un problème sortant pour l’entité et peut utiliser les valeurs en tant que valeurs intermédiaires. Vous délivrerez la demande dans une version ultérieure règle une fois que vous établissez la valeur à utiliser comme code immuable.

**Règle 2 : Vérifier l’existence de ms-ds-consistencyguid pour l’utilisateur**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Cette règle définit un indicateur temporaire appelé **idflag** qui est défini sur **useguid** s’il n’existe aucune **ms-ds-concistencyguid** rempli pour l’utilisateur. La logique derrière cela est fait qu’AD FS n’autorise pas revendications vides. Donc lorsque vous ajoutez des revendications http://contoso.com/ws/2016/02/identity/claims/objectguid et http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid dans la règle 1, vous retrouver avec un **msdsconsistencyguid** réclamer uniquement si la valeur est remplie pour l’utilisateur. Si elle n’est pas remplie, AD FS détecte qu’il aura une valeur vide et supprime immédiatement. Tous les objets a **GUID d’objet**, cette déclaration sera toujours disponible après l’exécution de la règle 1.

**Règle 3 : Émettre ms-ds-consistencyguid en tant qu’ID immuable si elle est présente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Il s’agit d’une vérification **existent** implicite. Si la valeur de la demande existe, puis problème qui en tant que l’ID immuable. L’exemple précédent utilise la réclamation **nameidentifier** . Vous devrez y remédier au type de réclamer appropriée pour l’ID immuable dans votre environnement.

**Règle 4 : Émettre GUID d’objet en tant qu’ID immuable si ms-ds-consistencyGuid n’est pas présent**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Dans cette règle, vous intégrez simplement l' indicateur temporaire **idflag**. Vous décidez de délivrer la demande en fonction de sa valeur.

> [AZURE.NOTE] La séquence de ces règles est importante.

#### <a name="sso-with-a-subdomain-upn"></a>Authentification unique avec un sous-domaine UPN

Vous pouvez ajouter plusieurs domaines à être fédéré à l’aide d’Azure AD Connect, comme décrit dans [Ajouter un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Vous devez modifier la déclaration UPN afin que l’ID de l’émetteur correspond au domaine racine et non le sous-domaine, car le domaine racine fédéré couvre également l’enfant.

Par défaut, la règle réclamer de l’ID de l’émetteur est définie comme :

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Réclamer de ID émetteur par défaut](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

La règle par défaut prend le suffixe UPN simplement et utilise dans la demande d’ID de l’émetteur. Par exemple, Jean est un utilisateur dans sub.contoso.com et contoso.com fédérée avec Azure AD. Saisit John john@sub.contoso.com en tant que le nom d’utilisateur lors de la connexion à Azure Active Directory et l’ID de l’émetteur par défaut réclamer la règle dans AD FS il traite de la manière suivante.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Revendiquer valeur :** http://sub.contoso.com/adfs/services/trust/

Pour que seul le domaine racine dans la valeur de réclamer émetteur, modifiez la règle réclamer pour correspondre à la suivante.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [options de connexion utilisateur](active-directory-aadconnect-user-signin.md).
