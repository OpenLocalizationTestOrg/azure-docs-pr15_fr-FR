<properties
   pageTitle="Créez le service Azure principal avec PowerShell | Microsoft Azure"
   description="Décrit comment utiliser PowerShell Azure pour créer une application Active Directory et principal du service et lui accorder l’accès aux ressources via un contrôle d’accès basé sur un rôle. Il indique comment authentifier application avec un mot de passe ou un certificat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Utiliser PowerShell Azure pour créer une entité de service pour accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure infrastructure du langage commun](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)

Lorsque vous avez une application ou un script qui doit accéder aux ressources, il est probablement ne souhaitez pas exécuter ce processus sous vos propres informations d’identification. Vous devrez différentes autorisations que vous voulez pour l’application, et vous ne souhaitez pas l’application de continuer à utiliser vos informations d’identification si vos responsabilités modifier. À la place, vous créez une identité de l’application qui inclut des informations d’authentification et les attributions de rôle. Chaque fois que l’application est exécutée, il authentifie avec ces informations d’identification. Cette rubrique vous montre comment utiliser [PowerShell Azure](powershell-install-configure.md) pour configurer tout ce dont vous avez besoin d’une application pour s’exécuter sous sa propre identité et les informations d’identification.

Avec PowerShell, vous avez deux possibilités pour l’authentification de l’application AD :

 - mot de passe
 - certificat

