<properties 
    pageTitle="Déployer et gérer des Hubs de Notification à l’aide de PowerShell" 
    description="Comment créer et gérer des Hubs de Notification à l’aide de PowerShell pour l’Automation" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Déployer et gérer des Hubs de Notification à l’aide de PowerShell

##<a name="overview"></a>Vue d’ensemble

Cet article vous explique comment utiliser créer et gérer Azure Notification Hubs à l’aide de PowerShell. Les tâches d’automatisation courantes suivantes sont représentées dans cette rubrique.

+ Créer un concentrateur de Notification
+ Définir les informations d’identification

Si vous devez également créer un nouvel espace de noms bus service pour votre hubs de notification, voir [Gérer les Bus de Service avec PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gestion des Notifications Hubs n’est pas prise en charge directement par les applets de commande inclus avec Azure PowerShell. La meilleure approche à partir de PowerShell consiste à référencer l’assembly Microsoft.Azure.NotificationHubs.dll. L’assembly est distribué avec le [package Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme basée sur l’abonnement. Pour plus d’informations sur l’obtention d’un abonnement, voir [Options d’achat], [Propose des membres]ou [Version d’évaluation gratuite].

- Un ordinateur sur lequel Azure PowerShell. Pour plus d’informations, voir [installer et configurer Azure PowerShell].

- Une idée des scripts PowerShell et .NET Framework packages NuGet.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluant une référence à l’assembly .NET Bus des services

Gestion des Hubs de Notification Azure n’est pas encore inclus dans les applets de commande PowerShell dans Azure PowerShell. Pour mettre en service hubs de notification, vous pouvez utiliser le client .NET fourni dans le [package Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Tout d’abord, vérifiez que votre script peut trouver l’assembly **Microsoft.Azure.NotificationHubs.dll** , qui est installé comme package NuGet dans un projet Visual Studio. Pour être flexibles, le script procède comme suit :

1. Détermine le chemin d’accès à partir duquel il a été appelé.
2. Parcourt le chemin d’accès jusqu'à ce qu’elle trouve un dossier nommé `packages`. Ce dossier est créé lorsque vous installez packages NuGet pour les projets Visual Studio.
3. Recherche de manière récursive la `packages` dossier pour un assembly nommé **Microsoft.Azure.NotificationHubs.dll**.
4. Fait référence à l’assembly afin que les types sont disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentées dans un script PowerShell :

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Créer la classe NamespaceManager

Pour la mise en service de Notification Hubs, créer une instance de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) à partir du Kit de développement. 

Vous pouvez utiliser l’applet de commande [Get-AzureSBAuthorizationRule] inclus avec Azure PowerShell pour récupérer une règle d’autorisation qui est utilisée pour fournir une chaîne de connexion. Nous allons stocker une référence à la `NamespaceManager` instance dans le `$NamespaceManager` variable. Nous utiliserons `$NamespaceManager` un concentrateur de notification de mise en service.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Mise en service un nouveau concentrateur de Notification 

Pour configurer un nouveau concentrateur de notification, utilisez l' [API .NET pour Hubs de Notification].

Dans cette partie du script configurer quatre variables locales. 

1. `$Namespace`: Définissez cette le nom de l’espace de noms dans lequel vous voulez créer un concentrateur de notification.
2. `$Path`: Définissez ce chemin d’accès sur le nom du nouveau concentrateur de notification.  Par exemple, « MyHub ».    
3. `$WnsPackageSid`: Définir le package identificateur de sécurité pour vous application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Définir le code secret pour vous application Windows à partir du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Ces variables sont utilisées pour vous connecter à votre espace de noms et créer un nouveau concentrateur de Notification configuré pour gérer les notifications de Services de Notification de Windows (WNS) avec les informations d’identification WNS pour une application Windows. Pour plus d’informations sur l’obtention du package identificateur de sécurité et de clé secrète, consultez la rubrique [Prise en main avec Hubs de Notification](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) didacticiel. 

+ L’extrait de code de script utilise la `NamespaceManager` objet pour vérifier si le Hub de Notification identifiée par `$Path` existe.

+ Si elle n’existe pas, le script crée un `NotificationHubDescription` avec WNS les informations d’identification et passer à la `NamespaceManager` classe `CreateNotificationHub` méthode.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Ressources supplémentaires

- [Gérer le Service Bus avec PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Comment créer des files d’attente, les rubriques et les abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un Namespace Bus de Service et un concentrateur de l’événement à l’aide d’un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Certains scripts prêts à l’emploi sont également disponibles pour téléchargement :
- [Service Bus des Scripts PowerShell](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Options d’achat]: http://azure.microsoft.com/pricing/purchase-options/
[Offres de membres]: http://azure.microsoft.com/pricing/member-offers/
[Version d’évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installer et configurer PowerShell Azure]: ../powershell-install-configure.md
[API .NET pour Hubs de Notification]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
