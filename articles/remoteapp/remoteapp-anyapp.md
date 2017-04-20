<properties
   pageTitle="Exécuter une application Windows sur n’importe quel appareil avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment partager n’importe quelle application Windows avec d’autres utilisateurs à l’aide de RemoteApp Azure."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Exécuter une application Windows sur n’importe quel appareil avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Vous pouvez exécuter une application Windows n’importe où sur n’importe quel appareil, immédiatement, ses - simplement à l’aide de RemoteApp Azure. Qu’il s’agisse d’une application personnalisée écrite de dix ans, ou une application Office, vos utilisateurs n’aient plus à être lié à un système d’exploitation spécifique (par exemple, Windows XP) pour les applications peu.

Avec Azure RemoteApp, vos utilisateurs peuvent également utiliser leurs propre appareils Android ou Apple et obtenir la même expérience qu’ils vous utilisez Windows (ou sur les téléphones Windows). Pour ce faire, qui héberge votre application Windows dans une collection de machines virtuelles Windows Azure - vos utilisateurs peuvent y accéder depuis n’importe où ils disposent d’une connexion internet. 

Poursuivez votre lecture pour obtenir un exemple d’exactement comment effectuer cette opération.

Dans cet article, nous allons partager l’accès avec tous nos utilisateurs. Toutefois, vous pouvez utiliser n’importe quelle application. Dans la mesure où vous pouvez installer votre application sur un ordinateur Windows Server 2012 R2, vous pouvez le partager en suivant les étapes ci-dessous. Vous pouvez consulter [Configuration requise pour l’application](remoteapp-appreqs.md) pour vous assurer que votre application est prises en charge.

Veuillez noter que, car Access est une base de données, et nous voulons cette base de données pour être utile, nous allez effectuer quelques étapes supplémentaires pour permettre aux utilisateurs l’accès le partage de données Access. Si votre application n’est pas une base de données ou si vous n’avez pas besoin vos utilisateurs puissent accéder à un partage de fichiers, vous pouvez ignorer ces étapes dans ce didacticiel

> [AZURE.NOTE] <a name="note"></a>Vous avez besoin d’un compte Azure pour effectuer ce didacticiel :
> - Vous pouvez [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): pouvez-vous crédits vous pouvez utiliser pour essayer de services Azure payants et même après leur utilisation jusqu'à vous pouvez conserver le compte et libérer de l’utilisation des services Azure, tels que des sites Web. Votre carte de crédit est jamais facturée, sauf si vous explicitement de modifiez vos paramètres et lui demander de vous être facturés.
> - Vous pouvez [Activer les avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): MSDN votre abonnement fournit les crédits chaque mois que vous pouvez utiliser pour les services Azure payants.


## <a name="create-a-collection-in-remoteapp"></a>Créer une collection de sites dans RemoteApp

Commencez par créer une collection de sites. La collection de sites sert de conteneur pour vos applications et les utilisateurs. Chaque collection de sites est basé sur une image : vous pouvez créer vos propres ou utilisez celui fourni avec votre abonnement. Pour ce didacticiel, nous utilisons l’image d’évaluation d’Office 2013 - il contient l’application que nous souhaitons partager.

1. Dans le portail Azure, faites défiler vers le bas dans l’arborescence de navigation de gauche jusqu'à ce que vous voyiez RemoteApp. Ouvrez cette page.
2. Cliquez sur **créer une collection de sites RemoteApp**.
3. Cliquez sur **Création rapide** et entrez un nom pour votre collection de sites.
4. Sélectionnez la région à utiliser pour créer votre collection de sites. Pour optimiser les performances, sélectionnez la zone qui est le plus proche géographiquement à l’emplacement où vos utilisateurs auront accès à l’application. Par exemple, dans ce didacticiel, les utilisateurs seront situées à Redmond, Washington. La région Azure le plus proche est **US ouest**.
5. Sélectionnez le plan de facturation que vous voulez utiliser. Le plan de facturation base place 16 utilisateurs sur un ordinateur virtuel Azure volumineux, alors que le plan de facturation standard a 10 utilisateurs sur un ordinateur virtuel Azure volumineux. Comme un exemple général, le plan de base fonctionne bien pour les flux de travail du type d’entrée de données. Pour une application de productivité, comme Office, vous souhaiterez peut-être l’offre standard.
6. Enfin, sélectionnez l’image Office Professionnel 2013. Cette image contient des applications Office 2013. Juste un rappel - cette image n’est valable pour les collections d’évaluation et validation des conceptions. Vous « Impossible d’utiliser cette image dans une collection de production.
7. À présent, cliquez sur **RemoteApp créer la collection de sites**.

