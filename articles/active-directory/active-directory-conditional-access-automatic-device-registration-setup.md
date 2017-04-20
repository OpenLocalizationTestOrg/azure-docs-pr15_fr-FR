<properties
    pageTitle="Configurer l’enregistrement automatique des périphériques d’à un domaine Windows avec Azure Active Directory | Microsoft Azure"
    description="Configurez vos appareils Windows à un domaine pour enregistrer automatiquement et silencieusement avec Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configurer l’enregistrement automatique de Windows périphériques à un domaine avec Azure Active Directory

Pour utiliser [access conditionnelle basée sur un appareil de Azure Active Directory](active-directory-conditional-access.md), vos ordinateurs à un domaine Windows doivent être enregistrés avec Azure Active Directory (AD Azure). Dans cet article, vous pouvez découvrir ce que vous devez faire pour configurer l’enregistrement des périphériques à un domaine sur Windows avec Azure Active Directory de votre organisation.

L’utilisation d’accès conditionnel dans Azure AD vous offre les avantages suivants :

-   Expérience améliorée session unique (SSO) dans les applications Azure AD via les comptes professionnel ou scolaire
-   Entreprise d’itinérance des paramètres de tous les périphériques
-   Accès à Windows Store pour les entreprises
-   Authentification renforcée et pratique connexion avec Windows Hello

> [AZURE.NOTE] La mise à jour de Windows 10 novembre propose certaines de l’expérience utilisateur améliorée dans Azure Active Directory, mais la mise à jour de Windows 10 anniversaire réforme de l’accès conditionnelle basée sur un appareil. Pour plus d’informations sur l’accès conditionnel, voir [Azure Active Directory basée sur un appareil conditionnelle accéder](active-directory-conditional-access.md). Pour plus d’informations sur les appareils Windows 10 dans l’espace de travail et comment un utilisateur enregistre un appareil Windows 10 avec Azure Active Directory, voir [Windows 10 pour les entreprises : utiliser des périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md).

Vous pouvez enregistrer des versions antérieures de Windows, y compris les versions suivantes :

-   Windows 8.1
-   Windows 7

Si vous utilisez un ordinateur Windows Server comme un ordinateur de bureau, vous pouvez enregistrer ces plateformes :

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Conditions préalables

L’exigence principale d’enregistrement automatique des appareils à un domaine à l’aide de Azure AD est d’avoir une version récente d’Azure Active Directory se connecter (Azure AD Connect).

Selon la façon dont vous avez déployé Azure AD Connect et si vous avez utilisé une installation rapide ou personnalisée ou une mise à niveau en place, les composants requis suivants a pu être configurés automatiquement :

-   **Point de connexion de Service dans Active Directory sur site**. Pour la découverte des informations de client Azure AD par les ordinateurs qui s’inscrire à Azure AD.

-   **Transformer des règles d’émission des Services de fédération active Directory (AD FS)**. Pour l’authentification ordinateur lors de l’enregistrement (s’applique aux configurations fédérées).

Si certains périphériques dans votre organisation ne sont pas Windows 10 à un domaine, vérifiez que vous effectuez les tâches suivantes :

- Définir une stratégie dans Azure AD afin que les utilisateurs peuvent enregistrer des appareils mobiles
- Configurer l’authentification Windows (intégrée) comme alternative valide à l’authentification multifacteur dans AD FS



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Définir un point de connexion de service pour la découverte du client Azure AD

Un objet de point de connexion de service doit exister dans la configuration de partition d’appellation contexte de la forêt du domaine où les ordinateurs sont jointes. Le point de connexion de service conserve des informations de découverte sur le client Azure AD où ordinateurs s’inscrire. Dans une configuration Active Directory forêt multiples, le point de connexion de service doit exister dans toutes les forêts contenant des ordinateurs à un domaine.

Définir le point de connexion de service à ces emplacements dans Active Directory :

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] Pour une forêt avec Active Directory nom de domaine *exemple.com*, la configuration du contexte d’appellation est CN = Configuration, DC = exemple, DC = com.

