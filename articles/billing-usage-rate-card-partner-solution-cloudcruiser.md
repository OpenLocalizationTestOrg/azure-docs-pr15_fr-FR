<properties
   pageTitle="Cloud Cruiser et l’intégration de l’API de facturation de Microsoft Azure | Microsoft Azure"
   description="Fournit une perspective unique à partir de Microsoft Azure facturation partenaire Cruiser Cloud, sur leur expérience intégration de l’API de facturation Azure dans leur produit.  Ceci est particulièrement utile pour les clients Azure et Cruiser Cloud qui intéressent à l’aide de/essayez Cruiser Cloud de Microsoft Azure Pack."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser et l’intégration de l’API de facturation de Microsoft Azure

Cet article décrit comment les informations collectées à partir de la nouvelle API de facturation Microsoft Azure peuvent être utilisées dans le Cloud Cruiser pour la simulation de coût de flux de travail et analyse.

## <a name="azure-ratecard-api"></a>API RateCard Azure
L’API RateCard fournit des informations sur les taux Azure. Après avoir authentifié avec les informations d’identification appropriées, vous pouvez interroger l’API pour collecter les métadonnées sur les services disponibles sur Azure, ainsi que les taux associés à votre ID d’offre.

Voici un exemple de réponse à partir de l’API affichant les prix pour la A0 instance (Windows) :

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interface de Cruiser à RateCard Azure API le cloud
Cruiser cloud peut exploiter les informations RateCard API de différentes manières. Pour cet article, nous vous montrent comment elle peut être utilisée pour rendre IaaS coût simulation et analyse de la charge de travail.

Pour illustrer ce cas d’utilisation, imaginons une charge de travail de plusieurs instances s’exécutant sur Microsoft Azure Pack (WAP). L’objectif est de simuler ce même charge de travail sur Azure et estimer les coûts liés aux telle migration. Afin de créer cette simulation, il existe deux principales tâches à effectuer :

1. **Importer et traiter les informations collectées à partir de l’API RateCard du service.** Cette tâche est également effectuée sur les classeurs, où l’extrait de l’API RateCard est transformé et publié dans une nouvelle offre de taux. Cette nouvelle offre de taux qui être utilisé dans les simulations pour estimer les prix Azure.

2. **Normaliser WAP services et Azure pour IaaS.** Par défaut, les services WAP sont basés sur des ressources (processeur, taille de la mémoire, taille du disque, etc.) lors de la Azure services sont basés sur la taille d’instance (A0, A1, A2, etc.). Cette première tâche peuvent être effectuées par ETL moteur du Cloud Cruiser, appelé classeurs, où ces ressources sont disponibles sur la taille d’instance, semblable aux services instance d’Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importer des données à partir de l’API RateCard

Les classeurs de Cruiser cloud offrent une façon automatisée pour collecter et traiter des informations à partir de l’API RateCard.  Les classeurs (chargement de la transformation extraire) ETL autorisent vous permettent de configurer la collection de sites, la transformation et la publication de données dans la base de données Cruiser Cloud.

Chaque classeur peut avoir une ou plusieurs collections, qui vous permet de corrélation des informations de différentes sources pour compléter ou augmenter les données d’utilisation. Les captures d’écran de deux suivants montrent comment créer une *collection de sites* dans un classeur existant et l’importation d’informations dans la *collection de sites* à partir de l’API RateCard :

![Figure 1 : création d’une nouvelle collection][1]

![Figure 2 : importer des données à partir de la nouvelle collection][2]

Après avoir importé les données dans le classeur, il est possible de créer plusieurs étapes et les processus de transformation, de modifier et de modéliser les données. Dans cet exemple, étant donné que nous intéresse uniquement infrastructure-as-a-Service (IaaS), nous pouvons utiliser les opérations de transformation pour supprimer les lignes inutiles, ou des enregistrements, relatives aux services différent IaaS.

La capture d’écran suivante montre les étapes de transformation permettant de traiter les données collectées à partir de l’API RateCard :

