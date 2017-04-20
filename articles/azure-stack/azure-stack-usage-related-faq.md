<properties
    pageTitle="Questions fréquentes relatives à l’utilisation | Microsoft Azure"
    description="Liste des mètres pile Azure, comparaison à l’utilisation Azure API, l’utilisation et signalés temps, les codes d’erreur."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="azure-stack-usage-api-faqs"></a>FAQ sur l’utilisation de l’API pile Azure
Cet article fournit des réponses à certaines questions fréquemment posées sur l’API de l’utilisation de pile Azure.

## <a name="what-meter-ids-can-i-see"></a>Quel compteur ID puis-je voir ?

Pour l’instant, l’utilisation est signalée pour le réseau, le stockage et le cluster les fournisseurs de ressources.

| **Fournisseur de ressources** | **ID compteur** |**Nom de compteur** | **Unité** | **Informations supplémentaires** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Réseau** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Utilisation d’adresses IP publiques | Adresse IP |                    
| **Espace de stockage**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | Go\*heures | Capacité totale utilisée par les tables |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | Go\*heures | Capacité totale utilisée par les objets BLOB de page |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | Go\*heures  | Capacité totale consommée en file d’attente |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | Go\*heures  | Capacité totale utilisée par les objets BLOB bloc |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Comptage des demandes dans 10,000s   | Demandes de service de table (dans 10,000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Données de pénétration dans Go | Entrée de données de service de tableau en Go |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress en Go | Sortie de données de service de tableau en Go |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Comptent les demandes dans 10,000s | Demandes de service BLOB (dans 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Données de pénétration dans Go          | Entrée de données de service BLOB en Go 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress en Go              | Sortie de données de service BLOB en Go 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Comptent les demandes dans 10,000s   | File d’attente des demandes de service (dans 10,000s) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Données de pénétration dans Go         | Entrée de données de service de file d’attente en Go 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress en Go  | Sortie de données de service de file d’attente en Go 
| **Cluster** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | Machine virtuelle taille heures | Taille de la machine virtuelle |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Comment les API de l’utilisation de pile Azure par rapport à l' [API de l’utilisation de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (actuellement en version d’évaluation) ?

-   L’API de l’utilisation de client est cohérente avec l’API Azure, avec une exception : l’indicateur *showDetails* actuellement n'est pas pris en charge dans une pile Azure.

-   L’API de l’utilisation de fournisseur s’applique uniquement aux pile Azure.

-   Pour l’instant, l' [API RateCard](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) qui est disponible dans Azure n’est pas disponible dans une pile Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Quelle est la différence entre signalés heure et l’utilisation ?

Rapports d’utilisation des données ont deux valeurs de temps principale :

-   **Le temps déclaré**. Lors de l’événement de l’utilisation saisie le système de l’utilisation

-   **Temps d’utilisation**. Le temps lorsque la ressource Azure pile a été consommée

Un écart dans les valeurs de temps d’utilisation et l’heure signalés peut s’afficher pour un événement de l’utilisation spécifique. Le délai peut être aussi long que plusieurs heures dans n’importe quel environnement.

Pour l’instant, vous pouvez interroger *uniquement par heure signalés*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Que signifient les codes d’erreur API de l’utilisation ?

| **Code d’état HTTP** | **Code d’erreur** | **Description** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Demande 400/incorrecte        | *NoApiVersion*     | Le paramètre de requête de *version de l’api* est manquant.
| Demande 400/incorrecte        | *InvalidProperty*  | Une propriété est manquante ou comporte une valeur non valide. Le message dans le code d’erreur dans le corps de réponse identifie la propriété manquante.
| Demande 400/incorrecte        | *RequestEndTimeIsInFuture*  | La valeur de *ReportedEndTime* est à l’avenir. À l’avenir les valeurs ne sont pas autorisés pour cet argument.
| Demande 400/incorrecte        | *SubscriberIdIsNotDirectTenant*    | Un appel fournisseur API utilisé un ID de l’abonnement n’est pas un client valide de l’appelant.
| Demande 400/incorrecte        | *SubscriptionIdMissingInRequest*   | L’ID de l’abonnement de l’appelant est manquant.
| Demande 400/incorrecte        | *InvalidAggregationGranularity*   | Une précision d’agrégation non valide a été demandée. Les valeurs valides sont quotidiennes et toutes les heures.
| 503                    | *ServiceUnavailable*   | Une erreur récupérable s’est produite, car le service est occupé (e) ou de l’appel est limitée. |

## <a name="next-steps"></a>Étapes suivantes
[Facturation client et refacturation dans Azure pile](azure-stack-billing-and-chargeback.md)

[Utilisation des ressources fournisseur API](azure-stack-provider-resource-api.md)

[Utilisation des ressources API du client](azure-stack-tenant-resource-usage-api.md)