Vous pouvez vérifier les valeurs objet et la découverte du client Azure AD en utilisant le script Windows PowerShell suivant. (Remplacez le contexte d’appellation de configuration dans l’exemple par votre contexte d’appellation de configuration).

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

La `$scp.Keywords` sortie affiche les informations de client Azure AD :

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si le point de connexion de service n’existe pas, créez-le en exécutant le script PowerShell suivant sur le serveur Azure AD Connect :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Lorsque vous exécutez `$aadAdminCred = Get-Credential`, utilisez le format *user@example.com* pour le nom d’utilisateur dans la boîte de dialogue **Get-Credential** .  
> Lorsque vous exécutez l’applet de commande initialisation ADSyncDomainJoinedComputerSync, remplacez [*nom du compte connecteur*] avec le compte de domaine qui est utilisé dans le compte de connecteur Active Directory.  
> L’applet de commande utilise le module Active Directory PowerShell, qui se base sur les Services Web Active Directory dans un contrôleur de domaine. Les Services Web Active Directory est prise en charge dans les contrôleurs de domaine dans Windows Server 2008 R2 et versions ultérieures. Pour contrôleurs de domaine dans Windows Server 2008 ou une version antérieure, utilisez l’API System.DirectoryServices via PowerShell pour créer le point de connexion de service et puis assigner les valeurs de mots clés.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Créer des règles de AD FS pour l’enregistrement de dispositifs instantanée d’organisations fédérées

Dans un Azure fédéré configuration AD, appareils s’appuient sur AD FS (ou sur le serveur de fédération locale) s’authentifier à Azure Active Directory. Ensuite, ils inscrivent contre Azure Active Directory appareil d’inscription Service (Service d’enregistrement de Azure AD appareil).

> [AZURE.NOTE] Dans une configuration non fédéré, hachage de mot de passe utilisateur est synchronisés avec Azure AD et ordinateurs Windows 10 et Windows Server 2016 à un domaine s’authentifier Service d’inscription Azure AD appareils. Un utilisateur s’authentifie à l’aide des informations d’identification que l’utilisateur écrit dans leurs comptes d’ordinateur local, et qui est transmis à Azure AD via Azure AD Connect. Pour les non - Windows 10 et ordinateurs Windows Server 2016 dans une configuration non fédéré, vous disposez des options de configuration d’une autorité de certification basée sur un appareil de votre organisation. Pour plus d’informations, consultez la section de packages télécharger Windows Installer pour les ordinateurs non Windows 10 dans cet article.

Pour les ordinateurs Windows 10 et Windows Server 2016, Azure AD Connect associe l’objet périphérique dans Azure Active Directory à l’objet de compte d’ordinateur local. Les affirmations suivantes doivent être réunies lors de l’authentification pour Service d’inscription Azure AD appareil terminer votre inscription et créer l’objet périphérique :

- http://schemas.Microsoft.com/ws/2012/01/AccountType contient la valeur DJ, qui identifie l’authentificateur principal comme un ordinateur à un domaine.
- http://schemas.Microsoft.com/Identity/Claims/onpremobjectguid contient la valeur de l’attribut **GUID d’objet** du compte d’ordinateur local.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/primarysid contient l’identificateur de principal de sécurité de l’ordinateur (identificateur de sécurité), qui correspond à la valeur de l’attribut **objectSid** du compte d’ordinateur local.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/issuerid contient la valeur Azure Active Directory utilise à approuver le jeton émis à partir d’AD FS ou à partir de la locale jeton Service STS (Security). Ceci est important dans une configuration Active Directory forêt multiples. Dans cette configuration, ordinateurs peuvent être liés à une forêt différent de celui qui se connecte à Azure AD à l’AD FS ou STS local. Pour la AD FS, utilisez la valeur http://< ;*nom de domaine*>/adfs/services/approbation /, où <*nom de domaine*> est le nom de domaine validées dans Azure AD.

