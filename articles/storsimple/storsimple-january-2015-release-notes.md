<properties 
   pageTitle="Notes de publication 0,2 mise à jour de 8000 StorSimple | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités et correctifs, problèmes ouverts et solutions de contournement disponibles pour le janvier 2015 Microsoft Azure StorSimple release (mise à jour 0,2)."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Notes de publication 0,2 mise à jour de la série StorSimple 8000 - janvier 2015

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivants identifient les problèmes critiques ouvertes pour la version de janvier 2015 de Microsoft Azure StorSimple. Ils contiennent également une liste StorSimple logiciels et microprogramme mises à jour incluses dans cette version. Il s’agit de la deuxième version après que la version de StorSimple 8000 série a été faite disponible à partir de juillet 2014.
  
Cette mise à jour ne modifie pas la version du logiciel périphérique physique à partir de la mise à jour d’octobre. Il reste version 6.3.9600.17312. L’image utilisée par l’image du périphérique virtuel a changé dans cette version. Par conséquent, tous les nouveaux périphériques virtuels créées après 20/1/2015 affichera la version en tant que 6.3.9600.17361.  

Veuillez consulter les informations suivantes contenues dans les notes de publication pour la mise à jour de janvier 2015.

> [AZURE.IMPORTANT]  
>
>- Cette mise à jour n’est pas disponible via Windows Update et ne peut pas être installé comme autres mises à jour. Votre appareil ne recevrez pas cette mise à jour même si vous avez appliqué les mises à jour à l’aide du portail classique Azure. Cette mise à jour s’applique uniquement aux périphériques virtuels créées après 20 janvier 2015. 
> 
>- La version de janvier de StorSimple ne contient pas des mises à jour à l’appareil physique StorSimple. Vous pouvez toujours appliquer des mises à jour Windows disponibles sur le périphérique virtuel, y compris les correctifs de sécurité récents, mais vous ne verrez pas un changement dans la version du périphérique physique StorSimple.

## <a name="whats-new-in-the-january-release"></a>Quelles sont les nouveautés dans la version de janvier

