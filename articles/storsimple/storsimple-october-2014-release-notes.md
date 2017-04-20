<properties 
    pageTitle="Notes de publication 0,1 mise à jour de 8000 StorSimple | Microsoft Azure"
    description="Décrit les nouvelles fonctionnalités et correctifs, problèmes ouverts et solutions de contournement disponibles pour l’octobre 2014 Microsoft Azure StorSimple release (mise à jour de 0,1)."
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
    ms.date="09/21/2016"
    ms.author="alkohli" />

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notes de publication 0,1 mise à jour de la série StorSimple 8000 – octobre 2014  

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivants identifient les problèmes ouverts critiques pour mettre à jour série StorSimple 8000 0,1 publiée en octobre 2014. Ils contiennent également une liste StorSimple logiciels et microprogramme mises à jour incluses dans cette version. Il s’agit de la première version une fois que la version de StorSimple 8000 série a été faite disponible à partir de juillet 2014 et correspond à la version du logiciel 6.3.9600.17312.  

Nous vous recommandons de rechercher et d’appliquer des mises à jour disponibles immédiatement après l’installation de l’appareil. Vous pouvez également activer les mises à jour automatiques pour télécharger et installer les mises à jour avec une priorité élevée à partir de Microsoft dès qu’elles sont disponibles. Pour plus d’informations, voir comment [mettre à jour votre appareil StorSimple](storsimple-update-device.md).  

Veuillez consulter les informations contenues dans les notes de publication avant de déployer les mises à jour dans votre solution StorSimple.  

>[AZURE.IMPORTANT]
> 
-   Utiliser le service Manager StorSimple et pas Windows PowerShell pour StorSimple pour installer les mises à jour d’octobre.  
-   Les mises à jour généralement prennent environ 3 heures.  
-   La version d’octobre de StorSimple ne contient pas les mises à jour le périphérique virtuel StorSimple. Vous pouvez toujours appliquer des mises à jour Windows disponibles, y compris les correctifs de sécurité récents, mais vous ne verrez pas une modification dans la version pour le périphérique virtuel.  

Vérifiez que les conditions préalables suivantes sont remplies avant la mise à jour de votre appareil StorSimple.  

