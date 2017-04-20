<properties
   pageTitle="Familiarisez-vous dans votre consommation de ressources de Microsoft Azure | Microsoft Azure"
   description="Fournit une vue d’ensemble conceptuelle de la Azure facturation de l’utilisation et RateCard APIs, qui sont utilisés pour fournir des informations sur la consommation de ressources Azure et les tendances."
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

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Familiarisez-vous dans votre consommation de ressources de Microsoft Azure

Clients et partenaires doivent pouvoir précisément prédire et d’afficher les coûts leur Azure.  Lorsqu’ils utilisent un dépenses d’un modèle d’exploitation, ils doivent également la possibilité d’effectuer showback et refacturation analyse, ainsi que de fournir fidélité de mode dans estimation et facturation, en particulier pour les déploiements dans le cloud volumineux.

L’utilisation des ressources Azure et API de carte de taux mentionnés dans cette adresse article ces besoins, en activant les nouvelles perspectives dans votre consommation de ressources Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Présentation de l’API de RateCard et utilisation des ressources Azure

L’utilisation des ressources Azure et RateCard APIs sont implémentées comme un fournisseur de ressources, dans le cadre de la famille des API exposé par le Gestionnaire de ressources Azure.  

### <a name="azure-resource-usage-api-preview"></a>Utilisation des ressources Azure API (Preview)
Clients et partenaires peuvent utiliser l’API de l’utilisation de ressources Azure pour obtenir les données de consommation Azure estimée. Les fonctionnalités incluent :

