<properties
    pageTitle="Vue d’ensemble des alertes dans Microsoft Azure | Microsoft Azure"
    description="Alertes permettent de contrôler les mesures de ressources Azure, les événements ou les journaux et recevoir une notification lorsqu’une condition que vous spécifiez est remplie."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Vue d’ensemble des alertes dans Microsoft Azure


Cet article décrit quelles sont les alertes, leurs avantages et comment commencer à utiliser leur utilisation.  

## <a name="what-are-alerts"></a>Que sont les alertes ?
Alertes constituent une méthode d’audits de surveillance des ressources Azure, événements, ou les journaux et puis averti lorsqu’une condition que vous spécifiez est remplie.

Vous pouvez recevoir des alertes basées sur :

- **Valeurs métriques**: cette alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans les deux sens. Autrement dit, les deux déclenche quand la condition est remplie tout d’abord et puis par la suite que lorsque la condition est n’est plus remplie.
- **Événements de journal d’activité**: cette alerte peut engendrer sur chaque événement ou uniquement lorsqu’un certain nombre d’événements se produire.

## <a name="alerts-in-different-azure-services"></a>Alertes dans différents services Azure

Alertes sont disponibles par les différents services, notamment :

- **Analyse de l’application**: vous permettent de conserver web test et métrique alertes. Voir [définir des alertes dans l’Application Insights](../application-insights/app-insights-alerts.md) et [analyser la disponibilité et la réactivité de tous les sites Web](../application-insights/app-insights-monitor-web-app-availability.md).
- **Journal Analytique (opérations Management Suite)**: permet le routage des journaux de diagnostic au journal Analytique. Opérations Management Suite permet métrique, le journal et autres types d’alertes. Pour plus d’informations, voir [alertes dans le journal Analytique](../log-analytics/log-analytics-alerts.md).  
- **Moniteur Azure**: permet d’alertes basées sur des valeurs métriques et événements de journal d’activité. Moniteur Azure inclut l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/dn931943.aspx).  Pour plus d’informations, reportez-vous [à l’aide du portail Azure, PowerShell ou l’interface de ligne de commande pour créer des alertes](insights-alerts-portal.md).

## <a name="alert-actions"></a>Actions d’alerte
Vous pouvez configurer une alerte pour effectuer les opérations suivantes :

- Envoyer des notifications par courrier électronique à l’administrateur de service, coadministrateurs ou adresses de messagerie supplémentaires que vous spécifiez.
- Appeler un webhook, qui vous permet de lancer des actions automation supplémentaires.

 ![Alertes expliqués](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Étapes suivantes

Obtenir des informations sur les règles d’alerte et les configurer à l’aide de :

- [Portail Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interface de ligne (commande)](insights-alerts-command-line-interface.md)
- [API REST moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
