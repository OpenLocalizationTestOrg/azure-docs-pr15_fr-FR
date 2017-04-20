<properties 
    pageTitle="Référence la stratégie de gestion des API Azure" 
    description="En savoir plus sur les stratégies disponibles pour configurer la gestion de l’API." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Référence la stratégie de gestion des API Azure

Cette section fournit un index pour les stratégies dans la [référence de stratégie de gestion des API][]. Pour plus d’informations sur l’ajout et la configuration des stratégies, voir [stratégies de gestion des API][].

Expressions de stratégie peuvent être utilisées en tant que valeurs d’attribut ou valeurs de texte dans un des stratégies de gestion de l’API, sauf indication contraire de la stratégie. Certaines stratégies telles que les stratégies de [flux de contrôle][] et [Définissez variable][] basées sur des expressions de stratégie. Pour plus d’informations, voir [Stratégies avancées][] et [expressions de stratégie][]

## <a name="policy-reference-index"></a>Index de référence de stratégie

-   [Stratégies de restriction d’accès][]
    -   [En-tête HTTP vérifier][] - applique existence et/ou la valeur d’un en-tête HTTP.
    -   [Taux d’appels limite par abonnement][] - utilisation de l’API empêche dépasse en limitant le taux d’appel, sur une base par abonnement.
    -   [Taux d’appels limite par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - utilisation de l’API empêche dépasse en limitant le taux d’appel, sur une base par clé.
    -   [Restreindre l’appelant adresses IP][] - filtres (permet de/refuse) appels à partir des adresses IP spécifiques et/ou les plages d’adresses.
    -   [Définir le quota d’utilisation par abonnement][] - vous permet d’appliquer un renouvelable ou durée de vie appel volume et/ou la bande passante quota, sur une base par abonnement.
    -   [Définir le quota d’utilisation par clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - vous permet d’appliquer un renouvelable ou durée de vie appel volume et/ou la bande passante quota, sur une base par clé.
    -   [Valider JWT][] - applique existence et la validité d’une JWT extrait à partir d’un en-tête HTTP spécifiée ou un paramètre de requête spécifié.
-   [Stratégies avancées][]
    -   [Flux de contrôle][] - applique conditionnellement déclarations de stratégie basées sur les résultats de l’évaluation booléennes [expressions][].
    -   [Demande de transfert][] - transfère la demande au service principal.
    -   [Journal des événements concentrateur][] : envoie des messages au format spécifié à une cible message définie par une entité [journal](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
    -   [Réessayer](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - tentatives d’exécution des instructions stratégie encadré, si et jusqu'à ce que la condition est remplie. L’exécution se répète à intervalles réguliers et nombre de tentatives jusqu'à spécifié.
    -   [Retourner réponse](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - l’exécution du pipeline abandon et renvoie la réponse spécifiée directement à l’appelant.
    -   [Envoyer la demande unidirectionnelle](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - envoie une demande à l’URL spécifiée sans attendre une réponse.
    -   [Envoyer la demande](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - envoie une demande à l’URL spécifiée.
    -   [Méthode de demande set](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - vous permet de modifier la méthode HTTP pour une demande.
    -   [Définir le statut](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - modifie le code d’état HTTP à la valeur spécifiée.
    -   [Définir la variable][] - conserver une valeur dans une variable nommé [contexte][] pour un accès ultérieur.
    -   [Trace](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - ajoute une chaîne dans le résultat de [L’API inspecteur](../api-management/api-management-howto-api-inspector.md) .
    -   [Patienter](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - attend encadré demande d’envoi, d’obtenir la valeur de cache ou les stratégies de flux de contrôle se termine avant de poursuivre.
-   [Stratégies d’authentification][]
    -   [Authentifier avec Basic][] - authentifier avec un service en aval à l’aide de l’authentification de base.
    -   [Authentification par certificat client][] - authentifier avec un service en aval à l’aide de certificats clients.
-   [Stratégies de mise en cache][] 
    -   [Accéder à partir de cache][] - effectuer cache rechercher et renvoyer une réponse mis en cache valide lorsqu’elles sont disponibles.
    -   [Store pour mettre en cache][] - réponse cache en fonction de la configuration du contrôle du cache spécifié.
    -   [Obtenir valeur cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - récupérer un élément mis en cache par clé.
    -   [Valeur de base de dans le cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - Store un élément dans le cache par clé.
    -   [Supprimez la valeur du cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - supprimer un élément dans le cache par clé.
-   [Croisées stratégies de domaine][] 
    -   [Autoriser les appels entre domaines][] - rend l’API accessible à partir de clients navigateur Adobe Flash et Silverlight Microsoft.
    -   [CORS][] - ajoute la prise en charge (CORS) à une opération ou une API pour autoriser les appels entre domaines à partir de clients navigateur le partage des ressources origine croisée.
    -   [JSONP][] - ajoute JSON avec prise en charge de marge intérieure (JSONP) à une opération ou une API pour autoriser les appels entre domaines à partir de clients de navigateur JavaScript.
-   [Stratégies de transformation][] 
    -   Le corps de JSON [JSON convertir au format XML][] - convertit demande ou une réponse au format XML.
    -   [Convertir XML vers JSON][] - convertit demande ou réponse corps dans le fichier XML en JSON.
    -   [Rechercher et remplacer la chaîne de corps][] - recherche une sous-chaîne demande ou de réponse et remplace par une autre sous-chaîne.
    -   [Masque les URL dans le contenu][] - réécriture (masques) des liens dans la réponse corps afin qu’ils pointent vers le lien équivalent via la passerelle.
    -   [Configurer le service en aval][] - modifie le service de serveur principal pour une demande entrante.
    -   [Définir le corps][] - définit le corps du message pour les demandes entrantes et sortantes.
    -   [En-tête HTTP définir][] - attribue une valeur à une réponse existante et/ou l’en-tête de la requête ou ajoute un en-tête de réponse et/ou demande de nouveau.
    -   [Définir le paramètre de chaîne de requête][] - ajoute, valeur remplace, ou suppressions demander le paramètre de chaîne de requête.
    -   [La réécriture d’URL][] - convertit une URL de requête à partir de sa forme public au formulaire attendu par le service web.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les expressions de stratégie, voir la vidéo suivante.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Stratégies de restriction d’accès]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[En-tête de chèque HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Taux d’appels limite par abonnement]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restreindre l’appelant adresses IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Définir le quota de l’utilisation par abonnement]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Valider JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Stratégies avancées]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flux de contrôle]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Définir la variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexte]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Demande de transfert]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Journal des événements concentrateur]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Stratégies d’authentification]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Vous authentifier avec Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authentification par certificat client]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Stratégies de mise en cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Accéder à partir du cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Stocker pour mettre en cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Croisées stratégies de domaine]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Autoriser les appels entre domaines]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Stratégies de transformation]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convertir JSON XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convertir XML JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Rechercher et remplacer la chaîne de corps]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Masque les URL dans le contenu]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Service en aval ensemble]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Jeu de corps]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[En-tête HTTP défini]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Paramètre de chaîne de requête de jeu]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Réécriture d’URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Stratégies de gestion des API]: api-management-howto-policies.md
[Référence de stratégie de gestion des API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expressions de stratégie]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
