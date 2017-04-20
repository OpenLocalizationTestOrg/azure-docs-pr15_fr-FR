<properties 
    pageTitle="Comment créer des API de gestion des API Azure" 
    description="Découvrez comment créer et configurer des API de gestion des API Azure." 
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

# <a name="how-to-create-apis-in-azure-api-management"></a>Comment créer des API de gestion des API Azure

Une API dans la gestion des API représente un ensemble d’opérations qui peut être appelé par les applications clientes. Nouvelles API est créés dans le portail publisher et puis sont ajoutées les opérations de votre choix. Une fois que les opérations sont ajoutées, l’API est ajouté à un produit et peut être publié. Une fois une API est publiée, peut être abonné à et utilisée par les développeurs.

Ce guide montre la première étape du processus : comment créer et configurer une nouvelle API de la gestion des API. Pour plus d’informations sur l’ajout des opérations et publication d’un produit, voir [comment ajouter des opérations à une API][] et [comment créer et publier un produit][].

## <a name="create-new-api"> </a>Créer une nouvelle API

API est créés et configurés dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **API** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Ajouter des API**.

![Créer des API][api-management-create-api]

Utilisez la fenêtre **Ajouter une nouvelle API** pour configurer la nouvelle API.

![Ajouter la nouvelle API][api-management-add-new-api]

Les champs suivants sont utilisés pour configurer la nouvelle API.

-   **Nom de l’API Web** fournit un nom unique et descriptif pour l’API. Il s’affiche dans les portails développeur et publisher.
-   **URL du service Web** fait référence au service HTTP l’API de mise en œuvre. Gestion des API transmet les requêtes à cette adresse.
-   **URL de l’API Web suffixe** est ajouté à l’URL de base pour le service de gestion des API. La base de l’URL est courant pour toutes les API hébergées par une instance de service de gestion de l’API. Gestion de l’API distingue API par leur suffixe et par conséquent le suffixe doit être unique pour chaque API pour un éditeur donné.
-   **Modèle de l’URL de l’API Web** détermine les protocoles qui peuvent être utilisés pour accéder à l’API. HTTPs est spécifié par défaut.
-   Pour vous permettre d’ajouter cette nouvelle API à un produit, cliquez sur les **produits (facultatifs)** liste déroulante et choisissez un produit. Cette étape peut être répétée plusieurs fois pour ajouter de l’API à plusieurs produits.

Une fois que les valeurs souhaitées sont configurés, cliquez sur **Enregistrer**. Une fois que la nouvelle API est créée, la page Résumé de l’API s’affiche dans le portail de publisher.

![Résumé de l’API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Paramètres de l’API configurer

Vous pouvez utiliser l’onglet **paramètres** et vérifier ou modifier la configuration d’une API. **Nom de l’API Web**et **suffixe dans l’URL de l’API Web** **URL du service Web**sont initialement définis lors de l’API est créé et peut être modifié ici. **Description** fournit une description facultative et **modèle de l’URL de l’API Web** détermine les protocoles qui peuvent être utilisés pour accéder à l’API.

![Paramètres de l’API][api-management-api-settings]

Pour configurer l’authentification de passerelle pour le service en aval l’API de mise en œuvre, sélectionnez l’onglet **sécurité** . La **informations d’identification de** liste déroulante peut servir à configurer l’authentification de **base HTTP** ou **certificats clients** . Pour utiliser l’authentification de base HTTP, suffit d’entrer les informations d’identification de votre choix. Pour plus d’informations sur l’utilisation d’authentification par certificat client, voir [Comment faire pour sécuriser les services principale à l’aide d’authentification par certificat client Gestion des API Azure][].

L’onglet **sécurité** peut également servir à configurer l' **autorisation de l’utilisateur** à l’aide de OAuth 2.0. Pour plus d’informations, voir [comment autoriser comptes développeur à l’aide de 2.0 OAuth gestion des API Azure][].

![Paramètres d’authentification de base][api-management-api-settings-credentials]

Cliquez sur **Enregistrer** pour enregistrer les modifications apportées aux paramètres de l’API.

## <a name="next-steps"> </a>Étapes suivantes

Une fois une API est créée et les paramètres configurés, les étapes suivantes sont pour ajouter les opérations à l’API, ajoutez l’API à un produit et publiez-le afin qu’il soit disponible pour les développeurs. Pour plus d’informations, voir les articles suivants.

-   [Comment ajouter des opérations à une API][]
-   [Comment créer et publier un produit][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Comment ajouter des opérations à une API]: api-management-howto-add-operations.md
[Comment créer et publier un produit]: api-management-howto-add-products.md

[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[La sécurisation de l’authentification par certificat Azure API de gestion des services principale à l’aide de client]: api-management-howto-mutual-certificates.md
[Comment autoriser des comptes pour les développeurs à l’aide de OAuth 2.0 Gestion des API Azure]: api-management-howto-oauth2.md