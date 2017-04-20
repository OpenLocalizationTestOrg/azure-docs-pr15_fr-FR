
<properties
    pageTitle="Migration vers Azure Services cognitifs recommandations API à partir de DataMarket recommandations API | Microsoft Azure"
    description="Machine Azure recommandations : migration vers le service cognitifs recommandations d’apprentissage"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Migration vers Azure Services cognitifs recommandations API à partir de DataMarket recommandations API
Cet article vous explique comment migrer à partir de l' [API de recommandations Microsoft DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) à l' [API de recommandations Microsoft Azure cognitifs Services](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

L’API de recommandations DataMarket acceptera nouveaux clients 31 décembre 2016 et s’être déconseillée 28 février 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Comment démarrer à l’aide de l’API de recommandations Services cognitifs Azure ?

Pour effectuer une migration à l’API de recommandations Services cognitifs, procédez comme suit :

1.  Si vous n’avez pas un abonnement Azure, [s’inscrire](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) pour une. 

1.  Obtenir des instructions étape par étape à partir du [Guide de démarrage rapide](cognitive-services-recommendations-quick-start.md) pour s’inscrire à l’API de recommandations Services cognitifs et utilisez-le par programme. 

1.  Accédez à la [page d’accueil cognitifs Services recommandations API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) pour en savoir plus sur le service et rechercher de la documentation.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>J’ai utilisé l’interface utilisateur de recommandations pour créer Mes modèles. Existe-t-il un outil similaire pour l’API de recommandations Services cognitifs ?

Absolument ! L’URL de la nouvelle [Interface utilisateur de recommandations](http://recommendations-portal.azurewebsites.net/) est http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] Vos informations d’identification DataMarket ne fonctionnent pas ici. S’inscrire à un abonnement dans le portail Azure et obtenez la clé de compte nécessaires à l’utilisation de la nouvelle [Interface utilisateur de recommandations](http://recommendations-portal.azurewebsites.net/).
Si vous n’avez pas une clé de compte, voir tâche 1 du [Guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>Le nouveau format de l’API est identique à l’API de recommandations DataMarket ?

L’API prend en charge les scénarios mêmes et les flux de processus en tant que ces scénarios pris en charge dans la version DataMarket, mais l’interface API réel a été mis à jour pour respecter les [Instructions de l’API REST Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). L’API ne sont plus cohérentes et travail meilleurs avec des outils qui prennent en charge Swagger.

Pour mieux comprendre chacune de l’API, consultez l' [Explorateur de l’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Utilisez l' *essayer de* bouton pour tester un appel API. Le format de fichiers utilisée par l’API de recommandations (catalogue et l’utilisation des fichiers) n’a pas changé, vous pouvez continuer à utiliser les fichiers et/ou l’infrastructure que vous avez créé pour générer ces fichiers même.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Quelles sont les nouvelles fonctionnalités de l’API de recommandations Services cognitifs ?

Sur les deux derniers mois, nous avons publié des nouvelles fonctionnalités de l’API de recommandations cognitifs Services :
-   Recommandations l’interface utilisateur de formation et test des modèles sans avoir à écrire une seule ligne de code
-   Lot score pour vous offrir des milliers de recommandations en même temps
-   Créer prise en charge des indicateurs à la qualité des modèles de recommandations de requête
-   Prise en charge des règles d’entreprise
-   Possibilité d’énumérer les et télécharger des fichiers de catalogue et l’utilisation
-   Prise en charge de génération classement pour interroger la qualité des fonctionnalités d’élément dans un modèle de recommandations
-   Ajouté possibilité de recherche d’un produit dans le catalogue

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Lorsque Microsoft peut-il empêcher prenant en charge l’API de recommandations DataMarket ?

[API de recommandations sur DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) cesse d’accepter de nouveaux clients après le 31 décembre 2016 et sera être complètement déconseillées par 28 février 2017. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>Que se passe-t-il si je n’ai pas les données dont j’ai besoin de recréer Mes modèles dans l’API de recommandations Services cognitifs ?

Nous voulons cette transition aussi simple que possible pour vous. Nous pouvons vous aider à déplacer vos anciens modèles à partir de votre compte DataMarket à votre nouvel abonnement Azure cognitifs Services recommandations API. Contactez-nous à l’adresse [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Nous vous demandera à fournir votre DataMarket ID de l’abonnement et votre ID de l’abonnement Services cognitifs, avant de nous associer les modèles à votre nouveau compte.
