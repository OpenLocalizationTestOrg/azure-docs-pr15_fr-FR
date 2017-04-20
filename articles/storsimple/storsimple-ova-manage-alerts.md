<properties 
   pageTitle="Afficher et gérer les alertes de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les conditions d’alerte tableau virtuel StorSimple et gravité et comment utiliser le service StorSimple Manager pour gérer les alertes."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Utiliser le service Manager StorSimple pour afficher et gérer les alertes pour le tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

L’onglet **alertes** dans le service Manager StorSimple fournit une méthode pour examiner et désactivez les alertes liées tableau virtuel StorSimple en temps réel. À partir de cet onglet, vous pouvez centraliser l’analyse les problèmes d’intégrité de vos tableaux virtuels StorSimple (également appelé StorSimple locaux périphériques virtuels) et la solution Microsoft Azure StorSimple globale.

Ce didacticiel décrit comment configurer les notifications d’alerte, les conditions d’alerte courantes, les niveaux de gravité d’alerte et comment afficher et suivi des alertes. En outre, il inclut les tables aide-mémoire alerte, qui vous permettent de localiser une alerte spécifique et répondre de façon appropriée.

![Page d’alertes](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurer les paramètres d’alerte

Vous pouvez choisir si vous souhaitez être informé par courrier électronique de conditions d’alerte pour chacun de vos périphériques virtuels StorSimple. En outre, vous pouvez identifier les autres destinataires de notification d’alerte en entrant leur adresse de messagerie dans la zone **autres destinataires de messagerie** , séparée par des points-virgules.

>[AZURE.NOTE] Vous pouvez entrer un maximum de 20 adresses de messagerie par périphérique virtuel.

Après avoir activé la notification par courrier électronique pour un périphérique virtuel, les membres de la liste de notification recevront un message électronique chaque fois qu’une alerte critique survient. Les messages seront envoyés à partir de *storsimple-alerts-noreply@mail.windowsazure.com* et décrira la condition d’alerte. Destinataires peuvent cliquer sur **Annuler l’abonnement** pour se supprimer de la liste de notifications de messagerie.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Pour activer la notification par courrier électronique des alertes pour un périphérique virtuel

1. Accédez à **appareils** > **Configuration** du périphérique virtuel. Accédez à la section **paramètres d’alerte** .

    ![paramètres d’alerte](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Sous **paramètres d’alerte**, définissez les options suivantes :

    1. Dans le champ de **notification par courrier électronique Envoyer** , sélectionnez **Oui**.

    2. Dans le champ **administrateurs de service de messagerie** , sélectionnez **Oui** si vous souhaitez que l’administrateur de service et les administrateurs de co-création reçoivent les notifications d’alerte.

    3. Dans le champ **autres destinataires de messagerie** , entrez les adresses de messagerie de tous les autres destinataires devant recevoir les notifications d’alerte. Entrez des noms dans le format *someone@somewhere.com*. Utilisez des points-virgules pour séparer les adresses de messagerie. Vous pouvez configurer un maximum de 20 adresses de messagerie par périphérique virtuel. 

        ![configuration de la notification alertes](./media/storsimple-ova-manage-alerts/alerts3.png)

3. Dans la partie inférieure de la page, cliquez sur **Enregistrer** pour enregistrer votre configuration.

4. Pour envoyer une notification par courrier électronique de test, cliquez sur la flèche en regard de **test envoyer un courrier électronique**. Le service Manager StorSimple affichera les messages d’état comme il transfère la notification de test. 

5. Lorsque le message suivant s’affiche, cliquez sur **OK**. 

    ![Alertes tester la messagerie électronique de notification envoyée](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Si le message de notification de test ne peut pas être envoyé, le service Manager StorSimple affiche un message approprié. Cliquez sur **OK**, patientez quelques minutes et réessayez d’envoyer votre message de notification de test. 

    Le message de notification de test sera semblable à ce qui suit.

    ![Exemple de courrier électronique de test alertes](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Conditions d’alerte courantes

Votre tableau virtuel StorSimple génère des alertes en réponse à une variété de conditions. Voici les conditions d’alerte les plus courants :

- **Problèmes de connectivité** – ces alertes se produisent lorsqu’il y a des difficultés à transférer des données. Problèmes de communication peuvent se produire lors du transfert de données et à partir du compte de stockage Azure ou en raison de l’absence de connectivité entre les périphériques virtuels et le service Manager StorSimple. Problèmes de communication sont les plus difficiles à résoudre, car il existe des points autant de panne. Vous devez d’abord toujours vérifier que la connectivité réseau et accès à Internet sont disponibles avant de passer à la résolution des problèmes plus avancées. Pour plus d’informations sur les ports et les paramètres du pare-feu, accédez à [tableau virtuel StorSimple requise](storsimple-ova-system-requirements.md). Pour vous aider à résoudre, accédez à [résoudre les problèmes avec l’applet de commande tester la connexion](storsimple-troubleshoot-deployment.md).

- **Problèmes de performances** – ces alertes peuvent se présenter lorsque votre système n’est pas des performances optimales, par exemple lorsqu’il est surchargé.

En outre, vous pouvez voir des alertes liées à la sécurité, les mises à jour ou les échecs des tâches.

## <a name="alert-severity-levels"></a>Niveaux de gravité d’alerte

Alertes ont différents niveaux de gravité, en fonction de l’impact de la situation alerte et qu’il soit nécessaire d’une réponse à l’alerte. Les niveaux de gravité sont :

- **Critique** : cette alerte est en réponse à une condition qui affecte les performances de votre système réussie. Action est requise pour vous assurer que la StorSimple service n’est pas interrompu.

- **Avertissement** – cette condition peut devenir critique si ne pas résolu. Vous devez examiner la situation et aucune action nécessaire pour effacer le problème.

- Des **informations** : cette alerte contient des informations qui peuvent être utiles dans leur suivi et gestion de votre système.

## <a name="view-and-track-alerts"></a>Afficher et effectuer le suivi des alertes

Le tableau de bord de service Manager StorSimple vous permet d’un coup de œil rapide au nombre d’alertes sur vos appareils virtuels, organisés par niveau de gravité.

![Tableau de bord alertes](./media/storsimple-ova-manage-alerts/alerts6.png)

Cliquez sur le niveau de gravité pour ouvrir l’onglet **alertes** . Les résultats comprennent uniquement les alertes qui correspondent à ce niveau de gravité.

![Rapport alertes relatif au type d’alerte](./media/storsimple-ova-manage-alerts/alerts7.png)

En cliquant sur une alerte dans la liste, vous fournit des détails supplémentaires pour l’alerte, y compris la dernière fois que l’alerte a été signalé, le nombre d’occurrences de l’alerte sur le périphérique et l’action recommandée pour résoudre l’alerte.

Si vous avez besoin envoyer des informations au Support Microsoft, vous pouvez copier les détails de l’alerte vers un fichier texte. Une fois que vous avez suivi les recommandations et résolu la condition d’alerte en local, vous devez désactiver l’alerte d’en sélectionnant l’alerte dans l’onglet **alertes** et en cliquant sur **Effacer**. Pour supprimer plusieurs alertes, sélectionnez chaque alerte et cliquez sur n’importe quelle colonne à l’exception de la colonne **alerte** , puis cliquez sur **Effacer** après avoir sélectionné toutes les alertes à effacer. Notez que certaines alertes sont désactivées automatiquement lorsque le problème est résolu ou que les mises à jour l’alerte avec les nouvelles informations.

Lorsque vous cliquez sur **Effacer**, vous aurez la possibilité pour fournir des commentaires sur l’alerte et les étapes que vous avez prises pour résoudre le problème. 

![alertes commentaires](./media/storsimple-ova-manage-alerts/clear-alert.png)

Cliquez sur l’icône de vérification ![icône de vérification](./media/storsimple-ova-manage-alerts/check-icon.png) Pour enregistrer vos commentaires.

Certains événements seront désactivées par le système si un autre événement se déclenche avec les nouvelles informations. Dans ce cas, vous verrez le message suivant.

![Message d’alerte effacer](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Alertes de tri et de révision

L’onglet **alertes** peut afficher jusqu'à 250 alertes. Si vous avez dépassé le nombre d’alertes, pas toutes les alertes seront affichera dans l’affichage par défaut. Vous pouvez combiner les champs suivants pour personnaliser les alertes qui sont affichent :

- **État** – vous pouvez afficher les alertes **actif** ou **désactivé** . Active les alertes sont toujours en cours déclenchées sur votre système, tandis que les alertes désactivées ont été désactivées manuellement par un administrateur ou désactivées par programme, car le système mis à jour la condition d’alerte avec les nouvelles informations.

- **Gravité** – vous pouvez afficher les alertes de tous les niveaux de gravité (critiques, avertissements, informations), ou simplement une certaine gravité, telles que les alertes critiques uniquement.

- **Source** – vous pouvez afficher les alertes de toutes les sources ou limiter les alertes à ceux fournis à partir d’une ou toutes les les appareils virtuels ou le service.

- **Laps de temps** – en spécifiant les dates **du** et **au** et que les horodatages, vous pouvez consulter alertes pendant la période qui vous intéresse.

## <a name="alerts-quick-reference"></a>Aide-mémoire des alertes

Les tableaux suivants répertorient certaines alertes Microsoft Azure StorSimple que vous pouvez rencontrer, ainsi que des recommandations et des informations supplémentaires lorsqu’elles sont disponibles. Alertes de périphérique virtuel StorSimple appartiennent à l’une des catégories suivantes :

- [Alertes de connectivité cloud](#cloud-connectivity-alerts)

- [Alertes de configuration](#configuration-alerts)

- [Alertes d’échec de tâche](#job-failure-alerts)

- [Alertes de performances](#performance-alerts)

- [Alertes de sécurité](#security-alerts)

- [Mettre à jour des alertes](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertes de connectivité cloud

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|APPAREIL *<device name>* n’est pas connecté dans le cloud.|Le périphérique nommé ne peut pas se connecter au cloud. |N’a pas pu se connecter au cloud. Cela peut être dû à une des opérations suivantes :<ul><li>Il peut y avoir un problème avec les paramètres réseau sur votre appareil.</li><li>Il peut y avoir un problème avec les informations d’identification du compte de stockage.</li></ul>Pour plus d’informations sur la résolution des problèmes de connectivité, accédez à l' [interface utilisateur local web](storsimple-ova-web-ui-admin.md) du périphérique.|


### <a name="configuration-alerts"></a>Alertes de configuration

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Configuration d’unité virtuelle locaux non prises en charge.|Baisse des performances.|La configuration actuelle peut entraîner une baisse des performances. Assurez-vous que votre serveur répond à la configuration minimale requise. Pour plus d’informations, accédez aux [Exigences de tableau virtuel StorSimple](storsimple-ova-system-requirements.md). 
|Espace disque généré sur <*nom de l’appareil*> sont insuffisant.|Avertissement d’espace disque.|Vous manquez d’espace disque mis en service. Pour libérer de l’espace, vous pouvez déplacer les charges de travail vers un autre volume ou partager ou supprimer des données.

### <a name="job-failure-alerts"></a>Alertes d’échec de tâche

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Copie de sauvegarde de <*nom de l’appareil*> n’a pas pu être effectuée.|Échec du travail de sauvegarde.|Impossible de créer une sauvegarde. Envisager l’une des opérations suivantes :<ul><li>Problèmes de connectivité peuvent empêcher l’opération de sauvegarde d’exécuter avec succès. Assurez-vous qu’il n’y a aucun problème de connectivité. Pour plus d’informations sur la résolution des problèmes de connectivité, accédez à [l’interface utilisateur local web](storsimple-ova-web-ui-admin.md) pour votre appareil virtuel.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, envisagez de supprimer toutes les sauvegardes qui ne sont plus utiles.</li></ul> Résoudre les problèmes, désactivez l’alerte et recommencez l’opération.|
|Restauration de <*nom de l’appareil*> n’a pas pu être effectuée.|Restaurer une tâche a échoué.|N’a pas pu restaurer à partir de sauvegarde. Envisager l’une des opérations suivantes :<ul><li>Votre liste de sauvegarde peut ne pas être valide. Actualiser la liste pour vérifier qu’il est toujours valide.</li><li>Problèmes de connectivité peuvent empêcher la restauration d’exécuter avec succès. Assurez-vous qu’il n’y a aucun problème de connectivité.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, envisagez de supprimer toutes les sauvegardes qui ne sont plus utiles.</li></ul>Résoudre les problèmes, désactivez l’alerte et recommencez l’opération de restauration.|
|Cloner de <*nom de l’appareil*> n’a pas pu être effectuée.|Dupliquer une tâche a échoué.|Impossible de créer un cloner. Envisager l’une des opérations suivantes :<ul><li>Votre liste de sauvegarde peut ne pas être valide. Actualiser la liste pour vérifier qu’il est toujours valide.</li><li>Problèmes de connectivité peuvent empêcher l’opération cloner d’exécuter avec succès. Assurez-vous qu’il n’y a aucun problème de connectivité.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, envisagez de supprimer toutes les sauvegardes qui ne sont plus utiles.</li></ul>Résoudre les problèmes, désactivez l’alerte et recommencez l’opération.|

### <a name="performance-alerts"></a>Alertes de performances

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Vous rencontrez des retards inattendus transfert de données.|Transfert de données lentes.|Limitation des erreurs se produisent lorsque vous dépassez les cibles extensibilité élevées d’un service de stockage. Le service de stockage pour cela, pour vous assurer qu’aucun client unique ou le client ne peut utiliser le service au détriment des autres. Pour plus d’informations sur la résolution de votre compte de stockage Azure, accédez à [surveiller, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
|Vous manquez d’espace disque local réservation <*nom de l’appareil*>.|Temps de réponse lente.|10 % de la taille totale générée <*nom de l’appareil*> est réservée sur le périphérique local et est maintenant insuffisante sur l’espace réservé. La charge de travail <*nom de l’appareil*> génère un taux d’évolution du plus élevé ou peut avoir récemment migré une grande quantité de données. Cela peut entraîner une baisse des performances. Envisager l’une des opérations suivantes pour résoudre ce problème :<ul><li>Augmenter la bande passante cloud pour cet appareil.</li><li>Réduire ou déplacez les charges de travail vers un autre volume ou partager.</li></ul>

### <a name="security-alerts"></a>Alertes de sécurité

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Mot de passe <*nom de l’appareil*> va expirer dans <*nombre*> jours.|Avertissement de mot de passe.| Votre mot de passe expirera dans < nombre < jours. Pensez à modifier votre mot de passe. Pour plus d’informations, accédez à [Modifier le mot de passe administrateur de périphérique tableau virtuel StorSimple](storsimple-ova-change-device-admin-password.md).

### <a name="update-alerts"></a>Mettre à jour des alertes

|Texte de l’alerte|Événement|Plus d’informations / actions recommandées|
|:---|:---|:---|
|Nouvelles mises à jour sont disponibles pour votre appareil.|Mises à jour de la matrice virtuel StorSimple sont disponibles.|Vous pouvez installer nouvelles mises à jour à partir de la page de **Maintenance** .|
|N’a pu analyser les nouvelles mises à jour sur <*nom de l’appareil*>.|Mettre à jour défaillance. |Une erreur s’est produite lors de l’installation de nouvelles mises à jour. Vous pouvez installer manuellement les mises à jour. Si le problème persiste, contactez [Le Support Microsoft](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le tableau virtuel StorSimple](storsimple-ova-overview.md).


