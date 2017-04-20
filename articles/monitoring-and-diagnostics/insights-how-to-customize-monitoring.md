<properties
    pageTitle="Vue d’ensemble des indicateurs dans Microsoft Azure | Microsoft Azure"
    description="Apprenez à personnaliser les graphiques surveillance dans Azure."
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

# <a name="overview-of-metrics-in-microsoft-azure"></a>Vue d’ensemble des indicateurs dans Microsoft Azure

Tous les services Azure superviser les principaux audits qui vous permettent de surveiller l’intégrité, performances, disponibilité et l’utilisation de vos services. Vous pouvez afficher les mesures dans le portail Azure, et vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder par programme à l’ensemble des indicateurs.

Pour certains services, vous devrez peut-être activer les diagnostics pour afficher les mesures. Pour d’autres personnes, tels que des machines virtuelles, vous obtiendrez un ensemble de métriques de base, mais vous avez besoin pour activer la version complète définir métriques haute fréquence. Consultez [Activer la surveillance et diagnostic](insights-how-to-use-diagnostics.md) pour en savoir plus.

## <a name="using-monitoring-charts"></a>À l’aide de graphiques de surveillance

Vous pouvez représenter une des mesures de leur sur une période de temps que vous choisissez.

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource qui vous intéresse dans l’analyse.

2. La section **surveillance** contient les mesures les plus importantes pour chaque ressource Azure. Par exemple, une application web comporte les **demandes et les erreurs**, l’emplacement dans lequel comme une machine virtuelle aurait **pourcentage processeur** et **disque lire et écrire**:  ![loupe d’analyse](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Cliquez sur n’importe quel graphique vous montrent la carte **métrique** . Sur la carte, outre le graphique, est une table qui vous montre les agrégations des mesures (par exemple, moyenne, minimale et maximale, dans la plage de temps que vous avez choisi). Au-dessous se trouvent les règles d’alerte pour la ressource.
    ![Carte métrique](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Pour personnaliser les lignes qui s’affichent, cliquez sur le bouton **Modifier** dans le graphique, ou la commande **Modifier le graphique** sur la carte métrique.

5. Dans la carte de modifier la requête, vous pouvez effectuer les trois points :
    - Modifier la période
    - Changer l’apparence entre barre et ligne
    - Choisissez différentes metics ![modifier la requête](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Modification de la plage horaire, il vous suffit de sélectionner une autre plage (par exemple, **Heures passées**) et cliquer sur **Enregistrer** dans la partie inférieure de la carte. Vous pouvez également choisir **personnalisée**, qui permet de vous permet de choisir une période donnée sur les 2 dernières semaines. Par exemple, vous pouvez voir la simplement 1 heure d’hier ensemble deux semaines, ou. Tapez dans la zone de texte pour entrer une autre heure.
    ![Période personnalisée](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. En dessous de la plage horaire, le canal que vous choisissez un nombre quelconque de mesures à afficher dans le graphique.

8. Lorsque vous cliquez sur Enregistrer vos modifications seront enregistrées pour cette ressource. Par exemple, si vous avez deux machines virtuelles et que vous modifiez un graphique sur une, il aura aucun impact sur l’autre.

## <a name="creating-side-by-side-charts"></a>Création de graphiques côte à côte

Avec la personnalisation puissante dans le portail, vous pouvez ajouter autant de graphiques que vous le souhaitez.

1. Dans le menu **...** en haut de la carte, cliquez sur **Ajouter des vignettes**:  
    ![Menu ajouter](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Ensuite, vous pouvez sélectionner sélectionner un graphique à partir de la **Galerie** sur le côté droit de l’écran :  ![galerie](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si vous ne voyez pas la mesure souhaitée, vous pouvez toujours ajouter un des indicateurs prédéfinis et **Modifier** le graphique pour afficher la métrique dont vous avez besoin.

## <a name="monitoring-usage-quotas"></a>Surveiller les quotas d’utilisation

La plupart des indicateurs vous afficher les tendances au fil du temps, mais certaines données, tels que les quotas d’utilisation, sont point-à-temps des informations avec un seuil.

Vous pouvez également afficher les quotas d’utilisation sur la carte pour les ressources des quotas :

![Utilisation](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Comme avec indicateurs, vous pouvez utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l’ensemble des quotas d’utilisation par programme.

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir des notifications d’alerte](insights-receive-alert-notifications.md) chaque fois qu’une métrique dépasse un seuil.
* [Activer la surveillance et diagnostic](insights-how-to-use-diagnostics.md) pour recueillir des métriques haute fréquence détaillées sur votre service.
* [Mettre à l’échelle nombre d’instances automatiquement](insights-how-to-scale.md) pour vous assurer que votre service n’est disponible et injoignable.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s’exécute dans le cloud.
* [Application perspectives pour applications JavaScript et les pages web](../application-insights/app-insights-web-track-usage.md) permet d’obtenir analytique client sur les navigateurs visitez une page web.
* [Analyser la disponibilité et la réactivité de n’importe quelle page web](../application-insights/app-insights-monitor-web-app-availability.md) avec des aperçus Application afin que vous pouvez déterminer si votre page ne fonctionne pas.
