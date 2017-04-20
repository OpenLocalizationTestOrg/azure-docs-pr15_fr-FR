<properties
    pageTitle="Effectuer le suivi d’état du service Azure à l’aide des journaux d’activité moniteur Azure | Microsoft Azure"
    description="Savoir quand Azure a rencontré interruptions dégradation ou de service de performances. "
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Effectuer le suivi d’état du service Azure à l’aide des journaux d’activité moniteur Azure

Azure publicizes chaque fois qu’il y a une dégradation des performances ou interruption du service. Vous pouvez parcourir ces événements dans le portail Azure, et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l’ensemble des événements par programme.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Parcourir les journaux d’intégrité du service pour votre abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans **accueil** vous devriez voir une vignette de **l’état du Service**. Cliquez dessus.

    ![Accueil](./media/insights-service-health/Insights_Home.png)

3. Vous consultez une liste de toutes les zones dans Azure. Cliquez sur n’importe quelle région pour faire apparaître la requête journal d’activité qui affiche des incidents de service qui ont modifié un de vos abonnements dans les dernières 24 heures.

    ![Fonctionnement du Service activité journal abonnement](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. Vous pouvez voir les détails d’un incident individuel en cliquant sur cet événement dans le tableau.

5. Modifier la **Timespan** pour afficher une durée plus longue.

## <a name="next-steps"></a>Étapes suivantes

* [Analyser la disponibilité et la réactivité de n’importe quelle page web](../application-insights/app-insights-monitor-web-app-availability.md) avec des aperçus Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
