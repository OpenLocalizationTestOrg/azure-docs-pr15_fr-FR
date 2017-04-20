<properties
   pageTitle="Vue d’ensemble du tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit le tableau StorSimple virtuel, en une solution de stockage intégrée qui gère les tâches de stockage entre un périphérique virtuel en local et le stockage de cloud Microsoft Azure."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduction à la matrice virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans le tableau virtuel Microsoft Azure StorSimple, une solution de stockage intégrée qui gère les tâches de stockage entre un périphérique virtuel local en cours d’exécution dans un hyperviseur et du stockage cloud Microsoft Azure. Le tableau virtuel (également connu sous le StorSimple local périphérique virtuel) est un serveur de fichiers efficace, rentable et facilement gérables ou une solution de serveur iSCSI qui élimine la plupart des problèmes et dépenses associées entreprise stockage et protection des données. Le tableau virtuel est particulièrement bien adapté aux scénarios office remote/succursales (termes).

Cette présentation se concentre sur le tableau virtuel. 

- Pour une vue d’ensemble de la série 8000 StorSimple, accédez à [série 8000 StorSimple : une solution de cloud hybride](storsimple-overview.md). 

- Pour plus d’informations sur l’appareil série StorSimple 5000/7000, accédez à [L’aide en ligne StorSimple](http://onlinehelp.storsimple.com/).

Le tableau virtuel prend en charge l’iSCSI ou le protocole de serveur Message bloc (PME). Il s’exécute sur votre infrastructure hyperviseur existante et fournit la hiérarchisation cloud, sauvegarde cloud, restauration rapide, restauration au niveau de l’élément, les fonctions de récupération d’urgence.

Le tableau suivant récapitule les fonctionnalités importantes du tableau virtuel.

| Fonctionnalité | Tableau virtuel |
| ------- | ------------- |
|Configuration requise pour l’installation | Utilisation de l’infrastructure de virtualisation (Hyper-V ou VMware)|
| Disponibilité | Nœud unique |
| Capacité totale (y compris le cloud) |Jusqu'à 64 To de capacité utilisable par périphérique virtuel |
| Capacité locale | 390 Go à 6,4 To utilisables par périphérique virtuel (qu’il soit nécessaire de mise en service 500 Go à 8 To d’espace disque)|
| Protocoles natifs | iSCSI ou PME |
| Objectif de temps de récupération (RTO) | iSCSI : moins de 2 minutes quelle que soit la taille |
| Récupération objectifs de point) | Les sauvegardes quotidiennes et à la demande |
| Hiérarchisation du stockage | Utilisations thermique mappage afin de déterminer quelles sont les données doivent être hiérarchisées ou l’arrière |
| Prise en charge | Infrastructure de virtualisation pris en charge par le fournisseur |
| Performances | Varie en fonction de l’infrastructure sous-jacente |
| Mobilité des données | Pouvez restaurer sur le même appareil ou effectuez l’élément au niveau de récupération (serveur de fichiers) |
| Niveaux de stockage | Cloud et stockage hyperviseur local |
| Taille de partage |Hiérarchisé : jusqu'à 20 To ; localement épinglées : jusqu'à 2 To |
| Taille du volume |Hiérarchisé : jusqu'à 5 to ; localement épinglées : jusqu'à 500 Go |
| Instantanés | Blocage cohérente |
| Restauration au niveau de l’élément | Oui ; les utilisateurs peuvent restaurer à partir des partages |

## <a name="why-use-storsimple"></a>Pourquoi utiliser StorSimple ?

StorSimple se connecte aux utilisateurs et les serveurs au stockage Azure en quelques minutes, sans modifier son application.

Le tableau suivant décrit certains des principaux avantages qui fournit la solution tableau virtuel.

