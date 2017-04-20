<properties 
    pageTitle="Comment ajouter des opérations à une API dans la gestion des API Azure | Microsoft Azure" 
    description="Découvrez comment ajouter des opérations à une API dans la gestion des API Azure." 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Comment ajouter des opérations à une API dans la gestion des API Azure

Avant de pouvoir utiliser une API dans la gestion des API, opérations doivent être ajoutées. Ce guide montre comment ajouter et configurer différents types d’opérations à une API de la gestion des API.

## <a name="add-operation"> </a>Ajoutez une opération

Opérations sont ajoutées et configurées pour une API dans le portail de publisher. Pour accéder au portail de publisher, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Sélectionnez l’API souhaité dans le portail publisher, puis sélectionnez l’onglet **opérations** . 

![Opérations][api-management-operations]

Cliquez sur **Ajouter une opération** pour ajouter une nouvelle opération. **nouvelle opération** s’affichera et l’onglet **Signature** est sélectionnée par défaut.

![Opération d’ajout][api-management-add-operation]

Spécifier le **verbe HTTP** en choisissant dans la liste déroulante.

![Méthode HTTP][api-management-http-method]

<a name="url-template"></a>

Définir le modèle d’URL en tapant dans un fragment d’URL composée d’un ou plusieurs segments de chemin d’URL et zéro, un ou plusieurs paramètres de chaîne de requête. Le modèle d’URL, ajouté à l’URL de base de l’API, identifie une seule opération HTTP. Il peut contenir un ou plusieurs nommée parties variables identifiées par des accolades. Ces parties variables sont appelés paramètres de modèle et sont attribuées dynamiquement extraites de l’URL de la demande lorsque la demande est traitée par la plateforme de gestion de l’API de valeurs.

![Modèle d’URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Si vous le souhaitez, spécifiez le **modèle de la réécriture d’URL**. Cela vous permet d’utiliser le modèle d’URL standard pour le traitement des demandes entrantes sur le serveur frontal, lors de l’appel principal via une URL convertie en fonction du modèle de réécriture. Paramètres de modèle à partir du modèle URL doivent être utilisés dans le modèle de réécriture. L’exemple suivant montre comment contenu type codé comme segment de chemin d’accès dans le service web à partir de l’exemple précédent peut être fournie comme paramètre de requête dans l’API publié via la plateforme de gestion de l’API à utiliser les modèles d’URL.

![Réécriture d’URL modèle][api-management-url-template-rewrite]

Les appelants à l’opération utilise le format `/customers?customerid=ALFKI` et il sera mappé à `/Customers('ALFKI')` lorsque le service principal est appelé.


Nom **d’affichage** et une **Description** fournissent une description de l’opération et servent à fournir une documentation pour les développeurs utilisant cette API dans le portail développeur.

![Description][api-management-description]

La description de l’opération peut être spécifiée en tant que texte brut ou HTML dans la zone de texte **Description** .

## <a name="operation-caching"> </a>Opération mise en cache

Réponse mise en cache réduit la latence perçue par les consommateurs API, consommation de bande passante réduit et diminue la charge sur le web HTTP mise en œuvre de l’API de service. 

Pour activer la mise en cache pour l’opération rapidement et facilement, cliquez sur l’onglet **mise en cache** et cochez la case **Activer** .

![La mise en cache][api-management-caching-tab]

**Durée** indique la période pendant laquelle la réponse de l’opération reste dans le cache. La valeur par défaut est 3 600 secondes ou 1 heure.

Clés de cache sont utilisées pour faire la distinction entre les réponses afin que la réponse correspondant à chaque clé cache différent obtenez sa propre valeur mis en cache distincte. Si vous le souhaitez, entrez les paramètres de chaîne de requête spécifique et/ou en-têtes HTTP à utiliser pour calculer les valeurs de clés de cache dans les zones de texte **variation par paramètres de chaîne de requête** et **variation par en-têtes** respectivement. Lorsqu’aucun sont URL de la requête spécifiée, complète et les valeurs d’en-tête HTTP suivantes sont utilisées dans génération de clés de cache : **Accepter** et **Accepter-jeu de caractères**.

>Pour plus d’informations sur la mise en cache et la mise en cache des stratégies, Découvrez [comment résultats de l’opération cache gestion des API Azure][].


## <a name="request-parameters"> </a>Paramètres de la demande

Paramètres de l’opération sont gérés sous l’onglet Paramètres. Paramètres spécifiés dans le **Modèle d’URL** sous l’onglet **Signature** sont ajoutés automatiquement et peuvent être modifiées uniquement en modifiant le modèle de l’URL. Vous pouvez entrer manuellement des paramètres supplémentaires.

Pour ajouter un paramètre de requête, cliquez sur **Ajouter un paramètre de requête** et entrez les informations suivantes :

-   **Nom** : nom du paramètre.
-   **Description** : une brève description du paramètre (facultatif).
-   **Type** : type de paramètre, sélectionnée dans la liste déroulante vers le bas.
-   **Valeurs** - valeurs qui peuvent être affectées à ce paramètre. Une des valeurs peut être marquée comme valeur par défaut (facultative).
-   **Obligatoire** : rendre le paramètre obligatoire en activant la case à cocher. 

![Paramètres de la demande][api-management-request-parameters]

## <a name="request-body"> </a>Corps de requête

Si l’opération permet (par exemple, placer, billet) et nécessite un corps peut donner un exemple de celle-ci dans l’ensemble des formats de représentation pris en charge (par exemple, json, XML). 

>Corps de la demande est utilisé uniquement à des fins de documentation et n’est pas validé.

Pour entrer un corps de la requête, basculez vers l’onglet **corps** .

Cliquez sur **Ajouter une représentation**, commencez à taper le nom de type de contenu souhaité (par exemple, application/json), sélectionnez-le dans la liste déroulante et collez l’exemple de corps demande souhaité dans le format sélectionné dans la zone de texte. 

![Corps de la requête][api-management-request-body]

Dans supplémentaire à textuelles, vous pouvez également spécifier une description facultative dans la zone de texte **Description** .

## <a name="responses"> </a>Les réponses

Il est conseillé de fournir des exemples de réponses pour tous les codes d’état l’opération peut produire. Chaque code d’état est peut-être plus d’un exemple de corps de réponse, une pour chacun des types de contenu pris en charge. 

Pour ajouter une réponse, cliquez sur **Ajouter** , puis commencez à taper le code d’état de votre choix. Dans cet exemple est le code d’état **200 OK**. Une fois que le code est affiché dans le menu déroulant, sélectionnez-le, puis le code de réponse est créé et ajouté à votre opération.

![Code de réponse][api-management-response-code]

Cliquez sur **Ajouter une représentation**, commencez à taper le nom du type de contenu souhaité (par exemple, application/json) et sélectionnez-le dans la liste déroulante vers le bas.

![Type de contenu de corps][api-management-response-body-content-type]

Collez l’exemple de corps de réponse dans le format sélectionné dans la zone de texte. 

![Corps de la réponse][api-management-response-body]

Si vous le souhaitez, ajoutez éventuellement une description dans la zone de texte **Description** .

Une fois l’opération terminée, cliquez sur **Enregistrer**.


## <a name="next-steps"> </a>Étapes suivantes

Une fois les opérations sont ajoutées à une API, l’étape suivante consiste à associer l’API à un produit et publiez-le afin que les développeurs peuvent appeler ses opérations.

-   [Comment créer et publier un produit][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Comment créer et publier un produit]: api-management-howto-add-products.md
[Comment faire pour mettre en cache opération des résultats gestion des API Azure]: api-management-howto-cache.md