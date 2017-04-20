<properties
    pageTitle="Prise en main diffuser du contenu à la demande à l’aide de .NET | Azure"
    description="Ce didacticiel vous guide dans les étapes de l’implémentation d’une application de distribution de contenu à la demande sur avec Azure Media Services à l’aide de .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Prise en main diffuser du contenu à la demande à l’aide du Kit de développement .NET

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Vue d’ensemble 

Ce didacticiel vous guide dans les étapes de l’implémentation d’une application de distribution de contenu vidéo à la demande (VoD) à l’aide d’Azure Media Services (AMS) SDK pour .NET.


Le didacticiel présente le flux de travail Media Services et les objets de programmation courantes et les tâches requis pour le développement de Media Services. À la fin du didacticiel, vous pourrez flux ou progressivement télécharger un exemple de fichier multimédia que vous avez téléchargée, codé et téléchargé.

## <a name="what-youll-learn"></a>Enseignements de cet article

Le didacticiel montre comment effectuer les tâches suivantes :

1.  Créer un compte Media Services (à l’aide du portail Azure).
2.  Configurer le point de terminaison de diffusion en continu (à l’aide du portail Azure).
3.  Créer et configurer un projet Visual Studio.
5.  Connectez-vous au compte Media Services.
6.  Créer un nouvel actif et télécharger un fichier vidéo.
7.  Encoder le fichier source dans un jeu de fichiers MP4 de débit adaptive.
8.  Publier l’actif et obtenez des URL pour le téléchargement de diffusion en continu et progressif.
9.  Tester en lisant votre contenu.

## <a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont requis pour terminer le didacticiel.

- Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. 
    
    Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez crédits pouvant être utilisées pour essayer services Azure payants. Même une fois que les crédits sont utilisés vers le haut, vous pouvez conserver le compte et utiliser des services Azure gratuits et fonctionnalités, telles que la fonctionnalité d’applications Web dans le Service d’application Azure.
- Systèmes d’exploitation : Windows 8 ou version ultérieure, Windows 2008 R2, Windows 7.
- .NET framework 4.0 ou version ultérieure
- Visual Studio 2010 SP1 (Professionnel, Premium, intégrale ou Express) ou versions ultérieures.


##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécutez un échantillon de [ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Créer un compte Azure Media Services à l’aide du portail Azure

Les étapes décrites dans cette section montrent comment créer un compte AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Media + CDN** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** Entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services est tous les nombres en minuscules ou des lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure que vous avez accès.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. En savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans un **emplacement**, sélectionnez la région géographique est utilisé pour stocker les enregistrements de métadonnées et éléments multimédias de votre compte Media Services. Cette zone est utilisée pour traiter et diffuser vos médias. Uniquement les zones des Services de support disponibles apparaissent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique comme votre compte Media Services, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles de noms de compte de stockage sont les mêmes que pour les comptes Media Services.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour afficher la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, Encoder actifs, surveiller l’avancement de tâche) utilisez la fenêtre **paramètres** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurer les points de terminaison de diffusion en continu à l’aide du portail Azure

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants est offrant des performances vidéo via adaptive débit diffusion en continu à vos clients. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Media Services fournit emballage dynamique qui vous permet d’effectuer votre débit adaptive contenu MP4 codé en continu les formats pris en charge par Media Services (MPEG tiret, TLS, Smooth Streaming, HDS) juste-à-temps, sans avoir à stocker des versions prédéfinies de chacun de ces formats de diffusion en continu.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un jeu de fichiers de débit adaptive MP4 (les étapes de codage vous trouverez plus loin dans ce didacticiel).  
- Créer au moins une unité de diffusion en continu pour le *point de terminaison de la diffusion* à partir de laquelle vous envisagez de remise votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de diffusion en continu.

Avec emballage dynamique, vous ne devez stocker et payer pour les fichiers au format de stockage unique et Media Services crée et gère la réponse appropriée basée sur les requêtes à partir d’un client.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :


1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison de la diffusion**. 

