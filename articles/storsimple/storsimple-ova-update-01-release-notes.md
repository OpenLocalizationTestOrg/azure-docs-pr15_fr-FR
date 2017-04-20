<properties 
   pageTitle="Notes de publication de mises à jour de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les problèmes critiques en cours et solutions pour le tableau virtuel StorSimple mise à jour 0,2 et 0,1 en cours d’exécution."
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notes de mise à jour du tableau virtuelle 0,2 et 0,1 StorSimple

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivants identifient les problèmes critiques ouverts et les problèmes résolus les mises à jour de Microsoft Azure StorSimple tableau virtuel. (Tableau virtuel Microsoft Azure StorSimple est également connu sous le nom du StorSimple local virtuel ou le périphérique virtuel StorSimple.) 

Les notes de publication sont à jour en permanence et dès la découverte de problèmes critiques nécessitant une solution de contournement, elles sont ajoutées. Avant de déployer votre périphérique virtuel StorSimple, attentivement les informations contenues dans les notes de publication.

Mise à jour 0,2 correspond à la version du logiciel **10.0.10280.0**; Mise à jour 0,1 est version **10.0.10279.0**. Les sections suivantes répertorient les modifications apportées à chaque mise à jour. 

> [AZURE.NOTE] Mises à jour des interruptions et va redémarrer votre appareil. Si e/s sont en cours, l’appareil générera un temps d’arrêt.

## <a name="issues-fixed-in-the-update-02"></a>Problèmes résolus dans la mise à jour 0,2
Mise à jour 0,2 inclut toutes les modifications de mise à jour de 0,1 outre le correctif décrit dans le tableau suivant :

Fonctionnalité                              | Problème                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Mises à jour                                 | Dans la dernière version, les mises à jour n’ont pas été détectées automatiquement dans le portail classique Azure, afin que vous deviez utiliser l’interface utilisateur Web local pour installer les mises à jour. Ce problème est résolu dans cette version. Après avoir installé la mise à jour 0,2, vous pouvez installer des mises à jour ultérieures à l’aide du portail classique Azure.                       

## <a name="whats-new-in-the-update-01"></a>Quelles sont les nouveautés dans la mise à jour 0,1

Mise à jour 0,1 contient les correctifs et améliorations suivantes. 

- **Résilience améliorées pour les interruptions cloud**: cette version comporte plusieurs correctifs autour de sinistre, sauvegarder, restaurer et hiérarchisation en cas d’interruption de connectivité cloud. 

- **Amélioration des performances de restauration**: cette version a correctifs ont sensiblement Couper vers le bas de l’heure d’achèvement des tâches restaurer.

- **Optimisation de récupération automatique espace**: suppression des données dans les volumes exactement générés, les blocs d’espace de stockage inutilisé doivent être récupérés. Cette version a amélioré le processus de récupération espace à partir du cloud résultant dans l’espace inutilisé devienne disponible plus vite par rapport aux versions précédentes.

- **Nouvelles images de disque virtuel**: nouveau disque dur virtuel, VHDX et VMDK sont désormais disponibles via le portail classique Azure. Vous pouvez télécharger ces images pour configurer de nouveaux appareils de mise à jour 0,1.

- **Améliorer la précision de l’état des tâches dans le portail**: dans la version antérieure du logiciel, état du travail Création de rapports dans le portail n’était pas précis. Ce problème est résolu dans cette version.

- **Expérience de jonction de domaine**: correctifs liés au domaine-« joindre » et changement de nom de l’appareil.


## <a name="issues-fixed-in-the-update-01"></a>Problèmes résolus dans la mise à jour 0,1

Le tableau suivant fournit un récapitulatif des problèmes résolus dans cette version.

| Non.  | Fonctionnalité                              | Problème                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | Dans certaines versions VMware, le disque du système d’exploitation a été considéré comme incomplet à l’origine d’alertes et interrompre le fonctionnement normal. Ce problème est résolu dans cette version.                                                                                                                                                                                    |
| 2    | serveur iSCSI                         | Dans la dernière version, l’utilisateur a été requis pour spécifier une passerelle pour chaque interface réseau activés du périphérique virtuel StorSimple. Ce comportement est modifié dans cette version afin que l’utilisateur doit configurer au moins une passerelle pour toutes les interfaces réseau activée.                                                                              |
| 3    | Prise en charge                      | Dans la version antérieure du logiciel, prend en charge collection du lot a échoué lors de la taille du package ont été supérieure à 1 Go. Ce problème est résolu dans cette version.                                                                                                                                                                               |
| 4    | Accès par le cloud                         |  Dans la dernière version, si la matrice virtuel StorSimple ne dispose pas de la connectivité réseau et a été redémarrée, l’interface utilisateur local aurait problèmes de connectivité. Ce problème est résolu dans cette version.                                                                                                                            |
| 5    | Graphiques de surveillance                    | Dans la version précédente, en suivant un basculement appareil, les graphiques de l’utilisation de la capacité cloud affichent des valeurs incorrectes dans le portail classique Azure. Ce problème est corrigé dans la version actuelle.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Problèmes connus dans la mise à jour 0,1

