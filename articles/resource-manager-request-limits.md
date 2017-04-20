<properties
   pageTitle="Limites de requête Gestionnaire de ressources Azure | Microsoft Azure"
   description="Décrit comment utiliser la limitation ayant des demandes d’Azure le Gestionnaire de ressources lorsque vous avez atteint la limite d’abonnements."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>La limitation de requêtes du Gestionnaire de ressources

Pour chaque abonnement et le client, le Gestionnaire de ressources limites requêtes de lecture à 15 000 par heure et enregistrer les requêtes à 1 200 par heure. Si votre application ou un script atteint ces limites, vous devez limiter vos demandes. Cette rubrique vous explique comment déterminer les autres demandes que vous avez avant d’atteindre la limite et comment répondre lorsque vous avez atteint la limite.

Lorsque vous atteignez la limite, vous recevez le code d’état HTTP **429 trop grand nombre de demandes**.

Le nombre de requêtes portée est limitée à votre abonnement ou votre client. Si vous avez plusieurs, applications simultanées effectuent des demandes dans votre abonnement, les demandes de ces applications sont ajoutés ensemble afin de déterminer le nombre de requêtes restantes.

Demandes d’abonnement portée sont ceux que l’Impliquez passant votre id de l’abonnement, telles que l’extraction de la ressource des groupes dans votre abonnement. Demandes de client portée n’incluent pas votre id de l’abonnement, telles que la récupération des emplacements Azure valides.

## <a name="remaining-requests"></a>Demandes restants

Vous pouvez déterminer le nombre de requêtes restants en examinant les en-têtes de réponse. Chaque demande contient des valeurs pour le nombre de demandes d’écriture et restant en lecture. Le tableau suivant décrit les en-têtes de réponse que vous pouvez examiner ces valeurs :

| En-tête de réponse | Description |
| --------------- | ----------- |
| x-MS-ratelimit-Remaining-Subscription-reads | Abonnement portée lit restant |
| x-MS-ratelimit-Remaining-Subscription-Writes | Abonnement portée écrit restant |
| x-MS-ratelimit-Remaining-tenant-reads | Client portée lit restant |
| x-MS-ratelimit-Remaining-tenant-Writes | Client portée écrit restant |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests | Abonnement dans l’étendue des demandes de type de ressource restant.<br /><br />Cette valeur de l’en-tête est renvoyée uniquement si un service a remplacé la limite par défaut. Gestionnaire de ressources permet d’ajouter cette valeur au lieu de l’abonnement lectures ou d’écriture. |
| x-MS-ratelimit-Remaining-Subscription-Resource-Entities-Read | Abonnement adaptées à des demandes de collecte de type de ressource restant.<br /><br />Cette valeur de l’en-tête est renvoyée uniquement si un service a remplacé la limite par défaut. Cette valeur fournit le nombre de demandes de collecte des restants (ressources de liste). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests | Demandes de type de ressources client portée restant.<br /><br />Cet en-tête est ajouté uniquement pour les demandes au niveau client et uniquement si un service a remplacé la limite par défaut. Gestionnaire de ressources permet d’ajouter cette valeur au lieu du client lectures ou écritures. |
| x-MS-ratelimit-Remaining-tenant-Resource-Entities-Read | Client adaptées à des demandes de collecte de type de ressource restant.<br /><br />Cet en-tête est ajouté uniquement pour les demandes au niveau client et uniquement si un service a remplacé la limite par défaut. |

## <a name="retrieving-the-header-values"></a>Récupérer les valeurs d’en-tête

Extraction de ces valeurs d’en-tête dans votre code ou script n’est pas différente de récupération de n’importe quelle valeur d’en-tête. 

Par exemple, en **c#**, vous retrouver la valeur d’en-tête d’un objet **HttpWebResponse** appelé **réponse** par le code suivant :

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

Dans **PowerShell**, récupérer la valeur d’en-tête à partir d’une opération appeler WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
Ou, si voulez voir les autres demandes pour le débogage, vous pouvez fournir la **-Déboguer** paramètre sur votre applet de commande **PowerShell** .

    Get-AzureRmResourceGroup -Debug
    
Qui retourne un grand nombre d’informations, y compris la valeur de réponse suivants :

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

Dans **Azure infrastructure du langage commun**, récupérer la valeur d’en-tête à l’aide de l’option plus détaillée.

    azure group list -vv --json

Qui retourne un grand nombre d’informations, y compris l’objet suivant :

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>En attente avant d’envoyer la demande suivante

Lorsque vous atteignez la limite de demande, le Gestionnaire de ressources renvoie le code d’état **429** HTTP et une **Nouvelle tentative après** valeur dans l’en-tête. La **Nouvelle tentative après** valeur spécifie le nombre de secondes d’attente de votre application (ou mise en veille) avant d’envoyer la demande suivante. Si vous envoyez une demande avant que la valeur de tentatives écoulée, votre demande n’est pas traitée et une nouvelle valeur de nouvelles tentatives est renvoyée.
