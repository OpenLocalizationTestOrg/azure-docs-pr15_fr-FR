<properties
    pageTitle="Prise en main moniteur Azure | Microsoft Azure"
    description="Prise en main de l’utilisation du moniteur Azure pour obtenir un aperçu du fonctionnement de vos ressources et effectuer une opération sur la base de données."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Prise en main moniteur Azure

Moniteur Azure est le service de plateforme qui fournit une source unique de surveillance des ressources Azure. Avec le moniteur Azure, vous pouvez visualiser, interroger, Router, archiver et agir sur les mesures et les journaux provenant de ressources dans Azure. Vous pouvez travailler avec ces données à l’aide de la carte de portail moniteur [Applets de commande PowerShell moniteur](./insights-powershell-samples.md), [Infrastructure du langage commun disponibilité sur plusieurs plateformes](insights-cli-samples.md)ou [Azure moniteur reste API](https://msdn.microsoft.com/library/dn931943.aspx). Dans cet article, nous passez en revue quelques les principaux composants du moniteur Azure, à l’aide du portail pour démonstration.

1. Dans le portail, accédez à **d’autres services** et recherchez l’option **Moniteur** . Cliquez sur l’icône d’étoile pour ajouter cette option à votre liste de favoris afin qu’il soit toujours facilement accessible à partir de la barre de navigation gauche.

    ![Surveiller dans la liste des services](./media/monitoring-get-started/monitor-more-services.png)

2. Cliquez sur l’option **Moniteur** pour ouvrir la carte **Moniteur** . Cette carte réunit toutes vos paramètres de surveillance et données un seul affichage consolidé. Il s’ouvre tout d’abord à la section du **journal d’activité** .

    ![Navigation de carte de moniteur](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] Options **des notifications de Service** et des **groupes de Notification** qui s’affichées au-dessus seront affiche uniquement à ceux qui ont rejoint l’aperçu privé de ces fonctionnalités.

    Moniteur Azure comporte trois catégories de base de données d’analyse : le **journal d’activité**, les **mesures**et les **journaux de diagnostic**.

3. Cliquez sur **Ouvrir une session activité** pour vous assurer que la section journal activité est affichée.

    ![Carte de journal d’activité](./media/monitoring-get-started/monitor-act-log-blade.png)

    Le [**journal d’activité**](./monitoring-overview-activity-logs.md) décrit toutes les opérations effectuées sur les ressources dans votre abonnement. En utilisant le journal d’activité, vous pouvez déterminer le « quoi, qui et quand » pour les créer, mettre à jour ou supprimer les opérations sur les ressources dans votre abonnement. Par exemple, le journal d’activité indique si une application web a été arrêtée et qui l’a arrêté. Activité journal des événements sont stockés dans la plateforme et disponibles pour rechercher des 90 jours.
   
    Vous pouvez créer et enregistrer des requêtes pour les filtres courants, puis épingler les requêtes les plus importantes à un tableau de bord du portail pour que vous sachiez toujours si les événements qui répondent à votre critère se sont produites.

4. Filtrer l’affichage à un groupe de ressources sur la semaine dernière, puis cliquez sur le bouton **Enregistrer** .

    ![Enregistrer la requête de journal d’activité](./media/monitoring-get-started/monitor-act-log-save.png)

5. À présent, cliquez sur le bouton en **forme** .

    ![Cliquez sur code confidentiel pour le journal d’activité](./media/monitoring-get-started/monitor-act-log-pin.png)

    La plupart des vues de cette procédure peut être épinglée à un tableau de bord. Cela vous permet de créer une seule source d’informations pour les données opérationnelles dans vos services. 

6. Revenir à votre tableau de bord. Vous pouvez maintenant voir que la requête (et le nombre de résultats) sont affiche dans votre tableau de bord. Ceci est utile si vous souhaitez voir rapidement toutes les actions de grande ampleur qui se sont produites récemment dans votre abonnement, par exemple. un nouveau rôle a été affecté ou une machine virtuelle a été supprimée.

    ![Journal d’activité épinglées au tableau de bord](./media/monitoring-get-started/monitor-act-log-db.png)

7. Revenir à la vignette **Moniteur** , puis cliquez sur la section **indicateurs** . Vous devez tout d’abord sélectionner une ressource en filtrant et en sélectionnant à l’aide des options de liste déroulante en haut de la carte.

    ![Filtrer les ressources pour indicateurs](./media/monitoring-get-started/monitor-met-filter.png)

    Toutes les ressources Azure émettent des [**indicateurs**](./monitoring-overview-metrics.md). Cet affichage regroupe toutes les mesures dans un seul volet de verre afin que vous pouvez facilement comprendre les performances de vos ressources.

8. Une fois que vous avez sélectionné une ressource, toutes les mesures disponibles apparaissent sur le côté gauche de la carte. Vous pouvez plusieurs indicateurs de graphique à la fois en sélectionnant métriques et modifier la graphique type et la plage horaire. Vous pouvez également afficher toutes les alertes métriques définie sur cette ressource.

    ![Carte métrique](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Certaines mesures sont disponibles uniquement en activant [Insights Application](../application-insights/app-insights-overview.md) et/ou Windows ou Linux Azure Diagnostics sur la ressource.

9. Lorsque vous êtes satisfait de votre graphique, vous pouvez utiliser le bouton **épingle** pour épingler à votre tableau de bord.

10. Revenir à la carte **Moniteur** , puis cliquez sur **les journaux de Diagnostic**.

    ![Carte de journaux de diagnostic](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Les journaux de diagnostic**](monitoring-overview-of-diagnostic-logs.md) sont journaux émis *par* une ressource qui fournissent des informations sur le fonctionnement de cette ressource particulière. Par exemple, compteurs règle groupe de sécurité du réseau et des journaux des flux de travail logique d’application sont les deux types de journaux de diagnostic. Ces fichiers journaux peut être stockées dans un compte de stockage, diffusés en continu à un concentrateur événement et/ou envoyé au [Journal Analytique](../log-analytics/log-analytics-overview.md). Journal Analytique est produit de renseignements opérationnels de Microsoft pour la recherche avancée et les alertes.
   
    Dans le portail, vous pouvez afficher et filtrer une liste de toutes les ressources dans votre abonnement à identifier s’ils ont activé les journaux de diagnostic.

11. Cliquez sur une ressource dans la carte les journaux de diagnostic. Si les journaux de diagnostic sont stockés dans un compte de stockage, vous verrez une liste des journaux horaires que vous pouvez télécharger directement.

    ![Journaux de diagnostic pour une ressource](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Vous pouvez également cliquer sur **Les paramètres de Diagnostic**, qui permet de vous permet de définir ou modifier vos paramètres d’archivage à un compte de stockage, la diffusion aux événements Hubs, ou en envoyant à un espace de travail journal Analytique.

    ![Activer les journaux de diagnostic](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Si vous avez configuré les journaux de diagnostic au journal Analytique, vous pouvez ensuite y effectuer des recherches dans la section de **recherche des journaux** du portail.

12. Accédez à la section **alertes** de la cuillère moniteur.

    ![carte d’alertes au public](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Vous pouvez gérer toutes les [**alertes**](./monitoring-overview-alerts.md) sur vos ressources Azure. Cela inclut les alertes sur indicateurs, événements de journal d’activité (en privé preview), tests de site web Application perspectives (emplacements) et diagnostics proactifs Application perspectives. Alertes peuvent déclencher un message électronique doit être envoyé ou une publication HTTP vers une URL webhook.
   
13. Cliquez sur **Ajouter une alerte métrique** pour créer une alerte.

    ![Ajout d’alerte métrique](./media/monitoring-get-started/monitor-alerts-add.png)

    Vous pouvez ensuite épingler une alerte à votre tableau de bord pour voir facilement son état à tout moment.

14. La section Moniteur inclut également des liens vers des solutions de gestion de [Journal Analytique](../log-analytics/log-analytics-overview.md) et applications [d’Analyse de l’Application](../application-insights/app-insights-overview.md) . Ces autres produits Microsoft ont une forte intégration avec Azure moniteur.

15. Si vous n’utilisez pas Application Insights ou journal Analytique, sans doute que Azure moniteur est un partenariat actuel surveiller, enregistrer et d’alerte produits. Consultez notre [page partenaires](./monitoring-partners.md) pour une liste complète et des instructions sur la façon d’intégrer.

En suivant ces étapes et en épinglant toutes les mosaïques pertinents à un tableau de bord, vous pouvez créer des vues complètes de votre application et infrastructure comme celle-ci :

![Tableau de bord moniteur Azure](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Étapes suivantes
- Lisez la [vue d’ensemble du moniteur Azure](./monitoring-overview.md)
