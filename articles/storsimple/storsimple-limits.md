<properties 
   pageTitle="Limites du système StorSimple | Microsoft Azure"
   description="Décrit les limites du système et les tailles recommandées pour les connexions et des composants de la série 8000 StorSimple."
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

# <a name="storsimple-system-limits"></a>Limites du système StorSimple

## <a name="overview"></a>Vue d’ensemble

StorSimple offre un stockage scalable et flexible pour votre centre de données. Toutefois, il existe certaines limites à garder à l’esprit que vous prévoyez, déployez et le fonctionnement de votre solution StorSimple. Le tableau suivant décrit ces limites et fournit quelques recommandations afin que vous pouvez tirer le meilleur parti de votre solution StorSimple.

| Identificateur limite | Limite | Commentaires |
|----------------- | ------|--------- |
| Nombre maximal d’informations d’identification du compte de stockage | 64 | |
| Nombre maximal de conteneurs de volume | 64 | |
| Nombre maximal de volumes | 255 | |
| Nombre maximal de volumes localement épinglés | 32 | |
| Nombre maximal de planifications par modèle de bande passante | 168 | Une planification pour toutes les heures, tous les jours de la semaine (24 * 7). |
| Taille maximale d’un volume hiérarchisé sur périphériques physiques | 64 To pour 8100 et 8600 | 8100 et 8600 sont périphériques physiques. |
| Taille maximale d’un volume hiérarchisé sur des périphériques virtuels dans Azure | 30 To pour 8010 <br></br> 64 To pour 8020 | 8010 et 8020 sont virtuels périphériques dans Azure qui utilisent un stockage Standard et Premium stockage respectivement. |
| Taille maximale d’un volume localement épinglé sur périphériques physiques | 8,5 To pour 8100 <br></br> 22,5 To pour 8600 | 8100 et 8600 sont périphériques physiques. |
| Nombre maximal de connexions iSCSI | 512 | |
| Nombre maximal de connexions iSCSI d’initiateurs | 512 | |
| Nombre maximal d’enregistrements de contrôle d’accès par périphérique | 64 | |
| Nombre maximal de volumes par la stratégie de sauvegarde | 24 | |
| Nombre maximal de sauvegardes conservées par planning (dans une stratégie de sauvegarde) | 64 | |
| Nombre maximal de planifications par la stratégie de sauvegarde | 10 | |
| Nombre maximal d’instantanés de n’importe quel type peuvent être conservées par volume | 256 | Ce nombre comprend des instantanés locales et en nuage instantanés. |
| Nombre maximal d’instantanés qui peuvent être présents dans n’importe quel appareil | 10 000 | |
| Nombre maximal de volumes de qui peut être traité en parallèle pour sauvegarder, restaurer ou cloner | 16 |<ul><li>S’il existe plus de 16 volumes, ils sont traités dans un ordre séquentiel dès emplacements de traitement sont disponibles.</li><li>Nouvelles sauvegardes de cloné ou un volume hiérarchisé restauré ne peut pas se produire jusqu'à ce que l’opération est terminée. Toutefois, pour un volume local, des sauvegardes peuvent une fois que le volume est en ligne.</li></ul>|
| Restaurer et récupérer temps pour les volumes hiérarchisés de duplication | < 2 minutes | <ul><li>Le volume est rendu disponible dans les 2 minutes d’opération restaurer ou cloner, quelle que soit la taille du volume.</li><li>Les performances de volume initiale peuvent être inférieures à la normale que la plupart des données et des métadonnées réside toujours dans le cloud. Il est possible que d’amélioration des performances en tant que flux de données à partir du cloud à l’appareil StorSimple.</li><li>La durée totale de métadonnées du téléchargement dépend de la taille du volume alloué. Métadonnées sont automatiquement placée dans l’appareil en arrière-plan en le taux de 5 minutes par To de données de volume alloué. Ce taux peut être affecté par la bande passante Internet dans le cloud.</li><li>L’opération de cloner ou de restaurer est terminée lorsque toutes les métadonnées se trouve sur l’appareil.</li><li>Opérations de sauvegarde ne peut pas être effectuées jusqu'à ce que la restauration ou cloner opération est entièrement terminée.|
| Restaurer le temps de récupérer des volumes localement épinglés | < 2 minutes | <ul><li>Le volume est rendu disponible dans les 2 minutes de la restauration, quelle que soit la taille du volume.</li><li>Les performances de volume initiale peuvent être inférieures à la normale que la plupart des données et des métadonnées réside toujours dans le cloud. Il est possible que d’amélioration des performances en tant que flux de données à partir du cloud à l’appareil StorSimple.</li><li>La durée totale de métadonnées du téléchargement dépend de la taille du volume alloué. Métadonnées sont automatiquement placée dans l’appareil en arrière-plan en le taux de 5 minutes par To de données de volume alloué. Ce taux peut être affecté par la bande passante Internet dans le cloud.</li><li>Contrairement aux volumes hiérarchisés, pour les volumes localement épinglés, les données du volume sont également téléchargées localement sur l’appareil. L’opération de restauration est terminée lorsque toutes les données de volume a été mis à l’appareil.</li><li>Les opérations de restauration peuvent être longues. La durée totale pour terminer la restauration dépend de la taille du volume local généré, votre bande passante Internet et les données existantes sur l’appareil. Opérations de sauvegarde sur le volume localement épinglé sont autorisées pendant que l’opération de restauration est en cours.|
|Taux de traitement des instantanés cloud| 15 minutes/to | <ul><li>Temps minimal pour rendre le cloud instantané prêt pour le téléchargement, par To de données de volume alloué de sauvegarde. </li><li> Total cloud instantané temps est calculé en ajoutant cette fois-ci dans le temps de téléchargement instantané, qui est affecté par la bande passante Internet vers le cloud.
| Débit de lecture/écriture nombre maximal de clients (lorsqu’il est pris en charge de la couche SSD) * | 920/720 Mo/s avec une interface de réseau solution 10 unique | Jusqu'à 2 x avec o et deux interfaces réseau. |
| Débit de lecture/écriture nombre maximal de clients (lorsqu’il est pris en charge de la couche du disque dur) * | 120/250 Mo/s |
| Nombre maximal de clients en lecture/écriture débit (lorsqu’il est pris en charge de la couche cloud)* pour la mise à jour 3 et versions ultérieures** | 60/40 Mo/s pour hiérarchisé volumes<br><br>60/80 Mo/s pour hiérarchisé volumes avec l’option archivage sélectionnée lors de la création du volume | Débit de lecture dépend de clients générer et maintenir suffisant profondeur de file d’attente d’e/s. <br><br>Vitesse obtenue dépend de la vitesse du compte de stockage sous-jacent utilisé. | 

& #42 ; Débit maximal par type e/s a été mesuré avec en lecture à 100 % et 100 % en écriture scénarios. Débit réel peut être inférieur et dépend e/s mélanger et conditions en réseau.

& #42 ; & #42 ; Numéros de performances avant la mise à jour 3 peuvent être inférieures.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue [Configuration requise StorSimple](storsimple-system-requirements.md). 
