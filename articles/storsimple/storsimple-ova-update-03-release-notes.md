<properties 
   pageTitle="Notes de publication de mises à jour de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les problèmes critiques en cours et solutions pour le tableau virtuel StorSimple mise à jour 0,3 en cours d’exécution."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notes de publication 0,3 mise à jour de tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivants identifient les problèmes critiques ouverts et les problèmes résolus les mises à jour de Microsoft Azure StorSimple tableau virtuel.

Les notes de publication sont à jour en permanence et dès la découverte de problèmes critiques nécessitant une solution de contournement, elles sont ajoutées. Avant de déployer votre tableau virtuel StorSimple, attentivement les informations contenues dans les notes de publication.

Mise à jour 0,3 correspond à la version de logiciel **10.0.10288.0**.

> [AZURE.NOTE] Mises à jour des interruptions et redémarrez votre appareil. Si e/s sont en cours, le périphérique entraîne temps d’arrêt.


## <a name="whats-new-in-the-update-03"></a>Quelles sont les nouveautés dans la mise à jour 0,3

Mise à jour 0,3 est principalement une version de résolution du bogue. Dans cette version, plusieurs bogues entraînant des échecs de sauvegarde dans la version précédente ont été résolus.

## <a name="issues-fixed-in-the-update-03"></a>Problèmes résolus dans la mise à jour 0,3

Le tableau suivant fournit un récapitulatif des problèmes résolus dans cette version.

| Non.  | Fonctionnalité                              | Problème                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Effectuer des sauvegardes régulières                                |Un problème a été observé dans la version précédente dans lequel les sauvegardes échouera à effectuer pour un partage de fichiers. Si ce problème s’est produite, le travail de sauvegarde échoue et une alerte critique a été élevée sur le service Manager StorSimple pour signaler à l’utilisateur. Ce problème s’est pas d’incidence sur les données sur les partages ou l’accès aux données. L’origine du problème a été identifié et corrigé dans cette version. <br></br> Le correctif ne s’applique pas rétroactivement aux partages que voient déjà ce problème. Les clients qui voient ce problème doivent tout d’abord appliquer la mise à jour 0,3, puis contactez le Support Microsoft pour effectuer une sauvegarde complète du système pour résoudre le problème. Au lieu de contacter le Support Microsoft, clients peuvent également restaurer sur un nouveau partage à partir d’une sauvegarde correcte pour les titres concernés.                                                                                                                                                                                 |
| 2    | iSCSI                         | Un problème a été observé dans la version précédente où les volumes disparaît lorsque vous copiez des données à un volume sur le tableau virtuel StorSimple. Ce problème a été résolu dans cette version. <br></br> Les solutions prennent effet uniquement sur nouvellement créé volumes. Les solutions ne s’appliquent pas rétroactivement aux volumes qui voient déjà ce problème. Il est conseillé aux utilisateurs pour afficher les volumes affectés en ligne via le portail classique Azure, effectuer une sauvegarde de ces volumes et puis restaurer ces volumes vers de nouveaux volumes.                                                               |


## <a name="known-issues-in-the-update-03"></a>Problèmes connus dans la mise à jour 0,3

Le tableau suivant fournit un récapitulatif des problèmes connus pour la matrice virtuelle StorSimple et inclut les questions noter release par rapport aux versions précédentes. 


