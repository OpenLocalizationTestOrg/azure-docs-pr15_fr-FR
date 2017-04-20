<properties
   pageTitle="Obtenir les valeurs requises pour l’authentification d’une application pour accéder aux base de données SQL à partir du code | Microsoft Azure"
   description="Créer une entité de service pour l’accès à la base de données SQL à partir de code."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtenir les valeurs requises pour l’authentification d’une application pour accéder aux base de données SQL à partir du code

Pour créer et gérer la base de données SQL à partir du code, vous devez enregistrer votre application dans le domaine Azure Active Directory (DAS) dans l’abonnement où vos ressources Azure ont été créés.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Créer un service principal pour accéder aux ressources à partir d’une application

Vous devez disposer de la dernière [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) installé et en cours d’exécution. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Le script PowerShell suivant crée l’application Active Directory (AD) et l’entité de service que nous avons besoin de s’authentifier notre application c#. Le script renvoie des valeurs qu'avons-nous besoin pour l’exemple c# précédent. Pour plus d’informations, voir [Utiliser Azure PowerShell pour créer un service principal pour accéder aux ressources](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Voir aussi

- [Créer une base de données SQL avec c#](sql-database-get-started-csharp.md)
- [Connexion à la base de données SQL à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md)


