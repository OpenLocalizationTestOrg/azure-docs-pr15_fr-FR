<properties
    pageTitle="Utilisation des applications App-V avec Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment utiliser les applications App-V dans Azure RemoteApp."
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



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Utilisation des applications App-V dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Vous pouvez utiliser des applications App-V dans une collection de hybride RemoteApp Azure, qui nécessite la jointure de domaines.

Avant de commencer, veillez à installer le client App-V 5.1 avec les dernières mises à jour. Vous avez besoin créer une [image personnalisée](remoteapp-create-custom-image.md) qui inclut le client App-V.  

Il est facile à utiliser votre infrastructure App-V avec Azure RemoteApp. Dans la mesure où une collection hybride est déployée dans un VNET Azure qui a accès à votre contrôleur de domaine et les ordinateurs virtuels sont joint au domaine, vous pouvez tirer parti de vos méthodes d’infrastructure et déploiement de App-v existants à application de App-V easyily hôte dans Azure RemoteApp. Voici quelques considérations que vous devez connaître en fonction du type de déploiement App-V qu'actuellement installée :

| Options de configuration |                       | Positif                                                               | Négatif                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Mode de remise       | Diffusion en continu (à la demande) | Application est toujours plus tard et frais                                     | Première latence de temps                                                                                    |
|                       | Monter               | Plus rapides ; application est déjà présente sur la machine virtuelle                              | Gonflement - prend la place de l’image (limite 127 Go)                                                           |
| Stockage de l’emplacement de l’application  | Contenu partagé        | Application s’exécute en mémoire d’instance Azure RemoteApp                         | Mange mémoire et correctement connecté à la diffusion en continu de serveur (fichier) où se trouve l’application                      |
|                       | Disque (mis en cache)         | Exécution rapide. Application ne dépendent pas la disponibilité de la Source de contenu | Gonflement - prend la place de l’image (limite 127 Go)                                                           |
| Ciblage             | Utilisateur                  | Nécessite une infrastructure App-V complète autonome                          |                                                                                                       |
|                       | Global (ordinateur)      |  Avant de publier ou ciblez utilisant la publication sur serveur                         |  Mettez à jour votre image Azure si vous souhaitez mettre à jour l’application (grande). Occupe de l’espace sur image. |

 Après avoir créé votre image personnalisée et votre collection de sites hybride, publiez votre application, attribuer aux utilisateurs et profitez de vos applications App-V existantes hébergées dans Azure RemoteApp remis à n’importe quel appareil n’importe où.
