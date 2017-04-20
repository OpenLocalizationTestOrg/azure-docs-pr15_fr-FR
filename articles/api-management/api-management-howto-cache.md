<properties
    pageTitle="Ajouter les mises en cache pour améliorer les performances de gestion des API Azure | Microsoft Azure"
    description="Découvrez comment faire pour améliorer la latence, la consommation de bande passante et la charge de service web pour les appels de service de gestion de l’API."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Ajouter les mises en cache pour améliorer les performances de gestion des API Azure

Gestion des API peuvent être configurées pour la mise en cache de réponse. La mise en cache de réponse peut considérablement réduire la latence API, consommation de bande passante et web charge de maintenance des données qui ne changent pas souvent.

Ce guide vous montre comment ajouter réponse mise en cache pour votre API et configurer des stratégies pour les opérations de l’API écho l’exemple. Vous pouvez ensuite appeler l’opération à partir du portail développeur pour vérifier la mise en cache en action.

>[AZURE.NOTE] Pour plus d’informations sur des éléments de mise en cache par clé à l’aide des expressions de stratégie, voir [Personnaliser la mise en cache gestion des API Azure](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de suivre les étapes décrites dans ce guide, vous devez avoir une instance de service de gestion des API avec une API et configuré un produit. Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

## <a name="configure-caching"> </a>Configurer une opération de mise en cache

Dans cette étape, vous allez examiner les paramètres de mise en cache de l’opération **d’Obtenir des ressources (mis en cache)** de l’échantillon API l’écho.

>[AZURE.NOTE] Chaque instance de service de gestion de l’API est préconfigurée avec une API écho pouvant être utilisées pour expérimenter et en savoir plus sur la gestion des API. Pour plus d’informations, voir [prise en main avec la gestion des API Azure][].

Pour commencer, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail de Publisher][api-management-management-console]

Cliquez sur **API** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **L’API l’écho**.

![API écho][api-management-echo-api]

Cliquez sur l’onglet **opérations** , puis cliquez sur l’opération **d’Obtenir des ressources (mis en cache)** dans la liste **opérations** .

![Opérations API écho][api-management-echo-api-operations]

Cliquez sur l’onglet **mise en cache** pour afficher les paramètres de mise en cache pour cette opération.

![Onglet mise en cache][api-management-caching-tab]

Pour activer la mise en cache pour une opération, activez la case à cocher **Activer** . Dans cet exemple, la mise en cache est activé.

Chaque réponse opération constitue la clé, selon les valeurs dans les champs **variation par paramètres de chaîne de requête** et **variation par en-têtes** . Si vous souhaitez mettre en cache plusieurs réponses en fonction de paramètres de chaîne de requête ou les en-têtes, vous pouvez les configurer dans ces deux champs.

**Durée** Spécifie l’intervalle d’expiration des réponses mises en cache. Dans cet exemple, l’intervalle est **3600** secondes, qui équivaut à une heure.

À l’aide de la configuration de la mise en cache dans cet exemple, la première requête à l’opération **d’Obtenir des ressources (mis en cache)** renvoie une réponse à partir du service principal. Cette réponse sera cachée, indexées par les en-têtes spécifiées et les paramètres de chaîne de requête. Les appels ultérieurs à l’opération, avec paramètres correspondants, ont la réponse mise en cache renvoyée, jusqu'à ce que l’intervalle de durée de cache a expiré.

## <a name="caching-policies"> </a>Passez en revue les stratégies de mise en cache

Dans cette étape, vous passez en revue les paramètres de mise en cache pour l’opération **d’Obtenir des ressources (mis en cache)** de l’échantillon API l’écho.

Lorsque les paramètres de mise en cache sont configurés pour une opération sur l’onglet **mise en cache** , mise en cache stratégies sont ajoutées pour l’opération. Ces politiques peuvent être affichés et modifiées dans l’éditeur de stratégie.

Cliquez sur **stratégies** dans le menu de **Gestion de l’API** sur la gauche, puis **API écho / d’obtenir la ressource (mis en cache)** dans la liste déroulante **opération** .

![Opération de portée de stratégie][api-management-operation-dropdown]

Cela permet d’afficher les stratégies pour cette opération dans l’éditeur de stratégie.

![Éditeur de stratégie de gestion des API][api-management-policy-editor]

La définition de stratégie pour cette opération inclut les stratégies qui définissent la configuration de la mise en cache qui ont été révisés à l’aide de l’onglet **mise en cache** dans l’étape précédente.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Modifications apportées aux stratégies de mise en cache dans l’éditeur de stratégie seront reflétées dans l’onglet **mise en cache** d’une opération et vice versa.

## <a name="test-operation"> </a>Une opération d’appel et tester la mise en cache

Pour afficher la mise en cache en action, nous pouvons appeler l’opération à partir du portail développeur. Dans le menu supérieur droit, cliquez sur **portail pour les développeurs** .

![Portail pour les développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur et sélectionnez **API écho**.

![API écho][api-management-apis-echo-api]

>Si vous n'avez qu’une seule API configurée ou visible à votre compte, puis en cliquant sur API vous permet d’accéder directement aux opérations de cette API.

Sélectionnez l’opération **Obtenir des ressources (mis en cache)** , puis cliquez sur **Ouvrir la Console**.

![Ouvrir la console][api-management-open-console]

La console vous permet les opérations directement à partir du portail de développement d’appel.

![Console][api-management-console]

Conserver les valeurs par défaut des **param1** **param2**.

Sélectionnez la clé souhaitée dans la liste déroulante **clé de l’abonnement** . Si votre compte est un abonnement, il sera déjà sélectionné.

Entrez **sampleheader:value1** dans la zone de texte **en-têtes de requête** .

Cliquez sur **HTTP Get** et prenez note des en-têtes de réponse.

Entrez **sampleheader:value2** dans la zone de texte **en-têtes de requête** , puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** est toujours **valeur1** dans la réponse. Essayez certaines des valeurs différentes et notez que la réponse de l’appel première mis en cache est renvoyée.

Entrez **25** dans le champ **param2** , puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** dans la réponse est désormais **valeur2**. Étant donné que les résultats de l’opération sont indexés par la chaîne de requête, la réponse précédente mis en cache n’est pas retournée.

## <a name="next-steps"> </a>Étapes suivantes

-   Pour plus d’informations sur les stratégies de mise en cache, voir [règles de mise en cache][] dans la [référence de stratégie de gestion des API][].
-   Pour plus d’informations sur des éléments de mise en cache par clé à l’aide des expressions de stratégie, voir [Personnaliser la mise en cache gestion des API Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Prise en main avec la gestion des API Azure]: api-management-get-started.md

[Référence de stratégie de gestion des API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Stratégies de mise en cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
