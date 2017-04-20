<properties
    pageTitle="Se connecter à un ordinateur virtuelle SQL Server (standard) | Microsoft Azure"
    description="Découvrez comment vous connecter à SQL Server s’exécutant sur une Machine virtuelle dans Azure. Cette rubrique utilise le modèle de déploiement classique. Les scénarios diffèrent selon la configuration réseau et l’emplacement du client."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Se connecter à un serveur SQL Machine virtuelle sur Azure (déploiement classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-connect.md)
- [Classique](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Vue d’ensemble

Cette rubrique décrit comment se connecter à votre instance de SQL Server s’exécutant sur une machine virtuelle Azure. Il traite certains [scénarios de connectivité général](#connection-scenarios) et fournit ensuite [la procédure détaillée pour la configuration de la connectivité de SQL Server dans une machine virtuelle Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Si vous utilisez le Gestionnaire de ressources machines virtuelles, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure à l’aide du Gestionnaire de ressources](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scénarios de connexion

La façon dont un client se connecte à SQL Server s’exécutant sur une Machine virtuelle varie selon l’emplacement du client et la configuration de l’ordinateur ou au réseau. Ces scénarios sont les suivantes :

- [Se connecter à SQL Server dans le même service cloud](#connect-to-sql-server-in-the-same-cloud-service)
- [Se connecter à SQL Server via internet](#connect-to-sql-server-over-the-internet)
- [Se connecter à SQL Server dans le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Avant de vous connecter avec n’importe lequel de ces méthodes, vous devez suivre la [procédure dans cet article pour configurer la connectivité](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Se connecter à SQL Server dans le même service cloud

Machines virtuelles peuvent être créés dans le même service cloud. Pour mieux comprendre ce scénario machines virtuelles, Découvrez [comment connecter des machines virtuelles avec un service de réseau ou sur le cloud virtuel](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). Ce scénario est lorsqu’un client sur une machine virtuelle essaie de se connecter à SQL Server s’exécutant sur un autre ordinateur virtuel dans le même service cloud.

Dans ce scénario, vous pouvez vous connecter à l’aide de la mémoire virtuelle **nom** (également indiquée comme **Nom de l’ordinateur** ou **nom d’hôte** dans le portail). C’est le nom que vous avez fourni pour la machine virtuelle lors de la création. Par exemple, si vous avez nommé votre SQL VM **mysqlvm**, un client machine virtuelle dans le service cloud même peut utiliser la chaîne de connexion pour vous connecter :

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Se connecter à SQL Server via Internet

Si vous voulez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, vous devez créer un point de terminaison machine virtuelle pour les communications TCP entrantes. Cette étape de configuration Azure, achemine le trafic ports TCP entrant vers un port TCP qui n’est accessible à la machine virtuelle.

Pour vous connecter via internet, vous devez utiliser un nom DNS de l’ordinateur et le numéro de port de point de terminaison machine virtuelle (configuré plus loin dans cet article). Pour trouver le nom de DNS, accédez au portail Azure, puis sélectionnez **machines virtuelles (classique)**. Sélectionnez ensuite votre machine virtuelle. Le **nom DNS** s’affiche dans la section **vue d’ensemble** .

Par exemple, considérez une machine virtuelle classique nommée **mysqlvm** avec un nom DNS **mysqlvm7777.cloudapp.net** et un point de terminaison machine virtuelle de **57500**. En supposant que connectivité correctement configurée, la chaîne de connexion peut servir à accéder à la machine virtuelle à partir de n’importe quel emplacement sur internet :

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cela permet la connectivité des clients via internet, cela n’implique pas que tout le monde peut se connecter à votre serveur SQL Server. À l’extérieur de clients doivent le nom d’utilisateur correct et un mot de passe. Pour renforcer la sécurité, n’utilisez pas le port connu 1433 du point de terminaison machine virtuelle public. Et si possible, vous pouvez ajouter un et votre point de terminaison pour limiter le trafic uniquement aux clients autorisée. Pour obtenir des instructions sur l’utilisation des utilisateurs avec points de terminaison, voir [gérer l’et sur un point de terminaison](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] Il est important de noter que lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données sortantes à partir du centre de données Azure sont couvertes normal [tarifs transferts de données sortant](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Se connecter à SQL Server dans le même réseau virtuel

[Réseau virtuel](../virtual-network/virtual-networks-overview.md) permet des scénarios supplémentaires. Vous pouvez vous connecter machines virtuelles dans le même réseau virtuel, même si ces machines virtuelles existent dans les services en nuage différent. Et avec un [site à VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md), vous pouvez créer une architecture hybride qui se connecte machines virtuelles à machines et réseaux locaux.

Réseaux virtuels vous permet également de participer à vos ordinateurs virtuels Azure à un domaine. Il s’agit de la seule façon d’utiliser l’authentification Windows pour SQL Server. Les autres scénarios connexion requièrent l’authentification SQL avec les noms d’utilisateur et les mots de passe.

Si vous vous apprêtez à configurer un environnement de domaine et l’authentification Windows, vous n’avez pas besoin d’utiliser les étapes décrites dans cet article pour configurer le point de terminaison public ou l’authentification SQL et les connexions. Dans ce scénario, vous pouvez vous connecter à votre instance de SQL Server en spécifiant le nom de la machine virtuelle SQL Server dans la chaîne de connexion. L’exemple suivant suppose que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Étapes de configuration de la connectivité de SQL Server dans une machine virtuelle Azure

Les étapes suivantes expliquent comment se connecter à l’instance SQL Server via internet à l’aide de SQL Server Management Studio (SSMS). Toutefois, les mêmes étapes s’appliquent à rendre votre machine virtuelle SQL Server accessibles pour vos applications, en cours d’exécution à la fois en local et dans Azure.

Avant de vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou sur internet, vous devez effectuer les tâches suivantes comme décrit dans les sections suivantes :

- [Créer un point de terminaison TCP pour la machine virtuelle](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Ports TCP ouverts dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour écouter le protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions de l’authentification SQL Server](#create-sql-server-authentication-logins)
- [Déterminer le nom DNS de la machine virtuelle](#determine-the-dns-name-of-the-virtual-machine)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

Le chemin d’accès de connexion est synthétisé par le diagramme suivant :

![Se connecter à une machine virtuelle SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous prévoyez également à utiliser des groupes de disponibilité AlwaysOn pour haute disponibilité et reprise, vous devez envisager d’implémenter un récepteur. Clients de base de données se connecter au récepteur plutôt que directement à l’un des instances SQL Server. Que l’auditeur achemine clients vers réplica principal dans le groupe de disponibilité. Pour plus d’informations, voir [configurer un récepteur ILB pour les groupes de disponibilité AlwaysOn dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Il est important vérifier toutes les meilleures pratiques de sécurité pour SQL Server s’exécutant sur une machine virtuelle Azure. Pour plus d’informations, voir [Considérations relatives à la sécurité pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-security.md).

[Explorer les rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure. 

D’autres rubriques liés à l’exécution de SQL Server dans Azure machines virtuelles, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
