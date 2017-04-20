<properties
    pageTitle="Configurer toujours dans le groupe de disponibilité dans Azure machine virtuelle - classique"
    description="Créer un groupe de disponibilité toujours activé avec des Machines virtuelles Azure. Ce didacticiel utilise principalement l’interface utilisateur et outils plutôt que l’écriture de script."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configurer toujours dans le groupe de disponibilité dans Azure machine virtuelle - classique

> [AZURE.SELECTOR]
- [Gestionnaire de ressources : modèle](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gestionnaire de ressources : manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classique : interface utilisateur](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classique : PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Ce didacticiel de bout en bout pour vous montre comment mettre en œuvre des groupes de disponibilité associés à SQL Server toujours en cours d’exécution sur des machines virtuelles Azure.

À la fin du didacticiel, votre solution SQL Server toujours sur dans Azure se compose des éléments suivants :

- Un réseau virtuel contenant plusieurs sous réseaux, y compris un serveur frontal et un sous-réseau principale

- Un contrôleur de domaine avec un domaine Active Directory (AD)

- Machines virtuelles déployé au sous-réseau principale et joint au domaine AD de deux SQL Server

- Un cluster WSFC 3-nœuds avec le modèle de quorum nœud majorité

- Un groupe de disponibilité avec deux réplicas validation synchrone d’une base de données de disponibilité

L’illustration suivante est une représentation graphique de la solution.

![Architecture de laboratoire de test pour AG dans Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Notez qu’il s’agit d’une des configurations possibles. Par exemple, vous pouvez réduire le nombre de machines virtuelles pour un groupe de disponibilité de deux réplica afin d’enregistrer sur les heures cluster dans Azure en utilisant le contrôleur de domaine comme le rappel de partage de fichier quorum dans un cluster WSFC 2 nœuds. Cette méthode réduit le nombre de machine virtuelle d’un à partir de la configuration ci-dessus.

Ce didacticiel suppose que les éléments suivants :

- Vous avez déjà un compte Azure.

- Vous connaissez déjà la mise en service une machine virtuelle de classique SQL Server dans la galerie de machine virtuelle à l’aide de l’interface utilisateur.

- Vous avez déjà une bonne compréhension des toujours sur les groupes de disponibilité. Pour plus d’informations, voir [Toujours sur les groupes de disponibilité (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si vous êtes intéressé par l’utilisation de toujours sur groupes de disponibilité avec SharePoint, voir également [Configurer SQL Server 2012 toujours activé disponibilité des groupes pour SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Créer le réseau virtuel et le serveur contrôleur de domaine

Vous devez commencer avec un compte d’évaluation Azure. Une fois que vous avez terminé la configuration de votre compte, vous devez être dans l’écran d’accueil du portail classique Azure.

1. Cliquez sur le bouton **Nouveau** dans le coin inférieur gauche de la page, comme illustré ci-dessous.

    ![Cliquez sur Nouveau dans le portail](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. Cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel** , puis sur **Créer personnalisé**, comme illustré ci-dessous.

    ![Créer le réseau virtuel](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. Dans la boîte de dialogue **Créer un réseau virtuel** , créez un nouveau réseau virtuel en parcourant les pages avec les paramètres ci-dessous. 

  	|Page|Paramètres|
|---|---|
|Détails du réseau virtuel|**NOM = ContosoNET**<br/>**RÉGION = fr ouest**|
|Serveurs DNS et connectivité VPN|Aucun|
|Espaces d’adressage réseau virtuel|Paramètres sont affichent dans la capture d’écran ci-dessous : ![Créer le réseau virtuel](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. Ensuite, vous créez la machine virtuelle que vous utiliserez comme contrôleur de domaine (DC). Cliquez sur **Nouveau** , puis **Calculer**, puis **Machine virtuelle**et **De la galerie**, comme illustré ci-dessous.

    ![Créer une machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. Dans la boîte de dialogue **créer un ordinateur de virtuel** , configurer un nouvel ordinateur virtuel en parcourant les pages avec les paramètres ci-dessous. 

  	|Page|Paramètres|
|---|---|
|Sélectionnez le système d’exploitation machine virtuelle|Centre de données Windows Server 2012 R2|
|Configuration de machine virtuelle|**DATE de publication de VERSION** = (dernière version)<br/>**Nom de la MACHINE virtuelle** = ContosoDC<br/>**Niveau** = STANDARD<br/>**Taille** = A2 (2 cœurs)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|
|Configuration de machine virtuelle|**SERVICE CLOUD** = créer un nouveau service cloud<br/>**Nom du CLOUD SERVICE DNS** = un nom de service cloud uniques<br/>**Nom DNS** = un nom unique (ex : ContosoDC123)<br/>**Région/groupe affinité/virtuelle réseau** = ContosoNET<br/>**Sous-réseaux virtuelle** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage générées automatiquement<br/>**Définie de disponibilité** = (aucun)|
|Options de machine virtuelle|Utiliser les valeurs par défaut|

Une fois que vous avez terminé la configuration de la nouvelle machine virtuelle, attendez que la machine virtuelle pour être provsioned. Ce processus prend un certain temps pour terminer, et si vous cliquez sur l’onglet **Machine virtuelle** dans le portail Azure classique, vous pouvez voir États-Unis vélo ContosoDC de **départ (Provisioning)** à **arrêt**, **démarrage**, **en cours d’exécution (Provisioning)**et enfin **en cours d’exécution**.

Le serveur contrôleur de domaine est désormais correctement mis en service. Ensuite, vous allez configurer le domaine Active Directory sur ce serveur contrôleur de domaine.

## <a name="configure-the-domain-controller"></a>Configurer le contrôleur de domaine

Dans la procédure suivante, vous configurez l’ordinateur ContosoDC comme contrôleur de domaine pour corp.contoso.com.

1. Dans le portail, sélectionnez l’ordinateur **ContosoDC** . Sous l’onglet **tableau de bord** , cliquez sur **se connecter** pour ouvrir un fichier RDP pour l’accès Bureau à distance.

    ![Se connecter à l’ordinateur Vritual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. Se connecter avec votre configuré compte d’administrateur (**\AzureAdmin**) et mot de passe (**Contoso ! 000**).

1. Par défaut, le tableau de bord du **Gestionnaire de serveur** doit s’afficher.

1. Cliquez sur le lien **Ajouter des rôles et les fonctionnalités** du tableau de bord.

    ![Explorateur de serveurs ajouter des rôles](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. Cliquez sur **suivant** jusqu'à atteindre la section **Rôles de serveur** .

1. Sélectionnez les rôles de **Services de domaine Active Directory** et **Serveur DNS** . Lorsque vous y êtes invité, ajouter des fonctionnalités supplémentaires requises par ces rôles.

    >[AZURE.NOTE] Vous obtenez une validation n’informer qu’aucune adresse IP statique. Si vous testez la configuration, cliquez sur Continuer. Pour production scénarios [utiliser PowerShell pour définir l’adresse IP de l’ordinateur contrôleur de domaine](./virtual-network/virtual-networks-reserved-private-ip.md).

    ![Boîte de dialogue rôles ajouter](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. Jusqu'à ce que vous atteigniez la section de **Confirmation** , cliquez sur **suivant** . Activez la case à cocher **redémarrer le serveur de destination automatiquement si nécessaire** .

1. Cliquez sur **installer**.

1. Une fois les fonctionnalités de terminer l’installation, revenez au tableau de bord du **Gestionnaire de serveur** .

1. Sélectionnez la nouvelle option **AD DS** dans le volet gauche.

1. Cliquez sur le lien **plus** dans la barre d’avertissement jaune.

    ![Boîte de dialogue AD DS sur machine virtuelle DNS Server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. Dans la colonne **Action** de la boîte de dialogue **Tous les détails de la tâche serveur** , cliquez sur **promouvoir ce serveur en contrôleur de domaine**.

1. Dans l' **Assistant Configuration de Services de domaine Active Directory**, utilisez les valeurs suivantes :

  	|Page|Paramètre|
|---|---|
|Configuration de déploiement|**Ajouter une nouvelle forêt** = sélectionné<br/>**Nom du domaine racine** = corp.contoso.com|
|Options contrôleur de domaine|**Mot de passe** = Contoso ! 000<br/>**Confirmez le mot de passe** = Contoso ! 000|

1. Cliquez sur **suivant** pour accéder à travers les autres pages de l’Assistant. Dans la page **Prerequisites Check** , vérifiez que vous voyez le message suivant : **toutes les conditions préalables passé avec succès**. Notez que vous devez examiner les messages d’avertissement applicables, mais il est possible de poursuivre l’installation.

1. Cliquez sur **installer**. La machine virtuelle **ContosoDC** redémarre automatiquement.

## <a name="configure-domain-accounts"></a>Configurer des comptes de domaine

Étapes suivantes configurer les comptes Active Directory (AD) pour une utilisation ultérieure.

1. Reconnectez-vous à l’ordinateur **ContosoDC** .

1. Dans le **Gestionnaire de serveur** sélectionnez **Outils** , puis sur **Centre d’administration Active Directory**.

    ![Centre d’administration d’Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. Dans le **Centre d’administration Active Directory** select **corp (local)** dans le volet gauche.

1. Dans le volet de **tâches** droit, sélectionnez **Nouveau** , puis cliquez sur **l’utilisateur**. Utilisez les paramètres suivants :

  	|Paramètre|Valeur|
|---|---|
|**Prénom**|Installer|
|**Utilisateur SamAccountName**|Installer|
|**Mot de passe**|Contoso ! 000|
|**Confirmez le mot de passe**|Contoso ! 000|
|**Autres options de mot de passe**|Sélectionnée|
|**Mot de passe n’expire jamais**|Activée|

1. Cliquez sur **OK** pour créer l’utilisateur **installer** . Ce compte servira à configurer le cluster de basculement et le groupe de disponibilité.

1. Créez deux autres utilisateurs avec les mêmes étapes : **CORP\SQLSvc1** et **CORP\SQLSvc2**. Ces comptes seront utilisées pour les instances de SQL Server. Ensuite, vous devez donner **CORP\Install** les autorisations nécessaires pour la configuration de Windows Service basculement cluster (WSFC).

1. Dans le **Centre d’administration Active Directory**, sélectionnez **corp (local)** dans le volet gauche. Puis, dans le volet de **tâches** droit, cliquez sur **Propriétés**.

    ![Propriétés de l’utilisateur CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. Sélectionnez les **Extensions de**, puis cliquez sur le bouton **Avancé** , sous l’onglet **sécurité** .

1. Dans la boîte de dialogue **Paramètres de sécurité avancés pour corp** . Cliquez sur **Ajouter**.

1. Cliquez sur **Sélectionner un principal**. Recherchez ensuite **CORP\Install**. Cliquez sur **OK**.

1. Sélectionnez les autorisations **lire toutes les propriétés** et des **objets de créer un ordinateur** .

    ![Autorisations des utilisateurs corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. Cliquez sur **OK**, puis cliquez sur **OK** à nouveau. Fermez la fenêtre de propriétés corp.

À présent que vous avez terminé la configuration d’Active Directory et les objets utilisateur, vous créez trois SQL Server ordinateurs virtuels et les ajouter dans ce domaine.

## <a name="create-the-sql-server-vms"></a>Créer des ordinateurs virtuels SQL Server

Ensuite, créez trois machines virtuelles, y compris un nœud de cluster WSFC et machines virtuelles de deux SQL Server. Pour créer des ordinateurs virtuels, revenez au portail Azure classique, cliquez sur **Nouveau**, **Calculer**, **Machine virtuelle**, puis **De la galerie**. Puis utiliser les modèles dans le tableau ci-dessous pour vous aider à créer les ordinateurs virtuels.

|Page|VM1|ORDINATEUR VIRTUEL 2|VM3|
|---|---|---|---|
|Sélectionnez le système d’exploitation machine virtuelle|**Centre de données Windows Server 2012 R2**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuration de machine virtuelle|**DATE de publication de VERSION** = (dernière version)<br/>**Nom de la MACHINE virtuelle** = ContosoWSFCNode<br/>**Niveau** = STANDARD<br/>**Taille** = A2 (2 cœurs)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|**DATE de publication de VERSION** = (dernière version)<br/>**Nom de la MACHINE virtuelle** = ContosoSQL1<br/>**Niveau** = STANDARD<br/>**Taille** = A3 (4 cœurs)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|**DATE de publication de VERSION** = (dernière version)<br/>**Nom de la MACHINE virtuelle** = ContosoSQL2<br/>**Niveau** = STANDARD<br/>**Taille** = A3 (4 cœurs)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|
|Configuration de machine virtuelle|**SERVICE CLOUD** = nom DNS du Service Cloud Unique créé précédemment (ex : ContosoDC123)<br/>**Région/groupe affinité/virtuelle réseau** = ContosoNET<br/>**Sous-réseaux virtuelle** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage générées automatiquement<br/>**Disponibilité définir** = créer une disponibilité définir<br/>**Nom du jeu de disponibilité** = SQLHADR|**SERVICE CLOUD** = nom DNS du Service Cloud Unique créé précédemment (ex : ContosoDC123)<br/>**Région/groupe affinité/virtuelle réseau** = ContosoNET<br/>**Sous-réseaux virtuelle** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage générées automatiquement<br/>**Définir disponibilité** = SQLHADR (vous pouvez également configurer la disponibilité définie une fois que l’ordinateur a été créé. Les trois ordinateurs doivent être affectées à l’ensemble de disponibilité SQLHADR.)|**SERVICE CLOUD** = nom DNS du Service Cloud Unique créé précédemment (ex : ContosoDC123)<br/>**Région/groupe affinité/virtuelle réseau** = ContosoNET<br/>**Sous-réseaux virtuelle** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage générées automatiquement<br/>**Définir disponibilité** = SQLHADR (vous pouvez également configurer la disponibilité définie une fois que l’ordinateur a été créé. Les trois ordinateurs doivent être affectées à l’ensemble de disponibilité SQLHADR.)|
|Options de machine virtuelle|Utiliser les valeurs par défaut|Utiliser les valeurs par défaut|Utiliser les valeurs par défaut|

<br/>

>[AZURE.NOTE] La configuration précédente suggère hiérarchisation STANDARD de machines virtuelles, étant donné que les ordinateurs couche base ne prennent pas en charge les points de terminaison équilibrage de charge requis pour créer plus tard une écoute de groupe de disponibilité. En outre, les tailles machine suggérés ici sont destinées aux groupes de disponibilité d’évaluation dans Azure machines virtuelles. Pour optimiser les performances de charges de travail, consultez les recommandations pour la configuration dans [les procédures recommandées pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-performance.md)et tailles de machine SQL Server.

Une fois que les ordinateurs trois virtuels sont entièrement mis en service, vous devez les intégrer au domaine **corp.contoso.com** accorder CORP\Install les droits d’administrateur pour les ordinateurs. Pour ce faire, procédez comme suit pour chacune des trois ordinateurs virtuels.

1. Tout d’abord, modifiez l’adresse du serveur DNS préféré. Commencez par le téléchargement de fichiers de (RDP) de bureau à distance de chaque machine virtuelle vers votre répertoire local en sélectionnant la machine virtuelle dans la liste et en cliquant sur le bouton **se connecter** . Pour sélectionner une machine virtuelle, cliquez sur n’importe où, mais la première cellule de la ligne, comme illustré ci-dessous.

    ![Télécharger le fichier RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. Démarrez le fichier RDP que vous avez téléchargée, puis connectez-vous à la machine virtuelle à l’aide de vos domaines compte d’administrateur (**BUILTIN\AzureAdmin**) et mot de passe (**Contoso ! 000**).

1. Une fois que vous êtes connecté, vous devriez voir le tableau de bord du **Gestionnaire de serveur** . Dans le volet gauche, cliquez sur **Serveur Local** .

1. Sélectionnez le lien **adresse IPv4 affectée par DHCP, IPv6 est activé** .

1. Dans la fenêtre **Connexions réseau** , sélectionnez l’icône réseau.

    ![Modification du serveur DNS préféré machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. Dans la barre de commandes, cliquez sur **Modifier les paramètres de cette connexion** (selon la taille de votre fenêtre, vous devrez peut-être cliquer sur la double flèche droite pour afficher cette commande).

1. Sélectionnez **protocole Internet Version 4 (TCP/IPv4)** , puis cliquez sur Propriétés.

1. Sélectionnez utiliser le serveur DNS suivant adresses et spécifiez **10.10.2.4** dans **serveur DNS préféré**.

1. L' adresse **10.10.2.4** est l’adresse affectée à une machine virtuelle dans le sous-réseau 10.10.2.0/24 dans un réseau virtuel Azure, et cette dernière est **ContosoDC**. Pour vérifier l’adresse IP **ContosoDC**de, utilisez la **nslookup contosodc** dans l’invite de commandes, comme indiqué ci-dessous.

    ![Utilisez NSLOOKUP pour trouver l’adresse IP de contrôleur de domaine](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. Cliquez sur O**K** , puis sur **Fermer** pour valider les modifications. Vous pouvez désormais joindre la machine virtuelle à **corp.contoso.com**.

1. Dans la fenêtre **Serveur Local** , cliquez sur le lien de **groupe de travail** .

1. Dans la section **Nom de l’ordinateur** , cliquez sur **Modifier**.

1. Activez la case à cocher de **domaine** et tapez **corp.contoso.com** dans la zone de texte. Cliquez sur **OK**.

1. Dans la boîte de dialogue menu contextuel de **La sécurité de Windows** , spécifiez les informations d’identification pour le compte d’administrateur domaine par défaut (**CORP\AzureAdmin**) et le mot de passe (**Contoso ! 000**).

1. Lorsque vous voyez le message « Bienvenue dans le domaine corp.contoso.com », cliquez sur **OK**.

1. Cliquez sur **Fermer**, puis cliquez sur **Redémarrer maintenant** dans la boîte de dialogue menu contextuel.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Ajoutez l’utilisateur Corp\Install en tant qu’administrateur sur chaque ordinateur virtuel :

1. Patientez jusqu’au redémarrage de la machine virtuelle, puis lancez le fichier RDP à nouveau pour vous connecter à la machine virtuelle en utilisant le compte **BUILTIN\AzureAdmin** .

1. Dans le **Gestionnaire de serveur** sélectionnez **Outils**, puis cliquez sur **Gestion de l’ordinateur**.

    ![Gestion de l’ordinateur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. Dans la fenêtre de **Gestion de l’ordinateur** , développez **utilisateurs et groupes locaux**, puis sur **groupes**.

1. Double-cliquez sur le groupe **administrateurs** .

1. Dans la boîte de dialogue **Propriétés d’administrateurs** , cliquez sur le bouton **Ajouter** .

1. Entrez l’utilisateur **CORP\Install**, puis cliquez sur **OK**. Lorsque vous êtes invité vos informations d’identification, utilisez le compte **AzureAdmin** avec la **Contoso ! 000** mot de passe.

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de l’administrateur** .

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Ajouter la fonctionnalité de **Basculement** à chaque machine virtuelle.

1. Dans le tableau de bord du **Gestionnaire de serveur** , cliquez sur **Ajouter des rôles et les fonctionnalités**.

1. Dans l' **Assistant de fonctionnalités et ajouter des rôles**, cliquez sur **suivant** jusqu'à atteindre la page **fonctionnalités** .

1. Sélectionnez **basculement**. Lorsque vous y êtes invité, ajouter d’autres fonctions dépendantes.

    ![Ajouter le basculement de cluster fonctionnalité de machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. Cliquez sur **suivant**, puis cliquez sur **installer** dans la page de **Confirmation** .

1. Installation de la fonctionnalité **Basculement** est terminée, cliquez sur **Fermer**.

1. Se déconnecter de l’ordinateur virtuel.

1. Répétez les étapes décrites dans cette section pour tous les trois serveurs-- **ContosoWSFCNode**, **ContosoSQL1**et **ContosoSQL2**.

Ordinateurs SQL Server virtuels sont désormais configurés et en cours d’exécution, mais ils sont installés avec SQL Server avec les options par défaut.

## <a name="create-the-wsfc-cluster"></a>Créer le Cluster WSFC

Dans cette section, vous créez le cluster WSFC hébergeant le groupe de disponibilité que vous créerez ultérieurement. À présent, vous devez terminé suivantes pour chacune des trois ordinateurs virtuels vous allez utiliser dans le cluster WSFC :

- Entièrement mis en service dans Azure

- Machine virtuelle joint au domaine

- Ajouté **CORP\Install** au groupe Administrateurs local

- Ajouter la fonctionnalité de basculement

Tous les il s’agit des conditions préalables sur chaque ordinateur virtuel avant de le joindre au cluster WSFC.

Notez également que le réseau virtuel Azure ne se comporte pas de la même façon qu’un réseau local. Vous avez besoin créer le cluster dans l’ordre suivant :

1. Créer un cluster à nœud unique sur l’un des nœuds (**ContosoSQL1**).

1. Modifier l’adresse IP cluster à une adresse IP inutilisée (**10.10.2.101**).

1. Mettre le nom de cluster en ligne.

1. Ajoutez les autres nœuds (**ContosoSQL2** et **ContosoWSFCNode**).

Suivez les étapes ci-dessous pour accomplir ces tâches qui configure entièrement le cluster.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en utilisant le compte de domaine **CORP\Install**.

1. Dans le tableau de bord du **Gestionnaire de serveur** , sélectionnez **Outils**, puis cliquez sur **Gestionnaire du Cluster de basculement**.

1. Dans le volet gauche, avec le bouton droit **Gestionnaire du Cluster de basculement**, puis cliquez sur **créer un Cluster**, comme illustré ci-dessous.

    ![Créer le Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. Dans l’Assistant Création d’un Cluster, créez un cluster d’un nœud en parcourant les pages avec les paramètres ci-dessous :

  	|Page|Paramètres|
|---|---|
|Avant de commencer|Utiliser les valeurs par défaut|
|Sélectionner les serveurs|Tapez **ContosoSQL1** dans la zone **nom de serveur d’entrée** , cliquez sur **Ajouter**|
|Avertissement de validation|Sélectionnez **n° j’ai ne nécessitent pas prise en charge de Microsoft pour ce cluster et par conséquent ne souhaitez pas exécuter les tests de validation. Lorsque je clique sur Suivant, continuez à créer le cluster**.|
|Point d’accès pour administrer le Cluster|Type **Cluster1** dans la zone **nom de Cluster**|
|Confirmation|Utilisez les valeurs par défaut, sauf si vous utilisez des espaces de stockage. Voir la note suite de ce tableau.|

    >[AZURE.WARNING] Si vous utilisez des [Espaces de stockage](https://technet.microsoft.com/library/hh831739), ce qui regroupe plusieurs disques dans des pools de stockage, vous devez désactiver la case à cocher **Ajouter tout le stockage éligible pour le cluster** dans la page de **Confirmation** . Si vous ne décochez pas cette option, les disques virtuels seront détachés pendant le processus de mise en cluster. Par conséquent, ils également n’apparaîtront pas dans le Gestionnaire de disque ou de l’Explorateur jusqu'à ce que les espaces de stockage sont supprimés de cluster et reconnectez à l’aide de PowerShell.

1. Dans le volet gauche, développez **Gestionnaire du Cluster de basculement**, puis cliquez sur **Cluster1.corp.contoso.com**.

1. Dans le volet central, faites défiler jusqu'à la section **Cluster Core ressources** et développer le **nom : Clutser1** détails. Vous devez voir le **nom** et les ressources **Adresse IP** à l’état **Échec** . La ressource adresse IP ne peut pas être mises en ligne car le cluster est affecté à la même adresse IP que celle d’un ordinateur, qui est une adresse en double.

1. Avec le bouton droit de la ressource **Adresse IP** a échoué, puis cliquez sur **Propriétés**.

    ![Propriétés du cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. Sélectionnez **l’Adresse IP statique** et spécifiez **10.10.2.101** dans la zone de texte adresse. Ensuite, cliquez sur **OK**.

1. Dans la section **Cluster Core ressources** , avec le bouton droit **nom : Cluster1** et cliquez sur **Mettre en ligne**. Patientez jusqu'à ce que les deux ressources sont en ligne. Lorsque le nom du cluster est en ligne, il met à jour le serveur contrôleur de domaine avec un nouveau compte d’ordinateur AD. Ce compte AD servira à exécuter le service de groupe groupé disponibilité ultérieurement.

1. Enfin, vous ajoutez les nœuds restants pour le cluster. Dans l’arborescence du navigateur, avec le bouton droit **Cluster1.corp.contoso.com** et cliquez sur **Ajouter un nœud**, comme illustré ci-dessous.

    ![Ajouter des nœuds au Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. Dans l' **Assistant Ajout de nœud**, cliquez sur **suivant**. Dans la page **Sélectionner des serveurs** , ajoutez **ContosoSQL2** et **ContosoWSFCNode** à la liste en tapant le nom du serveur dans la zone **nom de serveur d’entrée** , puis sur **Ajouter**. Lorsque vous avez terminé, cliquez sur **suivant**.

1. Dans la page **d’Avertissement de Validation** , cliquez sur **non** (dans un scénario de production vous devez effectuer les tests de validation). Ensuite, cliquez sur **suivant**.

1. Dans la page de **Confirmation** , cliquez sur **suivant** pour ajouter les nœuds.

    >[AZURE.WARNING] Si vous utilisez des [Espaces de stockage](https://technet.microsoft.com/library/hh831739), ce qui regroupe plusieurs disques dans des pools de stockage, vous devez désactiver la case à cocher **Ajouter tout le stockage éligible pour le cluster** . Si vous ne décochez pas cette option, les disques virtuels seront détachés pendant le processus de mise en cluster. Par conséquent, ils également n’apparaîtront pas dans le Gestionnaire de disque ou de l’Explorateur jusqu'à ce que les espaces de stockage sont supprimés de cluster et reconnectez à l’aide de PowerShell.

1. Une fois que les nœuds sont ajoutés au cluster, cliquez sur **Terminer**. Gestionnaire de Cluster basculement affiche désormais que votre cluster possède trois nœuds, énumérez-les dans le conteneur de **nœuds** .

1. Vous déconnectez la session de bureau à distance.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Préparer les Instances de SQL Server pour le groupe de disponibilité

Dans cette section, vous allez effectuer les opérations suivantes sur **ContosoSQL1** et **contosoSQL2**:

- Ajout d’une connexion pour **NT\SYSTÈME** avec un autorisations nécessaires pour l’instance SQL Server par défaut

- Ajouter des **CORP\Install** en tant que sysadmin rôle à l’instance SQL Server par défaut

- Ouvrir le pare-feu pour l’accès à distance de SQL Server

- Activer la fonctionnalité toujours sur les groupes de disponibilité

- Changer le compte de service SQL Server **CORP\SQLSvc1** et **CORP\SQLSvc2**, respectivement

Ces actions peuvent être effectuées dans n’importe quel ordre. Toutefois, les étapes ci-dessous guidera les dans l’ordre. Suivez les étapes pour **ContosoSQL1** et **ContosoSQL2**:

1. Si vous n’êtes pas connecté déconnecter la session de bureau à distance pour la machine virtuelle, faites-le maintenant.

1. Ouvrez les fichiers RDP pour **ContosoSQL1** et **ContosoSQL2** et connectez-vous en tant que **BUILTIN\AzureAdmin**.

1. Tout d’abord, ajoutez **NT\SYSTÈME** pour les connexions SQL Server et avec les autorisations nécessaires. Lancez **SQL Server Management Studio**.

1. Cliquez sur **se connecter** pour vous connecter à l’instance SQL Server par défaut.

1. Dans l' **Explorateur d’objets**, développez **sécurité**, puis puis **connexions**.

1. Avec le bouton droit de la connexion **NT\SYSTÈME** , puis cliquez sur **Propriétés**.

1. Dans la page **éléments sécurisables** , pour le serveur local, sélectionnez **Autoriser** pour les autorisations suivantes, puis cliquez sur **OK**.

    - Modifier un groupe de disponibilité

    - Se connecter SQL

    - État du serveur

1. Ensuite, ajoutez **CORP\Install** en tant que **sysadmin** rôle à l’instance SQL Server par défaut. Dans l' **Explorateur d’objets**, cliquez sur **connexions** , puis cliquez sur **Nouvelle connexion**.

1. Tapez **CORP\Install** dans la zone **nom de connexion**.

1. Dans la page **Rôles de serveur** , sélectionnez **sysadmin**. Ensuite, cliquez sur **OK**. Une fois la connexion est créée, vous pouvez l’afficher en développement des **connexions** dans **l’Explorateur d’objets**.

1. Ensuite, vous créez une règle de pare-feu pour SQL Server. À partir de l’écran de **démarrage** , lancez **Le pare-feu Windows avec fonctions avancées de sécurité**.

1. Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet droit, cliquez sur **Nouvelle règle**.

1. Dans la page **Type de règle** , sélectionnez **le programme**, puis cliquez sur **suivant**.

1. Dans la page du **programme** , sélectionnez **ce chemin d’accès du programme** , puis tapez %ProgramFiles%\Microsoft **Server\MSSQL12 SQL. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** dans la zone de texte (si vous suivez les instructions suivantes, mais l’utilisation de SQL Server 2012, le répertoire SQL Server est **MSSQL11. MSSQLSERVER**). Cliquez sur **suivant**.

1. Dans la page **Action** , conserver **Autoriser la connexion** sélectionnée et cliquez sur **suivant**.

1. Dans la page de **profil** , acceptez les paramètres par défaut, puis cliquez sur **suivant**.

1. Dans la page **nom** , attribuez un nom de règle, telles que **SQL Server (règle de programme)** dans la zone de texte **nom** , puis cliquez sur **Terminer**.

1. Ensuite, vous activez la fonctionnalité **Toujours sur les groupes de disponibilité** . À partir de l’écran de **démarrage** , lancez le **Gestionnaire de Configuration SQL Server**.

1. Dans l’arborescence du navigateur, cliquez sur **Services de SQL Server**, puis cliquez sur le service **SQL Server (MSSQLSERVER)** et cliquez sur **Propriétés**.

1. Cliquez sur l’onglet **Toujours sur haute disponibilité** , puis sélectionnez **Activer toujours sur les groupes de disponibilité**, comme indiqué ci-dessous, puis cliquez sur **Appliquer**. Cliquez sur **OK** dans la boîte de dialogue contextuel et ne fermez pas encore la fenêtre Propriétés. Vous allez redémarrer le service SQL Server après avoir modifié le compte de service.

    ![Toujours activer sur les groupes de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. Ensuite, vous modifiez le compte de service SQL Server. Cliquez sur l’onglet **Connexion** , puis tapez **CORP\SQLSvc1** (pour **ContosoSQL1**) ou **CORP\SQLSvc2** (pour **ContosoSQL2**) dans la zone **Nom du compte**, puis renseignez et confirmez le mot de passe, puis cliquez sur **OK**.

1. Dans la fenêtre contextuelle, cliquez sur **Oui** pour redémarrer le service SQL Server. Une fois que le service SQL Server est redémarré, les modifications apportées dans la fenêtre Propriétés sont efficaces.

1. Déconnectez-vous d’ordinateurs virtuels.

## <a name="create-the-availability-group"></a>Créer le groupe de disponibilité

Vous êtes maintenant prêt à configurer un groupe de disponibilité. Voici un plan de ce que vous ferez :

- Créer une nouvelle base de données (**MyDB1**) sur **ContosoSQL1**

- Effectuer une sauvegarde complète et une sauvegarde de la base de données

- Restaurer la version complète et ouvrez une session sauvegardes **ContosoSQL2** avec l’option **NORECOVERY**

- Créer le groupe de disponibilité (**AG1**) avec réplicas secondaires lisibles validation synchrone et basculement automatique

### <a name="create-the-mydb1-database-on-contososql1"></a>Créer la base de données MyDB1 sur ContosoSQL1 :

1. Si vous n’avez pas encore d’ouvert déconnecter les sessions de bureau à distance pour **ContosoSQL1** et **ContosoSQL2**, faites-le maintenant.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en tant que **CORP\Install**.

1. Dans **L’Explorateur de fichiers**, sous * *C:\**, créez un répertoire appelé * *sauvegarde**. Vous allez utiliser cette utilisation répertoire sauvegarder et restaurer votre base de données.

1. Le nouveau répertoire d’avec le bouton droit, pointez sur **partager avec**, puis cliquez sur **personnes spécifiques**, comme illustré ci-dessous.

    ![Créer un dossier de sauvegarde](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. Ajouter **CORP\SQLSvc1** et lui donner l’autorisation **En lecture/écriture** , puis ajouter **CORP\SQLSvc2** et lui donner l’autorisation de **lecture** , comme indiqué ci-dessous, puis cliquez sur **partager**. Une fois le processus de partage de fichiers sont terminée, cliquez sur **terminé**.

    ![Accorder des autorisations pour un dossier de sauvegarde](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. Ensuite, vous créez la base de données. Dans le menu **Démarrer** , lancez **SQL Server Management Studio**, puis cliquez sur **se connecter** pour vous connecter à l’instance SQL Server par défaut.

1. Dans l' **Explorateur d’objets**, avec le bouton droit de **bases de données** , puis cliquez sur **Nouvelle base de données**.

1. Dans la zone **nom de la base de données**, tapez **MyDB1**, puis cliquez sur **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Effectuez une sauvegarde de MyDB1 complète et restaurer sur ContosoSQL2 :

1. Ensuite, vous effectuez une sauvegarde complète de la base de données. Dans l' **Explorateur d’objets**, développez les **bases de données**, puis avec le bouton droit **MyDB1**, puis pointez sur **tâches**, puis cliquez sur **Sauvegarder**.

1. Dans la section **Source** , conserver le **type de sauvegarde** est défini sur **complète**. Dans la section de **Destination** , cliquez sur **Supprimer** pour supprimer le chemin d’accès de fichier par défaut pour le fichier de sauvegarde.

1. Dans la section de **Destination** , cliquez sur **Ajouter**.

1. Dans la zone de texte **nom de fichier** , tapez ** \\ContosoSQL1\backup\MyDB1.bak**. Ensuite, cliquez sur **OK**, puis cliquez sur **OK** pour sauvegarder la base de données. Une fois l’opération de sauvegarde terminée, cliquez sur **OK** pour fermer la boîte de dialogue.

1. Ensuite, vous prenez un journal des transactions copie de sauvegarde de la base de données. Dans l' **Explorateur d’objets**, développez les **bases de données**, puis avec le bouton droit **MyDB1**, puis pointez sur **tâches**, puis cliquez sur **Sauvegarder**.

1. Dans type de **sauvegarde** , sélectionnez **Journal des transactions**. Conserver le chemin d’accès du fichier de **Destination** définie sur celui que vous avez spécifiée précédemment cliquez sur **OK**. Une fois l’opération de sauvegarde terminée, cliquez à nouveau sur **OK** .

1. Ensuite, vous restaurez les sauvegardes de journal des transactions et plein sur **ContosoSQL2**. Lancez le fichier RDP pour **ContosoSQL2** et connectez-vous en tant que **CORP\Install**. Laissez la session de bureau à distance pour **ContosoSQL1** ouverte.

1. Dans le menu **Démarrer** , lancez **SQL Server Management Studio**, puis cliquez sur **se connecter** pour vous connecter à l’instance SQL Server par défaut.

1. Dans l' **Explorateur d’objets**, avec le bouton droit de **bases de données** , puis cliquez sur **Restaurer la base de données**.

1. Dans la section **Source** , sélectionnez le **périphérique**, puis cliquez sur le bouton **...** bouton.

1. Dans la zone **Sélectionner les périphériques de sauvegarde**, cliquez sur **Ajouter**.

1. Emplacement du fichier de sauvegarde, tapez \\ContosoSQL1\backup, puis cliquez sur Actualiser, puis sélectionnez MyDB1.bak, puis cliquez sur OK et cliquez à nouveau sur OK. Normalement, la sauvegarde complète et la sauvegarde du journal de la sauvegarde affecte pour rétablir du volet.

1. Accédez à la page Options, puis sélectionnez Restaurer avec NORECOVERY dans l’état de la récupération et puis cliquez sur OK pour restaurer la base de données. Une fois la restauration terminée, cliquez sur OK.

### <a name="create-the-availability-group"></a>Créer le groupe de disponibilité :

1. Revenez à la session de bureau à distance pour **ContosoSQL1**. Dans l' **Explorateur d’objets** dans SSMS, droit **Toujours sur haute disponibilité** , puis cliquez sur **Assistant Nouveau groupe de disponibilité**, comme illustré ci-dessous.

    ![Lancer l’Assistant Nouveau groupe de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. Dans la page **d’Introduction** , cliquez sur **suivant**. Dans la page **Spécifier un nom de groupe de disponibilité** , tapez **AG1** dans **nom du groupe de disponibilité**, puis cliquez à nouveau sur **suivant** .

    ![L’Assistant Nouveau AG, indiquez AG nom](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. Dans la page **Sélectionner les bases de données** , sélectionnez **MyDB1** et cliquez sur **suivant**. La base de données remplit les conditions préalables pour un groupe de disponibilité parce que vous avez bien suivi au moins une sauvegarde complète du réplica principal initial.

    ![L’Assistant Nouveau AG, sélectionnez bases de données](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. Dans la page **Spécifier réplicas** , cliquez sur **Ajouter réplica**.

    ![L’Assistant Nouveau AG, spécifiez réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. La boîte de dialogue **se connecter au serveur** s’affiche. Tapez **ContosoSQL2** dans **nom du serveur**, puis cliquez sur **se connecter**.

    ![L’Assistant Nouveau AG, se connecter au serveur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. Dans la page **Spécifier réplicas** , normalement, **ContosoSQL2** répertoriés dans **Doubles disponibles**. Configurer les réplicas comme illustré ci-dessous. Lorsque vous avez terminé, cliquez sur **suivant**.

    ![L’Assistant Nouveau AG, spécifiez réplicas (complète)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. Dans la page **Sélectionner la synchronisation initiale des données** , cliquez sur **joindre uniquement** , puis cliquez sur **suivant**. Vous avez déjà effectué la synchronisation des données manuellement lorsque vous avez effectué les sauvegardes complète et de transaction sur **ContosoSQL1** et les restaurer sur **ContosoSQL2**. Vous pouvez à la place choisir de ne pas effectuer la sauvegarde et restauration sur votre base de données et sélectionnez **complète** pour permettre à l’Assistant Nouveau groupe de disponibilité effectuer la synchronisation des données à votre place. Toutefois, il n’est pas recommandé de très grandes bases de données qui sont trouvent dans certaines entreprises.

    ![L’Assistant Nouveau AG, activez la synchronisation des données initiale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. Dans la page **Validation** , cliquez sur **suivant**. Cette page doit ressembler à ci-dessous. Un avertissement est pour la configuration de la réception, car vous n’avez pas configuré un récepteur de groupe de disponibilité. Vous pouvez ignorer ce message d’avertissement, car ce didacticiel ne configure pas un récepteur. Pour configurer le récepteur au terme de ce didacticiel, consultez [configurer un récepteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

    ![Assistant Nouveau AG, Validation](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. Dans la page **Résumé** , cliquez sur **Terminer**, puis patienter pendant que l’Assistant configure le nouveau groupe de disponibilité. Dans la page de **progression** , vous pouvez cliquer sur **plus de détails** pour afficher la progression détaillée. Une fois l’Assistant terminé, vérifiez que la page de **résultats** pour vérifier que le groupe de disponibilité est correctement créé, comme indiqué ci-dessous, puis cliquez sur **Fermer** pour quitter l’Assistant.

    ![Résultats de l’Assistant Nouveau AG,](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. Dans l' **Explorateur d’objets**, développez **Toujours sur haute disponibilité**, puis développez les **Groupes de disponibilité**. Vous devez maintenant voir le nouveau groupe de disponibilité dans ce conteneur. Avec le bouton droit **AG1 (principale)** et cliquez sur **Afficher le tableau de bord**.

    ![Afficher le tableau de bord AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. Votre **Toujours sur tableau de bord** doit ressembler à celui illustré ci-dessous. Vous pouvez voir les réplicas, le mode de basculement de chaque réplica et l’état de synchronisation.

    ![Tableau de bord AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. Revenir au **Gestionnaire de serveur**, sélectionnez **Outils**et lancez ensuite le **Gestionnaire de Cluster de basculement**.

1. Développez **Cluster1.corp.contoso.com**, puis développez **Services et applications**. Sélectionnez les **rôles** et notez que le rôle de groupe de disponibilité **AG1** a été créé. Notez que AG1 n’a pas de n’importe quelle adresse IP à la base de données clients peuvent se connecter au groupe de disponibilité, car vous n’avez pas configuré un récepteur. Vous pouvez vous connecter directement vers le nœud principal pour les opérations en lecture / écriture et le nœud secondaire pour les requêtes en lecture seule.

    ![Dans le Gestionnaire de Cluster basculement AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] N’essayez pas de bascule vers le groupe de disponibilité à partir du Gestionnaire de Cluster basculement. Toutes les opérations de basculement doivent être exécutées à partir de dans **Toujours sur tableau de bord** dans SSMS. Pour plus d’informations, consultez [restrictions relatives à l’aide du WSFC basculement Cluster Gestionnaire des groupes de disponibilité](https://msdn.microsoft.com/library/ff929171.aspx).

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant correctement implémenté SQL Server toujours en créant un groupe de disponibilité dans Azure. Pour configurer un récepteur pour ce groupe de disponibilité, consultez [configurer un récepteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Pour d’autres informations sur l’utilisation de SQL Server dans Azure, voir [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