2. Cliquez sur la valeur par défaut diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de diffusion en continu, faites glisser le curseur **unités de diffusion en continu** .

    ![Unités de diffusion en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de n’importe quelle unité nouveau peut prendre jusqu'à 20 minutes.

##<a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

1. Créer une nouvelle Application Console c# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, un **emplacement**et un **nom de la Solution**, puis cliquez sur **OK**.

2. Utilisez le package NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) pour installer **.NET SDK Extensions Azure Media Services**.  Les Extensions Media Services .NET SDK est un ensemble de méthodes d’extension et les fonctions d’assistance qui seront Simplifiez votre code et le rendre plus facile à développer avec Media Services. Installer ce package, également installe **Media Services.NET SDK** et ajoute tous les autres dépendances requises.

3. Ajoutez une référence à assembly System.Configuration. Cet assembly contient la classe **System.Configuration.ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration, par exemple, App.config.

4. Ouvrez le fichier App.config (ajouter le fichier à votre projet si elle n’a pas été ajouté par défaut) et ajouter une section *appSettings* vers le fichier. Définissez les valeurs pour votre Azure Media Services nom et compte clé de compte, comme le montre l’exemple suivant. Pour obtenir le nom du compte et les informations de clé, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez votre compte AMS. Puis, sélectionnez **paramètres** > **clés**. Les fenêtres de touches gérer affiche le nom du compte et les clés principales et secondaires s’affiche.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Remplacer du texte existant **à l’aide** au début du fichier Program.cs par le code suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Créer un nouveau dossier dans le répertoire de projets et copier un fichier .mp4 ou .wmv que vous souhaitez coder et diffuser en continu ou télécharger progressivement. Dans cet exemple, le chemin d’accès « C:\VideoFiles » est utilisé.

##<a name="connect-to-the-media-services-account"></a>Connectez-vous au compte Media Services

Lorsque vous utilisez Media Services avec .NET, vous devez utiliser la classe **CloudMediaContext** pour la plupart des Services de support tâches de programmation : connexion au compte Media Services ; Création, mise à jour, l’accès à et supprimer les objets suivants : actifs, fichiers de biens, tâches, les stratégies d’accès, Locator, etc..

