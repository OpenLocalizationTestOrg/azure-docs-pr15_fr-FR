<properties
   pageTitle="Résolution des erreurs de Client Docker sur Windows à l’aide de Visual Studio | Microsoft Azure"
   description="Résoudre les problèmes que vous rencontrez lorsque vous utilisez Visual Studio pour créer et déployer des applications web sur Docker sur Windows à l’aide de Visual Studio."
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
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Résolution des problèmes de développement Docker Visual Studio

Lorsque vous travaillez avec Visual Studio Tools pour Docker Preview, vous pouvez rencontrer des problèmes en raison de la nature preview.
Voici quelques problèmes et solutions courantes.


## <a name="unable-to-validate-volume-mapping"></a>Impossible de valider le mappage de volume
Mappage de volume est requis pour partager le code source et les fichiers binaires de votre application avec le dossier d’application dans le conteneur.  Mappages de volume spécifique sont contenues dans les fichiers docker compose.dev.debug.yml et docker compose.dev.release.yml. Comme fichiers sont modifiés sur votre ordinateur hôte, les conteneurs de reflètent ces modifications dans une structure de dossiers similaire.

Pour activer le mappage de volume, ouvrez **paramètres...** à partir de l’icône de barre d’état « moby » Docker pour Windows, puis sur l’onglet **Lecteurs partagés** .  Assurez-vous que la lettre qui héberge votre projet, ainsi que la lettre du lecteur où réside %USERPROFILE% sont partagés par les archiver, puis sur **Appliquer**.

Pour tester si mappage volume fonctionne, une fois que l’ou les lecteurs ont été partagé, reconstruction et F5 à partir de Visual Studio ou essayez les éléments suivants à partir d’une commande invite de commandes :

*Dans une invite de commande Windows*

*[Remarque : cela suppose que votre dossier utilisateurs se trouve sur le lecteur « C » et qu’il a été partagé.  Mettre à jour comme bon vous semble si vous avez partagé un autre lecteur]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Dans le conteneur Linux*

```
/ # ls
```

Vous devriez voir une liste à partir du dossier utilisateurs/Public des répertoires.
Si aucun fichier n’est affichées et que votre dossier /c/Users/Public n’est pas vide, mappage de volume n’est pas configuré correctement. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Modifier dans le répertoire repère pour voir le contenu de la `/c/Users/Public` répertoire :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Remarque :** *Lorsque vous travaillez avec machines virtuelles Linux, le système de fichiers conteneur respecte la casse.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Génération : Tâche « PrepareForBuild » a échoué de façon inattendue.

Microsoft.DotNet.Docker.CommandLine.ClientException : Une erreur s’est produite lors de la tentative de connexion :

Vérifiez que l’hôte docker par défaut est en cours d’exécution. Ouvrez une invite de commandes et exécutez :

```
docker info
```

Si elle retourne une erreur essayez de démarrer l’application de bureau **Docker pour Windows** .  Si l’application de bureau est en cours d’exécution puis l’icône **moby** dans la barre d’état doit être visible. Cliquez avec le bouton droit sur l’icône de barre d’état et ouvrez **paramètres**.  Cliquez sur l’onglet **Réinitialiser** , puis **redémarrer Docker …**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Manuellement la mise à niveau à partir de la version 0,31 à 0,40


1. Sauvegarder le projet
1. Supprimez les fichiers suivants dans le projet :

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Fermez la Solution et supprimez les lignes suivantes à partir du fichier .xproj :

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Rouvrez la Solution
1. Supprimer les lignes suivantes du fichier Properties\launchSettings.json :

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Supprimez les fichiers suivants liés à Docker depuis project.json dans les publishOptions :

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Désinstallez la version précédente et réinstallez Docker outils 0,40, puis sur **Ajouter -> Docker prise en charge** dans le menu contextuel pour votre ASP.Net Core Web ou votre Application Console. Cela ajoutera les nouveau objets Docker nécessaires à votre projet. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Une boîte de dialogue d’erreur se produit lorsque vous tentez **d’Ajouter -> prise en charge Docker** ou déboguer (F5) une Application Core ASP.NET dans un conteneur

Nous avons vu parfois la désinstallation et l’installation d’extensions, cache MEF (Managed extensibilité Framework) de Visual Studio peut être endommagé. Dans ce cas elle peut provoquer une erreur différentes boîtes de dialogue lors de l’ajout de prise en charge Docker et/ou essayez d’exécuter ou déboguer (F5) votre Application Core ASP.NET. En tant qu’une solution de contournement temporaire, exécutez les étapes suivantes pour supprimer et régénérer le cache MEF.

1. Fermez toutes les instances de Visual Studio
1. Ouvrez %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Supprimez les dossiers suivants
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Ouvrez Visual Studio
1. Essayez à nouveau le scénario 
