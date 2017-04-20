<properties
   pageTitle="L’utilisation de Microsoft Azure et Cloudyn RateCard API activer pour fournir ITFM pour les clients | Microsoft Azure"
   description="Fournit une perspective unique à partir de Microsoft Azure facturation partenaire Cloudyn, sur leur expérience intégration de l’API de facturation Azure dans leur produit.  Ceci est particulièrement utile pour les clients Azure et Cloudyn qui intéressent à l’aide de/essayez Cloudyn pour les Services Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>L’utilisation de Microsoft Azure et RateCard API activer Cloudyn fournir ITFM pour les clients

Cloudyn, un partenaire de développement Microsoft et leader des fonctionnalités de gestion de cloud, a été choisie pour obtenir un aperçu de l’utilisation des ressources Microsoft Azure et RateCard APIs privé.  L’API de l’utilisation permet d’accéder aux données de consommation Azure estimée pour un abonnement. L’API RateCard fournit des informations de tarification complètes de tous les services Azure, pour les clients non - Enterprise contrat EA. Intégré ensemble, ces API fournissent une base des informations complètes pour l’entrée dans les outils informatiques financier Management (ITFM) tels que ceux fournis par Cloudyn.

## <a name="introduction"></a>Introduction

Dite « multiplication » de données à partir de l’API de l’utilisation des données à partir de l’API RateCard (prix de l’utilisation [unités] [$unit] = utilisation détaillées et le coût) crée des informations les plus granulaires, précises et fiables facturation disponibles pour Azure aujourd'hui.

![Vue d’ensemble ITFM][1]

