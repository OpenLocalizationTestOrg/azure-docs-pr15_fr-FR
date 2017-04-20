<properties 
   pageTitle="Meilleures pratiques concernant les tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les meilleures pratiques pour le déploiement et la gestion de la matrice virtuel StorSimple."
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
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Tableau virtuel StorSimple meilleures pratiques

## <a name="overview"></a>Vue d’ensemble

Tableau virtuel Microsoft Azure StorSimple est une solution de stockage intégrée qui gère les tâches de stockage entre un périphérique virtuel local en cours d’exécution dans un hyperviseur et du stockage cloud Microsoft Azure. Tableau virtuel StorSimple est une alternative efficace et économique, à la matrice physique 8000 série. Le tableau virtuel peut s’exécuter sur votre infrastructure hyperviseur existante, prend en charge les protocoles PME et iSCSI et est adapté à des scénarios office/succursales à distance. Pour plus d’informations sur les solutions StorSimple, accédez à [Vue d’ensemble de Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Cet article décrit les meilleures pratiques implémentés pendant la configuration initiale, le déploiement et la gestion du tableau virtuel StorSimple. Les meilleures pratiques suivantes fournissent des instructions validées pour la configuration et la gestion de votre tableau virtuel. Cet article est ciblé sur les administrateurs informatiques qui déploiement et gérer les tableaux virtuels dans leurs centres de données.

Nous vous recommandons de consulter les meilleures pratiques pour vérifier que votre périphérique est toujours en conformité lorsque des modifications sont apportées au flux le programme d’installation ou d’une opération de périodiquement. Si vous rencontrez des problèmes lors de la mise en œuvre les meilleures pratiques suivantes sur votre tableau virtuel, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance.

## <a name="configuration-best-practices"></a>Meilleures pratiques de configuration 

Les meilleures pratiques suivantes traitent les instructions qui doivent suivre au cours de la configuration initiale et le déploiement des matrices virtuels. Les meilleures pratiques suivantes incluent ceux liés à la mise en service de la machine virtuelle, les paramètres de stratégie de groupe, de redimensionnement, la configuration de la mise en réseau, la configuration des comptes de stockage et la création partages et des volumes pour le tableau virtuel. 

### <a name="provisioning"></a>Mise en service 

Tableau virtuel StorSimple est une machine virtuelle () sa mise en service sur l’hyperviseur (Hyper-V ou VMware) de votre serveur hôte. Lors de la configuration de la machine virtuelle, assurez-vous que votre hôte est en mesure de dédier suffisamment de ressources. Pour plus d’informations, accédez aux [besoins en ressources minimale](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) de mise en service un tableau. 

Mettre en œuvre les meilleures pratiques suivantes lors de la configuration de la matrice virtuelle :


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Type de machine virtuelle**   | **Génération 2** Machine virtuelle pour l’utilisation avec Windows Server 2012 ou version ultérieure et une image *.vhdx* . <br></br> **Génération 1** Machine virtuelle pour l’utilisation avec un Windows Server 2008 ou version ultérieure et une image *.vhd* .                                                                                                              | Utiliser machine virtuelle version 8-11 *.vmdk* image.                                                                      |
| **Type de mémoire**            | Configurer en tant que **mémoire statique**. <br></br> N’utilisez pas l’option de **mémoire dynamique** .            |                                                    |
| **Type de données de disque**         | Mise en service comme **taille dynamique**.<br></br> **Taille fixe** prend beaucoup de temps. <br></br> N’utilisez pas l’option **comparaison** .                                                                                                                   | Utilisez l’option de **mise en service fine** .                                                                                      |
| **Modification des données disque** | Développement ou réduction n’est pas autorisée. Une tentative de le faire entraîne la perte de toutes les données locales sur appareil.                       | Développement ou réduction n’est pas autorisée. Une tentative de le faire entraîne la perte de toutes les données locales sur appareil. |

### <a name="sizing"></a>Redimensionnement

Lorsque votre tableau virtuel StorSimple de redimensionnement, tenez compte des facteurs suivants :

- Réservation locale pour les volumes ou partages. Environ 12 % de l’espace est réservée au niveau local pour chaque volume hiérarchisé généré ou partager. Environ 10 % de l’espace est également réservé pour un volume épinglé localement pour le système de fichiers.
- Instantané de surcharge. Environ 15 % d’espace sur la couche locale est réservé aux instantanés.
- Vous avez besoin pour restaure. Si cette restaurer comme une nouvelle opération, redimensionnement doit tenir compte de l’espace nécessaire pour restaurer. Restaurer est supérieure ou terminé pour un partage ou un volume de la même taille.
- Une mémoire tampon doit être réparti pour toute croissance inattendue.

Selon les facteurs précédentes, la configuration requise de redimensionnement peut être représentée par l’équation suivante :

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Les exemples suivants illustrent comment vous pouvez redimensionner un tableau virtuel selon vos besoins.

#### <a name="example-1"></a>Exemple 1 :
Sur votre tableau virtuel, vous voulez être en mesure de 

- mise en service un 2 To volume hiérarchisé ou partager.
- mise en service un 1 to volume hiérarchisé ou partager.
- mise en service un 300 Go du volume localement épinglé ou partager.


Pour les volumes ou partages précédent, laissez-nous calculer l’espace disque au niveau local. 

Tout d’abord, pour chaque volume hiérarchisé/partager, réservation locale est égale à 12 % de la taille du volume/partager. Pour le volume localement épinglé/partage, réservation locale est de 10 % de la taille du volume/partage localement épinglé (en plus de la taille générée). Dans cet exemple, vous devez

- Réservation local 240 Go (pour un 2 To hiérarchisé volume/partager)
- Réservation local 120 Go (pour un 1 to hiérarchisé volume/partager)
- 330 Go pour volume localement épinglé ou partage (en ajoutant 10 % de réservation locale à la taille de 300 Go sa mise en service)

L’espace total requis jusqu’au niveau local est : 240 + 120 Go + 330 = 690 go.

Ensuite, nous devons au moins autant d’espace sur la couche locale en tant que la plus grande réservation unique. Ce montant supplémentaire est utilisé au cas où vous devriez restaurer à partir d’un instantané de cloud. Dans cet exemple, la plus grande réservation locale est 330 Go (y compris réservation pour le système de fichiers), afin que vous ajouterez à la Go : 690 690 + 330 Go = 1020 go.
Si nous avons effectué restaure supplémentaires suivants, nous pouvons toujours libérer de l’espace à partir de l’opération de restauration précédente.

Enfin, nous avons besoin de 15 % de votre espace local total jusqu'à présent pour stocker les instantanés locales, afin que seuls 85 % d'entre elles sont disponible. Dans cet exemple, qui serait d’environ 1020 Go = 0.85&ast;données générées disque to. Par conséquent, le disque de données généré serait (1020&ast;(1/0,85)) = 1 200 Go = 1,20 To ~ 1,25 To (arrondi à quartile le plus proche)

En tenant compte de croissance inattendue et restaure nouveau, vous devez doit configurer un disque local du autour 1,25-1,5 To.

> [AZURE.NOTE] Nous vous recommandons également que le disque local est légèrement mis en service. Cette recommandation est, car l’espace restaurer est nécessaire uniquement lorsque vous souhaitez restaurer les données plues de cinq jours. Restauration au niveau de l’élément vous permet de restaurer des données pour les cinq derniers jours sans avoir besoin de l’espace supplémentaire pour la restauration.

#### <a name="example-2"></a>Exemple 2 : 
Sur votre tableau virtuel, vous voulez être en mesure de 

- mise en service un 2 To hiérarchisé en volume
- prévoir un volume 300 Go épinglée localement

En fonction de 12 % de réservation d’espace local pour les volumes/partages hiérarchisés et 10 % pour les volumes/partages localement épinglés, nous avons besoin de

- Réservation local 240 Go (2 To hiérarchisé volume/partager)
- 330 Go pour volume localement épinglé ou partage (en ajoutant 10 % de réservation locale vers l’espace de 300 Go sa mise en service)

Espace total requis au niveau local est : 240 Go + 330 = 570 Go

Espace local minimal requis pour la restauration est 330 go. 

15 % de votre disque total est utilisé pour stocker des instantanés de sorte que seuls 0,85 est disponible. Par conséquent, la taille du disque est (900&ast;(1/0,85)) = to 1,06 ~ 1,25 To (arrondi à quartile le plus proche) 

En tenant compte de toute croissance inattendue, vous pouvez configurer un disque local 1,25-1,5 To.


### <a name="group-policy"></a>Stratégie de groupe

Stratégie de groupe est une infrastructure qui vous permet d’implémenter des configurations spécifiques pour les utilisateurs et ordinateurs. Paramètres de stratégie de groupe sont contenus dans les objets de stratégie de groupe (stratégie de groupe), qui sont liées aux conteneurs de Services de domaine Active Directory (AD DS) suivants : sites, des domaines ou des unités organisationnelles (ou). 

Si votre tableau virtuel est joint au domaine, stratégie de groupe peut être appliqués à celui-ci. Ces objets de stratégie de groupe peuvent installer des applications comme un logiciel antivirus qui peuvent avoir un impact sur le fonctionnement de la matrice virtuel StorSimple.

Par conséquent, il est recommandé que vous :

-   Vérifiez que votre tableau virtuel est dans sa propre unité d’organisation (OU) pour Active Directory. 

-   Assurez-vous qu’aucun objet de stratégie de groupe (stratégie de groupe) n’est appliquées à votre tableau virtuel. Vous pouvez bloquer l’héritage pour vous assurer que le tableau virtuel (nœud enfant) n’hérite pas automatiquement les objets de stratégie du parent. Pour plus d’informations, accédez à [Bloquer l’héritage](https://technet.microsoft.com/library/cc731076.aspx).


### <a name="networking"></a>Mise en réseau

La configuration du réseau de votre tableau virtuel s’effectue via l’interface utilisateur web local. Une interface de réseau virtuel est activée via l’hyperviseur dans laquelle le tableau virtuel est mis en service. Utilisez la page [Paramètres du réseau](storsimple-ova-deploy3-fs-setup.md) pour configurer l’adresse IP de l’interface réseau virtuel, sous-réseau et passerelle.  Vous pouvez également configurer les serveurs DNS principal et secondaire, paramètres de temps et les paramètres proxy facultatif pour votre appareil. La plupart de la configuration réseau sont une configuration unique. Passez en revue les [besoins réseau StorSimple](storsimple-ova-system-requirements.md#networking-requirements) avant de déployer la matrice virtuelle.

Lorsque vous déployez votre tableau virtuel, nous vous recommandons de suivre les meilleures pratiques suivantes :

-   Vérifiez que le réseau dans lequel la matrice virtuelle est déployée toujours la capacité à dédier 5 la bande passante Mbps Internet (ou plus). 

    -   Vous avez besoin de la bande passante Internet varie selon les caractéristiques de la charge de travail et le taux de modification des données.

    -   La modification de données qui peut être résolue est directement proportionnelle à votre bande passante Internet. Par exemple lorsque vous prenez une sauvegarde, une bande passante Mbps 5 peut accueillir une modification de données d’environ 18 Go en 8 heures. Avec quatre fois plus de bande passante (20 Mbps), vous pouvez gérer quatre fois plus modification de données (72 Go). 

-   Vérifiez que la connectivité à Internet est toujours disponible. Sources non fiables ou interruptions connexions Internet pour les appareils, peuvent entraîner une perte d’accès aux données dans le cloud et peuvent entraîner une configuration non prises en charge.

-   Si vous envisagez de déployer votre périphérique en tant qu’un serveur iSCSI : 
    -   Nous vous recommandons de désactiver l’option **automatiquement une adresse IP obtenir** (DHCP). 
    -   Configurer des adresses IP statiques. Vous devez configurer un serveur principal et un serveur DNS secondaire.

    -   Si la définition de plusieurs interfaces réseau sur votre serveur virtuel de tableau, la première interface réseau (par défaut, cette interface est **Ethernet**) communiquer avec le cloud. Pour contrôler le type de trafic, vous pouvez créer plusieurs interfaces réseau virtuel sur votre tableau virtuel (configuré comme un serveur iSCSI) et connecter ces interfaces à différents sous-réseaux.

-   Pour limiter la bande passante cloud uniquement (utilisé par le tableau virtuel), configurez la limitation sur le routeur ou le pare-feu. Si vous définissez la limitation dans votre hyperviseur, il sera limiter tous les protocoles, y compris iSCSI et PME au lieu de simplement la bande passante cloud. 

-   Assurez-vous que la synchronisation avec temps de hyperviseurs sont activée. Si à l’aide de Hyper-V, sélectionnez votre tableau virtuel dans le Gestionnaire Hyper-V, accédez à **paramètres &gt; Integration Services**et vérifiez que la **synchronisation de l’heure** est cochée.

### <a name="storage-accounts"></a>Comptes de stockage

Tableau virtuel StorSimple peuvent être associé à un compte de stockage unique. Ce compte de stockage peut être un compte de stockage généré automatiquement, un compte dans le même abonnement en tant que le service, ou un compte de stockage liés à un autre abonnement. Pour plus d’informations, voir comment [Gérer les comptes de stockage pour votre tableau virtuel](storsimple-ova-manage-storage-accounts.md).

Utilisez les recommandations suivantes pour les comptes de stockage associées à votre tableau virtuel.

-   Lorsque vous liez plusieurs tableaux virtuels avec un compte de stockage unique, prendre en compte la capacité maximale (64 To) d’un tableau virtuel et la taille maximale (500 To) pour un compte de stockage. Cela limite le nombre de tableaux virtuels plein qui peuvent être associés à ce compte de stockage à environ 7.

-   Lorsque vous créez un nouveau compte de stockage
    -   Nous vous recommandons de créer il dans la région du bureau distant/succursale où votre tableau virtuel StorSimple est déployé pour réduire la latence le plus proche.

    -   N’oubliez pas que vous ne pouvez pas déplacer vers un compte de stockage pour différentes régions. Vous ne pouvez pas également déplacer un service abonnements.

    -   Utilisez un compte de stockage qui mettent en œuvre, redondance entre les centres de données. Stockage geo redondants (GRS), Zone redondants stockage (ZRS) et stockage localement redondantes (LRS) sont toutes prises en charge pour une utilisation avec votre tableau virtuel. Pour plus d’informations sur les différents types de comptes de stockage, accédez à [la réplication du stockage Azure](../storage/storage-redundancy.md).


### <a name="shares-and-volumes"></a>Partages et des volumes

Pour votre tableau virtuel StorSimple, vous pouvez prévoir partages lorsqu’il est configuré comme un serveur de fichiers et les volumes lorsqu’il est configuré comme un serveur iSCSI. Les meilleures pratiques pour la création de partages et des volumes sont liées à la taille et le type de configuré.

#### <a name="volumeshare-size"></a>Taille de volume/partager

Dans votre tableau virtuel, vous pouvez prévoir partages lorsqu’il est configuré comme un serveur de fichiers et les volumes lorsqu’il est configuré comme un serveur iSCSI. Les meilleures pratiques pour la création de partages et des volumes sont liés à la taille et le type de configuré. 

Gardez à l’esprit les recommandations suivantes lors de la configuration des partages ou volumes sur votre appareil virtuel.

-   La taille des fichiers par rapport à la taille d’un partage hiérarchisé générée peut affecter les performances de hiérarchisation. Utilisation des fichiers volumineux peut entraîner un niveau lent arrière. Lorsque vous travaillez avec des fichiers volumineux, nous vous recommandons de que le fichier plus grand est inférieur à 3 % de la taille de partage.

-   Un maximum de 16 volumes/partages peut être créé dans le tableau virtuel. Si localement épinglé, les volumes/partages peut être entre 50 Go et 2 To. Si plusieurs niveaux, les volumes/partages doit être comprise entre 500 Go et 20 To. 

-   Lorsque vous créez un volume, facteur dans la consommation de données attendues ainsi que la croissance future. Tandis que le volume ne peut pas être développé plus tard, vous pouvez toujours restaurer vers un volume plus important.

-   Une fois que le volume a été créé, vous ne pouvez pas diminuer la taille du volume sur StorSimple.
   
-   Lors de l’écriture vers un volume hiérarchisé sur StorSimple, lorsque les données du volume atteint un certain seuil (par rapport à l’espace local réservé pour le volume), la IO est limitée. Continuent à écrire sur ce volume ralentit considérablement vers le bas la IO. Bien que vous disposez d’un volume hiérarchisé au-delà de sa capacité généré (nous n’activement arrêtez pas l’utilisateur à partir de l’écriture au-delà de la capacité générée), vous voyez une notification d’alerte pour l’effet que vous avez expérimente beaucoup. Une fois que vous voyez l’alerte, vous devez impérativement prendre des mesures de réparation telles que supprimer les données du volume ou de restaurer le volume vers un volume plus important (extension du volume est actuellement pas pris en charge).

-   Pour des exemples d’utilisation de la récupération après sinistre, comme le nombre de partages/volumes autorisées est 16 et le nombre maximal de partages/volumes qui peut être traité en parallèle est également 16, le nombre de partages/volumes n’a pas une incidence sur vos RPO et RTO. 

#### <a name="volumeshare-type"></a>Type de volume/partage

StorSimple prend en charge les deux types de volume/partage selon leur utilisation : localement épinglés et hiérarchisé. Volumes/partages localement épinglés sont thickly sa mise en service alors que les volumes/partages hiérarchisés sont légèrement sa mise en service. 

Nous vous recommandons de mettre en œuvre les meilleures pratiques suivantes lorsque vous configurez StorSimple volumes/partages :

-   Identifier le type de volume basé sur les charges de travail que vous voulez déployer avant de créer un volume. Utiliser des volumes localement épinglés pour charges de travail qui nécessitent garanties locales de données (même pendant une panne cloud) et qui exigent la latence faible cloud. Une fois que vous créez un volume de votre tableau virtuel, vous ne pouvez pas modifier le type de volume de localement par épinglées à plusieurs niveaux ou *vice versa*. Par exemple, créer des volumes localement épinglés lors du déploiement de charges de travail SQL ou charges de travail d’hébergement machines virtuelles (machines virtuelles) ; utiliser des volumes hiérarchisés pour charges de travail de partage de fichier.

-   Cochez l’option des données archivage les moins fréquemment utilisées lorsque vous travaillez avec des fichiers de grande taille. Une plus grande taille de segment déduplication de 512 Ko est utilisée lorsque cette option est activée afin d’accélérer le transfert de données dans le cloud.

#### <a name="volume-format"></a>Format de volume

Après avoir créé StorSimple volumes sur votre serveur iSCSI, vous devez initialisation, monter et mettre en forme les volumes. Cette opération est exécutée sur l’hôte connecté à votre appareil StorSimple. Meilleures pratiques suivantes sont recommandés lorsque montage et mise en forme des volumes sur l’hôte StorSimple.

-   Effectuer une mise en forme rapide sur tous les volumes StorSimple.

-   Lors de la mise en forme un volume StorSimple, utilisez une taille d’unité d’allocation (Australie) de 64 Ko (valeur par défaut est 4 Ko). Les 64 Ko Australie est basé sur un test effectué en interne pour les charges de travail StorSimple courantes et autres charges de travail.

-   Lorsque vous utilisez le tableau virtuel StorSimple configuré comme un serveur iSCSI, n’utilisez pas de volumes fractionnés ou disques dynamiques en tant que ces volumes ou disques ne sont pas pris en charge par StorSimple.

#### <a name="share-access"></a>Accès au partage

Lorsque vous créez des actions sur votre serveur de fichiers tableau virtuel, suivez ces instructions :

-   Lorsque vous créez un partage, attribuer un groupe d’utilisateurs en tant qu’un administrateur de partage au lieu d’un seul utilisateur.

-   Vous pouvez gérer les autorisations une fois que le partage est créé en modifiant les partages via l’Explorateur Windows.

#### <a name="volume-access"></a>Accès aux volumes

Lorsque vous configurez les volumes iSCSI à votre tableau virtuel StorSimple, il est important de contrôler l’accès à l’endroit où nécessaires. Pour déterminer quels sont les serveurs hôte peuvent accéder aux volumes, créer et associer des enregistrements de contrôle d’accès (ACRs) StorSimple volumes.

Utilisez les meilleures pratiques suivantes lorsque vous configurez ACRs pour les volumes StorSimple :

-   Toujours associer au moins un blocage à un volume.

-   Définir plusieurs ACRs uniquement dans un environnement groupé.

-   Lorsque vous affectez plusieurs blocage à un volume, assurez-vous que le volume n’est pas exposé de façon à l’endroit où il est accessible simultanément par plusieurs hôtes non groupé. Si vous avez attribué plusieurs ACRs à un volume, un message d’avertissement s’affiche pour vous permettre de consulter votre configuration.

### <a name="data-security-and-encryption"></a>Le chiffrement et la sécurité des données

Votre tableau virtuel StorSimple contient des fonctionnalités de sécurité et de chiffrement données garantissant la confidentialité et l’intégrité de vos données. Lorsque vous utilisez ces fonctionnalités, il est recommandé de suivre les meilleures pratiques suivantes : 

-   Définissez une clé de chiffrement de stockage cloud pour générer le chiffrement AES-256 avant que les données sont envoyées à partir de votre tableau virtuel dans le cloud. Cette clé n’est pas obligatoire si vos données sont chiffrées avec laquelle commencer. La clé de générer et protégée à l’aide d’un système de gestion de clés tels que [l’archivage sécurisé clé Azure](../key-vault/key-vault-whatis.md).

-   Lorsque vous configurez le compte de stockage via le service Manager StorSimple, assurez-vous que vous activez le mode SSL créer un canal sécurisé pour les communications réseau entre votre périphérique StorSimple et le cloud.

-   Régénérer les clés pour vos comptes de stockage (en accédant au service de stockage Azure) régulièrement au compte les modifications apportées à accéder en fonction de la liste des administrateurs modifiée.

-   Données dans votre tableau virtuel sont compressées et dédupliquées avant d’être envoyé vers Azure. Nous ne recommandons d’utiliser le service de rôle déduplication des données sur votre hôte Windows Server.


## <a name="operational-best-practices"></a>Meilleures pratiques

Les meilleures pratiques sont des recommandations qui doivent être suivies pendant l’opération de la matrice virtuelle ou gestion quotidienne. Les pratiques suivantes traitent des tâches de gestion spécifiques tels que des sauvegardes, restauration à partir d’un jeu de sauvegarde, effectuer un basculement, désactiver et supprimer le tableau, analyse du fonctionnement et l’utilisation du système et en exécutant des virus analysent sur votre tableau virtuel.

### <a name="backups"></a>Effectuer des sauvegardes régulières

Les données de votre tableau virtuel sont sauvegardées dans le cloud de deux façons, par défaut automatisé sauvegarde quotidienne du périphérique entier commençant au 22:30 ou via une sauvegarde manuelle à la demande. Par défaut, le périphérique crée automatiquement des instantanés cloud quotidienne de toutes les données résidant dessus. Pour plus d’informations, accédez à [sauvegarder votre tableau virtuel StorSimple](storsimple-ova-backup.md).

Impossible de modifier la fréquence et la rétention associé aux sauvegardes par défaut, mais vous pouvez configurer l’heure à laquelle les sauvegardes quotidiennes sont lancées quotidiennement. Lorsque vous configurez l’heure de début pour les sauvegardes automatisées, nous vous recommandons :

-   Planifier vos sauvegardes en dehors des heures. Heure de début de la sauvegarde ne doit pas correspondent par hôte de nombreuses IO.

-   Démarrer une sauvegarde manuelle à la demande lors de la planification effectuer un appareil basculement ou avant à la fenêtre de maintenance, de protéger les données de votre tableau virtuel.

### <a name="restore"></a>Restaurer

Vous pouvez restaurer à partir d’un jeu de deux façons de sauvegarde : restaurer vers un autre volume ou partager ou effectuer une récupération au niveau des éléments (disponible uniquement dans un tableau virtuel configuré comme un serveur de fichiers). Restauration au niveau de l’élément permet d’effectuer une récupération granulaire des fichiers et dossiers à partir d’une sauvegarde de cloud de toutes les actions sur le périphérique StorSimple. Pour plus d’informations, accédez à [restaurer à partir d’une sauvegarde](storsimple-ova-restore.md).

Lorsque vous effectuez une restauration, n’oubliez pas les instructions suivantes :

-   Votre tableau virtuel StorSimple ne reconnaît pas les restaurer sur place. Il peut s’agir toutefois facilement obtenus par un processus en deux étapes : libérer de l’espace sur le serveur virtuel de tableau, puis restaurez vers un autre volume/partage.

-   Lors de la restauration à partir d’un volume local, n’oubliez pas la restauration sera une opération longue. Bien que le volume ne soient rapidement en ligne, les données continuent à être hydraté en arrière-plan.

-   Le type de volume reste la même pendant le processus de restauration. Un volume hiérarchisé est restauré vers un autre volume hiérarchisé et un volume localement épinglé à l’autre localement épinglées en volume.

-   Lorsque vous essayez de restaurer un volume ou un partage à partir d’une sauvegarde définir, le cas d’échec de la tâche de restauration, un volume cible ou partager peut-être toujours être créé dans le portail. Il est important que vous pouvez supprimer ce volume cible inutilisées ou partager dans le portail pour réduire les éventuels problèmes futurs découlant de cet élément.

### <a name="failover-and-disaster-recovery"></a>Basculement et récupération d’urgence

Un basculement de périphérique permet de migrer vos données à partir d’un appareil *source* dans le centre de données vers un autre périphérique *cible* situé dans le même ou un autre emplacement géographique. Le basculement d’appareil est pour l’ensemble de l’unité. Pendant le basculement, les données de cloud pour le périphérique source changent la propriété à celle de l’équipement cible.

Pour votre tableau virtuel StorSimple, vous pouvez uniquement basculer vers à un autre tableau virtuel géré par le service Manager StorSimple même. Basculement vers un périphérique 8000 série ou un tableau géré par un autre service Manager StorSimple (celui du périphérique source) n’est pas autorisé. Pour en savoir plus sur les considérations relatives à basculement, accédez à la [Configuration requise pour le basculement d’appareil](storsimple-ova-failover-dr.md).

Lorsque vous effectuez un fail sur pour votre tableau virtuel, gardez les éléments suivants à l’esprit :

-   Pour un basculement planifié, il est recommandé de prendre tous les volumes/partages en mode hors connexion avant de lancer le basculement. Suivez les instructions propres à un système d’exploitation pour prendre les volumes/partages en mode hors connexion sur l’hôte du premier, puis d’exécuter ceux en mode hors connexion sur votre appareil virtuel.

-   Pour une fichier de serveur reprise (DR), nous vous recommandons de rejoindre le périphérique cible dans le même domaine comme source afin que les autorisations de partage sont automatiquement résolues. Uniquement le basculement d’un équipement cible dans le même domaine est pris en charge dans cette version.

-   Une fois que le DR a bien été effectuée, le périphérique source est automatiquement supprimé. Bien que l’appareil n’est plus disponible, la machine virtuelle que vous avez configuré sur le système hôte utilise toujours des ressources. Nous vous recommandons de supprimer cette machine virtuelle à partir de votre système hôte pour empêcher les frais de plus alloués.

-   Notez que même si le basculement échoue, **les données sont toujours dans le cloud**. Considérez les trois scénarios suivants dans laquelle le basculement n’a pas réussi :

    -   Une erreur s’est produite dans les étapes initiales du basculement tels que lorsque les vérifications préalables DR sont en cours d’exécution. Dans ce cas, votre périphérique cible est toujours utilisable. Vous pouvez réessayer le basculement sur le même appareil cible.

    -   Une erreur est survenue pendant le processus de basculement réel. Dans ce cas, le périphérique cible est marqué inutilisable. Vous devez mettre en service et configurer un autre tableau virtuel cible et utiliser que pour le basculement.

    -   Le basculement a été achevé après quoi le périphérique source a été supprimé mais l’équipement présente des problèmes et vous ne pouvez pas accéder à des données. Les données sont toujours approuvées dans le cloud et peuvent être récupérées facilement en créant un autre tableau virtuel et puis à l’utiliser comme un périphérique cible pour le DR.

### <a name="deactivate"></a>Désactiver

Lorsque vous désactivez un tableau virtuel StorSimple, vous la connexion entre l’appareil et le service correspondant StorSimple Gestionnaire de serveur. Désactivation est une opération **définitive** et ne peut pas être annulée. Un périphérique désactivé ne peut pas être enregistré par le service Manager StorSimple à nouveau. Pour plus d’informations, accédez à [désactiver et supprimer votre tableau virtuel StorSimple](storsimple-deactivate-and-delete-device.md).

Gardez les meilleures pratiques suivantes à l’esprit lors de la désactivation de votre tableau virtuel :

-   Prendre un instantané de cloud de toutes les données avant la désactivation d’un périphérique virtuel. Lorsque vous désactivez un tableau virtuel, toutes les données de périphérique local est perdue. Prendre un instantané de cloud vous permettra de récupérer les données ultérieurement.

-   Avant de désactiver un tableau virtuel StorSimple, veillez à arrêter ou supprimer des clients et des hôtes qui dépendent de votre appareil.

-   Supprimer un appareil désactivé si vous n’utilisez plus afin qu’il ne méthode d’allocation des frais.

### <a name="monitoring"></a>Surveillance des mots clés

Pour vous assurer que votre tableau virtuel StorSimple se trouve dans un état correct en continu, vous devez surveiller le groupe et vérifiez que vous recevez des informations à partir du système, y compris les alertes. Pour contrôler la santé générale du tableau virtuel, mettre en œuvre les meilleures pratiques suivantes :

- Configurer l’analyse pour effectuer le suivi de l’espace disque de votre disque de données de tableau virtuel ainsi que le disque du système d’exploitation. Si Hyper-V en cours d’exécution, vous pouvez utiliser une combinaison de System Center Machine virtuelle Manager (SCVMM) et System Center Operations Manager (SCOM) pour contrôler vos hôtes virtualisation.   

- Configurer les notifications par courrier électronique sur votre tableau virtuel pour envoyer des alertes à certains niveaux d’utilisation.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Applications d’analyse antivirus et recherche d’Index

Un tableau virtuel StorSimple pouvez hiérarchiser automatiquement des données à partir du niveau local dans le cloud Microsoft Azure. Lorsqu’une application telle qu’une recherche d’index ou une analyse antivirus est utilisée pour analyser les données stockées sur StorSimple, vous devez prendre en charge que les données de nuage ne pas obtenir accessible et extrait revenir au niveau local.

Nous vous recommandons de mettre en œuvre les meilleures pratiques suivantes lorsque vous configurez l’analyse de recherche ou un virus index à votre tableau virtuel :

-   Désactiver les opérations d’analyse complète configuré automatiquement.

-   Pour les volumes hiérarchisés, configurez l’application index recherche ou un virus analyse pour effectuer une analyse incrémentielle. Cette recherche uniquement les nouvelles données susceptibles de résidant au niveau local. Les données hiérarchisées vers le cloud ne sont pas accessible lors d’une opération incrémentielle.

-   Vérifiez les paramètres et les filtres de recherche correctes sont configurées afin que seuls les initial types de fichiers sont analysés. Par exemple, les fichiers (GIF, JPEG et TIFF) image et dessins techniques ne doivent pas être analysés pendant la reconstruction d’index incrémentiel ou complet.

Si vous utilisez Windows le processus d’indexation, suivez ces instructions :

-   N’utilisez pas l’indexeur Windows pour les volumes hiérarchisés car il rappels de grandes quantités de données (To) à partir du cloud si l’index doit être reconstruit fréquemment. Reconstruction de l’index permet de récupérer tous les types de fichiers pour leur contenu d’index.

-   Utiliser les fenêtres de processus pour les volumes localement épinglés d’indexation comme ce serait accéder aux données uniquement sur les couches locales pour générer l’index (les données de nuage ne seront pas accessible).

### <a name="byte-range-locking"></a>Verrouillage de Byte

Applications peuvent verrouiller une plage d’octets spécifiée dans les fichiers. Si le verrouillage d’octets est activé sur les applications que vous écrivent dans votre StorSimple, puis hiérarchisation ne fonctionne pas dans votre tableau virtuel. Pour la hiérarchisation pour travailler de manière, toutes les zones des fichiers accédés doivent être déverrouillées. Verrouillage de plage octets n’est pas pris en charge avec volumes hiérarchisés sur votre tableau virtuel.

Mesures recommandées pour atténuer le verrouillage d’octets sont les suivantes :

-   Désactivez la plage d’octets verrouillage dans votre logique d’application.

-   Utiliser localement épinglées volumes (au lieu de niveaux) pour les données associées à cette application. Volumes localement épinglés ne pas monter dans le cloud.

-   Lorsqu’à l’aide d’épinglé localement volumes avec octet plage verrouillage activé, le volume peut provenir en ligne avant la restauration est terminée. Dans ce cas, vous devez attendre la restauration soit terminée.

## <a name="multiple-arrays"></a>Plusieurs matrices

Plusieurs tableaux virtuels peuvent doivent être déployés au compte pour un ensemble ouvrables croissant de données qui pourraient renverser sur le cloud et donc affecter les performances de l’appareil. Dans ce cas, il est préférable pour appareils échelle fur et à la plage de travail. Cette fonctionnalité nécessite un ou plusieurs périphériques à ajouter dans le centre de données locale. Lorsque vous ajoutez les appareils, vous pouvez :

-   Fractionner le jeu de données actuel.
-   Déployer les nouvelles charges de travail sur les nouveaux périphériques.
-   Si vous déployez plusieurs tableaux virtuels, nous vous recommandons d’équilibrage de charge perspective, distribuer la matrice sur hôtes hyperviseur différents.

-  Plusieurs tableaux virtuels (lorsqu’il est configuré comme un serveur de fichiers ou un serveur iSCSI) peuvent être déployées dans un Namespace distribué de système de fichiers. Pour plus d’informations, accédez au [Fichier système Namespace Solution Distributed avec Guide de déploiement de stockage Cloud hybride](https://www.microsoft.com/download/details.aspx?id=45507). Réplication de système de fichiers distribué n’est actuellement pas recommandée d’utiliser avec le tableau virtuel. 


## <a name="see-also"></a>Voir aussi
Découvrez comment [administrer votre tableau virtuel StorSimple](storsimple-ova-manager-service-administration.md) via le service Manager StorSimple.
