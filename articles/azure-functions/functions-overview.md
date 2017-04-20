<properties
   pageTitle="Azure fonctionne présentation | Microsoft Azure"
   description="Comprendre l’utilisation des fonctions Azure pour optimiser les charges de travail asynchrones en minutes."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, cluster dynamique, architecture sans serveur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Vue d’ensemble des fonctions Azure

Fonctions Azure est une solution pour l’exécution facilement des petits fragments de code, ou « fonctions », dans le cloud. Vous pouvez écrire simplement le code que vous avez besoin pour le problème à portée de main, sans vous soucier d’un ensemble de l’application ou de l’infrastructure pour l’exécuter. Cela peut rendre le développement encore plus productif, et vous pouvez utiliser votre langage de développement de choix, tels que c#, F #, Node.js, Python ou PHP. Payer uniquement pour la gestion de la confidentialité Azure et l’heure de que votre code s’exécute à l’échelle selon vos besoins.

Cette rubrique fournit une vue d’ensemble des fonctions Azure. Si vous souhaitez travailler directement dans et commencer à utiliser les fonctions Azure, commencer par [créer votre première fonction Azure](functions-create-first-azure-function.md). Si vous recherchez des informations techniques sur les fonctions, voir la [référence du développeur](functions-reference.md).

## <a name="features"></a>Fonctionnalités

Voici certaines fonctionnalités clés des fonctions Azure :
    
