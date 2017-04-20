<properties
   pageTitle="Utiliser les applets de commande PowerShell avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment utiliser les applets de commande Windows PowerShell dans Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Utiliser les applets de commande Windows PowerShell avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

 Vous pouvez utiliser les applets de commande PowerShell de RemoteApp Azure pour administrer et maintenir vos collections. Utilisez les informations ci-dessous pour commencer.

## <a name="get-the-cmdlets"></a>Obtenir les applets de commande 
-------------
Tout d’abord télécharger les applets de commande Powershell Azure [ici](http://go.microsoft.com/?linkid=9811175), le RemoteApp applets de commande sont inclus dans celle-ci. 

Consultez l' [aide de l’applet de commande RemoteApp Azure](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurer les applets de commande Azure pour utiliser votre abonnement
------------------
Suivez [ce guide](../powershell-install-configure.md) afin d’utiliser les applets de commande par rapport à votre abonnement Azure.

Vous pouvez utiliser ces étapes pour la mise en route rapide :

1.  Téléchargez et installez les [applets de commande PowerShell Azure](http://go.microsoft.com/?linkid=9811175).
2.  Lancez Microsoft Azure PowerShell.
3.  Exécutez **Ajouter AzureAccount** s’authentifier à votre abonnement Azure. Lorsque vous y êtes invité, entrez le nom d’utilisateur et mot de passe que vous utilisez pour vous connecter au portail Azure.  
4.  Exécutez **Get-AzureSubscription** pour répertorier les abonnements associés à votre compte d’utilisateur. 
5.  Exécutez **Select AzureSubscription** et spécifiez le nom de l’abonnement ou l’ID à utiliser dans la console PowerShell.

Félicitations, votre console PowerShell Azure est configuré et vous êtes prêt à utiliser. N’oubliez pas que vous devez Répétez étapes 2 à 5 pour chaque fois que vous démarrez le la console PowerShell Azure.  

## <a name="create-a-cloud-collection"></a>Créer une collection de cloud
--------------------
Il est simple, exécutez la commande suivante :

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

La commande ci-dessus publie automatiquement les applications Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio et Word).

Création d’une collection de sites peut prendre 30 minutes ou plus de temps. Par conséquent, cette commande renvoie un ID de suivi que vous pouvez utiliser comme suit :


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Une fois terminée la collection de sites, vous pouvez ajouter des utilisateurs à la collection de sites avec la commande suivante :

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Et que vous avez terminé ! Cet utilisateur doit être en mesure de vous connecter à l’application en utilisant le client Azure RemoteApp trouvé [ici](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Applets de commande disponibles
Il existe d’autres commandes qui nous ont un nombre important, la documentation de leur sera bientôt peu de temps :

Applets de commande RemoteApp Collection base : 

- Nouvelle AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Jeu de AzureRemoteAppCollection
- Mise à jour AzureRemoteAppCollection
- Supprimer AzureRemoteAppCollection
- AzureRemoteAppUser ajouter
- Get-AzureRemoteAppUser
- Supprimer AzureRemoteAppUser
- Get-AzureRemoteAppSession
- AzureRemoteAppSession déconnecter
- AzureRemoteAppSessionLogoff appeler
- Envoyer AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- AzureRemoteAppProgram publier
- AzureRemoteAppProgram annuler la publication
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Applets de commande de réseau virtuel RemoteApp :

- Nouvelle AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Jeu de AzureRemoteAppVNet
- Supprimer AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get - AzureRemoteAppVpnDeviceConfigScript
- Réinitialiser AzureRemoteAppVpnSharedKey

Applets de commande RemoteApp modèle image :

- Nouvelle AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Renommer AzureRemoteAppTemplateImage
- Supprimer AzureRemoteAppTemplateImage

Autres applets de commande RemoteApp :

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Jeu de AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 
