<properties 
    pageTitle="Comment créer et publier un produit dans la gestion des API Azure" 
    description="Découvrez comment créer et publier des produits dans la gestion des API Azure." 
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
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Comment créer et publier un produit dans la gestion des API Azure

Gestion des API Azure un produit contient un ou plusieurs API ainsi qu’un quota d’utilisation et les conditions d’utilisation. Une fois qu’un produit a été publié, les développeurs peuvent s’abonner au produit et commencer à utiliser les API de produits. La rubrique fournit un guide sur la création d’un produit, l’ajout d’une API et publication pour les développeurs.

## <a name="create-product"> </a>Créer un produit

Opérations sont ajoutées et configurées pour une API dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Dans le menu de gauche pour afficher la page **produits** , cliquez sur **produits** , puis cliquez sur **Ajouter un produit**.

![Produits][api-management-products]

![Nouveau produit][api-management-add-new-product]

Dans le champ **nom** et une description du produit dans le champ **Description** , entrez un nom descriptif pour le produit.

Produits de gestion de l’API peuvent être **ouverte** ou **protégé**. Produits protégées doivent être abonnés à avant de pouvoir être utilisés, pendant l’ouverture produits peuvent être utilisés sans un abonnement. Vérifiez **l’abonnement** pour créer un produit protégé qui nécessite un abonnement. Il s’agit du paramètre par défaut.

Cochez la case **Exiger l’approbation d’abonnement** administrateur pour réviser et accepter ou refuser des tentatives d’abonnement à ce produit. Si la case est désactivée, tentatives d’abonnement seront acceptés automatiquement. Pour plus d’informations sur les abonnements, voir [vue abonnés à un produit][].

Pour permettre les comptes développeur pour vous abonner plusieurs fois sur le produit, cochez la case à cocher **Autoriser plusieurs abonnements** . Si cette case à cocher n’est pas activée, chaque compte développeur peut s’abonner qu’une seule fois sur le produit.

![Illimité plusieurs abonnements][api-management-unlimited-multiple-subscriptions]

Pour limiter le nombre de plusieurs abonnements sonneries, cochez la case à cocher **limiter le nombre de sonnerie abonnements à** , entrez la limite de l’abonnement. Dans l’exemple suivant, les abonnements sonneries sont limités à quatre par compte développeur.

![Quatre plusieurs abonnements][api-management-four-multiple-subscriptions]

Une fois que toutes les nouvelles options de produit sont configurées, cliquez sur **Enregistrer** pour créer le nouveau produit.

![Produits][api-management-products-page]

>Par défaut nouveaux produits non publiés et sont visibles uniquement au groupe **administrateurs** .

Pour configurer un produit, cliquez sur le nom du produit dans l’onglet **produits** .

## <a name="add-apis"> </a>API ajouter à un produit

La page **produits** contient des quatre liens pour une configuration : **Résumé**, **paramètres**, **visibilité**et **les abonnés**. L’onglet **Résumé** est l’endroit où vous pouvez ajouter des API et publier ou annuler la publication d’un produit.

![Résumé][api-management-new-product-summary]

Avant de publier votre produit que vous devez ajouter un ou plusieurs API. Pour ce faire, cliquez sur **Ajouter des API au produit**.

![Ajouter des API][api-management-add-apis-to-product]

Sélectionnez les API souhaités, puis cliquez sur **Enregistrer**.

## <a name="add-description"> </a>Ajouter des informations descriptives à un produit

L’onglet **paramètres** vous permet de fournir des informations détaillées sur le produit tels que son objectif et les API permet d’accéder à d’autres informations utiles. Le contenu est destiné aux développeurs de l’API en appelant et peuvent être écrites en texte brut ou des balises HTML.

![Paramètres du produit][api-management-product-settings]

**Abonnement** pour créer un produit protégé qui nécessite un abonnement à utiliser ou désactivez la case à cocher pour créer un produit en cours qui peut être appelé sans un abonnement.

Si vous voulez approuver manuellement toutes les demandes d’abonnement produit, sélectionnez **Exiger l’approbation de l’abonnement** . Par défaut, tous les abonnements produit sont accordées automatiquement.

Pour autoriser les comptes développeur pour vous abonner plusieurs fois sur le produit, cochez la case à cocher **Autoriser plusieurs abonnements** et vous pouvez également spécifier une limite. Si cette case à cocher n’est pas activée, chaque compte développeur peut s’abonner qu’une seule fois sur le produit.

Vous pouvez également remplir le champ **conditions d’utilisation** pour décrire les conditions d’utilisation du produit quels abonnés doivent accepter pour pouvoir utiliser le produit.

## <a name="publish-product"> </a>Publier un produit

Avant de l’API dans un produit peuvent être appelés, le produit doit être publié. Sous l’onglet **Résumé** pour le produit, cliquez sur **Publier**, puis cliquez sur **Oui, publiez-le** pour confirmer. Pour rendre un produit publié précédemment privé, cliquez sur **Annuler la publication**.

![Publier le produit][api-management-publish-product]

## <a name="make-visible"> </a>Rendre un produit visible pour les développeurs

L’onglet **visibilité** vous permet de choisir les rôles sont en mesure de voir le produit dans le portail développeur ou s’y abonner le produit.

![Visibilité de produit][api-management-product-visiblity]

Pour activer ou désactiver la visibilité d’un produit pour les développeurs dans un groupe, cochez ou décochez la case à cocher en regard du groupe, puis sur **Enregistrer**.

>Pour plus d’informations, voir [comment créer et utiliser des groupes pour gérer les comptes pour les développeurs dans Azure API gestion][].

## <a name="view-subscribers"> </a>Afficher les abonnés à un produit

L’onglet **abonnés** répertorie les développeurs qui abonnement sur le produit. Les détails et les paramètres pour chaque développeur peuvent être affichés en cliquant sur nom du développeur. Dans cet exemple aucun développeurs n’abonné encore sur le produit.

![Développeurs][api-management-developer-list]

## <a name="next-steps"> </a>Étapes suivantes

Une fois les API souhaités sont ajoutés et le produit publié, les développeurs peuvent s’abonner au produit et commencer à appeler les API. Pour consulter le didacticiel décrivant ces éléments ainsi que la configuration du produit avancées voir [comment créer et configurer les paramètres de produit avancées dans la gestion des API Azure][].

Pour plus d’informations sur l’utilisation des produits, voir la vidéo suivante.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Aux abonnés d’affichage d’un produit]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Comment créer et utiliser des groupes pour gérer les comptes pour les développeurs gestion des API Azure]: api-management-howto-create-groups.md
[Comment créer et configurer les paramètres avancés produit gestion des API Azure]: api-management-howto-product-with-rules.md 