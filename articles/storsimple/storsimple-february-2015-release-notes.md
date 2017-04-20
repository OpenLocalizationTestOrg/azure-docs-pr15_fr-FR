<properties 
   pageTitle="Notes de publication 0,3 mise à jour de 8000 StorSimple | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités et correctifs, problèmes ouverts et solutions de contournement disponibles pour le février 2015 Microsoft Azure StorSimple release (mise à jour 0.3)."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Notes de publication 0,3 mise à jour de la série StorSimple 8000 - février 2015

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivants identifient les problèmes ouverts critiques pour mettre à jour série StorSimple 8000 0,3 publiée en février 2015. Ils contiennent également une liste StorSimple logiciels et microprogramme mises à jour incluses dans cette version. Il s’agit de la troisième version après que la version de StorSimple 8000 série a été faite disponible à partir de juillet 2014.
  
Cette mise à jour ne modifie pas la version du logiciel appareil à partir de la mise à jour de janvier. Il reste version 6.3.9600.17312. Vous pouvez vérifier que la mise à jour a été installé en vérifiant la date de **Dernière mise à jour** . Si la date est le 2/10/2015 ou version ultérieure, la mise à jour a été installé correctement.  

Nous vous recommandons de rechercher et d’appliquer des mises à jour disponibles immédiatement après avoir installé votre périphérique StorSimple. Vous pouvez également activer les mises à jour automatiques pour télécharger et installer les mises à jour avec une priorité élevée à partir de Microsoft dès qu’elles sont disponibles. Pour plus d’informations, voir [mettre à jour votre périphérique StorSimple](storsimple-update-device.md).  

Veuillez consulter les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple.  

>[AZURE.IMPORTANT]   
>
> - Utiliser le service Manager StorSimple et pas Windows PowerShell pour StorSimple pour installer la mise à jour de février.   
> - Il faut environ une heure pour installer cette mise à jour. Toutefois, si vous installez les mises à jour cumulatives, le processus peut prendre environ 3 heures.  
> - La version de février de StorSimple ne contient pas les mises à jour le périphérique virtuel StorSimple. Vous pouvez toujours appliquer des mises à jour Windows disponibles sur le périphérique virtuel, y compris les correctifs de sécurité récents, mais vous ne verrez pas une modification dans la version pour le périphérique virtuel.  

Assurez-vous que les conditions préalables suivantes sont remplies avant la mise à jour de votre appareil StorSimple.  

