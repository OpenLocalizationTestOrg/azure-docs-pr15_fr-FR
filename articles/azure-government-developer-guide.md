<properties 
    pageTitle="Guide du développeur pour le gouvernement Azure" 
    description="Cela fournit une comparaison des fonctionnalités et des recommandations sur le développement d’applications pour le gouvernement Azure" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Guide du développeur Microsoft Azure pour le gouvernement 

<p> Pour le gouvernement de Microsoft Azure est un physiquement et réseau isolé instance de Microsoft Azure.  Ce guide du développeur fournira plus d’informations sur les différences que les développeurs et les administrateurs auront besoin d’interagir et de travailler avec ces zones distinctes de Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>Dans cette rubrique


+ [Vue d’ensemble](#Overview)
+ [Conseils pour les développeurs](#Guidance)
+ [Fonctionnalités actuellement disponibles dans Microsoft Azure pour le gouvernement](#Features)
+ [Mappage de point de terminaison](#Endpoint)
+ [Étapes suivantes](#next)


## <a name="Overview"></a>Vue d’ensemble

Pour le gouvernement de Microsoft Azure est une instance distincte du service Microsoft Azure répondre aux besoins sécurité et conformité de gouvernements locales et agences fédérales américaines et leurs fournisseurs de solutions. Pour le gouvernement Azure offre physique et isolement des déploiements pour le gouvernement non-US réseau et filtré US personnel. 

Microsoft fournit plusieurs outils pour créer et déployer des applications cloud service Microsoft Azure global (« Service Global ») et les services Microsoft Azure pour le gouvernement de Microsoft.

Lorsque vous créez et déployer des applications sur les Services pour le gouvernement Azure, plutôt que d’utiliser le Service général, les développeurs doivent connaître les principales différences entre les deux services.  Configuration des points de terminaison, écrire des applications et à leur déploiement sous forme de services pour le gouvernement Azure spécifiquement autour de la configuration et la configuration de leur environnement de programmation.

Les informations contenues dans ce document récapitule les différences et les informations disponibles sur le site du(http://www.azure.com/gov "Gouvernement Azure") [Azure pour le gouvernement]et la [Bibliothèque technique de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") sur MSDN les suppléments. Informations officielles peuvent également être disponibles dans tous les autres emplacements tels que la [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ » Microsoft Azure Trust Center » /), [Centre de Documentation Azure](https://azure.microsoft.com/documentation/) et [Blogs Azure] (https://azure.microsoft.com/blog/ « Blogs Azure » /). 

Ce contenu est destiné aux partenaires et les développeurs qui déployant sur Microsoft Azure pour le gouvernement.



## <a name="Guidance"></a>Conseils pour les développeurs
Étant donné que la plupart des contenus techniques qui ne sont disponible actuellement suppose que les applications sont en cours de développement pour le Service Global plutôt que pour les administrations publiques de Microsoft Azure, il est important pour vous assurer que les développeurs aient connaissance des différences clés pour les applications développées pour être hébergé dans Azure pour le gouvernement.

- Tout d’abord, il existe des services et des différences de fonctionnalités entre, cela signifie que certaines fonctionnalités qui se trouvent dans des zones spécifiques du Service Global ne soient pas disponibles dans Azure pour le gouvernement.

- Ensuite, pour les fonctionnalités qui vous sont proposées dans Azure pour le gouvernement, il existe des différences de configuration du service Global.  Par conséquent, vous devez examiner les exemples de code, les configurations et les étapes pour vous assurer que vous êtes création et l’exécution de l’environnement de Services de Cloud pour le gouvernement Azure.


## <a name="Features"></a>Fonctionnalités actuellement disponibles dans Microsoft Azure pour le gouvernement
Pour le gouvernement Azure comporte actuellement les services suivants disponibles dans les régions IOWA nous et pour le gouvernement et nous et pour le gouvernement VIRGINIE :

- Machines virtuelles
- Services en nuage
- Espace de stockage
- Active Directory
- Planificateur
- Mise en réseau virtuelle
- Base de données SQL
- Fichiers Azure
- Services de support
- Gestionnaire de trafic
- Bus des services
- StorSimple
- Redis Cache
- Sauvegarde Azure
- Automatisation
- ExpressRoute
- etc..

D’autres services sont disponibles, et plus de services sont ajoutés en permanence.  Pour la plus récente liste des services, voir la [page de régions](https://azure.microsoft.com/regions/#services) qui met en surbrillance chaque région disponibles et les services.  

Pour l’instant, nous Iowa et pour le gouvernement et nous Virginia et pour le gouvernement sont les centres de données prise en charge pour le gouvernement Azure.  Reportez-vous à la page de régions ci-dessus pour centres de données actuel et les services disponibles.

## <a name="Endpoint"></a>Mappage de point de terminaison

Utilisez le tableau suivant pour vous aider lors du mappage des points de terminaison Microsoft Azure et base de données SQL publics aux points de terminaison spécifiques pour le gouvernement Azure.


Type de service|Public Azure|Pour le gouvernement Azure
---|---|---
Portail de gestion|Manage.windowsazure.com|Manage.windowsazure.us
Général|*. Windows.NET|*. usgovcloudapi.net
Principaux|*. core.windows.net|*. core.usgovcloudapi.net
Cluster|*. cloudapp.net|*. usgovcloudapp.net
Stockage d’objets BLOB|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Stockage de file d’attente|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Stockage de table|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Gestion des services|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
Base de données SQL|*. database.windows.net|*. database.usgovcloudapi.net
Point de terminaison avec équilibrage de charge processeur|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* Pour l’authentification processeur via Azure AD, veuillez vous reporter à [L’authentification Azure Gestionnaire de ressources demandes](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Étapes suivantes

Si vous souhaitez en savoir plus et à propos de pour le gouvernement Azure exploiter certains liens ci-dessous.

- **[S’inscrire à une version d’évaluation](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Acquisition et accéder à Azure pour le gouvernement](http://azure.com/gov)**

- **[Vue d’ensemble pour le gouvernement Azure](/azure-government-overview)**

- **[Blog Azure pour le gouvernement](http://blogs.msdn.com/b/azuregov/)**

- **[Conformité Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
