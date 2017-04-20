<properties 
   pageTitle="Notes de publication StorSimple 8000 série mise à jour 3 | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, problèmes et solutions de contournement pour StorSimple 8000 série mise à jour 3."
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
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>Notes de publication StorSimple 8000 série mise à jour 3  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et d’identifient les problèmes critiques ouverts pour StorSimple 8000 série mise à jour 3. Ils contiennent également une liste les mises à jour du logiciel StorSimple incluses dans cette version. 

Mise à jour 3 peut être appliqué à n’importe quel appareil StorSimple essayé Release (disponibilité générale) ou mise à jour de 0,1 2.2 mise à jour. La version de périphérique associée 3 mise à jour est 6.3.9600.17759.

Veuillez consulter les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple.

>[AZURE.IMPORTANT]
> 
> - Mise à jour 3 a logiciel du périphérique, le pilote LSI et microprogramme et Storport et Spaceport mis à jour. Il faut environ 1,5 à 2 heures pour installer cette mise à jour. 

> - De nouvelles versions, vous ne voyez ne peut-être pas mises à jour immédiatement, étant donné que nous avons une mise en place progressive les mises à jour. Patientez quelques jours et puis rechercher les mises à jour à nouveau en tant que celles-ci ne sera bientôt plus disponible.


## <a name="whats-new-in-update-3"></a>Quelles sont les nouveautés dans la mise à jour 3