| Fonctionnalité | Avantage |
|---------|---------|
| Intégration transparente | Le tableau virtuel prend en charge l’iSCSI ou le protocole PME. Le déplacement des données entre le niveau local et le niveau de cloud est rationnel et transparent à l’utilisateur.|
| Coûts de stockage réduite | Avec StorSimple, vous devez configurer stockage local suffisant pour répondre aux exigences actuelles pour les données à chaud plus utilisées. Comme les besoins de stockage augmentent, stockage cloud StorSimple niveaux ces données en économique. Les données sont dédupliquées et compressées avant d’envoyer vers le cloud afin de réduire davantage les dépenses en matière de stockage.|
| Gestion du stockage simplifiée | StorSimple fournit une gestion centralisée dans le cloud à l’aide de StorSimple Manager pour gérer plusieurs appareils.| 
| Reprise amélioré et conformité | StorSimple facilite sinistre plus rapides en restaurant les métadonnées immédiatement et restaurer les données selon vos besoins. Cela signifie opérations normales continuez avec une interruption minimale.|
| Mobilité des données | Niveaux de données dans le cloud sont accessibles à partir d’autres sites à des fins de migration et de restauration. Notez que vous pouvez restaurer les données uniquement à la matrice virtuel d’origine. Toutefois, vous utilisez fonctions de récupération d’urgence pour restaurer l’intégralité du tableau virtuel à un autre tableau virtuel.|

## <a name="storsimple-workload-summary"></a>Charge de travail StorSimple résumé

Un résumé des charges de travail StorSimple pris en charge est sous forme de tableau en dessous.

| Scénario                | Charge de travail              | Prise en charge |  Restrictions                                  | Version              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| Collaboration de termes      | Partage de fichiers          | Oui       | Consultez [limites maximales pour serveur de fichiers](storsimple-ova-limits.md). <br>Voir [configuration système requise pour les versions PME prises en charge](storsimple-ova-system-requirements.md).   | Toutes les versions      |


## <a name="workflows"></a>Flux de travail

Le tableau virtuel StorSimple est particulièrement approprié pour le flux de travail suivants :

