<properties
    pageTitle="Mise à niveau vers la Version 2 de l’API d’Analytique texte | Microsoft Azure"
    description="Apprentissage texte Analytique - mise à niveau vers la Version 2 de l’ordinateur Azure"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Mise à niveau vers la Version 2 de l’API d’Analytique du texte #

Ce guide vous guidera tout au long du processus de mise à niveau de votre code à partir de la [première version de l’API](../machine-learning/machine-learning-apps-text-analytics.md) à l’aide de la deuxième version. 

Si vous n’avez jamais utilisé l’API et que vous voulez en savoir plus, vous pouvez **[en savoir plus sur l’API ici](//go.microsoft.com/fwlink/?LinkID=759711)** ou **[Suivez le Guide de démarrage rapide](//go.microsoft.com/fwlink/?LinkID=760860)**. Pour référence technique, reportez-vous à la **[Définition de l’API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Partie 1. Obtenir une nouvelle clé ###

Tout d’abord, vous devez obtenir une nouvelle clé de l’API à partir du **Portail Azure**:

1. Accédez au service de texte Analytique via la [Galerie de Intelligence Cortana](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Ici, vous trouverez également des liens vers les exemples de documentation et le code.

1. Cliquez sur **s’inscrire**. Ce lien vous accédez alors au portail de gestion Azure, où vous pouvez vous inscrire pour le service.

1. Sélectionner un plan. Vous pouvez sélectionner la **couche gratuit pour 5 000 transactions par mois**. Comme une offre gratuite, vous ne sera pas être facturée au moyen du service. Vous avez besoin pour vous connecter à votre abonnement Azure. 

1. Une fois que vous vous inscrivez pour Analytique de texte, vous aurez une **Clé d’API**. Copiez cette clé, que vous en aurez besoin lorsque vous utilisez les services API.

### <a name="part-2-update-the-headers"></a>Partie 2. Mettre à jour les en-têtes ###

Mettre à jour les valeurs d’en-tête soumis comme illustré ci-dessous. Notez que la clé de compte est codée n’est plus.

**Version 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Version 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Partie 3. Mettre à jour l’URL de base ###

**Version 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Version 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Partie 4 a. Mettre à jour les formats pour les langues, expressions clés et opinion ###

#### <a name="endpoints"></a>Points de terminaison ####

OBTENIR des points de terminaison sont désormais déconseillées, afin que toutes les entrées doivent être envoyée en tant que billet demande. Mettre à jour les points de terminaison à ceux présentés ci-dessous.

| |Point de terminaison unique version 1|Point de terminaison lot version 1|Point de terminaison de la version 2|
|---|---|---|---|
|Type d’appel|Télécharger|Publier|Publier|
|Opinion|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Expressions clés|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Traduction|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formats d’entrée ####

Notez que seul format billet est désormais accepté, afin que vous devez reformater toute entrée qui précédemment utilisé en conséquence les points de terminaison document unique. Entrées ne respectent pas la casse.

**Version 1 (lot)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Sortie des opinion ####

**Version 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Sortie des expressions clés ####

**Version 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Sortie des langues ####


**Version 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Partie 4 b. Mettre à jour les formats de rubriques ###

#### <a name="endpoints"></a>Points de terminaison ####

| |Point de terminaison version 1 | Point de terminaison de la version 2|
|---|---|---|
|Soumettre pour la détection de rubrique (POST)|```StartTopicDetection```|```topics```|
|Récupérer des résultats de la rubrique (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formats d’entrée ####

**Version 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Résultats de l’envoi ####

**Version 1 (POST)**

Précédemment, lorsque la tâche est terminée, vous recevez le résultat JSON suivant, où l’identificateur serait ajouté à une URL pour récupérer la sortie.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Version 2 (POST)**

La réponse maintenant inclura une valeur d’en-tête comme suit, où `operation-location` est utilisé comme point de terminaison pour interroger des résultats :

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Résultats de l’opération ####

**Version 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2 (GET)**

Comme auparavant, **interroger régulièrement la sortie** (la période suggérée est toutes les minutes) jusqu'à ce que le résultat est renvoyé. 

Lorsque l’API rubriques est terminé, une lecture état `succeeded` sera retourné. Cela inclura ensuite les résultats dans le format illustré ci-dessous :

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Partie 5. Tester ! ###

Vous devez à présent mention ! Tester votre code avec un petit exemple pour vous assurer que vous pouvez traiter correctement vos données.
