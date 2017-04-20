<properties
    pageTitle="Limitation avec la gestion des API Azure de requêtes avancées"
    description="Découvrez comment créer et appliquer des quotas flexibles et taux limitation des stratégies de gestion des API Azure."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitation avec la gestion des API Azure de requêtes avancées

Possibilité limiter les demandes entrantes est un rôle clé de la gestion de l’API Azure. Soit en contrôlant le taux de requêtes ou les demandes de total/données transférées, gestion des API permet aux fournisseurs API protéger leurs API d’abuse et obtenir la valeur de différents niveaux de produit API.

## <a name="product-based-throttling"></a>Produit en fonction de la limitation
À ce jour, la limitation de taux fonctionnalités aient été limitées en cours portée à un abonnement produit particulier (une clé pour l’essentiel), définis dans le portail de gestion de l’API de publisher. Il est utile pour le fournisseur de l’API appliquer des limites sur les développeurs inscrit à utiliser leur API, cependant, il ne permet pas, par exemple, dans la limitation aux utilisateurs finaux individuels de l’API. Il est possible que pour un utilisateur de l’application du développeur de consommer le quota entière et puis empêcher d’autres clients du développeur d’être en mesure d’utiliser l’application. En outre, plusieurs clients qui peuvent générer un volume élevé de demandes peuvent limiter l’accès aux utilisateurs occasionnels.

## <a name="custom-key-based-throttling"></a>La limitation basé sur des clés personnalisé
Les nouvelles stratégies [taux limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [par clé de quota](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) offrent une solution beaucoup plus flexible pour contrôler le trafic. Ces nouvelles stratégies vous permettent de définir des expressions pour identifier les clés qui seront utilisés pour effectuer le suivi de l’utilisation du trafic. Le fonctionnement est illustré plus simples avec un exemple. 

## <a name="ip-address-throttling"></a>Adresse IP la limitation
Les stratégies suivantes restreignent une adresse IP du client unique à seulement 10 appels chaque minute, avec un total de 1 000 000 appels et 10 000 kilo-octets de bande passante par mois. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Si tous les clients sur Internet utilisé une adresse IP unique, il peut s’agir d’un moyen efficace de limiter l’utilisation par l’utilisateur. Toutefois, il est probable que plusieurs utilisateurs seront partage une seule adresse IP publique en raison de leur accès à Internet via un périphérique NAT. Malgré cela, pour les API qui autorisent l’accès non authentifiés la `IpAddress` peuvent être la meilleure option.

## <a name="user-identity-throttling"></a>La limitation d’identité utilisateur
Si un utilisateur final est authentifié, puis une clé de limitation peut être générée en fonction des informations qui identifie un utilisateur.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

Dans cet exemple nous extraire l’en-tête d’autorisation, convertissez-le au `JWT` objet et l’objet du jeton permet d’identifier l’utilisateur et utiliser celle-ci comme le taux de limitation de clé. Si l’identité de l’utilisateur est stockée dans le `JWT` comme un des autres réclamations puis que valeur peut être utilisée à la place.

## <a name="combined-policies"></a>Stratégies combinées
Bien que les nouvelles stratégies limitation offrent davantage de contrôle que les stratégies de limitation existantes, il est toujours valeur qui combine les deux fonctions. Limitation par clé d’abonnement produit ([taux d’appel limite par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et [définir le quota d’utilisation par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) est un excellent moyen pour activer monétisation par le biais d’en fonction des niveaux de l’utilisation d’une API. Dessinez le contrôle à grains fins de pouvoir limiter par l’utilisateur est complémentaire et empêche comportement d’un utilisateur de ralentir l’expérience d’un autre. 

## <a name="client-driven-throttling"></a>Piloté par la limitation de client
Lorsque la limitation de la clé est définie à l’aide d’une [expression de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx), il est le fournisseur de l’API qui consiste à choisir comment la limitation est dans l’étendue. Toutefois, un développeur peut souhaitez contrôler comment ils limite de taux leurs propres clients. Cela peut être activé par le fournisseur de l’API en introduisant un en-tête personnalisé pour permettre à l’application du développeur client communiquer la clé à l’API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Cela permet l’application du développeur client choisir comment ils souhaitent créer le taux de limitation de clé. Avec un peu d’ingenuity un développeur client pouviez créer leurs propres niveaux de taux en allocation des jeux de clés aux utilisateurs et faire pivoter l’utilisation des clés.

## <a name="summary"></a>Résumé
Gestion des API Azure fournit des taux et devis la limitation pour les protéger et ajouter la valeur à votre service API. La nouvelle limitation avec les règles de portée personnalisés stratégies vous dessinez à grains fins contrôle sur ces politiques pour activer vos clients à créer des applications indépendante. Les exemples de cet article illustrent l’utilisation de ces nouvelles stratégies par taux de fabrication limitation des touches avec les adresses IP client, identité de l’utilisateur et les valeurs de client générée. Toutefois, il existe de nombreuses autres parties du message qui peuvent être utilisées comme agent utilisateur, fragments de chemin d’URL, la taille des messages.

## <a name="next-steps"></a>Étapes suivantes
Veuillez nous envoyer vos commentaires dans le thread Disqus pour cette rubrique. Il serait pratique entendre sur les autres valeurs clés potentiels qui ont été un choix logique dans vos scénarios.

## <a name="watch-a-video-overview-of-these-policies"></a>Regarder une vidéo de présentation de ces politiques
Pour plus d’informations sur les stratégies de [taux limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [par clé de quota](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) traitées dans cet article, veuillez regardez la vidéo suivante.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
