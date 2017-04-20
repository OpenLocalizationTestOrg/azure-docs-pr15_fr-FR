<properties
    pageTitle="Configurer toujours dans le groupe de disponibilité dans Azure machine virtuelle automatiquement - Gestionnaire de ressources"
    description="Créer un groupe de disponibilité toujours sur avec Azure machines virtuelles en mode Azure le Gestionnaire de ressources. Ce didacticiel utilise principalement l’interface utilisateur pour créer automatiquement la solution entière."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurer toujours dans le groupe de disponibilité dans Azure machine virtuelle automatiquement - Gestionnaire de ressources

> [AZURE.SELECTOR]
- [Gestionnaire de ressources : modèle](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gestionnaire de ressources : manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classique : interface utilisateur](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classique : PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Ce didacticiel de bout en bout pour vous montre comment créer un groupe de disponibilité de SQL Server avec des machines virtuelles Azure le Gestionnaire de ressources. Le didacticiel utilise Azure cartes pour configurer un modèle. Vous passez en revue les paramètres par défaut, tapez paramètres requis et mettre à jour les cartes de dans le portail lorsque vous passez en revue ce didacticiel.

À la fin du didacticiel, votre solution de groupe de disponibilité de SQL Server dans Azure se compose des éléments suivants :

- Un réseau virtuel contenant plusieurs sous réseaux, y compris un serveur frontal et un sous-réseau principale

- Contrôleur de domaine deux avec un domaine Active Directory (AD)

- Machines virtuelles déployé au sous-réseau principale et joint au domaine AD de deux SQL Server

- Un cluster WSFC 3-nœuds avec le modèle de quorum nœud majorité

- Un groupe de disponibilité avec deux réplicas validation synchrone d’une base de données de disponibilité

L’illustration suivante est une représentation graphique de la solution.

![Architecture de laboratoire de test pour AG dans Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Toutes les ressources dans cette solution appartiennent à un groupe de ressources unique.

Ce didacticiel suppose que les éléments suivants :

- Vous avez déjà un compte Azure. Si vous n’en avez, [s’inscrire à un compte d’évaluation](http://azure.microsoft.com/pricing/free-trial/).

- Vous connaissez déjà la mise en service une machine virtuelle de SQL Server dans la galerie de machine virtuelle à l’aide de l’interface utilisateur. Pour plus d’informations, voir [une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Vous avez déjà une bonne compréhension des groupes de disponibilité. Pour plus d’informations, voir [groupes toujours sur disponibilité (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si vous êtes intéressé par l’utilisation de groupes de disponibilité avec SharePoint, voir également [configurer SQL Server 2012 toujours sur les groupes de disponibilité pour SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

Dans ce didacticiel, vous allez utiliser le portail Azure pour :

- Sélectionnez le modèle de toujours sur à partir du portail

- Passez en revue les paramètres du modèle et mettre à jour de certains paramètres de configuration pour votre environnement

- Azure moniteur tel qu’il crée l’ensemble de l’environnement

- Se connecter à un contrôleur de domaine, puis à un des serveurs SQL

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Mise en service le cluster à partir de la galerie

Azure fournit une image de la galerie pour l’ensemble de la solution. Pour localiser le modèle :

1.  Connectez-vous au portail Azure à l’aide de votre compte.
1.  Dans le portail Azure, cliquez sur **+ nouveau.** Le portail s’ouvre la nouvelle carte.
1.  Sur la nouvelle carte rechercher **AlwaysOn**.
![Trouver le modèle AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Dans les résultats de recherche recherchez **AlwaysOn Cluster de SQL Server**.
![Modèle AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  Sous **Sélectionnez un modèle de déploiement** choisissez **Gestionnaire de ressources**.

### <a name="basics"></a>Concepts de base

Cliquez sur les **Concepts de base** et configurer les éléments suivants :

- **Nom d’utilisateur administrateur** est un compte d’utilisateur avec des autorisations d’administrateur de domaine et un membre du rôle serveur fixe sysadmin SQL Server sur les deux instances de SQL Server. Pour ce didacticiel utilisez **DomainAdmin**.

- **Mot de passe** est le mot de passe pour le compte d’administrateur de domaine. Utiliser un mot de passe complexe. Confirmez le mot de passe.

- **Abonnement** est l’abonnement Azure êtes facturé pour exécuter l’ensemble des ressources déployés pour le groupe de disponibilité. Vous pouvez spécifier un autre abonnement si votre compte comporte plusieurs abonnements.

- **Groupe de ressources** est le nom pour le groupe que toutes les ressources Azure créé par ce didacticiel appartient. Pour ce didacticiel utiliser **SQL-HA-routage**. Pour plus d’informations, voir (vue d’ensemble du Gestionnaire de ressources Azure) [ressource-groupe-overview.md / #-groupes de ressources].

- **Emplacement** est la région Azure où les ressources de ce didacticiel seront créés. Sélectionnez une région Azure pour héberger l’infrastructure.

Voici l’aspect de la carte de **Concepts de base** :

![Concepts de base](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Cliquez sur **OK**.

### <a name="domain-and-network-settings"></a>Paramètres de domaine et de réseau

Ce modèle galerie Azure crée un nouveau domaine avec nouveau domaine. Il crée également un nouveau réseau et deux sous-réseaux. Le modèle ne permet pas de création des serveurs dans un domaine existant ou un réseau virtuel. L’étape suivante consiste à configurer les paramètres réseau et de domaine.

Dans **paramètres réseau et de domaine** carte passez en revue les valeurs prédéfinies pour les paramètres de domaine et de réseau :

- **Nom du domaine racine forêt** est le nom de domaine qui sera utilisé pour le domaine AD hébergeant le cluster. Pour le didacticiel utilisez **contoso.com**.

- **Nom du réseau virtuel** est le nom de réseau pour le réseau virtuel Azure. Pour ce didacticiel utiliser **autohaVNET**.

- **Nom du sous-réseau contrôleur de domaine** est le nom d’une partie du réseau virtuel qui héberge le contrôleur de domaine. Pour ce didacticiel utilisez **sous-réseau-1**. Ce sous-réseau utilisera adresse préfixe **10.0.0.0/24**.

- **Nom du sous-réseau SQL Server** est le nom d’une partie du réseau virtuel que hôtes les serveurs SQL Server et le partage de fichiers rappel. Pour ce didacticiel utilisez **sous-réseau 2**. Ce sous-réseau utilisera adresse préfixe **10.0.1.0/26**.

Pour en savoir plus sur les réseaux virtuels dans [Azure Voir vue d’ensemble de réseau virtuel](../virtual-network/virtual-networks-overview.md).  

Les **paramètres de domaine et de réseau** doit ressembler à ceci :

![Paramètres de domaine et de réseau](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Si nécessaire, vous pouvez modifier ces valeurs. Pour ce didacticiel, nous utilisons les valeurs prédéfinies.

- Passez en revue les paramètres, puis cliquez sur **OK**.

###<a name="availability-group-settings"></a>paramètres de groupe de disponibilité

Passez en revue les valeurs prédéfinies pour le groupe de disponibilité et que l’auditeur sur les **paramètres de groupe de disponibilité** .

- **Nom du groupe de disponibilité** est le nom de la ressource de groupe pour le groupe de disponibilité. Pour ce didacticiel utilisez **Contoso-ag**.

- **nom du groupe de disponibilité récepteur** est utilisé par le cluster et l’équilibrage de charge interne. Clients qui se connectent à SQL Server peuvent utiliser ce nom pour vous connecter au réplica de la base de données approprié. Pour ce didacticiel utilisez **récepteur de Contoso**.

-  **port d’écoute disponibilité groupe** Spécifie que le port TCP que l’auditeur SQL Server utilisera. Pour ce didacticiel, utilisez le port par défaut, **1433**.

Si nécessaire, vous pouvez modifier ces valeurs. Pour ce didacticiel, utilisez les valeurs prédéfinies.  

![paramètres de groupe de disponibilité](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Cliquez sur **OK**.

###<a name="vm-size-storage-settings"></a>Taille de la mémoire virtuelle, les paramètres de stockage

Sous **taille de mémoire virtuelle, les paramètres de stockage** choisir une taille de machine virtuelle SQL Server et passez en revue les autres paramètres.

- **Taille de machine virtuelle SQL Server** est la taille de machine virtuelle Azure pour les deux serveurs SQL. Choisissez une taille de machine virtuelle appropriée pour votre charge de travail. Si vous créez cet environnement pour le didacticiel utiliser **DS2**. Pour les charges de travail choisir une taille de machine virtuelle qui peut prendre en charge la charge de travail. Nombreuses charges de travail de production nécessite **DS4** ou supérieure. Le modèle sera créer deux machines virtuelles de cette taille et installer SQL Server sur chacun d’eux. Pour plus d’informations, voir [formats pour les machines virtuelles](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure va installer Enterprise Edition de SQL Server. Le coût varie selon l’édition et la taille de la machine virtuelle. Pour plus d’informations sur les coûts en cours, consultez [machines virtuelles tarification](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Taille de machine virtuelle contrôleur de domaine** est la taille de la machine virtuelle pour le domaine. Pour ce didacticiel utilisez **D2**.

- **Fichier partager rappel machine virtuelle taille** est la taille de la machine virtuelle pour le rappel de partage de fichier. Pour ce didacticiel utilisez **A1**.

- **Compte de stockage SQL** est le nom du compte de stockage pour stocker les données SQL Server et les disques du système d’exploitation. Pour ce didacticiel utiliser **alwaysonsql01**.

- **Compte de stockage du contrôleur de domaine** est le nom du compte de stockage pour le domaine. Pour ce didacticiel utiliser **alwaysondc01**.

- **Taille du disque de données SQL Server** dans to est la taille du disque de données SQL Server dans to. Spécifier un nombre compris entre 1 et 4. Il s’agit de la taille du disque de données qui sera associé à chaque SQL Server. Pour ce didacticiel utilisez **1**.

- **Optimisation du stockage** définit les paramètres de configuration de stockage spécifiques pour les machines virtuelles SQL Server en fonction du type de charge de travail. Tous les serveurs SQL dans ce scénario utilisent stockage premium avec Azure disque hôte cache en lecture seule. En outre, vous pouvez optimiser les paramètres de SQL Server pour la charge de travail en choisissant l’une de ces trois paramètres :

    - **La charge de travail général** définit sans paramètres de configuration spécifiques

    - **Traitement transactionnel** définit l’indicateur de suivi 1117 et 1118

    - **Data Warehouse** définit l’indicateur de suivi 1117 et 610

Pour ce didacticiel, utilisez **la charge de travail général**.

![Paramètres de taille de stockage mémoire virtuelle](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Passez en revue les paramètres, puis cliquez sur **OK**.

####<a name="a-note-about-storage"></a>Une note sur le stockage

D’autres optimisations dépendent de la taille des disques de données SQL Server. Pour chaque To de disque de données, Azure ajoute un 1 to premium espace de stockage supplémentaire (SSD). Lorsqu’un serveur requiert 2 To ou plus, le modèle crée un pool de stockage sur chaque SQL Server. Un pool de stockage est une forme de virtualisation du stockage dans laquelle plusieurs disques sont configurés pour fournir capacité, performances et résilience plus élevé.  Le modèle crée un espace de stockage du pool de stockage et cela présente sous forme d’un seul de données pour le système d’exploitation. Le modèle désigne ce disque comme disque de données pour SQL Server. Le modèle de règle au pool de stockage pour SQL Server avec les paramètres suivants :

- Taille de répartition est le paramètre Interleaved pour le disque virtuel. Pour les charges de travail transactions cela est définie à 64 Ko. Pour data warehouse charges de travail le paramètre est 256 Ko.

- La résilience est simple (sans résilience).

>[AZURE.NOTE] Stockage Azure premium est localement redondant et conserve trois copies des données dans une région unique, ce qui résilience supplémentaire au pool de stockage n’est pas obligatoire.

- Nombre de colonnes est égal au nombre de disques dans le pool de stockage.

Pour plus d’informations sur le stockage des pools d’espace et de stockage consultez :

- [Vue d’ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx).

- [Sauvegarde Windows Server et Pools de stockage](http://technet.microsoft.com/library/dn390929.aspx)

Pour plus d’informations sur les meilleures pratiques de configuration SQL Server, voir [les procédures recommandées pour SQL Server dans des machines virtuelles Azure](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Paramètres de SQL Server

Sous **paramètres de SQL Server** Vérifiez et modifiez le préfixe du nom machine virtuelle SQL Server, version de SQL Server, compte de service SQL Server et mot de passe et l’automatique SQL correction de planification de la maintenance.

- **Préfixe du nom de SQL Server** est utilisé pour créer un nom pour chaque SQL Server. Pour ce didacticiel utilisez **Contoso-ag**. Les noms de SQL Server seront *Contoso-ag-0* et *Contoso-ag-1*.

- **Version de SQL Server** est la version de SQL Server. Pour ce didacticiel utiliser **SQL Server 2014**. Vous pouvez également choisir **SQL Server 2012** ou **SQL Server 2016**.

- **Nom d’utilisateur SQL Server service compte** est le nom de compte de domaine pour le service SQL Server. Pour ce didacticiel utilisez **sqlservice**.

- **Mot de passe** est le mot de passe du compte de service SQL Server.  Utiliser un mot de passe complexe. Confirmez le mot de passe.

- **Planification de la maintenance SQL automatique correction** identifie le jour de la semaine que Azure corrige automatiquement les serveurs SQL Server. Pour ce didacticiel tapez **dimanche**.

- **SQL automatique correctifs de maintenance heure de début** est l’heure du jour à votre région Azure lors de la correction automatique commence.

>[AZURE.NOTE]La fenêtre de correction pour chaque machine virtuelle est décalée d’une heure. Seule machine virtuelle est appliquée à la fois afin d’éviter toute interruption des services.

![Paramètres de SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Passez en revue les paramètres, puis cliquez sur **OK**.

###<a name="summary"></a>Résumé

Sur la page de résumé Azure vérifie les paramètres. Vous pouvez également télécharger le modèle. Consultez le résumé. Cliquez sur **OK**.

###<a name="buy"></a>Acheter

Cette carte final contient les **conditions d’utilisation**et la **déclaration de confidentialité**. Consultez les informations suivantes. Lorsque vous êtes prêt pour Azure commencer à créer les machines virtuelles et toutes les autres ressources requises pour le groupe de disponibilité, cliquez sur **créer**.

Le portail Azure crée le groupe de ressources et toutes les ressources.

##<a name="monitor-deployment"></a>Déploiement de moniteur

Surveiller l’avancement de déploiement à partir du portail Azure. Une icône représentant le déploiement est automatiquement épinglée au tableau de bord du portail Azure.

![Tableau de bord Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Se connecter à SQL Server

La nouvelle instance de SQL Server s’exécutent sur machines virtuelles qui n’ont pas de connexions à internet. Toutefois, le domaine ont une connexion internet. Pour vous connecter aux serveurs SQL avec Bureau à distance, première RDP à un contrôleur de domaine. À partir du contrôleur de domaine, ouvrez une deuxième RDP à SQL Server.

RDP au contrôleur de domaine principal, procédez comme suit :

1.  À partir d’Azure portail tableau de bord très que le déploiement a réussi.

1.  Cliquez sur **ressources**.

1.  Dans la carte de **ressources** , cliquez sur **ad-principal-dc** qui est le nom de la machine virtuelle du contrôleur de domaine principal.

1.  Dans la carte pour **ad-principal-dc** cliquez sur **se connecter**. Votre navigateur vous demande si vous voulez ouvrir ou enregistrer l’objet de connexion à distance. Cliquez sur **Ouvrir**.
![Se connecter au contrôleur de domaine](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Connexion Bureau à distance** peut vous avertir que l’éditeur de cette connexion à distance ne peut pas être identifié. Cliquez sur **se connecter**.

1.  Sécurité de Windows vous invite à entrer vos informations d’identification pour vous connecter à l’adresse IP du contrôleur de domaine principal. Cliquez sur **utiliser un autre compte**. Nom **d’utilisateur** , tapez **contoso\DomainAdmin**. C’est le compte que vous avez choisi pour nom d’utilisateur administrateur. Utilisez le mot de passe complexe que vous avez choisies lorsque vous avez configuré le modèle.

1.  **Bureau à distance** vous informe que l’ordinateur distant ne peut pas être authentifié en raison de problèmes dans son certificat de sécurité. Il affiche le nom de certificat de sécurité. Si vous avez suivi le didacticiel le nom sera **ad-principal-dc.contoso.com**. Cliquez sur **Oui**.

Vous êtes maintenant connecté au contrôleur de domaine principal. RDP à SQL Server, procédez comme suit :

1.  Sur le contrôleur de domaine, ouvrez **Connexion Bureau à distance**.

1.  Pour **ordinateur**, tapez le nom d’un des serveurs SQL. Pour ce didacticiel, tapez **0 de SQL Server**.

1.  Utilisez le même compte d’utilisateur et mot de passe que vous avez utilisé pour RDP au contrôleur de domaine.

Vous êtes maintenant connecté à RDP à SQL Server. Vous pouvez ouvrez SQL Server management studio, connectez-vous à l’instance par défaut de SQL Server et vérifier que le groupe availabilty est configuré.


