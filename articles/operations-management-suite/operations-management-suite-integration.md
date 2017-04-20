<properties
   pageTitle="Intégration de la Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Outre les fonctionnalités standard de OMS, vous pouvez l’intégrer avec d’autres applications de gestion et services de proposer un environnement de gestion des hybrides, pour fournir des scénarios de gestion personnalisée à votre environnement, ou pour fournir une gestion personnalisée expérience pour vos clients.  Cet article fournit une vue d’ensemble de vos différentes options pour l’intégration avec OMS et des liens vers des articles fournissant des informations techniques détaillées."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Intégration de la Suite de gestion des opérations (OMS)

Opérations Management Suite est sur le nuage solution de gestion informatique de Microsoft qui vous permet de gérer et protéger vos localement et infrastructure en nuage.  Outre les fonctionnalités standard de OMS, vous pouvez l’intégrer avec d’autres applications de gestion et services de proposer un environnement de gestion des hybrides, pour fournir des scénarios de gestion personnalisée à votre environnement, ou pour fournir une gestion personnalisée expérience pour vos clients.  Cet article fournit une vue d’ensemble de vos différentes options pour l’intégration avec les services OMS et des liens vers des articles fournissant des informations techniques détaillées. 



## <a name="log-analytics"></a>Journal Analytique
Gestion des données collectées par journal Analytique sont stockées dans un référentiel qui est hébergé dans Azure.  Toutes les données stockées dans le référentiel est disponible dans les recherches de journal qui procurent une analyse rapide très grandes quantités de données.  Vos besoins d’intégration peuvent être pour remplir le référentiel avec de nouvelles données rendre disponibles pour l’analyse, ou pour extraire les données dans le référentiel pour fournir une nouvelle visualisation ou pour intégrer à un autre outil de gestion.

Chaque élément de données dans le référentiel est stocké en tant qu’enregistrement.  Lorsque vous remplissez le référentiel, vous devez fournir aux utilisateurs le type d’enregistrement qui utilise votre solution et une description de ses propriétés.  Lorsque vous récupérez des données, vous avez besoin de ces informations sur les données avec lequel vous travaillez.

