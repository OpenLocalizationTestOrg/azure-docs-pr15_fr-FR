<properties
    pageTitle="Mise en service d’un serveur Machine virtuelle SQL | Microsoft Azure"
    description="Créer et vous connecter à une machine virtuelle SQL Server dans Azure à l’aide du portail. Ce didacticiel utilise le mode Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Configurer une machine virtuelle SQL Server dans le portail Azure

> [AZURE.SELECTOR]
- [Portail](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Ce didacticiel de bout en bout pour vous montre comment utiliser le portail Azure pour mettre en service un ordinateur virtuel qui exécute SQL Server.

La galerie Azure machine virtuelle () inclut plusieurs images contenant Microsoft SQL Server. En quelques clics, vous pouvez sélectionner l’une des images SQL VM à partir de la galerie et mettre en service dans votre environnement Azure.

Dans ce didacticiel, vous allez :

- [Sélectionnez une image SQL VM dans la galerie](#select-a-sql-vm-image-from-the-gallery)
- [Configurer et créer la machine virtuelle](#configure-the-vm)
- [Ouvrez la machine virtuelle avec Bureau à distance](#open-the-vm-with-remote-desktop)
- [Se connecter à SQL Server à distance](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Sélectionnez une image SQL VM dans la galerie

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte.

    >[AZURE.NOTE] Si vous n’avez pas un compte Azure, visitez [Azure essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

1. Dans le portail Azure, cliquez sur **Nouveau**. Le portail ouvre la carte de **Nouveau** . Les ressources machine virtuelle SQL Server sont dans le groupe **Machines virtuelles** de la place de marché.

1. Dans la carte de **Nouveau** , cliquez sur **Machines virtuelles**.

1. Pour afficher toutes les images disponibles, cliquez sur **Afficher toutes** dans la carte de **Machines virtuelles** .

    ![Machines virtuelles Azure carte](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. Sous **serveurs de base de données**, cliquez sur **SQL Server**. Vous devrez peut-être faire défiler vers le bas pour localiser les **serveurs de base de données**. Passez en revue les modèles de SQL Server disponibles.

    ![Galerie Machine virtuelle SQL Images](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Chaque modèle identifie une version de SQL Server et un système d’exploitation. Sélectionnez une de ces images dans la liste. Puis passez en revue la carte de détails qui fournit une description de l’image de la machine virtuelle.

    >[AZURE.NOTE] Images SQL VM incluent les coûts de licence pour SQL Server dans le prix par minute de la machine virtuelle que vous créez. Il existe une autre option pour mettre votre-appartient-licence (BYOL) et rémunération uniquement pour la machine virtuelle. Les noms d’image sont précédés {BYOL}. Pour plus d’informations sur cette option, voir [prise en main SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).

1. Sous **Sélectionnez un modèle de déploiement**, vérifiez que **Le Gestionnaire de ressources** est activée. Gestionnaire de ressources est le modèle de déploiement recommandé pour les nouvelles machines virtuelles. Cliquez sur **créer**.

    ![Créer machine virtuelle SQL avec le Gestionnaire de ressources](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurer la machine virtuelle
Il existe cinq cartes pour configurer une machine virtuelle SQL Server.

| Étape               | Description                          |
|---------------------|-------------------------------|
| **Concepts de base**              | [Configurer les paramètres de base](#1-configure-basic-settings)      |
| **Taille**                | [Choisir la taille de la machine virtuelle](#2-choose-virtual-machine-size)   |
| **Paramètres**            | [Configurer les fonctionnalités facultatives](#3-configure-optional-features)   |
| **Paramètres de SQL Server** | [Configurer les paramètres de SQL server](#4-configure-sql-server-settings) |
| **Résumé**             | [Consultez le résumé](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. configurer les paramètres de base
Sur la carte **Concepts de base** , fournissent les informations suivantes :

* Entrez une machine virtuelle unique **nom**.
* Spécifiez un **nom d’utilisateur** pour le compte d’administrateur local sur l’ordinateur virtuel. Ce compte est également ajouté au rôle serveur fixe **sysadmin** SQL Server.
* Fournir un mot de **passe**.
* Si vous avez plusieurs abonnements, vérifiez que l’abonnement est correct pour la nouvelle machine virtuelle.
* Dans la zone de **groupe de ressources** , tapez un nom pour un nouveau groupe de ressources. Par ailleurs, pour une ressource existante groupe cliquez sur **Sélectionnez existante**. Un groupe de ressources est un ensemble de ressources liées dans Azure (machines virtuelles, comptes de stockage, réseaux virtuels, etc.).

    >[AZURE.NOTE] À l’aide d’un nouveau groupe de ressources est utile si vous êtes simplement test ou en savoir plus sur les déploiements SQL Server dans Azure. Une fois que vous avez terminé avec votre test, supprimez le groupe de ressources pour supprimer automatiquement la machine virtuelle et toutes les ressources associées à ce groupe de ressources. Pour plus d’informations sur les groupes de ressources, voir [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

* Sélectionnez un **emplacement** pour ce déploiement.
* Cliquez sur **OK** pour enregistrer les paramètres.

    ![Carte de concepts de base SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Cliquez sur taille de la machine virtuelle
La **taille de** l’étape, choisissez une taille de machine virtuelle dans la carte de **Choisir une taille** . La carte s’affiche en premier tailles machine recommandés basés sur le modèle que vous avez sélectionné. Il estime également le coût mensuel pour exécuter la machine virtuelle.

![Options de taille de la machine virtuelle SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Pour les charges de travail, nous vous recommandons de sélectionner une taille de machine virtuelle qui prend en charge le [Stockage Premium](../storage/storage-premium-storage.md). Si vous n’avez pas besoin de ce niveau de performances, utilisez le bouton **Afficher tout** , qui affiche toutes les options de taille de la machine. Par exemple, vous pouvez utiliser une plus petite taille de l’ordinateur pour un environnement de test ou de développement.

>[AZURE.NOTE] Pour plus d’informations sur machine virtuelle tailles voir [tailles pour les machines virtuelles](virtual-machines-windows-sizes.md). Pour les considérations sur la taille de la machine virtuelle SQL Server, voir [les procédures recommandées pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

Sélectionnez la taille de votre ordinateur, puis cliquez sur **Sélectionner**.

## <a name="3-configure-optional-features"></a>3. configurer les fonctionnalités facultatives
Sur la carte de **paramètres** , configurez stockage Azure, mise en réseau et de surveillance pour la machine virtuelle.

- Sous **stockage**, spécifiez un **disque tapez** de Standard ou Premium (SSD). Stockage Premium est recommandé pour les charges de travail.

>[AZURE.NOTE] Si vous sélectionnez une taille de l’ordinateur qui ne prend pas en charge le stockage Premium Premium (SSD), la taille de votre ordinateur change automatiquement.  

- Sous **compte de stockage**, vous pouvez accepter le nom de compte de stockage généré automatiquement. Vous pouvez également cliquer sur le **compte de stockage** pour choisir un compte existant et configurer le type de compte de stockage. Par défaut, Azure crée un nouveau compte de stockage avec stockage localement redondants. Pour plus d’informations sur les options de stockage, voir [la réplication du stockage Azure](../storage/storage-redundancy.md).

- Sous **réseau**, vous pouvez accepter les valeurs automatiquement remplis. Vous pouvez également cliquer sur chaque fonctionnalité pour configurer manuellement le **réseau virtuel**, **sous-réseau**, **adresse IP publique**et **Groupe de sécurité réseau**. Aux fins de ce didacticiel, conservez les valeurs par défaut.

- Azure active **surveillance** par défaut avec le même compte de stockage désigné pour la machine virtuelle. Vous pouvez modifier ces paramètres ici.

- Sous **définie disponibilité**, spécifiez un jeu de disponibilité. Aux fins de ce didacticiel, vous pouvez sélectionner **Aucune**. Si vous envisagez de définir des groupes de disponibilité AlwaysOn SQL, configurez la disponibilité pour éviter de recréer la machine virtuelle.  Pour plus d’informations, voir [Gérer la disponibilité des ordinateurs virtuels](virtual-machines-windows-manage-availability.md).

Lorsque vous avez terminé la configuration de ces paramètres, cliquez sur **OK**.

## <a name="4-configure-sql-server-settings"></a>4. configurer les paramètres de SQL server
Sur la carte de **paramètres de SQL Server** , configurer des paramètres spécifiques et optimisations pour SQL Server. Voici les paramètres que vous pouvez configurer pour SQL Server.

| Paramètre               |
|---------------------|
| [Connectivité](#connectivity)              |
| [Authentification](#authentication)                |
| [Configuration de stockage](#storage-configuration)            |
| [Automatisé correction](#automated-patching) |
| [Effectuer des sauvegardes automatiques](#automated-backup)             |
| [Intégration de l’archivage sécurisé clés Azure](#azure-key-vault-integration)             |
| [Services R](#r-services) |

### <a name="connectivity"></a>Connectivité
Sous **connectivité SQL**, spécifiez le type d’accès que vous souhaitez l’instance SQL Server sur cet ordinateur virtuel. Aux fins de ce didacticiel, sélectionnez **Public (internet)** pour autoriser les connexions à SQL Server à partir d’ordinateurs ou services sur internet. Lorsque cette option est sélectionnée, Azure configure automatiquement le pare-feu et le groupe de sécurité de réseau pour autoriser le trafic sur le port 1433.  

![Options de connectivité SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Pour vous connecter à SQL Server via internet, vous devez également activer l’authentification SQL Server, qui est décrit dans la section suivante.

>[AZURE.NOTE] Il est possible d’ajouter des restrictions supplémentaires pour les communications réseau à votre machine virtuelle de SQL Server. Vous pouvez le faire en modifiant le groupe de sécurité réseau après la création de la machine virtuelle. Pour plus d’informations, voir [qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)

Si vous préférez ne pas activer les connexions au moteur de base de données via internet, choisissez une des options suivantes :

- **Local (à l’intérieur de la machine virtuelle uniquement)** pour autoriser les connexions à SQL Server uniquement à partir de la machine virtuelle.
- **Privé (au sein du réseau virtuel)** pour autoriser les connexions à SQL Server à partir d’ordinateurs ou services dans le même réseau virtuel.

>[AZURE.NOTE] L’image machines virtuelles SQL Server Express Edition n’active pas automatiquement le protocole TCP/IP. C’est vrai même pour les options de connectivité publiques et privées. Pour l’édition Express, vous devez utiliser le Gestionnaire de Configuration SQL Server pour [activer manuellement le protocole TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) après la création de la machine virtuelle.

En règle générale, accroître la sécurité en choisissant la connectivité plus restrictive qui permet à votre scénario. Mais toutes les options sont sécurisables via les règles de groupe de sécurité réseau et SQL / l’authentification Windows.

**Port** par défaut 1433. Vous pouvez spécifier un autre numéro de port.
Pour plus d’informations, voir se connecter à une Machine virtuelle SQL Server (Gestionnaire de ressources) [| Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Authentification
Si vous avez besoin de l’authentification SQL Server, cliquez sur **Activer** sous **authentification SQL**.

![Authentification SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Si vous envisagez d’accéder à SQL Server via internet (c'est-à-dire que l’option de connectivité Public), vous devez activer l’authentification SQL ici. Accès du public à SQL Server nécessite l’utilisation de l’authentification SQL.

Si vous activez l’authentification SQL Server, spécifiez un **nom d’utilisateur** et **mot de passe**. Ce nom d’utilisateur est configuré comme une connexion de l’authentification SQL Server et un membre du rôle serveur fixé **sysadmin** . Pour plus d’informations sur les Modes d’authentification, voir [Choisir un Mode d’authentification](http://msdn.microsoft.com/library/ms144284.aspx) .

Si vous n’activez pas l’authentification SQL Server, vous pouvez utiliser le compte d’administrateur local sur l’ordinateur virtuel pour vous connecter à l’instance SQL Server.

### <a name="storage-configuration"></a>Configuration de stockage
Cliquez sur la **configuration de stockage** pour spécifier les besoins de stockage.

![Configuration du stockage SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Si vous sélectionnez stockage Standard, cette option n’est pas disponible. Optimisation du stockage automatique est disponible uniquement pour le stockage Premium.

Vous pouvez spécifier des exigences comme entrées/sorties par seconde (sorties par), débit en Mo/s et la taille de l’espace de stockage total. Configurez ces valeurs en utilisant les échelles coulissante. Le portail calcule automatiquement le nombre de disques en fonction de ces exigences.

Par défaut, Azure optimise le stockage pour 5000 sorties par 200 Mo et 1 To d’espace de stockage. Vous pouvez modifier ces paramètres de stockage en fonction de la charge de travail. Sous **stockage optimisé pour**, sélectionnez une des options suivantes :

- **Général** est le paramètre par défaut et prend en charge la plupart des charges de travail.
- Traitement **transactionnel** optimise le stockage de charges de travail OLTP classique de la base de données.
- **Data Warehouse** optimise le stockage de charges de travail d’analyse et de création de rapports.

>[AZURE.NOTE] Les limites supérieures sur les curseurs varient selon la taille de votre ordinateur virtuel sélectionné.

### <a name="automated-patching"></a>Automatisé correction
**Automatisé correction** est activée par défaut. Correction automatique permet d’Azure à corriger automatiquement SQL Server et le système d’exploitation. Spécifiez un jour de la semaine, l’heure et la durée d’une période de maintenance. Azure effectue correction dans cette fenêtre de maintenance. La planification de la fenêtre maintenance utilise les paramètres régionaux machine virtuelle pour le moment. Si vous ne souhaitez pas Azure à corriger automatiquement SQL Server et le système d’exploitation, cliquez sur **désactiver**.  

![SQL automatisé correction](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Pour plus d’informations, voir [Automatisé d’appliquer un correctif pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Effectuer des sauvegardes automatiques
Activer les sauvegardes automatiques de la base de données pour toutes les bases de données sous **des sauvegardes automatiques**. Effectuer des sauvegardes automatiques sont désactivée par défaut.

Lorsque vous activez effectue des sauvegardes automatisées SQL, vous pouvez configurer les éléments suivants :

- Période de rétention (jours) des sauvegardes
- Compte de stockage à utiliser pour effectuer des sauvegardes régulières
- Option de chiffrement et mot de passe des sauvegardes

Pour chiffrer la sauvegarde, cliquez sur **Activer**. Puis spécifiez le **mot de passe**. Azure crée un certificat pour chiffrer les sauvegardes et utilise le mot de passe pour protéger ce certificat.

![SQL des sauvegardes automatiques](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Pour plus d’informations, voir [Sauvegarde automatique pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Intégration de l’archivage sécurisé clé Azure
Pour stocker des secrets de sécurité dans Azure pour le chiffrement, cliquez sur **l’intégration de l’archivage sécurisé clés Azure** et cliquez sur **Activer**.

![Intégration de l’archivage sécurisé clés Azure SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

Le tableau suivant répertorie les paramètres requis pour configurer l’intégration de l’archivage sécurisé Azure clé.

|PARAMÈTRE|DESCRIPTION|EXEMPLE|
|----------|----------|-------|
|**URL de l’archivage sécurisé clés** |L’emplacement de l’archivage sécurisé clé.|https://contosokeyvault.Vault.Azure.NET/ |
|**Nom principal** |Azure Active Directory nom principal de service. Ce nom est également appelé ID de Client.  |5-4e11-af04eb07b669ccf2 fde2b411 - 33d|
| **Code secret principal**|Azure Active Directory service principal secret. Ce mot de passe est également appelé le code Secret Client. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Nom d’identification**|**Nom d’identification**: intégration AKV crée une information d’identification dans SQL Server, ce qui permet de la machine virtuelle à accéder à l’archivage sécurisé clé. Choisissez un nom pour cette information d’identification.| mycred1|

Pour plus d’informations, voir [Configurer l’intégration Azure clé pour SQL Server sur machines virtuelles Azure l’archivage sécurisé](virtual-machines-windows-ps-sql-keyvault.md).

Lorsque vous avez terminé de configurer les paramètres de SQL Server, cliquez sur **OK**.

### <a name="r-services"></a>Services R
Pour SQL Server 2016 Enterprise edition, vous pouvez activer les [Services de SQL Server R](https://msdn.microsoft.com/library/mt604845.aspx). Cela vous permet d’utiliser analytique avancée avec SQL Server 2016. Cliquez sur **Activer** dans la carte de **Paramètres de SQL Server** .

![Activer les Services de SQL Server R](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Pour les images de SQL Server qui ne sont pas 2016 Enterprise edition, l’option pour activer les Services R est désactivée.

## <a name="5-review-the-summary"></a>5. consultez le résumé
Sur la carte de **synthèse** , consultez le résumé, cliquez sur **OK** pour créer SQL Server, groupe de ressources et des ressources spécifiées pour cet ordinateur virtuel.

Vous pouvez surveiller le déploiement à partir du portail azure. Le bouton de **Notifications** dans la partie supérieure de l’écran affiche l’état de base du déploiement.

>[AZURE.NOTE] Pour vous fournir une idée sur les temps de déploiement, j’ai déployé un SQL VM à la région US Extrême-Orient avec les paramètres par défaut. Ce déploiement de test a eu un total de 26 minutes pour terminer. Mais vous pouvez rencontrer plus rapide ou le temps de déploiement plus lent en fonction de votre région et les paramètres sélectionnés.

## <a name="open-the-vm-with-remote-desktop"></a>Ouvrez la machine virtuelle avec Bureau à distance

Procédez comme suit pour vous connecter à la machine virtuelle avec Bureau à distance :

1. Une fois que la machine virtuelle Azure intégrée, l’icône de la machine virtuelle apparaît dans votre tableau de bord Azure. Vous pouvez également trouver en parcourant vos ordinateurs virtuels existants. Cliquez sur votre nouvelle machine virtuelle SQL. Une carte **machine virtuelle** affiche les détails de votre ordinateur virtuel.
1. En haut de la cuillère **machine virtuelle** , cliquez sur **se connecter**.
1. Le navigateur télécharge un fichier RDP pour la machine virtuelle. Ouvrez le fichier RDP.
    ![Bureau à distance à la machine virtuelle SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. La connexion Bureau à distance vous avertit que l’éditeur de cette connexion à distance ne peut pas être identifié. Cliquez sur **se connecter** pour continuer.
1. Dans la boîte de dialogue **Sécurité de Windows** , cliquez sur **utiliser un autre compte**.
1. Pour type de **nom d’utilisateur** ** \<nom d’utilisateur >**, où <user name> est le nom d’utilisateur que vous avez spécifié lorsque vous avez configuré la machine virtuelle. Vous devez ajouter une barre oblique inverse initiale avant le nom.
1. Entrez le **mot de passe** que vous avez configuré précédemment pour cet ordinateur virtuel, puis cliquez sur **OK** pour vous connecter.
1. Si une autre boîte de dialogue **Connexion Bureau à distance** vous demande si vous voulez vous connecter, cliquez sur **Oui**.

Après que vous être connecté à la machine virtuelle SQL Server, vous pouvez lancer SQL Server Management Studio et vous connecter avec l’authentification Windows à l’aide de vos informations d’identification d’administrateur local. Si vous avez activé l’authentification SQL Server, vous pouvez également vous connecter avec l’authentification SQL à l’aide de la connexion SQL et le mot de passe que vous avez configuré lors de la configuration.

Accéder à l’ordinateur vous permet de modifier directement machine et paramètres de SQL Server selon vos besoins. Par exemple, peut configurer les paramètres du pare-feu ou modifier les paramètres de configuration SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Se connecter à SQL Server à distance

Dans ce didacticiel, nous avons choisi accès **Public** pour la machine virtuelle et **L’authentification SQL Server**. Ces paramètres configurés automatiquement la machine virtuelle pour autoriser les connexions de SQL Server à partir de n’importe quel client via internet (en supposant qu’ils ont la connexion SQL correcte).

>[AZURE.NOTE] Si vous n’avez pas coché Public lors de la configuration, des étapes supplémentaires sont requises pour accéder à votre instance de SQL Server via internet. Pour plus d’informations, voir [se connecter à une Machine virtuelle de SQL Server](virtual-machines-windows-sql-connect.md).

Les sections suivantes montrent comment se connecter à votre instance de SQL Server sur votre ordinateur virtuel à partir d’un autre ordinateur via internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Étapes suivantes
D’autres informations sur l’utilisation de SQL Server dans Azure, voir [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md) et les [Questions fréquemment posées](virtual-machines-windows-sql-server-iaas-faq.md).

Pour obtenir une vidéo de présentation de SQL Server Azure machines virtuelles en fonctionnement, regardez [Azure machine virtuelle est la meilleure plate-forme pour SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorer les rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure.
