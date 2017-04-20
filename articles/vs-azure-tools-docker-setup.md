<properties
   pageTitle="Configurer un hôte Docker avec VirtualBox | Microsoft Azure"
   description="Instructions détaillées pour configurer une instance de Docker par défaut à l’aide de la Machine Docker et VirtualBox"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Configurer un hôte Docker avec VirtualBox

## <a name="overview"></a>Vue d’ensemble
Cet article vous guide tout au long de la configuration d’une instance de Docker par défaut à l’aide de la Machine Docker et VirtualBox. Si vous utilisez la [version bêta Docker pour Windows](http://beta.docker.com/), cette configuration n’est pas nécessaire.

## <a name="prerequisites"></a>Conditions préalables
Les outils suivants doivent être installées.

- [Boîte à outils docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configuration du client Docker avec Windows PowerShell

Pour configurer un client Docker, ouvrir Windows PowerShell et effectuez les opérations suivantes :

1. Créer une instance de l’hôte docker par défaut.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Vérifiez que l’instance par défaut est configuré et en cours d’exécution. (Vous devriez voir apparaître une instance nommée « par défaut » en cours d’exécution.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![sortie ls docker machine][0]
 
1. Définir par défaut en tant que l’hôte actif et configurer votre environnement.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Afficher les conteneurs de Docker actives. La liste doit être vide.

    ```PowerShell
    docker ps
    ```

    ![sortie de ps docker][1]
 
> [AZURE.NOTE]Chaque fois que vous redémarrez votre ordinateur de développement, vous devez redémarrer votre hôte docker local.
> Pour ce faire, exécutez la commande suivante à l’invite : `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
