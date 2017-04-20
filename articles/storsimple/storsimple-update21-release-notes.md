<properties 
   pageTitle="Notes de publication StorSimple 8000 série mise à jour 2.2 | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, problèmes et solutions de contournement pour StorSimple 8000 série mise à jour 2.2."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-22-release-notes"></a>Notes de publication StorSimple 8000 série mise à jour 2.2  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et d’identifient les problèmes critiques ouverts pour StorSimple 8000 série mise à jour 2.2. Ils contiennent également une liste les mises à jour du logiciel StorSimple incluses dans cette version. 

Mise à jour 2.2 peut être appliqué à n’importe quel appareil StorSimple essayé Release (disponibilité générale) ou mise à jour de 0,1 2.1 mise à jour. La version de périphérique associée 2.2 mise à jour est 6.3.9600.17708.

Veuillez consulter les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple.

>[AZURE.IMPORTANT]
> 
> - Mise à jour 2.2 a mises à jour uniquement. Il faut environ 1,5 à 2 heures pour installer cette mise à jour. 

> - Si vous exécutez 2.1 mise à jour, nous vous conseillons d’appliquer 2.2 mise à jour dès que possible.

> - De nouvelles versions, vous ne voyez ne peut-être pas mises à jour immédiatement, étant donné que nous avons une mise en place progressive les mises à jour. Patientez quelques jours et puis rechercher les mises à jour à nouveau en tant que celles-ci ne sera bientôt plus disponible.


## <a name="whats-new-in-update-22"></a>Quelles sont les nouveautés dans la mise à jour 2.2

Les améliorations clées suivantes ont été apportées à la mise à jour 2.2.

 
- **Optimisation de récupération automatique espace** – lors de la suppression des données dans les volumes exactement générés, les blocs d’espace de stockage inutilisé doivent être récupérés. Cette version a amélioré le processus de récupération espace à partir du cloud résultant dans l’espace inutilisé devienne disponible plus vite par rapport aux versions précédentes.


- **Améliorations des performances instantané** – mise à jour 2.2 a amélioré le temps de traiter un nuage instantané dans certains scénarios où grands volumes sont utilisés et il est minime à aucune évolution du données. Scénario qui pourraient bénéficier de cette amélioration serait les volumes archive.


- **Collecte de package renforcer de prise en charge** – améliorations dans la manière dont le package de prise en charge est collectée puis téléchargé dans cette version ont été. 


- **Mettre à jour les améliorations de la fiabilité** – cette version a correctifs qui génèrent une fiabilité améliorée de la mise à jour.

  
 

## <a name="issues-fixed-in-update-22"></a>Problèmes résolus dans la mise à jour 2.2

Le tableau suivant fournit un récapitulatif des problèmes qui ont été résolus dans les mises à jour 2.2 et 2.1.    

| N° | Fonctionnalité                                    | Problème                                                                                                                                                                                                                                                                                        | S’applique au périphérique physique | S’applique au périphérique virtuel |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Performances de l’hôte                      | Dans la version antérieure, des problèmes de performances côté hôte ont été observées lors de la création d’un volume localement épinglé et lors de la conversion d’un volume hiérarchisé vers un volume localement épinglé. Ces problèmes sont résolus dans cette version entraînant une amélioration des performances hôte pendant les procédures de création et la conversion du volume.                                                                        | Oui                        | N°                        |
| 2  | Volumes localement épinglés                     | Parfois, le système aurait se bloquer lorsque vous créez un volume localement épinglé. Ce bogue a été résolu dans cette version.                                                                                                                                                               | Oui                        | N°                        |
| 3  | Hiérarchisation                                    | Se sont produites interruptions se bloque lorsque les métadonnées pour les appareils de Cloud StorSimple (8010 et 8020) hiérarchisé vers le cloud. Ce problème est résolu dans cette version.                                                                                                                              | N°                         | Oui                       |
| 4  | Création d’une capture instantanée                          | Des problèmes sont liées à la création d’instantanés incrémentiels dans les scénarios avec des volumes importants et minimale, voire aucune évolution du données. Ces problèmes sont résolus dans cette version.                                                                                                                 | Oui                        | Oui                       |
| 5  | Authentification Openstack                   | Lorsque vous utilisez Openstack comme fournisseur de service cloud, l’utilisateur serait rencontrer un bogue rare lié à l’authentification où l’analyseur JSON a donné lieu un blocage. Ce problème est résolu dans cette version.                                                                                                                              | Oui                        | N°                        |
| 6  | Copie côté hôte                             | Dans les versions antérieures du logiciel, un peu bogues liés à la synchronisation ODX a été visible lorsque vous copiez les données d’un volume vers un autre volume. Ceci crée un basculement contrôleur et le système peut potentiellement passer en mode de récupération. Ce problème est résolu dans cette version. | Oui                        | N°       |
| 7  | Windows Management Instrumentation (WMI) | Dans les versions précédentes du logiciel, il existe plusieurs instances de l’échec du proxy web à l’exception «<ManagementException> Échec de chargement du fournisseur ». Ce bogue a été attribué à une perte de mémoire WMI et est maintenant fixe.                                                               | Oui                        | N°                        |
| 8  | Mise à jour                                     | Dans certains cas rares, dans les versions précédentes du logiciel, l’utilisateur a reçu une « CisPowershellHcsscripterror » lors de l’analyse ou d’installer des mises à jour. Ce problème est résolu dans cette version.                                                                                        | Oui                        | Oui                       |
| 9  | Prise en charge                            | Dans cette version, ont été améliorations apportées à la façon dont le package de prise en charge est collectée et téléchargé.                                                                                                                                                                                                      | Oui                        | Oui                                    |


