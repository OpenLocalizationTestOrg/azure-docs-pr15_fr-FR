<properties 
   pageTitle="Surveiller votre appareil StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le service Manager StorSimple pour surveiller les performances, l’utilisation des capacités, débit du réseau et performances du périphérique e/s."
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Le service Manager StorSimple permet d’analyser votre appareil StorSimple 

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser le service Manager StorSimple pour surveiller des appareils spécifiques au sein de votre solution StorSimple. Vous pouvez créer des graphiques personnalisées basés sur les performances, l’utilisation des capacités, débit réseau et indicateurs de performance appareil e/s. 

Pour afficher les informations d’analyse pour un périphérique spécifique, dans le portail classique Azure, sélectionnez le service Manager StorSimple. Cliquez sur l’onglet **Moniteur** , puis dans la liste des appareils mobiles. La page **Moniteur** contient les informations suivantes.

## <a name="io-performance"></a>Performances 

**Les performances** pistes métriques liées au nombre de lecture et opérations d’écriture entre soit les interfaces initiateur iSCSI sur le serveur hôte et le périphérique ou l’appareil et le cloud. Cette performance peut être mesurée pour un volume spécifique, un conteneur de volume spécifique ou tous les conteneurs de volume.

Le tableau ci-dessous indique les e/s pour l’initiateur sur votre appareil pour tous les volumes d’un périphérique de production. Les mesures tracées sont lire et écrivent des octets par seconde, lire et écrire opérations IO par seconde et lire et écrire latence.

