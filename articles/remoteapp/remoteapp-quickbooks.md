<properties 
    pageTitle="Déployer QuickBooks dans Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment partager QuickBooks avec Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Comment déployer QuickBooks dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Utilisez les informations suivantes pour partager QuickBooks sous forme d’une application dans Azure RemoteApp.


Vous pouvez partager QuickBooks 2015 entreprise avec Azure RemoteApp dans un environnement hybride ou le cloud collection. Le fichier d’entreprise doit se trouver sur un ordinateur virtuel exécutant QuickBooks serveur de base de données distinct à partir des serveurs RemoteApp Azure. Jamais stocker le fichier d’entreprise sur votre image Azure RemoteApp - perte de données est correctement si vous procédez comme suit. Seul QuickBooks Enterprise prend en charge hébergeant le fichier QuickBooks sur un partage externe avec le serveur de base de données QuickBooks accessible via un réseau Windows standard.   

> [AZURE.IMPORTANT] Le serveur de base de données QuickBooks qui héberge le fichier société doit se trouver sur un ordinateur virtuel distinct au sein de la même VNET que la collection RemoteApp Azure.  

## <a name="steps-to-deploy-quickbooks"></a>Étapes pour déployer QuickBooks

1. Créer une machine virtuelle Azure et installer QuickBooks, serveur de base de données de QuickBooks et placez le fichier d’entreprise sur un ordinateur virtuel Azure.  Veillez à configurer correctement les règles de pare-feu.
2. Installez QuickBooks sur une [image personnalisée](remoteapp-imageoptions.md) et créer une [collection de sites RemoteApp Azure](remoteapp-collections.md), nuage ou hybride, dans le VNET même exact où réside la machine virtuelle qui héberge le serveur de base de données QuickBooks avec les fichiers de la société. 
3.  [Publier](remoteapp-publish.md) Application QuickBooks aux utilisateurs
4.  Lancer le client hébergé Azure RemoteApp QuickBooks, accédez à l’aide de la mise en réseau Windows standard de la machine virtuelle qui héberge le serveur de base de données QuickBooks et ouvrez le fichier de société. 

## <a name="documentation-references"></a>Références de documentation

- QuickBooks [configurations prises en charge](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- QuickBooks [options de déploiement](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Vous pouvez également consulter mon Ignite présentation, [principes de base de Microsoft Azure RemoteApp gestion et d’Administration](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - avance rapide à 1:02:45 pour accéder à la partie QuickBooks.

## <a name="deployment-architecture"></a>Architecture de déploiement

![QuickBooks + déploiement d’Azure RemoteApp](./media/remoteapp-quickbooks/ra-quickbooks.png)