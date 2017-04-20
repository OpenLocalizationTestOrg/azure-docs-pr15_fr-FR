<properties
    pageTitle="Activer surveillance et diagnostics dans Microsoft Azure | Microsoft Azure "
    description="Découvrez comment configurer des diagnostics pour vos ressources dans Azure."
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

# <a name="enable-monitoring-and-diagnostics"></a>Activer la surveillance et diagnostic

Dans le [Portail Azure](https://portal.azure.com), vous pouvez configurer riche et fréquents, surveillance et les diagnostics données concernant vos ressources. Vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour configurer les diagnostics par programme.

Données Diagnostics, la surveillance et métrique dans Azure sont enregistrées dans un compte de stockage de votre choix. Cela vous permet d’utiliser les outils que vous souhaitez lire les données, à partir de l’Explorateur de stockage, à Power BI aux outils tiers.

## <a name="when-you-create-a-resource"></a>Lorsque vous créez une ressource

La plupart des services permettent d’activer les diagnostics lorsque vous les créez dans le [Portail Azure](https://portal.azure.com).

1. Accédez à **Nouveau** et sélectionnez la ressource que qui vous intéresse.

2. Sélectionnez **configuration facultative**.
    ![Carte des Diagnostics](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Sélectionnez **Diagnostics**, puis cliquez **sur**. Vous devrez choisir le compte de stockage que vous souhaitez diagnostics soient enregistrées dans. Vous seront facturés débit normal pour le stockage et transactions lorsque vous envoyez des diagnostics à un compte de stockage.

4. Cliquez sur **OK** et créer la ressource.

## <a name="change-settings-for-an-existing-resource"></a>Modifier les paramètres pour une ressource existante

Si vous avez déjà créé une ressource et que vous souhaitez modifier les paramètres des diagnostics (pour modifier le niveau de collecte de données, par exemple), vous pouvez faire que directement dans le portail Azure.

1. Accédez à la ressource, puis cliquez sur la commande **paramètres** .

2. Sélectionnez **Diagnostics**.

3. La carte **Diagnostics** dispose tous les diagnostics possibles et les données d’analyse collection de sites pour cette ressource. Pour certaines ressources, vous pouvez également choisir une stratégie de **rétention** pour les données, pour nettoyer à partir de votre compte de stockage.
    ![Diagnostics de stockage](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Une fois que vous avez choisi les paramètres, cliquez sur la commande **Enregistrer** . Il peut prendre un peu tandis que d’analyser les données soient affichées lorsque vous l’activez pour la première fois.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Catégories de collecte de données pour les machines virtuelles
Pour les machines virtuelles tous les journaux et les mesures seront enregistrés à intervalles d’une minute, vous pouvez faire toujours les dernières informations relatives à votre ordinateur.

- **Les mesures de base** : mesures de fonctionnement sur votre ordinateur virtuel comme processeur et la mémoire
- **Indicateurs de réseau et web** : mesures relatives à vos connexions réseau et les services web
- **Indicateurs de .NET** : métriques sur les applications .NET et ASP.NET s’exécutant sur votre ordinateur virtuel
- **Indicateurs SQL** : Si vous exécutez Microsoft SQL Service, ses performances
- **Journaux d’événements application Windows** : événements de Windows qui sont envoyés au canal application
- **Journaux d’événements système Windows** : événements de Windows qui sont envoyés au canal système. Cela inclut également tous les événements à partir de [Microsoft contre les logiciels malveillants](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Journaux de sécurité des événements Windows** : événements de Windows qui sont envoyés au canal de sécurité
- **Les journaux de diagnostics infrastructure** : journalisation à propos de l’infrastructure de collecte des diagnostics
- **Journaux IIS** : les journaux sur votre serveur IIS

Notez que certaines répartitions de Linux pour le moment ne sont pas prises en charge et l’Agent invité doit être installé sur l’ordinateur virtuel.

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir des notifications alertes](insights-receive-alert-notifications.md) chaque fois que les événements opérationnels se produisent ou métriques croisées un seuil.
* [Indicateurs de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service n’est disponible et injoignable.
* [Mettre à l’échelle nombre d’instances automatiquement](insights-how-to-scale.md) pour vous assurer que votre échelle de service basé sur la demande.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s’exécute dans le cloud.
* [Afficher les événements et les journaux d’audit](insights-debugging-with-events.md) pour en savoir tout ce qui est arrivé dans votre service.
* [État du service de suivi](insights-service-health.md) pour déterminer quand Azure a rencontré interruptions dégradation ou de service de performances.
