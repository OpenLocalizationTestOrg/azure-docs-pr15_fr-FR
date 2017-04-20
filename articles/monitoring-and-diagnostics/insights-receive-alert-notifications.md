<properties
    pageTitle="Recevoir des notifications d’alerte pour les services Azure | Microsoft Azure"
    description="Recevoir une notification lorsque les conditions de règles d’alerte sont remplies."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>Recevoir des notifications d’alerte

Vous pouvez recevoir une alerte basée sur les audits de surveillance pour ou des événements sur vos services Azure.

Pour une règle d’alerte sur une valeur métrique, lorsque la valeur d’une métrique spécifiée dépasse un seuil affecté, la règle d’alerte devient active et envoyer une notification. Pour une règle d’alerte sur les événements, une règle peut envoyer une notification sur *chaque* événement, ou uniquement lorsqu’un certain nombre d’événements de se produire.

Lorsque vous créez une règle d’alerte, vous pouvez sélectionner des options pour envoyer une notification par courrier électronique à l’administrateur de service et coadministrateurs ou à un autre administrateur que vous pouvez spécifier. Un message de notification est envoyé lorsque la règle est activée, et quand une condition d’alerte est résolue.

Vous pouvez utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour configurer et obtenir des informations sur les règles d’alerte par programme.

## <a name="create-an-alert-rule"></a>Créer une règle d’alerte

1. Dans le [portail](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource qui vous intéresse dans l’analyse.

2. Cliquez sur la vignette de **règles d’alerte** dans la loupe **d’opérations** .

3. Cliquez sur la commande **Ajouter une alerte** .

    ![Ajout d’alerte](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Vous pouvez nommer votre règle d’alerte, puis choisissez une description qui s’affichent dans le message électronique de notification.

5. Lorsque vous sélectionnez **métrique** vous devez choisir une condition et une valeur de seuil pour la mesure. Il s’agit de la période de temps Azure utilise pour surveiller et traçage activité alerte.

    ![Une condition et une seuil](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Vous pouvez également choisir des **événements**et recevez une notification lorsqu’un certain événement se produit.

    ![Événements](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Enfin, vous pouvez choisir envoyer de notification par courrier électronique aux administrateurs responsables.

Une fois que vous cliquez sur **Enregistrer**, après quelques minutes vous serez informé à chaque fois que la métrique vous choisissez dépasse le seuil.

## <a name="managing-your-alert-rules"></a>Gestion de vos règles d’alerte

Une fois que vous avez créé une règle d’alerte, vous pouvez afficher un aperçu de l’alerte seuil par rapport à la métrique du jour précédent.

![Événements](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Vous pouvez modifier bien entendu cette règle d’alerte et **désactiver** ou **Activer** dessus si vous voulez temporairement arrêter de recevoir des notifications à ce sujet.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer webhooks sur vos alertes](insights-webhooks-alerts.md) pour acheminer des notifications à différents canaux
* [Indicateurs de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service n’est disponible et injoignable.
* [Activer la surveillance et diagnostic](insights-how-to-use-diagnostics.md) pour recueillir des métriques haute fréquence détaillées sur votre service.
* [Analyser la disponibilité et la réactivité de n’importe quelle page web](../application-insights/app-insights-monitor-web-app-availability.md) avec des aperçus Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s’exécute dans le cloud.
* [Afficher les événements et les journaux d’audit](insights-debugging-with-events.md) pour en savoir tout ce qui est arrivé dans votre service.
* [État du service de suivi](insights-service-health.md) pour déterminer quand Azure a rencontré interruptions dégradation ou de service de performances.