* **Choix d’une langue** - fonctions écriture à l’aide de c#, F #, Node.js, Python, PHP, lot, bash, Java ou n’importe quel exécutable.
* **Modèle de tarification facturables-utilisation** - rémunération uniquement pour le temps d’exécution de votre code. Consultez l’option Plan de Service application dynamique dans la [section de tarification](#pricing) ci-dessous.  
* **Importer vos propres dépendances** - fonctions prend en charge NuGet et NPM, afin d’utiliser vos bibliothèques favoris.  
* **Sécurité intégrée** - fonctions qui s’affiche suite HTTP protéger avec les fournisseurs de OAuth tels que Azure Active Directory Facebook, Google, Twitter et Account Microsoft.  
* **Intégration simplifié** - facilement exploiter services Azure et les offres de logiciel-as-a-service (SaaS). Consultez la [section intégrations](#integrations) ci-dessous pour obtenir des exemples.  
* **Développement flexible** - votre directement des fonctions dans le portail de Code ou configurer l’intégration continue et déployer votre code via GitHub, Visual Studio Team Services et d’autres [Outils de développement de prise en charge](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Open source** - fonctions runtime est l’open source et [disponible sur GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Que puis-je faire avec les fonctions ?

Fonctions Azure est une excellente solution pour traiter des données, intégration des systèmes, utilisation de l’internet-de-objets (IoT) et la création des API simples et microservices. Envisagez de fonctions pour des tâches comme image ou un ordre de traitement, gestion des fichiers et des tâches de longue que vous voulez exécuter dans un thread d’arrière-plan, ou pour toutes les tâches que vous voulez exécuter sur un planning de. 

Fonctions fournit des modèles pour vous aider à démarrer avec des scénarios clés, notamment les suivantes :

* **BlobTrigger** - des objets BLOB Azure processus Storage lorsqu’ils sont ajoutés aux conteneurs. Vous pouvez utiliser ce de redimensionnement de l’image.
* **EventHubTrigger** - répondre aux événements remis à un concentrateur événement Azure. Particulièrement utile dans les scénarios Internet des objets (IoT), traitement des flux de travail ou expérience utilisateur et instrumentation de l’application.
* **Webhook générique** - processus webhook HTTP demande à partir de n’importe quel service qui prend en charge webhooks.
* **GitHub webhook** - répondre aux événements qui se produisent dans vos référentiels GitHub. Pour obtenir un exemple, voir [créer un webhook ou fonction API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - déclencher l’exécution de votre code à l’aide d’une requête HTTP.
* **QueueTrigger** - répondre à des messages entrants dans une file d’attente de stockage Azure. Pour obtenir un exemple, voir [créer une fonction d’Azure qui est lié à un service Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - connecter votre code à d’autres services Azure ou services sur site par écoutez files d’attente. 
* **ServiceBusTopicTrigger** - connecter votre code à d’autres services Azure ou services sur site en vous abonnant aux rubriques. 
* **TimerTrigger** - exécuter nettoyage de disque ou d’autres tâches de lot sur une planification prédéfinie. Pour obtenir un exemple, voir [créer une fonction de traitement des événements](functions-create-an-event-processing-function.md).

Fonctions Azure prend en charge *déclencheurs*, qui sont des façons de démarrer l’exécution de votre code et les *liaisons*, qui sont des méthodes de simplification de codage pour les données d’entrée et de sortie. Pour une description détaillée des déclencheurs et des liaisons qui fournit des fonctions Azure, voir [fonctions Azure déclencheurs et référence du développeur liaisons](functions-triggers-bindings.md).


## <a name="integrations"></a>Intégrations d’entreprise

Fonctions Azure s’intègre à divers Azure et services 3ème tiers. Vous pouvez utiliser ces pour déclencher votre fonction, démarrer l’exécution ou pour servir d’entrée et de sortie pour votre code. Les intégrations service suivantes sont prises en charge par les fonctions Azure. 

* DocumentDB Azure
* Événement Azure Hubs 
* Applications Mobile Azure (de tables)
* Notification Azure Hubs
* Azure Bus des services (files d’attente et rubriques)
* Stockage Azure (blob, files d’attente et des tables) 
* GitHub (webhooks)
* Local (à l’aide de Bus des services)

## <a name="pricing"></a>Combien coûte fonctionne coût ?

Fonctions Azure a deux sortes de plans, choisissez celui qui vous convient le mieux à vos besoins : 

* **Plan d’hébergement dynamique** - lors de l’exécution de votre fonction, Azure fournit toutes les ressources informatiques nécessaires. Vous n’avez pas à vous soucier de la gestion des ressources, et vous payez uniquement pour le temps que votre code s’exécute. Complètes pour plus d’informations sont disponibles dans la [page de fonctions des prix](/pricing/details/functions). 

* **Plan de services d’application** - d’exécuter votre fonctions comme votre web, mobile et les applications de l’API. Lorsque vous utilisez déjà l’application de Service pour les autres applications, vous pouvez exécuter votre fonctions sur la même offre sans frais supplémentaires. Vous trouverez tous les détails dans la [page Application Service tarifs](/pricing/details/app-service/).

Pour plus d’informations sur les fonctions de mise à l’échelle, Découvrez [comment mettre à l’échelle des fonctions Azure](functions-scale.md).

##<a name="next-steps"></a>Étapes suivantes

+ [Créer votre première fonction Azure](functions-create-first-azure-function.md)  
Créer votre première fonction à l’aide de démarrage rapide pour les fonctions Azure et lancez-vous dans. 
+ [Référence du développeur fonctions Azure](functions-reference.md)  
Fournit des informations techniques sur le runtime Azure fonctions et une référence pour codage de fonctions et la définition des déclencheurs et des liaisons.
+ [Test des fonctions Azure](functions-test-a-function.md)  
Décrit les différents outils et techniques pour tester vos fonctions.
+ [Comment mettre à l’échelle des fonctions Azure](functions-scale.md)  
Traite des offres de service disponibles avec les fonctions Azure, y compris le plan de services dynamique et comment choisir l’offre de droite. 
+ [Pour plus d’informations sur le Service d’application Azure](../app-service/app-service-value-prop-what-is.md)  
Fonctions Azure s’appuie sur la plateforme Azure Application Service pour les fonctionnalités principales, tels que les déploiements, variables d’environnement et diagnostics. 