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
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>L’importation de la définition d’une API avec opérations de gestion des API Azure

Gestion des API, nouvelles API peut être créées et les opérations ajoutées manuellement, ou l’API peut être importé ainsi que les opérations en une seule étape.

API et leurs opérations peuvent être importées avec les formats suivants.

-   WADL
-   Swagger

Ce guide montre comment créer une nouvelle API et ses opérations en une seule opération d’importation. Pour plus d’informations sur la création manuelle d’une API et ajouter des opérations, voir [comment créer des API][] et explique [comment ajouter des opérations à une API][].

## <a name="import-api"> </a>Importer une API

API est créés et configurés dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

![Portail de Publisher][api-management-management-console]

Cliquez sur **API** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Importer des API**.

![API d’importation][api-management-import-apis]

La fenêtre **Importer API** comprend trois onglets qui correspondent aux trois façons pour fournir la spécification API.

-   **À partir de Presse-papiers** vous permet de coller la spécification de l’API dans la zone de texte désigné.
-   **À partir du fichier** vous permet de rechercher et sélectionner le fichier qui contient la spécification de l’API.
-   **À partir de l’URL** vous permet de fournir l’URL à la spécification de l’API.

![Format d’importation API][api-management-import-api-clipboard]

Après avoir fourni la spécification API, utilisez les cases à droite pour indiquer la spécification de format. Les formats suivants sont pris en charge.

-   WADL
-   Swagger

Entrez un **suffixe dans l’URL de l’API Web**. Il est ajouté à l’URL de base pour votre service de gestion des API. La base de l’URL est courant pour toutes les API hébergées sur chaque instance d’un service de gestion de l’API. Gestion de l’API distingue API par leur suffixe et par conséquent le suffixe doit être unique pour chaque API dans une instance de service de gestion des API spécifique.

Une fois que toutes les valeurs sont entrées, cliquez sur **Enregistrer** pour créer l’API et opérations associées. 

>[AZURE.NOTE] Pour un didacticiel de l’importation d’une calculatrice de base API au format Swagger, voir [Gérer votre première API gestion des API Azure](api-management-get-started.md).

## <a name="export-api"></a> Exporter une API

Outre l’importation de nouvelles API, vous pouvez exporter les définitions de votre API à partir du portail de publisher. Pour ce faire, cliquez sur **Exporter API** à partir de l' **onglet Résumé** de votre **API**.

![API d’exportation][api-management-export-api]

API peut être exportés à l’aide de WADL ou Swagger. Sélectionnez le format souhaité, cliquez sur **Enregistrer**et choisissez l’emplacement dans lequel vous voulez enregistrer le fichier.

![Format de l’API d’exportation ?][api-management-export-api-format]

## <a name="next-steps"> </a>Étapes suivantes

Une fois une API est créée et les opérations importées, vous pouvez consulter et configurer des paramètres supplémentaires, ajoutez l’API à un produit et publiez-le afin qu’il soit disponible pour les développeurs. Pour plus d’informations, consultez les guides suivants.

-   [Comment configurer les paramètres de l’API][]
-   [Comment créer et publier un produit][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[Comment ajouter des opérations à une API]: api-management-howto-add-operations.md
[Comment créer et publier un produit]: api-management-howto-add-products.md
[Comment créer des API]: api-management-howto-create-apis.md
[Comment configurer les paramètres de l’API]: api-management-howto-create-apis.md#configure-api-settings
