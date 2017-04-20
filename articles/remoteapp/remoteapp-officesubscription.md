
<properties 
    pageTitle="Comment utiliser votre abonnement Office 365 avec Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment vous pouvez utiliser votre abonnement Office 365 dans Azure RemoteApp pour partager des applications Office."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Comment utiliser votre abonnement Office 365 avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Saviez-vous que vous pouvez utiliser votre abonnement Office 365 existant dans Azure RemoteApp pour partager des applications Office à partir du cloud ? Poursuivez votre lecture pour plus d’informations sur votre Office 365 + options RemoteApp Azure, notamment des liens vers des articles sur Office 365 pour vous aider à tirer pleinement parti de votre abonnement.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Comment utiliser les comptes Office 365 pour RemoteApp Azure ?
Consultez l’article de nouveau de Peter pour toutes les informations : [comment utiliser RemoteApp Azure aux comptes d’utilisateur Office 365](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Puis-je utiliser mon abonnement Office 365 pour exécuter des applications Office dans Azure RemoteApp ?

Oui ! En fait, à l’aide de votre abonnement Office 365 est le seul moyen pour importer vos applications Office à Azure RemoteApp.

(Remarque : Si votre déploiement d’Azure RemoteApp est fourni par un partenaire d’hébergement, ils pourront à vous fournir des licences Office basées sur un [Contrat de licence de fournisseur de Service](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx))


L’avantage de votre abonnement Office 365 est qu’elle vous permet d’utiliser la même licence utilisateur sur de nombreuses plates-formes différentes et environnements, y compris le cloud Azure. Lorsque vous utilisez les applications Office dans Azure RemoteApp vous n’avez pas besoin acheter des licences supplémentaires ou configurer vos licences existantes d’une manière particulière. Il vous suffit d’un abonnement Office 365 incluant [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus permet [d’activation de l’ordinateur partagé](https://technet.microsoft.com/library/Dn782860.aspx) : cette fonctionnalité permet de temporaire activation basée sur l’utilisateur pour Office dans virtuel et les environnements de cloud comme Azure RemoteApp (ou des Services Bureau à distance).

Quelles offres Office 365 incluent Office 365 ProPlus ? Consultez le tableau [disponibilité de Service au sein de chaque offre](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Notez que pas toutes les offres incluent Office 365 ProPlus (par exemple, l’offre Office 365 entreprise). Si votre offre n’existe pas, vous pouvez la mise à niveau vers une offre qui effectue (par exemple, Office 365 éducation E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>OK, comment sont mon site Office 365 licences ProPlus utilisées avec RemoteApp Azure ?

Chaque licence utilisateur pour Office 365 ProPlus permet à un utilisateur unique activer des applications Office sur 5 ordinateurs et tablettes et téléphones. Chaque activation est enregistrée avec l’utilisateur jusqu'à ce qu’ils désactivé Office sur l’appareil. (Les utilisateurs peuvent gérer leurs appareils dans le [portail Office 365](https://portal.office365.com/).)

Avec Azure RemoteApp un seul utilisateur peut se connecte à plusieurs ordinateurs sur le même jour sans vous en rendre compte. C’est parce que le service gère automatiquement et nuances de ressources dans le cloud, tandis que l’utilisateur voit uniquement les applications et les programmes que vous avez partagés. Pour ce scénario Office 365 ProPlus propose un mode de l’activation d’un ordinateur partagé - ce qui signifie que cet utilisateur ne doit pas effectuer la gestion des licences pour accéder à ces ressources et que les ordinateurs individuels ne sont pas par rapport à la limite de l’activation de 5 ordinateur.

Dans la mesure où vous (l’administrateur) attribuez des licences Office 365 ProPlus à vos utilisateurs, ils peuvent utiliser Office sur leur appareil personnel, ainsi que votre collection RemoteApp Azure.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Quelles applications Office puis-je utiliser avec Office 365 et Azure RemoteApp ?

Vous pouvez utiliser votre abonnement Office 365 pour activer et partager Office 2013 dans les déploiements RemoteApp Azure. Nous ne gèrent pas l’utilisation d’autres versions d’Office avec Azure RemoteApp. Cela inclut Office 2003, Office 2007, Office 2010 et Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>Qu’en est-il de Visio Pro ou Project Pro ?

L’image Office 365 ProPlus inclus dans votre abonnement RemoteApp inclut Visio Pro et Project Pro. Mais vous ne pouvez pas utiliser votre abonnement Office 365 ProPlus pour activer ces programmes - ils ont chacun leur propre licence. Vous pouvez les activer dans le [portail Office 365](https://portal.office365.com/). 

Vous n’êtes pas obligé de ces programmes de licence si vous ne voulez pas les utiliser. Simplement activer les programmes que vous souhaitez utiliser - et ignorer les autres colonnes. Vous verrez toujours les dans l’image, mais vous ne pouvez pas les utiliser. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Comment puis-je commencer avec Office 365 et Azure RemoteApp ?

Maintenant que vous connaissez les détails du contrat de licence Office 365, nous allons vous aider à commencer à utiliser dans Azure RemoteApp, c’est très simple :

Lorsque vous créez votre collection de sites RemoteApp Azure, utilisez l’image **Office 365 ProPlus (abonnement nécessaire)** .

![Azure RemoteApp image avec Office 365 ProPlus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Cette image contient la dernière version de Windows Server et Office 365 ProPlus. Après avoir configuré votre collection de sites (y comprises publication applications), vos utilisateurs simplement connectez-vous à Azure RemoteApp (à l’aide de leur client RemoteApp) et fournir leurs informations d’identification Office 365 pour toutes les applications Office. Licences sont automatiquement remis sans n’importe quel jeu vers le haut ou gestion obligatoire.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Puis-je créer une image personnalisée avec Office 365 ProPlus ?

Vous pouvez créer une image personnalisée pour votre collection de sites qui contient Office 365 ProPlus. Il existe 2 choix - utiliser l’image de la galerie Azure que nous fournissons ou vous pouvez créer votre propre image personnalisée et installer Office 365 ProPlus il.

### <a name="use-the-azure-gallery-image"></a>Utiliser l’image de la galerie Azure

Pour déployer Office 365 ProPlus sur une collection de sites, le plus simple consiste à [commencer par l’une des images Galerie Azure](remoteapp-image-on-azurevm.md) inclus dans votre abonnement Azure RemoteApp. Vérifiez que vous sélectionnez l’image **Windows Server hôte Bureau à distance Session avec Office 365 ProPlus préinstallés** . Ensuite, installez toutes les applications que vous voulez dans cette image, et que vous êtes mention.

### <a name="use-a-custom-image"></a>Utiliser une image personnalisée

Vous pouvez toujours créer une image personnalisée : vous pouvez créer une [Machine virtuelle Azure](remoteapp-image-on-azurevm.md) ou [créer l’image localement](remoteapp-create-custom-image.md) et téléchargez-le sur Azure. Dans les deux cas, vérifiez que vous installez Office 365 ProPlus en utilisant le nœud de l’activation d’un ordinateur partagé. Utilisez l' [Outil déploiement d’Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) et suivez les [instructions](https://technet.microsoft.com/library/Dn782858.aspx) pour l’installation.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Désactiver les mises à jour automatiques pour Office 365 ProPlus dans votre image personnalisée - IMPORTANT

Votre image personnalisée est utilisée par Azure RemoteApp sous forme de modèle pour l’ajout de ressources supplémentaires en tant que la demande à partir de votre augmente les utilisateurs. Pour empêcher les retards et les problèmes de connexion, désactivez les mises à jour automatiques pour Office dans l’image. Si vous n’effectuez pas le cas, puis chaque ressource créé avec ce modèle met automatiquement à jour lorsqu’il est démarré. À la place, utilisez la procédure de RemoteApp Azure standard pour mettre à jour votre image personnalisée. Cette façon dont vous mettez à jour les applications Office une fois sur l’image du modèle et puis laissez Azure RemoteApp intégrées d’obtenir les mises à jour à vos utilisateurs.

Pour désactiver les mises à jour automatiques, ajoutez le code suivant au fichier de configuration outil déploiement d’Office :

        <Updates Enabled="FALSE" />

Maintenant votre fichier de configuration doit contenir ces lignes :
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Comment puis-je mettre à jour une image avec Office 365 ProPlus ?

Il existe de nombreuses raisons pour mettre à jour l’image dans votre collection de sites. Voici quelques :

- Obtenir les dernières mises à jour de Windows 
- Obtenir les dernières mises à jour application Office 365 ProPlus
- Mettre à jour votre application personnalisée
- Modifier les autres paramètres de configuration pour l’image elle-même

Pour la procédure de bout en bout de mise à jour de votre collection de sites pour utiliser l’image que vous mis à jour, accédez [ici](remoteapp-update.md). Mais, pour plus d’informations sur la façon de mettre à jour l’image et Office 365 ProPlus, consultez les informations suivantes.

Vous avez deux options de mise à jour de votre image - remplacer votre image avec un autre complètement ou manuellement mettre à jour votre image existante.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Remplacer votre image avec l’image de la galerie Azure dernière + ajouter des personnalisations
Avec cette option, vous permettent de Microsoft prendre en charge les mises à jour Windows Server et Office 365 ProPlus. Au lieu de la mise à jour de votre image existante, vous devez créer une complètement nouvelle image basée sur la dernière image de la galerie. Ensuite, répétez les étapes auparavant Personnalisez l’image : installer des applications personnalisées, modifiez la configuration de l’image, etc..

Les images Galerie sont régulièrement mises à jour, vous pouvez compter, sachant que vos applications Windows Server et Office 365 ProPlus sont à jour. Bien entendu, le compromis est que vous devrez appliquer vos personnalisations chaque fois que vous recevez une nouvelle image. Vous pouvez créer des scripts pour automatiser la définition de vos personnalisations.

### <a name="manually-update-your-existing-image"></a>Mettre à jour manuellement votre image existante

Avec cette option, les outils Windows standard vous permet d’appliquer des mises à jour à l’image. Pour Office 365 ProPlus, utilisez l’outil déploiement d’Office pour télécharger et installer les dernières mises à jour ou les versions d’Office 365 ProPlus.

> [AZURE.IMPORTANT] N’oubliez pas : désactiver les mises à jour automatiques Office 365 ProPlus.

Vous avez besoin de plus d’informations sur les mises à jour à l’aide de l’outil déploiement d’Office ?

- [Déployer démarrer en un clic pour les produits Office 365 à l’aide de l’outil déploiement d’Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Déploiement et la mise à jour d’Office 365 ProPlus à l’aide de l’outil déploiement d’Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vidéo)
- [Configurer les paramètres de mise à jour pour Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)