![Créer une collection de cloud dans RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Cela commence la création de votre collection de sites, mais peut prendre jusqu'à une heure.

Vous êtes maintenant prêt à ajouter vos utilisateurs.

## <a name="share-the-app-with-users"></a>Partager l’application avec des utilisateurs

Une fois que votre collection de sites a été créé, il est temps de publier l’accès aux utilisateurs et ajouter les utilisateurs qui doivent y accéder.

Si vous avez accédé en s’éloignant du nœud RemoteApp Azure pendant la création de la collection de sites, commencez par créer votre manière revenir à la page d’accueil Azure.

2. Cliquez sur la collection de sites que vous avez créé précédemment pour accéder à des options supplémentaires et configurer la collection de sites.
![Une collection de cloud RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Sous l’onglet **publication** , cliquez sur **Publier** en bas de l’écran, puis cliquez sur **programmes du menu Démarrer publier**.
![Publier un programme RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Sélectionnez les applications que vous souhaitez publier dans la liste. Dans notre cas, nous avons choisi Access. Cliquez sur **terminé**. Attendez que les applications terminer la publication.
![Publication Access dans RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Une fois que l’application a terminé la publication, tête sur l’onglet de **L’accès utilisateur** pour ajouter tous les utilisateurs qui ont besoin d’accéder à vos applications. Entrez les noms d’utilisateur (adresse de messagerie) pour vos utilisateurs, puis sur **Enregistrer**.

![Ajouter des utilisateurs à RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. À présent, il est temps pour informer les utilisateurs sur ces nouvelles applications et comment y accéder. Pour ce faire, envoyez vos utilisateurs un message électronique les pointant sur l’URL de téléchargement de client de bureau à distance.
![URL de téléchargement du client pour RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Configurer l’accès à Access

Certaines applications doivent configuration supplémentaire une fois que vous les déployez via RemoteApp. En particulier, pour l’accès, nous allons créer un partage de fichiers sur Azure auquel tous les utilisateurs peuvent accéder. (Si vous ne voulez pas le faire, vous pouvez créer une [collection de sites hybride](remoteapp-create-hybrid-deployment.md) [au lieu de notre collection cloud] qui permet à vos utilisateurs accéder aux fichiers et des informations sur votre réseau local.) Ensuite, nous avons besoin indiquer à notre aux utilisateurs de mapper un lecteur local sur leur ordinateur avec le système de fichiers Azure.

La première partie que vous en tant que l’administrateur. Ensuite, nous avons quelques étapes pour vos utilisateurs.

1. Commencez par la publication de l’interface de ligne de commande (cmd.exe). Dans l’onglet **publication** , sélectionnez **cmd**, puis cliquez sur **Publier > publier programme à l’aide du chemin d’accès**.
2. Entrez le nom de l’application et le chemin d’accès. Dans notre cas, utilisez « Explorateur » comme nom et « % SYSTEMDRIVE%\windows\explorer.exe » en tant que le chemin d’accès.
![Publier le fichier cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. À présent, vous devez créer un [compte de stockage](../storage/storage-create-storage-account.md)Azure. Nous avons nommé notre « accessstorage », de choisir un nom qui a un sens pour vous. (Pour misquote Highlander, il peut être qu’un seul « accessstorage ».) ![Compte de stockage Azure notre](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Revenez maintenant à votre tableau de bord afin d’obtenir le chemin d’accès à votre espace de stockage (emplacement de point de terminaison). Vous allez utiliser cette peu, vérifiez que copiez-le quelque part.
![Le chemin d’accès du compte de stockage](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Une fois que le compte de stockage a été créé, vous devez ensuite la clé primaire access. Cliquez sur **Gérer les touches d’accès**, puis copiez la clé primaire access.
6. À présent, définissez le contexte du compte de stockage et créer un nouveau partage de fichiers pour l’accès. Dans une fenêtre élevée de Windows PowerShell, exécutez les applets de commande suivantes :

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Pour notre part, il s’agit donc nous exécuter les applets de commande :

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Il est désormais activer de l’utilisateur. Tout d’abord, demander à vos utilisateurs d’installer un [client RemoteApp](remoteapp-clients.md). Ensuite, les utilisateurs doivent mapper un lecteur de leur compte pour ce partage Azure que vous avez créé et ajouter leurs fichiers Access. Voici comment ils procèdent :

1. Dans le client RemoteApp, access les applications publiées. Démarrez le programme cmd.exe.
2. Exécutez la commande suivante pour mapper un lecteur de votre ordinateur pour le partage de fichiers :

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Si vous définissez le paramètre **/ permanente** sur Oui, le lecteur mappé est conservé dans toutes les sessions.
1. À présent, lancez l’application de l’Explorateur de fichiers à partir de RemoteApp. Copiez les fichiers Access que vous souhaitez utiliser dans l’application partagée pour le partage de fichiers.
![Insertion de fichiers Access dans un partage Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Enfin, ouvrez Access et ouvrez la base de données que vous venez de partager. Vous devriez voir vos données dans Access en cours d’exécution à partir du cloud.
![Une base de données Access réel en cours d’exécution à partir du cloud](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Vous pouvez désormais utiliser Access sur tous vos appareils mobiles - Assurez-vous que vous installez un client RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

À présent que vous maîtrisez la création d’une collection de sites, essayez de créer une [collection de sites qui utilise Office 365](remoteapp-tutorial-o365anywhere.md). Ou vous pouvez créer une [collection de sites hybride ](remoteapp-create-hybrid-deployment.md)qui peuvent accéder à votre réseau local.

<!--Image references-->
 
