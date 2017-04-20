<properties 
    pageTitle="La personnalisation du portail de développement de gestion de l’API Azure à l’aide de modèles | Microsoft Azure" 
    description="Découvrez comment personnaliser le portail de développement de gestion de l’API Azure à l’aide de modèles." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>La personnalisation du portail de développement de gestion de l’API Azure à l’aide de modèles

Gestion des API Azure fournit plusieurs fonctionnalités de personnalisation pour permettre aux administrateurs de [Personnaliser l’apparence du site du portail pour les développeurs](api-management-customize-portal.md), ainsi que personnaliser le contenu des pages du portail développeur à l’aide d’un ensemble de modèles que configurer le contenu des pages eux-mêmes. À l’aide de syntaxe [DotLiquid](http://dotliquidmarkup.org/) et un ensemble fourni de ressources de type chaîne localisés, les icônes et les contrôles de la page, vous avez une grande flexibilité pour configurer le contenu des pages selon vos besoins à l’aide de ces modèles.

## <a name="developer-portal-templates-overview"></a>Vue d’ensemble des modèles portail développeur

Modèles de portail développeur sont gérées dans le portail de développement par les administrateurs de l’instance de service de gestion de l’API. Pour gérer les modèles de développeur, accédez à votre instance de service de gestion de l’API dans le portail classique Azure, puis cliquez sur **Parcourir**.

![Portail pour les développeurs][api-management-browse]

Si vous êtes déjà dans le portail de publisher, vous pouvez accéder au portail développeur en cliquant sur le **portail pour les développeurs**.

![Menu du portail pour les développeurs][api-management-developer-portal-menu]

Pour accéder aux modèles portail développeur, cliquez sur la gauche pour afficher le menu de personnalisation de l’icône Personnaliser, puis cliquez sur **modèles**.

![Modèles de portail développeur][api-management-customize-menu]

La liste des modèles affiche différentes catégories de modèles portant sur les différentes pages dans le portail développeur. Chaque modèle est différent, mais la procédure pour les modifier et publier les modifications est identiques. Pour modifier un modèle, cliquez sur le nom du modèle.

![Modèles de portail développeur][api-management-templates-menu]

Cliquer sur un modèle vous permet d’accéder à la page du portail développeur qui peut personnaliser ce modèle. Dans cet exemple, la **liste des produits** du modèle s’affiche. Le modèle de **liste de produits** contrôle la zone de l’écran indiqué par le rectangle rouge. 

![Modèle de liste de produits][api-management-developer-portal-templates-overview]

Certains modèles, tels que les modèles de **Profil utilisateur** , personnaliser différentes parties de la même page. 

![Modèles de profil utilisateur][api-management-user-profile-templates]

L’éditeur pour chaque modèle portail développeur comporte deux sections affichées au bas de la page. Le côté gauche affiche le volet d’édition pour le modèle, et la partie droite affiche le modèle de données pour le modèle. 

Le volet de modification de modèle contient le balisage qui détermine l’apparence et le comportement de la page correspondante dans le portail développeur. Le balisage dans le modèle utilise la syntaxe de la [DotLiquid](http://dotliquidmarkup.org/) . Un seul éditeur populaires pour DotLiquid est [DotLiquid pour les concepteurs](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Les modifications apportées au modèle pendant la modification sont affichées en temps réel dans le navigateur, mais ne sont pas visibles à vos clients jusqu'à ce que vous [Enregistrer](#to-save-a-template) et [Publier](#to-publish-a-template) le modèle.

![Balisage de modèle][api-management-template]

Le volet **données du modèle** fournit un guide pour le modèle de données pour les entités qui peuvent être utilisés dans un modèle spécifique. Il fournit ce guide en affichant les données réelles qui sont actuellement affichées dans le portail développeur. Vous pouvez développer les volets modèle en cliquant sur le rectangle dans le coin supérieur droit du volet **données du modèle** .

![Modèle de données][api-management-template-data]

Dans l’exemple précédent, il existe deux catégories de produits affichées dans le portail de développement qui ont été récupérées à partir des données affichées dans le volet **données du modèle** , comme le montre l’exemple suivant.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

Les marques de révision dans le modèle de **liste de produits** traite les données pour offrir la sortie désirée en parcourant la collection de produits pour afficher des informations et un lien vers chacun des produits. Remarque la `<search-control>` et `<page-control>` éléments du balisage. Ces contrôlent l’affichage de la recherche et la pagination des contrôles de la page. `ProductsStrings|PageTitleProducts`est une référence de chaîne localisée qui contient le `h2` texte d’en-tête de la page. Pour obtenir une liste de chaîne de ressources, les contrôles de page et les icônes disponibles pour utiliser dans les modèles de portail développeur, voir [référence de modèles portail de gestion de l’API du développeur](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Enregistrer un modèle

Pour enregistrer un modèle, cliquez sur Enregistrer dans l’éditeur de modèles.

![Enregistrer le modèle][api-management-save-template]

Les modifications enregistrées ne sont pas actifs dans le portail de développement jusqu'à ce qu’elles sont publiées.

## <a name="to-publish-a-template"></a>Pour publier un modèle

Modèles enregistrés peuvent être publiés individuellement, ou tous les éléments. Pour publier un modèle particulier, cliquez sur Publier dans l’éditeur de modèles.

![Publier le modèle][api-management-publish-template]

Cliquez sur **Oui** pour confirmer et que le modèle direct sur le portail développeur.

![Confirmer publier][api-management-publish-template-confirm]

Pour publier toutes les versions de modèle actuellement non publié, cliquez sur **Publier** dans la liste de modèles. Modèles non publiés sont indiquées par un astérisque placé après le nom du modèle. Dans cet exemple, les modèles de **liste de produits** et **produit** sont publiées.

![Publier des modèles][api-management-publish-templates]

Cliquez sur **publier les personnalisations** pour confirmer.

![Confirmer publier][api-management-publish-customizations]

Modèles nouvellement publiés sont efficaces immédiatement dans le portail développeur.

## <a name="to-revert-a-template-to-the-previous-version"></a>Pour revenir à la version précédente

Pour revenir à la version publiée précédente, cliquez sur Nouveau dans l’éditeur de modèles.

![Rétablir le modèle][api-management-revert-template]

Cliquez sur **Oui** pour confirmer.

![Confirmer][api-management-revert-template-confirm]

La version publiée précédemment d’un modèle est en ligne dans le portail de développement une fois l’opération d’annulation est terminée.

## <a name="to-restore-a-template-to-the-default-version"></a>Pour restaurer un modèle dans la version par défaut

Restaurer les modèles dans sa version par défaut est un processus en deux étapes. Tout d’abord les modèles doivent être restaurés et puis les versions restaurées doivent être publiées.

Pour restaurer un seul modèle à la version par défaut, cliquez sur Restaurer dans l’éditeur de modèles.

![Rétablir le modèle][api-management-reset-template]

Cliquez sur **Oui** pour confirmer.

![Confirmer][api-management-reset-template-confirm]

Pour rétablir tous les modèles de leurs versions par défaut, cliquez sur **restaurer les modèles par défaut** dans la liste des modèles.

![Restaurer des modèles][api-management-restore-templates]

Les modèles restaurés doivent puis publiés individuellement ou simultanément en suivant les étapes décrites dans [pour publier un modèle](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Référence du développeur modèles portail

Pour plus d’informations de référence pour les modèles portail pour les développeurs, les ressources de type chaîne, les icônes et les contrôles de la page, voir [référence de modèles portail de gestion de l’API du développeur](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Regarder une vidéo de présentation

Regardez la vidéo suivante pour savoir comment ajouter un forum de discussion et évaluations dans les pages de l’API et opération dans le portail développeur à l’aide de modèles.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