## <a name="known-issues-in-update-22"></a>Problèmes connus dans 2.2 mise à jour

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
| 18 | Volumes localement épinglés | Si vous annulez un travail de restauration et un basculement contrôleur apparaît immédiatement par la suite, le travail de restauration s’affichent **Échec** au lieu de **annulé**. Si une tâche de restauration échoue et un contrôleur basculement immédiatement par la suite, le travail de restauration s’affichent **annulé** au lieu de **a échoué**. | Ce problème peut se produire si votre tâche restaurer compte uniquement localement épinglé volumes ou un mélange des volumes localement épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention utilisateur est nécessaire. | Oui | N° |
| 19 |Volumes localement épinglés | Si vous annulez une tâche restaurer ou si une restauration échoue, puis un basculement contrôleur, une tâche de restauration supplémentaires s’affiche dans la page **tâches** . | Ce problème peut se produire si votre tâche restaurer compte uniquement localement épinglé volumes ou un mélange des volumes localement épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention utilisateur est nécessaire. | Oui | N° |
| 20 |Volumes localement épinglés | Si vous tentez de convertir un volume hiérarchisé (créé et cloné avec la mise à jour 1.2 ou version antérieur) à un volume localement épinglé et votre appareil est espace insuffisant ou coupure cloud, l’onglet « clone » peut être endommagés.| Ce problème se produit uniquement avec les volumes qui ont été logiciel créé et cloné avec mise à jour partielle 2.1. Il s’agit d’un scénario rare.|
| 21 | Conversion du volume | Ne mettent pas à jour les ACRs joints à un volume pendant la conversion d’un volume (hiérarchisé à épinglées localement, ou inversement). Mise à jour des ACRs peut entraîner une altération des données. | Si nécessaire, mettre à jour les ACRs avant la conversion en volume et ne pas émettre d’autres mises à jour de blocage alors que la conversion est en cours. |

## <a name="controller-and-firmware-updates-in-update-22"></a>Mises à jour contrôleur et microprogramme 2.2 mise à jour

Cette version comporte des mises à jour logicielles uniquement. Toutefois, si vous mettez à jour à partir d’une version antérieure à 2 mise à jour, vous devez installer le pilote, Storport, Spaceport et (dans certains cas) disque mises à jour sur votre appareil.
 
Pour plus d’informations sur la façon d’installer le pilote Storport, Spaceport et mises à jour de microprogramme de disque, voir [Installer 2.2 mise à jour](storsimple-install-update-21.md) sur votre appareil StorSimple.

 
## <a name="virtual-device-updates-in-update-22"></a>Mises à jour de périphérique virtuel dans 2.2 mise à jour

Cette mise à jour ne peut pas être appliqué au périphérique virtuel. Nouveaux appareils virtuels devra être créé. 

## <a name="next-step"></a>Étape suivante

Découvrez comment [Installer 2.2 mise à jour](storsimple-install-update-21.md) sur votre appareil StorSimple.
