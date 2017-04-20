<properties
    pageTitle="Utilisation des ressources fournisseur API | Microsoft Azure"
    description="Référence pour l’utilisation des ressources API, ce qui récupérer des informations sur l’utilisation de pile Azure."
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

# <a name="provider-resource-usage-api"></a>Utilisation des ressources fournisseur API

Le fournisseur de magasin de termes s’applique à l’administrateur de service et à tous les fournisseurs déléguées. Les administrateurs de service et fournisseurs déléguées peuvent utiliser l’API de l’utilisation du fournisseur pour afficher l’utilisation de leurs clients directs. Par exemple, P0 peuvent appeler l’API fournisseur pour obtenir des informations d’utilisation sur l’utilisation de P1 et de P2 direct et P1 peuvent appeler pour obtenir des informations sur P3 et P4.

![Modèle conceptuel de la hiérarchie de fournisseur](media/azure-stack-provider-resource-api/image1.png)


## <a name="api-call-reference"></a>Référence de l’appel API

### <a name="request"></a>Demande

La demande Obtient des détails de consommation pour les abonnements demandées et pour la période demandée. Il n’existe aucun corps de la requête.

Cette utilisation API est une API fournisseur, afin de l’appelant doit être affectée à un rôle propriétaire, collaborateur ou un lecteur d’abonnement du fournisseur.

| **Méthode**  | **URI de la demande** |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|  Télécharger        | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Arguments

| **Argument**              | **Description** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *armendpoint*             | Azure extrémité Gestionnaire de ressources de votre environnement pile Azure. La convention Azure pile est que le nom du point de terminaison processeur se trouve dans le format https://api. {nom de domaine} ». Par exemple, si le nom de domaine est azurestack.local, le point de terminaison processeur sera https://api.azurestack.local. |
| *subId*                   | ID de l’abonnement de l’utilisateur qui effectue l’appel. |
| *reportedStartTime*       | Heure de début de la requête. La valeur de *date/heure* doit être au format UTC et au début de l’heure, par exemple, 13:00. Pour l’agrégation quotidienne, définissez cette valeur sur UTC minuit. Le format est *une séquence d’échappement* ISO8601, par exemple, 2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z, où deux-points sont désactivé par 3 % et plus est désactivé par % 2 b afin qu’il soit URI convivial. |
| *reportedEndTime*         | Heure de fin de la requête. Les contraintes qui s’appliquent à *reportedStartTime* s’appliquent également à cet argument. La valeur de *reportedEndTime* ne peut pas être à l’avenir. |
| *aggregationGranularity*  | Paramètre facultatif qui comporte deux valeurs potentielles discrètes : tous les jours et horaires. Comme les valeurs suggérant, une renvoie les données de précision quotidienne, et l’autre est une résolution horaire. L’option quotidien est la valeur par défaut. |
| *subscriberId*            | ID d’abonnement. Pour obtenir les données filtrées, l’ID de l’abonnement d’un client direct du fournisseur est requis. Si aucun paramètre ID abonnement n’est spécifié, l’appel renvoie les données d’utilisation pour clients direct tous les du fournisseur. |
| *version de l’API*             | Version du protocole qui est utilisé pour effectuer cette demande. Vous devez utiliser 2015-06-01-aperçu. |
| *continuationToken*       | Jeton récupéré au dernier appel au fournisseur de l’API de l’utilisation. Cela est nécessaire lorsqu’une réponse est supérieure à 1 000 lignes. Il s’agit le signet de l’avancement. Si celui-ci est absent, les données soient récupérées à partir du début du jour ou heure, en fonction de la précision passé. |



### <a name="response"></a>Réponse

OBTENIR /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00 % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity = quotidienne & subscriberId = sub1.1 & version de l’api = 1.0

{

« valeur » :\[

{

« id » : » /subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1 »,

« nom » : « sub1.1-meterID1 »

« type » : « Microsoft.Commerce/UsageAggregate »

« propriétés » : {}

« subscriptionId » : « sub1.1 »

« usageStartTime » : « 2015-03-03T00:00:00 + 00:00 »,

« usageEndTime » : « 2015-03-04T00:00:00 + 00:00 »,

« instanceData » : « {\\« Microsoft.Resources\\» : {\\« ResourceURI des services Web\\» :\\» resourceUri1\\»,\\emplacement »\\

« :\\« Alaska\\»,\\« balises\\» : null,\\« additionalInfo\\» : null}} »,

:2.4000000000 « quantité »,

« meterId » : « meterID1 »

}

},

…

### <a name="response-details"></a>Détails de la réponse


| **Argument**       | **Description**
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *ID*               | Nº unique de l’agrégat de l’utilisation
| *nom*             | Nom de l’agrégat de l’utilisation
| *type*             | Définition de la ressource
| *subscriptionId*   | Identificateur d’abonnement de l’utilisateur Azure pile
| *usageStartTime*   | UTC heure de début de la plage de l’utilisation auquel appartient cet agrégat l’utilisation
| *usageEndTime*     | Heure de fin UTC de la plage de l’utilisation auquel appartient cet agrégat l’utilisation
| *instanceData*     | Paires clé-valeur de détails de l’instance (dans un nouveau format) :<br> *resourceUri des services Web*: pleinement qualifiée Nº ressource, qui inclut les groupes de ressources et le nom de l’instance <br> *emplacement*: région dans lequel ce service a été exécuté <br> *balises*: balises de ressources qui sont spécifiées par l’utilisateur <br> *additionalInfo*: plus d’informations sur la ressource est utilisée, par exemple, type d’image ou version du système d’exploitation |
| *quantité*         | Montant de la consommation des ressources qui se sont produites dans cet intervalle de temps |
| *meterId*          | Nº unique pour la ressource qui a été consommées (également appelés *ResourceID*) |

## <a name="next-steps"></a>Étapes suivantes

[Utilisation des ressources client référence de l’API](azure-stack-tenant-resource-usage-api.md)

[Forum aux questions sur liés à l’utilisation](azure-stack-usage-related-faq.md)
