<properties
   pageTitle="Débogage des applications dans un conteneur Docker local | Microsoft Azure"
   description="Découvrez comment modifier une application qui s’exécute dans un conteneur Docker local, actualisez le conteneur via modifier et actualiser et définir des points d’arrêt de débogage"
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
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Débogage des applications dans un conteneur Docker local

## <a name="overview"></a>Vue d’ensemble
Visual Studio Tools pour Docker fournit une méthode unique pour développer dans un et validez votre application localement dans un conteneur Linux Docker.
Vous n’êtes pas obligé de redémarrer le conteneur chaque fois que vous modifiez un code.
Cet article illustre comment utiliser la fonctionnalité « Modifier et actualiser » pour démarrer une application Web Core ASP.NET dans un conteneur Docker local, apportez les modifications nécessaires et actualisez le navigateur pour afficher ces modifications.
Il vous également montrent comment définir des points d’arrêt pour le débogage.

> [AZURE.NOTE] Prise en charge Windows conteneur sera bientôt dans une version ultérieure

## <a name="prerequisites"></a>Conditions préalables
Les outils suivants doivent être installées.

- [Mise à jour de Visual Studio 2015 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Installer la [Mise à jour de Visual Studio 2015 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Kit de développement Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Pour exécuter conteneurs Docker localement, vous aurez besoin d’un client docker local.
Vous pouvez utiliser la finale [Boîte à outils Docker](https://www.docker.com/products/overview#/docker_toolbox) qui requiert Hyper-V à désactiver, ou vous pouvez utiliser [Docker pour Windows bêta](https://beta.docker.com) qui utilise Hyper-V et requiert Windows 10.

Si vous utilisez la boîte à outils Docker, vous devez [configurer le client Docker](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. créer une application web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Ajoutez la prise en charge Docker

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. modifier votre code et actualiser

Pour parcourir rapidement les modifications, vous pouvez démarrer votre application dans un conteneur et continuer à apporter des modifications, les lecteurs comme vous le feriez avec IIS Express.

1. Définir la Configuration de Solution sur `Debug` , puis appuyez sur ** &lt;CTRL + F5 >** pour générer votre image docker et exécutez-le.

    Une fois que l’image conteneur a été généré et s’exécute dans un conteneur Docker, Visual Studio s’ouvre l’application Web dans votre navigateur par défaut.
    Si vous utilisez le navigateur Microsoft Edge ou dans le cas contraire comportent des erreurs, consultez la section [résolution des problèmes](vs-azure-tools-docker-troubleshooting-docker-errors.md) .

1. Accédez à la page à propos, ce qui correspond à l’endroit où nous allons apporter nos modifications.

1. Retournez dans Visual Studio et ouvrez `Views\Home\About.cshtml`.

1. Ajoutez le contenu HTML suivant à la fin du fichier et enregistrer les modifications.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  L’affichage de la fenêtre de sortie, lorsque la génération .NET est terminée et que vous consultez ces lignes, basculez vers le navigateur et actualisez la page à propos.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  Vos modifications ont été appliquées !

## <a name="4-debug-with-breakpoints"></a>4. déboguer des points d’arrêt

Souvent, modifications devront davantage inspection, exploiter les fonctionnalités de débogage de Visual Studio.

1.  Retournez dans Visual Studio et ouvrez`Controllers\HomeController.cs`

1.  Remplacez le contenu de la méthode About() avec les éléments suivants :

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Définir un point d’arrêt à gauche de la `string message`... ligne.

1.  Appuyez sur la touche ** &lt;F5 >** pour démarrer le débogage.

1.  Accédez à la page à propos de pour atteindre votre point d’arrêt.

1.  Basculez dans Visual Studio pour afficher le point d’arrêt et vérifiez que la valeur du message.

    ![][2]

##<a name="summary"></a>Résumé

Avec [Visual Studio 2015 Tools pour Docker](https://aka.ms/DockerToolsForVS), vous pouvez obtenir la productivité utiliser localement, le réalisme de production de développement dans un conteneur Docker.

## <a name="troubleshooting"></a>Résolution des problèmes

[Résolution des problèmes de développement Docker Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>En savoir plus sur Docker avec Visual Studio, Windows et Azure

- [Docker Tools pour Visual Studio](http://aka.ms/dockertoolsforvs) - développez votre code .NET Core dans un conteneur
- [Docker Tools pour Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - créer et déployer des conteneurs docker
- [Docker Tools pour Visual Studio Code](http://aka.ms/dockertoolsforvscode) - services de langue pour l’édition des fichiers docker, avec davantage de scénarios e2e bientôt
- [Informations sur le conteneur Windows](http://aka.ms/containers)- Windows Server et des informations sur le serveur Nano
- [Service de conteneur Azure](https://azure.microsoft.com/services/container-service/) - [contenu d’un Service conteneur Azure](http://aka.ms/AzureContainerService)
-    Pour plus d’exemples d’utilisation de Docker, voir [utiliser Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour des Démarrages rapides supplémentaires à partir de la démonstration HealthClinic.biz, consultez [Azure développeur outils Démarrages rapides](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Divers outils Docker

[Certains outils de rédaction docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Articles bonne

[Présentation des Microservices de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Présentations

- [Stéphane Lasker : VS Live Orléans 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction à ASP.NET Core @ créer 2016 - où vous en démo](https://channel9.msdn.com/Events/Build/2016/B810)
- [Développer des applications .NET dans des conteneurs, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