Cette rubrique indique comment utiliser les deux options dans PowerShell. Si vous souhaitez connecter à Azure depuis une infrastructure de programmation (telle Python, Ruby ou Node.js), l’authentification par mot de passe peut-être être la meilleure option. Avant de décider d’utiliser un mot de passe ou un certificat, consultez la section [exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les cadres différents.

## <a name="active-directory-concepts"></a>Concepts relatifs à Active Directory

Dans cet article, vous créez deux objets - l’application Active Directory (AD) et le service principal. L’application AD est une représentation globale de votre application. Il contient les informations d’identification (un id de l’application et un mot de passe ou certificat). Le service principal est la représentation de votre application dans un Active Directory local. Il contient l’attribution de rôle. Cette rubrique se concentre sur une application unique client dans lequel l’application est destinée à exécuter au sein d’une organisation. Vous utilisez généralement applications unique client pour les applications de métier qui s’exécutent au sein de votre organisation. Dans une application unique client, vous avez une application AD et principal d’un service.

Vous vous demandez peut-être - pourquoi dois-je les deux objets ? Cette approche est plus pertinent pour lorsque vous envisagez d’applications clients multiples. Vous utilisez généralement clients à plusieurs applications pour les applications logicielles en tant que service (SaaS), où votre application est exécutée dans nombreux différents abonnements. Pour les applications clients multiples, vous avez une application AD et plusieurs identités de service (un dans chaque Active Directory qui autorise l’accès à l’application). Pour configurer une application cliente multiples, consultez le [guide du développeur autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorisations requises

Pour effectuer cette rubrique, vous devez disposer des autorisations suffisantes dans votre Azure Active Directory et votre abonnement Azure. En particulier, vous devez être en mesure de créer une application dans l’annuaire Active Directory et affecter l’entité de service à un rôle. 

Dans votre Active Directory, votre compte doit être un administrateur (par exemple, **l’Administrateur Global** ou **Administrateur de l’utilisateur**). Si votre compte est attribué au rôle **d’utilisateur** , vous devez être un administrateur élever vos autorisations.

Dans votre abonnement, votre compte doit avoir `Microsoft.Authorization/*/Write` accès, ce qui est accordé via le rôle de [propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Si votre compte est affecté au rôle de **collaborateur** , vous recevez un message d’erreur lorsque vous tentez d’affecter l’entité de service à un rôle. Là encore, votre administrateur d’abonnement doit vous accorder un accès suffisant.

À présent, passez à une section pour l’authentification de [mot de passe](#create-service-principal-with-password) ou [certificat](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Créez le service principal avec mot de passe

Dans cette section, vous effectuez les étapes pour :

- créer l’application AD avec un mot de passe
- créer l’entité de service
- affecter le rôle de lecteur à l’entité de service

Pour rapidement effectuer ces étapes, voir les applets de trois commande. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Passons en revue ces étapes plus attentivement pour vous assurer que comprendre le processus.

1. Connectez-vous à votre compte.

        Add-AzureRmAccount

1. Créer une nouvelle application Active Directory en fournissant un nom d’affichage, l’URI qui décrit votre application, les URI qui identifient votre application et le mot de passe pour votre identité de l’application.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Pour les applications unique client, les URI ne sont pas validées.
     
     Si votre compte n’a pas les [autorisations requises](#required-permissions) dans l’annuaire Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».

1. Examinez le nouvel objet application. 

        $app
        
     Notez en particulier la propriété **ApplicationId** , qui est requis pour la création d’entités de service, attributions de rôle et acquisition le jeton d’accès.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Créez un principal de service pour votre application en passant l’id de l’application de l’application Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Accorder le service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de service au rôle de **lecteur** , qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, voir [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre **nom principal de service** , fournissent **ApplicationId** que vous avez utilisé lors de la création de l’application. 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si votre compte n’a pas les autorisations suffisantes pour attribuer un rôle, vous recevez un message d’erreur. Le message indique votre compte **n’a pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write' sur étendue « / abonnements / {guid} »**. 

Voilà ! Votre application AD et principal de service sont configurés. La section suivante vous montre comment ouvrir une session avec les informations d’identification via PowerShell. Si vous voulez utiliser les informations d’identification dans votre application de code, vous pouvez atteindre les [exemples d’applications](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Fournir des informations d’identification via PowerShell

À présent, vous devez ouvrir une session en tant que l’application pour effectuer des opérations.

1. Créer un objet **PSCredential** qui contient vos informations d’identification en exécutant la commande **Get-Credential** . Vous devez **ApplicationId** avant d’exécuter cette commande Vérifiez que vous avez disponibles pour coller.

        $creds = Get-Credential

2. Vous êtes invité à entrer vos informations d’identification. Le nom d’utilisateur, utilisez **ApplicationId** que vous avez utilisé lors de la création de l’application. Le mot de passe, utilisez celui que vous avez spécifié lors de la création du compte.

     ![Entrez les informations d’identification](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Chaque fois que vous êtes connecté en tant qu’une entité de service, vous devez fournir l’id de client de l’annuaire de votre application AD. Un client est une instance d’Active Directory. Si vous avez uniquement un abonnement, vous pouvez utiliser :

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Si vous avez plusieurs abonnements, spécifiez l’abonnement où se trouve votre Active Directory. Pour plus d’informations, voir [comment Azure abonnements sont associés à Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Se connecter en tant que l’entité de service en spécifiant que ce compte est une entité de service et qui fournit l’objet informations d’identification. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Vous êtes maintenant authentifié en tant que le service principal pour l’application Active Directory que vous avez créé.

### <a name="save-access-token-to-simplify-log-in"></a>Enregistrer le jeton d’accès pour simplifier la connexion

Pour éviter de fournir les informations d’identification principal service chaque fois qu’il doit se connecter, vous pouvez enregistrer le jeton d’accès.

1. Pour utiliser le jeton d’accès en cours dans une session ultérieure, enregistrez le profil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Ouvrez le profil et examinez son contenu. Notez qu’elle contient un jeton d’accès. 
        
2. Au lieu de manuellement une nouvelle connexion, il vous suffit de charger le profil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Le jeton d’accès arrive à expiration, à l’aide d’un profil enregistré fonctionne uniquement pour tant que le jeton est valide.
        
## <a name="create-service-principal-with-certificate"></a>Créez le service principal avec certificat

Dans cette section, vous effectuez les étapes pour :

- créer un certificat auto-signé
- créer l’application AD avec le certificat
- créer l’entité de service
- affecter le rôle de lecteur à l’entité de service

Pour effectuer rapidement ces étapes avec Azure PowerShell 2.0 sur Windows 10 ou Windows Server 2016 Technical Preview, voir les applets de commande. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Passons en revue ces étapes plus attentivement pour vous assurer que comprendre le processus. Cet article vous explique comment faire pour accomplir les tâches en utilisant les versions antérieures de systèmes d’exploitation ou PowerShell Azure également.

### <a name="create-the-self-signed-certificate"></a>Créer le certificat auto-signé

La version de PowerShell disponible avec Windows 10 et Windows Server 2016 Technical Preview a une applet de commande **New-SelfSignedCertificate** mis à jour pour générer un certificat auto-signé. Systèmes d’exploitation antérieurs ont l’applet de commande New-SelfSignedCertificate, mais elle ne propose pas les paramètres nécessaires pour cette rubrique. À la place, vous devez importer un module pour générer le certificat. Cette rubrique indique les deux approches pour générer le certificat basé sur le système d’exploitation que vous avez. 

- Si vous avez **Windows 10 ou Windows Server 2016 Technical Preview**, exécutez la commande suivante pour créer un certificat auto-signé : 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Si vous **ne possédez pas Windows 10 ou Windows Server 2016 Technical Preview**, vous devez télécharger le [auto-signé Générateur de certificat](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) à partir de Microsoft Script Center. Extraire le contenu et importer l’applet de commande que vous avez besoin.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Ensuite, générer le certificat.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Vous disposez de votre certificat et que vous pouvez commencer la création de votre application AD.

### <a name="create-the-active-directory-app-and-service-principal"></a>Créez le service et application Active Directory principal

1. Récupérer la valeur de clé à partir du certificat.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Connectez-vous à votre compte Azure.

        Add-AzureRmAccount

3. Créer une nouvelle application Active Directory en fournissant un nom d’affichage, l’URI qui décrit votre application, les URI qui identifient votre application et le mot de passe pour votre identité de l’application.

     Si vous avez Azure PowerShell 2.0 utilisation (août 2016 ou version ultérieure), l’applet de commande suivante :

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Si vous avez Azure PowerShell 1.0, utilisez l’applet de commande suivante :
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Pour les applications unique client, les URI ne sont pas validées.
    
    Si votre compte n’a pas les [autorisations requises](#required-permissions) dans l’annuaire Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».
        
    Examinez le nouvel objet application. 

        $app

    Avez-vous remarqué la propriété **ApplicationId** , qui est nécessaire pour la création des identités de service, attributions de rôle et acquisition de jetons d’accès.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Créez un principal de service pour votre application en passant l’id de l’application de l’application Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Accorder le service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de service au rôle de **lecteur** , qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, voir [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre **nom principal de service** , fournissent **ApplicationId** que vous avez utilisé lors de la création de l’application.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si votre compte n’a pas les autorisations suffisantes pour attribuer un rôle, vous recevez un message d’erreur. Le message indique votre compte **n’a pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write' sur étendue « / abonnements / {guid} »**.

Voilà ! Votre application AD et principal de service sont configurés. La section suivante vous montre comment vous connecter à l’aide de certificat via PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fournir le certificat par le biais de script PowerShell automatisé

Chaque fois que vous êtes connecté en tant qu’une entité de service, vous devez fournir l’id de client de l’annuaire de votre application AD. Un client est une instance d’Active Directory. Si vous avez uniquement un abonnement, vous pouvez utiliser :

    $tenant = (Get-AzureRmSubscription).TenantId
    
Si vous avez plusieurs abonnements, spécifiez l’abonnement où se trouve votre Active Directory. Pour plus d’informations, voir [administrer votre annuaire Azure AD](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Pour l’authentification dans votre script, spécifiez le compte est un service principal et fournir l’empreinte numérique du certificat, les id de l’application et l’id de client. Pour automatiser votre script, vous pouvez stocker ces valeurs en tant que variables d’environnement et de les récupérer pendant l’exécution, ou vous pouvez les inclure dans votre script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Vous êtes maintenant authentifié en tant que le service principal pour l’application Active Directory que vous avez créé.

## <a name="sample-applications"></a>Exemples d’applications

Les exemples d’application suivants montrent comment se connecter en tant que l’entité de service.

**.NET**

- [Déployer un SSH activé machine virtuelle avec un modèle avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gérer les ressources Azure et groupes de ressources avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Mise en route des ressources - déployer à l’aide de modèles Gestionnaire de ressources Azure - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Mise en route des ressources - gérer le groupe de ressources - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestion des ressources Azure et groupes de ressources avec Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gérer les ressources et groupes avec Node.js ressources Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestion des ressources Azure et groupes de ressources Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Étapes suivantes
  
- Pour plus d’informations sur l’intégration d’une application dans Azure pour la gestion des ressources, consultez le [guide du développeur autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).
- Pour une explication plus détaillée des applications et des identités de service, voir [objets Application et Principal du Service](./active-directory/active-directory-application-objects.md). 
- Pour plus d’informations sur l’authentification Active Directory, voir [Scénarios d’authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).



