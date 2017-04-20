<properties
    pageTitle="Gérer votre première API gestion des API Azure | Microsoft Azure"
    description="Apprenez à créer des API, ajouter des opérations et mise en route de la gestion des API."
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

# <a name="manage-your-first-api-in-azure-api-management"></a>Gérer votre première API gestion des API Azure

## <a name="overview"> </a>Vue d’ensemble

Ce guide vous montre comment rapidement prise en main de l’utilisation de la gestion de l’API Azure et faire appel à votre première API.

## <a name="concepts"> </a>Quoi consiste la gestion de l’API Azure ?

Vous pouvez utiliser Gestion des API Azure pour effectuer un serveur principal et lancer un programme API entière basé sur lui.

Scénarios courants sont les suivantes :

* Les attaques d' **infrastructure mobile sécurisation** par des passerelles access avec des clés de l’API, empêchant les pratiques recommandées à l’aide de la limitation ou l’utilisation de stratégies de sécurité avancées comme validation jeton JWT.
* **Écosystèmes partenaires fil l’activation** en offrant d’intégration partenaire rapide via le portail développeur et en créant une façade API de dissocier les mises en œuvre internes qui ne sont pas pour l’améliorer la consommation partenaire.
* **Exécution d’un programme API interne** grâce à un emplacement centralisé pour l’organisation pour communiquer sur la disponibilité et les dernières modifications apportées à l’API, access basées sur les comptes d’organisation, des passerelles toutes basé sur un canal sécurisé entre la passerelle API et le système principal.


Le système est composé des éléments suivants :

* La **passerelle API** est le point de terminaison qui :
  * Accepte l’API des appels et les route vers votre les serveurs principaux.
  * Vérifie API clés, JWT jetons, les certificats et autres informations d’identification.
  * Applique les quotas d’utilisation et limites de taux.
  * Transforme votre API à la volée sans les modifications du code.
  * Met en cache les réponses principal dans lequel définir.
  * Journaux d’appels métadonnées à des fins analytique.

* Le **portail de publisher** est l’interface d’administration dans lequel vous configurez votre programme API. Utiliser pour :
    * Définir ou importer un schéma API.
    * Package API dans les produits.
    * Définir des stratégies, tels que les quotas ou transformations sur les API.
    * Obtenir des informations à partir d’analytique.
    * Gérer les utilisateurs.

* Le **portail pour les développeurs** sert de la présence de site web principal pour les développeurs, où ils peuvent :
    * Documentation de l’API de lecture.
    * Essayer une API via la console interactive.
    * Créer un compte et s’abonner pour obtenir des clés de l’API.
    * Analytique Access sur leur utilisation.


## <a name="create-service-instance"> </a>Créer une instance de gestion de l’API

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite][].

Utilisation de gestion des API la première étape consiste à créer une instance de service. Connectez-vous au [Portail classique Azure][] et cliquez sur **Nouveau**, **Services d’application**, **Gestion de l’API**, **créer**.

![Nouvelle instance de gestion de l’API][api-management-create-instance-menu]

Pour l' **URL**, spécifiez un nom unique sous-domaine à utiliser pour l’URL du service.

Sélectionnez **l’abonnement** et **région** souhaitée pour votre instance de service. Après avoir effectué vos sélections, cliquez sur le bouton **suivant** .

![Nouveau service de gestion de l’API][api-management-create-instance-step1]

Entrez **Contoso Ltd.** pour le **Nom de l’organisation**, entrez votre adresse de messagerie dans le champ **E-Mail administrateur** .

>[AZURE.NOTE] Cette adresse de messagerie est utilisée pour les notifications à partir du système de gestion de l’API. Pour plus d’informations, voir [comment configurer les notifications et les modèles de courrier électronique dans la gestion des API Azure][].

![Nouveau service de gestion de l’API][api-management-create-instance-step2]

Instances de service de gestion de l’API sont disponibles dans trois niveaux : développeur, Standard et Premium. Par défaut, les nouvelles instances de service de gestion de l’API sont créés dans la couche développeur. Pour sélectionner la couche Standard ou Premium, cochez la case à cocher **Paramètres avancés** , puis sélectionnez le niveau souhaité sur l’écran suivant.

