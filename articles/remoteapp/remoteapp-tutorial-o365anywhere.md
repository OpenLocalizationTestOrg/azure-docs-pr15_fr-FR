<properties
   pageTitle="Obtenir la même expérience Office 365 sur n’importe quel appareil avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment partager n’importe quelle application Office 365 avec d’autres utilisateurs à l’aide de RemoteApp Azure."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Obtenir la même expérience Office 365 sur n’importe quel appareil avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Cet article s’expliquer comment déployer Office 365 sur n’importe quel appareil dans votre entreprise. Vos utilisateurs peuvent obtenir les mêmes fonctionnalités et l’expérience de l’interface utilisateur sur Android, Apple et Windows.

Nous cette phase de l’aide de Azure RemoteApp en hébergeant Office 365 sur des machines virtuelles en mesure d’échelle dans Azure auxquels les utilisateurs peuvent se connecter. Cet ensemble de machines virtuelles que nous appelons une collection « nuage ».

## <a name="create-a-cloud-collection"></a>Créer une collection de cloud

Tout d’abord une fois que vous avez créé un compte Azure, accédez à **RemoteApp** en cliquant sur le lien dans la partie gauche.
![Affichage RemoteApp Azure sur le portail Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Puis continuez en cliquant sur **Nouveau** dans le bas et « création rapide » une collection de sites. Fournir un nom, la région, l’abonnement, le plan et l’image « Proffesional Office 2013 » que nous fournissons.
![Boîte de dialogue Créer](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Une fois que vous avez terminé le formulaire que nom doit commencer le processus de création d’une collection de sites. Cela peut prendre jusqu'à une heure ou ainsi.

![En attente](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Une fois que le processus est terminé, il se présente comme suit. Si vous cliquez sur **publication** , nous voyons que la plupart des applications Office ont déjà été publiées pour nous.
![Collection créée](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Applications publiées](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

À ce stade vous pouvez également ajouter plusieurs utilisateurs qui ont accès à cette collection en cliquant sur **L’accès des utilisateurs**.
![Configurer l’accès utilisateur](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Maintenant nous allons essayer de se connecter à Office 365 !

## <a name="connect-to-office-365"></a>Se connecter à Office 365

Nous au-dessus de tête à [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), faites défiler vers le bas et cliquez sur **télécharger les clients** pour installer le client Azure RemoteApp sur le périphérique que vous utilisez. Les captures d’écran ci-dessous sont pour Windows.

Une fois que l’application démarre vous êtes invité à vous connecter avec votre compte Microsoft (anciennement appelé un « identifiant Live ID »), utilisez la même que celle que votre compte Azure pour l’instant. Lorsque vous êtes connecté dans vous devez afficher une notification de nouveau invitations, cliquez sur il et vous devriez voir une liste comme ci-dessous. Accepter l’invitation qui correspond à votre Azure propriétaire de l’adresse de messagerie.

![Nouvelle invitation](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Quoi il ressemble lorsqu’il y a des invitations nouveau.

![Accepter une application](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Une fois que vous acceptez l’invitation, vous devriez voir toutes les applications Office dans le client Azure RemoteApp.

![Liste des applications](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Lorsque vous cliquez sur un de ces que l’application doit commencer la machine virtuelle Azure et doivent être toutes la valeur ! Profitez !

![démarrage](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
