<properties 
   pageTitle="Notes de publication de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les problèmes critiques en cours et solutions pour le tableau virtuel StorSimple."
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
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>Notes de publication de tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivants identifient les problèmes critiques ouvertes pour la version officielle (disponibilité générale) mars 2016 du Microsoft Azure StorSimple virtuelle tableau (également connu sous le périphérique de virtuel StorSimple locaux ou le périphérique virtuel StorSimple). Cette version correspond à la version du logiciel 10.0.10271.0.

Les notes de publication sont à jour en permanence et dès la découverte de problèmes critiques nécessitant une solution de contournement, elles sont ajoutées. Avant de déployer votre périphérique virtuel StorSimple, attentivement les informations contenues dans les notes de publication. 

Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.


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
