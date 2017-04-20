<properties
    pageTitle="Comment migrer à partir d’un RemoteApp VNET vers un VNET Azure | Microsoft Azure"
    description="Découvrez comment migrer à partir d’un VNET RemoteApp à un VNET Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Comment migrer une collection hybride à partir d’un RemoteApp VNET vers un VNET Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Bonne nouvelle ! Nous avons activé pour déployer des collections de RemoteApp hybride directement dans votre Azure virtuel réseaux existants (VNETs) au lieu de créer VNETs RemoteApp spécifiques. Cela vous permet de tirer parti des dernières fonctionnalités VNET (par exemple, ExpressRoute) et donnez un accès réseau direct à vos collections hybride à d’autres services Azure et machines virtuelles déployés sur ce VNET.  (Cette dernière obtient vous améliorer les performances et le programme d’installation plus facilement aux configurations VNET-VNET).


Supposons que vous avez déjà créé un déploiement collection RemoteApp appelée *OriginalCollection* avec un RemoteApp VNET appelé *RemoteAppVNET*. Voici la procédure migrer vers une nouvelle VNET Azure appelé *AzureVNET*.

1.  Sous l’onglet **réseaux** dans le [portail de gestion](http://manage.windowsazure.com/), créez un VNET appelé *AzureVNET*, à l’aide de la même emplacement, la configuration de DNS et espace d’adressage (pour au moins une des sous-réseaux *AzureVNET* ) en tant que vous avez utilisé pour *RemoteAppVNET*.
2.  Configurer *AzureVNET* à l’hôte ou demandez à la connectivité réseau pour le déploiement d’Active Directory *OriginalCollection* est joint au domaine.
3.  Sous l’onglet **RemoteApps** , créez une nouvelle collection RemoteApp appelée *Nouvelle Collection*. (Utilisez l’option **créer avec VNET** , **Création rapide**).
3.  Configurer *NewCollection* à déployer sur un sous-réseau dans *AzureVNET*.
4.  Configurer *NewCollection* pour utiliser la même image et les informations de participation domaine comme vous avez utilisé pour *OriginalCollection*.
5.  Après quelques heures, *NewCollection* apparaît dans votre liste de collection de sites avec un état actif.

À présent, si vous n’avez pas besoin de migrer des informations utilisateur à partir de la collection d’origine dans la nouvelle collection, procédez ensuite comme suit :

6.  Supprimer *OriginalCollection*.
7.  Supprimer *RemoteAppVNET*.

Et, vous avez terminé !

Vous pouvez également si vous n’avez pas besoin de migrer des informations utilisateur à partir de la collection d’origine dans la nouvelle collection, suivre ces étapes :

6.  Envoyer un message électronique à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) avec votre ID d’abonnement Azure, le nom de votre collection de sites d’origine et le nom de votre nouvelle collection et demandez-lui de migrer vos informations utilisateur.
7.  Sous 2 jours ouvrés l’équipe RemoteApp se déplace la liste d’accès utilisateur et tous les documents de l’utilisateur et paramètres utilisateur de la collection d’origine dans la nouvelle collection.
8.  Supprimer *OriginalCollection*.
9.  Supprimer *RemoteAppVNET*.

Et maintenant, vous avez terminé !

Si vous avez des questions ou que vous avez besoin d’aide spéciaux, veuillez envoyer un e-mail [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
