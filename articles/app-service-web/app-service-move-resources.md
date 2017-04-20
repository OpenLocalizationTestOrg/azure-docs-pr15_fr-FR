<properties
    pageTitle="Déplacer des ressources de l’application Web vers un autre groupe de ressources"
    description="Décrit les scénarios où vous pouvez déplacer des applications Web et Services d’application à partir d’un groupe de ressources à l’autre."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Configurations de transfert pris en charge

Vous pouvez déplacer des ressources Azure Web App à l’aide de l' [Api de ressources processeur déplacer](../resource-group-move-resources.md).

Applications Web Azure prend actuellement en charge les scénarios déplacer suivants :

* Déplacement de tout le contenu d’un groupe de ressources (applications web, offres de service d’application et les certificats) vers un autre groupe de ressources 
    * Remarque : Le groupe de ressources de destination peut contenir pas toutes les ressources Microsoft.Web dans ce scénario
* Déplacement des applications web individuelles à un groupe de ressources différent, tout en les hébergeant dans leur plan de services application active (le plan de services d’application reste dans le groupe de ressources ancien)