![Performances IO d’initiateur à appareil](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Dans le même appareil, les opérations d’e/s sont tracées pour les données à partir de l’appareil dans le cloud pour tous les conteneurs de volume. Sur cet appareil, les données sont uniquement dans la couche linéaire et rien ne compte dispersées dans le cloud. Il n’existe aucune opération en lecture / écriture se produisant à partir de l’appareil dans le cloud. Par conséquent, les sommets dans le graphique sont à un intervalle de 5 minutes qui correspond à la fréquence à laquelle la pulsation est activée entre l’appareil et le service. 

![Performances IO à partir de l’appareil vers le cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Pour le même appareil, un nuage instantané aux données de volume en commençant à 2:00 pm. Cela a donné lieu à partir du périphérique de données vers le cloud. Lectures écritures ont été traitées dans le cloud dans cette durée. Le graphique IO indique une pointe dans les diverses métriques correspondant à la fois quand l’instantané. 

![Performances IO pour appareil vers le cloud après capture instantanée cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Utilisation de la capacité 

**Utilisation de la capacité** effectue le suivi des mesures associées à la quantité d’espace de stockage de données qui est utilisé par les volumes, les conteneurs de volume ou appareil. Vous pouvez créer des rapports basés sur la capacité de stockage de votre appareil, votre stockage cloud ou votre stockage principal. Utilisation de la capacité peut être mesurée sur un volume spécifique, un conteneur de volume spécifique ou tous les conteneurs de volume.


Le principal, cloud, et capacité de stockage périphérique peut être décrite comme suit :

###<a name="primary-storage-capacity-utilization"></a>Utilisation de la capacité de stockage principal
 
Ces graphiques indiquent le volume de données écrites sur les volumes StorSimple avant que les données sont dédupliquées et compressées. Vous pouvez afficher l’utilisation du stockage principal par tous les volumes ou d’un seul volume.

Lorsque vous affichez les graphiques de l’utilisation du stockage principal volume capacité pour tous les volumes par rapport à chacun des volumes individuels et totaliser les données principales dans ces deux cas, il peut être une incompatibilité entre les deux nombres. La totalité des données principales sur tous les volumes ne peuvent pas ajouter à la somme des données principales des volumes individuels. Cela peut être dû à une des opérations suivantes :

- **Données de capture instantanée incluses pour tous les volumes**: ce comportement est visible uniquement si vous exécutez version antérieure à la mise à jour 3. Les données principales affichées pour tous les volumes sont la somme des données principales pour chaque volume et les données de capture instantanée. Les données principales affichées pour un volume donné correspondant à uniquement la quantité de données allouées sur le volume (et n’incluent pas les données de capture instantanée de volume correspondantes).

    Cela peut également s’expliquer par l’équation suivante :

    *Données principales (tous les volumes) = somme (données principales (volume i) + taille des données de capture instantanée (volume i))*
    
    *où, données principal (volume i) = taille des données principales allouées au volume i*
 
    Si les instantanés sont supprimés via le service, la suppression est effectuée en mode asynchrone en arrière-plan. Cela peut prendre quelques instants pour la taille des données en volume à mettre à jour après la suppression de l’instantané. 

    Si vous exécutez la mise à jour 3 ou version ultérieure, les données de capture instantanée ne sont pas incluses dans les données du volume. Et l’utilisation du principal se calcule comme suit :

    * Données principal (tous les volumes) = somme des (données principales (volume i)
    
    *où, données principal (volume i) = taille des données principales allouées au volume i*
 
- **Volumes avec surveillance désactivé inclus dans tous les volumes**: Si vous avez des volumes sur votre appareil pour lesquelles la surveillance est désactivée, les données d’analyse pour ces volumes individuels ne seront pas disponibles dans les graphiques. Toutefois, les données pour tous les volumes dans le graphique comprennent les volumes dont la surveillance est désactivée. 
 
- **Supprimé volumes avec les sauvegardes associés live inclus pour tous les volumes**: si volumes contenant des données de capture instantanée sont supprimés, mais les instantanés associés existent toujours, puis que vous voyiez une incompatibilité.

- **Volumes supprimés inclus pour tous les volumes**: dans certains cas, les anciens volumes peuvent exister même si celles-ci ont été supprimés. L’effet de suppression n’est pas visible et l’appareil peut afficher la capacité disponible inférieure. Vous devez contacter le Support Microsoft pour supprimer ces volumes.

Les graphiques suivants montrent la capacité de stockage principal d’un périphérique StorSimple avant et après un instantané de cloud. Comme il s’agit uniquement les données de volume, un instantané de cloud ne doit pas modifier le stockage principal. Comme vous pouvez le voir, le graphique n’affiche aucune différence entre l’utilisation de la capacité principal résulte prendre un instantané de cloud. L’instantané cloud démarrée à environ 2:00 pm sur votre appareil.

![Utilisation de la capacité primaire avant instantané cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilisation de la capacité primaire après capture instantanée cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Si vous exécutez mise à jour 2 ou version ultérieure, vous pouvez répartir vers le bas de la capacité de stockage principal en un volume individuel, tous les volumes, tous les volumes hiérarchisés et tous les volumes localement épinglés comme illustré ci-dessous. Répartir par tous les volumes localement épinglés vous permettra de déterminer rapidement la quantité de la couche locale est utilisée.

![Utilisation de la capacité principal pour tous les volumes localement épinglés](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Utilisation de la capacité de stockage cloud

Ces graphiques montrent la quantité d’espace de stockage cloud utilisé. Ces données sont dédupliquées et compressées. Ce montant inclut des instantanés cloud qui contiennent des données qui n’est pas répercutées dans n’importe quel volume principal et sont conservées à des fins de rétention hérités ou requis. Vous pouvez comparer le serveur principal et en nuage chiffres de consommation de stockage pour avoir une idée du taux de réduction de données, même si le nombre ne sera pas exact. Les graphiques suivants montrent l’utilisation de la capacité de stockage cloud d’un périphérique StorSimple avant et après un instantané de cloud. L’instantané cloud démarrée à environ 2:00 pm sur votre appareil et vous pouvez voir l’utilisation de la capacité cloud sont passées en même temps, augmentant de 5.73 Mo 4.04 Go.

![Utilisation de la capacité cloud avant instantané cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilisation de la capacité cloud après capture instantanée cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Utilisation de la capacité de stockage appareil

Ces graphiques montrent l’utilisation de l’appareil, qui sera l’utilisation du stockage plus de primaire parce qu’il inclut la couche linéaire SSD totale. Ce niveau contient une quantité de données qui existe également sur les autres niveaux du périphérique. La capacité de la couche linéaire SSD est changent afin que les nouvelles données entrants, les données anciennes sont déplacées vers le niveau du disque dur (date à laquelle il est dédupliqué et compressé) et par la suite dans le cloud.

Dans le temps, l’utilisation des capacités principale et l’utilisation des capacités appareil probablement augmente ensemble jusqu'à ce que les données commencent à monter en cascade dans le cloud. À ce stade, l’utilisation de la capacité appareil probablement commence à plateau, mais l’utilisation de la capacité primaire augmente lors de l’écriture de données supplémentaires.

Les graphiques suivants montrent la capacité de stockage principal d’un périphérique StorSimple avant et après un instantané de cloud. L’instantané cloud démarré à 2:00 et l’utilisation de la capacité appareil démarré en réduisant à cet horaire. L’utilisation de la capacité de stockage appareil est passé vers le bas de 11.58 Go à 7.48 Go. Cela indique que probablement les données compressées dans la couche SSD linéaire a été dédupliquées, compressées et déplacées vers le niveau du disque dur. Notez que si le périphérique comporte déjà une grande quantité de données niveaux la SSD et le disque dur, vous voyez ne peut-être pas cette baisse. Dans cet exemple, le périphérique a une petite quantité de données.

![Utilisation de la capacité appareil avant instantané cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilisation de la capacité appareil après capture instantanée cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Débit du réseau

**Débit réseau** effectue le suivi des mesures associées à la quantité de données transférées à partir des interfaces réseau initiateur iSCSI sur le serveur hôte et l’appareil et entre le périphérique et le cloud. Vous pouvez surveiller cette métrique pour chacune des interfaces réseau iSCSI sur votre appareil.

Les graphiques suivants montrent le débit réseau des données 0 et des données 4, les deux interfaces réseau solution 1 sur votre appareil. Dans ce cas, données 0 a été activé pour le cloud alors que 4 de données a été activée iSCSI. Vous pouvez voir entrant et le trafic sortant de votre périphérique StorSimple. La courbe plate dans le graphique à partir de 3:24 pm est due au fait que nous recueillons vos données uniquement 5 minutes et doit être ignorées. 

![Débit réseau pour données4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Débit réseau pour données4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Performances de l’unité 

**Performances de l’unité** effectue le suivi des indicateurs liés aux performances de votre appareil. Le tableau suivant indique les statistiques de l’utilisation du processeur pour un appareil en production.

![Utilisation de l’UC pour appareil](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le tableau de bord périphérique de service Manager StorSimple](storsimple-device-dashboard.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
