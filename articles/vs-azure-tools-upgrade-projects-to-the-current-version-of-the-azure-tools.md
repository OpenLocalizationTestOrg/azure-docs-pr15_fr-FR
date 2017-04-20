<properties
   pageTitle="Comment mettre à niveau des projets vers la version actuelle des outils Azure | Microsoft Azure"
   description="Découvrez comment mettre à niveau un projet Azure dans Visual Studio vers la version actuelle des outils Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Comment mettre à niveau des projets vers la version actuelle des outils Azure pour Visual Studio

## <a name="overview"></a>Vue d’ensemble

Après avoir installé la version actuelle des outils Azure (ou une version précédente est plus récente que 1,6), tous les projets qui ont été créés à l’aide d’un outils Azure relâchez avant 1,6 (novembre 2011) passeront automatiquement dès que vous les ouvrez. Si vous avez créé des projets à l’aide de la version (novembre 2011) 1,6 de ces outils et que vous avez toujours cette version installée, vous pouvez ouvrir ces projets dans l’ancienne version et décider ultérieurement s’il faut mettre à niveau.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Change de votre projet lorsque vous mettez à niveau

Si un projet est mis à niveau automatiquement ou si vous spécifiez que vous voulez mettre à niveau, votre projet est modifié pour travailler avec les versions actuelles de certains assemblys et certaines propriétés sont également modifiées comme décrit dans cette section. Si votre projet requiert d’autres modifications pour être compatibles avec la version la plus récente des outils, vous devez effectuer ces modifications manuellement.

- Le fichier web.config pour les rôles web et le fichier app.config pour les rôles de travail sont mis à jour pour faire référence à la version la plus récente de Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Les assemblys Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll et Microsoft.WindowsAzure.ServiceRuntime.dll sont mis à niveau vers les nouvelles versions.

- Profils de publication qui ont été stockées dans le fichier de projet Azure (.ccproj) sont déplacées vers un fichier séparé, avec l’extension .azurePubXml, dans le sous-répertoire **Publier** .

- Certaines propriétés dans le profil de publication sont mis à jour pour prendre en charge des fonctionnalités nouvelles et modifiées. **AllowUpgrade** est remplacée par **DeploymentReplacementMethod** parce que vous pouvez mettre à jour un service cloud déployé par incréments ou simultanément.

- La propriété **UseIISExpressByDefault** est ajoutée et la valeur false afin que le serveur web qui est utilisé pour le débogage ne modifie automatiquement à partir d’Internet Information Services (IIS) pour IIS Express. IIS Express est le serveur web par défaut pour les projets qui sont créés avec les versions plus récentes des outils.

- Si la mise en cache Azure est hébergé dans un ou plusieurs des rôles de votre projet, certaines propriétés de la configuration du service (fichier .cscfg) et la définition de service (fichier .csdef) sont modifiées lorsqu’un projet est mis à niveau. Si le projet utilise le package NuGet mise en cache Azure, le projet est mis à niveau vers la version la plus récente du package. Vous devez ouvrir le fichier web.config et vérifier que la configuration du client a été correctement conservée pendant le processus de mise à niveau. Si vous avez ajouté les références aux assemblys de client Azure mise en cache sans utiliser le package NuGet, ces assemblys ne sont pas actualisés ; Vous devez mettre à jour manuellement ces références vers les nouvelles versions.

>[AZURE.IMPORTANT] Pour les projets F #, vous devez mettre à jour manuellement les références aux assemblys Azure afin qu’ils référencent les versions plus récentes de ces assemblys.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Comment mettre à niveau un projet Azure vers la version actuelle

1. Installer la version actuelle des outils Azure dans l’installation de Visual Studio que vous souhaitez utiliser pour le projet mis à niveau et ouvrez le projet que vous souhaitez mettre à niveau. Si le projet a été créé avec un outils Azure relâchez avant 1,6 (novembre 2011), le projet automatiquement mis à niveau vers la version actuelle. Si le projet a été créé avec la November 2011 version et cette version est toujours installée, le projet s’ouvre dans cette version.

1. Dans l’Explorateur, ouvrir le menu contextuel pour le nœud de projet, sélectionnez **Propriétés**, puis l’onglet **Application** de la boîte de dialogue qui s’affiche.

    L’onglet **Application** affiche la version des outils associée au projet. Si la version actuelle d’Azure outils s’affiche, le projet a déjà été mis à niveau. Si vous avez installé une version plus récente des outils que l’onglet ce qui s’affiche, un bouton **mettre à niveau** apparaît.

1. Cliquez sur le bouton **mettre à niveau** à un projet de mise à niveau vers la version actuelle des outils.

1. Générez le projet, puis traitez toutes les erreurs qui résultent des modifications de l’API. Pour plus d’informations sur la façon de modifier votre code pour la nouvelle version, voir la documentation de l’API spécifique.
