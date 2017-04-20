<properties 
    pageTitle="Concepts clés de gestion des API" 
    description="Découvrez les API, produits, rôles, groupes et autres concepts clés d’API gestion." 
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
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>Quelle est la gestion des API ?

Gestion des API permet aux organisations de publier des API externes, partenaires et les développeurs internes pour déverrouiller le potentiel de leurs données et services. Les entreprises partout sont cherchant à étendre leurs opérations comme une plateforme numérique, création de nouveaux canaux, recherche de nouveaux clients et conduite engagement plus approfondie avec existants. Gestion des API fournit les compétences de base pour vous assurer que d’un programme API réussi via engagement développeur, perspectives métier, analytique, sécurité et protection.

Regarder la vidéo suivante pour avoir un aperçu de la gestion de l’API Azure et découvrez comment utiliser la gestion des API pour ajouter les nombreuses fonctionnalités à votre API, notamment le contrôle d’accès, taux limitation, surveillance, journalisation des événements et réponse mise en cache, avec un minimum de travail de votre part.

> [AZURE.VIDEO azure-api-management-overview]

Pour utiliser la gestion de l’API, les administrateurs créer API. Chaque API est constituée d’une ou plusieurs opérations, et chaque API peut être ajoutée à un ou plusieurs produits. Pour utiliser une API, les développeurs s’abonner à un produit contenant cette API et puis elles peuvent appeler le fonctionnement de l’API, soumis à des stratégies de l’utilisation qui peuvent être en vigueur.

Cette rubrique fournit une vue d’ensemble des concepts clés de gestion des API.

