<properties 
    pageTitle="Comment utiliser l’inspecteur de l’API de suivi des appels gestion des API Azure" 
    description="Découvrez comment effectuer le suivi des appels en utilisant l’inspecteur de l’API dans la gestion des API Azure." 
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

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Comment utiliser l’inspecteur de l’API de suivi des appels gestion des API Azure

Gestion des API constitue un outil API inspecteur pour vous aider à débogage et résolution des problèmes de votre API. L’inspecteur de l’API peut être utilisé par programme et peut également être utilisé directement à partir du portail de développement. 

Outre les opérations de suivi, l’inspecteur de l’API également le suivi des évaluations [expression de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Pour une démonstration, consultez [Cloud garde épisode 177 : plus les fonctionnalités de gestion de l’API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) et avance rapide à 21:00.

Ce guide fournit une vue d’ensemble de l’aide de l’API inspecteur.

>[AZURE.NOTE] API inspecteur traces sont uniquement générées et rendus disponibles pour les demandes contenant des touches abonnement qui appartiennent au compte [d’administrateur](api-management-howto-create-groups.md) .

## <a name="trace-call"></a> Utilisation API inspecteur effectuer le suivi d’un appel

Pour utiliser l’inspecteur de l’API, ajoutez un **ocp-apim-trace : true** demande à votre appel opération, l’en-tête, puis téléchargez et examinez la trace à l’aide de l’URL indiquée par l’en-tête de réponse **apim ocp-emplacement suivi** . Cela peut être effectué par programmation et peut également être effectuée directement à partir du portail de développement.

Ce didacticiel montre comment utiliser l’inspecteur de l’API suivi des opérations à l’aide de l’API de calculatrice de base est configuré dans le didacticiel de mise en route lors de l’obtention de [Gérer votre première API](api-management-get-started.md) . Si vous n’avez pas terminé ce didacticiel ne prend quelques instants pour importer l’API de calculatrice simple, ou vous pouvez utiliser une autre API de votre choix, tels que l’API de l’écho. Chaque instance de service de gestion de l’API est préconfigurée avec une API écho pouvant être utilisées pour expérimenter et en savoir plus sur la gestion des API. L’API écho renvoie toute entrée est envoyée à celui-ci. Pour l’utiliser, vous pouvez appeler n’importe quel verbe HTTP, et la valeur de retour sera simplement que vous avez envoyé. 



Pour commencer, cliquez sur le **portail pour les développeurs** dans le portail classique Azure pour votre service de gestion de l’API. Opérations peuvent être appelées directement à partir du portail de développement qui offre un moyen pratique pour afficher et tester les opérations d’une API.

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

![Portail de gestion de l’API de développeur][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis cliquez sur **Calculatrice de base**.

![API écho][api-management-api]

Cliquez sur **essayer** pour essayer de l’opération **ajouter deux entiers** .

![Essaie][api-management-open-console]

Conserver les valeurs de paramètre par défaut, puis sélectionnez la clé d’abonnement pour le produit que vous souhaitez utiliser à partir de la **clé abonnement** déroulante.

Par défaut dans le portail développeur la **Trace de Apim Ocp** en-tête est déjà défini sur **true**. Cet en-tête configure qu’une trace est générée ou non.

![Envoyer][api-management-http-get]

Cliquez sur **Envoyer** pour l’opération d’appel.

![Envoyer][api-management-send-results]

Dans la réponse en-têtes correspond à un **emplacement ocp-apim-suivi** avec une valeur similaire à l’exemple suivant.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

La trace peut être téléchargée à partir de l’emplacement spécifié et examinée comme illustré dans l’étape suivante.

## <a name="inspect-trace"> </a>Inspecter le suivi

Pour passer en revue les valeurs dans la trace, téléchargez le fichier de suivi à partir de l’URL **apim ocp-emplacement suivi** . Il est un fichier texte au format JSON et contient des entrées semblables à l’exemple suivant.

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>Étapes suivantes

-   Regardez une démonstration d’effectuer le suivi des expressions de stratégie dans [Cloud garde épisode 177 : plus les fonctionnalités de gestion de l’API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avance rapide à 21:00 pour voir la démo.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 