- **Contrôle d’accès basé sur un rôle azure-** - clients et partenaires peuvent configurer leurs stratégies d’accès dans le [portail Azure](https://portal.azure.com) ou via [applets de commande PowerShell Azure](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications peuvent accéder aux données d’utilisation de l’abonnement. Les appelants doivent utiliser standards jetons Azure Active Directory pour l’authentification. L’appelant doit également être ajouté au rôle le lecteur, propriétaire ou collaborateur pour accéder aux données d’utilisation pour un abonnement Azure spécifique.

- **Toutes les heures ou agrégations quotidien** - appelants peuvent spécifier s’ils souhaitent leurs données d’utilisation Azure dans les plages horaires ou plages quotidiennes. La valeur par défaut est quotidiennes.

- **Instance métadonnées fournies (y compris les balises de ressources)** – l’occurrence de détails tels que l’uri de ressource complet (/subscriptions/ {-id de l’abonnement} / …), ainsi que la ressource balises d’informations et des ressources de groupe seront fournies dans la réponse. Vous aider les clients de façon déterministe et allouer par programme l’utilisation par les balises, pour des scénarios d’utilisation comme planification croisée.

- **Métadonnées de ressources fournies** - détails de la ressource tels que le nom de compteur compteur catégorie, sous-catégorie compteur, unité et région recevra également dans la réponse, pour donner les appelants mieux comprendre de ce qui a été utilisée. Nous travaillons également pour aligner la terminologie de métadonnées de ressources sur le portail Azure, utilisation Azure CSV, EA facturation CSV et autres expériences au public, pour permettre aux mise en corrélation de données sur expériences clients.

- **L’utilisation pour tous les types d’offre** – les données d’utilisation seront accessibles pour tous les offrent types notamment paiement, MSDN, engagement monétaire, monétaires de crédit et EA.

### <a name="azure-resource-ratecard-api-preview"></a>Ressource Azure RateCard API (Preview)
Clients et partenaires peuvent utiliser l’API Azure ressource RateCard pour obtenir la liste des ressources Azure disponibles, avec estimée tarifs informations pour chaque. Les fonctionnalités incluent :

- **Contrôle d’accès basé sur un rôle azure-** - clients et partenaires peuvent configurer leurs stratégies d’accès dans le [portail Azure](https://portal.azure.com) ou via [applets de commande PowerShell Azure](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications peuvent accéder aux données RateCard. Les appelants doivent utiliser standards jetons Azure Active Directory pour l’authentification. L’appelant doit également être ajouté au rôle le lecteur, propriétaire ou collaborateur pour accéder aux données d’utilisation pour un abonnement Azure spécifique.

- **Prise en charge de paiement MSDN, monétaire et votre engagement monétaires de crédit offre (EA ne pas pris en charge)** - cette API fournit des informations de taux au niveau de l’offre d’Azure, par rapport au niveau de l’abonnement.  L’appelant de cette API doit passer les informations de l’offre pour obtenir les taux et les détails de la ressource.  Offres EA ont personnalisés taux par d’inscription, nous ne pouvons pas à fournir les taux EA pour le moment.

## <a name="scenarios"></a>Scénarios

Voici quelques-unes des scénarios qui sont rendus possibles grâce à la combinaison de l’utilisation et les RateCard APIs :

- **Azure consacrer au cours du mois** , les clients peuvent utiliser l’utilisation et RateCard APIs en combinaison pour obtenir une meilleure vue d’ensemble de leur cloud consacrer au cours du mois, en analysant les plages horaires et quotidiennes des estimations de l’utilisation et les frais.

- **Configurer des alertes** – clients et partenaires peuvent configurer des alertes basée monétaires ou ressources sur leur consommation cloud en obtenant la consommation estimée et l’estimation de frais à l’aide de l’utilisation et l’API RateCard.

- **Facture Predict** – clients et partenaires ont leur consommation estimée et cloud passé et appliquer des algorithmes d’apprentissage machine pour prévoir quel est leur facture à la fin du cycle de facturation.

- **Analyse des coûts de consommation préliminaire** – can clients également utiliser l’API RateCard pour prévoir la quantité leur facture serait si elles ont été pour atteindre leurs charges de travail Azure, à condition que vous le souhaitez les nombres de l’utilisation. Si clients doivent charges de travail existants dans d’autres nuages ou nuages privés, ils peuvent également mapper leur utilisation de Azure dépenser taux pour obtenir une meilleure estimation de leur Azure estimée. Cela fournit une vue améliorée de ce qui peut être obtenu via la [Calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/), de (par exemple) partenaires facturation fournissent la possibilité de tableau croisé dynamique sur offre et comparer entre offre différents types au-delà de paiement, y compris engagement monétaire et monétaires de crédit. Les API fournissent également la fonctionnalité permettant de des modifications estimation par région, de coûts l’activation du type d’analyse de scénarios nécessaire pour prendre des décisions de déploiement, comme des ressources déploiement dans différents DC dans le monde entier peuvent avoir un impact direct sur le coût total.

- **Analyse what-if** -

    - Clients et partenaires Déterminez si il serait plus économique à exécuter leurs charges de travail dans une autre région ou dans une autre configuration de la ressource Azure. Azure ressources coûts varient en fonction de la région Azure dans lequel ils sont en cours d’exécution, et ainsi, les clients et partenaires obtenir des optimisations de coût.

    - Clients et partenaires peuvent également déterminer si un autre type d’offre Azure offre un meilleur taux sur une ressource Azure.

## <a name="partner-solutions"></a>Solutions des partenaires

[L’utilisation de Microsoft Azure et RateCard API activer Cloudyn à fournir ITFM pour les clients](billing-usage-rate-card-partner-solution-cloudyn.md) décrit l’expérience de l’intégration offert par partenaire Azure facturation API [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  Cet article fournit une couverture détaillée de leurs expériences, y compris une courte vidéo qui montre comment un client Azure peut utiliser Cloudyn et des API facturation Azure analyse des gains à partir de leurs données Azure consommation.

[Cruiser cloud et intégration de l’API facturation Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) décrit comment [du Cloud Cruiser Express pour Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) fonctionne directement à partir du portail WAP, permettant aux clients de gérer facilement les aspects opérationnels et financiers de leur cloud de Microsoft Azure privée ou publique à partir d’une interface utilisateur unique.   

## <a name="next-steps"></a>Étapes suivantes
+ Consultez la [Référence des API REST facturation Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) pour plus d’informations sur les deux API qui font partie de l’ensemble des API fournies par le Gestionnaire de ressources Azure.
+ Si vous voulez lancez-vous dans l’exemple de code, consultez nos exemples de Code de l’API pour le facturation d’Azure Microsoft sur des [Échantillons de Code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Pour en savoir plus
+ Consultez l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure.
+ Pour plus d’informations sur la gamme d’outils de nécessaires pour faciliter la compréhension du cloud dépenser, reportez-vous à l’article Gartner [Marché Guide pour les outils informatiques financières Management (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