Pour créer manuellement, les règles suivantes dans AD FS, utilisez le script PowerShell suivant dans une session qui est connectée à votre serveur. Remplacez la première ligne par le nom de domaine de votre organisation validées dans Azure Active Directory.

> [AZURE.NOTE] Si vous n’utilisez pas AD FS pour votre serveur de fédération local, suivez les instructions de votre fournisseur pour créer les règles appliquées par ces revendications de problème.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Utilisez uniquement les trois premières règles si votre environnement local est une forêt unique. Si vos ordinateurs sont dans une autre forêt que celle synchronisation avec Azure AD ou si vous utilisez des noms de remplacement à celles de la configuration de la synchronisation, vous devez également inclure les règles restants.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Ordinateurs Windows 10 et Windows Server 2016 à un domaine s’authentifier à l’aide de IWA à un point de terminaison Web approbation active est hébergé par AD FS. Assurez-vous que le point de terminaison est actif. Si vous utilisez le Proxy d’Application Web, n’oubliez pas que ce point de terminaison est publié via le serveur proxy. Le point de terminaison est adfs/services/approbation/13/windowstransport. Pour vérifier si elle est active, dans la console de gestion AD FS accédez au **Service** > **points de terminaison**. Si vous n’utilisez pas AD FS pour votre serveur de fédération local, suivez les instructions de votre fournisseur pour vous assurer que le point de terminaison correspondant est actif.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Configurer la AD FS pour l’authentification de l’enregistrement de dispositifs

Assurez-vous que IWA est définie comme alternative valide pour l’authentification multifacteur pour l’enregistrement de dispositifs dans AD FS. Pour ce faire, vous devez avoir une émission transformer règle transmission à la méthode d’authentification.

1. Dans la console de gestion AD FS, accédez à **AD FS** > **Relations d’approbation** > **Approbations des parties de confiance**.

2. Cliquer sur l’objet de gestion de la confidentialité Microsoft Office 365 identité plateforme confiance fête, puis sélectionnez **Modifier les règles réclamer**.

3.  Sous l’onglet **Règles de transformation d’émission** , sélectionnez **Ajouter une règle**.

4.  Dans la liste modèle de **règle** , sélectionnez **Envoyer des demandes à l’aide d’une règle personnalisée**.

5.  Cliquez sur **suivant**.

6.  Dans la zone **nom de la règle réclamer** , entrez **Auth méthode réclamer règle**.

7.  Dans la zone de la **règle** , entrez cette commande :

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. Sur votre serveur de fédération, entrez cette commande PowerShell :

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> est le nom d’objet fête confiance pour votre objet Azure AD approbation de tiers de confiance. Cet objet est généralement nommé *Plate-forme d’identité Microsoft Office 365*.



## <a name="deployment-and-rollout"></a>Et le déploiement

Lorsque les ordinateurs joints au domaine remplissent les conditions préalables, ils sont prêts à vous inscrire avec Azure AD.

Les ordinateurs à un domaine mise à jour de l’anniversaire de mariage Windows 10 et Windows Server 2016 enregistre automatiquement avec Azure AD la prochaine fois que l’appareil redémarre ou lorsqu’un utilisateur se connecte à Windows. Nouveaux ordinateurs qui sont jointes au domaine inscrivent Azure AD lors du redémarrage de l’appareil après l’opération de jointure de domaine.

> [AZURE.NOTE] Ordinateurs à un domaine Windows 10 enregistre automatiquement avec Azure AD uniquement si l’objet de stratégie de groupe de déploiement est créée.

Vous pouvez utiliser un objet de stratégie de groupe pour contrôler la mise en œuvre de l’enregistrement automatique de Windows 10 et Windows Server 2016 à un domaine ordinateurs. Pour déployer l’enregistrement automatique d’ordinateurs à un domaine non Windows 10, vous pouvez déployer un package d’installation de Windows sur les ordinateurs que vous sélectionnez.