>[AZURE.NOTE] La couche développeur est de développement, de test et de programmes API pilotes lorsque la disponibilité n’est pas un problème. Dans les couches Standard et Premium, vous pouvez mettre à l’échelle votre nombre d’unités réservés pour gérer davantage de trafic. Les couches Standard et Premium fournissent votre service de gestion de l’API puissance de traitement et les performances. Vous pouvez effectuer ce didacticiel à l’aide de n’importe quel niveau. Pour plus d’informations sur les niveaux de gestion de l’API, voir [Gestion des API tarifs][].

Cliquez sur la case à cocher pour créer votre instance de service.

![Nouveau service de gestion de l’API][api-management-instance-created]

Une fois l’instance de service est créée, l’étape suivante consiste à créer ou importer une API.

## <a name="create-api"> </a>Importer une API

Une API se compose d’un ensemble d’opérations qui peut être appelé à partir d’une application cliente. Opérations de l’API sont traitées aux services web existants.

API pouvant être créées (et opérations peuvent être ajoutées) manuellement, ou qu’ils puissent être importés. Dans ce didacticiel, nous allons importer l’API d’un service web de calculatrice exemple fourni par Microsoft et hébergé sur Azure.

>[AZURE.NOTE] Pour des instructions sur la création d’une API et ajout manuel d’opérations, voir [comment créer des API](api-management-howto-create-apis.md) et explique [comment ajouter des opérations à une API](api-management-howto-add-operations.md).

API est configurés à partir du portail publisher, qui est accessible via le portail classique Azure. Pour atteindre le portail publisher, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API.

![Portail de Publisher][api-management-management-console]

Pour importer la calculatrice API, cliquez sur **API** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Importer des API**.

![Bouton Importer API][api-management-import-api]

Effectuez les opérations suivantes pour configurer la calculatrice API :

1. Cliquez sur **à partir de l’URL**, entrez **http://calcapi.cloudapp.net/calcapi.json** dans la zone de texte **URL du document spécification** , puis cliquez sur le bouton d’option **Swagger** .
2. Tapez **calc** dans la zone de texte **suffixe dans l’URL de l’API Web** .
3. Cliquez dans la zone **produits (facultatifs)** et sélectionnez **Starter**.
4. Cliquez sur **Enregistrer** pour importer l’API.

![Ajouter la nouvelle API][api-management-import-new-api]

