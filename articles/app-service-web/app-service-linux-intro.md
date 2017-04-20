<properties 
    pageTitle="Présentation du Service d’application sur Linux | Microsoft Azure" 
    description="Découvrez les services d’application sur Linux." 
    keywords="service d’application Azure, linux, systèmes d’exploitation"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Présentation du Service d’application sous Linux
Service d’application sur Linux est actuellement dans la version d’évaluation et prend en charge des applications web en cours d’exécution en mode natif sur Linux. 

## <a name="overview"></a>Vue d’ensemble ##
Clients application Service permet sur Linux hôte web apps en mode natif sur Linux pour piles d’applications pris en charge. La section fonctionnalités suivante répertorie les piles application actuellement prises en charge.

## <a name="features"></a>Fonctionnalités ##
Service d’application sur Linux prend actuellement en charge les piles application suivante

- Node.js
- PHP

Les clients peuvent déployer leurs applications à l’aide

- FTP.
- Git local.
- GitHub ou BitBucket.

Pour suivre l’évolution de l’application


- Clients peuvent faire évoluer leur application web monter et descendre en modifiant le niveau dans leur Plan de Service d’application. 
- Clients peuvent évoluer leurs applications arrière et exécuter son application sur plusieurs instances dans les limites de leur référence (SKU).

Pour Kudu certaines des fonctionnalités de base sont prises en charge

- Environnement.
- Déploiements.
- Console Visual Basic.

## <a name="limitations"></a>Limitations ##

Le portail de gestion Azure affiche des fonctionnalités actuellement prises en charge pour le Service d’application sur Linux uniquement et masquer le reste. En tant que notre équipe l’activation des fonctionnalités plus nous sera conserver la réflexion cela sur le portail de gestion. Certaines fonctionnalités telles que l’intégration VNET et AAD / d’authentification tiers ou extensions de site Kudu ne fonctionnent pas actuellement. Mais à mesure que nous ces utilisation nous mettra à jour notre documentation et le blog sur les modifications.

Cette version d’évaluation n’est actuellement disponible dans les régions suivantes

-   États-Unis Ouest.
-   Europe ouest.
-   Pays d’Asie du Sud-est.

Application Web sur Linux est uniquement prise en charge dans dédié application Service Plans et n’a pas un niveau libre ou partagé. En outre, les offres de service d’application pour standard et Linux web apps sont excluent mutuellement, vous ne pouvez pas créer une application web Linux dans un plan de services d’application non Linux.

Dans le navigateur sur Linux doit être créé dans un groupe de ressources qui ne contient-elle pas non Linux web apps dans la même région.

En raison de l’absence de recyclage avec chevauchement des applications web, les clients doivent s’attendre qu'un temps d’arrêt petite en cas d’une application web avez redémarré. 

## <a name="next-steps"></a>Étapes suivantes ##

Suivez les liens suivants pour commencer à utiliser le Service d’application sur Linux. Publiez questions et remarques sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Création d’applications Web dans le Service d’application sous Linux](./app-service-linux-how-to-create-a-web-app.md)
* [À l’aide de la Configuration de PM2 pour Node.js dans les applications Web sous Linux](./app-service-linux-using-nodejs-pm2.md)