![Complétez le référentiel OMS](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Remplir le référentiel Analytique de journal
Il existe plusieurs méthodes pour remplir le référentiel OMS.  La méthode que vous utilisez varient selon certains facteurs tels que l’endroit où se trouve la source de données, le format des données et dont vous devez prendre en charge les clients.  Une fois que les données sont stockées dans le référentiel, il importe peu comment elles ont été collectées.

Les sections suivantes décrivent les différentes options pour remplir le référentiel OMS.

#### <a name="connected-sources-and-data-sources"></a>Connecté Sources et sources de données 
Sources connectées sont les emplacements où les données peuvent être récupérées pour le référentiel OMS.  Sources de données et Solutions exécutent sur les Sources connectées et définissent les données collectées.  Si votre application écrit des données à un de ces sources de données, vous pouvez le collecter en configurant la source de données.  Par exemple, si votre application crée des événements de journal système, puis elles peuvent être collectées par la source de données du journal système sur un agent Linux.

- [Sources de données dans le journal Analytique](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Solutions

Solutions étendent les fonctionnalités de OMS.  Une solution peut collecter des données provenant de la source connectée ou elle peut effectuer une analyse sur collectées déjà dans le référentiel des enregistrements.  Chaque solution fournie par Microsoft a un article individuel qui fournit les informations sur les données qu’il collecte.

- [Solutions de journal Analytique](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>Collecteur de données HTTP API

L’API de collecteur de données journal Analytique HTTP est une API REST qui vous permet d’ajouter des données JSON vers le référentiel de journal Analytique.  Vous pouvez tirer parti de cette API lorsque vous avez une application qui ne fournit des données à l’aide d’une des autres sources de données ou solutions.  Il peut être utilisé pour remplir le référentiel à partir de n’importe quel client qui peut appeler l’API et ne dépend pas de la planification de la collecte d’une source de données ou une solution.

- [Ouvrez une session collecteur de données HTTP Analytique API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Récupérer des données à partir du référentiel de journal Analytique

Il existe plusieurs méthodes pour récupérer des données à partir du référentiel OMS.  Vous pouvez souhaitez que les utilisateurs pour récupérer des données à l’aide de la console OMS et lui fournir les différents types de visualisations et analyse.  Vous pouvez également récupérer les données à partir d’un processus externe tel qu’une autre solution de gestion.

#### <a name="log-searches"></a>Journal des recherches

Toutes les données stockées dans le référentiel OMS est disponible via le journal des recherches.  Les utilisateurs peuvent effectuer une analyse de leur propre ad hoc dans la console OMS ou créer un tableau de bord avec une visualisation pour une recherche de journal spécifique.  Solutions peuvent contenir des vues personnalisées avec des visualisations en fonction de critères de recherche prédéfinis.  Vous pouvez utiliser l’API de recherche de journal pour accéder aux données dans le référentiel OMS à partir d’un outil d’administration ou application externes.  

- [Journal des recherches dans le journal Analytique](../log-analytics/log-analytics-log-searches.md)
- [Recherche des journaux de journal Analytique API REST](../log-analytics/log-analytics-log-search-api.md)
- [Applets de commande journal Analytique](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Affichages personnalisés 
Le Concepteur de vue permet de créer des affichages personnalisés dans la console OMS permettant aux utilisateurs de visualisation et l’analyse des données dans votre solution.  Chaque affichage comprend une vignette qui s’affiche dans la page principale de la console et un nombre quelconque de composants de visualisation basés sur des recherches de journal que vous définissez.
  
- [Concepteur de vue Analytique journal](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

Journal Analytique peut automatiquement exporter des données à partir du référentiel OMS dans Power BI afin que vous pouvez exploiter ses visualisations et outils d’analyse.  Elle effectue cette exportation sur un planning afin que les données sont mises à jour. 

- [Exporter des données de journal Analytique à Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automatisation

OMS peuvent automatiser le processus de réagir aux données collectées ou pour effectuer d’autres fonctions de gestion.  Il peut collecter les données à partir de votre application et l’insérer dans le référentiel OMS, ou vous pouvez l’automatiser la correction d’un problème connu en réponse aux données figurant dans le référentiel. 

![Automatisation](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Procédures opérationnelles

Procédures opérationnelles dans Azure Automation exécutent des scripts PowerShell et flux de travail dans le cloud Azure.  Vous pouvez les utiliser pour gérer les ressources dans Azure ou autres ressources qui sont accessibles à partir du cloud.  Procédures opérationnelles peut également être exécuté dans un centre de données locale à l’aide de hybride Runbook concerné.  Vous pouvez démarrer un runbook à partir du portail Azure ou à partir de l’utilisation de plusieurs méthodes de PowerShell ou de l’API d’automatisation des processus externes.

- [Démarrage d’un runbook dans Azure Automation](../automation/automation-starting-a-runbook.md)
- [Applets de commande Automation Azure](https://msdn.microsoft.com/library/dn690262.aspx)
- [API REST Automation](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automatisation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertes

Règles d’alerte exécutent automatiquement des recherches de journaux selon un planning.  Si les résultats correspondent aux critères particuliers l’alerte qui en résulte peut démarrer un runbook dans Azure Automation ou appeler une webhook qui peut démarrer un processus externe.  Les deux conditions ces réponses peuvent inclure les détails de l’alerte, y compris les données renvoyées dans les journaux.

- [Alertes dans le journal Analytique](../log-analytics/log-analytics-alerts.md)
- [API alerte journal Analytique](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Sauvegarde et restauration de Site

Azure sauvegarde et restauration de Site proposent des services pour protéger vos données d’entreprise et d’assurer la disponibilité des serveurs et les applications.  Vous pouvez utiliser ces services pour effectuer des scénarios tels que fournissant des services de sauvegarde de votre application ou initiation du basculement d’une machine virtuelle.

- [Applets de commande de sauvegarde Azure](https://msdn.microsoft.com/library/mt619253.aspx)
- [Récupération de Site Azure API REST](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Applets de commande de récupération sites Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Solutions personnalisées

Vous pouvez serez intégrer logique d’intégration à une solution personnalisée pour exécuter dans votre espace de travail ou dans l’espace de travail d’un client.  Votre solution peut inclure une des méthodes d’intégration dans cet article en plus des autres ressources à fournir un scénario de gestion complète.  Les ressources de la solution sont empaquetés tel que lorsque la solution est supprimée, toutes les ressources qu’il a créé sont supprimés de l’espace de travail OMS et d’abonnement Azure.

Par exemple, votre solution peut inclure un runbook Automation pour collecter et traiter les données et y ajouter le référentiel de journal Analytique à l’aide de l’API de collecteur de données HTTP.  Vous pouvez également inclure un affichage personnalisé qui présente et analyse les données collectées.  

- Créer des solutions personnalisées (bientôt disponible)    

## <a name="next-steps"></a>Étapes suivantes
- Référence du [Kit de développement logiciel OMS](operations-management-suite-sdk.md) pour des informations techniques sur l’automatisation des services OMS.  