![Figure 3 : opérations de Transformation pour traiter les données collectées à partir de l’API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Plans de définition de taux et nouveaux Services

Il existe différentes façons de définir des services de Cloud Cruiser. Une des options consiste à importer les services dans les données d’utilisation. Cette méthode est généralement utilisée lorsque vous travaillez avec des nuages publics, où les services sont déjà définies par le fournisseur.

Un Plan de taux est un ensemble de taux ou les prix qui peuvent être appliqués à différents services, en fonction des dates d’effet, ou un groupe de clients, parmi d’autres options. Plans de taux également utilisés sur Cruiser Cloud pour créer la simulation ou scénarios « What-if », à comprendre comment les modifications dans les services peuvent affecter le coût total d’une charge de travail.

Dans cet exemple, nous utiliserons les informations du service à partir de l’API RateCard pour définir de nouveaux services de Cloud Cruiser. Dans la même manière, nous pouvons utiliser les taux associés aux services pour créer un nouveau Plan de taux sur Cruiser Cloud.

À la fin du processus de transformation, il est possible de créer une nouvelle étape et publier les données à partir de l’API RateCard en tant que taux et de nouveaux services.

![Figure 4 : les données à partir de l’API RateCard de publication en tant que nouveaux Services et taux][4]

### <a name="verify-azure-services-and-rates"></a>Vérifier les taux et les Services Azure

Après publication les services et les taux, vous pouvez vérifier la liste des services importées dans l’onglet *Services* de Cloud Cruiser :

![Figure 5 : vérifier les nouveaux Services][5]

Sous l’onglet *Taux Plans* , vous pouvez vérifier la nouvelle offre de taux appelée « AzureSimulation » avec les taux importée à partir de l’API RateCard.

![Figure 6 : vérification du nouveau Plan de taux et de taux associé.][6]

### <a name="normalize-wap-and-azure-services"></a>Normaliser WAP et Services Azure

Par défaut, WAP fournit des informations de l’utilisation en fonction de l’utilisation de cluster, la mémoire et ressources réseau. Dans Cruiser Cloud, vous pouvez définir vos services basés directement sur l’attribution ou limitée l’utilisation de ces ressources. Par exemple, vous pouvez définir un taux de base pour chaque heure de l’UC, ou de charger les Go de mémoire affectée à une instance.

Dans cet exemple, afin de comparer les coûts entre WAP et Azure, nous devons agréger l’utilisation des ressources sur WAP par lots, qui peuvent alors être appliqués aux services Azure. Cette transformation peut être facilement implémentée dans les classeurs :

![Figure 7 : transformation des données WAP pour normaliser services][7]

La dernière étape en le classeur est publier les données dans la base de données Cruiser Cloud. Au cours de cette étape, les données d’utilisation sont maintenant regroupées dans des services (qui correspondent aux Services Azure) et liées au taux par défaut pour créer les frais.

Après avoir terminé le classeur, vous pouvez automatiser le traitement des données, en ajoutant une tâche dans le planificateur et en spécifiant la fréquence et l’heure pour le classeur à exécuter.

![Figure 8 : planification de classeur][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Créer des rapports pour l’analyse de Simulation coût la charge de travail

Une fois que l’utilisation est collectée et frais sont chargées dans la base de données Cruiser Cloud, nous pouvons tirer parti du module Cloud Cruiser perspectives pour créer la charge de travail simulation que nous avons besoin de coût.

Pour montrer ce scénario, nous avons créé le rapport suivant :

![Comparaison des coûts][9]

Le graphique supérieur présente une comparaison des coûts par les services, comparant le prix de l’exécution de la charge de travail pour chaque service spécifique entre WAP (bleu foncé) et Azure (bleu clair).

Le graphique du bas montre les mêmes données mais répartis par département. Ceci affiche les coûts pour chaque service à exécuter leur charge de travail sur WAP et Azure, ainsi que la différence entre eux dans la barre d’épargne (vert).

## <a name="azure-usage-api"></a>API de l’utilisation Azure


### <a name="introduction"></a>Introduction

Microsoft a récemment introduit l’API de l’utilisation d’Azure, permettant aux abonnés de récupérer par programme les données d’utilisation pour obtenir des informations dans leur consommation. Il s’agit d’une bonne nouvelle pour les clients Cloud Cruiser qui peuvent tirer parti du jeu de données plus riche disponible via cette API.

Nuage Cruiser peut tirer parti de l’intégration avec l’API de l’utilisation de plusieurs manières. La précision (toutes les heures les informations d’utilisation) et fournissent des informations sur les métadonnées ressources disponibles via l’API dataset nécessaire pour prendre en charge les modèles Showback ou refacturation flexibles. 

Dans ce didacticiel, nous présente un exemple de comment Cruiser Cloud peut tirer parti les informations d’utilisation des API. Plus précisément, nous créer un groupe de ressources sur Azure, associer des balises pour la structure de compte, puis décrivez le processus de tirant et les informations de balise en nuage Cruiser de traitement.
 
L’objectif final est de pouvoir créer des rapports comme celui ci-dessous et être en mesure d’analyser des coûts et consommation basée sur la structure de compte remplie par les balises.

![Figure 10 : rapport avec répartitions qui utilisent des balises][10]

### <a name="microsoft-azure-tags"></a>Indicateurs de Microsoft Azure

Les données disponibles via l’API de l’utilisation de Azure incluent non seulement les informations de consommation, mais également les métadonnées des ressources, y compris les balises associés. Balises offrent un moyen simple pour organiser vos ressources, mais pour être efficace, vous devez vous assurer que :

- Balises sont correctement appliquées aux ressources au moment de la mise en service
- Balises servent correctement le processus de Showback/refacturation pour lier l’utilisation de la structure de compte de l’organisation.

Ces deux exigences peuvent être difficiles, en particulier lorsqu’il y a un processus manuel sur la disposition ou le chargement côté. Balises tapés, incorrectes ou même manquantes sont courantes plaintes des clients lorsqu’à l’aide de balises et ces erreurs simplifient la sur le côté tarification très difficile.

Avec la nouvelle API de l’utilisation de Azure, Cloud Cruiser pouvez extraire des informations de liaison de ressources et via un outil ETL sophistiqué intitulé classeurs, résoudre les erreurs de liaison courantes. Par transformation à l’aide d’expressions régulières et corrélation des données, Cruiser Cloud peut identifier les ressources incorrectement balisés et appliquer les balises appropriées, garantissant l’association correcte des ressources avec le client.

Dans la partie en cours de chargement, Cruiser Cloud permet d’automatiser le processus de Showback/refacturation et ils peuvent utiliser les informations de balise pour lier l’utilisation du consommateur approprié (service, Division, Project, etc.). Cette automatisation fournit un grand d’amélioration du produit et permet de garantir un processus de charge cohérent et contrôlable.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Création d’un groupe de ressources avec indicateurs dans Microsoft Azure
La première étape de ce didacticiel consiste à créer un groupe de ressources dans le portail Azure, puis créer de nouveaux marqueurs à associer aux ressources. Dans cet exemple, nous allons créer les balises suivantes : département, environnement, propriétaire, Project.

La capture d’écran ci-dessous montre un exemple de groupe de ressources avec les balises associées.

![Figure 11 : groupe de ressources avec des balises associées sur portail Azure][11]

L’étape suivante consiste à extraire les informations à partir de l’API de l’utilisation dans Cruiser Cloud. L’API de l’utilisation actuellement fournit des réponses au format JSON. Voici un exemple de données récupérées :


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importer des données à partir de l’API de l’utilisation dans Cruiser Cloud

Les classeurs de Cruiser cloud offrent une façon automatisée pour collecter et traiter des informations à partir de l’API de l’utilisation. Un classeur (chargement de la transformation extraire) ETL vous autorise à configurer la collection de sites, la transformation et la publication de données dans la base de données Cruiser Cloud.

Chaque classeur peut avoir une ou plusieurs collections de sites. Cela vous permet de corrélation des informations de différentes sources pour compléter ou augmenter les données d’utilisation. Dans cet exemple, nous allons créer une nouvelle feuille dans le classeur modèle Azure (_UsageAPI)_ et définir une nouvelle _collection de sites_ pour importer des informations à partir de l’API de l’utilisation.

![Figure 3 : les données d’utilisation API importées dans la feuille UsageAPI][12]

Notez que ce classeur comporte déjà des autres feuilles pour importer des services à partir d’Azure (_ImportServices_) et traiter les informations de consommation de l’API de facturation (_PublishData_).

Nous sera ensuite utiliser l’API de l’utilisation pour remplir la feuille _UsageAPI_ et faire correspondre les informations avec les données de consommation de l’API facturation sur la feuille _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>Les informations de balise à partir de l’API de l’utilisation de traitement

Après avoir importé les données dans le classeur, nous allons créer des étapes de transformation dans la feuille _UsageAPI_ afin de traiter les informations à partir de l’API. Première étape consiste à utiliser un traitement « JSON Fractionner » pour extraire les balises d’un seul champ, puis créer des champs pour chacun d'entre eux (département, Project, propriétaire et environnement).

![Figure 4 : créer des champs pour les informations de balise][13]

Notez les informations de balise (jaune) ne figure pas dans le service « Mise en réseau », mais nous pouvons vérifier qu’il fait partie du même groupe de ressources en consultant le champ _ResourceGroupName_ . Étant donné que nous avons les balises pour les autres ressources à partir de ce groupe de ressources, nous pouvons utiliser ces informations pour appliquer les balises manquantes à cette ressource plus loin dans le processus.

L’étape suivante consiste à créer une table de choix associant les informations des balises à la _ResourceGroupName_. Servira de cette table de choix à l’étape suivante pour enrichir les données de consommation par des balises.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Ajouter les informations de balise pour les données de consommation

Nous pouvons maintenant accéder à la feuille _PublishData_ , qui permet de traiter les informations de consommation de l’API de facturation et ajoutez les champs extraites les balises. Ce processus est exécuté en recherchant dans la table de choix créée à l’étape précédente, à l’aide de la _ResourceGroupName_ comme clé pour les recherches.

![Figure 5 : remplissage de la structure de compte avec les informations les recherches][14]

Notez que les champs de structure de compte approprié pour le service « Mise en réseau » ont été appliqués, résoudre le problème avec les balises manquants. Nous également rempli les champs de structure de compte pour les ressources différent de notre objectif groupe de ressources avec « Autres », afin de les différencier sur les rapports.

Nous devons maintenant simplement ajouter une étape pour publier les données d’utilisation. Au cours de cette étape, les taux appropriés pour chaque service définis dans notre taux planifier seront appliquées pour les informations d’utilisation, avec les frais qui en résulte chargé dans la base de données.

Le plus intéressant est que vous avez uniquement traitée ce processus une seule fois. Lorsque le classeur est terminé, vous devez simplement ajouter au planificateur et s’exécutera toutes les heures ou tous les jours à la date planifiée. Il est alors qu’une question de création de rapports ou personnalisation existantes, afin d’analyser les données pour obtenir des informations significatives à partir de votre utilisation cloud.

### <a name="next-steps"></a>Étapes suivantes

+ Pour obtenir des instructions détaillées sur la création de rapports et des classeurs Cruiser Cloud, reportez-vous à online [documentation](http://docs.cloudcruiser.com/) (valide connexion requise du Cloud Cruiser).  Pour plus d’informations sur le Cloud Cruiser, veuillez contacter [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Pour une vue d’ensemble des RateCard APIs et utilisation des ressources Azure, consultez [Familiarisez-vous dans votre consommation de ressources de Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Consultez la [Référence des API REST facturation Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) pour plus d’informations sur les deux API qui font partie de l’ensemble des API fournies par le Gestionnaire de ressources Azure.
+ Si vous voulez lancez-vous dans l’exemple de code, consultez nos exemples de Code de l’API pour le facturation d’Azure Microsoft sur des [Échantillons de Code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Pour en savoir plus
+ Consultez l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figure 1 : création d’une nouvelle collection"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figure 2 : importer des données à partir de la nouvelle collection"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figure 3 : opérations de Transformation pour traiter les données collectées à partir de l’API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figure 4 : les données à partir de l’API RateCard de publication en tant que nouveaux Services et taux"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figure 5 : vérifier les nouveaux Services"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figure 6 : vérification du nouveau Plan de taux et de taux associé."
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 : transformation des données WAP pour normaliser services"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figure 8 : planification de classeur"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figure 9 : exemple de rapport pour le scénario de comparaison de coût de la charge de travail"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figure 10 : rapport avec répartitions qui utilisent des balises"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figure 11 : groupe de ressources avec des balises associées sur portail Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figure 12 : les données d’utilisation API importées dans la feuille UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figure 13 : créer des champs pour les informations de balise"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figure 14 : remplissage de la structure de compte avec les informations les recherches"