- Vérifiez que les deux contrôleurs appareil sont exécutés avant d’analyser les mises à jour. Si aucun contrôleur ne fonctionne pas, l’analyse échouera. Pour vérifier que les contrôleurs sont dans un état correct, accédez à **État du matériel** sous la page de **Maintenance** . S’il existe des composants de ce **qu’il soit nécessaire attention**, contactez le Support Microsoft avant de continuer.  
- Assurez-vous que les adresses IP fixe pour les deux contrôleur de 0 et 1 contrôleur sont prenant et peuvent se connecter à Internet sont utilisées pour traiter les mises à jour à l’appareil. Vous pouvez utiliser l' [applet de commande de connexion de Test](https://technet.microsoft.com/library/hh849808.aspx) ping sur une adresse connue en dehors du réseau tels que outlook.com, pour vérifier que le contrôleur a connectivité au réseau externe.  
- Vérifiez que les ports sortants requis sont disponibles sur votre appareil StorSimple pour les communications sortantes. Pour plus d’informations, voir la [Configuration requise pour votre appareil StorSimple de mise en réseau](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Si la version du logiciel appareil est antérieure à 6.3.9600.17312 (mise à jour octobre 2014), désactivez les ports de données 2 et 3 de données, si activé, avant de commencer la mise à jour. Si vous laissez les données 2 ou 3 données ports sont activés lorsque vous appliquez la mise à jour, il peut entraîner votre manette appareil passer en mode de récupération. Notez que lorsque vous désactivez les interfaces réseau, tous les volumes associés seront disponibles hors et e/s est interrompue pendant la durée de la mise à jour.  

## <a name="whats-new-in-the-october-release"></a>Quelles sont les nouveautés dans la version d’octobre

Cette mise à jour inclut les améliorations suivantes :

- Vous pouvez désormais utiliser le service du Gestionnaire de StorSimple l’interface utilisateur pour gérer les contrôleurs de votre appareil. La gestion des actions incluent redémarrer, arrêt, ou activez un contrôleur. Pour plus d’informations, accédez à [contrôleurs de périphérique StorSimple gérer](storsimple-manage-device-controller.md).  
- Vous pouvez planifier l’allocation de bande passante réseau étendu en fonction des combinaisons de jour de la semaine et heure de la journée. Cela vous permet de mieux utiliser la bande passante WAN en dehors des heures. Modèles de bande passante différentes sont autorisées pour les conteneurs de volume différent. Pour plus d’informations, accédez à [gérer vos modèles de bande passante StorSimple](storsimple-manage-bandwidth-templates.md).  
- Vous pouvez configurer les notifications par courrier électronique pour informer les administrateurs et autres personnes des problèmes existants ou éventuellement à venir. Pour plus d’informations, accédez à [paramètres d’alerte configurer](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problèmes résolus dans la version d’octobre


Le tableau suivant fournit un récapitulatif des problèmes qui ont été corrigés dans cette mise à jour.  

| Non. | Fonctionnalité | Problème | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces réseau | Dans la version précédente, les interfaces réseau DATA 2 et 3 de données ont été échangées du logiciel. Ce problème a été corrigé dans cette mise à jour. Désactivez les paramètres et les désactiver ces interfaces réseau avant d’installer la mise à jour. Après avoir installé la mise à jour, vous devrez reconfigurer ces interfaces. | Oui | N° |
| 2 | Prise en charge | Dans la version précédente, si vous avez exécuté l’applet de commande Windows PowerShell **Exportation HcsSupportPackage** pour récupérer les journaux contrôleur de gestion de carte de base (BMC), l’opération a échoué avec l’avertissement suivant : « l’opération a réussi sur ce contrôleur, mais a échoué sur le contrôleur homologue en raison des erreurs suivantes. Vérifiez que l’homologue est correct et si le nœud actuel peut se connecter à l’homologue. » Ce problème est résolu maintenant. | Oui | N° |
| 3 | Basculement entre périphériques | Dans la version précédente, il a été une chance d’incohérence des données si un travail **découvrir la sauvegarde** a échoué lors d’un basculement de l’appareil. Ce problème est résolu maintenant. | Oui | N° |
| 4 | Basculement entre périphériques | Dans la version précédente, après un basculement appareil, sauvegardes ont été visibles mais le conteneur volume associé n’était pas présent sur le périphérique cible. Ce problème est résolu maintenant. | Oui | N° |
| 5 | Basculement entre périphériques | Dans la version précédente, il a été un bogue dans l’énumération des sauvegardes cloud pendant l’opération de restauration de Registre qui pourrait conduire incohérence des données s’il y a des problèmes de connectivité cloud. | Oui | N° |
| 6 | Mise à jour | Dans la version précédente, la tâche de mise à jour de microprogramme appareil a échoué et affiche une erreur qui précisé que les applets de commande n’ont pas étaient reconnaissables et que la mise à jour a échoué en conséquence. Le contrôleur est entré puis en mode de récupération. Ce problème est résolu maintenant. | Oui | N° |
| 7 | Installation | Erreurs dues à l’appareil n’est ne pas correctement image lors de l’installation ont été résolus. | Oui | N° |
| 8 | Usine par défaut | Vous pouvez ignorer éventuellement la vérification du microprogramme pour usine par défaut. Il s’agit d’une modification de la version précédente. | Oui | N° |
| 9 | Usine par défaut | Dans la version précédente, lors de l’exécution d’une applet de commande de réinitialisation en usine, contrôles de version microprogramme ont été apportées uniquement pour certains composants matériels. Tests de microprogramme supplémentaires ont été apportées après le premier redémarrage dans le processus, ce qui peut entraîner la réinitialisation échec. Ce correctif garantit que tous les tests microprogramme sont effectuées lorsque l’usine réinitialiser applet de commande est exécuté et avant le premier système redémarrer. | Oui | N° |
| 10 | Rotation clée de compte de stockage | L’applet de commande **Appeler HcsmServiceDataEncryptionKeyChange** utilisée pour faire pivoter les clés de compte de stockage maintenant vous invite à l’utilisateur à entrer la clé de chiffrement des données de service. Il s’agit d’une modification de la version précédente dans lequel la clé de chiffrement des données de service a été passée comme paramètre en ligne. | Oui | N° |
| 11 | Retour arrière dans les 24 heures | Pendant la restauration d’urgence, le nettoyage de disque sur le périphérique source arriver pas correctement, à l’origine retour arrière échec. Ce problème a été corrigé dans cette version. | Oui | N° |

## <a name="known-issues-in-the-october-release"></a>Problèmes connus dans la version d’octobre

Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.

| Non. | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Usine par défaut | Dans certains cas, lorsque vous effectuez une réinitialisation par défaut, le périphérique StorSimple peut être bloqué et affiche ce message : **Rétablir par défaut est en cours (phase 8)**. Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours. | N’appuyez sur CTRL + C après le lancement d’une réinitialisation factory. Si vous êtes déjà dans cet état, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 2 | Usine par défaut | Effectuez pas usine réinitialiser un appareil StorSimple qui est mis à jour à partir de disponibilité générale octobre 2014 relâchez. | Cette opération ne fonctionnera que si un correctif est installé. Contactez le Support Microsoft pour obtenir ce correctif requis. | Oui | N° | 
| 3 | Quorum de disque | Parfois, si la majorité des disques du boîtier EBOD d’un appareil 8600 est déconnectée résultant dans aucun quorum de disque, puis le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont connectés à nouveau. | Vous devrez redémarrez l’appareil. Si le problème persiste, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 4 | Échecs d’instantané cloud | Parfois, un instantané de cloud peut échouer avec l’erreur **maximale sauvegarde atteint**. Cela se produit si vous dépassez 255 clones en ligne sur le même appareil, à partir du même volume d’origine qui a été supprimé. | | Oui | Oui |
| 5 | ID de contrôleur incorrecte | Lorsqu’un remplacement contrôleur est effectué, contrôleur 0 peut apparaissent sous forme de contrôleur de 1. Pendant le remplacement de contrôleur, lors du chargement de l’image à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme ID. du contrôleur égal Parfois, ce problème peut également se produire après le redémarrage d’un système. |Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même une fois le remplacement contrôleur terminée. | Oui | N° |
| 6 | Dispositif de surveillance des graphiques | Dans le service Gestionnaire StorSimple, les graphiques de surveillance appareil ne fonctionnent pas lors de la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour l’appareil. | Modifier la configuration du proxy web pour le périphérique enregistré auprès de votre service Manager StorSimple afin que l’authentification est définie sur aucun. Pour ce faire, exécutez la Windows PowerShell pour l’applet de commande Set-HcsWebProxy StorSimple. | Oui | Oui |
| 7 | Comptes de stockage | Au moyen du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela vous aboutissez à une situation dans laquelle les données de l’utilisateur ne sont pas accessibles. | | Oui | Oui |
| 8 | Basculement entre périphériques | Plusieurs basculement d’un conteneur de volume à partir du même périphérique source pour appareils différents cible n’est pas pris en charge. | Basculement à partir d’un seul périphérique inactif sur plusieurs appareils fera les conteneurs de volume sur le premier basculé appareil perdent propriété des données. Après une telle reprise, ces conteneurs volume apparaît ou se comportent différemment lorsque vous affichez les dans le portail classique Azure. | Oui | N° |
| 9 | Installation | Au cours de carte StorSimple pour l’installation SharePoint, vous devez fournir une adresse IP appareil afin que l’installation s’exécute correctement.    | | Oui | N° |
| 10 | Proxy Web | Si votre configuration du proxy web a HTTPS comme le protocole spécifié, puis vos communications appareil-service seront affectées et accédera l’appareil mobile en mode hors connexion. Prise en charge des packages seront également générées dans le processus, consomme des ressources importantes sur votre appareil. | Assurez-vous que l’URL du proxy web a HTTP en tant que le protocole spécifié. Plus d’informations sur la façon de [proxy web de configurer pour votre appareil](storsimple-configure-web-proxy.md). | Oui | N° |
| 11 | Proxy Web | Si vous configurez et activez le proxy web sur un appareil enregistré, vous devez redémarrer le contrôleur actif sur votre appareil. | | Oui | N° |
| 12 | Latence élevée cloud et la charge de travail d’e/s élevé | Lorsque votre appareil StorSimple rencontre une combinaison de latence cloud très haute (ordre des secondes) et la charge de travail d’e/s haute, les volumes appareil coder une dégradation et e/s peut échouer avec une erreur « appareil n’est pas prêt ». | Vous devez redémarrer l’appareil contrôleurs manuellement ou effectuez un basculement appareil pour résoudre ce problème. | Oui | N° |

## <a name="physical-device-updates-in-the-october-release"></a>Mises à jour de périphérique physique dans l’octobre release

Lorsque ces mises à jour sont appliquées à un périphérique physique, la version du logiciel remplacera par 6.3.9600.17312. Sauf indication contraire, ces notes de publication s’appliquent à tous les modèles de l’appareil StorSimple. Pour plus d’informations sur ces mises à jour, voir [octobre 2014 physique logicielle mise à jour pour Microsoft Azure StorSimple matériel](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Contrôleur de SCSI (sa) joint série et mises à jour d’octobre à la mise à jour

Cette version met à jour le pilote et le microprogramme sur le contrôleur associations de sécurité de votre périphérique physique. Pour plus d’informations sur le contrôleur de sa mise à jour, voir [octobre 2014 mettre à jour pour les associations de sécurité LSI contrôleurs dans Microsoft Azure StorSimple matériel](http://support.microsoft.com/kb/2987020).   

Cette version s’applique également une mise à jour cumulative microprogramme afin de corriger les problèmes de fiabilité avec les composants matériels du périphérique. Pour plus d’informations sur la mise à jour de microprogramme, voir [microprogramme octobre 2014 mise à jour pour Microsoft Azure StorSimple matériel](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Mises à jour de périphérique virtuel dans l’octobre release

Cette version ne contient pas des mises à jour pour le périphérique virtuel. Appliquer la mise à jour ne modifie pas la version d’un périphérique virtuel.
 
