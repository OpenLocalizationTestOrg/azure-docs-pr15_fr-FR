<properties
   pageTitle="Vue d’ensemble du Cache Local application Service Azure | Microsoft Azure"
   description="Cet article explique comment activer, redimensionner et l’état de la fonctionnalité de Cache Local Azure Application Service de requête"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# <a name="azure-app-service-local-cache-overview"></a>Vue d’ensemble du Cache Local application Service Azure

Contenu de l’application web Azure est stocké sur le stockage Azure et est exposé vers le haut d’une manière résistant comme un partage de contenu. Ce modèle est conçu pour fonctionner avec un éventail d’applications et possède les attributs suivants :  

* Le contenu est partagé par plusieurs instances de machine virtuelle () de l’application web.
* Le contenu est résistant et peut être modifié en exécutant des applications web.
* Les fichiers journaux et les fichiers de données de diagnostic sont disponibles sous le même dossier contenu partagé.
* Publication de nouveaux contenus directement met à jour le dossier de contenu. Vous pouvez consulter immédiatement le même contenu par le biais du site Web SCM et l’application web en cours d’exécution (généralement quelques technologies d’assistance comme ASP.NET initier un redémarrage de l’application web sur certaines modifications de fichier pour obtenir les dernières modifications).

Alors que plusieurs applications web utilisent un ou l’ensemble de ces fonctionnalités, certaines applications web simplement besoin d’une banque de contenu High performance et en lecture seule qu’il peut effectuer à partir de haute disponibilité. Ces applications peuvent bénéficier d’une instance de machine virtuelle d’un cache local spécifique.

La fonctionnalité de Cache Local Azure Application Service fournit une vue de rôle web de votre contenu. Ce contenu est un cache d’écriture-mais-ignorer votre du contenu de stockage qui est créé de façon asynchrone au démarrage de site. Lorsque le cache est prêt, le site est modifiée pour s’exécuter sur le contenu mis en cache. Applications Web qui s’exécutent sur Cache Local ont les avantages suivants :

* Ils sont protégés au latence qui se produire lorsqu’ils accèdent à contenu sur le stockage Azure.
* Ils sont protégés pour les mises à niveau planifiées ou non planifiées comptez-vous et les autres interruptions avec stockage Azure qui se produisent sur des serveurs assurant le partage de contenu.
* Ils ont moins le redémarrage de l’application en raison de changements de partage de stockage.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Comment le Cache Local modifie le comportement du Service d’application

* Le cache local est une copie des dossiers /site et /siteextensions de l’application web. Il est créé dans l’instance locale de la machine virtuelle au démarrage de l’application web. La taille de la mémoire cache locale par application web est limitée à 300 Mo par défaut, mais vous pouvez l’augmenter jusqu'à 1 Go.
* Le cache local est en lecture / écriture. Toutefois, toutes les modifications seront supprimées lors de l’application web déplace machines virtuelles ou obtient redémarrée. Vous ne devez pas utiliser Cache Local pour les applications qui stockent des données critiques dans le magasin de contenu.
* Applications Web peuvent continuer à écrire des fichiers journaux et les données de diagnostic manière actuellement. Les fichiers journaux et les données, cependant, sont stockées localement sur l’ordinateur virtuel. Puis ils sont copiées régulièrement dans le magasin de contenu partagé. La copie dans le magasin de contenu partagé est un effort préférentiel--écriture sauvegarde peut être perdus dû à un blocage soudain d’une instance de machine virtuelle.
* Il existe une modification dans l’arborescence de dossiers de fichiers journaux et les données pour les applications web qui utilisent le Cache Local. Il existe désormais des sous-dossiers dans les dossiers de fichiers journaux et les données de stockage qui suivent le modèle d’attribution de noms de « identificateur unique » + horodatage. Chacun des sous-dossiers correspond à une instance de machine virtuelle dans lequel l’application web est en cours d’exécution ou a été exécuté.  
* Publication des modifications à l’application web via une des méthodes de publication seront publiée dans le magasin de contenu partagé. Il s’agit par la conception, car nous où le contenu publié résistant. Pour actualiser le cache local de l’application web, elle doit être redémarré. Cela semble comme une étape de trop ? Pour rendre le cycle de vie transparente, consultez les informations plus loin dans cet article.
* D:\Home pointera vers le cache local. D:\Local continuera en pointant sur le stockage spécifique machine virtuelle temporaire.
* L’affichage du contenu par défaut du site SCM continuent à celle de la banque de contenu partagée.

## <a name="enable-local-cache-in-app-service"></a>Activer le Cache Local dans le Service d’application

Vous configurez Cache Local en utilisant une combinaison de paramètres de l’application réservés. Vous pouvez configurer ces paramètres de l’application à l’aide des méthodes suivantes :

