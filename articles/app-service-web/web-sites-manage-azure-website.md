<properties 
    pageTitle="Gérer une application web dans le Service d’application Azure" 
    description="Liens vers des ressources pour la gestion d’une application web dans le Service d’application Azure." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Gérer une application web dans le Service d’application Azure

Cette rubrique contient des liens vers des ressources pour la gestion d’une application web dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Gestion des inclut toutes les tâches de maintenir votre application web fonctionne correctement. 

La durée de vie d’une application web, vous allez effectuer différentes tâches de gestion, lorsque vous passez de déploiement initial mises à jour, gestion et fonctionnement normal.

Nombreuses tâches de gestion de l’application web peuvent être effectuées dans le portail Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Avant de déployer votre application web sur production

### <a name="choose-a-tier"></a>Choisissez un niveau

Azure Application Service est proposé dans cinq niveaux : libre, Shared, basique, Standard et Premium. Pour plus d’informations sur les fonctionnalités et le prix de chaque niveau, voir [informations de tarification](/pricing/details/app-service/). 

- [Plans de services d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) vous permettent de regrouper plusieurs applications web sous le même niveau.
- Vous pouvez toujours [Basculer niveaux](web-sites-scale.md) après avoir créé votre application web.

### <a name="configuration"></a>Configuration

Le [Portail Azure](https://portal.azure.com/) permet de définir différentes options de configuration. Pour plus d’informations, voir [Configuration des applications web dans le Service d’application Azure](web-sites-configure.md). Voici une liste de vérification rapide :

- Sélectionnez **versions runtime** pour .NET, PHP, Java ou Python, si nécessaire.
- Activer le **protocole WebSocket** si votre application web utilise le protocole WebSocket. (Cela inclut les applications qui utilisent [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md)).
- Vous exécutez travaux web continue ? Si c’est le cas, activez **Toujours sur**.
- Définir le **document par défaut**, tel que index.html.

Outre ces paramètres de configuration de base, vous souhaiterez peut-être effectuer les opérations suivantes :

- Chiffrement **Secure Socket Layer (SSL)** . Pour utiliser SSL avec un nom de domaine personnalisé, vous devez obtenir un certificat SSL et configurer votre application web pour l’utiliser. Voir [Activer HTTPS pour une application web dans le Service d’application Azure](web-sites-configure-ssl-certificate.md).
- **Nom de domaine personnalisé.** Votre application web a automatiquement un sous-domaine sous azurewebsites.net. Vous pouvez associer un nom de domaine personnalisé, tel que contoso.com. Consultez [configurer un nom de domaine personnalisé dans le Service d’application Azure](web-sites-custom-domain-name.md).

Configuration spécifiques à une langue :

- **PHP**: [configurer PHP dans Azure Application Service Web Apps](web-sites-php-configure.md).
- **Python**: [configuration Python avec Azure Application Service Web Apps](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Pendant l’exécution de votre application web

Pendant l’exécution de votre application web, vous voulez vous assurer qu’il n’est disponible et qu’elle s’adapte au trafic des utilisateurs. Vous devrez également résoudre les erreurs.

### <a name="monitoring"></a>Surveillance des mots clés

- À partir du portail Azure, vous pouvez [Ajouter des indicateurs de performance](web-sites-monitor.md) tels que l’UC et le nombre de demandes des clients.
- [Échelle de votre application web](web-sites-scale.md) en réponse au trafic. Selon votre niveau, vous pouvez ajuster le nombre de machines virtuelles et/ou la taille des instances machine virtuelle. Dans les couches Standard et Premium, vous pouvez également configurer autoscaling, afin que votre application web est automatiquement, redimensionnée selon une planification fixe ou en réponse à charger.  
 
### <a name="backups"></a>Effectuer des sauvegardes régulières

- Configurer [des sauvegardes automatiques](web-sites-backup.md) de votre application web. En savoir plus sur les sauvegardes dans [cette vidéo](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Découvrez les options pour la [récupération de base de données](../sql-database/sql-database-business-continuity.md) dans la base de données SQL Azure.

### <a name="troubleshooting"></a>Résolution des problèmes

- Si un problème survient, vous pouvez [résoudre les problèmes dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), à l’aide des journaux de diagnostics et de débogage en direct dans le cloud. 
- En dehors de Visual Studio, il existe différentes façons pour collecter les journaux de diagnostic. Consultez [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](web-sites-enable-diagnostic-log.md).
- Pour les applications Node.js, consultez [le débogage d’une application web Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Restauration de données

- [Restaurer](web-sites-restore.md) une application web qui a été précédemment sauvegardée.


## <a name="when-you-update-your-web-app"></a>Lorsque vous mettez à jour votre application web

Si vous n’avez pas activé les sauvegardes automatiques, vous pouvez créer une [sauvegarde manuelle](web-sites-backup.md).

Envisagez d’utiliser le [déploiement par étapes](web-sites-staged-publishing.md). Cette option vous permet de publier des mises à jour sur un déploiement intermédiaire qui s’exécute côte à côte avec votre déploiement en production. 

Si vous utilisez Visual Studio Team Services, vous pouvez configurer déploiement continu à partir de contrôle de code source :

- [À l’aide du contrôle de Version Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [À l’aide de Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