Utilisation de ces API fournit des informations clés sur les coûts, ce qui permet de Cloudyn pour analyser les comptes clients de façon simple et programmation et effectuer diverses tâches ITFM pour ses clients et l’utilisation des clients.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Intégration de Cloudyn avec l’API de l’utilisation et RateCard
L’API RateCard requiert plusieurs paramètres d’entrée--, tels que les informations de région devise et paramètres régionaux, mais les plus importantes est OfferDurableID, qui spécifie le type d’Azure offrant le client utilise (paiement, plans héritées engagement 6 et 12 mois, MSDN offres, MPN offres, offres promotionnelles et autres personnes). Vous trouverez le OfferDurableID dans [portail de la facturation et l’utilisation d’Azure](https://account.windowsazure.com/Subscriptions), sous le « ID offrent » de l’abonnement donnée.

Lors de l’enregistrement pour les services [Cloudyn pour Azure](https://www.cloudyn.com/microsoft-azure/) , clients peuvent ajouter leur code OfferDurableID, qui permet de Cloudyn extraire les informations de tarification pertinentes via l’API RateCard.  Informations sur les différents types d’offres peuvent être a détecté un la page [Microsoft Azure offrent des détails](https://azure.microsoft.com/support/legal/offer-details/) .

![Vue d’ensemble du moteur de Cloudyn ITFM][2]

Cloudyn utilise l’utilisation et APIs RateCard, en plus de l’API de performances Azure, créer des couches supplémentaires de visualisation, analytique, d’alerte, création de rapports, des coûts et conseils, offrant aux clients Azure un outil fiable entreprise cloud ITFM.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Exemples d’utilisation Cloudyn ITFM activées par RateCard API et l’utilisation de l’intégration
Cas activées par l’utilisation d’utilisation courantes Cloudyn ITFM et RateCard APIs incluent :

+ Permet d' **Analyse des coûts** - cloud coûts rompus vers le bas jusqu'à n’importe quelle dimension identification native (fournisseur, service, compte et région etc.). L’utilisation de Azure et RateCard APIs facilitent ce une tâche aisée, en fournissant la décomposition plus granulaire de l’utilisation et des coûts de données par compte, qui est ensuite regroupé et filtrée par Cloudyn et présenté à l’utilisateur, sous forme graphique ou tabulaire.

![Graphique en secteurs analyse des coûts][3]

+ **Coût Allocation 360** - finance vous permettent de conserver et les responsables informatiques pour révéler d’analyse des coûts réels, des pilotes et des tendances de leur déploiement cloud. Il davantage permet aux responsables de facilement associer dépenses déploiement divisions, départements, régions et plus d’informations, fournir une vue d’ensemble de coûts cloud et de faciliter les showbacks et un débit enterprise. L’utilisation de Azure et RateCard APIs servent d’entrée pour moteur d’allocation du Cloudyn coût, qui complète les API en définissant des méthodes et la logique métier d’utilisation des ressources sans balisés ou untaggable.

![Graphique de répartition 360][4]

+ **Redimensionnement économique** - fournit des recommandations de redimensionnement droite pour machines virtuelles sous-utilisés, réduisant dépenses du client sur les ordinateurs générés trop ou trop volumineux. Il fait en examinant machine virtuelle du processeur et indicateurs de RAM (via l’API de performances), heures de coût (via l’API RateCard) et d’exécution (via l’API de l’utilisation). Cloudyn ensuite des recommandations droite redimensionnement basées sur des ressources du processeur ou RAM sousutilisées (performances) et calcule des économies estimées à multiplier la delta prix (RateCard) entre les ordinateurs virtuels par la temps-une utilisation réelle (utilisation) de la machine sous-utilisés.

![Redimensionnement rentable][5]

+ **Recommandations porter cloud** - fournit des conseils financiers sur cloud porter. Il examine les coûts actuels d’un utilisateur des ressources de cloud qui sont déployés sur les fournisseurs de cloud principales et par rapport au coût d’un déploiement équivalent sur Azure. Il propose puis granulaires, par ressource, financière basée sur porter des recommandations sur Azure. Après avoir évalué le déploiement équivalent requis sur Azure (basé sur les préférences utilisateur et les indicateurs de performance), Cloudyn utilise l’API RateCard pour évaluer le coût du déploiement équivalent sur Azure.

+ **Les rapports de performances** - activées par les performances de Azure API, ces rapports fournissent un tableau de fonctionnalités d’utilisation du processeur et de RAM à recommandations d’optimisation. Voici un exemple de rapport de l’utilisation de l’instance, présentation répartition instance par moyenne de l’UC.

![Rapports de performances][6]

+ **Gestionnaire des catégories** - une fonctionnalité puissante de Cloudyn réunissant ordre aux ressources cloud désorganisées. Il fournit aux utilisateurs la liberté de créer leurs propres catégories uniques (balises) de mesure efficaces et de création de rapports qui est conforme aux pratiques professionnelles. En outre, les utilisateurs peuvent facilement régler et classer marquage n’est pas cohérente (c'est-à-dire fautes de frappe et d’autres différences) et détecter automatiquement les ressources non balisés pour attribution précis sur le coût.

![Gestionnaire des catégories][7]

## <a name="video"></a>Vidéo

Voici une courte vidéo qui montre comment un client Azure peut utiliser Cloudyn pour Azure et des API de facturation Azure, pour obtenir des informations à partir de leurs données Azure consommation.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Étapes suivantes

+ Démarrer un essai gratuit de [Cloudyn pour Azure](https://www.cloudyn.com/microsoft-azure/) pour voir comment vous pouvez obtenir la transparence du coût des rapports personnalisés et analytique pour votre déploiement de cloud Microsoft Azure.
+ Pour une vue d’ensemble des RateCard APIs et utilisation des ressources Azure, consultez [Familiarisez-vous dans votre consommation de ressources de Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Consultez la [Référence des API REST facturation Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) pour plus d’informations sur les deux API qui font partie de l’ensemble des API fournies par le Gestionnaire de ressources Azure.
+ Si vous voulez lancez-vous dans l’exemple de code, consultez nos exemples de Code de l’API pour le facturation d’Azure Microsoft sur des [Échantillons de Code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Pour en savoir plus
+ Pour en savoir plus sur les offres de Microsoft Azure Enterprise accord (EA), visitez le site [Azure Gestion des licences pour les entreprises] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Consultez l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure.
+ Pour plus d’informations sur la gamme d’outils de nécessaires pour faciliter la compréhension du cloud dépenser, reportez-vous à l’article Gartner [Marché Guide pour les outils informatiques financières Management (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