Cette mise à jour contient un correctif en rapport avec les volumes de passer en mode hors connexion sur le périphérique virtuel. (Voir [problèmes résolus dans cette version](#issues-fixed-in-the-january-release).)  

La mise à jour ne contient pas de nouvelles fonctions ou fonctionnalités.  

## <a name="issues-fixed-in-the-january-release"></a>Problèmes résolus dans la version de janvier

Le tableau suivant décrit le problème a été résolu dans cette mise à jour.

|Non.|Fonctionnalité|Problème|S’applique au périphérique physique|S’applique au périphérique virtuel 
|---|-------|-----|--------------------------|-------------------------
|1|Volumes hors connexion|Lors de la latence élevée cloud persiste pendant quelques minutes, les volumes de périphérique virtuel StorSimple passez en mode hors connexion sur les hôtes. Ce correctif augmente le seuil de latence cloud, ce qui réduit les situations qui pourraient les volumes passer en mode hors connexion sur hosts.|N°|Oui  

## <a name="known-issues-in-the-january-release"></a>Problèmes connus dans la version de janvier

Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.
 
|Non.|Fonctionnalité|Problème|Commentaires/solution de contournement|S’applique au périphérique physique|S’applique au périphérique virtuel 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Usine par défaut|  Dans certains cas, lorsque vous effectuez une réinitialisation par défaut, le périphérique StorSimple peut être bloqué et affiche ce message : **Rétablir usine est en cours (phase 8).** Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours.| N’appuyez sur CTRL + C après le lancement d’une réinitialisation factory. Si vous êtes déjà dans cet état, contactez le Support Microsoft pour obtenir la procédure suivante.|Oui|N°|
|2|Quorum de disque| Parfois, si la majorité des disques du boîtier EBOD d’un appareil 8600 est déconnectée résultant dans aucun quorum de disque, puis le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont connectés à nouveau.|Vous devrez redémarrez l’appareil. Si le problème persiste, contactez le Support Microsoft pour obtenir la procédure suivante.|Oui |N°
|3|Échecs d’instantané cloud|Parfois, un instantané de cloud peut échouer avec l’erreur **maximale sauvegarde atteint**. Cela se produit si vous dépassez 255 clones en ligne sur le même appareil, à partir du même volume d’origine qui a été supprimé.||Oui|Oui
|4|ID de contrôleur incorrecte|Lorsqu’un remplacement contrôleur est effectué, contrôleur 0 peut apparaissent sous forme de contrôleur de 1. Pendant le remplacement de contrôleur, lors du chargement de l’image à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme ID. du contrôleur égal  Parfois, ce problème peut également se produire après le redémarrage d’un système.|Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même une fois le remplacement contrôleur terminée.|Oui|N° 
|5| Dispositif de surveillance des graphiques|Dans le service Gestionnaire StorSimple, les graphiques de surveillance appareil ne fonctionnent pas lors de la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour l’appareil.|Modifier la configuration du proxy web pour le périphérique enregistré auprès de votre service Manager StorSimple afin que l’authentification est définie sur aucun. Pour ce faire, exécutez la Windows PowerShell pour l’applet de commande Set-HcsWebProxy StorSimple.|Oui|Oui
|6| Comptes de stockage|Au moyen du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela vous aboutissez à une situation dans laquelle les données de l’utilisateur ne sont pas accessibles.|| Oui |  Oui
|7|Basculement entre périphériques| Plusieurs basculement d’un conteneur de volume à partir du même périphérique source pour appareils différents cible n’est pas pris en charge.| Basculement à partir d’un seul périphérique inactif sur plusieurs appareils fera les conteneurs de volume sur le premier basculé appareil perdent propriété des données. Après une telle reprise, ces conteneurs volume apparaît ou se comportent différemment lorsque vous affichez les dans le portail classique Azure.|Oui|N°
|8| Installation|Au cours de carte StorSimple pour l’installation SharePoint, vous devez fournir une adresse IP appareil afin que l’installation s’exécute correctement.||Oui|N°
|9| Proxy Web|Si votre configuration du proxy web a HTTPS comme le protocole spécifié, puis vos communications appareil-service seront affectées et accédera l’appareil mobile en mode hors connexion. Prise en charge des packages seront également générées dans le processus, consomme des ressources importantes sur votre appareil.|Assurez-vous que l’URL du proxy web a HTTP en tant que le protocole spécifié. Afficher des informations supplémentaires sur la façon de [proxy web de configurer pour votre appareil](storsimple-configure-web-proxy.md).|Oui |N°
|10|Proxy Web|  Si vous configurez et activez le proxy web sur un appareil enregistré, vous devez redémarrer le contrôleur actif sur votre appareil.|| Oui |N°
|11|Latence élevée cloud et la charge de travail d’e/s élevé|Lorsque votre appareil StorSimple rencontre une combinaison de latence cloud très haute (ordre des secondes) et la charge de travail d’e/s haute, les volumes appareil coder une dégradation et e/s peut échouer avec une erreur « appareil n’est pas prêt ».|Vous devez redémarrer l’appareil contrôleurs manuellement ou effectuez un basculement appareil pour résoudre ce problème.|Oui|N°

## <a name="physical-device-updates-in-the-january-release"></a>Mises à jour de périphérique physique au mois de janvier release

Cette mise à jour ne contient pas d’autres modifications à l’appareil StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Contrôleur de SCSI (sa) joint série et mises à jour au mois de janvier mise à jour

Cette version ne contient pas les mises à jour le contrôleur SCSI (sa) joint série ou du microprogramme. La mise à jour du pilote lors du octobre, release 2014. 

## <a name="virtual-device-updates-in-the-january-release"></a>Mises à jour de périphérique virtuel au mois de janvier release

Cette version contient une image mise à jour pour le périphérique virtuel. Tous les appareils virtuels créées après 20 janvier 2015 seront affichera la version du logiciel comme 6.3.9600.17361.

 
