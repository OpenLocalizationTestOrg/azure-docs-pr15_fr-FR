<properties
    pageTitle="Créer un service de recherche Azure à l’aide du portail Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="Découvrez comment configurer un service de recherche Azure à l’aide du portail Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Créer un service de recherche Azure à l’aide du portail Azure

Ce guide vous guidera dans le processus de création (ou mise à disponibilité) un service de recherche Azure à l’aide du [Portail Azure](https://portal.azure.com/).

Ce guide suppose que vous avez souscrit un abonnement Azure déjà et pouvez se connecter au portail Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Dans le portail Azure rechercher recherche Azure
1. Accédez au [Portail Azure](https://portal.azure.com/) et se connecter.
1. Cliquez sur le signe plus (« + ») dans le coin supérieur gauche.
2. Sélectionnez les **données + stockage**.
3. Sélectionnez **Rechercher Azure**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Sélectionnez un nom de service et le point de terminaison URL de votre service
1. Nom de votre service sera partie de l’URL de point de terminaison de votre service de recherche Azure par rapport à laquelle vous effectuerez vos appels d’API pour gérer et utiliser le service de recherche.
2. Tapez votre nom de service dans le champ **URL** . Le nom du service :
  * doit contenir uniquement des lettres minuscules, des chiffres ou des tirets («- »)
  * Impossible d’utiliser un tiret («- ») en tant que les 2 premiers caractères ou le dernier caractère unique
  * ne peut pas contenir de tirets consécutifs («-- »)
  * est compris entre 2 et 60 caractères


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Sélectionnez un abonnement à l’endroit où vous permet de solidariser votre service
Si vous avez plusieurs abonnements, vous pouvez sélectionner celui inclura ce service de recherche Azure.

## <a name="select-a-resource-group-for-your-service"></a>Sélectionnez un groupe de ressources de votre service
Créer un nouveau groupe de ressources ou sélectionnez-en un. Un groupe de ressources est un ensemble de services Azure et ressources qui sont utilisés ensemble. Par exemple, si vous utilisez recherche Azure à indexer une base de données SQL, ces deux services doivent faire partie du même groupe de ressources.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Sélectionnez l’emplacement où votre service est hébergé
Comme un service Azure, Azure recherche est disponible pour être hébergés dans des centres de données dans le monde entier. Veuillez noter que [prix peuvent différer](https://azure.microsoft.com/pricing/details/search/) en géographie.

## <a name="select-your-pricing-tier"></a>Sélectionnez votre niveau de tarification
[Recherche Azure n’est actuellement proposé dans plusieurs niveaux de prix](https://azure.microsoft.com/pricing/details/search/): gratuit, base ou Standard. Chaque niveau possède son propre [capacité et les limites](search-limits-quotas-capacity.md). Pour obtenir des instructions, voir [Choisir un tarification ou référence (SKU)](search-sku-tier.md) .

Dans ce cas, nous avons choisi la couche Standard de nos services.

## <a name="select-the-create-button-to-provision-your-service"></a>Sélectionnez le bouton « Créer » pour configurer votre service

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Mettre à l’échelle votre service

Une fois que votre service est créé, vous pouvez optimiser pour l’adapter à vos besoins. Si vous avez choisi la couche Standard pour votre service de recherche Azure, vous pouvez mettre à l’échelle votre service à deux dimensions : doubles et partitions. Si vous avez choisi le niveau de base, vous pouvez uniquement ajouter réplicas.

*__Partitions__* permettre à votre service stocker et rechercher dans les documents plus.

*__Réplicas__* autoriser votre service peut gérer une charge plus élevée de requêtes de recherche - [un service requiert 2 réplicas pour obtenir un SLA en lecture seule et requiert 3 réplicas pour obtenir un SLA en lecture/écriture](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Accédez à la carte de gestion de votre service de recherche Azure dans le portail Azure.
2. Dans la carte de **paramètres** , sélectionnez **échelle**.
3. Vous pouvez ajuster votre service en ajoutant des réplicas ou Partitions.
  * Vous ne pouvez pas mettre à l’échelle votre service au-delà de recherche 36 unités. Le nombre total d’unités de recherche est le produit de vos réplicas et partitions (réplicas * Partitions = nombre Total d’unités recherche).
  * Si vous avez choisi le niveau de base, vous pouvez uniquement mettre à l’échelle à 3 réplicas. Services de base sont liés à une seule partition.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Suivant
Mise en service d’un service de recherche Azure, vous serez prêt à [définir un index de recherche Azure](search-what-is-an-index.md) afin de pouvoir télécharger et effectuer une recherche dans vos données.

Pour obtenir un didacticiel rapide, voir [prise en main Azure recherche dans le portail](search-get-started-portal.md) .