>[AZURE.NOTE] **Gestion des API** prend actuellement en charge la version 1.2 et 2.0 de Swagger document pour l’importation. Vous assurer que, même si la [spécification 2.0 Swagger](http://swagger.io/specification) déclare que `host`, `basePath`, et `schemes` propriétés sont facultatives, votre document 2.0 Swagger **doit** contenir ces propriétés ; Sinon, elle ne sont importée. 

Une fois que l’API est importée, la page Résumé de l’API s’affiche dans le portail de publisher.

![Résumé de l’API][api-management-imported-api-summary]

La section API comporte plusieurs onglets. L’onglet **Résumé** affiche les mesures de base et des informations sur l’API. L’onglet [paramètres](api-management-howto-create-apis.md#configure-api-settings) est utilisé pour afficher et modifier la configuration d’une API. L’onglet [opérations](api-management-howto-add-operations.md) est utilisée pour gérer les opérations de l’API. L’onglet **sécurité** peut être utilisé pour configurer l’authentification de passerelle pour le serveur principal à l’aide de l’authentification de base ou [l’authentification par certificat commun](api-management-howto-mutual-certificates.md)et configurer [l’autorisation d’utilisateur à l’aide de OAuth 2.0](api-management-howto-oauth2.md).  L’onglet **problèmes** est utilisé pour afficher les problèmes signalés par les développeurs qui utilisent votre API. L’onglet **produits** est utilisé pour configurer les produits contenant cette API.

Par défaut, chaque instance de gestion de l’API est fourni avec deux exemples de produits :

-   **Starter**
-   **Illimité**

Dans ce didacticiel, l’API de calculatrice de base a été ajouté au produit Starter lors de l’API ont été importée.

Pour passer des appels à une API, les développeurs Abonnez-vous d’abord à un produit qui lui permet d’accéder à celui-ci. Les développeurs peuvent s’abonner aux produits dans le portail pour les développeurs ou les administrateurs peuvent s’abonner aux développeurs de produits dans le portail de publisher. Vous êtes un administrateur dans la mesure où vous avez créé l’instance de gestion de l’API dans la procédure précédente dans le didacticiel, afin que vous êtes déjà abonné à tous les produits par défaut.

## <a name="call-operation"> </a>Appeler une opération à partir du portail pour les développeurs

Opérations peuvent être appelées directement à partir du portail pour les développeurs, qui offre un moyen pratique pour afficher et tester les opérations d’une API. Dans cette étape didacticiel, vous allez appeler opération **ajouter deux entiers** de l’API calculatrice de base. Cliquez sur **le portail développeur** dans le menu en haut à droite du portail de publisher.

![Portail pour les développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis cliquez sur **Calculatrice de base** pour afficher les opérations disponibles.

![Portail pour les développeurs][api-management-developer-portal-calc-api]

Notez les descriptions d’exemple et les paramètres qui ont été importés ainsi que les API et les opérations, fournissant une documentation pour les développeurs qui utilisera cette opération. Ces descriptions peuvent également être ajoutées lorsque opérations sont ajoutées manuellement.

Pour appeler l’opération **ajouter deux entiers** , cliquez sur **essayer**.

![Essaie][api-management-developer-portal-calc-api-console]

Vous pouvez entrer des valeurs pour les paramètres ou conserver les valeurs par défaut, puis sur **Envoyer**.

![HTTP Get][api-management-invoke-get]

Une fois qu’une opération est appelée, le portail développeur affiche l' **état de la réponse**, les **en-têtes de réponse**et tout **contenu de la réponse**.

![Réponse][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Afficher analytique

Pour afficher analytique de calculatrice de base, revenez en le portail publisher en sélectionnant **Gérer** dans le menu en haut à droite du portail développeur.

![Gérer][api-management-manage-menu]

L’affichage par défaut pour le portail publisher est le **tableau de bord**, qui fournit une vue d’ensemble de votre instance de gestion de l’API.

![Tableau de bord][api-management-dashboard]

Pointez la souris sur le graphique pour **Calculatrice de base** afficher les mesures spécifiques pour l’utilisation de l’API pour une période donnée.

>[AZURE.NOTE] Si vous ne voyez pas toutes les lignes sur votre graphique, revenez au portail de développement et passer des appels à l’API, patientez quelques instants et puis revenez au tableau de bord.

Cliquez sur **Afficher les détails** pour afficher la page de résumé de l’API, y compris une version plus grande des mesures affichées.

![Analytique][api-management-mouse-over]

![Résumé][api-management-api-summary-metrics]

Pour des métriques détaillées et les rapports, cliquez sur **Analytique** dans le menu de **Gestion de l’API** sur la gauche.

![Vue d’ensemble][api-management-analytics-overview]

La section **Analytique** comporte les quatre onglets suivants :

-   **En un clin de œil** fournit métriques générale de l’utilisation et la santé, ainsi que les développeurs, meilleurs produits, API supérieur et opérations supérieure.
-   **L’utilisation** offre une présentation détaillée en appels API et la bande passante, y compris une représentation géographique.
-   **Santé** se concentre sur les codes d’état, mettre en cache taux de réussite, temps de réponse et API et réactivité du service.
-   **Activité** fournit des rapports d’exploration vers le bas sur l’activité spécifique en développeur, produit, API et opération.

## <a name="next-steps"> </a>Étapes suivantes

- Découvrez comment [protéger votre API avec des limites de taux](api-management-howto-product-with-rules.md).

[Version d’évaluation gratuite Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Comment faire pour configurer les notifications et les modèles de courrier électronique dans la gestion des API Azure]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Prix de gestion de l’API]: http://azure.microsoft.com/pricing/details/api-management/

[Portail classique Azure]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
