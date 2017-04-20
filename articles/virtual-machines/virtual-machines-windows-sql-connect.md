<properties
    pageTitle="Se connecter à un ordinateur virtuelle SQL Server (Gestionnaire de ressources) | Microsoft Azure"
    description="Découvrez comment vous connecter à SQL Server s’exécutant sur une Machine virtuelle dans Azure. Cette rubrique utilise le modèle de déploiement classique. Les scénarios diffèrent selon la configuration réseau et l’emplacement du client."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Se connecter à un serveur SQL Machine virtuelle sur Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-connect.md)
- [Classique](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Vue d’ensemble

Cette rubrique décrit comment se connecter à votre instance de SQL Server s’exécutant sur une machine virtuelle Azure. Il traite certains [scénarios de connectivité général](#connection-scenarios) et fournit ensuite [la procédure détaillée pour la configuration de la connectivité de SQL Server dans une machine virtuelle Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure classique](virtual-machines-windows-classic-sql-connect.md).

Si vous préférez qu’une vue d’ensemble complet de mise en service et la connectivité, voir [mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scénarios de connexion

La façon dont un client se connecte à SQL Server s’exécutant sur une Machine virtuelle varie selon l’emplacement du client et la configuration de l’ordinateur ou au réseau. Ces scénarios sont les suivantes :

- [Se connecter à SQL Server via internet](#connect-to-sql-server-over-the-internet)
- [Se connecter à SQL Server dans le même réseau virtuel](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Se connecter à SQL Server via Internet

Si vous voulez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, il existe plusieurs étapes requises, telles que la configuration du pare-feu, l’activation de l’authentification SQL et configuration de votre groupe de sécurité réseau vous devez configurer une règle de groupe de sécurité de réseau pour autoriser le trafic TCP sur le port 1433.

Si vous utilisez le portail de mise en service d’une image de machine virtuelle SQL Server avec le Gestionnaire de ressources, ces étapes sont effectuées automatiquement lorsque vous sélectionnez **Public** pour l’option de connectivité SQL :

![Option de connectivité public SQL pendant la mise en service](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Si ce n’est pas une lors de la configuration, puis vous pouvez configurer manuellement SQL Server et vos machines virtuelles en suivant la [procédure dans cet article pour configurer manuellement la connectivité](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] L’image machines virtuelles SQL Server Express Edition n’active pas automatiquement le protocole TCP/IP. Pour l’édition Express, vous devez utiliser le Gestionnaire de Configuration SQL Server pour [activer manuellement le protocole TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) après la création de la machine virtuelle.

Une fois que cela est fait, n’importe quel client ayant accès à internet peut se connecter à l’instance SQL Server en spécifiant l’adresse IP de la machine virtuelle ou l’étiquette DNS affectée à cette adresse IP. Si le port SQL Server est 1433, vous n’avez pas besoin de spécifier la chaîne de connexion.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Même si cela permet la connectivité des clients via internet, cela n’implique pas que tout le monde peut se connecter à votre serveur SQL Server. À l’extérieur de clients doivent le nom d’utilisateur correct et un mot de passe. Pour renforcer la sécurité, vous pouvez éviter le port 1433 connu. Par exemple, si vous configuré SQL Server pour écouter sur le port 1500 et établie pare-feu ad hoc et les règles de groupe de sécurité réseau, vous pouvez connecter en ajoutant le numéro de port pour le nom du serveur comme dans l’exemple suivant :

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Il est important de noter que lorsque vous utilisez cette technique pour communiquer avec SQL Server, toutes les données sortantes à partir du centre de données Azure sont couvertes normal [tarifs transferts de données sortant](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Se connecter à SQL Server dans le même réseau virtuel

[Réseau virtuel](../virtual-network/virtual-networks-overview.md) permet des scénarios supplémentaires. Vous pouvez vous connecter machines virtuelles dans le même réseau virtuel, même si ces machines virtuelles existent dans différents groupes de ressources. Et avec un [site à VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md), vous pouvez créer une architecture hybride qui se connecte machines virtuelles à machines et réseaux locaux.

Réseaux virtuels vous permet également de participer à vos ordinateurs virtuels Azure à un domaine. Il s’agit de la seule façon d’utiliser l’authentification Windows pour SQL Server. Les autres scénarios connexion requièrent l’authentification SQL avec les noms d’utilisateur et les mots de passe.

Si vous utilisez le portail de mise en service d’une image de machine virtuelle SQL Server avec le Gestionnaire de ressources, les règles de pare-feu appropriées pour les communications sur le réseau virtuel sont le programme d’installation lorsque vous sélectionnez **privé** pour que l’option de connectivité SQL. Si ce n’est pas une lors de la configuration, puis vous pouvez configurer manuellement SQL Server et vos machines virtuelles en suivant la [procédure dans cet article pour configurer manuellement la connectivité](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Mais si vous envisagez de configurer un environnement de domaine et l’authentification Windows, vous n’avez pas besoin d’utiliser les étapes décrites dans cet article pour configurer l’authentification SQL et les connexions. Également, vous n’avez pas besoin de configurer des règles de groupe de sécurité réseau pour l’accès par le biais d’internet.

>[AZURE.NOTE] L’image machines virtuelles SQL Server Express Edition n’active pas automatiquement le protocole TCP/IP. Pour l’édition Express, vous devez utiliser le Gestionnaire de Configuration SQL Server pour [activer manuellement le protocole TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) après la création de la machine virtuelle.

En supposant que vous avez configuré DNS de votre réseau virtuel, vous pouvez vous connecter à votre instance de SQL Server en spécifiant le nom d’ordinateur machine virtuelle SQL Server dans la chaîne de connexion. L’exemple suivant suppose également que l’authentification Windows a également été configurée et que l’utilisateur a accès à l’instance SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Notez que dans ce scénario, vous pouvez également spécifier l’adresse IP de la machine virtuelle.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Étapes de configuration manuelle de connectivité SQL Server dans une machine virtuelle Azure

Les étapes suivantes montrent comment configurer manuellement la connectivité à l’instance SQL Server et puis également pour vous connecter via internet à l’aide de SQL Server Management Studio (SSMS). Notez que la plupart de ces étapes sont effectuées automatiquement lorsque vous sélectionnez les options de connectivité de SQL Server appropriées dans le portail.

Avant de vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou sur internet, vous devez effectuer les tâches suivantes comme décrit dans les sections suivantes :

- [Ports TCP ouverts dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour écouter le protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions de l’authentification SQL Server](#create-sql-server-authentication-logins)
- [Configurer une règle de trafic entrant de groupe de sécurité réseau](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurer une étiquette de DNS pour l’adresse IP publique](#configure-a-dns-label-for-the-public-ip-address)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’instructions en même temps que ces étapes de connectivité de mise en service, voir [mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explorer les rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure.

D’autres rubriques liés à l’exécution de SQL Server dans Azure machines virtuelles, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
