<properties 
    pageTitle="Comment faire pour configurer ordinateur pour le développement de Services de support avec .NET" 
    description="En savoir plus sur les conditions préalables pour les Services de support à l’aide de Media Services SDK pour .NET. Apprenez également à créer une application Visual Studio." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Développement de Media Services avec .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Cette rubrique explique comment commencer à développer des applications de Services de support à l’aide de .NET.

La bibliothèque **Azure Media Services.NET SDK** vous permet de programmer Media Services à l’aide de .NET. Pour rendre encore plus facile à développer avec .NET, la bibliothèque **d’Azure Media Services.NET SDK Extensions** est fournie. Cette bibliothèque contient un ensemble de méthodes d’extension et les fonctions d’assistance destiné à simplifier votre code .NET. Les deux bibliothèques sont disponibles par le biais **NuGet** et **GitHub**.


##<a name="prerequisites"></a>Conditions préalables

-   Un compte Media Services dans un abonnement Azure nouvel ou existant. Consultez la rubrique [comment créer un compte de Services de support](media-services-portal-create-account.md).
-   Systèmes d’exploitation : Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET framework 4.5.
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1 (Professionnel, Premium, intégrale ou Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

Cette section vous montre comment créer un projet dans Visual Studio et le configurer pour le développement de Media Services.  Dans ce cas le projet est une application de console Windows c#, mais les mêmes étapes de configuration illustrés ici s’appliquent à d’autres types de projets que vous pouvez créer des applications de Services de support (par exemple, une application Windows Forms ou une application Web ASP.NET).

Cette section montre comment utiliser **NuGet** pour ajouter Media Services .NET SDK et autres bibliothèques dépendantes.

Par ailleurs, vous pouvez obtenir les dernières bits Media Services .NET SDK à partir de GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) et [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), générer la solution et ajoutez les références au projet client. Notez que toutes les dépendances nécessaires téléchargés et automatiquement extrait.

1. Créer une nouvelle Application Console c# dans Visual Studio 2010 SP1 ou version ultérieure VS. Entrez le **nom**, un **emplacement**et un **nom de la Solution**, puis cliquez sur OK.

2. Générez la solution.

2. Utilisez **NuGet** pour installer et ajouter **Les Extensions SDK .NET Azure Media Services**. Installer ce package, également installe **Media Services.NET SDK** et ajoute tous les autres dépendances requises.

    Vous assurer que la version la plus récente de NuGet installé. Pour plus d’instructions d’installation et d’informations, voir [NuGet](http://nuget.codeplex.com/).

2. Dans l’Explorateur de solutions, double-cliquez sur le nom du projet et sélectionnez packages NuGet gérer...

    La boîte de dialogue Gérer les Packages NuGet s’affiche.

3. Dans la galerie en ligne, recherchez Azure MediaServices Extensions, choisissez .NET SDK Extensions Azure Media Services, puis cliquez sur le bouton Installer.

    Le projet est modifié et les références pour les Extensions Media Services .NET SDK, Media Services .NET SDK et autres assemblys dépendants sont ajoutées.

4. Pour promouvoir un environnement de développement plus claire, pensez à activer NuGet Package restaurer. Pour plus d’informations, voir [restaurer de Package NuGet «](http://docs.nuget.org/consume/package-restore).

3. Ajoutez une référence à assembly **System.Configuration** . Cet assembly contient System.Configuration. Classe **ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration (par exemple, App.config).

    Pour ajouter des références à l’aide de la boîte de dialogue Gérer les références, cliquez sur le nom du projet dans l’Explorateur de solutions. Ensuite, sélectionnez Ajouter et références.

    La boîte de dialogue Gérer les références s’affiche.

4. Sous assemblys .NET framework, recherchez et sélectionnez l’assembly System.Configuration et appuyez sur OK.
5. Ouvrez le fichier App.config (ajouter le fichier à votre projet si elle n’a pas été ajouté par défaut) et ajouter une section *appSettings* vers le fichier.     
Définissez les valeurs pour votre Azure Media Services nom et compte clé de compte, comme le montre l’exemple suivant.

    Pour rechercher le nom et la clé des valeurs, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres apparaît à droite. Dans la fenêtre Paramètres, sélectionnez clés. Cliquez sur l’icône en regard de chaque zone de texte copie la valeur dans le Presse-papiers système.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Remplacer du texte existant **à l’aide** au début du fichier Program.cs par le code suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

À ce stade, vous êtes prêt à commencer à développer une application Media Services.    


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
