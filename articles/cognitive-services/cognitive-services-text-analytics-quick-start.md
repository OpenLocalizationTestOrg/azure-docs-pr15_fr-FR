<properties
    pageTitle="Guide de démarrage rapide : Machine apprentissage texte Analytique API | Microsoft Azure"
    description="Machine Azure texte Analytique - Guide de démarrage rapide d’apprentissage"
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

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Prise en main les API Analytique texte pour détecter opinion, expressions clés, rubriques et langue

<a name="HOLTop"></a>

Ce document décrit comment intégré à votre service ou l’application à utiliser les [API Analytique de texte](//go.microsoft.com/fwlink/?LinkID=759711).
Vous pouvez utiliser ces API pour détecter opinion, expressions clés, rubriques et langue de votre texte. [Cliquez ici pour voir une démonstration interactive de l’expérience.](//go.microsoft.com/fwlink/?LinkID=759712)

Consultez les [définitions de l’API](//go.microsoft.com/fwlink/?LinkID=759346) de documentation technique relative à l’API.

Ce guide est destiné version 2 des API. Pour plus d’informations sur la version 1 de l’API, [reportez-vous à ce document](../machine-learning/machine-learning-apps-text-analytics.md).

À la fin de ce didacticiel, vous ne pourrez pas détecter par programmation :

- **Opinion** - correspond à texte positive ou négative ?

- **Phrases clé** - quelles sont les personnes discuter dans un seul article ?

- **Rubriques** - quelles sont les personnes traitant de nombreux articles ?

- **Langues** - pose correspond à texte écrit ?

Notez que cette API facture 1 transaction par document soumis. Par exemple, si vous demandez opinion pour les documents 1000 en un seul appel, 1000 transactions seront déduites.



<a name="Overview"></a>
## <a name="general-overview"></a>Vue d’ensemble ##

Ce document est un guide étape par étape. Notre objectif est de vous guident dans les étapes nécessaires pour former un modèle et vous oriente vers des ressources qui vous permet de mettre en production. Cet exercice prendra environ 30 minutes.

Pour ces tâches, vous avez besoin d’un éditeur et appelez les points de terminaison RESTful dans la langue de votre choix.

Prise en main !

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Tâche 1 - signature pour les API Analytique du texte ####

Dans cette tâche, vous sera s’inscrire pour le service analytique du texte.

1. Accédez aux **Services cognitifs** dans le [Portail Azure](//go.microsoft.com/fwlink/?LinkId=761108) et vérifiez **Que Analytique de texte** est sélectionné comme type de l’API.

1. Sélectionner un plan. Vous pouvez sélectionner la **couche gratuit pour 5 000 transactions par mois**. Comme une offre gratuite, vous ne sera pas être facturée au moyen du service. Vous avez besoin pour vous connecter à votre abonnement Azure. 

1. Complétez les autres champs et créez votre compte.

1. Une fois que vous vous inscrivez pour texte Analytique, recherchez votre **Clé d’API**. Copiez la clé primaire, car vous en aurez besoin lorsque vous utilisez les services API.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Tâche 2 - détecter opinion et des expressions clés et les langues ####

Il est facile de détecter les langues, expressions clés et opinion dans votre texte. Vous obtiendrez par programmation le même résultat que l' [expérience de démonstration](//go.microsoft.com/fwlink/?LinkID=759712) retourne.

>[AZURE.TIP] Pour effectuer une analyse opinion, nous vous recommandons de fractionner le texte en phrases. Ceci est généralement mène à une précision plus élevée dans des prédictions opinion.

Notez que les langues prises en charge sont les suivantes :

| Fonctionnalité | Codes de langue prise en charge |
|:-----|:----|
| Opinion | `en`(En anglais), `es` (espagnol), `fr` (Français), `pt` (Brésil) |
| Expressions clés | `en`(En anglais), `es` (espagnol), `de` (allemand), `ja` (japonais) |


1. Vous devez configurer les en-têtes à ce qui suit. Notez que JSON est actuellement le format d’entrée accepté uniquement pour les API. XML n’est pas pris en charge.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. Ensuite, mettez en forme des lignes d’entrée dans JSON. Pour opinion, expressions clés et langue, le format est le même. Notez que chaque ID doit être unique et sera l’ID retourné par le système. La taille maximale d’un document unique pouvant être envoyé est de 10 Ko et la taille totale maximale de l’entrée soumise est de 1 Mo. Ne plus de 1 000 documents peuvent être présentées dans un appel. Limitation de taux existe à un taux de 100 appels par minute : nous vous recommandons dès lors que vous envoyez de grandes quantités de documents dans un seul appel. Langue est un paramètre facultatif qui doit être indiqué si l’analyse du texte non anglais. Voici un exemple d’entrée ci-dessous, où le paramètre facultatif `language` pour opinion analyse ou touche extraction d’une expression est incluse :

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Passer un appel **billet** au système avec l’entrée pour opinion, expressions clés et langue. Les URL seront présentera comme suit :

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Cet appel renverra une JSON mis en forme de réponse aux ID et propriétés détectées. Voici un exemple de la sortie opinion en dessous (avec les détails de l’erreur exclues). Dans le cas d’opinion, un score compris entre 0 et 1 est retourné pour chaque document :

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Tâche 3 - détecter les rubriques dans un corpus de texte ####

Il s’agit d’une nouvelle version API qui retourne haut détecté rubriques pour une liste des soumis enregistrements texte. Une rubrique est identifiée avec une phrase clé, qui peut être un ou plusieurs connexes mots. L’API est conçu pour fonctionner correctement pour courte, humain texte écrit tels que des révisions et les commentaires des utilisateurs.

Cette API requiert **un minimum de 100 enregistrements texte** à présenter, mais est conçu pour détecter les rubriques sur des centaines de milliers d’enregistrements. Tous les enregistrements non anglais ou enregistrements avec des mots inférieure à 3 seront ignorés et ne doivent donc pas être affectées aux rubriques. Pour la détection de la rubrique, la taille maximale d’un document unique pouvant être envoyé est 30 Ko et la taille totale maximale de l’entrée soumise est 30 Mo. La détection rubrique est limité à 5 envois 5 minutes de taux.

Il existe deux paramètres d’entrée supplémentaires **facultatif** qui peuvent vous aider à améliorer la qualité de résultats :

- **Cesser de mots.**  Ces termes et leurs formulaires Fermer (par exemple, pluriels) seront exclues du pipeline de détection ensemble de la rubrique. Utilisez cette option pour les mots courants (par exemple, « problème », « erreur » et « utilisateurs » peuvent être bonnes décisions concernant la réclamation d’un client sur le logiciel). Chaque chaîne doit être un seul mot.
- **Arrêter de phrases** - ces expressions exclues de la liste des rubriques retournées. Utilisez cette option pour exclure les rubriques génériques que vous ne souhaitez pas voir dans les résultats. Par exemple, « Microsoft » et « Azure » serait qui conviennent des rubriques à exclure. Chaînes peuvent contenir plusieurs mots.

Suivez ces étapes pour détecter les rubriques dans votre texte.

1. Mettre en forme l’entrée dans JSON. Cette fois, vous pouvez définir des mots et arrêter phrases.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. Les mêmes en-têtes, telle que définie dans la tâche 2, rendre un **billet** appeler au point de terminaison rubriques :

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Ceci renverra une `operation-location` comme en-tête dans la réponse, dont la valeur est l’URL de requête pour les rubriques qui en résulte :

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Interroger retourné `operation-location` régulièrement avec une requête **GET** . Une fois par minute est recommandé.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. Le point de terminaison renverra une réponse, y compris `{"status": "notstarted"}` avant le traitement, `{"status": "running"}` pendant le traitement et `{"status": "succeeded"}` avec la sortie une fois terminée. Vous pouvez ensuite utiliser la sortie qui sera au format suivant (note détails tels que les dates et mettre en forme de l’erreur ont été exclus de cet exemple) :

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Notez que la réponse réussie des rubriques à partir du `operations` point de terminaison aura le schéma suivant :

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Explications pour chaque partie de cette réponse sont les suivantes :

**rubriques**

| Clé | Description |
|:-----|:----|
| ID | Un identificateur unique pour chaque sujet. |
| score | Nombre de documents qui sont affectés à la rubrique. |
| keyPhrase | Un mot ou une expression pour la rubrique synthèse. |

**topicAssignments**

| Clé | Description |
|:-----|:----|
| documentId | Identificateur pour le document. Correspond à l’ID inclus dans l’entrée. |
| IDRubrique | ID de la rubrique qui le document a été affecté à. |
| distance | Score du document-rubrique affiliation comprise entre 0 et 1. Bas à une distance score est l’affiliation rubrique forte. |

**erreurs**

| Clé | Description |
|:-----|:----|
| ID | Identificateur unique de document d’entrée l’erreur fait référence à. |
| Message | Message d’erreur. |

## <a name="next-steps"></a>Étapes suivantes ##

Félicitations ! Vous avez maintenant terminé à l’aide d’analytique texte sur vos données. Vous souhaiterez peut-être maintenant rechercher en utilisant un outil tel que [Power BI](//powerbi.microsoft.com) pour visualiser vos données, ainsi que l’automatisation vos idées pour vous fournir une vue en temps réel de vos données de texte.

Pour voir comment les fonctionnalités de texte Analytique, tels qu’opinion, peuvent servir dans le cadre d’un robot, voir l’exemple [Robots émotionnelle](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) sur le site robots Framework.