| Non. | Fonctionnalité | Problème | Solution de contournement/commentaires |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Mises à jour | Les périphériques virtuels créés dans la version préliminaire ne peut pas être mis à jour dans la version officielle pris en charge. | Ces périphériques virtuels doivent être basculées pour la version officielle à l’aide d’un flux de travail reprise après sinistre. |
| **2.** | Disque de données généré | Une fois que vous avez configuré un disque de données d’une certaine taille spécifiée et créé le périphérique virtuel StorSimple correspondant, vous ne devez pas développer ou réduire le disque de données. Essayez de faire des résultats dans une perte de toutes les données au niveau du périphérique local. |   |
| **3.** | Stratégie de groupe | Lorsqu’un périphérique est à un domaine, appliquer une stratégie de groupe peut affecter l’opération d’appareil. | Assurez-vous que votre tableau virtuel concerne dans sa propre unité d’organisation (OU) Active Directory et aucun objet de stratégie de groupe (stratégie de groupe) n’est appliquées à celui-ci.|
| **4.** | Interface utilisateur web local | Si les fonctionnalités de sécurité renforcée sont activées dans Internet Explorer (renforcée), certaines pages de l’interface utilisateur web local comme résolution des problèmes ou Maintenance peuvent ne pas fonctionne correctement. Boutons sur les pages suivantes ne fonctionnent également pas. | Désactiver les fonctionnalités de sécurité renforcée dans Internet Explorer.|
| **5.** | Interface utilisateur web local | Dans une machine virtuelle Hyper-V, les interfaces réseau dans le site web interface utilisateur s’affichent sous forme de 10 Go/s interfaces. | Ce comportement est une réflexion de Hyper-V. Hyper-V affiche toujours 10 Go/s cartes réseau virtuel. |
| **6.** | Volumes hiérarchisés ou des partages | Plage d’octets verrouillage pour les applications qui fonctionnent avec la StorSimple volumes hiérarchisés n’est pas pris en charge. Si le verrouillage d’octets est activé, la hiérarchisation StorSimple ne fonctionne pas. | Mesures recommandées sont les suivantes : <br></br>Désactivez la plage d’octets verrouillage dans votre logique d’application.<br></br>Choisir de placer les données de cette application dans localement épinglés volumes Contrairement à des volumes hiérarchisés.<br></br>*Inconvénient*: lorsqu’à l’aide de localement épinglées volumes et octets plage verrouillage est activé, le volume localement épinglé peut être en ligne avant même que la restauration est terminée. Dans ce cas, si une restauration est en cours, puis vous devez attendre la restauration soit terminée. |
| **7.** | Partages hiérarchisés | Utilisation des fichiers volumineux peut entraîner lente couche arrière. | Lorsque vous travaillez avec des fichiers volumineux, nous vous recommandons de que le fichier plus grand est inférieur à 3 % de la taille de partage. |
| **8.** | Utilisé capacité d’actions | Vous pouvez voir partager consommation lorsqu’il n’y a aucuns données sur le partage. C’est parce que la capacité utilisée pour les partages inclut des métadonnées. |   |
| **9.** | Récupération d’urgence | Vous ne pouvez effectuer la reprise d’un serveur de fichiers dans le même domaine que celui de l’appareil source. Reprise d’un équipement cible dans un autre domaine n’est pas pris en charge dans cette version. | Ceci est activée dans une version ultérieure. |
| **10.** | PowerShell Azure | Les périphériques virtuels StorSimple ne peut pas être gérés tout au long la session PowerShell Azure dans cette version. | Toute la gestion des périphériques virtuels doit être effectuée via le portail classique Azure et l’interface utilisateur web local. |
| **11.** | Modification du mot de passe | La console d’appareil tableau virtuel accepte uniquement les entrées au format de clavier en-US. |   |
| **12.** | CHAP | Impossible de supprimer les informations d’identification CHAP une fois créées. En outre, si vous modifiez les informations, vous devez mettre les volumes hors connexion et puis importez-les en ligne pour que la modification prenne effet. | Ce problème est résolu dans une version ultérieure. |
| **13.** | serveur iSCSI  | Le « stockage d’utilisé » affichées pour un volume iSCSI peut être différent dans le service Manager StorSimple et l’hôte iSCSI. | L’hôte iSCSI a l’affichage du système de fichiers.<br></br>Le périphérique voit les blocs alloués lorsque le volume a été à la taille maximale.|
| **14.** | Serveur de fichiers  | Si un fichier dans un dossier possède un flux de données secondaire (publicités) associé, les annonces n’est pas sauvegardé ou restauré via le sinistre, cloner et récupération au niveau des éléments.| |


## <a name="next-step"></a>Étape suivante

[Installer la mise à jour 0,3](storsimple-ova-install-update-01.md) sur votre tableau virtuel StorSimple.

## <a name="references"></a>Références

Vous recherchez une note de version antérieure ? Atteindre : 

- [Notes de mise à jour de mise à jour du tableau virtuel StorSimple 0,1 et 0,2](storsimple-ova-update-01-release-notes.md)

- [Notes de publication de disponibilité StorSimple virtuel général de tableau](storsimple-ova-pp-release-notes.md)
 

