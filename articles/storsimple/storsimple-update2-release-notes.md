<properties 
   pageTitle="Notes de publication StorSimple 8000 série mise à jour 2 | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, problèmes et solutions de contournement pour StorSimple 8000 série mise à jour 2."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>Notes de publication StorSimple 8000 série mise à jour 2  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et d’identifient les problèmes critiques ouverts pour StorSimple 8000 série mise à jour 2. Ils contiennent également une liste des logiciels StorSimple, pilote et mises à jour du microprogramme disque incluses dans cette version. 

Mise à jour 2 peut être appliqué à n’importe quel appareil StorSimple essayé de mise à jour 1.2 Release (disponibilité générale) ou mise à jour de 0,1. La version de périphérique associée 2 mise à jour est 6.3.9600.17673.

Veuillez consulter les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple.

>[AZURE.IMPORTANT]
> 
- Il faut environ 4-7 heures pour installer cette mise à jour (y compris les mises à jour Windows). 
- Mise à jour 2 a logiciel, pilote LSI et SSD mises à jour.
- De nouvelles versions, vous ne voyez ne peut-être pas mises à jour immédiatement, étant donné que nous avons une mise en place progressive les mises à jour. Patientez quelques jours et puis rechercher les mises à jour à nouveau en tant que celles-ci ne sera bientôt plus disponible.


## <a name="whats-new-in-update-2"></a>Quelles sont les nouveautés dans la mise à jour 2

Mise à jour 2 présente les nouvelles fonctionnalités suivantes.

- **Localement épinglées volumes** – dans les versions précédentes de la série 8000 StorSimple, des blocs de données ont été hiérarchisé vers le cloud basé sur l’utilisation. Il n’a aucun moyen de garantir que blocs restera sur l’ordinateur local. Mise à jour 2, lorsque vous créez un volume, vous pouvez désigner un volume comme données localement épinglées et principales de ce volume ne seront pas en cascade dans le cloud. Afin que le cloud peut servir à des fins données mobilité et urgence récupération des instantanés des volumes localement épinglés sont toujours copiés dans le cloud pour la sauvegarde. En outre, vous pouvez modifier le type de volume (autrement dit, convertir hiérarchisé volumes à des volumes localement épinglés et volumes de convertir localement épinglée à plusieurs niveaux). 

- **Améliorations de périphérique virtuel StorSimple** – précédemment, la série 8000 StorSimple positionné le périphérique virtuel comme une solution d’urgence récupération ou développement/test. Qu’un seul modèle de périphérique virtuel (modèle 1100) s’est produite. Mise à jour 2 présente deux modèles périphérique virtuel : 

     - 8010 (anciennement appelé le 1100) – aucune modification ; a une capacité de 30 To et utilise le stockage standard Azure.
     - 8020 – a une capacité de 64 To et utilise le stockage Azure Premium pour améliorer les performances.

    Il existe un disque dur virtuel unique pour les deux modèles de périphérique virtuel (8010/8020). Lorsque vous commencez tout d’abord le périphérique virtuel, il détecte les paramètres de plateforme et applique la version du modèle correct.

- **Améliorations de mise en réseau** – mise à jour 2 contient les améliorations suivantes de mise en réseau :

     - Plusieurs cartes réseau peut être activé pour le cloud afin que le basculement peut se produire si une carte réseau échoue.
     - Améliorations du routage, avec des mesures fixes pour cloud activé blocs.
     - Réessayer en ligne de ressources échecs avant un basculement.
     - Nouvelles d’alertes pour des échecs de service.

- **Améliorations de la mise à jour** – mise à jour 1,2 et versions antérieures, la série 8000 StorSimple a été mis à jour via deux canaux : mise à jour Windows pour cluster, iSCSI et ainsi de suite et Microsoft Update pour binaires et microprogramme.
    Mise à jour 2 utilise Microsoft Update pour toutes les mises à jour. Cela doit conduire à moins de temps correction ou effectuant basculement. 

- **Mises à jour** – ce qui suit mises à jour sont inclus :
    - LSI : lsi_sas2.sys Version du produit 2.00.72.10
    - SSD (aucune mise à jour du disque dur) : XMGG, XGEG, KZ50, F6C2 et VR08

- **Prise en charge proactive** – mise à jour 2 permet à Microsoft extraire des informations de diagnostic supplémentaires à partir de l’appareil. Notre équipe opérations identifie les périphériques qui rencontrent des problèmes, nous ne mieux équipées pour recueillir des informations à partir de l’appareil et diagnostiquer les problèmes. **En acceptant 2 mise à jour, vous permettez de fournir un ce support dynamique**.    
 

## <a name="issues-fixed-in-update-2"></a>Problèmes résolus dans la mise à jour 2

Le tableau suivant fournit un récapitulatif des problèmes qui ont été corrigés 2 mises à jour.    

| Non. | Fonctionnalité | Problème | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfaces réseau | Après une mise à niveau 1 mise à jour, le service Manager StorSimple a signalé que les ports de données 2 et données 3 a échoué à un seul contrôleur. Ce problème a été résolu. | Oui | N° |
| 2 | Mises à jour | Après une mise à niveau 1 mise à jour, alertes sonore s’est produite dans le portail classique Azure sur plusieurs appareils. Ce problème a été résolu. | Oui | N° |
| 3 | Authentification Openstack | Lorsque vous utilisez Openstack comme fournisseur de services cloud, vous pouvez recevoir un message d’erreur que votre chaîne d’authentification cloud était trop longue. J’ai résolu. | Oui | N° |


## <a name="known-issues-in-update-2"></a>Problèmes connus dans la mise à jour 2

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
| 20 |Volumes localement épinglés | Si vous tentez de convertir un volume hiérarchisé (créé et cloné avec la mise à jour 1.2 ou version antérieur) à un volume localement épinglé et votre appareil est espace insuffisant ou coupure cloud, l’onglet « clone » peut être endommagés.| Ce problème se produit uniquement avec les volumes qui ont été créés et cloné avec préliminaire logiciel mise à jour 2. Il s’agit d’un scénario rare.|
| 21 | Conversion du volume | Ne mettent pas à jour les ACRs joints à un volume pendant la conversion d’un volume (hiérarchisé à épinglées localement, ou inversement). Mise à jour des ACRs peut entraîner une altération des données. | Si nécessaire, mettre à jour les ACRs avant la conversion en volume et ne pas émettre d’autres mises à jour de blocage alors que la conversion est en cours. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Mises à jour contrôleur et microprogramme 2 mise à jour

Cette version met à jour le pilote et le microprogramme de disque sur votre appareil.
 
- Pour plus d’informations sur le microprogramme LSI mettre à jour, voir l’article 3121900 de la base de connaissances Microsoft. 
- Pour plus d’informations sur le microprogramme du disque mise à jour, consultez l’article 3121899 de la base de connaissances Microsoft.
 
## <a name="virtual-device-updates-in-update-2"></a>Mises à jour de périphérique virtuel 2 mise à jour

Cette mise à jour ne peut pas être appliqué au périphérique virtuel. Nouveaux appareils virtuels devra être créé. 

## <a name="next-step"></a>Étape suivante

Découvrez comment [Installer 2 mise à jour](storsimple-install-update-2.md) sur votre appareil StorSimple.
