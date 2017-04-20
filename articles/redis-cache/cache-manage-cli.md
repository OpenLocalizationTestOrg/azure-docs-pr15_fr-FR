<properties 
    pageTitle="Comment créer et gérer le Cache Redis Azure à l’aide de l’Interface de ligne Azure (commande Azure) | Microsoft Azure" 
    description="Découvrez comment installer l’infrastructure du langage commun Azure sur n’importe quelle plate-forme, comment l’utiliser pour vous connecter à votre compte Azure et comment créer et gérer un cache Redis depuis l’interface Azure." 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Comment créer et gérer le Cache Redis Azure à l’aide de l’Interface de ligne Azure (commande Azure)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure infrastructure du langage commun](cache-manage-cli.md)

L’infrastructure du langage commun Azure est un excellent moyen pour gérer votre infrastructure Azure à partir de n’importe quelle plate-forme. Cet article vous explique comment créer et gérer vos instances de Cache Redis Azure à l’aide de l’infrastructure du langage commun Azure.

## <a name="prerequisites"></a>Conditions préalables

Pour créer et gérer des instances de Cache Redis Azure à l’aide d’Azure infrastructure du langage commun, vous devez effectuer les étapes suivantes.

-   Vous devez disposer d’un compte Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) dans quelques instants.
-   [Installer l’infrastructure du langage commun Azure](../xplat-cli-install.md).
-   Connectez votre installation Azure infrastructure du langage commun avec un compte personnel Azure, ou avec un professionnel ou scolaire compte Azure et se connecter à partir de l’infrastructure du langage commun Azure à l’aide du `azure login` commande. Pour mieux comprendre les différences et choisissez, voir [se connecter à un abonnement Azure à partir de l’Interface de ligne de Azure (commande Azure)](../xplat-cli-connect.md).
-   Avant d’exécuter une des commandes suivantes, basculez l’infrastructure du langage commun Azure en mode de gestionnaire de ressources en exécutant la `azure config mode arm` commande. Pour plus d’informations, voir [définir le mode d’Azure le Gestionnaire de ressources](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode).

## <a name="redis-cache-properties"></a>Redis des propriétés de Cache

Les propriétés suivantes sont utilisées lorsque vous créez et mise à jour des instances de Cache Redis.

| Propriété            | Commutateur                                  | Description                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nom                | -n,--nom                              | Nom du Cache Redis.                                                                                                                                                                                                                             |
| groupe de ressources      | -g,--groupe de ressources                    | Nom du groupe de ressources.                                                                                                                                                                                                                          |
| emplacement            | -l,--emplacement                          | Emplacement de création du cache.                                                                                                                                                                                                                            |
| taille                | -z,--taille                              | Taille du Cache Redis. Valeurs valides : [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]                                                                                                                                                                  |
| référence (SKU)                 | -x,--référence (SKU)                               | Redis référence (SKU). Doit être égale à : [basique, Standard, Premium]                                                                                                                                                                                             |
| EnableNonSslPort    | e-,--enable-non-ssl-port               | Propriété EnableNonSslPort du Cache Redis. Ajoutez cet indicateur si vous voulez activer le Port SSL Non pour le cache                                                                                                                                    |
| Redis Configuration | -c,--redis-configuration               | Redis Configuration. Entrez une chaîne JSON mis en forme des clés de configuration et des valeurs ici. Format : « { » » : « », « » : » «} »                                                                                                                                     |
| Redis Configuration | -f,--redis fichier de configuration          | Redis Configuration. Entrez le chemin d’accès d’un fichier contenant des clés de configuration et valeurs ici. Format de l’entrée du fichier : { » » : « », « » : « »}                                                                                                                |
| Éclater Nb         | -r,--éclater Nb                       | Nombre de milieu des fragments pour créer un cache de Cluster Premium avec cluster.                                                                                                                                                                               |
| Réseau virtuel     | -v,--réseau virtuel                   | Lorsque vous hébergez le cache dans un VNET, spécifie l’ID de ressource exacte processeur du réseau virtuel pour déployer le cache redis dans. Exemple de format : /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| type de clé            | -t,--type de clé                          | Type de clé pour procéder au renouvellement. Valeurs valides : [principal, secondaire]                                                                                                                                                                                             |
| StaticIP            | -p,--statique ip < statique ip >             | Lorsque vous hébergez le cache dans un VNET, indique une adresse IP unique dans le sous-réseau pour le cache. Si ne pas fourni, une est choisie pour vous à partir du sous-réseau.                                                                                                |
| Sous-réseau              | t,--sous-réseau<subnet>                    | Lorsque vous hébergez le cache dans un VNET, indique le nom du sous-réseau dans laquelle vous voulez déployer le cache.                                                                                                                                                    |
| VirtualNetwork      | -v,--réseau virtuel <-réseau virtuel > | Lorsque vous hébergez le cache dans un VNET, spécifie l’ID de ressource exacte processeur du réseau virtuel pour déployer le cache redis dans. Exemple de format : /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Abonnement        | -d,--abonnement                      | L’identificateur d’abonnement.                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>Afficher toutes les commandes de Cache Redis

Pour afficher toutes les commandes de Cache Redis et leurs paramètres, utilisez la `azure rediscache -h` commande.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Créer un Cache Redis

Pour créer un Cache Redis, utilisez la commande suivante :

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Pour plus d’informations sur cette commande, exécutez la `azure rediscache create -h` commande.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Supprimer un Cache Redis existant

Pour supprimer un Cache Redis, utilisez la commande suivante :

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Pour plus d’informations sur cette commande, exécutez la `azure rediscache delete -h` commande.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Liste toutes les cache Redis au sein de votre abonnement ou d’un groupe de ressources

Pour répertorier tous les cache Redis au sein de votre abonnement ou d’un groupe de ressources, utilisez la commande suivante :

    azure rediscache list [options]

Pour plus d’informations sur cette commande, exécutez la `azure rediscache list -h` commande.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Afficher les propriétés d’un Cache Redis existant

Pour afficher les propriétés d’un Cache Redis existant, utilisez la commande suivante :

    azure rediscache show [--name <name> --resource-group <resource-group>]

Pour plus d’informations sur cette commande, exécutez la `azure rediscache show -h` commande.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>Modifier les paramètres d’un Cache Redis existant

Pour modifier les paramètres d’un Cache Redis existant, utilisez la commande suivante :

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Pour plus d’informations sur cette commande, exécutez la `azure rediscache set -h` commande.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renouveler la clé d’authentification pour un Cache Redis existant

Pour renouveler la clé d’authentification pour un Cache Redis existant, utilisez la commande suivante :

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Spécifier `Primary` ou `Secondary` pour `key-type`.

Pour plus d’informations sur cette commande, exécutez la `azure rediscache renew-key -h` commande.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Touches de liste principales et secondaires d’un Cache Redis existant

Aux clés principales et secondaires liste d’un Cache Redis existant, utilisez la commande suivante :

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Pour plus d’informations sur cette commande, exécutez la `azure rediscache list-keys -h` commande.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
