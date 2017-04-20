<properties
    pageTitle="Gérer le Service Bus avec PowerShell | Microsoft Azure"
    description="Gérer le Service Bus avec des scripts PowerShell"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Gérer le Service Bus avec PowerShell

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure PowerShell est un environnement de script que vous pouvez utiliser pour contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article décrit comment utiliser PowerShell pour la mise en service et gérer les entités Bus des services tels qu’espaces de noms, files d’attente et Hubs événement à l’aide d’une console PowerShell Azure locale.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer les conditions préalables suivantes :

- Un abonnement Azure. Azure est une plateforme basée sur l’abonnement. Pour plus d’informations sur l’obtention d’un abonnement, voir [Options d’achat][], [Propose des membres][]ou [Version d’évaluation gratuite][].

- Un ordinateur sur lequel Azure PowerShell. Pour plus d’informations, voir [installer et configurer Azure PowerShell][].

- Une idée des scripts PowerShell et .NET Framework packages NuGet.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluant une référence à l’assembly .NET Bus des services

Un nombre limité d’applets de commande PowerShell sont disponibles pour la gestion des Bus des services. Pour mettre en service entités qui ne sont pas exposées via les applets de commande existante, vous pouvez utiliser le client .NET Bus des services dans le [package NuGet Bus de Service][].

Tout d’abord, assurez-vous que le script peut trouver l’assembly **Microsoft.ServiceBus.dll** , qui est installé avec le package NuGet. Pour être flexibles, le script procède comme suit :

1. Détermine le chemin d’accès à partir duquel il a été appelé.
2. Parcourt le chemin d’accès jusqu'à ce qu’elle trouve un dossier nommé `packages`. Ce dossier est créé lors de l’installation de packages NuGet.
3. Recherche de manière récursive la `packages` dossier pour un assembly nommé **Microsoft.ServiceBus.dll**.
4. Fait référence à l’assembly afin que les types sont disponibles pour une utilisation ultérieure.

Voici comment ces étapes sont implémentées dans un script PowerShell :

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Mise en service un espace de noms Bus des services

Applets de commande PowerShell deux prennent en charge les opérations d’espace de noms de Bus des services. Au lieu de l’API .NET SDK, vous pouvez utiliser [Get-AzureSBNamespace][] et [AzureSBNamespace de nouveau][].

Cet exemple crée plusieurs variables locales dans le script ; `$Namespace` and `$Location`.

- `$Namespace`est le nom de l’espace de noms Bus des services dont nous voulez-vous utiliser.
- `$Location`identifie le centre de données dans laquelle le script dispositions l’espace de noms.
- `$CurrentNamespace`enregistre l’espace de noms de référence qui le script récupère (ou crée).

Dans un script réel, `$Namespace` et `$Location` peut être passé en tant que paramètres.

Cette partie du script effectue les tâches suivantes :

1. Tente de récupérer un espace de noms Bus des services avec le nom fourni.
2. Si l’espace de noms est détecté, il signale ce qui a été trouvé.
3. Si l’espace de noms ne trouve pas, il crée l’espace de noms, puis récupère l’espace de noms nouvellement créé.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Pour configurer d’autres entités Bus des services, créer une instance de la classe [NamespaceManager][] à partir du Kit de développement.
Vous pouvez utiliser l’applet de commande [Get-AzureSBAuthorizationRule][] pour récupérer une règle d’autorisation qui est utilisée pour fournir une chaîne de connexion. Nous allons stocker une référence à la `NamespaceManager` instance dans le `$NamespaceManager` variable. Nous utiliserons `$NamespaceManager` plus loin dans le script mise en service des autres entités.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Mise en service des autres entités Bus des services

Pour fournir des autres entités, tels que files d’attente et rubriques événement Hubs, utilisez l' [API .NET Bus des services][]. Cet article se concentre uniquement sur événement Hubs, mais les étapes pour d’autres entités sont identiques. En outre, des exemples plus détaillés, y compris les autres entités, sont référencées à la fin de cet article.

Cette partie du script crée quatre variables locales plus. Ces variables sont utilisées d’instanciation un `EventHubDescription` objet. Le script effectue les tâches suivantes :

1. À l’aide de la `NamespaceManager` (objet), vérifiez si le Hub de l’événement identifié par `$Path` existe.
2. Si elle n’existe pas, créez un `EventHubDescription` et passer à la `NamespaceManager` classe `CreateEventHubIfNotExists` méthode.
3. Après avoir déterminé que le Hub de l’événement est disponible, créez un groupe de consommateur à l’aide `ConsumerGroupDescription` et `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrer un espace de noms vers un autre abonnement Azure

La séquence de commandes suivante déplace un espace de noms d’un abonnement Azure à l’autre. Pour exécuter cette opération, l’espace de noms doit déjà être active et l’utilisateur qui exécute les commandes PowerShell doit être un administrateur sur l’abonnement à la source et la cible.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Étapes suivantes

Cet article fourni un plan de base pour la mise en service entités Bus des services à l’aide de PowerShell. Tout ce que vous pouvez faire en utilisant les bibliothèques de client .NET, vous pouvez également effectuer dans un script PowerShell.

Obtenir des exemples détaillés sont disponibles sur ces billets de blog :

- [Comment créer des files d’attente, les rubriques et les abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Comment créer un Namespace Bus de Service et un concentrateur de l’événement à l’aide d’un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Certains scripts prêts à l’emploi sont également disponibles pour téléchargement :
- [Service Bus des Scripts PowerShell](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Options d’achat]: http://azure.microsoft.com/pricing/purchase-options/
[Offres de membres]: http://azure.microsoft.com/pricing/member-offers/
[Version d’évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installer et configurer PowerShell Azure]: ../powershell-install-configure.md
[Package NuGet Bus de service]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nouvelle AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET Bus des services]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
