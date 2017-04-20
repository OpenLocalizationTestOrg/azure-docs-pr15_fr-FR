<properties
    pageTitle="API d’apprentissage machine : Texte Analytique | Microsoft Azure"
    description="Machine apprentissage texte Analytique API de Microsoft peuvent être utilisées pour analyser un texte non structuré rubrique la détection, l’extraction des phrase clé, détection de la langue et analyse opinion."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>API d’apprentissage machine : Texte Analytique pour opinion, clé d’Extraction d’une expression, détection de la langue et la détection de rubrique

>[AZURE.NOTE] Ce guide est destiné version 1 de l’API. Pour la version 2, [**reportez-vous à ce document**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Version 2 est désormais la version par défaut de cette API.

## <a name="overview"></a>Vue d’ensemble

L’API Analytique texte est une suite de texte analytique [services web](https://datamarket.azure.com/dataset/amla/text-analytics) créées avec apprentissage automatique Azure. L’API peut être utilisée pour analyser un texte non structuré pour les tâches telles qu’analyse opinion, extraction phrase clé, détection de la langue et détection de la rubrique. Aucune donnée formation n’est nécessaire pour utiliser cette API : simplement importez des données texte. Cette API utilise avancée techniques de traitement en langage naturel pour optimale des prédictions de cours.

Vous pouvez voir analytique de texte en action sur notre [site démo](https://text-analytics-demo.azurewebsites.net/), où vous trouverez également [exemples](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) sur la mise en œuvre analytique texte en c# et Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Analyse opinion

L’API renvoie un résultat numérique entre 0 et 1. Scores près de 1 indiquent opinion positive, tandis que scores près de 0 indiquent la présence négative opinion. Score opinion est généré à l’aide des techniques de classification. Les fonctionnalités d’entrée au classifieur incluent n-g, fonctionnalités générées à partir des balises partie de discours et incorporations word. Pour l’instant, anglais est le seul langage pris en charge.
 
## <a name="key-phrase-extraction"></a>Extraction de phrase clé

L’API renvoie une liste de chaînes indiquant les principaux points principaux de la saisie de texte. Nous utilisons techniques à partir sophistiquée de traitement en langage naturel Kit de ressources de Microsoft Office. Pour l’instant, anglais est le seul langage pris en charge.

## <a name="language-detection"></a>Détection de la langue

L’API renvoie la langue détectée et un résultat numérique entre 0 et 1. Scores près de 1 indiquent 100 % s’assurer que la langue identifiée est vraie. Un total de 120 langues sont prises en charge.

## <a name="topic-detection"></a>Détection de rubrique

Il s’agit d’une nouvelle version API qui retourne haut détecté rubriques pour une liste des soumis enregistrements texte. Une rubrique est identifiée avec une phrase clé, qui peut être un ou plusieurs connexes mots. Cette API requiert un minimum de 100 enregistrements texte à présenter, mais est conçu pour détecter les rubriques sur des centaines de milliers d’enregistrements. Notez que cette API facture 1 transaction par enregistrement texte envoyé. L’API est conçu pour fonctionner correctement pour courte, humain texte écrit tels que des révisions et les commentaires des utilisateurs.

---

## <a name="api-definition"></a>Définition de l’API

### <a name="headers"></a>En-têtes

Veillez à inclure les en-têtes corrects dans la demande, qui doit se présenter comme suit :

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous pouvez trouver votre clé de compte à partir de votre compte sur le [Marché des données Azure](https://datamarket.azure.com/account/keys). Notez que JSON actuellement uniquement est accepté pour les formats d’entrée et de sortie. XML n’est pas pris en charge.

---

## <a name="single-response-apis"></a>API de réponse unique

### <a name="getsentiment"></a>GetSentiment

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemple de demande**

Dans l’appel en dessous, nous invitons analyse opinion pour la phrase « Hello World » :

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Cela renverra une réponse comme suit :

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Exemple de demande**

Dans l’appel ci-dessous, nous invitons que les phrases clés figurant dans le texte « Elle a été un hôtel exceptionnelle afin de rester à, avec jardin unique et le personnel convivial » :

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Cela renverra une réponse comme suit :

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Exemple de demande**

Dans l’appel GET ci-dessous, nous invitons pour l’opinion pour les phrases clés dans le texte *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Cela renverra une réponse comme suit :

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Paramètres facultatifs**

`NumberOfLanguagesToDetect`est un paramètre facultatif. La valeur par défaut est 1.

---

## <a name="batch-apis"></a>API de traitement par lots

Le service de texte Analytique vous permet de faire opinion et une expression clé extractions en mode de traitement par lots. Notez que chacun des enregistrements a obtenu détermine comme une transaction. Par exemple, si vous demandez opinion d’enregistrements 1000 dans un seul appel, 1000 transactions seront déduites.

Notez que les ID entrés dans le système sont les ID retournés par le système. Le service web ne vérifie pas que ces ID est uniques. Il est la responsabilité de l’appelant à vérifier le caractère unique. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Exemple de demande**

Dans l’appel billet ci-dessous, nous invitons pour les éléments des phrases « Hello World », « Foo Bonjour » et « Mes Bonjour » dans le corps de la requête :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corps de la requête :

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Dans la réponse ci-dessous, vous obtenez la liste de résultats associée à votre texte ID :

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Exemple de demande**

Dans cet exemple, nous invitons pour la liste des éléments pour les phrases clés dans les textes suivants : 

* « Il a été un hôtel exceptionnelle afin de rester à, avec jardin unique et le personnel convivial »
* « Elle a été une conférence build étonnant, avec très intéressante parle »
* « Le trafic a été terrible, j’ai passé trois heures accédant à l’aéroport »

Cette requête est effectuée comme un appel billet au point de terminaison :

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corps de la requête :

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Dans la réponse ci-dessous, vous obtenez la liste des expressions clés associé à votre texte ID :

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "Anglais",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "Français",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>Rubrique détection API

Il s’agit d’une nouvelle version API qui retourne haut détecté rubriques pour une liste des soumis enregistrements texte. Une rubrique est identifiée avec une phrase clé, qui peut être un ou plusieurs connexes mots. Notez que cette API facture 1 transaction par enregistrement texte envoyé.

Cette API requiert un minimum de 100 enregistrements texte à présenter, mais est conçu pour détecter les rubriques sur des centaines de milliers d’enregistrements.


### <a name="topics--submit-job"></a>Rubriques – travail envoyer

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Exemple de demande**


Dans l’appel de billet ci-dessous, nous invitons rubriques pour un ensemble de 100 articles, où les articles d’entrée premier et derniers sont affichées et deux StopPhrases sont inclus.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corps de la requête :

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Dans la réponse ci-dessous, vous obtenez l’identificateur pour le travail soumis :

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Liste de mot unique ou plusieurs expressions de word qui ne doivent pas être retournées comme des sujets. Peut être utilisé pour filtrer les rubriques très génériques. Par exemple, dans un jeu de données sur des révisions hôtel, « hôtel » et « hostel » peut-être arrêter sensible phrases.  

### <a name="topics--poll-for-job-results"></a>Rubriques – sondage pour les résultats de la tâche

**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Exemple de demande**

Passez l’identificateur retourné à partir de l’étape « Envoyer travail » pour récupérer les résultats. Nous vous recommandons d’appeler ce point de terminaison minute jusqu’au statut = « Terminé » dans la réponse. Vous devrez patienter environ 10 minutes pour une tâche pour complète ou plus de temps pour les projets avec des milliers d’enregistrements.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Pendant le traitement, la réponse sera comme suit :

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


L’API renvoie le résultat au format JSON au format suivant :

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Les propriétés pour chaque partie de la réponse sont les suivantes :

**Propriétés TopicInfo**

| Clé | Description |
|:-----|:----|
| IDRubrique | Un identificateur unique pour chaque sujet. |
| Score | Nombre d’enregistrements affectés à la rubrique. |
| KeyPhrase | Un mot ou une expression pour la rubrique synthèse. Peut être 1 ou plusieurs mots. |

**Propriétés TopicAssignment**

| Clé | Description |
|:-----|:----|
| ID | Identificateur de l’enregistrement. Correspond à l’ID inclus dans l’entrée. |
| IDRubrique | ID de la rubrique qui l’enregistrement a été affecté à. |
| Distance | Confiance que l’enregistrement appartient à la rubrique. Distance rapprocher à zéro indique une confiance plus élevée. |
