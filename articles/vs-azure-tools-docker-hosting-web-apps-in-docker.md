<properties
   pageTitle="Déployer un conteneur ASP.NET Core Linux Docker à un hôte distant Docker | Microsoft Azure"
   description="Découvrez comment utiliser Visual Studio Tools pour Docker à déployer une application web ASP.NET Core un conteneur Docker en cours d’exécution sur un ordinateur Azure Docker hôte Linux virtuel"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Déployer un conteneur ASP.NET à un hôte Docker distant

## <a name="overview"></a>Vue d’ensemble
Docker est un moteur de conteneur léger similaire de certaines façons pour une machine virtuelle, que vous pouvez utiliser pour héberger des applications et services.
Ce didacticiel vous guide dans l’aide de l’extension [Visual Studio 2015 Tools pour Docker](http://aka.ms/DockerToolsForVS) pour déployer une application ASP.NET Core à un hôte Docker sur Azure à l’aide de PowerShell.

## <a name="prerequisites"></a>Conditions préalables
Les éléments suivants sont nécessaires pour effectuer ce didacticiel :

- Créez un machines virtuelles Docker Azure comme décrit dans [l’utilisation de docker-machine à calculer avec Azure](./virtual-machines/virtual-machines-linux-docker-machine.md)
- Installer la [Mise à jour de Visual Studio 2015 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Kit de développement Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
- Installer les [Outils de 2015 Visual Studio pour Docker - aperçu](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. créer une application web ASP.NET Core
La procédure suivante vous guide dans la création d’une application ASP.NET Core base qui est utilisée dans ce didacticiel.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Ajoutez la prise en charge Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Utilisez le Script PowerShell DockerTask.ps1 

1.  Ouvrez une invite PowerShell à la racine de votre projet. 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Valider la distance hôte est en cours d’exécution. Vous devriez voir état = en cours d’exécution 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Si vous utilisez la version bêta Docker, votre hôte n’est pas répertorié ici.

1.  Créer l’application à l’aide-paramètre de génération

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] Si vous utilisez la version bêta Docker, omettez l’ordinateur argument -
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Exécuter l’application, à l’aide de la - paramètre exécuter

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] Si vous utilisez la version bêta Docker, omettez l’ordinateur argument -
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Une fois que docker est terminée, vous devriez voir résultats semblables à ce qui suit :

    ![Afficher votre application][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
