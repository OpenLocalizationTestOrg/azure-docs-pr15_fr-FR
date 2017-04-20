<properties
    pageTitle="Protéger votre API avec la gestion des API Azure | Microsoft Azure"
    description="Découvrez comment protéger votre API avec les quotas et la limitation de stratégies (limitation de vitesse)."
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

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Protéger votre API avec des limites de taux à l’aide de gestion de l’API Azure

Ce guide vous montre comme il est facile pour ajouter une protection pour la principale API en configurant des stratégies de limite et quota taux avec la gestion des API Azure.

Dans ce didacticiel, vous allez créer un produit « Version d’évaluation gratuite « API qui permet aux développeurs émettre des appels jusqu'à 10 par minute et avec un maximum de 200 appels par semaine à votre API à l’aide du [taux d’appel limite par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et stratégies de [quota d’utilisation ensemble par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) . Puis vous publier l’API et tester la stratégie de limite de taux.

Pour les scénarios limitation plus avancées en utilisant les stratégies de [taux limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [par clé de quota](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , voir [limitation avec la gestion des API Azure de requêtes avancées](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Pour créer un produit

Dans cette étape, vous allez créer un produit de la version d’évaluation gratuite qui ne nécessite pas d’approbation de l’abonnement.

>[AZURE.NOTE] Si vous avez déjà disposez d’un produit configuré et que vous souhaitez utiliser pour ce didacticiel, vous pouvez passer directement à [configurer appel taux stratégies de limite et quota][] et suivre le didacticiel à partir de là à l’aide de votre produit à la place du produit de la version d’évaluation gratuite.

Pour commencer, cliquez sur **Gérer** dans Azure classique pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [Gérer votre première API gestion des API Azure][] .

Cliquez sur **Products** dans le menu de **Gestion de l’API** sur la gauche pour afficher la page de **produits** .

![Ajouter un produit][api-management-add-product]

Cliquez sur **Ajouter produit** pour afficher la boîte de dialogue **Ajouter un nouveau produit** .

![Ajouter un nouveau produit][api-management-new-product-window]

Dans la zone **titre** , tapez la **Version d’évaluation gratuite**.

Dans la zone **Description** , tapez le texte suivant :  **abonnés seront en mesure d’exécuter 10 minutes appels avec un maximum de 200 appels/semaine après laquelle accès refusé.**

Produits de gestion de l’API peuvent être protégés ou ouvrent. Produits protégées doivent être abonnés à avant de pouvoir être utilisés. Produits ouverts peuvent être utilisés sans un abonnement. Vérifiez que **l’abonnement** est sélectionnée pour créer un produit protégé qui nécessite un abonnement. Il s’agit du paramètre par défaut.

Si vous voulez qu’un administrateur pour réviser et accepter ou refuser des tentatives d’abonnement à ce produit, sélectionnez **Exiger l’approbation de l’abonnement**. Si la case à cocher n’est pas sélectionnée, tentatives d’abonnement seront acceptés automatiquement. Dans cet exemple, les abonnements sont automatiquement approuvées, n’activez ne pas la case.

Pour autoriser les comptes développeur pour vous abonner plusieurs fois pour le nouveau produit, activez la case à cocher **Autoriser plusieurs abonnements sonneries** . Ce didacticiel ne pas utiliser plusieurs abonnements sonneries, donc laissez-la désactivée.

Une fois toutes les valeurs sont entrées, cliquez sur **Enregistrer** pour créer le produit.

![Produit ajouté][api-management-product-added]

Par défaut, les nouveaux produits sont visibles par les utilisateurs dans le groupe **administrateurs** . Nous allons ajouter le groupe **développeurs** . Cliquez sur la **Version d’évaluation gratuite**, puis cliquez sur l’onglet **visibilité** .

>Gestion des API, groupes sont utilisées pour gérer la visibilité des produits pour les développeurs. Produits accorder visibilité aux groupes et les développeurs peuvent afficher et s’abonner aux produits qui sont visibles par les groupes dans lequel elles appartiennent. Pour plus d’informations, voir [comment créer et utiliser des groupes de gestion des API Azure][].

![Ajouter un groupe de développeurs][api-management-add-developers-group]

Activez la case à cocher **les développeurs** , puis cliquez sur **Enregistrer**.

## <a name="add-api"> </a>Pour ajouter une API sur le produit

Dans cette étape de ce didacticiel, nous allons ajouter l’API écho vers le nouveau produit de version d’évaluation gratuite.

>Chaque instance de service de gestion de l’API est préconfigurée avec une API écho pouvant être utilisées pour expérimenter et en savoir plus sur la gestion des API. Pour plus d’informations, voir [Gérer votre première API gestion des API Azure][].

Cliquez sur **produits** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur la **Version d’évaluation gratuite** pour configurer le produit.

![Configurer produit][api-management-configure-product]

Cliquez sur **Ajouter des API au produit**.

![Ajouter des API au produit][api-management-add-api]

Sélectionnez **L’écho API**, puis cliquez sur **Enregistrer**.

![Ajouter l’écho API][api-management-add-echo-api]

## <a name="policies"> </a>Pour configurer des stratégies de limite et le quota de taux appel

Limites de taux et les quotas sont configurés dans l’éditeur de stratégie. Cliquez sur **stratégies** sous le menu de **Gestion de l’API** sur la gauche. Dans la liste des **produits** , cliquez sur **Version d’évaluation gratuite**.

![Stratégie de produit][api-management-product-policy]

Cliquez sur **Ajouter une stratégie** pour importer le modèle de stratégie et commencez à créer le taux de stratégies de limite et quota.

![Ajouter une stratégie][api-management-add-policy]

Pour insérer des stratégies, placez le curseur dans la section soit **entrants** ou **sortants** du modèle de stratégie. Stratégies de limite et le quota de taux sont des stratégies entrants, donc positionnez le curseur dans l’élément entrant.

![Éditeur de stratégie][api-management-policy-editor-inbound]

Les deux stratégies que nous ajoutons dans ce didacticiel sont les stratégies de [taux d’appel limite par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et [définir le quota d’utilisation par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Instructions de stratégie][api-management-limit-policies]

Une fois que le curseur est placé dans l’élément de stratégie **entrant** , cliquez sur la flèche en regard de **taux d’appel limite par abonnement** pour insérer son modèle de stratégie.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Taux d’appels limite par abonnement** peut être utilisé au niveau du produit et peut également être utilisées par les API et les niveaux de nom opération individuelle. Dans ce didacticiel, seules les stratégies au niveau du produit sont utilisées, par conséquent supprimer les éléments de **l’api** et **opération** depuis l’élément de la **limite de taux** , afin que seule la reste élément extérieure **limite de taux** , comme le montre l’exemple suivant.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Dans le produit de la version d’évaluation gratuite, le taux d’appel autorisée maximale est de 10 appels par minute, tapez **10** comme la valeur pour l’attribut **appels** et **60** pour l’attribut **période de renouvellement** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Pour configurer la stratégie **définir le quota d’utilisation par abonnement** , placez votre curseur directement en dessous de l’élément récemment ajouté **à la limite de taux** au sein de l’élément **entrant** , puis cliquez sur la flèche située à gauche de **définir le quota d’utilisation par abonnement**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Étant donné que cette stratégie est également destinée à être au niveau du produit, supprimez les éléments de nom **api** et **opération** , comme illustré dans l’exemple suivant.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Les quotas peuvent être en fonction du nombre d’appels par intervalle, la bande passante ou les deux. Dans ce didacticiel, nous allons sans limitation en fonction de la bande passante, par conséquent supprimer l’attribut de **bande passante** .

    <quota calls="number" renewal-period="seconds">
    </quota>

Dans le produit de la version d’évaluation gratuite, le quota est 200 appels par semaine. Spécifier **200** comme valeur pour l’attribut **appels** et de configurer **604800** comme valeur pour l’attribut **période de renouvellement** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalles de stratégie sont spécifiées en secondes. Pour calculer l’intervalle pour une semaine, multipliez le nombre de jours (7) par le nombre d’heures par jour (24) par le nombre de minutes dans une heure (60) par le nombre de secondes dans une minute (60) : 7 *24* 60 * 60 = 604800.

Lorsque vous avez terminé la configuration de la stratégie, elle doit correspondre à l’exemple suivant.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Après avoir configuré les stratégies souhaitées, cliquez sur **Enregistrer**.

![Enregistrer la stratégie][api-management-policy-save]

## <a name="publish-product"></a> Pour publier le produit

À présent que l’API sont ajoutés et les stratégies sont configurées, le produit doit être publié afin qu’il peut être utilisé par les développeurs. Cliquez sur **produits** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur la **Version d’évaluation gratuite** pour configurer le produit.

![Configurer produit][api-management-configure-product]

Cliquez sur **Publier**, puis cliquez sur **Oui, publiez-le** pour confirmer.

![Publier le produit][api-management-publish-product]

## <a name="subscribe-account"> </a>Pour vous abonner à un compte de développeur sur le produit

Maintenant que le produit est publié, il est disponible pour être abonné à et utilisée par les développeurs.

>Les administrateurs d’une instance de gestion de l’API sont automatiquement abonnés à tous les produits. Dans ce didacticiel étape, nous vous inscrire un des comptes pour les développeurs non administrateur sur le produit de la version d’évaluation gratuite. Si votre compte développeur fait partie du rôle administrateur, puis vous pouvez suivre en même temps que cette étape, même si vous êtes déjà abonné.

Cliquez sur **utilisateurs** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur le nom de votre compte développeur. Dans cet exemple, nous utilisons le compte développeur **Clayton Gragg** .

![Configurer pour les développeurs][api-management-configure-developer]

Cliquez sur **Ajouter un abonnement**.

![Ajout d’abonnement][api-management-add-subscription-menu]

Sélectionnez la **Version d’évaluation gratuite**, puis cliquez sur **s’abonner**.

![Ajout d’abonnement][api-management-add-subscription]

>[AZURE.NOTE] Dans ce didacticiel, plusieurs abonnements simultanées ne sont pas activés pour le produit de la version d’évaluation gratuite. S’il s’agissait, vous le feriez invité nommer l’abonnement, comme le montre l’exemple suivant.

![Ajout d’abonnement][api-management-add-subscription-multiple]

Après avoir cliqué sur **s’abonner**, le produit s’affiche dans la liste des **abonnements** pour l’utilisateur.

![Abonnement ajouté][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Pour appeler une opération et tester la limite de taux

Maintenant que le produit de la version d’évaluation gratuite est configuré et publié, nous puissions appeler certaines opérations et tester la stratégie de limite de taux.
Basculer vers le portail de développement en cliquant sur **portail pour les développeurs** dans le menu supérieur droit.

![Portail pour les développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu du haut, puis cliquez sur **L’API l’écho**.

![Portail pour les développeurs][api-management-developer-portal-api-menu]

Cliquez sur **Obtenir une ressource**, puis cliquez sur **essayer**.

![Ouvrir la console][api-management-open-console]

Conserver les valeurs de paramètre par défaut, puis sélectionnez votre clé d’abonnement pour le produit de la version d’évaluation gratuite.

![Clé abonnement][api-management-select-key]

>[AZURE.NOTE] Si vous avez plusieurs abonnements, veillez à sélectionner la clé pour la **Version d’évaluation gratuite**, sinon les stratégies qui ont été configurés dans les étapes précédentes ne seront pas en vigueur.

Cliquez sur **Envoyer**, puis affichez la réponse. Notez l' **état de la réponse** de **200 OK**.

![Résultats de l’opération][api-management-http-get-results]

Cliquez sur **Envoyer** à un rythme supérieur à la politique de limite de taux de 10 appels par minute. Une fois la stratégie de limite de taux est dépassée, un état de la réponse de **429 trop grand nombre de demandes** est renvoyé.

![Résultats de l’opération][api-management-http-get-429]

Le **contenu de la réponse** indique le temps restant avant de tentatives sera réussies.

Lorsque la stratégie de limite de taux de 10 appels par minute est activée, les appels ultérieurs échouera jusqu'à 60 secondes se sont écoulées à partir de la première des 10 appels réussies sur le produit avant de la limite de taux a été dépassée. Dans cet exemple, l’intervalle restant est 54 secondes.

## <a name="next-steps"> </a>Étapes suivantes

-   Regardez une démonstration de la définition des limites de taux et les quotas de la vidéo suivante.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Gérer votre première API gestion des API Azure]: api-management-get-started.md
[Comment créer et utiliser des groupes de gestion des API Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurer des stratégies de limite et le quota de taux appel]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
