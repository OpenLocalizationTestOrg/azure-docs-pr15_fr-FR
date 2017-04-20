<properties 
   pageTitle="Surveiller l’utilisation et les statistiques dans un service de recherche Azure | Microsoft Azure | Service de recherche cloud hébergé" 
   description="Suivre la taille de la consommation et index de la ressource de recherche Azure, un service de recherche cloud hébergés sur Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Surveiller l’utilisation et les statistiques dans un service de recherche d’Azure

Suivi de la croissance d’index et de taille du document peut vous aider à ajuster le fait la capacité avant de cliquer sur la limite supérieure que vous avez défini pour votre service. 

Pour contrôler l’utilisation des ressources, nombre ainsi que les statistiques de votre service sont affichées facilement dans le [Portail Azure](https://portal.azure.com), mais vous pouvez également obtenir les informations par programme si vous créez un outil d’administration de service personnalisé. Cet article décrit les étapes pour ces deux techniques.

Vous pouvez également utiliser la nouvelle fonctionnalité d’analytique le trafic de recherche pour une vue d’ensemble de l’activité au niveau de l’index. Visitez [Analytique le trafic de recherche pour la recherche Azure](search-traffic-analytics.md) pour commencer.

##<a name="view-counts-and-metrics-in-the-portal"></a>Afficher des nombres et indicateurs dans le portail 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Ouvrez le tableau de bord de service de votre service de recherche Azure. Vous trouverez des vignettes du service sur la page d’accueil, ou vous pouvez passer au service de Parcourir dans la JumpBar. Voir [Création d’un service](search-create-service-portal.md) pour obtenir des instructions étape par étape.

La section Utilisation comprend un compteur qui vous indique quelle partie de ressources disponibles sont actuellement en cours d’utilisation.

  ![][1]

Rappeler que le service partagé avec un maximum d’un réplica et partition chacun. Par ailleurs, il peut prendre uniquement en charge 10 000 documents dans total ou 50 Mo de données, selon ce qui vient en premier.

##<a name="get-index-statistics-using-the-rest-api"></a>Obtenir les statistiques d’index à l’aide de l’API REST

L’API REST de recherche Azure et le Kit de développement .NET fournissent un accès par programme aux métriques de service.  Si vous utilisez [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) charger un index à partir de la base de données SQL Azure ou DocumentDB, une API supplémentaire est disponible pour obtenir les nombres que vous avez besoin. 

  + [Obtenir les statistiques d’Index](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Nombre de Documents](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obtenir le statut de l’indexation](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Étapes suivantes

Passez en revue [limites et à la capacité](search-limits-quotas-capacity.md) afin de déterminer la combinaison des partitions et duplications que vous devez si capacités existantes sont insuffisante. 

Pour plus d’informations sur l’administration du service, consultez [Gérer votre service de recherche sur Microsoft Azure](search-manage.md) .

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
