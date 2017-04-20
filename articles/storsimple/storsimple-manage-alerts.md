<properties 
   pageTitle="Afficher et gérer les alertes StorSimple | Microsoft Azure"
   description="Décrit les conditions d’alerte StorSimple et gravité, comment configurer les notifications d’alerte et comment utiliser le service StorSimple Manager pour gérer les alertes."
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
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Utiliser le service Manager StorSimple pour afficher et gérer les alertes StorSimple

## <a name="overview"></a>Vue d’ensemble

L’onglet **alertes** dans le service Manager StorSimple fournit une méthode pour examiner et désactivez les alertes liées appareil StorSimple en temps réel. À partir de cet onglet, vous pouvez centraliser l’analyse les problèmes d’intégrité de vos périphériques StorSimple et la solution Microsoft Azure StorSimple globale.

Ce didacticiel décrit les conditions d’alerte courantes, niveaux de gravité d’alerte et comment configurer les notifications d’alerte. En outre, il inclut les tables aide-mémoire alerte, qui vous permettent de localiser une alerte spécifique et répondre de façon appropriée.

![Page d’alertes](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Conditions d’alerte courantes

Votre appareil StorSimple génère des alertes en réponse à une variété de conditions. Voici les conditions d’alerte les plus courants :

- **Problèmes liés au matériel** – ces alertes vous indiquent concernant l’état de votre matériel. Ils vous permettent de savoir si la mise à jour est nécessaires, si une interface réseau rencontre des problèmes ou s’il existe un problème avec l’un de vos lecteurs de données.

- **Problèmes de connectivité** – ces alertes se produisent lorsqu’il y a des difficultés à transférer des données. Problèmes de communication peuvent se produire lors du transfert de données et à partir du compte de stockage Azure ou en raison de l’absence de connectivité entre les périphériques et le service Manager StorSimple. Problèmes de communication sont les plus difficiles à résoudre, car il existe des points autant de panne. Vous devez d’abord toujours vérifier que la connectivité réseau et accès à Internet sont disponibles avant de passer à la résolution des problèmes plus avancées. Pour vous aider à résoudre, accédez à [résoudre les problèmes avec l’applet de commande tester la connexion](storsimple-troubleshoot-deployment.md).

- **Problèmes de performances** – ces alertes peuvent se présenter lorsque votre système n’est pas des performances optimales, par exemple lorsqu’il est surchargé.

En outre, vous pouvez voir des alertes liées à la sécurité, les mises à jour ou les échecs des tâches.

## <a name="alert-severity-levels"></a>Niveaux de gravité d’alerte

Alertes ont différents niveaux de gravité, en fonction de l’impact de la situation alerte et qu’il soit nécessaire d’une réponse à l’alerte. Les niveaux de gravité sont :

- **Critique** : cette alerte est en réponse à une condition qui affecte les performances de votre système réussie. Action est requise pour vous assurer que la StorSimple service n’est pas interrompu.

- **Avertissement** – cette condition peut devenir critique si ne pas résolu. Vous devez examiner la situation et aucune action nécessaire pour effacer le problème.

- Des **informations** : cette alerte contient des informations qui peuvent être utiles dans leur suivi et gestion de votre système.

## <a name="configure-alert-settings"></a>Configurer les paramètres d’alerte

Vous pouvez choisir si vous souhaitez être informé par courrier électronique de conditions d’alerte pour chacun de vos périphériques StorSimple. En outre, vous pouvez identifier les autres destinataires de notification d’alerte en entrant leur adresse de messagerie dans la zone **autres destinataires de messagerie** , séparée par des points-virgules.

>[AZURE.NOTE] Vous pouvez entrer un maximum de 20 adresses de messagerie par périphérique.

Après avoir activé la notification par courrier électronique pour un appareil, les membres de la liste de notification recevront un message électronique chaque fois qu’une alerte critique survient. Les messages seront envoyés à partir de *storsimple-alerts-noreply@mail.windowsazure.com* et décrira la condition d’alerte. Destinataires peuvent cliquer sur **Annuler l’abonnement** pour se supprimer de la liste de notifications de messagerie.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Pour activer la notification par courrier électronique des alertes pour un appareil

1. Accédez à **appareils** > **configurer** pour l’appareil.

2. Sous **Paramètres d’alerte**, définissez les options suivantes :

    1. Dans le champ de **notification par courrier électronique Envoyer** , sélectionnez **Oui**.

    2. Dans le champ **administrateurs de service de messagerie** , sélectionnez **Oui** si vous souhaitez que l’administrateur de service et les administrateurs de co-création reçoivent les notifications d’alerte.

    3. Dans le champ **autres destinataires de messagerie** , entrez les adresses de messagerie de tous les autres destinataires devant recevoir les notifications d’alerte. Entrez des noms dans le format *someone@somewhere.com*. Utilisez des points-virgules pour séparer les adresses de messagerie. Vous pouvez configurer un maximum de 20 adresses de messagerie par périphérique. 

        ![Configuration de la notification alertes](./media/storsimple-manage-alerts/AlertNotify.png)

3. Pour envoyer une notification par courrier électronique de test, cliquez sur la flèche en regard de **test envoyer un courrier électronique**. Le service Manager StorSimple affichera les messages d’état comme il transfère la notification de test. 

4. Lorsque le message suivant s’affiche, cliquez sur **OK**. 

    ![Alertes tester la messagerie électronique de notification envoyée](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Si le message de notification de test ne peut pas être envoyé, le service Manager StorSimple affiche un message approprié. Cliquez sur **OK**, patientez quelques minutes et réessayez d’envoyer votre message de notification de test. 

## <a name="view-and-track-alerts"></a>Afficher et effectuer le suivi des alertes

Le tableau de bord de service Manager StorSimple vous permet d’un coup de œil rapide au nombre d’alertes sur vos appareils, organisés par niveau de gravité.

![Tableau de bord alertes](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Cliquez sur le niveau de gravité pour ouvrir l’onglet **alertes** . Les résultats comprennent uniquement les alertes qui correspondent à ce niveau de gravité.

![Rapport alertes relatif au type d’alerte](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

En cliquant sur une alerte dans la liste, vous fournit des détails supplémentaires pour l’alerte, y compris la dernière fois que l’alerte a été signalé, le nombre d’occurrences de l’alerte sur le périphérique et l’action recommandée pour résoudre l’alerte. S’il s’agit d’une alerte matériel, elle identifie également le composant matériel.

![Exemple d’alerte sur le matériel](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Si vous avez besoin envoyer des informations au Support Microsoft, vous pouvez copier les détails de l’alerte vers un fichier texte. Une fois que vous avez suivi les recommandations et résolu la condition d’alerte en local, vous devez désactiver l’alerte de l’appareil en sélectionnant l’alerte dans l’onglet **alertes** et en cliquant sur **Effacer**. Pour supprimer plusieurs alertes, sélectionnez chaque alerte et cliquez sur n’importe quelle colonne à l’exception de la colonne **alerte** , puis cliquez sur **Effacer** après avoir sélectionné toutes les alertes à effacer. Notez que certaines alertes sont désactivées automatiquement lorsque le problème est résolu ou que les mises à jour l’alerte avec les nouvelles informations.

Lorsque vous cliquez sur **Effacer**, vous aurez la possibilité pour fournir des commentaires sur l’alerte et les étapes que vous avez prises pour résoudre le problème. Certains événements seront désactivées par le système si un autre événement se déclenche avec les nouvelles informations. Dans ce cas, vous verrez le message suivant.

![Message d’alerte effacer](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Alertes de tri et de révision

Vous trouverez peut-être plus efficace d’exécuter des rapports sur alertes afin que vous pouvez consulter et les supprimer en groupes. En outre, l’onglet **alertes** peut afficher jusqu'à 250 alertes. Si vous avez dépassé le nombre d’alertes, pas toutes les alertes seront affichera dans l’affichage par défaut. Vous pouvez combiner les champs suivants pour personnaliser les alertes qui sont affichent :

- **État** – vous pouvez afficher les alertes **actif** ou **désactivé** . Active les alertes sont toujours en cours déclenchées sur votre système, tandis que les alertes désactivées ont été désactivées manuellement par un administrateur ou désactivées par programme, car le système mis à jour la condition d’alerte avec les nouvelles informations.

- **Gravité** – vous pouvez afficher les alertes de tous les niveaux de gravité (critiques, avertissements, informations), ou simplement une certaine gravité, telles que les alertes critiques uniquement.

- **Source** – vous pouvez afficher les alertes de toutes les sources ou limiter les alertes à ceux fournis dans le service ou une ou toutes les appareils.

- **Laps de temps** – en spécifiant les dates **du** et **au** et que les horodatages, vous pouvez consulter alertes pendant la période qui vous intéresse.

## <a name="alerts-quick-reference"></a>Aide-mémoire des alertes

Les tableaux suivants répertorient certaines alertes Microsoft Azure StorSimple que vous pouvez rencontrer, ainsi que des recommandations et des informations supplémentaires lorsqu’elles sont disponibles. Alertes de périphérique StorSimple appartiennent à l’une des catégories suivantes :

- [Alertes de connectivité cloud](#cloud-connectivity-alerts)

- [Alertes cluster](#cluster-alerts)

- [Alertes de récupération d’urgence](#disaster-recovery-alerts)

- [Alertes de matériel](#hardware-alerts)

- [Alertes d’échec de tâche](#job-failure-alerts)

- [Alertes relatives aux volumes localement épinglé](#locally-pinned-volume-alerts)

- [Alertes de mise en réseau](#networking-alerts)

- [Alertes de performances](#performance-alerts)

- [Alertes de sécurité](#security-alerts)

- [Prise en charge package alertes](#support-package-alerts)

- [Mettre à jour des alertes](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertes de connectivité cloud

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Connectivité à <*nom d’identification cloud*> ne peut pas être établie.|Impossible de se connecter à ce compte de stockage.|Il semble que vous devrez éventuellement un problème de connectivité avec votre appareil. Exécutez le `Test-HcsmConnection` applet de commande d’utiliser l’Interface Windows PowerShell pour StorSimple sur votre appareil pour identifier et résoudre le problème. Si les paramètres sont corrects, le problème peut être avec les informations d’identification du compte de stockage pour lequel l’alerte a été déclenché. Dans ce cas, utilisez la `Test-HcsStorageAccountCredential` applet de commande pour déterminer s’il existe des problèmes que vous pouvez résoudre.<ul><li>Vérifiez vos paramètres réseau.</li><li>Vérifiez vos informations d’identification du compte de stockage.</li></ul>|
|Nous n’avons pas reçu une pulsation à partir de votre appareil pour les dernière <*nombre*> minutes.|Impossible de se connecter à l’appareil.|Il semble qu’actuellement un problème de connectivité avec votre appareil. Utilisez le `Test-HcsmConnection` applet de commande d’utiliser l’Interface Windows PowerShell pour StorSimple sur votre appareil à identifier et résoudre le problème ou contactez votre administrateur réseau.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportement StorSimple lors de l’échec de la connectivité de cloud

Que se passe-t-il en cas d’échec de la connectivité de cloud pour mon périphérique StorSimple fonctionne en production ?

En cas de connectivité cloud sur votre appareil de production StorSimple, puis en fonction de l’état de votre appareil, les éléments suivants peuvent se produire : 

- **Pour les données locales sur votre appareil**: depuis un certain temps, il sera sans interruption de service et lectures continuent à être prises en charge. Toutefois, tandis que le nombre de IOs en suspens augmente et dépasse la limite, les lectures peuvent commencer à échouer. 

    Selon la quantité de données sur votre appareil, les opérations d’écriture continueront également à se produire pour les premières heures après l’interruption de la connectivité de cloud. Les opérations d’écriture seront puis ralentir et finalement commencer à échouer si la connectivité cloud est interrompue pour plusieurs heures. (Il y a stockage temporaire sur le périphérique pour les données vers le cloud. Cette zone est vidée lorsque les données sont envoyées. En cas de connectivité, des données dans cette zone de stockage ne seront pas répercutées dans le cloud, et IO échouera.)   

 
- **Pour les données dans le cloud**: pour la plupart des erreurs de connectivité cloud, une erreur est renvoyée. Une fois que la connectivité est restaurée, IOs sont repris sans l’utilisateur faire apparaître le volume en ligne. Il arrive intervention de l’utilisateur peut être nécessaire pour ramener le volume en ligne à partir du portail classique Azure. 
 
- **Pour des instantanés cloud en cours**: l’opération est retentée quelques heures dans les heures de 4 et 5 et si la connectivité n’est pas restaurée, les instantanés cloud échouera.


### <a name="cluster-alerts"></a>Alertes cluster

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|APPAREIL basculé vers <*nom de l’appareil*>.|Appareil est en mode maintenance.|APPAREIL basculé en raison d’entrer ou quitter le mode de maintenance. Ceci est normal et aucune action n’est nécessaire. Une fois que vous avez reconnu cette alerte, supprimer de la page des alertes.|
|APPAREIL basculé vers <*nom de l’appareil*>.|APPAREIL microprogramme ou logiciel a été mis à jour.|Un basculement de cluster en raison d’une mise à jour s’est produite. Ceci est normal et aucune action n’est nécessaire. Une fois que vous avez reconnu cette alerte, supprimer de la page des alertes.|
|APPAREIL basculé vers <*nom de l’appareil*>.|Contrôleur a été arrêté ou redémarré.|APPAREIL basculé, car le contrôleur actif a été arrêté ou redémarré par un administrateur. Aucune action n’est nécessaire. Une fois que vous avez reconnu cette alerte, supprimer de la page des alertes.|
|APPAREIL basculé vers <*nom de l’appareil*>.|Basculement planifié.|Vérifiez qu’il s’agissait d’un basculement planifié. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.|
|APPAREIL basculé vers <*nom de l’appareil*>.|Basculement non planifié.|StorSimple est conçu pour récupérer automatiquement à partir de basculement non planifiée. Si vous voyez un grand nombre de ces alertes, contactez le Support Microsoft.|
|APPAREIL basculé vers <*nom de l’appareil*>.|Cause autres/inconnu.|Si vous voyez un grand nombre de ces alertes, contactez le Support Microsoft. Une fois que le problème est résolu, désactivez cette alerte à partir de la page des alertes.|
|Un service de périphériques critiques signale l’état comme ayant échoué.|Échec du service DataPath. |Contactez le Support Microsoft pour obtenir une assistance.|
|Adresse IP virtuelle interface réseau <*données #*> rapports état comme ayant échoué. |Cause autres/inconnu. |Conditions parfois temporaires peuvent entraîner ces alertes. Si c’est le cas, puis cette alerte automatiquement disparaît après un certain temps. Si le problème persiste, contactez le Support Microsoft.|
|Adresse IP virtuelle interface réseau <*données #*> rapports état comme ayant échoué.|Nom de l’interface : <*données #*> adresse IP <IP address> ne peut pas être mis en ligne, car une adresse IP en double a été détectée sur le réseau. |Vérifiez que l’adresse IP en double est supprimé à partir du réseau ou reconfigurer l’interface avec une adresse IP différente.|


### <a name="disaster-recovery-alerts"></a>Alertes de récupération d’urgence

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Opérations de récupération n’a pas peuvent restaurer tous les paramètres de ce service. Données de configuration d’appareil sont dans un état n’est pas cohérente pour certains appareils.|Incohérence des données détecté après sinistre.|Données chiffrées sur le service ne sont pas synchronisées avec celui sur l’appareil. Autoriser l’appareil <*nom de l’appareil*> à partir du Gestionnaire de StorSimple pour démarrer le processus de synchronisation. Utiliser l’Interface Windows PowerShell pour StorSimple permet d’exécuter le `Restore-HcsmEncryptedServiceData` sur appareil <*nom de l’appareil*> cmdlet, fournir l’ancien mot de passe comme une entrée à cette applet de commande pour restaurer le profil de sécurité. Réexécutez le `Invoke-HcsmServiceDataEncryptionKeyChange` applet de commande pour mettre à jour la clé de chiffrement des données de service. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.|


### <a name="hardware-alerts"></a>Alertes de matériel

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Composant matériel <*ID du composant*> rapports état <*état*>.||Conditions parfois temporaires peuvent entraîner ces alertes. Si c’est le cas, cette alerte disparaît après un certain temps. Si le problème persiste, contactez le Support Microsoft.|
|Contrôleur passif fonctionne pas correctement.|Contrôleur passif (secondaire) ne fonctionne pas.|Votre périphérique est opérationnel, mais un contrôleur de fonctionne correctement. Essayez de redémarrer ce contrôleur. Si le problème n’est pas résolu, contactez le Support Microsoft.|

### <a name="job-failure-alerts"></a>Alertes d’échec de tâche

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échec de la sauvegarde de <*ID du groupe source volume*>.|Travail de sauvegarde a échoué.|Problèmes de connectivité peuvent empêcher l’opération de sauvegarde d’exécuter avec succès. S’il n’y a aucun problème de connectivité, il pouvez que vous avez atteint le nombre maximal de sauvegardes. Supprimer toutes les sauvegardes qui ne sont plus utiles et recommencez l’opération. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.|
|Duplication de <*ID d’élément de source de sauvegarde*> < de*numéros de série de volume destination*> a échoué.|Échoué de la tâche Cloner.|Actualiser la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité cloud empêchent l’opération cloner d’exécuter avec succès. S’il n’y a aucun problème de connectivité, il pouvez que vous avez atteint la limite de stockage. Supprimer toutes les sauvegardes qui ne sont plus utiles et recommencez l’opération. Une fois que vous avez bien suivi action appropriée pour résoudre le problème, désactivez cette alerte à partir de la page des alertes.|
|Restauration de <*ID d’élément de source de sauvegarde*> a échoué.|Restaurez l’échec de la tâche.|Actualiser la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité cloud empêchent la restauration d’exécuter avec succès. S’il n’y a aucun problème de connectivité, il pouvez que vous avez atteint la limite de stockage. Supprimer toutes les sauvegardes qui ne sont plus utiles et recommencez l’opération. Une fois que vous avez bien suivi action appropriée pour résoudre le problème, désactivez cette alerte à partir de la page des alertes.|

### <a name="locally-pinned-volume-alerts"></a>Alertes relatives aux volumes localement épinglé

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échec de la création du volume local <*nom du volume*>.| La tâche de création de volume a échoué. <*Message erreur correspondant au code d’erreur Échec*>.|Problèmes de connectivité peuvent empêcher l’opération de création d’espace de terminer avec succès. Volumes localement épinglés thickly mise en service et le processus de création d’espace implique débordement volumes hiérarchisés vers le cloud. S’il n’y a aucun problème de connectivité, vous avez pouvez saturation de l’espace local sur l’appareil. Déterminer si un espace existe sur le périphérique avant d’essayer de cette opération.|
|Extension de volume local <*nom du volume*> a échoué.|Le travail de modification de volume a échoué en raison de <*message erreur correspondant au code d’erreur Échec*>.|Problèmes de connectivité peuvent empêcher l’opération d’extension volume d’exécuter avec succès. Volumes localement épinglés thickly mise en service et le processus d’extension de l’espace d’implique débordement volumes hiérarchisés vers le cloud. S’il n’y a aucun problème de connectivité, vous avez pouvez saturation de l’espace local sur l’appareil. Déterminer si un espace existe sur le périphérique avant d’essayer de cette opération.|
|Échoué de la conversion du volume <*nom du volume*>.|La tâche de conversion de volume pour convertir le type de volume de localement épinglée à plusieurs niveaux a échoué.|Conversion du volume de type localement épinglée à plusieurs niveaux pas pu être terminé. Assurez-vous qu’il n’y a aucun problème de connectivité empêche l’opération de se terminer. Pour résoudre les problèmes de connectivité accédez à [résoudre les problèmes avec l’applet de commande Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Le volume localement épinglé d’origine est maintenant marqué comme un volume hiérarchisé étant donné que certaines des données à partir du volume localement épinglé a dispersées dans le cloud lors de la conversion. Le volume hiérarchisé qui en résulte occupe toujours un espace local sur le périphérique ne peut pas être récupéré des volumes locaux futures.<br>Résoudre les problèmes de connectivité, désactivez l’alerte et reconvertir ce volume en le type de volume localement épinglé d’origine afin de toutes les données soient accessibles localement à nouveau.|
|Échoué de la conversion du volume <*nom du volume*>.|La tâche de conversion de volume pour convertir le type de volume à partir de plusieurs niveaux localement épinglée a échoué.|Conversion du volume de type hiérarchisé localement épinglée pas pu être effectuée. Assurez-vous qu’il n’y a aucun problème de connectivité empêche l’opération de se terminer. Pour résoudre les problèmes de connectivité accédez à [résoudre les problèmes avec l’applet de commande Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Le volume hiérarchisé d’origine désormais marqué comme un volume localement épinglé comme partie du processus de conversion continue à contiennent des données qui résident dans le cloud, tandis que l’espace thickly généré sur le périphérique pour ce volume pouvez n’est plus récupéré des volumes locaux futures.<br>Résoudre les problèmes de connectivité, désactivez l’alerte et reconvertir ce volume en le type de volume hiérarchisé d’origine afin d’espace local thickly sa mise en service sur le périphérique peut être récupéré.|
|Approche de consommation d’espace local pour des instantanés locales de <*nom du groupe en volume*>|Instantanés locaux pour la stratégie de sauvegarde peuvent bientôt manquer d’espace et être annulées pour éviter les échecs d’écriture hôte.|Fréquents instantanés locales en parallèle avec un évolution du élevée des données dans les volumes associés à ce groupe de stratégies de sauvegarde sont à l’origine d’espace local sur le périphérique à utiliser rapidement. Supprimer les instantanés locales qui ne sont plus utiles. En outre, mettez à jour vos plannings instantané local pour cette stratégie de sauvegarde à prendre des instantanés locales moins souvent et vérifiez que cloud des instantanés régulièrement. Si ces actions ne sont pas prises, local pour ces instantanés peuvent vous être saturation et le système supprime automatiquement les pour vous assurer que les écritures de l’hôte continuent à être analysé avec succès.|
|Des instantanés locales pour <*nom du groupe volume*> ont été annulées.|Les instantanés locales pour <*nom du groupe volume*> ont été annulées, puis supprimées, car ils ont été supérieure à l’espace local sur l’appareil.|Pour vous assurer que cela ne se répète pas à l’avenir, passez en revue les planifications instantané local pour cette stratégie de sauvegarde et supprimez tout instantanés locales qui ne sont plus utiles. Fréquents instantanés locales en parallèle avec un évolution du élevée des données dans les volumes associés à ce groupe de stratégies de sauvegarde peuvent provoquer un espace local sur le périphérique à utiliser rapidement.|
|Restauration de <*ID d’élément de source de sauvegarde*> a échoué.|Le travail de restauration a échoué.|Si vous avez localement épinglés ou un mélange des volumes localement épinglés et hiérarchisés dans cette stratégie de sauvegarde, actualisez la sauvegarde de la liste afin de vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité cloud empêchent la restauration d’exécuter avec succès. Les volumes localement épinglés qui ont été restaurées dans le cadre de ce groupe instantané n’ont pas toutes leurs données téléchargées sur le périphérique et, si vous avez un mélange des volumes hiérarchisés et localement épinglés dans ce groupe instantané, elles ne seront plus synchronisés entre eux. Pour terminer l’opération de restauration, effectuez les volumes dans ce groupe en mode hors connexion sur l’hôte et recommencez l’opération de restauration. Notez que les modifications apportées aux données du volume qui ont été effectuées pendant le processus de restauration seront perdues.|

### <a name="networking-alerts"></a>Alertes de mise en réseau

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|N’a pas pu démarrer ou les services StorSimple.|Erreur DataPath |Si le problème persiste, contactez le Support Microsoft.|
|Adresse IP en double détecté pour « Data0 ».| |Le système a détecté un conflit pour adresse IP '10.0.0.1'. La ressource réseau « Data0 » sur le périphérique *<device1>* est en mode hors connexion. Assurez-vous que cette adresse IP n’est pas utilisée par une autre entité sur ce réseau. Pour résoudre les problèmes de réseau, accédez à [résoudre les problèmes avec l’applet de commande Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contactez votre administrateur réseau pour vous aider à résoudre ce problème. Si le problème persiste, contactez le Support Microsoft. |
|Adresse IPv4 (ou IPv6) « Data0 » est en mode hors connexion.| |La ressource réseau « Data0 » avec adresse IP '10.0.0.1.' et longueur « 22 » sur le périphérique de préfixe *<device1>* est en mode hors connexion. Assurez-vous que les ports de commutateur auquel cette interface est connectée sont opérationnelles. Pour résoudre les problèmes de réseau, accédez à [résoudre les problèmes avec l’applet de commande Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Alertes de performances

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Le chargement du périphérique a dépassé <*seuil*>.|Plus lente que prévu temps de réponse.|Votre périphérique signale l’utilisation d’entrée/sortie surchargé. Ceci peut provoquer votre appareil afin de ne fonctionnent ne pas correctement il convient. Passez en revue les charges de travail que vous avez connecté à l’appareil et déterminez s’il y en a qui pourrait être déplacée vers un autre périphérique, ou qui ne sont plus nécessaires.<br>Pour mieux comprendre l’état actuel, accédez à [utiliser le service Manager StorSimple à surveiller votre appareil](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Alertes de sécurité

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Session de Support Microsoft a commencé.|Tiers d’accéder à la session de prise en charge.|Confirmez que cet accès est autorisé. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.|
|Mot de passe pour <*élément*> expirera <*durée*>.|Approche de l’expiration du mot de passe.|Modifier votre mot de passe avant son expiration.|
|Informations de configuration de sécurité manquant pour <*ID de l’élément*>.||Les volumes associés à ce conteneur de volume ne peuvent pas être utilisés pour dupliquer votre configuration StorSimple. Pour vous assurer que vos données sont stockées en toute sécurité, nous vous recommandons de supprimer le conteneur de volume et aucun volume associé au conteneur de volume. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.|
|<*nombre*> tentatives de connexion a échoué pour <*ID de l’élément*>.|Plusieurs tentatives de connexion infructueuses.|Votre périphérique est peut-être sous attaque ou un utilisateur autorisé tente de se connecter avec un mot de passe.<ul><li>Contactez votre utilisateurs autorisés et vérifiez que ces tentatives étaient d’une source légitime. Si vous continuez de voir grand nombre de tentatives de connexion infructueuses, envisagez de désactivation de la gestion à distance et de contacter votre administrateur réseau. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.</li><li>Vérifiez que vos instances Gestionnaire d’instantanés sont configurés avec le mot de passe correct. Après avoir pris les mesures nécessaires, désactivez cette alerte à partir de la page des alertes.</li></ul>Pour plus d’informations, accédez à [Modifier un mot de passe qui a expiré](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|Un ou plusieurs échecs s’est produite lors de la modification de la clé de chiffrement des données de service.||Erreurs lors de la modification de la clé de chiffrement de données de service se sont produites. Une fois que vous avez résolu les conditions d’erreur, exécutez la `Invoke-HcsmServiceDataEncryptionKeyChange` applet de commande d’utiliser l’Interface Windows PowerShell pour StorSimple sur votre appareil pour mettre à jour le service. Si ce problème persiste, contactez le support Microsoft. Après avoir résolu le problème, désactivez cette alerte à partir de la page des alertes.|

### <a name="support-package-alerts"></a>Prise en charge package alertes

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Échec de la création du package de prise en charge.|StorSimple n’a pas pu générer le package.|Recommencez cette opération. Si le problème persiste, contactez le Support Microsoft. Une fois que vous avez résolu le problème, désactivez cette alerte à partir de la page des alertes.|

### <a name="update-alerts"></a>Mettre à jour des alertes

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Correctif installé.|Mise à jour du logiciel/microprogramme terminée.|Le correctif a été installé correctement sur votre appareil.|
|Mises à jour manuelles disponibles.|Notification de mises à jour disponibles.|Utilisez l’Interface de Windows PowerShell pour StorSimple sur votre appareil pour installer ces mises à jour. <br>Pour plus d’informations, accédez à [mettre à jour votre périphérique série 8000 StorSimple](storsimple-update-device.md).|
|Nouvelles mises à jour disponibles.|Notification de mises à jour disponibles.|Vous pouvez installer ces mises à jour à partir de la page de **Maintenance** ou à l’aide de l’Interface de Windows PowerShell pour StorSimple sur votre appareil. <br>Pour plus d’informations, accédez à [mettre à jour votre périphérique série 8000 StorSimple](storsimple-update-device.md).|
|Impossible d’installer les mises à jour.|Mises à jour n’ont pas été installés.|Votre système n’a pas pu installer les mises à jour. Vous pouvez installer ces mises à jour à partir de la page de **Maintenance** ou à l’aide de l’Interface de Windows PowerShell pour StorSimple sur votre appareil. Si le problème persiste, contactez le Support Microsoft. <br>Pour plus d’informations, accédez à [mettre à jour votre périphérique série 8000 StorSimple](storsimple-update-device.md).|
|Impossible de vérifier automatiquement les nouvelles mises à jour.|Échoué de la vérification automatique.|Vous pouvez vérifier manuellement les nouvelles mises à jour à partir de la page de **Maintenance** .|
|Nouvel agent Windows Update Agent disponible.|Notification de mise à jour disponible.|Téléchargez l’Agent Windows Update dernière et l’installer à partir de l’interface Windows PowerShell.|
|Version du composant microprogramme <*ID du composant*> ne correspond pas avec votre matériel.|Mises à jour de microprogramme n’ont pas été installés.|Contacter le support technique Microsoft.|

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [StorSimple erreurs et résolution des problèmes d’un périphérique opérationnel](storsimple-troubleshoot-operational-device.md).