- [Gestion du stockage sur le nuage](#cloud-based-storage-management)
- [En fonction de l’emplacement de sauvegarde](#location-independent-backup)
- [Données protection et récupération d’urgence](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestion du stockage sur le nuage

Vous pouvez utiliser le service du Gestionnaire de StorSimple en cours d’exécution dans le portail classique Azure pour gérer les données stockées sur plusieurs appareils et à plusieurs endroits. Ceci est particulièrement utile dans les scénarios distribué branchement. Notez que vous devez créer des instances distinctes du service StorSimple Manager pour gérer les tableaux virtuels et périphériques StorSimple physiques. 

![gestion du stockage sur le nuage](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>En fonction de l’emplacement de sauvegarde

Avec le tableau virtuel, cloud instantanés fournissent une copie point-à-temps indépendantes de l’emplacement d’un volume ou le partage. Cloud instantanés sont activées par défaut et ne peut pas être désactivées. Tous les volumes et partages sont sauvegardez en même temps via une stratégie de sauvegarde quotidienne unique, et vous pouvez effectuer des sauvegardes ad hoc supplémentaires dès que nécessaire.

### <a name="data-protection-and-disaster-recovery"></a>Données protection et récupération d’urgence

Le tableau virtuel prend en charge les données protection et urgence récupération scénarios suivants :

- **Restaurer le volume ou partager** – utilisation la restauration en tant que nouveau flux de travail pour récupérer un volume ou le partage. Utilisez cette approche pour restaurer la totalité du volume ou le partage.
- **Restauration au niveau de l’élément** – partages autoriser un accès simplifié aux sauvegardes récentes. Vous pouvez facilement restauration d’un fichier à partir d’un dossier spécial .backup disponible dans le cloud. Cette fonctionnalité restaurer est pilotée par l’utilisateur et aucune intervention d’administration est requise.
- **Reprise** : utiliser la fonctionnalité de basculement pour récupérer tous les volumes ou partages vers un nouveau tableau virtuel. Vous créez le nouveau tableau virtuel et l’enregistrer avec le service Manager StorSimple, puis basculer le tableau virtuel d’origine. Le nouveau tableau virtuel puis suppose que les ressources mis en service. 

## <a name="virtual-array-components"></a>Composants de tableau virtuels

Le tableau virtuel inclut les éléments suivants :

- [Tableau virtuel](#virtual-array) , un dispositif de stockage cloud hybride basé sur une machine virtuelle sa mise en service dans votre environnement virtualisé ou hyperviseur.  
- [Service Manager StorSimple](#storsimple-manager-service) – une extension du portail classique Azure qui vous permet de gérer un ou plusieurs périphériques StorSimple à partir d’une interface web unique que vous pouvez accéder à partir de différents emplacements géographiques. Vous pouvez utiliser le service Manager StorSimple pour créer et gérer les services, afficher et gérer les appareils et les alertes et gérer des volumes, partages et des instantanés existants.
- [Interface utilisateur web local](#local-web-user-interface) – une interface utilisateur basée sur le web qui est utilisée pour configurer l’appareil afin qu’elle puisse se connecter au réseau local et puis enregistrez le périphérique avec le service Manager StorSimple. 
- [Interface de ligne de commande](#command-line-interface) – une interface Windows PowerShell que vous pouvez utiliser pour démarrer une session de prise en charge sur le tableau virtuel.
Les sections suivantes décrivent chacun de ces composants plus en détail et expliquent comment la solution organise les données, affecte le stockage et facilite la gestion du stockage et protection des données.

### <a name="virtual-array"></a>Tableau virtuel

Le tableau virtuel est une solution de stockage à un seul nœud qui offre un stockage principal, gère la communication avec stockage cloud et permet de garantir la sécurité et la confidentialité de toutes les données qui se trouve sur le périphérique.

Le tableau virtuel est disponible en un seul modèle est disponible au téléchargement. Le tableau de stockage a une capacité maximale de 6,4 To sur le périphérique (avec un espace de stockage sous-jacentes requis to 8) et y compris les 64 To stockage cloud. 

Le tableau virtuel contient les fonctionnalités suivantes :

- Il est rentable. Il fait utiliser de votre infrastructure de virtualisation existante et peut être déployé sur votre hyperviseur Hyper-V ou VMware existant.
- Il se trouve dans le centre de données et peut être configuré comme un serveur iSCSI ou un serveur de fichiers. 
- Il est intégré avec le cloud.
- Sauvegardes sont stockées dans le cloud, ce qui peut faciliter la récupération d’urgence et simplifier la récupération au niveau des éléments (ILR). 
- Vous pouvez appliquer des mises à jour dans le tableau virtuel, comme vous le feriez s’appliquent à un périphérique physique.

>[AZURE.NOTE] Un tableau virtuel ne peut pas être développé. Par conséquent, il est important de mise en service de stockage adéquat lorsque vous créez le périphérique virtuel. 

### <a name="storsimple-manager-service"></a>Service Manager StorSimple

Microsoft Azure StorSimple fournit une interface utilisateur basée sur le web (le service Manager StorSimple) qui vous permet de gérer le centre de données et de stockage en nuage de façon centralisée. Vous pouvez utiliser le service Manager StorSimple pour effectuer les tâches suivantes :

- Gérer plusieurs tableaux virtuels StorSimple à partir d’un service unique. 
- Configurer et gérer les paramètres de sécurité des appareils StorSimple. (Chiffrement dans le nuage est dépendant de Microsoft Azure API).
- Configurer les propriétés et les informations d’identification du compte de stockage.
- Configurer et gérer des volumes ou des partages.
- Sauvegarder et restaurer des données sur volumes ou des partages.
- Surveiller les performances.
- Revoir les paramètres système et d’identifier les éventuels problèmes.

Le service Manager StorSimple vous permet d’effectuer des tâches d’administration quotidiennes de votre tableau virtuel.

Pour plus d’informations, accédez à [utiliser le service Manager StorSimple pour administrer votre appareil StorSimple](storsimple-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface utilisateur web local

Le tableau virtuel inclut une interface utilisateur basée sur le web qui est utilisée pour une configuration unique et de l’inscription de l’appareil avec le service Manager StorSimple. Vous pouvez l’utiliser pour arrêter redémarrez le tableau virtuel, exécuter les tests de diagnostic et mettre à jour logiciel, modifier le mot de passe administrateur appareil, afficher les journaux système, contactez le Support Microsoft pour une demande de service. 

Pour plus d’informations sur l’utilisation de l’interface utilisateur basée sur le web, accédez à [utiliser l’interface utilisateur de basée sur le web pour administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de ligne de commande

L’interface Windows PowerShell inclus vous permet de lancer une session de prise en charge avec Support Microsoft afin qu’ils peuvent vous aider à résoudre les problèmes et résoudre les problèmes que vous pouvez rencontrer sur votre appareil virtuel.

## <a name="storage-management-technologies"></a>Technologies de gestion de stockage

Outre la matrice virtuel et autres composants, la solution StorSimple utilise les technologies de logiciel suivantes permettent d’accéder rapidement à des données importantes, réduire la consommation de stockage et protéger les données stockées dans votre tableau virtuel :

- [Hiérarchisation du stockage automatique](#automatic-storage-tiering) 
- [Partages localement épinglés et des volumes](#locally-pinned-shares-and-volumes)
- [Déduplication et la compression des données hiérarchisé ou sauvegardés dans le cloud](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Sauvegardes planifiées et à la demande](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Hiérarchisation du stockage automatique

Le tableau virtuel utilise un nouveau mécanisme hiérarchisation pour gérer les données stockées dans le tableau virtuel et le cloud. Il existe deux niveaux : le tableau virtuel et Azure local de stockage cloud. Le tableau virtuel StorSimple Réorganise automatiquement les données dans les couches basés sur une carte thermique, qui effectue le suivi de l’utilisation actuelle, l’âge et relations à d’autres données. Données plus actives (plus) sont stockées localement, tandis que moins des données actives et inactives sont automatiquement migrées vers le cloud. (Toutes les sauvegardes sont stockées dans le cloud). StorSimple ajuste et réorganise les données et modifier les affectations de stockage en tant que modèles d’utilisation. Par exemple, certaines informations peuvent devenir moins actifs au fil du temps. Dès qu’elles seront progressivement moins actif, il est hiérarchisé arrière dans le cloud. Si ces mêmes données redevient actives, il est en cascade dans au groupe de stockage.

Données d’un partage hiérarchisé particulier ou le volume sont assurées son propre espace niveau local. (environ 10 % de l’espace total généré de ce partage ou de volume). Alors que cela permet de réduire la capacité de stockage sur le périphérique virtuel qui partage ou du volume, elle garantit que hiérarchisation pour un partage ou le volume de ne pas être affectée par besoins hiérarchisation des autres partages ou volumes. Par conséquent la charge de travail très occupé (e) sur un partage ou un volume ne peut pas forcer toutes les autres charges de travail dans le cloud. 

![hiérarchisation du stockage automatique](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Vous pouvez spécifier un volume épinglé localement, auquel cas les données demeurent sur le tableau virtuel et ne sont jamais hiérarchisé vers le cloud. Pour plus d’informations, accédez à [localement épinglées partages et des volumes](#locally-pinned-shares-and-volumes).

### <a name="locally-pinned-shares-and-volumes"></a>Partages localement épinglés et des volumes

Vous pouvez créer des volumes épinglées localement et les partages appropriés. Cette fonctionnalité garantit que les données requises par les applications critiques est conservé dans le tableau virtuel et ne sont jamais hiérarchisées vers le cloud. Partages localement épinglés et des volumes présentent les fonctionnalités suivantes : 

- Ils ne sont pas soumis à latence cloud ou problèmes de connectivité.
- Ils encore bénéficient de fonctionnalités de récupération des sauvegarde et de StorSimple cloud.

Vous pouvez restaurer un partage de localement épinglé ou volume comme hiérarchisé ou hiérarchisé ou épinglées volume localement. 

Pour plus d’informations sur les volumes localement épinglés, accédez à [utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes-u2.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Déduplication et la compression des données hiérarchisé ou sauvegardés dans le cloud

StorSimple utilise la compression déduplication et les données afin de réduire davantage les besoins de stockage dans le cloud. Déduplication permettant de réduire le montant total des données stockées en supprimant redondantes le jeu de données stocké. Comme informations changent, StorSimple ignore les données ne change pas et capture uniquement les modifications. En outre, StorSimple permet de réduire la quantité de données stockées en identifiant et en supprimant des informations en double. 

>[AZURE.NOTE] Données stockées dans le tableau virtuel ne sont pas dédupliquées ou compressées. Tous les déduplication et compression se produit juste avant que les données sont envoyées dans le cloud.

### <a name="scheduled-and-on-demand-backups"></a>Sauvegardes planifiées et à la demande

Fonctionnalités de protection des données StorSimple permettent de créer des sauvegardes à la demande. En outre, un planning de sauvegarde par défaut garantit que les données sont sauvegardées quotidiennement. Les sauvegardes sont effectuées sous la forme d’instantanés incrémentiels, qui sont stockées dans le cloud. Des instantanés, qui enregistrent uniquement les modifications apportées depuis la dernière sauvegarde, peuvent être créées et restaurées rapidement. Ces instantanés peuvent être essentiel dans récupération d’urgence scénarios, car ils remplacement les systèmes de stockage secondaire (par exemple, de la bande de sauvegarde) et vous permettent pas de restaurer les données dans votre centre de données ou sur d’autres sites si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [préparer le portail de tableau virtuel](storsimple-ova-deploy1-portal-prep.md).


