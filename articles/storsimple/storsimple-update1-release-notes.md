<properties 
   pageTitle="Notes de mise à jour série StorSimple 8000 1.2 | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, problèmes et solutions de contournement pour StorSimple 8000 série mise à jour 1.2."
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-12-release-notes"></a>Notes de mise à jour série StorSimple 8000 1.2  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et d’identifient les problèmes critiques ouverts pour StorSimple 8000 série mise à jour 1.2. Ils contiennent également une liste des logiciels StorSimple, pilote et mises à jour du microprogramme disque incluses dans cette version. 

Mise à jour 1.2 peut être appliqué à n’importe quel appareil StorSimple Release (disponibilité générale), mise à jour de 0,1, mise à jour 0,2 ou mettre à jour 0,3 logiciel en cours d’exécution. Mise à jour 1.2 n’est pas disponible si votre appareil exécute 1 mise à jour ou mise à jour 1.1. Si votre appareil est en cours d’exécution Release (disponibilité générale), veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à installer cette mise à jour.

Le tableau suivant répertorie les versions du logiciel appareil correspondant aux mises à jour 1, 1.1 et 1.2.

| Si la mise à jour en cours d’exécution... | Il s’agit de votre version du logiciel appareil. |
|---------------------|---------------------------------------|
| Mise à jour 1.2          | 6.3.9600.17584                        |
| Mettre à jour 1.1          | 6.3.9600.17521                        |
| Mise à jour 1.0          | 6.3.9600.17491                        |

