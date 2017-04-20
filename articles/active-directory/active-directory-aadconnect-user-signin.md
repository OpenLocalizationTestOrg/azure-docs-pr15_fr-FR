<properties
    pageTitle="Azure AD Connect : Utilisateur se connecter | Microsoft Azure"
    description="Azure AD Connect utilisateur se connecter pour les paramètres personnalisés."
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
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD connecter connexion utilisateur sur les options

Azure AD Connect permet à vos utilisateurs à vous connecter au cloud et en local des ressources à l’aide des mêmes mots de passe. Cet article décrit des concepts clés pour chaque modèle d’identité pour vous aider à choisir l’identité que vous souhaitez utiliser pour votre connexion à Azure Active Directory.

Si vous connaissez déjà modèle d’identité AD Azure et que vous voulez en savoir plus sur une méthode spécifique, cliquez simplement sur en dessous de la rubrique appropriée.

* [Synchronisation de mot de passe](#password-synchronization)
* [Authentification unique fédéré (avec les services ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Choix d’une méthode de connexion utilisateur
Pour la plupart des organisations qui veulent juste utilisateur se connecter à Office 365, les applications SaaS et autres Azure AD basé sur les ressources, l’option de synchronisation de mot de passe par défaut est recommandée.
Certaines organisations, cependant, ont bonne raison de l’utilisation du signe fédéré option tels que AD FS.  Parmi lesquels :

- Votre organisation possède déjà AD FS ou un 3e partie fournisseur fédération déployé
- Votre stratégie de sécurité interdit synchronisation hachage de mot de passe dans le cloud
- Exiger que les utilisateurs connaissent l’authentification unique transparente (sans mot de passe supplémentaire invite) lors de l’accès à des ressources de cloud à partir de domaine rejoint machines du réseau d’entreprise
- Vous avez besoin des fonctionnalités spécifiques QU'AD FS a
    - Authentification multifacteur locales à l’aide d’un fournisseur tiers ou des cartes à puce (en savoir plus sur les fournisseurs de l’authentification Multifacteur tiers pour AD FS dans Windows Server 2012 R2)
    - Fonctionnalités d’intégration Active Directory tels que le verrouillage du compte contours ou mot de passe et travail heures une stratégie AD
    - Accès conditionnel aux deux en local et ressources à l’aide de l’enregistrement de dispositifs, jointure Azure AD ou stratégies Intune MDM en nuage

### <a name="password-synchronization"></a>Synchronisation de mot de passe
Avec la synchronisation de mot de passe, hachage des mots de passe utilisateur est synchronisés depuis Active Directory local vers Azure Active Directory.  Lorsque les mots de passe sont modifiés ou réinitialiser en local, les nouveaux mots de passe sont synchronisés immédiatement à Azure AD afin que vos utilisateurs peuvent toujours utiliser le même mot de passe pour les ressources de cloud instar locaux.  Les mots de passe ne sont jamais envoyées à Azure AD ni stockés dans Azure Active Directory en texte clair.
Synchronisation de mot de passe peut servir avec écriture différée mot de passe pour activer libre service le mot de passe réinitialisé dans Azure Active Directory.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Plus d’informations sur la synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Fédération à l’aide d’une nouvelle ou existante AD FS dans Windows Server 2012 R2 batterie
Avec fédéré de connexion, vos utilisateurs de s’aux services Azure AD en fonction de leur mot de passe en local et, tandis que sur le réseau d’entreprise, sans avoir à entrer leur mot de passe à nouveau.  La fédération avec AD FS vous permet déployer une nouvelle ou spécifier un ADFS existant dans la batterie de serveurs Windows Server 2012 R2.  Si vous choisissez de spécifier une batterie de serveurs existante, Azure AD Connect configure l’approbation entre votre batterie de serveurs et Azure AD pour que vos utilisateurs puissent signer.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Pour déployer la fédération avec AD FS dans Windows Server 2012 R2, vous devez les éléments suivants

Si vous déployez une batterie :

- Un serveur Windows Server 2012 R2 pour le serveur de fédération.
- Un serveur Windows Server 2012 R2 pour le Proxy d’Application Web.
- un fichier .pfx avec un certificat SSL pour votre nom de service fédération initial, tel que fs.contoso.com.

Si vous déployez une batterie ou à l’aide d’une batterie de serveurs existante :

- Informations d’identification d’administrateur local sur vos serveurs de fédération.
- Droits d’administrateur local sur n’importe quel groupe de travail (non joint à un domaine) serveurs sur lequel vous voulez déployer le rôle de Proxy de l’Application Web.
- L’ordinateur sur lequel vous exécutez l’Assistant doit être en mesure de vous connecter à tous les autres ordinateurs sur lequel vous voulez installer AD FS ou Proxy de l’Application Web via la gestion à distance de Windows.

[Configurer l’authentification unique avec AD FS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Ouverture de session à l’aide d’une version antérieure d’AD FS ou une solution tierce

Si vous avez déjà configuré se cloud sur l’utilisation d’une version antérieure d’AD FS (par exemple, AD FS 2.0) ou d’un fournisseur de fédération tiers, vous pouvez choisir d’ignorer la configuration de connexion utilisateur via Azure AD Connect.  Cela vous permettra obtenir la dernière synchronisation et autres fonctionnalités de Azure AD Connect tout en utilisant votre solution existante pour se sur.

[Liste de compatibilité Azure Active Directory federation tiers](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Connexion utilisateur et le nom d’utilisateur principal (UPN)

### <a name="understanding-user-principal-name"></a>Nom d’utilisateur principal compréhension

Dans Active Directory, le suffixe UPN par défaut est le nom DNS du domaine dans lequel le compte d’utilisateur créé. Dans la plupart des cas, il s’agit du nom de domaine enregistré en tant que le domaine d’entreprise sur Internet. Toutefois, vous pouvez ajouter plusieurs suffixes UPN à l’aide d’Active Directory domaines et approbations Active Directory.

Le nom UPN de l’utilisateur est au format username@domai. Par exemple, un active directory contoso.com utilisateur John peut être UPN john@contoso.com. Le nom UPN de l’utilisateur est basé sur RFC 822. Bien que UPN et messagerie utilisent la même mise en forme, la valeur de UPN pour un utilisateur peut ou peut ne pas être égale à l’adresse de messagerie de l’utilisateur.

### <a name="user-principal-name-in-azure-ad"></a>Nom d’utilisateur principal dans Azure Active Directory

Azure AD Connect Assistant doit utiliser l’attribut userPrincipalName ou vous permettent de spécifier l’attribut (dans une installation personnalisée) pour être utilisé à partir de locaux en tant que le nom d’utilisateur principal dans Azure AD. Il s’agit de la valeur qui sera utilisée pour se connecter à Active Directory Azure. Si la valeur de l’attribut UPN utilisateur ne correspond pas à un domaine vérifié dans Azure Active Directory, puis Azure AD remplacera avec une valeur par défaut. onmicrosoft.com valeur.

Chaque répertoire dans Azure Active Directory est fourni avec un nom de domaine intégrés dans la contoso.onmicrosoft.com formulaire qui vous permet de commencer à utiliser Azure ou autres services Microsoft. Vous pouvez améliorer et simplifier l’expérience de connexion à l’utilisation des domaines personnalisés. Pour plus d’informations sur le domaine personnalisé noms dans Azure Active Directory et comment vérifier un domaine, veuillez lire [Ajouter votre nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure AD connexion configuration

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD connexion configuration avec Azure AD Connect
Azure AD connexion expérience dépendant qu’est en mesure d’adapter le suffixe UPN d’un utilisateur en cours de synchronisation à un des domaines personnalisés vérifiés dans l’annuaire Active Directory Azure Azure AD. Azure AD Connect fournit une aide lors de la configuration des paramètres de connexion Azure AD, afin que l’expérience utilisateur de connexion dans le cloud est semblable à l’expérience en local. Azure AD Connect répertorie les suffixes UPN définis pour l’ou les domaines et essaie de les mettre en correspondance avec un domaine personnalisé dans Azure Active Directory et vous aide à utiliser l’action appropriée qui doit être prise.
La page de connexion Azure AD répertorie les les suffixes UPN définis pour Active Directory local et affiche l’état correspondante par rapport à chaque suffixe. Les valeurs de statut peuvent s’agir de la ci-dessous :

| État | Description | Action nécessitée |
|:----|:----|:----|
|Vérifié| Azure AD Connect a détecté qu'un correspondant vérifié le domaine dans Azure Active Directory. Tous les utilisateurs pour ce domaine de se connecter à l’aide de leurs informations d’identification en local| Aucune action n’est nécessaire |
|Non vérifié| Azure AD Connect n’a trouvé un domaine personnalisé correspondant dans Azure AD, mais elle n’est pas vérifié. Suffixe UPN des utilisateurs de ce domaine est modifiée par défaut. onmicrosoft.com suffixe après la synchronisation si le domaine n’est pas vérifié. | Vérifier le domaine personnalisé dans Azure Active Directory. [Pour en savoir plus](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|Pas encore été ajouté| Azure AD Connect n’a pas pu trouver un domaine personnalisé qui correspond au suffixe UPN. Suffixe UPN des utilisateurs de ce domaine est modifiée par défaut. onmicrosoft.com si le domaine n’est pas ajouté et verifeid dans Azure.| Ajouter et vérifier un domaine personnalisé qui correspond au suffixe UPN [Pour en savoir plus](./active-directory-add-domain.md)|

Azure AD-page de connexion répertorie les suffix(s) UPN définies pour Active Directory local et le domaine personnalisé correspondant dans Azure AD avec l’état actuel de la vérification. Dans installation personnalisée, vous pouvez désormais sélectionner l’attribut de nom d’utilisateur principal dans la page **connexion Azure AD** .

![Azure AD-page de connexion](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Vous pouvez cliquer sur le bouton Actualiser ré-récupérer le dernier état des domaines personnalisés à partir d’Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Sélection d’attribut de nom d’utilisateur principal dans Azure AD

UserPrincipalName - attribut userPrincipalName est l’attribut utilisateurs utiliseront lorsqu’ils se connecter à Active Directory Azure et Office 365. Les domaines utilisés, également connu sous le-suffixe UPN, doivent être vérifiées dans Azure AD avant que les utilisateurs sont synchronisés. Il est fortement recommandé à garder à l’attribut userPrincipalName par défaut. Si cet attribut est non prenant et ne peut pas être vérifié, il est possible sélectionner un autre attribut, par exemple envoyer par courrier électronique, comme l’attribut stockant l’ID de connexion. Il s’agit autre identifiant. La valeur de l’attribut autre ID doit suivre la norme RFC822. Un autre ID peut être utilisé avec mot de passe authentification unique (SSO) et fédération l’authentification unique en tant que la solution se connecter.

>[AZURE.NOTE] À l’aide d’un autre ID n’est pas compatible avec toutes les charges de travail Office 365. Pour plus d’informations, reportez-vous à la [Configuration d’un autre ID de connexion](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>États de domaine personnalisé différent et effet sur l’expérience de connexion Azure
Il est très important de comprendre la relation entre les États de domaine personnalisé dans votre annuaire Azure AD et les suffixes UPN défini en local. Laissez-nous traitée les différentes possibles Azure connexion expériences lorsque vous configurez synchronisation suivante à l’aide d’Azure AD lier.

Pour les informations ci-dessous, supposons que nous sont concernées par la contoso.com suffixe UPN qui est utilisée dans l’annuaire local dans le cadre de UPN, par exemple user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Configuration rapide / synchronisation de mot de passe
| État         | Effet sur l’expérience utilisateur connexion Azure |
|:-------------:|:----------------------------------------|
| Pas encore été ajouté | Dans ce cas aucun domaine personnalisé pour contoso.com n’a été ajouté dans le répertoire Azure AD. Les utilisateurs qui ont UPN en local avec suffixe @contoso.com, ne seront pas en mesure d’utiliser leur UPN local pour se connecter à Azure. Au lieu de cela, ils devront utiliser un nouvel UPN fourni par Azure AD en ajoutant le suffixe répertoire Azure Active Directory par défaut. Par exemple, si vous vous synchronisez des utilisateurs à Azure AD répertoire azurecontoso.onmicrosoft.com puis l’utilisateur local user@contoso.com sera attribué UPNuser@azurecontoso.onmicrosoft.com|
| Non vérifié | Dans ce cas, nous avons un domaine personnalisé contoso.com ajouté dans le répertoire Azure AD, mais elle n’est pas encore vérifié. Si vous continuez avec la synchronisation des utilisateurs sans vérifier le domaine, puis les utilisateurs doivent être affectées un nouvel UPN par Azure AD comme il fait dans le scénario « N’a pas ajouté ».|
| Vérifié | Dans ce cas, nous avons un domaine personnalisé contoso.com déjà ajouté et vérifiée dans Azure AD pour le suffixe UPN. Les utilisateurs seront en mesure d’utiliser leur nom d’utilisateur principal local, par exemple, user@contoso.com, pour vous connecter à Azure une fois qu’ils sont synchronisés avec Azure AD|

###### <a name="ad-fs-federation"></a>Fédération AD FS
Vous ne pouvez pas créer une fédération avec la valeur par défaut. domaine onmicrosoft.com dans Azure AD ou un domaine personnalisé non vérifié dans Azure AD. Lorsque vous exécutez l’Assistant Azure AD Connect, si vous sélectionnez un domaine non vérifié pour créer une fédération avec puis Azure AD Connect vous demandera avec les enregistrements nécessaires à créer l’endroit où votre DNS est hébergé pour le domaine. Pour plus d’informations, voir [ici](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si vous avez sélectionné utilisateur se connecter en tant que « Fédération avec AD FS », vous devez avoir un domaine personnalisé pour poursuivre la création d’une fédération dans Azure AD. Pour notre discussion, cela signifie que nous devons disposer d’un domaine personnalisé contoso.com ajouté dans le répertoire Azure AD.

| État         | Effet sur l’expérience utilisateur connexion Azure |
|:-------------:|:----------------------------------------|
| Pas encore été ajouté | Dans ce cas Azure AD Connect Impossible de trouver un correspondant personnalisé domaine pour le contoso.com suffixe UPN dans répertoire Azure AD. Vous devez ajouter un domaine personnalisé contoso.com si vous avez besoin aux utilisateurs de se connecter à l’aide de AD FS avec leur UPN local comme user@contoso.com.|
| Non vérifié | Dans ce cas Azure AD Connect vous invitera avec détails utiles sur la façon dont vous pouvez vérifier votre domaine ultérieurement|
| Vérifié | Dans ce cas vous pouvez alors avec la configuration sans action supplémentaire|  

## <a name="changing-user-sign-in-method"></a>Modifier la méthode de connexion utilisateur

Vous pouvez modifier la méthode de connexion utilisateur de la fédération pour la synchronisation de mot de passe à l’aide du disque de tâches dans Azure AD Connect après la configuration initiale de Azure AD Connect à l’aide de l’Assistant. Réexécutez l’Assistant Azure AD Connect et s’affiche avec une liste de tâches que vous pouvez effectuer. Sélectionnez **connexion utilisateur modifier** dans la liste des tâches. 

![Modifier la connexion utilisateur »](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Dans la page suivante, vous devrez fournir les informations d’identification pour Azure AD.

![Se connecter à Active Directory Azure](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Dans la page de **connexion utilisateur** , sélectionnez **La synchronisation de mot de passe**. Cette opération modifie le répertoire de fédérés vers un gérées.

![Se connecter à Active Directory Azure](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Si vous devez apporter uniquement un commutateur temporaire à la synchronisation de mot de passe, puis cochez **ne convertissez pas les comptes d’utilisateur**. Ne vérifie ne pas si l’option, vous aboutissez à la conversion de chaque utilisateur fédéré et peut prendre quelques heures.
  
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

En savoir plus sur [Azure AD Connect : concepts de conception](active-directory-aadconnect-design-concepts.md)


