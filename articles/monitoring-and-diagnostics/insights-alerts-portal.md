<properties
    pageTitle="Portail Azure permet de créer des alertes pour des services Azure | Microsoft Azure"
    description="Le portail Azure permet de créer des alertes Azure, qui peuvent engendrer des notifications ou automation lorsque les conditions spécifiées sont remplies."
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
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Portail Azure permet de créer des alertes pour des services Azure

> [AZURE.SELECTOR]
- [Portail](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [INFRASTRUCTURE DU LANGAGE COMMUN](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment configurer des alertes Azure à l’aide du portail Azure.   

Vous pouvez recevoir une alerte basée sur les audits de surveillance pour ou des événements sur vos services Azure.

- **Valeurs métriques** - l’alert se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous attribuez dans les deux sens. Autrement dit, les deux déclenche quand la condition est remplie tout d’abord et puis par la suite que lorsque la condition est n’est plus remplie.    
- **Événements du journal activité** - une alerte peut déclencher sur *chaque* événement, ou uniquement lorsqu’un certain nombre d’événements se produire.


Vous pouvez configurer une alerte lorsqu’il déclenche, procédez comme suit :

- envoyer des notifications par courrier électronique à l’administrateur de service et coadministrateurs
- envoyer des messages électroniques aux messages électroniques supplémentaires que vous spécifiez.
- appeler un webhook
- lancer l’exécution d’une procédure opérationnelle Azure (uniquement à partir du portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide

- [Portail Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de ligne (commande)](insights-alerts-command-line-interface.md)
- [API REST moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une métrique grâce au portail Azure

1. Dans le [portail](https://portal.azure.com/), localisez la ressource que vous voulez contrôler et sélectionnez.

2. Sélectionnez **alertes** ou des **règles d’alerte** sous la section surveillance. Le texte et l’icône peuvent varier légèrement pour différentes ressources.  

    ![Surveillance des mots clés](./media/insights-alerts-portal/AlertRulesButton.png)


3. Sélectionnez la commande **Ajouter une alerte** et renseignez les champs.

    ![Ajout d’alerte](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nom de** l’alerte de règle, puis choisissez une **Description**, qui affiche également dans des messages électroniques de notification.
5. Sélectionnez la **métrique** que vous souhaitez analyser, puis cliquez sur une valeur **d’une Condition** et une **seuil** pour la mesure. Choisissez également la **période** pendant laquelle la règle métrique doit être remplie avant l’alertes déclencheurs. Par exemple, si vous utilisez la période « PT5M » et votre alerte cherche processeur supérieure à 80 %, l’alerte se déclenche lors de l’UC a été régulièrement ci-dessus 80 % de 5 minutes. Une fois que le premier déclencheur se produit, il déclenche à nouveau lors de l’UC reste inférieure à 80 % de 5 minutes. La mesure processeur se produit toutes les minutes.   

6. Vérifier les **propriétaires de messagerie …** si vous souhaitez que les administrateurs et coadministrateurs à être envoyé par courrier électronique lorsque l’alerte se déclenche.

7. Si vous souhaitez que les messages électroniques supplémentaires pour recevoir une notification lorsque l’alerte se déclenche, ajoutez-les dans le champ **administrateurs supplémentaires email(s)** . Séparez les messages électroniques multiples par des points-virgules-*email@contoso.com;email2@contoso.com*

8. Placer un URI valide dans le champ **Webhook** si vous voulez qu’il appelé lorsque l’alerte se déclenche.

9. Si vous utilisez Automation Azure, vous pouvez sélectionner une procédure opérationnelle à exécuter lorsque l’alerte se déclenche.

10. Sélectionnez **OK** lorsque vous avez terminé pour créer l’alerte.   

Après quelques minutes, l’alerte est active et déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes

Une fois que vous avez créé une alerte, vous pouvez le sélectionner et :

- Afficher un graphique affichant le seuil de métrique et les valeurs réelles du jour précédent.
- Modifier ou supprimer.
- **Désactiver** ou **Activer** dessus si vous voulez temporairement arrêter ou reprendre recevoir des notifications qui.



## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un aperçu de surveillance Azure](monitoring-overview.md) , y compris les types d’informations que vous pouvez collecter et surveiller.
* Apprenez-en davantage sur [webhooks configuration dans les alertes](insights-webhooks-alerts.md).
* Découvrez les [Procédures opérationnelles Automation Azure](..\automation\automation-starting-a-runbook.md).
* Obtenez une [vue d’ensemble des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) et recueillir des métriques haute fréquence détaillées sur votre service.
* Obtenez une [vue d’ensemble de la collecte de métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service n’est disponible et injoignable.