Le tableau suivant fournit un récapitulatif des problèmes connus pour la matrice virtuel StorSimple et inclut les questions noter release par rapport aux versions précédentes. **La version de problèmes près répertoriée dans cette version sont signalés par un astérisque. Presque toutes les problèmes répertoriés dans cette liste ont transféré à partir de la version de disponibilité générale du tableau virtuel StorSimple.**


| Non. | Fonctionnalité | Problème | Solution de contournement/commentaires |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Mises à jour | Les périphériques virtuels créés dans la version préliminaire ne peut pas être mis à jour dans la version officielle pris en charge. | Ces périphériques virtuels doivent être basculées pour la version officielle à l’aide d’un flux de travail reprise après sinistre. |
| **2.** | Disque de données généré | Une fois que vous avez configuré un disque de données d’une certaine taille spécifiée et créé le périphérique virtuel StorSimple correspondant, vous ne devez pas développer ou réduire le disque de données. Essayez de le faire provoquera une perte de toutes les données au niveau du périphérique local. |   |
| **3.** | Stratégie de groupe | Lorsqu’un périphérique est à un domaine, appliquer une stratégie de groupe peut affecter l’opération d’appareil. | Assurez-vous que votre tableau virtuel concerne dans sa propre unité d’organisation (OU) Active Directory et aucun objet de stratégie de groupe (stratégie de groupe) n’est appliquées à celui-ci.|
| **4.** | Interface utilisateur web local | Si les fonctionnalités de sécurité renforcée sont activées dans Internet Explorer (renforcée), certaines pages de l’interface utilisateur web local comme résolution des problèmes ou Maintenance peuvent ne pas fonctionne correctement. Boutons sur les pages suivantes ne fonctionnent également pas. | Désactiver les fonctionnalités de sécurité renforcée dans Internet Explorer.|
| **5.** | Interface utilisateur web local | Dans une machine virtuelle Hyper-V, les interfaces réseau dans le site web interface utilisateur s’affichent sous forme de 10 Go/s interfaces. | Ce comportement est une réflexion de Hyper-V. Hyper-V affiche toujours 10 Go/s cartes réseau virtuel. |
| **6.** | Volumes hiérarchisés ou des partages | Plage d’octets verrouillage pour les applications qui fonctionnent avec la StorSimple volumes hiérarchisés n’est pas pris en charge. Si le verrouillage d’octets est activé, la hiérarchisation StorSimple ne fonctionnera pas. | Mesures recommandées sont les suivantes : <br></br>Désactivez la plage d’octets verrouillage dans votre logique d’application.<br></br>Choisir de placer les données de cette application dans localement épinglés volumes Contrairement à des volumes hiérarchisés.<br></br>*Inconvénient*: si à l’aide de localement épinglé volumes et octets plage verrouillage est activé, n’oubliez pas que le volume localement épinglé peut être en ligne avant même que la restauration est terminée. Dans ce cas, si une restauration est en cours, puis vous devez attendre la restauration soit terminée. |
| **7.** | Partages hiérarchisés | Utilisation des fichiers volumineux peut entraîner lente couche arrière. | Lorsque vous travaillez avec des fichiers volumineux, nous vous recommandons de que le fichier plus grand est inférieur à 3 % de la taille de partage. |
| **8.** | Utilisé capacité d’actions | Vous pouvez voir partager la consommation en l’absence de toutes les données sur le partage. C’est parce que la capacité utilisée pour les partages inclut des métadonnées. |   |
| **9.** | Récupération d’urgence | Vous ne pouvez effectuer la reprise d’un serveur de fichiers dans le même domaine que celui de l’appareil source. Reprise d’un équipement cible dans un autre domaine n’est pas pris en charge dans cette version. | Cela feront dans une version ultérieure. |
| **10.** | PowerShell Azure | Les périphériques virtuels StorSimple ne peut pas être gérés tout au long la session PowerShell Azure dans cette version. | Toute la gestion des périphériques virtuels doit être effectuée via le portail classique Azure et l’interface utilisateur web local. |
| **11.** | Modification du mot de passe | La console d’appareil tableau virtuel accepte uniquement les entrées au format de clavier en-US. |   |
| **12.** | CHAP | Impossible de supprimer les informations d’identification CHAP une fois créées. En outre, si vous modifiez les informations, vous devez mettre les volumes hors connexion et puis importez-les en ligne pour que la modification prenne effet. | Ils seront abordés dans une version ultérieure. |
| **13.** | serveur iSCSI  | Le « stockage d’utilisé » affichées pour un volume iSCSI peut être différent dans le service Manager StorSimple et l’hôte iSCSI. | L’hôte iSCSI a l’affichage du système de fichiers.<br></br>Le périphérique voit les blocs alloués lorsque le volume a été à la taille maximale.|
| **14.** | Fichier serveur *  | Si un fichier dans un dossier possède un flux de données secondaire (publicités) associé, les annonces n’est pas sauvegardé ou restauré via le sinistre, cloner et récupération au niveau des éléments.| |


## <a name="next-step"></a>Étape suivante

[Installer les mises à jour](storsimple-ova-install-update-01.md) sur votre tableau virtuel StorSimple.
