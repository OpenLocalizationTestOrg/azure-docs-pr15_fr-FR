<properties
   pageTitle="Gestionnaire de ressources pris en charge les services | Microsoft Azure"
   description="Décrit les fournisseurs de ressources qui prennent en charge le Gestionnaire de ressources, leurs schémas et versions API disponibles et les régions hébergeant les ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Gestionnaire de ressources fournisseurs, régions, versions API et schémas

Azure Resource Manager fournit une nouvelle méthode pour déployer et gérer les services qui composent vos applications. Prend en charge la plupart des, mais pas tous les services Gestionnaire de ressources et des services prennent en charge le Gestionnaire de ressources que partiellement. Cette rubrique fournit une liste de fournisseurs de ressources pris en charge pour le Gestionnaire de ressources Azure.

Lorsque vous déployez vos ressources, vous devez également connaître les régions ces ressources de support et les versions de l’API sont disponibles pour les ressources. La section [régions pris en charge](#supported-regions) vous montre comment déterminer les régions de travail pour votre abonnement et les ressources. La section [API pris en charge les versions](#supported-api-versions) vous montre comment déterminer les versions de l’API que vous pouvez utiliser.

Pour voir quels services sont prises en charge dans le portail Azure et le portail classique, consultez [graphique Azure disponibilité du portail](https://azure.microsoft.com/features/azure-portal/availability/). Pour afficher les services qui prennent en charge les ressources déplacement, voir [déplacer des ressources à nouveau groupe de ressources ou d’abonnement](resource-group-move-resources.md).

Les tableaux suivants répertorient les Microsoft services prise en charge de déploiement et la gestion via le Gestionnaire de ressources et qui n’est pas le cas. Le lien dans la colonne **Modèles de démarrage rapide** envoie une requête vers le référentiel de modèles de démarrage rapide d’Azure pour le fournisseur de ressources spécifié. Modèles de démarrage rapide sont ajoutés et fréquemment mis à jour. La présence d’un lien d’un service particulier ne signifie pas nécessairement que la requête renvoie les modèles à partir du référentiel. Il existe également de nombreux fournisseurs tiers ressource qui prennent en charge le Gestionnaire de ressources. Vous découvrez comment afficher tous les fournisseurs de ressources dans la section [types et fournisseurs de ressources](#resource-providers-and-types) .


## <a name="compute"></a>Cluster

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------------------------ |-------- | ------ | ------ |
| Traitement par lots   | Oui     | [Lot reste](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Conteneur | Oui | [Service de conteneur reste](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Services du cycle de vie Dynamics | Oui  |      |        |  |
| Jeux d’échelle | Oui | [Échelle définie reste](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| TISSU de service | Oui  | [Service tissu reste](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Machines virtuelles | Oui | [MACHINE VIRTUELLE RESTE](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [desmachines virtuelles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Machines virtuelles (classique) | Limité | - | - | - |
| Application Remote | N°      | -  | - | - |
| Services de cloud (classique) | Limité (voir ci-dessous) | - | - | - |

Machines virtuelles (classique) fait référence à des ressources qui ont été déployés via le modèle de déploiement classique, plutôt que par le biais du modèle de déploiement du Gestionnaire de ressources. En règle générale, ces ressources ne prennent pas en charge les opérations de gestionnaire de ressources, mais il existe certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, voir [Présentation du gestionnaire ressource déploiement et déploiement classique](resource-manager-deployment-model.md). 

Services de cloud (classiques) peuvent être utilisés avec d’autres ressources classiques ; Toutefois, les ressources classiques ne prennent pas parti de toutes les fonctionnalités du Gestionnaire de ressources et ne sont pas une option intéressante pour solutions futures. À la place, pensez à modifier votre infrastructure d’application pour utiliser les ressources des espaces de noms Microsoft.Compute, Microsoft.Storage et Microsoft.Network.


## <a name="networking"></a>Mise en réseau

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------- | ------ | ------ |
| Passerelle d’application | Oui | [Application passerelle reste](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Oui | [RESTE DE DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Oui  | [ExpressRoute reste](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Équilibrage de charge | Oui  | [Équilibrage de charge reste](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gestionnaire de trafic | Oui | [Gestionnaire de trafic reste](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Réseaux virtuels | Oui| [Réseau virtuel reste](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Passerelle VPN | Oui | [Passerelle réseau reste](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connexions](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Espace de stockage

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Espace de stockage | Oui  | [Stockage reste](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Compte de stockage](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Oui  |         |        |  |

## <a name="databases"></a>Bases de données

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Oui  | [DocumentDB reste](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis Cache | Oui |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de données SQL | Oui | [Base de données SQL reste](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Data Warehouse SQL | Oui |   |      |


## <a name="web--mobile"></a>Web et Mobile

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Applications API | Oui |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Applications API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gestion des API | Oui  | [Gestion des API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Contenu modérateur | Oui |   |   |   |
| Application de fonction | Oui |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Applications de logique | Oui   | [API REST de Service de flux de travail](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Applications mobiles | Oui |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Engagements mobiles | Oui  |  [Engagement mobile reste](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Recherche | Oui  | [Recherche reste](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Applications Web | Oui |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Analytique

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------- | ------ | ------ |
| Catalogue de données | Oui  | [Catalogue de données reste](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Données par défaut | Oui | [Données usine reste](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Données Lake Analytique | Oui |   |   [2015-10-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Données Lake Store | Oui  | [Données Lake Store reste](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [2015-10-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Oui | [HDInsights reste](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Apprentissage automatique | Oui | [Apprentissage reste de l’ordinateur](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [2016-05-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Flux Analytique | Oui  | [Vapeur Analytique reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | Oui | [Power BI incorporé reste](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Analyse décisionnelle

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Services cognitifs | Oui |  | [2016-02-01-aperçu](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet des objets

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Concentrateur de l’événement | Oui  | [Événement concentrateur reste](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Oui | [Concentrateur IoT reste](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notification Hubs | Oui | [Concentrateur de notification reste](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Média et CDN

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| CDN | Oui | [RESTE CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [04-02-2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Service de support | Oui | [Services de support reste](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Intégration hybride

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Services BizTalk | Oui |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Service de récupération | Oui | [Récupération reste du site](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus des services | Oui | [Service Bus reste](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identité et gestion de l’accès 

Azure Active Directory fonctionne avec le Gestionnaire de ressources pour activer le contrôle d’accès basé sur un rôle pour votre abonnement. Pour en savoir plus sur l’utilisation de contrôle d’accès basé sur un rôle et Active Directory, consultez [Contrôle d’accès basé sur un rôle Azure](./active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Services pour les développeurs 

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Analyse de l’application | Oui  | [Perspectives reste](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Cartes Bing | Oui  |          |        |  |
| Ateliers DevTest | Oui |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Compte de Visual Studio | Oui   |          | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Gestion et la sécurité

| Service | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Automatisation | Oui | [Automatisation reste](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Archivage sécurisé clé | Oui | [Archivage sécurisé clé reste](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Archivage sécurisé clé](resource-manager-template-keyvault.md)<br />[Code secret de l’archivage sécurisé clés](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Perspectives opérationnelles | Oui |          |        |  |
| Planificateur | Oui  | [Planificateur reste](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sécurité (preview) | Oui | [Sécurité reste](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Gestionnaire de ressources

| Fonctionnalité | Gestionnaire de ressources activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorisation | Oui | [Verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Contrôle d’accès basé sur un rôle](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Verrouillage des ressources](resource-manager-template-lock.md)<br />[Attributions de rôle](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressources | Oui | [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Liens vers les ressources](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Types et les fournisseurs de ressources

Lorsque vous déployez des ressources, vous devez souvent récupérer des informations sur les fournisseurs de ressources et les types. Vous pouvez récupérer ces informations via l’API REST, PowerShell Azure ou Azure infrastructure du langage commun.

Pour travailler avec un fournisseur de ressources, ce fournisseur ressource doit être enregistré avec votre compte. Par défaut, de nombreux fournisseurs de ressources sont automatiquement enregistrées ; Toutefois, vous devrez peut-être enregistrer manuellement certains fournisseurs de ressources. Les exemples ci-dessous illustrent comment obtenir l’état de l’inscription d’un fournisseur de ressources et enregistrer le fournisseur de ressources, si nécessaire.

### <a name="portal"></a>Portail

Vous pouvez facilement repérer une liste de fournisseurs de ressources pris en charge les étapes suivantes :

1. Sélectionnez **Mes autorisations**.

    ![mes autorisations](./media/resource-manager-supported-services/my-permissions.png)

2. Sélectionnez **l’état des ressources fournisseur**

    ![état de fournisseur de ressources](./media/resource-manager-supported-services/resource-provider-status.png)

3. Vous consultez la liste complète des fournisseurs de ressources pour votre abonnement.

    ![liste de fournisseurs de ressources](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>API REST

Pour intégrer l’ensemble de la ressource disponible fournisseurs, y compris leurs types, emplacements, versions API et statut d’enregistrement, utilisent l’opération [de la liste tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Si vous avez besoin d’enregistrer un fournisseur de ressources, voir [Enregistrer un abonnement avec un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

L’exemple suivant montre comment intégrer l’ensemble des fournisseurs de ressources disponibles.

    Get-AzureRmResourceProvider -ListAvailable
    

L’exemple suivant montre comment obtenir les types de ressources pour un fournisseur de ressources particulier.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
Le résultat est semblable à :

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Pour enregistrer un fournisseur de ressources, fournissent l’espace de noms :

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Azure infrastructure du langage commun

L’exemple suivant montre comment intégrer l’ensemble des fournisseurs de ressources disponibles.

    azure provider list
    
Le résultat est semblable à :

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Vous pouvez enregistrer les informations relatives à un fournisseur de ressources particulier dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Pour enregistrer un fournisseur de ressources, fournissent l’espace de noms :

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Régions pris en charge

Lorsque vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Gestionnaire de ressources est pris en charge dans toutes les régions, mais les ressources que vous déployez ne peuvent pas pris en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations de votre abonnement que vous empêchent d’utiliser certaines régions qui prennent en charge de la ressource. Ces limitations peuvent être liées à la taxe des problèmes pour votre pays ou le résultat d’une stratégie de placé par l’administrateur de votre abonnement à utiliser uniquement certaines régions. 

Pour obtenir une liste complète de toutes les régions pris en charge pour tous les services Azure, consultez [Services par région](https://azure.microsoft.com/regions/#services); Toutefois, cette liste peut inclure les régions que votre abonnement ne prend pas en charge. Vous pouvez déterminer les régions pour un type particulier qui prend en charge de votre abonnement via le portail, API REST, PowerShell ou Azure infrastructure du langage commun.

### <a name="portal"></a>Portail

Vous pouvez voir les régions pris en charge pour un type de ressource les étapes suivantes :

1. Sélectionnez **plusieurs services de** > **Explorateur de ressources**.

    ![Explorateur de ressources](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Ouvrez le nœud **fournisseurs** .

    ![sélectionner des fournisseurs](./media/resource-manager-supported-services/select-providers.png)

3. Sélectionner un fournisseur de ressources et d’afficher les régions pris en charge et les versions de l’API.

    ![fournisseur d’affichage](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST

Pour découvrir les régions sont disponibles pour un type particulier dans votre abonnement, utilisez l’opération [de la liste tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

L’exemple suivant montre comment obtenir les régions pris en charge pour les sites web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Le résultat est semblable à :

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>Azure infrastructure du langage commun

L’exemple suivant retourne tous les emplacements pris en charge pour chaque type de ressource.

    azure location list

Vous pouvez également filtrer les résultats emplacement avec un utilitaire JSON comme [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Qui retourne :

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versions de l’API prises en charge

Lorsque vous déployez un modèle, vous devez spécifier une version de l’API à utiliser pour la création de chaque ressource. La version de l’API correspond à une version d’opérations API REST publiés par le fournisseur de ressources. Comme un fournisseur de ressources permet de nouvelles fonctionnalités, il publie une nouvelle version de l’API REST. Par conséquent, la version de l’API que vous spécifiez dans votre modèle affecte quelles sont les propriétés que vous pouvez spécifier dans le modèle. En règle générale, vous souhaitez sélectionner la dernière version de l’API lors de la création de modèles. Modèles existants, vous pouvez décider si vous voulez continuer à utiliser une version antérieure de l’API ou mettre à jour votre modèle pour la dernière version tirer parti des nouvelles fonctionnalités.

### <a name="portal"></a>Portail

Déterminer les versions de l’API pris en charge de la même façon que vous déterminez prise en charge des régions (illustrées précédemment).

### <a name="rest-api"></a>API REST

Pour découvrir les versions de l’API sont disponibles pour les types de ressources, utilisez l’opération [de la liste tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

L’exemple suivant montre comment obtenir les versions de l’API disponibles pour un type particulier.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
Le résultat est semblable à :
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>Azure infrastructure du langage commun

Vous pouvez enregistrer les informations (y compris les versions de l’API disponibles) pour un fournisseur de ressources dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Vous pouvez ouvrir le fichier et recherchez l’élément **apiVersions**

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles de gestionnaire de ressources, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md).
- Pour savoir comment déployer des ressources, voir [déployer une application avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).
