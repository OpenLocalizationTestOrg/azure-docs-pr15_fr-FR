<properties
    pageTitle="Se connecter à un domaine appareils à Azure AD pour Windows 10 expériences | Microsoft Azure"
    description="Explique comment les administrateurs peuvent configurer la stratégie de groupe pour activer les périphériques à être associés à un domaine au réseau d’entreprise."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences

Jonction de domaine est que les organisations de façon traditionnelle ont connectés appareils pour le travail de ces dernières années 15 et bien plus encore. Il a activé aux utilisateurs de se connecter à leurs périphériques à l’aide de leur travail Windows Server Active Directory (Active Directory) ou scolaire et autorisé informatique entièrement gérer les appareils suivants. Organisations s’appuient généralement sur les méthodes d’images pour appareils de mise en service pour les utilisateurs et généralement utilisent System Center Configuration Manager (SCCM) ou stratégie de groupe pour les gérer.

Jonction de domaine dans Windows 10 fournira les avantages suivants après avoir connecté les périphériques à Azure Active Directory (AD Azure) :

- Session unique (SSO) vers des ressources Azure AD où que vous soyez
- Accès à l’entreprise à partir du Windows Store à l’aide de comptes professionnel ou scolaire (aucun compte Microsoft obligatoire)
- Compatible entreprise d’itinérance des paramètres utilisateur sur les périphériques à l’aide de comptes professionnel ou scolaire (aucun compte Microsoft obligatoire)
- Authentification forte et pratique connexion pour les comptes professionnel ou scolaire avec Microsoft Passport et Windows Hello
- Possibilité de limiter l’accès uniquement aux appareils conformes aux paramètres de stratégie de groupe unité d’organisation

## <a name="prerequisites"></a>Conditions préalables

Jonction de domaine continue à être utiles. Toutefois, pour obtenir des avantages Azure AD SSO, itinérance des paramètres avec Professionnel ou scolaire comptes et l’accès à Windows Store avec des comptes scolaire ou Professionnel, vous avez besoin de ce qui suit :

- Abonnement AD Azure
- Azure AD Connect pour étendre le répertoire local vers Azure Active Directory
- Stratégie qui a configuré pour se connecter à un domaine appareils à Azure Active Directory
- Windows 10 génération (build 10551 ou version ultérieure) pour appareils mobiles

Pour permettre à Microsoft Passport pour le travail et Windows Hello, vous devez également les éléments suivants :

- Clé publique (infrastructure de clé publique) pour l’émission de certificats utilisateur.
- Gestionnaire de Configuration système Centre version 1509 pour Technical Preview. Pour plus d’informations, voir [Microsoft système Centre de Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) et [Système Centre de Blog de l’équipe Gestionnaire de Configuration](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Cela est nécessaire pour déployer des certificats d’utilisateur en fonction des clés de Microsoft Passport.

Comme alternative à l’exigence de déploiement d’infrastructure de clé publique, vous pouvez procédez comme suit :

- Avoir quelques superflus aux Services de domaine Active Directory de Windows Server 2016.

Pour activer l’accès conditionnelle, vous pouvez créer des paramètres de stratégie de groupe qui autorisent l’accès à des appareils aucun déploiement supplémentaires à un domaine. Pour gérer le contrôle d’accès basé sur la conformité du périphérique, vous devez les éléments suivants :

- Gestionnaire de Configuration System Center version 1509 pour Technical Preview pour les scénarios Passport

## <a name="deployment-instructions"></a>Instructions pour le déploiement



### <a name="step-1-deploy-azure-active-directory-connect"></a>Étape 1 : Déploiement d’Azure Active Directory se connecter

Azure AD Connect vous permettra de mise en service des ordinateurs locaux en tant qu’objets périphérique dans le cloud. Pour déployer Azure AD Connect, reportez-vous à « Installer Azure AD Connect » dans l’article [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect).

 - Si vous avez suivi d’une [installation personnalisée pour Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) (pas l’installation rapide), puis suivez la procédure **créer une connexion de service pointer dans Active Directory local**, plus loin dans cette étape.
 - Si vous avez une configuration fédérée avec Azure AD avant d’installer Azure AD Connect (par exemple, si vous avez déployé des Services de fédération Active Directory (AD FS) avant), puis suivez la procédure de **configuration AD FS réclamer des règles** , plus loin dans cette étape.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Créer un point de connexion de service dans Active Directory local

Appareils à un domaine utilisera le point de connexion de service pour découvrir les informations de client Azure AD au moment de l’enregistrement automatique avec le service d’inscription appareil Azure.

Sur le serveur Azure AD Connect, exécutez les commandes PowerShell suivantes :

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Lors de l’exécution de l’applet de commande $aadAdminCred = Get-Credential, utilisez le format *user@example.com* pour le nom d’utilisateur de l’information d’identification qui est entrée lorsque la fenêtre contextuelle Get-Credential s’affiche.

Lorsque vous exécutez l’applet de commande initialisation ADSyncDomainJoinedComputerSync..., remplacez [*nom du compte connecteur*] avec le compte de domaine qui est utilisé comme le compte de connecteur Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Configurer des règles de revendications AD FS
Configurer les règles de revendications AD FS permet instantanée d’enregistrement d’un ordinateur sur lequel le service d’inscription appareils Azure en permettant aux ordinateurs s’authentifier à l’aide de Kerberos/NTLM via AD FS. Sans cette étape, ordinateurs obtiendrez à Azure Active Directory de manière différée (soumis à la fois de synchronisation Azure AD Connect).

>[AZURE.NOTE]
Si vous n’avez pas AD FS en tant que la fédération serveur local, suivez les instructions de votre fournisseur pour créer des règles de réclamer.

Sur le serveur AD FS (ou sur une session connecté au serveur AD FS), exécutez les commandes PowerShell suivantes :

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Ordinateurs Windows 10 va authentifier à l’aide de l’authentification Windows intégrée à un point de terminaison Web approbation active hébergé par AD FS. Vérifiez que ce point de terminaison est activée. Si vous utilisez le Proxy d’authentification Web, également vous assurer que ce point de terminaison est publié via le serveur proxy. Vous pouvez le faire en vérifiant l’adfs/services/approbation/13/windowstransport. Il doit figurer comme étant activés dans la console de gestion AD FS sous **Service** > **points de terminaison**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Étape 2 : Configurer l’inscription automatique appareil via la stratégie de groupe dans Active Directory

Vous pouvez utiliser la stratégie de groupe dans Active Directory pour configurer vos appareils Windows 10 à un domaine pour enregistrer automatiquement avec Azure AD.

> [AZURE.NOTE]
> Pour voir dernières obtenir des instructions sur la configuration d’inscription automatique appareils mobiles, [la configuration de l’enregistrement automatique du domaine Windows joint appareils avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Ce modèle de stratégie de groupe a été renommé dans Windows 10. Si vous exécutez l’outil de stratégie de groupe à partir d’un ordinateur Windows 10, la stratégie s’affichent en tant que : <br>
> **Domaine de Registre des ordinateurs connectés en tant qu’unités**<br>
> La stratégie est à l’emplacement suivant :<br>
> ***Inscription de l’ordinateur modèles Configuration/stratégies/administration/Windows composants/appareil***


## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