Veuillez consulter les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple. Pour plus d’informations, voir comment [Installer 1.2 mise à jour sur votre appareil StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Il faut environ 5 à 10 heures pour installer cette mise à jour (y compris les mises à jour de Windows). 
- Mise à jour 1.2 a logiciel, pilote LSI et mises à jour de microprogramme de disque. Pour installer, suivez les instructions [d’installation 1.2 mise à jour sur votre appareil StorSimple](storsimple-install-update-1.md).
- De nouvelles versions, vous ne voyez ne peut-être pas mises à jour immédiatement, étant donné que nous avons une mise en place progressive les mises à jour. Rechercher les mises à jour en quelques jours à nouveau que celles-ci ne sera bientôt plus disponible.


## <a name="whats-new-in-update-12"></a>Quelles sont les nouveautés dans la mise à jour 1.2

Les fonctionnalités suivantes ont été publiées tout d’abord à 1 mise à jour a été mis à disposition pour un ensemble d’utilisateurs restreint. Avec la version 1.2 mise à jour, la plupart des utilisateurs StorSimple s’affichent les nouvelles fonctionnalités suivantes et les améliorations :

- **Migration de 5000-7000 en série 8000 périphériques série** – cette version présente une nouvelle fonctionnalité de migration qui permet le StorSimple 5000-7000 série matériel aux utilisateurs de migrer leurs données à un appareil physique de série 8000 StorSimple ou à un appareil virtuel. La fonctionnalité de migration comporte deux principaux atouts :                                                                  

    - **Continuité des activités**, grâce à la migration des données existantes sur les appareils de série 5000-7000 pour appareils 8000 série.
    - **Offres de fonctionnalité améliorée des appareils série 8000**, telles que la gestion centralisée efficace de plusieurs appareils via le service Manager StorSimple, mieux cours du matériel et mises à jour de microprogramme, authentification multifacteur, mobilité des données et fonctionnalités dans la feuille de route future.

    Consultez le [guide de migration](http://www.microsoft.com/download/details.aspx?id=47322) pour plus d’informations sur la migration d’une série de 5000-7000 StorSimple sur un appareil série 8000. 

- **Disponibilité dans le portail pour le gouvernement Azure** – StorSimple est désormais disponible dans le portail pour le gouvernement Azure. Voir comment [déployer un appareil StorSimple dans le portail pour le gouvernement Azure](storsimple-deployment-walkthrough-gov.md).

- **Prise en charge des autres fournisseurs de services de cloud** – les autres fournisseurs de services cloud pris en charge sont Amazon S3, S3 Amazon avec les enregistrements de ressource, HP et OpenStack (version bêta).

- **Mise à jour de dernière API de stockage** – avec cette version, StorSimple a été mis à jour au service de stockage Azure dernière API. Appareils série 8000 StorSimple qui exécutent des versions de logiciels de mise à jour partielle 1 (version finale, 0,1, 0,2 et 0,3) utilisent des versions de l’API de Service de stockage Azure antérieurs à 17 juillet 2009. Comme indiqué dans [l’annonce sur suppression des versions de service de stockage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)de mise à jour, par 1 août 2016, ces API est déconseillé. Vous devez impérativement que vous appliquez la mise à jour série StorSimple 8000 1 avant le 1 août 2016. Si vous ne parvenez pas à le faire, appareils StorSimple cesse de fonctionner correctement.

- **Prise en charge pour la Zone redondants stockage (ZRS)** – avec la mise à niveau vers la dernière version de l’API de stockage, la série 8000 StorSimple prend en charge les zones redondantes stockage (ZRS) en plus de stockage localement redondantes (LRS) et stockage Geo redondantes (GRS). Consultez cet [article sur les options de redondance de stockage Azure](../storage/storage-redundancy.md) pour plus d’informations ZRS.

- **Améliorer l’expérience de déploiement et de mise à jour initiale** – dans cette version, l’installation et le processus de mise à jour ont été améliorées. L’installation de l’Assistant Configuration a été améliorée afin de fournir des commentaires à l’utilisateur si les paramètres de pare-feu et la configuration réseau sont incorrects. Applets de commande de Diagnostics supplémentaires ont été fournies pour vous aider à résoudre les problèmes réseau du périphérique. Voir l' [article de déploiement de résolution des problèmes](storsimple-troubleshoot-deployment.md) pour plus d’informations sur les nouvelles applets de commande diagnostic utilisé pour le dépannage.

## <a name="issues-fixed-in-update-12"></a>Problèmes résolus dans la mise à jour 1.2

Le tableau suivant fournit un récapitulatif des problèmes qui ont été résolus dans les mises à jour 1.2, 1.1 et 1.    


| Non. | Fonctionnalité | Problème | Mise à jour fixe dans | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell pour StorSimple | Lorsqu’un utilisateur accessible à distance le périphérique StorSimple à l’aide de Windows PowerShell pour StorSimple, puis démarrer l’Assistant de configuration, un blocage s’est produite dès que les données 0 IP a été tapé. Cette erreur est maintenant corrigée 1 mise à jour. | Mise à jour 1 | Oui | Oui |
| 2 | Usine par défaut | Dans certains cas, lorsque vous avez effectué une réinitialisation par défaut, le périphérique StorSimple a été bloqué et affiche ce message : **Rétablir par défaut est en cours (phase 8)**. Ceci s’est produit si vous avez appuyé sur CTRL + C lors de l’applet de commande en cours. Cette erreur est corrigée maintenant.| Mise à jour 1 | Oui | N° |
| 3 | Usine par défaut | Après la réinitialisation d’une usine échec contrôleur double, vous ont été autorisé à procéder à l’enregistrement du périphérique. Cela a donné lieu à une configuration système non prises en charge. Mise à jour 1, un message d’erreur s’affiche et l’enregistrement est bloqué sur un appareil qu’a une usine échec réinitialisé. | Mise à jour 1 | Oui | N° |
| 4 | Usine par défaut | Dans certains cas, alertes incompatibilité positif FAUX ont été élevés. Alertes incompatibilité incorrecte seront n’est plus générés sur les appareils exécutant 1 mise à jour. | Mise à jour 1 | Oui | N° |
| 5 | Usine par défaut | Si une réinitialisation usine a été interrompue avant la fin, le périphérique entré en mode de récupération et vous n'a pas autorisé à accéder à Windows PowerShell pour StorSimple. Cette erreur est corrigée maintenant. | Mise à jour 1 | Oui | N° |
| 6 | Récupération d’urgence | Un incident reprise a été bogue dans laquelle DR échoue lors de la découverte des sauvegardes sur le périphérique cible. | Mise à jour 1 | Oui | Oui |
| 7 | Surveillance voyants | Dans certains cas, la surveillance voyants à l’arrière de matériel n’indique pas état correct. Le LED bleu a été désactivé. DONNÉES 0 et 1 voyants de données ont été signaux même lorsque ces interfaces n’étaient pas configurés. Le problème a été résolu et voyants surveillance maintenant indiquent l’état correct.  | Mise à jour 1 | Oui | N° |
| 8 | Surveillance voyants | Dans certains cas, après avoir appliqué la mise à jour 1, la lumière bleue sur le contrôleur active désactivé ce qui le rend difficile à identifier le contrôleur actif. Ce problème a été résolu dans cette version de correctif.| Mise à jour 1.2 | Oui | N° |
| 9 | Interfaces réseau | Dans les versions antérieures, un appareil StorSimple configuré avec une passerelle non prenant pourrait hors ligne. Dans cette version, la mesure du routage de données 0 ont été apportées à la plus basse ; Par conséquent, même si d’autres interfaces réseau sont compatibles avec les cloud, tout le trafic de cloud à partir du périphérique est routé via données 0. | Mise à jour 1 | Oui | Oui | 
| 10 | Effectuer des sauvegardes régulières | Un bogue 1 mise à jour qui a provoqué des sauvegardes échec après 24 jours a été résolu dans la version de correctif 1.1 mise à jour. | Mettre à jour 1.1 | Oui | Oui |
| 11 | Effectuer des sauvegardes régulières | Un bogue dans les versions précédentes a donné lieu à une baisse des performances pour des instantanés cloud avec taux de modification faible. Ce bogue a été corrigé dans cette version de correctif.| Mise à jour 1.2 | Oui | Oui |
| 12 | Mises à jour | Un bogue 1 mise à jour qui a signalé un échec de la mise à niveau et qui a provoqué les contrôleurs passer en mode de récupération a été résolu dans cette version de correctif.| Mise à jour 1.2 | Oui | Oui |


## <a name="known-issues-in-update-12"></a>Problèmes connus dans la mise à jour 1.2

Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.

| Non. | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disque | Parfois, si la majorité des disques du boîtier EBOD d’un appareil 8600 est déconnectée résultant dans aucun quorum de disque, puis le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont connectés à nouveau. | Vous devrez redémarrez l’appareil. Si le problème persiste, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 2 | ID de contrôleur incorrecte | Lorsqu’un remplacement contrôleur est effectué, contrôleur 0 peut apparaissent sous forme de contrôleur de 1. Pendant le remplacement de contrôleur, lors du chargement de l’image à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme ID. du contrôleur égal Parfois, ce problème peut également se produire après le redémarrage d’un système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même une fois le remplacement contrôleur terminée. | Oui | N° |
| 3 | Comptes de stockage | Au moyen du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela vous aboutissez à une situation dans laquelle les données de l’utilisateur ne sont pas accessibles. | Oui | Oui |
| 4 | Basculement entre périphériques | Plusieurs basculement d’un conteneur de volume à partir du même périphérique source pour appareils différents cible n’est pas pris en charge. Basculement entre les périphériques à partir d’un seul périphérique inactif à plusieurs appareils fera les conteneurs de volume sur le premier basculé appareil perdent propriété des données. Après une telle reprise, ces conteneurs volume apparaît ou se comportent différemment lorsque vous affichez les dans le portail classique Azure. | | Oui | N° |
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

## <a name="physical-device-updates-in-update-12"></a>Mises à jour de périphérique physique dans 1.2 mise à jour

Si le correctif de mise à jour 1.2 est appliqué à un périphérique physique (en exécutant les versions antérieures à la mise à jour 1), la version du logiciel remplacera par 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Contrôleur et microprogramme mises à jour dans la mise à jour 1.2

Cette version met à jour le pilote et le microprogramme de disque sur votre appareil.
 
- Pour plus d’informations sur la mise à jour du contrôleur associations de sécurité, voir [mise à jour 1 pour les associations de sécurité LSI contrôleurs dans Microsoft Azure StorSimple matériel](https://support.microsoft.com/kb/3043005). 

- Pour plus d’informations sur le microprogramme du disque mettre à jour, voir [microprogramme de disque mise à jour 1 pour Microsoft Azure StorSimple matériel](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Mises à jour de périphérique virtuel dans 1.2 mise à jour

Cette mise à jour ne peut pas être appliqué au périphérique virtuel. Nouveaux appareils virtuels devra être créé. 

## <a name="next-steps"></a>Étapes suivantes

- [Installer la mise à jour 1.2 sur votre appareil](storsimple-install-update-1.md).
 