Remplacer la classe programme par défaut par le code suivant. Le code montre comment lire les valeurs de connexion à partir du fichier App.config et comment créer l’objet **CloudMediaContext** afin de vous connecter aux Services de support. Pour plus d’informations sur la connexion aux Services de support, consultez l’article [connexion à Media Services avec le Kit de développement de Services de support pour .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La fonction **Main** appelle les méthodes qui seront définies plus loin dans cette section.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Créer un nouvel actif et télécharger un fichier vidéo

Dans les Services de support, vous téléchargez (ou acquisition) vos fichiers numériques dans un bien. L’entité de **biens** peut contenir vidéo, audio, images, collections miniatures, texte tracks et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés, votre contenu est stocké en toute sécurité dans le nuage pour un traitement ultérieur et la diffusion en continu. Les fichiers dans l’actif sont appelés **Fichiers actif**.

La méthode **UploadFile** définie sous appels **CreateFromFile** (défini dans les Extensions .NET SDK). **CreateFromFile** crée un nouveau bien téléchargé dans lequel le fichier source spécifié.

La méthode **CreateFromFile** prend **AssetCreationOptions** qui vous permet de spécifier une des options de la création de biens suivants :

- **Aucun** : aucun chiffrement est utilisé. Il s’agit de la valeur par défaut. Notez que lorsque vous utilisez cette option, votre contenu n’est pas protégé lors des transferts ou au reste dans le stockage.
Si vous envisagez d’effectuer un MP4 à l’aide de téléchargement progressif, utilisez cette option.
- **StorageEncrypted** - Utilisez cette option pour chiffrer votre contenu effacer localement en utilisant le chiffrement-256 bits Standard (AES), qui télécharge puis à l’endroit où il est stocké le stockage Azure chiffrée au reste. Ressources protégés par le chiffrement de stockage sont automatiquement non chiffrés et placés dans un système de fichiers chiffrés avant le codage et éventuellement chiffrés avant de le télécharger comme un nouvel actif de sortie. Le cas d’utilisation principal pour le stockage de chiffrement est lorsque vous voulez protéger vos fichiers multimédias d’entrée de haute qualité avec chiffrement fort inactives sur disque.
- **CommonEncryptionProtected** - Utilisez cette option si vous téléchargez du contenu déjà chiffré et protégé par chiffrement courantes ou PlayReady DRM (par exemple, Smooth Streaming protégé avec PlayReady DRM).
- **EnvelopeEncryptionProtected** – Utilisez cette option si vous téléchargez TLS cryptée avec AES. Notez que les fichiers doivent avoir été codés et chiffrés par gestionnaire transformer.

La méthode **CreateFromFile** vous permet également de spécifier un rappel afin d’indiquer la progression du téléchargement du fichier.

Dans l’exemple suivant, nous spécifier **Aucun** pour afficher les options de biens.

Ajoutez la méthode suivante à la classe Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Encoder le fichier source dans un jeu de fichiers MP4 de débit adaptatif

Après avoir traiter les biens dans les Services de support, support peut être codé, transmuxed, filigrane et ainsi de suite, avant qu’il est envoyée aux clients. Ces activités sont planifiées et exécutez sur plusieurs instances de rôle de l’arrière-plan pour garantir la disponibilité et les performances. Ces activités sont appelées tâches, et chaque tâche se compose de tâches atomiques faire le travail réel sur le fichier actif.

Comme mentionné précédemment, lorsque vous travaillez avec Azure Media Services, un des scénarios plus courants a décidé de débit adaptive diffusion en continu à vos clients. Media Services peuvent empaqueter dynamiquement un jeu de fichiers de débit adaptive MP4 dans un des formats suivants : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder ou transcoder votre mezzanine (source) de fichiers dans un jeu de fichiers de débit adaptive MP4 ou Smooth Streaming débit adaptive.  
- Accéder au moins une unité de diffusion en continu du point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu.

Le code suivant montre comment envoyer une tâche de codage. La tâche contient une tâche qui spécifie le fichier mezzanine à transcoder dans un jeu de débit adaptive MP4s à l’aide de **Media Encoder Standard**. Le code soumet le travail et attend jusqu'à ce qu’elle soit terminée.

Une fois que la tâche est terminée, vous ne pourrez pas diffuser votre actif ou progressivement télécharger des fichiers de MP4 qui ont été créées après transcodage.
Notez que vous n’avez pas besoin d’avoir plus de 0 unités de diffusion en continu pour télécharger des fichiers MP4 progressivement.

Ajoutez la méthode suivante à la classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publier du bien et recueillir des URL pour le téléchargement de diffusion en continu et progressif

Pour diffuser en continu ou télécharger un bien, vous devez tout d’abord « publier » en créant un repère. Repères permettent d’accéder aux fichiers contenues dans l’actif. Media Services prend en charge les deux types de repères : OnDemandOrigin Locator, utilisé pour diffuser des médias (par exemple, MPEG tiret, TLS ou Smooth Streaming) et Locator Access Signature (sa), utilisé pour télécharger des fichiers multimédias.

Après avoir créé les repères, vous pouvez créer les URL qui sont utilisés pour diffuser en continu ou téléchargez vos fichiers.


Une URL de diffusion en continu de Smooth Streaming format est le suivant :

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Une URL de diffusion en continu de TLS a le format suivant :

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Une URL de diffusion en continu de tiret MPEG a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Une URL de sa permet de télécharger des fichiers a le format suivant :

    {blob container name}/{asset name}/{file name}/{SAS signature}

Extensions Media Services .NET SDK fournissent des méthodes d’assistance pratique qui renvoient mis en forme d’URL pour la ressource publiée.

Le code suivant utilise .NET SDK Extensions pour créer des repères et pour obtenir la diffusion en continu et URL de téléchargement progressive. Le code affiche également comment télécharger des fichiers vers un dossier local.

Ajoutez la méthode suivante à la classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Tester en lisant votre contenu  

Une fois que vous exécutez le programme défini dans la section précédente, les URL semblables à celui-ci seront affichera dans la fenêtre de la console.

URL de diffusion en continu Adaptive :

Diffusion en continu lisse

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

TLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

TIRET MPEG

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL de téléchargement progressive (audio et vidéo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Pour vous de flux vidéo, utilisez [Le lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pour tester le téléchargement progressif, collez une URL dans un navigateur (par exemple, Internet Explorer, Chrome ou Safari).


##<a name="next-steps-media-services-learning-paths"></a>Étapes suivantes : Media Services rubriques d’apprentissage professionnelles

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>Recherchez d’autres informations ?

Si cette rubrique ne contenait pas ce que vous attendiez, il manque un élément, ou dans une autre façon n’a pas été répond à vos besoins, veuillez nous fournir vos commentaires à l’aide du thread Disqus ci-dessous.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
