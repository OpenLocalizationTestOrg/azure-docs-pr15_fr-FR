<properties
 pageTitle="Limites de planificateur et les valeurs par défaut"
 description="Limites de planificateur et les valeurs par défaut"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-limits-and-defaults"></a>Limites de planificateur et les valeurs par défaut

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Limitations, les limites, les valeurs par défaut et les Quotas de planificateur

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>L’en-tête x-ms-demande-id

Chaque demande faite auprès du service Planificateur renvoie un en-tête de réponse nommé**x-ms-demande-id**. Cet en-tête contient une valeur opaque qui identifie la demande.

Si une demande de manière cohérente à travers ignore et que vous avez vérifié que la demande est correctement préparée, vous pouvez utiliser cette valeur pour signaler l’erreur à Microsoft. Dans votre rapport, inclure la valeur de x-ms-demande-id, le temps approximatif la demande a été faite, l’identificateur de l’abonnement, tâche collecte, et/ou travail et le type d’opération qui a tenté de la demande.

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Azure Scheduler concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler Azure](scheduler-powershell-reference.md)

 [Azure Scheduler disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Authentification sortante Scheduler Azure](scheduler-outbound-authentication.md)