> [AZURE.NOTE] La stratégie de groupe pour un contrôle de déploiement déclenche également l’inscription d’ordinateurs à un domaine Windows 8.1. Vous pouvez utiliser la stratégie d’enregistrement d’ordinateurs à un domaine Windows 8.1. Ou, si vous avez un mélange de versions de Windows, y compris les versions de Windows 7 ou Windows Server, vous pouvez enregistrer toutes vos non Windows 10 et ordinateurs Windows Server 2016 à l’aide d’un package d’installation de Windows.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Créer un objet de stratégie de groupe pour contrôler la mise en œuvre l’enregistrement automatique

Pour contrôler la mise en œuvre l’enregistrement automatique des ordinateurs à un domaine avec Azure AD, vous pouvez déployer la stratégie de groupe **Enregistrer ordinateurs à un domaine en tant que périphériques** sur les ordinateurs que vous souhaitez enregistrer. Par exemple, vous pouvez déployer la stratégie à une unité d’organisation ou à un groupe de sécurité.

Pour définir la stratégie, procédez comme suit :

1. Ouvrez le Gestionnaire de serveur, puis puis accédez à **Outils** > **Gestion des stratégies de groupe**.

2. Atteindre le nœud de domaine qui correspond au domaine où vous souhaitez activer l’enregistrement automatique des ordinateurs Windows 10 ou Windows Server 2016.

3. Avec le bouton droit **Objets stratégie de groupe**, puis sélectionnez **Nouveau**.

4. Entrez un nom pour votre objet de stratégie de groupe. Par exemple, *L’enregistrement automatique à Azure Active Directory*. Sélectionnez **OK**.

4. Avec le bouton droit de votre nouvel objet de stratégie de groupe et sélectionnez **Modifier**.

5. Accédez à la **Configuration de l’ordinateur** > **stratégies** > **modèles d’administration** > **composants Windows** > **d’inscription appareils mobiles**. Droit **Registre domaine ordinateurs en tant que périphériques**, puis sélectionnez **Modifier**.

    > [AZURE.NOTE] Ce modèle de stratégie de groupe a été renommé provenant de versions antérieures de la console de gestion des stratégies de groupe. Si vous utilisez une version antérieure de la console, accédez à la **Configuration de l’ordinateur** > **stratégies** > **Modèles d’administration** > **Composants Windows** > **Jointure poste de travail** > **automatiquement les ordinateurs de clients de jointure poste de travail**.

6. Sélectionnez **activé**, puis **Appliquer**.

7. Sélectionnez **OK**.

8. Liez l’objet de stratégie de groupe à l’emplacement de votre choix. Par exemple, vous pouvez le lier à une unité d’organisation spécifique. Vous pouvez également la liez à un groupe de sécurité spécifique d’ordinateurs enregistre automatiquement avec Azure AD. Pour définir cette stratégie pour tous les ordinateurs joints au domaine Windows 10 et Windows Server 2016 dans votre organisation, liez l’objet de stratégie de groupe pour le domaine.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Télécharger les packages Windows Installer pour les ordinateurs non Windows 10  

Pour vous inscrire à un domaine ordinateurs exécutant Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, vous pouvez télécharger et installer ces fichiers de package (.msi) Windows Installer :

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Déployer le package à l’aide d’un système de distribution des logiciels tels que le Gestionnaire de Configuration de System Center. Le package prend en charge les options d’installation en mode silencieux standard avec le paramètre *silencieux* . Gestionnaire de Configuration de System Center 2016 propose des avantages supplémentaires provenant de versions antérieures, comme la fonctionnalité permettant d’effectuer le suivi des enregistrements terminées. Pour plus d’informations, voir [système Centre 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

Le programme d’installation crée une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur. La tâche se déclenche lorsque l’utilisateur se connecte à Windows. La tâche inscrit silencieusement l’appareil avec Azure AD avec les informations d’identification utilisateur après l’authentification par le biais IWA. Pour afficher la tâche planifiée, accédez à **Microsoft** > **Rejoindre d’espace de travail**et puis accédez à la bibliothèque du Planificateur de tâches.



## <a name="next-steps"></a>Étapes suivantes

- [Accès à conditionnelle Azure Active Directory](active-directory-conditional-access.md)
