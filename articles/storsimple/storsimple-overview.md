<properties 
   pageTitle="Quelles sont les StorSimple ? | Microsoft Azure" 
   description="Décrit StorSimple hiérarchisation, l’appareil, périphérique virtuel, services et la gestion du stockage et présente les termes clés utilisés dans StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série 8000 StorSimple : une solution de stockage cloud hybride

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple, une solution de stockage intégrée qui gère les tâches de stockage entre les périphériques en local et du stockage cloud Microsoft Azure. StorSimple est une solution de réseau (SAN) de zone de stockage efficace, rentable et facilement gérables qui élimine la plupart des problèmes et dépenses associées entreprise stockage et protection des données. Il utilise le périphérique série 8000 StorSimple propriétaire s’intègre avec les services cloud et propose un ensemble d’outils de gestion pour afficher tous les stockage d’entreprise, y compris le stockage cloud transparente. (Les informations de déploiement StorSimple publiées sur le site Web Microsoft Azure s’applique aux périphériques série 8000 StorSimple uniquement. Si vous utilisez un appareil série StorSimple 5000/7000, accédez à [l’Aide de StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple utilise [la hiérarchisation du stockage](#automatic-storage-tiering) pour gérer les données stockées sur différents supports de stockage. Le jeu de travail en cours est stocké en local sur disques (SSDs), les données qui sont utilisées moins fréquemment sont stockées sur des disques durs (disques durs) et données d’archivage sont basculées vers le cloud. En outre, StorSimple utilise déduplication et la compression pour réduire la quantité de stockage qui utilise les données. Pour plus d’informations, accédez à [la déduplication et la compression](#deduplication-and-compression). Pour les définitions d’autres termes et concepts clés utilisés dans la documentation de série 8000 StorSimple, accédez à [la terminologie StorSimple](#storsimple-terminology) à la fin de cet article.

Avec StorSimple mise à jour 2, vous pouvez identifier les volumes appropriés comme *localement épinglées* pour garantir que les données principal restent locales à l’appareil et ne pas niveau dans le cloud. Ainsi, vous permet d’exécuter les charges de travail qui sont sensibles à la latence cloud, tel que SQL et machine virtuelle charges de travail, sur les volumes épinglées localement, tout en continuant à utiliser le cloud pour la sauvegarde. Pour plus d’informations sur les volumes localement épinglés, voir [utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes-u2.md). 

Mise à jour 2 vous permet également de créer des périphériques virtuels StorSimple tirant parti de la latence basse et haute performance fourni par le stockage Azure premium. Pour plus d’informations sur les périphériques virtuels StorSimple premium, voir [déployer et gérer un périphérique virtuel StorSimple dans Azure](storsimple-virtual-device-u2.md). Pour plus d’informations sur le stockage Azure premium, accédez à [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md).

En plus de gestion du stockage, fonctions de protection des données StorSimple permettent de créer à la demande et sauvegardes planifiées et les stocker localement ou dans le cloud. Les sauvegardes sont effectuées sous la forme d’instantanés incrémentiels, ce qui signifie qu’ils peuvent être créés et restaurés rapidement. Cloud instantanés peuvent être essentiel dans récupération d’urgence scénarios, car ils remplacement les systèmes de stockage secondaire (par exemple, de la bande de sauvegarde) et vous permettent pas de restaurer les données dans votre centre de données ou sur d’autres sites si nécessaire.

![icône de vidéo](./media/storsimple-overview/video_icon.png) Regardez la vidéo pour une brève introduction à Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>Pourquoi utiliser StorSimple ?

Le tableau suivant décrit certains des principaux avantages fournissant Microsoft Azure StorSimple.

| Fonctionnalité | Avantage |
|---------|---------|
|Intégration transparente | Microsoft Azure StorSimple utilise le protocole iSCSI pour lier invisible installations de stockage de données. Cela garantit que les données stockées dans le cloud, auprès du centre de données, ou sur des serveurs distants s’affiche pour être stockées dans un emplacement unique.|
|Coûts de stockage réduite|Microsoft Azure StorSimple attribue local suffisant ou stockage cloud pour répondre aux exigences actuelles et étend stockage cloud uniquement lorsque cela est nécessaire. Il réduit encore besoins de stockage et de note de frais en supprimant les versions redondantes des mêmes données (déduplication) et en utilisant la compression.|
|Gestion du stockage simplifiée|Microsoft Azure StorSimple fournit des outils d’administration que vous pouvez utiliser pour configurer et gérer les données stockées en local, sur un serveur distant et dans le cloud système. En outre, vous pouvez gérer la sauvegarde et restaurer des fonctions à partir d’un composant logiciel enfichable Microsoft Management Console (MMC). StorSimple fournit une interface distincte, facultative que vous pouvez utiliser pour étendre les services de protection des données et de gestion des StorSimple au contenu stocké sur les serveurs SharePoint. |
|Reprise amélioré et conformité|Microsoft Azure StorSimple ne nécessite pas de temps de récupération étendue. Au lieu de cela, il restaure les données qu’il est nécessaire. Cela signifie opérations normales continuez avec une interruption minimale. En outre, vous pouvez configurer des stratégies pour spécifier les plannings de sauvegarde et de conservation des données.
|Mobilité des données|Données téléchargées aux services cloud Microsoft Azure sont accessibles à partir d’autres sites à des fins de migration et de restauration. En outre, vous pouvez utiliser StorSimple pour configurer les périphériques virtuels StorSimple sur machines virtuelles (machines virtuelles) en cours d’exécution dans Microsoft Azure. Les ordinateurs virtuels peuvent ensuite utiliser périphériques virtuels pour accéder aux données stockées fins de test ou récupération.|
|Prise en charge des autres fournisseurs de services de cloud |Mettre à jour la série 8000 StorSimple avec un logiciel de 1 ou ultérieure prend en charge Amazon S3 avec les enregistrements de ressource, HP et OpenStack cloud services, ainsi que Microsoft Azure. (Vous devez toujours un compte de stockage de Microsoft Azure fins de gestion des périphériques.) Pour plus d’informations, accédez à [Nouveautés de mise à jour 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuité des activités | Mise à jour 1 ou version ultérieure fournit une nouvelle fonctionnalité de migration qui permet aux StorSimple utilisateurs de série 5000-7000 migrer leurs données vers un périphérique de série 8000 StorSimple.|
|Disponibilité dans le portail pour le gouvernement Azure | Mise à jour StorSimple 1 ou version ultérieure est disponible dans le portail pour le gouvernement Azure. Pour plus d’informations, voir [déployer votre appareil StorSimple local dans le portail d’administration](storsimple-deployment-walkthrough-gov.md).|
|Disponibilité et protection des données | La série 8000 StorSimple avec la mise à jour 1 ou version ultérieure prend en charge la Zone redondants stockage (ZRS), en plus de stockage stockage localement redondantes (LRS) et Geo redondantes (GRS). Consultez [cet article sur les options de redondance de stockage Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) pour plus d’informations ZRS.|
|Prise en charge pour les applications critiques | Avec StorSimple mise à jour 2, vous pouvez identifier les volumes appropriés épinglées localement. Cette fonctionnalité permet de garantir que les données requises par les applications critiques ne sont pas hiérarchisées dans le cloud. Volumes localement épinglés ne sont pas soumis à latence cloud ou problèmes de connectivité. Pour plus d’informations sur les volumes localement épinglés, voir [utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes-u2.md).|
|Faible latence et haute performance | StorSimple mise à jour 2 vous permet de créer des périphériques virtuels qui tirer parti des performances élevées, les fonctionnalités de faible latence de stockage Azure premium. Pour plus d’informations sur les périphériques virtuels StorSimple premium, voir [déployer et gérer un périphérique virtuel StorSimple dans Azure](storsimple-virtual-device-u2.md).|

![icône de vidéo](./media/storsimple-overview/video_icon.png) [cette vidéo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) offre une vue d’ensemble des fonctionnalités de la série 8000 StorSimple et des avantages.

## <a name="storsimple-components"></a>Composants StorSimple

La solution Microsoft Azure StorSimple comprend les éléments suivants :

- **Appareil de Microsoft Azure StorSimple** – un tableau de stockage local hybride contenant SSDs et disques durs, ainsi que la contrôleurs redondants et des fonctions de basculement automatique. Les contrôleurs de gérer le stockage hiérarchisation, en plaçant actuellement utilisées (ou active) des données sur le stockage local (dans les serveurs appareil ou en local), tout en les déplaçant moins fréquemment utilisées dans le cloud.
- **Périphérique virtuel StorSimple** – également connu sous le matériel virtuel StorSimple, il s’agit une version du logiciel de l’appareil StorSimple qui réplique l’architecture et la plupart des fonctionnalités de l’appareil de stockage physique hybride. Le périphérique virtuel StorSimple s’exécute sur un seul nœud dans une machine virtuelle Azure. Périphériques virtuels Premium qui tirer parti du stockage Azure premium, sont disponibles dans la mise à jour 2 et versions ultérieures.
- **Service Manager StorSimple** – une extension du portail classique Azure qui vous permet de gérer un StorSimple périphérique ou StorSimple virtuel à partir d’une interface web unique. Vous pouvez utiliser le service Manager StorSimple pour créer et gérer les services, afficher et gérer les périphériques, afficher les alertes, gérer les volumes et afficher et gérer les stratégies de sauvegarde et le catalogue de sauvegarde.
- **Windows PowerShell pour StorSimple** – une interface de ligne de commande que vous pouvez utiliser pour gérer le périphérique StorSimple. Windows PowerShell pour StorSimple inclut des fonctionnalités qui vous permettent d’inscrire votre appareil StorSimple, configurer l’interface réseau sur votre appareil, installer certains types de mises à jour, résoudre les problèmes de votre appareil en accédant à la session de support et modifier l’état du périphérique. Vous pouvez accéder Windows PowerShell pour StorSimple en vous connectant à la console en série ou à l’aide de Windows PowerShell distante.
- **Applets de commande azure PowerShell StorSimple** – un ensemble d’applets de commande Windows PowerShell qui vous permettent d’automatiser les tâches de niveau de service et la migration à partir de la ligne de commande. Pour plus d’informations sur les applets de commande PowerShell Azure pour StorSimple, accédez à la [référence de l’applet de commande](https://msdn.microsoft.com/library/dn920427.aspx).
- **Gestionnaire d’instantanés StorSimple** – un composant logiciel enfichable MMC qui utilise des groupes de volumes et le Service Windows Volume ombre pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser le Gestionnaire d’instantanés StorSimple créer cloner et des plannings de sauvegarde ou restaurer des volumes. 
- **Carte StorSimple pour SharePoint** – un outil de manière transparente étend Microsoft Azure StorSimple stockage et protection des données aux batteries de serveurs SharePoint, tout en effectuant StorSimple stockage visible et facile à gérer à partir du portail d’Administration centrale de SharePoint.

L’illustration ci-dessous fournit une vue d’ensemble des composants et architecture de Microsoft Azure StorSimple.

![Architecture StorSimple](./media/storsimple-overview/overview-big-picture.png)

Les sections suivantes décrivent chacun de ces composants plus en détail et expliquent comment la solution organise les données, affecte le stockage et facilite la gestion du stockage et protection des données. La dernière section fournit des définitions de certains des termes importants et concepts relatifs aux composants StorSimple et de leur gestion.

## <a name="storsimple-device"></a>APPAREIL StorSimple

Le périphérique Microsoft Azure StorSimple est un tableau de stockage local hybride fournissant stockage principal et iSCSI accès aux données stockées sur celui-ci. Il gère la communication avec stockage cloud et permet de garantir la sécurité et la confidentialité de toutes les données qui se trouve sur la solution Microsoft Azure StorSimple.

Le périphérique StorSimple inclut SSDs et disques durs lecteurs de disques durs, ainsi que la prise en charge pour le basculement cluster et automatique. Il contient un processeur partagé, le stockage partagé et deux contrôleurs en miroir. Chaque contrôleur fournit les éléments suivants :

- Connexion à un ordinateur hôte
- Jusqu'à six ports réseau pour vous connecter au réseau local (LAN)
- Contrôle du matériel
- Mémoire vive non volatiles (NVRAM), ce qui conserve les informations même si alimentation est interrompue
- Cluster prenant en charge les mise à jour pour gérer les mises à jour logicielles sur des serveurs dans un cluster de basculement afin que les mises à jour offrent peu ou aucun effet sur la disponibilité du service
- Service de cluster, qui fonctionne comme un cluster principale, une haute disponibilité et réduction des effets indésirables susceptibles de se produire si un disque dur ou SSD échoue ou est hors connexion

Qu’un seul contrôleur est actif à tout moment dans le temps. Si le contrôleur actif échoue, le deuxième contrôleur devient automatiquement actif. 

Pour plus d’informations, accédez à [état et composants matériels StorSimple](storsimple-monitor-hardware-status.md).

## <a name="storsimple-virtual-device"></a>Périphérique virtuel StorSimple

Vous pouvez utiliser StorSimple pour créer un périphérique virtuel qui réplique l’architecture et les fonctionnalités de l’appareil de stockage physique hybride. Le périphérique virtuel StorSimple (également appelé StorSimple virtuel celle-ci) s’exécute sur un seul nœud dans une machine virtuelle Azure. (Un périphérique virtuel peut être créé uniquement sur un ordinateur virtuel Azure. Vous ne pouvez pas créer une sur un appareil StorSimple ou un serveur local.) 

Le périphérique virtuel comporte les fonctionnalités suivantes :

- Il se comporte comme un appareil physique et peut proposer une interface iSCSI aux machines virtuelles dans le cloud. 
- Vous pouvez créer un nombre illimité de périphériques virtuels dans le nuage et les activer et désactiver comme bon vous semble. 
- Il vous permet de simuler les environnements locaux dans sinistre, de développement et de scénarios de test et peut vous aider à récupération au niveau des éléments à partir de sauvegardes. 

Mise à jour 2 et les versions ultérieures, le périphérique virtuel StorSimple est disponible en deux modèles : le périphérique 8010 (auparavant appelé le modèle 1100) et le périphérique 8020. Le périphérique 8010 a une capacité maximale de 30 To. Le périphérique 8020, qui tire parti du stockage Azure premium, a une capacité maximale de 64 To. (Dans les niveaux locales, stockage premium Azure stocke les données sur SSDs alors que stockage standard stocke les données sur disques durs.) Notez que vous devez posséder un compte de stockage Azure premium à utiliser le stockage premium. Pour plus d’informations sur le stockage premium, accédez à [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md).

Pour plus d’informations sur le périphérique virtuel StorSimple, accédez à [déployer et gérer un périphérique virtuel StorSimple dans Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Service Manager StorSimple

Microsoft Azure StorSimple fournit une interface utilisateur basée sur le web (le service Manager StorSimple) qui vous permet de gérer le centre de données et de stockage en nuage de façon centralisée. Vous pouvez utiliser le service Manager StorSimple pour effectuer les tâches suivantes :

- Configurer les paramètres système pour les appareils StorSimple.
- Configurer et gérer les paramètres de sécurité des appareils StorSimple.
- Configurer les propriétés et des informations d’identification cloud.
- Configurer et gérer des volumes sur un serveur.
- Configurer les groupes de volumes.
- Sauvegarder et restaurer des données.
- Surveiller les performances.
- Revoir les paramètres système et d’identifier les éventuels problèmes.

Vous pouvez utiliser le service Manager StorSimple pour effectuer toutes les tâches d’administration à l’exception de ceux qui nécessitent système, telles que la configuration initiale et l’installation des mises à jour.

Pour plus d’informations, accédez à [utiliser le service Manager StorSimple pour administrer votre appareil StorSimple](storsimple-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour créer et gérer le service Microsoft Azure StorSimple et configurer et surveiller les périphériques StorSimple. Il est une interface de ligne de commande Windows PowerShell – basé, incluant dédié applets de commande pour la gestion de votre appareil StorSimple. Windows PowerShell pour StorSimple contient des fonctionnalités qui vous permettent de :

- Enregistrer un appareil.
- Configurer l’interface réseau sur un appareil.
- Installer certains types de mises à jour.
- Résoudre les problèmes de votre appareil en accédant à la session de prise en charge.
- Modifier l’état de l’appareil.

Vous pouvez accéder Windows PowerShell pour StorSimple depuis une console série (sur un ordinateur hôte connecté directement à l’appareil) ou à distance à l’aide de Windows PowerShell distante. Notez que certaines Windows PowerShell pour les tâches StorSimple, telles que l’inscription initiale d’un appareil, ne peut être effectué sur la console série. 

Pour plus d’informations, accédez à [Utiliser Windows PowerShell pour StorSimple administrer votre appareil](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Applets de commande PowerShell StorSimple Azure

Les applets de commande Azure PowerShell StorSimple sont un ensemble d’applets de commande Windows PowerShell qui vous permettent d’automatiser les tâches de niveau de service et la migration à partir de la ligne de commande. Pour plus d’informations sur les applets de commande PowerShell Azure pour StorSimple, accédez à la [référence de l’applet de commande](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>Gestionnaire d’instantanés StorSimple

Gestionnaire d’instantanés StorSimple est un composant logiciel enfichable Microsoft Management Console (MMC) que vous pouvez utiliser pour créer cohérentes, copies de sauvegarde de point dans le temps de local et données cloud. Le composant logiciel enfichable s’exécute sur un hôte Windows Server. Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple à :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volume pour vous assurer que les données sauvegardées est cohérentes avec les applications.
- Gérer les stratégies de sauvegarde afin que les données sont sauvegardées sur une planification prédéfinie et stockées dans un emplacement désigné (localement ou dans le cloud).
- Restaurer des volumes et des fichiers individuels.

Effectuer des sauvegardes régulières capturées sous la forme d’instantanés qui enregistrent que les modifications depuis le dernier instantané pris et nécessitent beaucoup moins d’espace de stockage que les sauvegardes complètes. Vous pouvez créer des plannings de sauvegarde ou effectuer des sauvegardes immédiates selon vos besoins. En outre, vous pouvez utiliser Gestionnaire d’instantanés StorSimple d’établir des stratégies de rétention qui contrôlent combien instantanés seront enregistrés. Si vous devez ensuite restaurer les données à partir d’une sauvegarde, vous permet de gestionnaire d’instantanés StorSimple vous sélectionnez à partir du catalogue de locale ou instantanés cloud. 

Si un incident se produit ou si vous avez besoin restaurer les données pour une autre raison, gestionnaire d’instantanés StorSimple restaure par incréments qu’il est nécessaire. Restauration des données ne nécessite pas arrêter l’ensemble du système pendant que vous restaurez un fichier, remplacez équipement ou déplacez des opérations vers un autre site.

Pour plus d’informations, accédez à [Nouveautés Gestionnaire d’instantanés StorSimple ?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Carte StorSimple pour SharePoint

Microsoft Azure StorSimple comprend la carte StorSimple pour SharePoint, un composant facultatif qui étend en toute transparence des fonctionnalités de protection des données et de stockage StorSimple aux batteries de serveurs SharePoint. La carte fonctionne avec un fournisseur de stockage d’objets Blob distants (RBS) et la fonctionnalité de SQL Server RBS, ce qui vous permet de déplacer des objets BLOB sur un serveur sauvegardé par le système Microsoft Azure StorSimple. Microsoft Azure StorSimple stocke les données BLOB localement ou dans le cloud, en fonction de l’utilisation.

La carte StorSimple pour SharePoint est gérée à partir du portail d’Administration centrale de SharePoint. Par conséquent, gestion de SharePoint reste centralisée, et tout le stockage apparaît dans la batterie de serveurs SharePoint.

Pour plus d’informations, accédez à [StorSimple carte pour SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologies de gestion de stockage
 
Outre le périphérique StorSimple dédié, périphérique virtuel et d’autres composants, Microsoft Azure StorSimple utilise les technologies de logiciel suivantes permettent d’accéder rapidement aux données et de réduire la consommation de stockage :

- [Hiérarchisation du stockage automatique](#automatic-storage-tiering) 
- [Contrôle de l’approvisionnement](#thin-provisioning) 
- [Déduplication et la compression](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Hiérarchisation du stockage automatique

Microsoft Azure StorSimple Réorganise automatiquement les données dans des couches logiques basés sur l’utilisation actuelle, l’âge et relation à d’autres données. Les données plus actives sont stockées localement, tout en moins des données actives et inactives sont automatiquement migrées vers le cloud. Le diagramme suivant illustre cette approche de stockage.
 
![Niveaux de stockage StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Pour activer l’accès rapide, StorSimple stocke les données très actives (données hot) sur SSDs dans le périphérique StorSimple. Il stocke les données qui sont utilisées occasionnellement (mat données) sur disques durs dans le périphérique ou sur des serveurs auprès du centre de données. Il déplace les données inactives, les données de sauvegarde, et conservation des données pour archivage ou à des fins de conformité dans le cloud. 

>[AZURE.NOTE] Mise à jour 2 ou version ultérieure, vous pouvez spécifier un volume épinglé localement, auquel cas les données demeurent sur le périphérique local et ne sont pas hiérarchisé vers le cloud. 

StorSimple ajuste et réorganise les données et modifier les affectations de stockage en tant que modèles d’utilisation. Par exemple, certaines informations peuvent devenir moins actifs au fil du temps. Dès qu’elles seront progressivement moins actif, il migré à partir de SSDs disques durs, puis dans le cloud. Si ces mêmes données redevient actives, il est migré à l’unité de stockage.

Le processus de hiérarchisation stockage se présente comme suit :

1. Un administrateur système configure un compte de stockage cloud Microsoft Azure.
2. L’administrateur utilise la console série et le service du Gestionnaire de StorSimple (en cours d’exécution dans le portail classique Azure) pour configurer le serveur de fichier et du périphérique, création de stratégies de protection des volumes et les données. Ordinateurs locaux (par exemple, des serveurs de fichiers) utilisent l’Interface petite ordinateur système Internet (iSCSI) pour accéder au périphérique StorSimple.
3. Au départ, StorSimple stocke les données sur la couche SSD rapide de l’appareil.
4. Comme la couche SSD approche la capacité, StorSimple deduplicates et compresse les blocs de données plus ancien et les déplace vers le niveau du disque dur.
5. En tant que la capacité approches du disque dur, StorSimple chiffre les plus ancien blocs de données et les envoie en toute sécurité sur le compte de stockage de Microsoft Azure via HTTPS.
6. Microsoft Azure crée plusieurs répliques des données dans son centre de données et dans un centre de données distante, garantissant que les données peuvent être récupérées en cas de sinistre. 
7. Lorsque le serveur de fichiers demande des données stockées dans le cloud, StorSimple renvoie en toute transparence et enregistre une copie sur la couche SSD du périphérique StorSimple.

### <a name="thin-provisioning"></a>Contrôle de l’approvisionnement

Contrôle de l’approvisionnement est une technologie de virtualisation dans laquelle stockage disponible apparaît dépassement ressources physiques. Au lieu de réservation d’espace de stockage suffisant à l’avance, StorSimple utilise le contrôle de l’approvisionnement pour allouer simplement suffisamment d’espace pour répondre aux exigences actuelles. La nature élastique de stockage cloud facilite cette approche car StorSimple pouvez augmenter ou diminuer le stockage cloud pour répondre à la demande de modification. 

>[AZURE.NOTE] Volumes localement épinglés ne sont pas exactement mis en service. Stockage alloué sur un volume local uniquement est mis en service dans son intégralité lorsque le volume est créé.

### <a name="deduplication-and-compression"></a>Déduplication et la compression

Microsoft Azure StorSimple utilise la compression déduplication et les données afin de réduire davantage les besoins de stockage.

Déduplication permettant de réduire le montant total des données stockées en supprimant redondantes le jeu de données stocké. Comme informations changent, StorSimple ignore les données ne change pas et capture uniquement les modifications. En outre, StorSimple permet de réduire la quantité de données stockées en identifiant et en supprimant les informations inutiles. 

>[AZURE.NOTE] Les données des volumes localement épinglés ne sont pas dédupliquées ou compressées. Toutefois, des sauvegardes des volumes localement épinglés sont dédupliqués et compressés.

## <a name="storsimple-workload-summary"></a>Charge de travail StorSimple résumé

Un résumé des charges de travail StorSimple pris en charge est sous forme de tableau en dessous.

| Scénario                  | Charge de travail                | Prise en charge |  Restrictions                                  | Version              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Collaboration             | Partage de fichiers            | Oui       |                                                | Toutes les versions         |
| Collaboration             | Partage de fichiers distribué| Oui       |                                                | Toutes les versions         |
| Collaboration             | SharePoint              | Oui *      |Prise en charge uniquement avec les volumes localement épinglés      | Mise à jour 2 ou version ultérieure   |
| Archivage                  | Archivage des fichiers simple   | Oui       |                                                | Toutes les versions         |
| Virtualisation            | Machines virtuelles        | Oui *      |Prise en charge uniquement avec les volumes localement épinglés      | Mise à jour 2 ou version ultérieure   |
| Base de données                  | SQL                     | Oui *      |Prise en charge uniquement avec les volumes localement épinglés      | Mise à jour 2 ou version ultérieure   |
| Surveillance vidéo        | Surveillance vidéo      | Oui *       |Prise en charge lorsque StorSimple est dédié uniquement pour cette charge de travail| Mise à jour 2 ou version ultérieure   |
| Sauvegarde                    | Sauvegarde cible principale | Oui *      |Prise en charge lorsque StorSimple est dédié uniquement pour cette charge de travail| Mise à jour 3 et version ultérieure |
| Sauvegarde                    | Sauvegarde cible secondaire | Oui *      |Prise en charge lorsque StorSimple est dédié uniquement pour cette charge de travail| Mise à jour 3 et version ultérieure |

*Oui & #42 ; -Restrictions et les instructions de la solution doivent être appliquées.*

Les charges de travail suivants ne sont pas prises en charge par les périphériques de série 8000 StorSimple. Si déployé sur StorSimple, ces charges de travail seront produit dans une configuration non prises en charge.

-  Création d’images médicale
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Données volumineux
-  Distribution de contenu
-  Démarrer à partir de SCSI

Voici une liste des composants infrastructure StorSimple pris en charge. 

| Scénario | Charge de travail      | Prise en charge |  Restrictions                                 | Version      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Général  | Voie Express | Oui       |                                               |Toutes les versions |
| Général  | DataCore FC   | Oui *       |Prise en charge avec DataCore SANsymphony            | Toutes les versions |
| Général  | DFSR          | Oui *      |Prise en charge uniquement avec les volumes localement épinglés     | Toutes les versions |
| Général  | L’indexation      | Oui *       |Pour les volumes hiérarchisés, seules les métadonnées de l’indexation est pris en charge (aucune donnée).<br>Pour les volumes localement épinglés, indexation complète est prise en charge.| Toutes les versions |
| Général  | Protection contre les virus    | Oui *       |Pour les volumes hiérarchisés, seuls les analyse lors de l’ouverture et fermeture est pris en charge.<br> Pour les volumes localement épinglés, analyse complète est prise en charge.| Toutes les versions |

*Oui & #42 ; -Restrictions et les instructions de la solution doivent être appliquées.*

## <a name="storsimple-terminology"></a>Terminologie StorSimple 

Avant de déployer votre solution Microsoft Azure StorSimple, nous vous recommandons de consulter les termes et définitions.

### <a name="key-terms-and-definitions"></a>Clé termes et définitions

| Magasin de termes (acronyme ou abréviation) | Description |
| ------------------------------ | ---------------- |
| enregistrement du contrôle d’accès (blocage)    | Un enregistrement associé à un volume sur votre appareil Microsoft Azure StorSimple déterminant quels hôtes peuvent se connecter à celle-ci. La détermination est basée sur l’iSCSI complet nom (nom qualifié) des hôtes (contenues dans le blocage) qui sont connectent à votre appareil StorSimple.|
| AES-256                        | Un algorithme de chiffrement AES (Advanced Standard) 256 bits pour le chiffrement des données telle qu’elle se déplace vers et depuis le cloud. |
| taille d’unité d’allocation (Australie)     | Systèmes de fichiers la plus petite quantité d’espace disque pouvant être alloués pour mettre en attente d’un fichier dans vos fenêtres. Si une taille de fichier n’est pas un multiple pair de la taille de cluster, un espace supplémentaire doit être utilisé pour stocker le fichier (jusqu’au multiple suivant de la taille de cluster) résultant dans l’espace perdu et la fragmentation du disque dur. <br>L’Australie recommandé pour les volumes StorSimple Azure est 64 Ko, car elle fonctionne bien avec les algorithmes déduplication.|
| hiérarchisation du stockage automatisé      | Déplacer automatiquement des données moins actifs de SSDs disques durs, puis à un niveau dans le cloud et en activant puis gestion du stockage tout à partir d’une interface utilisateur centrale.|
| catalogue de sauvegarde | Collection des sauvegardes, généralement liées par le type d’application qui a été utilisé. Cette collection s’affiche dans la page du catalogue de sauvegarde du service Manager StorSimple l’interface utilisateur.|
| fichier de sauvegarde de catalogue             | Un fichier qui contient la liste des instantanés disponibles actuellement stockés dans la base de données de sauvegarde du Gestionnaire de capture instantanée StorSimple. |
| stratégie de sauvegarde                   | Sélection des volumes, type de sauvegarde et un calendrier qui vous permet de créer des sauvegardes sur un planning prédéfini.|
| objets binaires volumineux (BLOB)    | Collection de données binaires stockées comme une seule entité dans un système de gestion de base de données. Objets BLOB sont généralement images, audio ou autres objets multimédias, bien que le code exécutable parfois binaire est stocké comme un objet BLOB.|
| HIP négociation d’authentification CHAP (Protocol) | Protocole utilisé pour authentifier l’homologue d’une connexion, en fonction de l’homologue partage d’un mot de passe ou un code secret. CHAP peut être à sens unique ou commun. Avec CHAP à sens unique, la cible authentifie un initiateur. CHAP commun nécessite que la cible authentifie l’initiateur et que l’initiateur authentifie la cible. | 
| cloner                          | Copie d’un volume. |
|Cloud comme un niveau (CaaT)          | Stockage intégré comme un niveau au sein de l’architecture de stockage de sorte que tout le stockage apparaît à faire partie du réseau de stockage d’une entreprise cloud.|
| fournisseur de services de cloud (fournisseur)   | Fournisseur du cloud computing services.|
| instantané du cloud                 | Une copie point-à-temps de données de volume qui sont stockées dans le cloud. Un instantané de cloud équivaut à un instantané répliqué sur un système de stockage différent, hors site. Cloud instantanés sont particulièrement utiles dans les scénarios de récupération d’urgence.|
| clé de chiffrement de stockage cloud   | Un mot de passe ou une clé utilisée par votre appareil StorSimple pour accéder aux données chiffrées envoyées par votre appareil dans le cloud.|
| mise à jour prenant en charge cluster         | Gestion des mises à jour logicielles sur des serveurs dans un cluster de basculement afin que les mises à jour offrent peu ou aucun effet sur la disponibilité du service.|
| DataPath                       | Collection d’unités fonctionnelles qui effectuent des opérations de traitement des données reliées entre elles.|
| désactiver                     | Action permanente qui interrompt la connexion entre le périphérique StorSimple et le service cloud associé. Nuage des instantanés de l’appareil restent après ce processus et peuvent être cloner ou utilisées pour la reprise.|
| la mise en miroir                 | Réplication des volumes de disque logique sur séparé dur lecteurs en temps réel pour assurer la disponibilité continue.|
| la mise en miroir de disque dynamique         | Réplication des volumes de disque logique sur des disques dynamiques.|
| disques dynamiques                  | Un format de volume de disque qui utilise le disque Gestionnaire logique pour stocker et gérer des données entre plusieurs disques physiques. Disques dynamiques peuvent être agrandies pour fournir davantage d’espace libre.|
| Étendue encadrement Bunch de disques (EBOD) | Un boîtier secondaire de votre périphérique Microsoft Azure StorSimple qui contient des disques disque dur supplémentaire de stockage supplémentaire.|
| mise en service FAT               | Un stockage conventionnelle mise en service qui est alloué en fonction d’anticipé besoins (et est généralement au-delà de la nécessité actuelle). Voir aussi *contrôle de l’approvisionnement*.|
| disque dur (disque dur)          | Un lecteur qui utilise des disques rotation pour stocker les données.|
| stockage cloud hybride           | Architecture de stockage qui utilise des ressources locales et hors site, y compris le stockage cloud.|
| Interface Internet petite ordinateur système (iSCSI) | Une stockage IP Internet Protocol norme de communication réseau pour la liaison des équipements de stockage de données ou des installations.|
| initiateur iSCSI                 | Un composant logiciel qui permet à un ordinateur hôte exécutant Windows pour vous connecter à un réseau de stockage iSCSI externe.|
| iSCSI nom complet (nom qualifié)      | Un nom unique qui identifie une cible iSCSI ou initiateur.|
| cible iSCSI                    | Composant logiciel qui fournit des sous-systèmes de disque dans les réseaux SAN iSCSI centralisé.|
| Live d’archivage                  | Une approche de stockage dans lequel les données archivage sont accessibles tout le temps (il n'est pas stocké hors site sur bande, par exemple). Microsoft Azure StorSimple utilise l’archivage live.|
|volume localement épinglé | un volume qui se trouve sur le périphérique et n’est jamais hiérarchisé vers le cloud. |
| instantané local                  | Une copie de point dans le temps de données d’un volume qui sont trouve sur le périphérique Microsoft Azure StorSimple.|
| StorSimple Microsoft Azure      | Une solution puissante composée d’un équipement de stockage de centre de données et les logiciels qui permet aux départements informatiques de tirer parti du stockage cloud comme s’il s’agissait de stockage du centre de données. StorSimple simplifie la protection des données et la gestion des données en réduisant les coûts. La solution regroupe stockage, d’archivage, sauvegarde et urgence récupération principale (DR) via l’intégration transparente avec le cloud. En combinant SAN stockage et nuage de gestion des données sur une plateforme d’entreprise, appareils StorSimple activer la vitesse, simplicité et fiabilité pour tous les besoins en matière de stockage.|
| Puissance et module de (PCM)  | Composants matériels de votre appareil StorSimple composé de l’alimentation et le ventilateur, par conséquent, le nom de Power et module de. Le boîtier principal du périphérique comporte deux 764W PCMs tandis que le boîtier EBOD comporte deux PCMs 580 w.|
| boîtier principal               | Encadrement principale de votre appareil StorSimple qui contient les contrôleurs de plateforme d’application.|
| objectif de temps de récupération (RTO)   | La durée maximale pendant laquelle qui doit être fiche avant un processus métier ou un système est entièrement restaurée après un incident.| 
|série joint SCSI (sa)       | Type de lecteur de disque dur (disque dur).|
| clé de chiffrement de données de service     | Une clé à n’importe quel appareil StorSimple nouvelle disposition qui enregistre auprès du service Manager StorSimple. Les données de configuration transférées entre le service Manager StorSimple et l’appareil sont chiffrées à l’aide d’une clé publique et peuvent ensuite être déchiffrées uniquement sur le périphérique à l’aide d’une clé privée. Clé de chiffrement de données de service permet au service obtenir cette clé privée pour le déchiffrement.|
| clé d’enregistrement de service        | Une clé qui vous permet d’enregistrer le périphérique StorSimple avec le service Manager StorSimple afin qu’il apparaisse dans le portail classique Azure pour poursuivre les actions de gestion.|
| Ordinateur petite système Interface (SCSI) | Un ensemble de normes pour connecter les ordinateurs physiquement et transmission des données entre elles.|
| lecteur Flash (SSD)         | Un disque contenant pas de pièces mobiles ; par exemple, un lecteur flash.|
| compte de stockage                 | Un ensemble d’informations d’identification access liée à votre compte de stockage pour un fournisseur de services de cloud donnée.| 
| Carte StorSimple pour SharePoint| Composant Microsoft Azure StorSimple en toute transparence étend StorSimple stockage et protection des données aux batteries de serveurs SharePoint.|
| Service Manager StorSimple      | Extension du portail classique Azure qui permet de gérer vos périphériques virtuels et Azure StorSimple locaux.|
| Gestionnaire d’instantanés StorSimple     | Un composant logiciel enfichable Microsoft Management Console (MMC) pour gérer les opérations de sauvegarde et de restauration dans Microsoft Azure StorSimple.|
| prendre sauvegarde                     | Une fonctionnalité qui permet à l’utilisateur effectuer une sauvegarde interactive d’un volume. Il est une autre façon de prendre une sauvegarde manuelle d’un volume Contrairement à prendre une sauvegarde automatisée via une stratégie définie.|
| contrôle de l’approvisionnement               | Une méthode permettant d’optimiser l’efficacité avec laquelle l’espace de stockage disponible est utilisée dans les systèmes de stockage. Dans le contrôle de l’approvisionnement, le stockage est réparti entre plusieurs utilisateurs en fonction de l’espace minimum requis par chaque utilisateur à un moment donné. Voir aussi *fat mise en service*.|
| hiérarchisation | Organisation des données en groupes logiques basés sur l’utilisation actuelle, l’âge et relation à d’autres données. StorSimple Réorganise automatiquement les données de niveaux.   |
| volume                          | Zones de stockage logiques présentées sous la forme de disques. StorSimple volumes correspondent aux volumes chargés par l’hôte, y compris ceux découvert par le biais iSCSI et un périphérique StorSimple.|
 | conteneur de volume                | Regroupement des volumes et des paramètres qui s’appliquent à eux. Tous les volumes de votre appareil StorSimple sont regroupées dans des conteneurs de volume. Paramètres du conteneur volume sont comptes de stockage, les paramètres de chiffrement des données envoyées vers le cloud avec clés de chiffrement associées et la bande passante utilisée pour les opérations portant sur le cloud.|
| groupe de volume                    | Dans le Gestionnaire d’instantané StorSimple, un groupe de volumes est un ensemble de volumes configurés pour faciliter le processus de sauvegarde.|
| Volume Shadow Copy Service (VSS)| Service de système d’exploitation Windows Server qui facilite la cohérence des applications à communiquer avec des applications prenant en charge VSS pour coordonner la création d’instantanés incrémentiels. VSS garantit que les applications sont temporairement inactives lorsque des instantanés.|
| Windows PowerShell pour StorSimple | Une interface de ligne de commande Windows PowerShell – basé utilisée pour faire fonctionner et gérer votre appareil StorSimple. Tout en conservant certaines des fonctionnalités de base de Windows PowerShell, cette interface a dédié applets de commande supplémentaires qui s’adressent à la gestion d’un périphérique StorSimple.|


## <a name="next-steps"></a>Étapes suivantes

Découvrez la [sécurité de StorSimple](storsimple-security.md).




 

 
