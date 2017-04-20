<properties
    pageTitle="Gérer le Cache Redis Azure avec PowerShell Azure | Microsoft Azure"
    description="Découvrez comment effectuer des tâches administratives pour Azure Redis Cache à l’aide de PowerShell Azure."
    services="redis-cache"
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gérer le Cache Redis Azure avec PowerShell Azure

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure infrastructure du langage commun](cache-manage-cli.md)

Cette rubrique montre comment effectuer des tâches courantes telles que la création, mettre à jour et mettre à l’échelle vos instances de Cache Redis Azure, comment régénérer les touches d’accès rapide et comment afficher des informations sur votre cache. Pour une liste complète des applets de commande PowerShell de Cache Redis Azure, voir [applets de commande Azure Redis Cache](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][modèle classique](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Conditions préalables

Si vous avez déjà installé PowerShell Azure, vous devez avoir Azure PowerShell version 1.0.0 ou version ultérieure. Vous pouvez vérifier la version de PowerShell Azure que vous avez installé avec cette commande à l’invite de commande PowerShell Azure.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Tout d’abord, vous devez vous connecter à Azure avec cette commande.

    Login-AzureRmAccount

Spécifier l’adresse de messagerie de votre compte Azure et son mot de passe de la fenêtre de connexion à Microsoft Azure.

Ensuite, si vous avez plusieurs abonnements Azure, vous devez définir votre abonnement Azure. Pour afficher la liste de vos abonnements en cours, exécutez cette commande.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pour spécifier l’abonnement, exécutez la commande suivante. Dans l’exemple suivant, le nom de l’abonnement est `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Avant de pouvoir utiliser Windows PowerShell avec le Gestionnaire de ressources Azure, vous devez les éléments suivants :

- Windows PowerShell, Version 3.0 ou 4.0. Pour rechercher la version de Windows PowerShell, tapez :`$PSVersionTable` et vérifiez que la valeur de `PSVersion` est 3.0 ou 4.0. Pour installer une version compatible, voir [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Pour obtenir une aide détaillée sur une applet de commande que vous voyez dans ce didacticiel, utilisez l’applet de commande Get-Help.

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide pour le `New-AzureRmRedisCache` applet de commande, tapez :

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Comment se connecter à Azure pour le gouvernement Cloud ou Azure Chine Cloud

Par défaut la Azure environnement est `AzureCloud`, qui représente l’instance nuage Azure global. Pour vous connecter à une autre instance, utilisez la `Add-AzureRmAccount` commande avec le `-Environment` ou -`EnvironmentName` commutateur de ligne de commande avec l’environnement souhaité ou le nom de l’environnement.

Pour voir la liste des environnements disponibles, exécutez la `Get-AzureRmEnvironment` applet de commande.

### <a name="to-connect-to-the-azure-government-cloud"></a>Pour vous connecter au Cloud pour le gouvernement Azure

Pour vous connecter au Cloud pour le gouvernement Azure, utilisez une des commandes suivantes.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Pour créer un cache dans le nuage pour le gouvernement Azure, utilisez un des emplacements suivants.

-   Virginie USGov
-   Iowa USGov

Pour plus d’informations sur le Cloud pour le gouvernement Azure, voir [Microsoft Azure pour le gouvernement](https://azure.microsoft.com/features/gov/) et [Guide du développeur Microsoft Azure pour le gouvernement](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Pour vous connecter au Cloud Chine Azure

Pour vous connecter au Cloud Chine Azure, utilisez une des commandes suivantes.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Pour créer un cache dans le nuage Chine Azure, utilisez un des emplacements suivants.

-   Moyen-Orient Chine
-   Amérique du Nord Chine

Pour plus d’informations sur le Cloud Chine Azure, voir [AzureChinaCloud pour Azure géré par 21Vianet en Chine](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Propriétés utilisées pour Azure Redis Cache PowerShell

Le tableau suivant contient des propriétés et des descriptions pour les paramètres couramment utilisés lors de la création et la gestion de vos instances de Cache Redis Azure à l’aide de PowerShell Azure.

| Paramètre          | Description                                                                                                                                                                                                        | Par défaut  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nom               | Nom du cache                                                                                                                                                                                                  |          |
| Emplacement           | Emplacement du cache                                                                                                                                                                                              |          |
| ResourceGroupName  | Nom de groupe de ressources dans lequel vous souhaitez créer le cache                                                                                                                                                                   |          |
| Taille               | La taille du cache. Les valeurs valides sont : P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 Mo, 1 Go, 2,5 Go, 6 Go, 13 Go, 26 Go, 53 Go                                                                     | 1 GO      |
| ShardCount         | Le nombre de milieu des fragments pour créer lorsque vous créez un cache premium avec cluster activé. Les valeurs valides sont : 1, 2, 3, 4, 5, 6, 7, 8, 9, 10                                                                                                      |          |
| RÉFÉRENCE (SKU)                | Spécifie la référence SKU du cache. Les valeurs valides sont : simple, Standard, Édition familiale Premium                                                                                                                                         | Standard |
| RedisConfiguration | Spécifie les paramètres de configuration Redis. Pour plus d’informations sur chaque paramètre, voir le tableau suivant [RedisConfiguration propriétés](#redisconfiguration-properties) . |          |
| EnableNonSslPort   | Indique si le port non SSL est activé.                                                                                                                                                                     | Faux    |
| MaxMemoryPolicy    | Ce paramètre est déconseillé : utilisez RedisConfiguration à la place.                                                                                                                                              |          |
| StaticIP           | Lorsque vous hébergez le cache dans un VNET, indique une adresse IP unique dans le sous-réseau pour le cache. Si ne pas fourni, une est choisie pour vous à partir du sous-réseau.                                                                                                                     |          |
| Sous-réseau             | Lorsque vous hébergez le cache dans un VNET, indique le nom du sous-réseau dans laquelle vous voulez déployer le cache.                                                                                                                  |          |
| VirtualNetwork     | Lorsque vous hébergez le cache dans un VNET, spécifie l’ID de la ressource de la VNET dans laquelle vous voulez déployer le cache.                                                                                                             |          |
| Type de clé            | Spécifie le touche d’accès rapide régénérer lors du renouvellement de touches d’accès rapide. Les valeurs valides sont : principal, secondaire |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>Propriétés RedisConfiguration

| Propriété                      | Description                                                                                                          | Tarifs niveaux       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| relationnelle sauvegarde activée            | Si la [Redis permanence des données](cache-how-to-premium-persistence.md) est activée                                     | Premium uniquement        |
| relationnelle-stockage-chaîne de connexion | La chaîne de connexion au compte de stockage de [Redis permanence des données](cache-how-to-premium-persistence.md)       | Premium uniquement        |
| fréquence de sauvegarde relationnelle          | Fréquence de sauvegarde pour [Redis permanence des données](cache-how-to-premium-persistence.md)                               | Premium uniquement        |
| réservé MaxMemory            | Configure la [mémoire réservés](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pour les processus non cache | Standard et Premium |
| stratégie de MaxMemory              | Configure la [stratégie de suppression](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pour le cache           | Tous les niveaux de prix   |
| événements keyspace avertir        | Configure [les notifications keyspace](cache-configure.md#keyspace-notifications-advanced-settings)                     | Standard et Premium |
| entrées hachage-max-ziplist      | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de petite agréger les données          | Standard et Premium |
| valeur hachage-max-ziplist        | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de petite agréger les données          | Standard et Premium |
| entrées jeu-max-intset        | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de petite agréger les données          | Standard et Premium |
| entrées zset-max-ziplist      | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de petite agréger les données          | Standard et Premium |
| valeur zset-max-ziplist        | Configure l' [Optimisation de la mémoire](http://redis.io/topics/memory-optimization) pour les types de petite agréger les données          | Standard et Premium |
| bases de données                     | Configure le nombre de bases de données. Cette propriété peut être configurée uniquement à la création du cache.                          | Standard et Premium |

## <a name="to-create-a-redis-cache"></a>Pour créer un Cache Redis

Nouvelle instance Azure Redis Cache créés à l’aide de l’applet de commande [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

>[AZURE.IMPORTANT] La première fois que vous créez un cache Redis dans un abonnement à l’aide du portail Azure, le portail enregistre la `Microsoft.Cache` espace de noms pour cet abonnement. Si vous essayez de créer le cache Redis première dans un abonnement à l’aide de PowerShell, vous devez d’abord enregistrer cet espace de noms à l’aide de la commande suivante : dans le cas contraire applets de commande telles que `New-AzureRmRedisCache` et `Get-AzureRmRedisCache` échouer.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Pour afficher la liste des paramètres disponibles et leur description pour `New-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Pour créer un cache avec les paramètres par défaut, exécutez la commande suivante.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, et `Location` sont des paramètres obligatoires, mais les autres sont facultatifs et leurs valeurs par défaut. Exécute la commande précédente crée une instance de référence (SKU) Azure Redis mémoire Cache Standard avec le nom spécifié, un emplacement et un groupe de ressources, qui est de 1 Go taille avec le port non SSL désactivé.

Pour créer un cache premium, spécifiez une taille de P1 (6 à 60 Go), P2 (13 à 130 Go), P3 (26 à 260 Go), ou P4 (53 à 530 Go). Pour activer le cluster, spécifiez un nombre partagé à l’aide du `ShardCount` paramètre. L’exemple suivant crée un cache premium P1 avec 3 milieu des fragments. Un cache premium P1 est de 6 Go taille et étant donné que nous avons spécifié trois milieu des fragments la taille totale est 18 (3 x 6 Go).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Pour spécifier des valeurs pour la `RedisConfiguration` paramètre, encadrer les valeurs à l’intérieur de `{}` comme clé/valeur comme paires `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. L’exemple suivant crée un cache 1 Go standard avec `allkeys-random` notifications de stratégie et keyspace maxmemory configurées avec `KEA`. Pour plus d’informations, voir [notifications Keyspace (paramètres avancés)](cache-configure.md#keyspace-notifications-advanced-settings) et [Maxmemory stratégie et réservé maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Pour configurer les paramètre lors de la création du cache de bases de données

La `databases` peut être configuré uniquement pendant la création du cache. L’exemple suivant crée une prime P3 (26 Go) cache les 48 bases de données à l’aide de l’applet de commande [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Pour plus d’informations sur la `databases` propriété, voir [configuration du serveur par défaut du Cache Redis les Azure](cache-configure.md#default-redis-server-configuration). Pour plus d’informations sur la création d’un cache à l’aide de l’applet de commande [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) , consultez la section précédente [pour créer un Cache Redis](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Mettre à jour un cache Redis

Instances de Cache Redis Azure sont mis à jour à l’aide de l’applet de commande [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Pour afficher la liste des paramètres disponibles et leur description pour `Set-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

La `Set-AzureRmRedisCache` applet de commande peut être utilisée pour mettre à jour des propriétés telles que `Size`, `Sku`, `EnableNonSslPort`et la `RedisConfiguration` valeurs. 

La commande suivante met à jour la stratégie maxmemory du Cache Redis nommé myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Mettre à l’échelle un cache Redis

`Set-AzureRmRedisCache`peut être utilisé pour adapter un cache Azure Redis instance lorsque la `Size`, `Sku`, ou `ShardCount` propriétés sont modifiées. 

>[AZURE.NOTE]Mise à l’échelle un cache à l’aide de PowerShell est couvertes par les limites et les instructions en tant que la mise à l’échelle un cache à partir du portail Azure même. Vous pouvez mettre à l’échelle à un autre niveau de tarification avec les restrictions suivantes.
>
>-  Vous ne peut pas mettre à l’échelle à partir d’un niveau de tarification supérieur à un niveau de tarification inférieur.
>    -    Vous ne pouvez pas mettre à l’échelle à partir d’un cache **Premium** vers le bas jusqu'à un **Standard** ou un cache de **base** .
>    -    Vous ne pouvez pas mettre à l’échelle d’un cache **Standard** vers le bas jusqu'à un cache de **base** .
>-  Vous pouvez mettre à l’échelle d’un cache de **base** à un cache **Standard** , mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d’une autre taille, vous pouvez effectuer une opération de mise à l’échelle ultérieure à la taille souhaitée.
>-  Vous ne pouvez pas mettre à l’échelle à partir d’un cache de **base** directement à un cache **Premium** . Vous devez mettre à l’échelle de **base** à **Standard** en une seule opération de mise à l’échelle, puis de **Standard** à **Premium** dans une opération de mise à l’échelle suivante.
>-  Vous ne pouvez pas mettre à l’échelle à partir d’une taille supérieure vers le bas jusqu'à la **C0 (250 Mo)** taille.
>
>Pour plus d’informations, voir [comment échelle Azure Redis Cache](cache-how-to-scale.md).

L’exemple suivant montre comment mettre à l’échelle un cache nommé `myCache` à un cache 2,5 Go. Notez que cette commande fonctionne pour un Basic ou un cache Standard.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Une fois que cette commande est émise, l’état du cache est renvoyé (semblable à l’appel `Get-AzureRmRedisCache`). Notez que la `ProvisioningState` est `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Une fois l’opération de mise à l’échelle terminée, le `ProvisioningState` devient `Succeeded`. Si vous avez besoin effectuer une opération de mise à l’échelle ultérieure, telles que la modification de base en Standard, puis en modifiant la taille, vous devez attendre que l’opération précédente est terminée ou vous recevez un message d’erreur semblable au suivant.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Pour obtenir des informations sur un cache Redis

Vous pouvez récupérer des informations sur un cache à l’aide de l’applet de commande [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Pour afficher la liste des paramètres disponibles et leur description pour `Get-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Pour renvoyer des informations sur tous les cache dans l’abonnement actif, exécutez `Get-AzureRmRedisCache` sans paramètres.

    Get-AzureRmRedisCache

Pour renvoyer des informations sur tous les cache dans un groupe de ressources spécifique, exécutez `Get-AzureRmRedisCache` avec la `ResourceGroupName` paramètre.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Pour renvoyer des informations sur un cache spécifique, exécutez `Get-AzureRmRedisCache` avec la `Name` paramètre contenant le nom du cache et la `ResourceGroupName` paramètre avec le groupe de ressources contenant en cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Pour récupérer les touches d’accès pour un cache Redis

Pour récupérer les touches d’accès rapide pour le cache, vous pouvez utiliser l’applet de commande [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Pour afficher la liste des paramètres disponibles et leur description pour `Get-AzureRmRedisCacheKey`, exécutez la commande suivante.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Pour récupérer les clés pour le cache, appelez le `Get-AzureRmRedisCacheKey` applet de commande et passez le nom de votre cache le nom du groupe de ressources qui contient le cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Régénérer les touches d’accès rapide pour le cache de résolution Redis

Pour régénérer les touches d’accès rapide pour le cache, vous pouvez utiliser l’applet de commande [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Pour afficher la liste des paramètres disponibles et leur description pour `New-AzureRmRedisCacheKey`, exécutez la commande suivante.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Pour régénérer la clé primaire ou secondaire pour votre cache, appelez le `New-AzureRmRedisCacheKey` applet de commande et passer dans le nom, groupe de ressources et spécifier `Primary` ou `Secondary` pour la `KeyType` paramètre. Dans l’exemple suivant, la touche d’accès secondaire pour un cache est régénérer.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Pour supprimer un cache Redis

Pour supprimer un cache Redis, utilisez l’applet de commande [Supprimer AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Pour afficher la liste des paramètres disponibles et leur description pour `Remove-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Dans l’exemple suivant, le cache nommé `myCache` est supprimé.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Pour importer un cache Redis

Vous pouvez importer des données dans une instance de Cache Redis Azure à l’aide de la `Import-AzureRmRedisCache` applet de commande.

>[AZURE.IMPORTANT] Importation/exportation est disponible uniquement pour cache [niveau premium](cache-premium-tier-intro.md) . Pour plus d’informations sur l’importation/exportation, voir [Importer et exporter des données dans le Cache Redis Azure](cache-how-to-import-export-data.md).

Pour afficher la liste des paramètres disponibles et leur description pour `Import-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

La commande suivante importe des données à partir du blob spécifié par l’uri associations de sécurité dans Azure Redis Cache.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Pour exporter un cache Redis

Vous pouvez exporter des données à partir d’une instance de Cache Redis Azure à l’aide de la `Export-AzureRmRedisCache` applet de commande.

>[AZURE.IMPORTANT] Importation/exportation est disponible uniquement pour cache [niveau premium](cache-premium-tier-intro.md) . Pour plus d’informations sur l’importation/exportation, voir [Importer et exporter des données dans le Cache Redis Azure](cache-how-to-import-export-data.md).

Pour afficher la liste des paramètres disponibles et leur description pour `Export-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


La commande suivante exporte les données à partir d’une instance de Cache Redis Azure dans le conteneur spécifié par l’uri associations de sécurité.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Pour redémarrer un cache Redis

Vous pouvez redémarrer votre instance de Cache Redis Azure à l’aide de la `Reset-AzureRmRedisCache` applet de commande.

>[AZURE.IMPORTANT] Redémarrage est disponible uniquement pour cache [niveau premium](cache-premium-tier-intro.md) . Pour plus d’informations sur le redémarrage de votre cache, consultez [administration Cache - redémarrer](cache-administration.md#reboot).

Pour afficher la liste des paramètres disponibles et leur description pour `Reset-AzureRmRedisCache`, exécutez la commande suivante.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

La commande suivante redémarre les deux nœuds du cache spécifié.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Windows PowerShell avec Azure, consultez les ressources suivantes :

- [Azure documentation applet de commande de Cache Redis sur MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Applets de commande Azure Gestionnaire de ressources](http://go.microsoft.com/fwlink/?LinkID=394765): Découvrez comment utiliser les applets de commande dans le module AzureResourceManager.
- [Groupes de ressources à l’aide pour gérer vos ressources Azure](../resource-group-template-deploy-portal.md): Découvrez comment créer et gérer des groupes de ressources dans le portail Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure): en savoir plus sur les nouvelles fonctionnalités dans Azure.
- [Blog de Windows PowerShell](http://blogs.msdn.com/powershell): en savoir plus sur les nouvelles fonctionnalités dans Windows PowerShell.
- [« Bonjour, l’écriture de script du Bonhomme ! » Blog](http://blogs.technet.com/b/heyscriptingguy/): obtenir réelles conseils et astuces à partir de la Communauté Windows PowerShell.