>[AZURE.NOTE] Pour plus d’informations, voir la [gestion de l’API basée sur le Cloud : maîtriser la puissance d’API](http://j.mp/ms-apim-whitepaper) livre blanc PDF. Ce livre blanc d’introduction sur la gestion des API par CITO Research couvre : 
>
> - Défis et exigences API courantes
>     - API de découplage et à la présentation façades
>     - Rendre les développeurs opérationnel et rapidement opérationnel
>     - Sécurisation de l’accès
>     - Analytique et indicateurs
> - Contrôle et mieux comprendre les une plateforme de gestion de l’API
> - À l’aide de solutions de cloud vs locales
> - Gestion des API Azure

## <a name="apis"> </a>API et opérations

API constituent le fondement d’une instance de service de gestion de l’API. Chaque API représente un ensemble d’opérations disponibles pour les développeurs. Chaque API contient une référence au service principale qui mettent en œuvre, l’API et sa carte opérations pour les opérations effectuées par le service de données principale. Opérations de gestion des API sont hautement configurables, avec un contrôle sur URL mappage, requête et paramètres de chemin d’accès, demande et contenu de la réponse et la mise en cache de réponse opération. La limite de taux, les quotas et les stratégies de restriction IP peuvent également être implémentées au niveau de l’opération individuelle ou API.

Pour plus d’informations, voir [comment créer des API][] et explique [comment ajouter des opérations à une API][].


## <a name="products"></a> Produits

Produits sont comment API est exposé pour les développeurs. Produits de gestion des API ont un ou plusieurs API et sont configurés avec un titre, description et conditions d’utilisation. Produits peuvent être **ouverte** ou **protégé**. Produits protégées doivent être abonnés à avant de pouvoir être utilisés, pendant l’ouverture produits peuvent être utilisés sans un abonnement. Lorsqu’un produit est prête à être utilisée par les développeurs, il peut être publié. Une fois qu’il est publié, pouvoir être affiché (et pour les produits protégées abonné à) par les développeurs. Approbation de l’abonnement est configurée au niveau du produit et peut exiger l’approbation administrateur ou être acceptés automatiquement.

Groupes sont utilisées pour gérer la visibilité des produits pour les développeurs. Produits accorder visibilité aux groupes et les développeurs peuvent afficher et s’abonner aux produits qui sont visibles par les groupes dans lequel elles appartiennent. 

Pour plus d’informations, voir [comment créer et publier un produit][] et la vidéo suivante.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Groupes

Groupes sont utilisées pour gérer la visibilité des produits pour les développeurs. Gestion des API contient les groupes système immuable suivants.

-   **Les administrateurs** , les administrateurs d’abonnement Azure sont membres de ce groupe. Les administrateurs gérer instances de service de gestion de l’API, création de l’API, opérations et produits qui sont utilisés par les développeurs.
-   **Les développeurs** - portail pour les développeurs authentifiés utilisateurs appartiennent à ce groupe. Les développeurs sont les clients qui génèrent des applications à l’aide de votre API. Les développeurs ont accès au portail développeur et créer des applications qui appellent les opérations d’une API.
-   **Invités** - utilisateurs du portail pour les développeurs non authentifiés, tels que des clients potentiels vous visitez le portail de développement d’une instance de gestion de l’API appartiennent à ce groupe. Ils peuvent avoir certain accès en lecture seule, telles que la fonctionnalité permettant d’afficher des API, mais pas les appeler.

Outre ces groupes système, les administrateurs peuvent créer des groupes personnalisés ou [tirer parti des groupes externes dans les clients Azure Active Directory associés](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Personnalisé et groupes externes peuvent être utilisés avec les groupes de système dans donnant visibilité les développeurs et l’accès aux produits API. Par exemple, vous pourriez créer un groupe personnalisé pour les développeurs affiliés à une organisation partenaire et lui permettre l’accès aux API à partir d’un produit contenant uniquement les API correspondantes. Un utilisateur peut être membre du plusieurs groupe.

Pour plus d’informations, voir [comment créer et utiliser des groupes][].

## <a name="developers"></a> Les développeurs

Les développeurs représentent les comptes d’utilisateurs dans une instance de service de gestion de l’API. Les développeurs peuvent être créés ou invités à rejoindre par les administrateurs, ou qu’ils peuvent s’inscrire à partir du [portail pour les développeurs][]. Chaque développeur est membre d’un ou plusieurs groupes et peut être s’abonner aux produits qui accorder visibilité à ces groupes.

Lorsque les développeurs s’abonner à un produit qu’ils reçoivent la clé du produit principale et secondaire. Cette clé est utilisée lorsque vous faites appel à l’API du produit.

Pour plus d’informations, voir [créer ou inviter les développeurs][] et [comment associer des groupes avec les développeurs][].

## <a name="policies"></a> Stratégies

Les stratégies sont une fonctionnalité puissante de gestion des API qui permettent de l’éditeur modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d’instructions qui sont exécutées dans un ordre séquentiel dans la demande ou de réponse d’une API. Instructions populaires incluent la conversion du format XML vers JSON et limitation de débit appel pour limiter la durée des appels entrants à partir d’un développeur, et de nombreuses autres stratégies sont disponibles.

Expressions de stratégie peuvent être utilisées en tant que valeurs d’attribut ou valeurs de texte dans un des stratégies de gestion de l’API, sauf indication contraire de la stratégie. Certaines stratégies telles que les stratégies de [flux de contrôle](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) et [Définissez variable](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) basées sur des expressions de stratégie. Pour plus d’informations, voir [Stratégies avancées](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), les [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx)et regarder la vidéo suivante.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Pour une liste complète des stratégies de gestion des API, voir [référence de la stratégie][]. Pour plus d’informations sur l’utilisation et la configuration des stratégies, voir [stratégies de gestion des API][]. Pour un didacticiel sur la création d’un produit avec taux de stratégies de limite et de quota, voir [comment créer et configurer les paramètres avancés de produit][]. Pour une démonstration, voir la vidéo suivante.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Portail pour les développeurs

Le portail développeur est l’endroit où les développeurs peuvent en savoir plus sur vos opérations API, d’affichage et d’appel et vous abonner aux produits. Clients potentiels peuvent visiter le portail pour les développeurs, afficher API et opérations et inscrire. L’URL de votre portail développeur se trouve dans le tableau de bord dans le portail classique Azure pour votre instance de service de gestion de l’API.

Vous pouvez personnaliser l’apparence de votre portail développeur en ajoutez du contenu personnalisé, personnalisation des styles et l’ajout de votre marque.

## <a name="api-management-and-the-api-economy"></a>Gestion des API et API économique

Pour en savoir plus sur la gestion des API, regardez la présentation suivante à partir de la conférence Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portail pour les développeurs]: #developer-portal

[Comment créer des API]: api-management-howto-create-apis.md
[Comment ajouter des opérations à une API]: api-management-howto-add-operations.md
[Comment créer et publier un produit]: api-management-howto-add-products.md
[Comment créer et utiliser des groupes]: api-management-howto-create-groups.md
[Comment faire pour associer des groupes avec les développeurs]: api-management-howto-create-groups.md#associate-group-developer
[Comment créer et configurer les paramètres avancés de produit]: api-management-howto-product-with-rules.md
[Comment créer ou inviter les développeurs]: api-management-howto-create-or-invite-developers.md
[Référence de la stratégie]: api-management-policy-reference.md
[Stratégies de gestion des API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