Les améliorations clées suivantes et correctifs ont été apportées à la mise à jour 3.

 
- **Modifications de récupération automatique espace** – démarrage mise à jour 3, les algorithmes de récupération espace s’exécuter sur le contrôleur de secours du système résultant s’exécuter plus rapidement. Pour plus d’informations sur les ports qui sont requises pour travailler avec récupération de l’espace, reportez-vous aux [besoins réseau StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

- **Améliorations des performances** – mise à jour 3 a amélioré les performances de lecture / écriture dans le cloud.

- **Améliorations relatives à la migration** – dans cette version, plusieurs correctifs et améliorations ont été apportées pour la fonction de Migration à partir d’appareils de série 5000/7000 8000 périphériques série. Pour plus d’informations sur l’utilisation de la fonctionnalité de migration, accédez à la [Migration d’équipement série 5000/7000 équipement de série 8000](https://www.microsoft.com/en-us/download/details.aspx?id=47322). 

- **Surveillance correctifs** - dans cette version, bogues relatifs à la surveillance des graphiques, tableaux de bord de service et tableau de bord appareil corrigés.


## <a name="issues-fixed-in-update-3"></a>Problèmes résolus dans la mise à jour 3

Le tableau suivant fournit un récapitulatif des problèmes qui ont été corrigés 3 mise à jour.    

| N° | Fonctionnalité                                    | Problème                                                                                                                                                                                                                                                                                        | S’applique au périphérique physique | S’applique au périphérique virtuel |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Migration des données côté hôte                      | Dans la version antérieure, l’application de Cloud StorSimple passait en mode hors connexion lors d’une migration de données côté hôte. Ce problème est résolu dans cette version.                                                | N°                        | Oui                        |
| 2  | Volumes localement épinglés                     | Dans la version précédente, se sont produites problèmes liés aux e/s défaillances, échecs de conversion de volume et datapath pour les volumes localement épinglés. Ces problèmes ont été causé racine et résolus dans cette version.                | Oui                        | N°                       |
| 3  | Surveillance des mots clés                                    | Il existe plusieurs problèmes liés aux reporting unités et de surveillance ainsi que les graphiques de tableau de bord appareil où des informations incorrectes sont affichées pour les volumes localement épinglés. Ces problèmes sont résolus dans cette version. | Oui                         | N°                       |
| 4  | E/s écritures dense                          | Lorsque vous utilisez StorSimple pour charges de travail impliquant écrit dense, l’utilisateur serait rencontrer un bogue rare où le jeu de travail a été en cours hiérarchisé dans le cloud. Ce problème est résolu dans cette version. | Oui                        | Oui                       |
| 5  | Sauvegarde                                     | Dans certains cas rares, dans les versions précédentes du logiciel, lorsque l’utilisateur d’effectuer une sauvegarde d’un cloner à distance, ils s’exécute des erreurs de cloud et l’opération va d’erreur. Dans cette version, le problème est résolu et l’opération terminée avec succès.             | Oui                        | Oui                       |
| 6  | Stratégie de sauvegarde                                     | Dans certains cas rares, dans les versions antérieures du logiciel, a été un bogue lié à la suppression de la stratégie de sauvegarde. Ce problème est résolu dans cette version.       | Oui                        | Oui                       |



## <a name="known-issues-in-update-3"></a>Problèmes connus dans la mise à jour 3

Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.

| Non. | Fonctionnalité | Problème | Commentaires / solution de contournement | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disque | Parfois, si la majorité des disques du boîtier EBOD d’un appareil 8600 est déconnectée résultant dans aucun quorum de disque, puis le pool de stockage passera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont connectés à nouveau. | Vous devrez redémarrez l’appareil. Si le problème persiste, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 2 | ID de contrôleur incorrecte | Lorsqu’un remplacement contrôleur est effectué, contrôleur 0 peut apparaissent sous forme de contrôleur de 1. Pendant le remplacement de contrôleur, lors du chargement de l’image à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme ID. du contrôleur égal Parfois, ce problème peut également se produire après le redémarrage d’un système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même une fois le remplacement contrôleur terminée. | Oui | N° |
| 3 | Comptes de stockage | Au moyen du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela vous aboutissez à une situation dans laquelle les données de l’utilisateur ne sont pas accessibles.|  | Oui | Oui |
| 4 | Basculement entre périphériques | Plusieurs basculement d’un conteneur de volume à partir du même périphérique source pour appareils différents cible n’est pas pris en charge. Basculement à partir d’un seul périphérique inactif sur plusieurs appareils fera les conteneurs de volume sur le premier basculé appareil perdent propriété des données. Après une telle reprise, ces conteneurs volume apparaît ou se comportent différemment lorsque vous affichez les dans le portail classique Azure. | | Oui | N° |
| 5 | Installation | Au cours de carte StorSimple pour l’installation SharePoint, vous devez fournir une adresse IP appareil afin que l’installation s’exécute correctement.    | | Oui | N° |
| 6 | Proxy Web | Si votre configuration du proxy web a HTTPS comme le protocole spécifié, puis vos communications appareil-service seront affectées et accédera l’appareil mobile en mode hors connexion. Prise en charge des packages seront également générées dans le processus, consomme des ressources importantes sur votre appareil. | Assurez-vous que l’URL du proxy web a HTTP en tant que le protocole spécifié. Pour plus d’informations, accédez au [proxy web de configurer pour votre appareil](storsimple-configure-web-proxy.md). | Oui | N° |
| 7 | Proxy Web | Si vous configurez et activez le proxy web sur un appareil enregistré, vous devez redémarrer le contrôleur actif sur votre appareil. | | Oui | N° |
| 8 | Latence élevée cloud et la charge de travail d’e/s élevé | Lorsque votre appareil StorSimple rencontre une combinaison de latence cloud très haute (ordre des secondes) et la charge de travail d’e/s haute, les volumes appareil coder une dégradation et e/s peut échouer avec une erreur « appareil n’est pas prêt ». | Vous devez redémarrer l’appareil contrôleurs manuellement ou effectuez un basculement appareil pour résoudre ce problème. | Oui | N° |
| 9 | PowerShell Azure | Lorsque vous utilisez l’applet de commande StorSimple **Get-AzureStorSimpleStorageAccountCredential & #124 ; Sélectionnez-Object - tout d’abord 1 - attente** pour sélectionner le premier objet afin que vous puissiez créer un nouvel objet **VolumeContainer** , l’applet de commande renvoie tous les objets. | Renvoyer à la ligne l’applet de commande entre parenthèses comme suit : **(Get-Azure-StorSimpleStorageAccountCredential) & #124 ; Sélectionnez-Object - tout d’abord 1 - attente** | Oui | Oui |
| 10| Migration | Lorsque plusieurs conteneurs de volume sont passés pour la migration, l’ATE de sauvegarde de la dernière est précise uniquement pour le premier conteneur de volume. En outre, migration parallèle commence après que les 4 premiers sauvegardes dans le premier conteneur de volume sont migrés. | Nous vous recommandons de migrer un conteneur de volume à la fois. | Oui | N° |
| 11| Migration | Après la restauration, les volumes ne sont pas ajoutés à la stratégie de sauvegarde ou le groupe de disque virtuel. | Vous devrez ajouter ces volumes à une stratégie de sauvegarde afin de créer des sauvegardes. | Oui | Oui |
| 12| Migration | Une fois la migration terminée, le périphérique série 5000/7000 ne doit pas accéder les conteneurs de données migrées. | Nous vous recommandons de supprimer les conteneurs de données migrées une fois la migration terminée et validée. | Oui | N° |
| 13| Cloner et DR | Un périphérique StorSimple 1 mise à jour en cours d’exécution ne peut pas cloner ou effectuer sinistre sur un périphérique en cours d’exécution avant mise à jour 1 logiciel. | Vous devez mettre à jour l’équipement à mettre à jour 1 pour permettre à ces opérations | Oui | Oui |
| 14 | Migration | Sauvegarde de la configuration de migration peut échouer sur un appareil série 5000-7000 lorsqu’il y a des groupes de volumes sans volume associé. | Supprimer tous les groupes de volume vide sans volume associé, puis recommencez la sauvegarde de la configuration.| Oui | N° |
| 15 | Applets de commande PowerShell Azure et volumes localement épinglés | Vous ne pouvez pas créer un volume localement épinglé via les applets de commande PowerShell Azure. (N’importe quel volume que vous créez via PowerShell Azure sera hiérarchisé.) |Toujours utiliser le service Manager StorSimple pour configurer les volumes localement épinglés.| Oui | N° |
| 16 |Espace disponible pour les volumes localement épinglés | Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes ne peut pas être mis à jour immédiatement. Le service Manager StorSimple met à jour l’espace local disponible environ toutes les heures.| Patientez une heure avant d’essayer de créer le nouveau volume. | Oui | N° |
| 17 | Volumes localement épinglés | Votre travail de restauration expose la sauvegarde temporaire instantané dans le catalogue de sauvegarde, mais uniquement pendant la durée de la tâche de restauration. En outre, il expose un groupe de disque virtuel avec préfixe **tmpCollection** dans la page **Stratégies de sauvegarde** , mais uniquement pour la durée de la tâche de restauration. | Ce problème peut se produire si votre tâche restaurer compte uniquement localement épinglé volumes ou un mélange des volumes localement épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention utilisateur est nécessaire. | Oui | N° |
| 18 | Volumes localement épinglés | Si vous annulez un travail de restauration et un basculement contrôleur apparaît immédiatement par la suite, le travail de restauration s’affichent **Échec** au lieu de **annulé**. Si une tâche de restauration échoue et un contrôleur basculement immédiatement par la suite, le travail de restauration s’affichent **annulé** au lieu de **a échoué**. | Ce problème peut se produire si votre tâche restaurer compte uniquement localement épinglé volumes ou un mélange des volumes localement épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce comportement produira pas. Aucune intervention utilisateur est nécessaire. | Oui | N° |
| 19 |Volumes localement épinglés | Si vous annulez une tâche restaurer ou si une restauration échoue, puis un basculement contrôleur, une tâche de restauration supplémentaires s’affiche dans la page **tâches** . | Ce problème peut se produire si votre tâche restaurer compte uniquement localement épinglé volumes ou un mélange des volumes localement épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce comportement produira pas. Aucune intervention utilisateur est nécessaire. | Oui | N° |
| 20 |Volumes localement épinglés | Si vous tentez de convertir un volume hiérarchisé (créé et cloné avec la mise à jour 1.2 ou version antérieur) à un volume localement épinglé et votre appareil est espace insuffisant ou coupure cloud, l’onglet « clone » peut être endommagés.| Ce problème se produit uniquement avec les volumes qui ont été logiciel créé et cloné avec mise à jour partielle 2.1. Il s’agit d’un scénario rare.|
| 21 | Conversion du volume | Ne mettent pas à jour les ACRs joints à un volume pendant la conversion d’un volume (hiérarchisé à épinglées localement, ou inversement). Mise à jour des ACRs peut entraîner une altération des données. | Si nécessaire, mettre à jour les ACRs avant la conversion en volume et ne pas émettre d’autres mises à jour de blocage alors que la conversion est en cours. |
| 22 | Mises à jour | Lorsque vous appliquez la mise à jour 3, la page de **Maintenance** dans le portail classique Azure affiche le message suivant lié à la mise à jour 2 - « série 8000 StorSimple mise à jour 2 inclut la possibilité de Microsoft pour le fait collecter les informations de journal à partir de votre appareil quand nous détecter les problèmes potentiels ». Ceci est erroné car il indique que l’appareil est mis à jour à la mise à jour 2 : Une fois que l’appareil est succeesfully mis à jour pour mettre à jour 3, ce message doit disparaître. | Cela sera corrigé dans une version ultérieure. | Oui | N° |


## <a name="controller-and-firmware-updates-in-update-3"></a>Mises à jour contrôleur et microprogramme 3 mise à jour

Cette version a LSI pilote et microprogramme mises à jour. Pour plus d’informations sur la façon d’installer le pilote LSI et les mises à jour, voir [Installer 3 mise à jour](storsimple-install-update-3.md) sur votre appareil StorSimple.

 
## <a name="virtual-device-updates-in-update-3"></a>Mises à jour de périphérique virtuel 3 mise à jour

Cette mise à jour ne peuvent pas être appliqué à l’application de Cloud StorSimple (également connu sous le périphérique virtuel). Nouveaux appareils virtuels devra être créé. 


## <a name="next-step"></a>Étape suivante

Découvrez comment [Installer 3 mise à jour](storsimple-install-update-3.md) sur votre appareil StorSimple.