- Vérifiez que les deux contrôleurs appareil sont exécutés avant d’analyser les mises à jour. Si aucun contrôleur ne fonctionne pas, l’analyse échouera. Pour vérifier que les contrôleurs sont dans un état correct, accédez à **État du matériel** sous la page de **Maintenance** . S’il existe des composants de ce **qu’il soit nécessaire attention**, contactez le Support Microsoft avant de continuer.
- Assurez-vous que les adresses IP fixe pour à la fois contrôleur de 0 et 1 sont prenant et peuvent se connecter à Internet sont utilisées pour traiter les mises à jour à l’appareil. Vous pouvez utiliser l' [applet de commande de connexion de Test](https://technet.microsoft.com/library/hh849808.aspx) ping sur une adresse connue en dehors du réseau, tels que outlook.com, pour vérifier que le contrôleur a connectivité au réseau externe.
- Vérifiez que les ports 80 et 443 sont disponibles sur votre appareil StorSimple pour les communications sortantes. Pour plus d’informations, voir la [Configuration requise pour votre appareil StorSimple de mise en réseau](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Si la version du logiciel appareil est antérieure à 6.3.9600.17312 (mise à jour octobre 2014), désactivez les ports de données 2 et 3 de données, si activé, avant de commencer la mise à jour. Quitter la Data 2 ou 3 données ports sont activés lorsque vous appliquez la mise à jour peut-être provoquer votre manette appareil passer en mode de récupération. Notez que lorsque vous désactivez les interfaces réseau, tous les volumes associés seront disponibles hors et e/s est interrompue pendant la durée de la mise à jour.  
  
## <a name="whats-new-in-the-february-release"></a>Quelles sont les nouveautés dans la version de février

Cette mise à jour contient un correctif pour usine réinitialisation du problème s’est produite sur les périphériques qui avaient été mis à niveau à partir de la disponibilité générale relâchez vers la version d’octobre 2014. Pour plus d’informations, voir [problèmes résolus dans cette version](#issues-fixed-in-the-february-release).   

Cette mise à jour ne contient pas de nouvelles fonctions ou fonctionnalités.  

## <a name="issues-fixed-in-the-february-release"></a>Problèmes résolus dans la version de février

Le tableau suivant décrit le problème a été résolu dans cette mise à jour.  
 
| Non. | Fonctionnalité | Problème | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Usine par défaut | Vous essayez d’effectuer une usine réinitialisée sur un appareil à l’origine avec la version disponible (version 6.3.9600.17215) installée mais a été mis à jour à l’octobre release (version 6.3.9600.17312). Échec de réinitialisation de l’usine et le périphérique devient instable. | Oui | N° |


## <a name="known-issues-in-the-february-release"></a>Problèmes connus dans la version de février

Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.
 
| Non. | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique au périphérique physique  | S’applique au périphérique virtuel |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Usine par défaut | Dans certains cas, lorsque vous effectuez une réinitialisation par défaut, le périphérique StorSimple peut être bloqué et affiche ce message : **Rétablir par défaut est en cours (phase 8)**. Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours. | N’appuyez sur CTRL + C après le lancement d’une réinitialisation factory. Si vous êtes déjà dans cet état, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 2 | Quorum de disque | Parfois, si la majorité des disques du boîtier EBOD d’un 8600device est déconnectée résultant dans aucun quorum de disque, puis le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont connectés à nouveau. | Vous devrez redémarrez l’appareil. Si le problème persiste, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 3 | Échecs d’instantané cloud | Parfois, un instantané de cloud peut échouer avec l’erreur **maximale sauvegarde atteint**. Cela se produit si vous dépassez 255 clones en ligne sur le même appareil, à partir du même volume d’origine qui a été supprimé. |  | Oui | Oui |
| 4 | ID de contrôleur incorrecte | Lorsqu’un remplacement contrôleur est effectué, contrôleur 0 peut apparaissent sous forme de contrôleur de 1. Pendant le remplacement de contrôleur, lors du chargement de l’image à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme ID. du contrôleur égal Parfois, ce problème peut également se produire après le redémarrage d’un système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même une fois le remplacement contrôleur terminée. | Oui | N° |
| 5 | Dispositif de surveillance des graphiques | Dans le service Gestionnaire StorSimple, les graphiques de surveillance appareil ne fonctionnent pas lors de la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour l’appareil. | Modifier la configuration du proxy web pour le périphérique enregistré auprès de votre service Manager StorSimple afin que l’authentification est définie sur aucun. Pour ce faire, exécutez la Windows PowerShell pour l’applet de commande Set-HcsWebProxy StorSimple. | Oui | Oui |
| 6 | Comptes de stockage | Au moyen du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela vous aboutissez à une situation dans laquelle les données de l’utilisateur ne sont pas accessibles. |  | Oui | Oui |
| 7 | Basculement entre périphériques | Plusieurs basculement d’un conteneur de volume à partir du même périphérique source pour appareils différents cible n’est pas pris en charge.  Basculement à partir d’un seul périphérique inactif sur plusieurs appareils fera les conteneurs de volume sur le premier basculé appareil perdent propriété des données. Après une telle reprise, ces conteneurs volume apparaît ou se comportent différemment lorsque vous affichez les dans le portail classique Azure. |   | Oui | N° |
| 8 | Installation | Au cours de carte StorSimple pour l’installation SharePoint, vous devez fournir une adresse IP appareil afin que l’installation s’exécute correctement. |  | Oui | N° |
| 9 | Proxy Web | Si votre configuration du proxy web a HTTPS comme le protocole spécifié, puis vos communications appareil-service seront affectées et accédera l’appareil mobile en mode hors connexion. Prise en charge des packages seront également générées dans le processus, consomme des ressources importantes sur votre appareil. | Assurez-vous que l’URL du proxy web a HTTP en tant que le protocole spécifié. Plus d’informations sur la façon de [proxy web de configurer pour votre appareil](storsimple-configure-web-proxy.md). | Oui | N° |
| 10 | Proxy Web | Si vous configurez et activez le proxy web sur un appareil enregistré, vous devez redémarrer le contrôleur actif sur votre appareil. |  | Oui | N° |
| 11 | Latence élevée cloud et la charge de travail d’e/s élevé | Lorsque votre appareil StorSimple rencontre une combinaison de latence cloud très haute (ordre des secondes) et la charge de travail d’e/s haute, les volumes appareil coder une dégradation et e/s peut échouer avec une erreur « appareil n’est pas prêt ». | Vous devez redémarrer l’appareil contrôleurs manuellement ou effectuez un basculement appareil pour résoudre ce problème. | Oui | N° |

## <a name="physical-device-updates-in-the-february-release"></a>Mises à jour de périphérique physique dans le février release

Cette mise à jour résout le problème de réinitialisation par défaut qui s’est produite sur les périphériques qui avaient été mis à niveau à partir de la disponibilité générale relâchez vers la version d’octobre 2014. Il ne contient pas les autres mises à jour à l’appareil StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Contrôleur de SCSI (sa) joint série et mises à jour dans le février mise à jour

Cette version ne contient pas les mises à jour le contrôleur SCSI (sa) joint série ou du microprogramme. La mise à jour du pilote lors du octobre, release 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Mises à jour de périphérique virtuel dans le février release

Cette version ne contient pas des mises à jour pour le périphérique virtuel. Appliquer la mise à jour ne modifie pas la version d’un périphérique virtuel.
 