* [Portail Azure](#Configure-Local-Cache-Portal)
* [Gestionnaire de ressources Azure](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurer le Cache Local à l’aide du portail Azure
<a name="Configure-Local-Cache-Portal"></a>

Activer le Cache Local sur une base de l’application web à l’aide de ce paramètre d’application :`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Paramètres de l’application portail Azure : Cache Local](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurer le Cache Local à l’aide du Gestionnaire de ressources Azure
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Changez le paramètre de taille de Cache Local

Par défaut, la taille de cache local est **300 Mo**. Cela inclut les /site et /siteextensions dossiers copiés à partir de la banque de contenu, ainsi que les dossiers de journaux et de données créés en local. Pour augmenter la taille limite, utilisez le paramètre application `WEBSITE_LOCAL_CACHE_SIZEINMB`. Vous pouvez augmenter la taille jusqu'à **1 Go** (1 000 Mo) par application web.

## <a name="best-practices-for-using-app-service-local-cache"></a>Recommandations d’utilisation application Service de Cache Local

Nous vous recommandons d’utiliser Cache Local conjointement avec la fonctionnalité [Environnements de reclassement](../app-service-web/web-sites-staged-publishing.md) .

* Ajouter le paramètre application _pense-bête_ `WEBSITE_LOCAL_CACHE_OPTION` avec la valeur `Always` à votre emplacement de **Production** . Si vous utilisez `WEBSITE_LOCAL_CACHE_SIZEINMB`, également ajoutez-la comme un paramètre à auto-maintien pour votre emplacement de Production.
* Créer un emplacement de **mise en attente** et publier dans votre emplacement de mise en attente. Vous ne généralement définir l’emplacement de mise en attente à utiliser Cache Local pour activer un cycle de vie génération-déploiement-test transparente pour les intermédiaires si vous obtenez les avantages de la mémoire Cache locale pour l’emplacement de production.
*   Tester votre site par rapport à votre emplacement de mise en attente.  
*   Lorsque vous êtes prêt, lancez une [opération de permutation](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) entre vos connecteurs intermédiaires et de Production.  
*   Paramètres du pense-bête incluent nom et pense-bête vers un emplacement. Donc lorsque l’emplacement de mise en attente obtient échangé en Production, il sera héritent des paramètres d’application Cache Local. L’emplacement de Production nouvellement échangez fonctionnera par rapport à la mémoire cache locale après quelques minutes et sera être préparer dans le cadre de préparation emplacement après permutation. Donc lorsque le remplacement emplacement est terminé, votre emplacement de Production exécutera par rapport à la mémoire cache locale.

## <a name="frequently-asked-questions-faq"></a>Forum aux questions (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Comment puis-je savoir si le Cache Local s’applique à mon application web ?

Si votre application web a besoin d’une banque de contenu High performance, fiable, n’utilise pas le magasin de contenu pour écrire des données critiques en cours d’exécution et taille total est inférieur à 1 Go, la réponse est « Oui » ! Pour obtenir la taille totale de vos dossiers /site et /siteextensions, vous pouvez utiliser l’extension de site « Utilisation du disque applications Azure Web ».  

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Comment puis-je savoir si mon site est passé à l’aide de Cache Local ?

Si vous utilisez la fonctionnalité de Cache Local aux environnements de mise en attente, l’opération de permutation ne sera pas terminée jusqu'à ce que le Cache Local est mis en température vers le haut. Pour vérifier si votre site est en cours d’exécution par rapport à la mémoire Cache locale, vous pouvez vérifier la variable d’environnement processus collaborateur `WEBSITE_LOCALCACHE_READY`. Suivez les instructions dans la page [variable d’environnement de processus de travail](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) pour accéder à la variable d’environnement processus collaborateur sur plusieurs instances.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Que j’ai publié simplement nouvelle modification, mais mon application web ne semble pas demandez-leur. Pourquoi ?

Si votre application web utilise le Cache Local, vous devez redémarrer votre site pour obtenir les dernières modifications. Ne voulez pas publier les modifications apportées à un site de production ? Consultez les options d’emplacement dans la section des pratiques recommandées précédent.

### <a name="where-are-my-logs"></a>Où se trouvent les journaux de mon ?

Avec Cache Local, les journaux et les dossiers de données ressemblent légèrement différentes. Toutefois, la structure de vos sous-dossiers reste la même, sauf que les sous-dossiers sont imbriqués sous un sous-dossier avec la mise en forme « machine virtuelle identificateur unique » + horodatage.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>J’ai Cache Local activé, mais mon application web obtient toujours redémarrée. Pourquoi ? J’ai qu'a permis de Cache Local avec le redémarrage de l’application fréquents.

Cache local d’éviter le redémarrage de l’application web liés au stockage. Toutefois, votre application web pourrait subir toujours redémarre pendant les mises à niveau de la machine virtuelle infrastructure planifiée. Redémarrage de l’application globale que vous rencontrez avec Cache Local activé doit être un nombre moins élevé.
