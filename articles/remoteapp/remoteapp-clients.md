
<properties
    pageTitle="Accès à vos applications à partir de n’importe quel appareil | Microsoft Azure"
    description="Découvrez quels clients sont pris en charge pour Azure RemoteApp et comment accéder à vos applications."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="accessing-your-apps-in-azure-remoteapp"></a>Accéder à vos applications dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Un des beautés de RemoteApp Azure est que vous pouvez accéder applications à partir de tous vos appareils mobiles. Mieux encore, vous pouvez commencer à travailler sur un appareil et transition transparente vers un second périphérique et reprendre là où vous vous étiez arrêté. Mise en route vous devez télécharger le client approprié pour votre appareil et se connecter au service.

Dans cette rubrique, nous allons examiner les clients actuellement pris en charge et comment les télécharger avant que je vous montrer comment se connecter à RemoteApp à partir de chacun des clients.

## <a name="supported-clients"></a>Clients pris en charge

Vous pouvez accéder à RemoteApp en suivant les étapes ci-dessous si votre appareil exécute l’un de ces systèmes d’exploitation :

 - Windows 10 
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows Phone 8.1
 - iOS
 - Mac OS X
 - Android


 Qu’en est-il des clients légers ? Les clients légers Windows incorporé suivants sont pris en charge :

- Windows incorporé 7 Standard
- Windows incorporé Standard 8
- Windows incorporé industrie 8.1 Pro
- Windows 10 IoT entreprise


## <a name="downloading-the-client"></a>Téléchargement du client

Quel que soit que vous utilisez la plateforme, le client, que vous devez accéder aux RemoteApp vous pouvez trouver sur la page de [téléchargement du client de bureau à distance](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) .

En cliquant sur les différents liens soit directement démarre le téléchargement du client ou vous envoie un que vous permet du client télécharger page dans l’app store pour cette plate-forme. Installer le client en suivant les instructions à l’écran.

Une fois que vous avez installé le client sur votre appareil et a lancé, accéder à la section correspondante ci-dessous pour savoir comment se connecter à RemoteApp à partir de ce client.

## <a name="android"></a>Android

Une fois que vous avez installé l’application de bureau à distance Microsoft à partir de Google Play store, vous pouvez trouver dans votre liste des applications sous **Bureau à distance**.

1. Lancement de l’application vous donne à un centre de connexion vide, à moins que vous avez déjà été à l’aide de l’application. Pour commencer à utiliser RemoteApp Azure, appuyez sur le bouton d’ajouter **« « + » »** , puis appuyez sur **Azure RemoteApp**. 

     ![Centre de connexion vide](./media/remoteapp-clients/Android1.png)

2. Vous devez connecter avec votre adresse de messagerie pour accéder au service. Appuyez sur **prise en main**.

    ![Se connecter invite de commandes](./media/remoteapp-clients/Android2.png)

3. Dans la page suivante, entrez votre **adresse de messagerie** et appuyez sur **Continuer**. Cela lance le processus de connexion à l’aide d’Azure Active Directory.

    ![Première page Azure Active Directory](./media/remoteapp-clients/Android3.png)

4. Suivez les instructions à l’écran pour vous connecter avec votre compte Microsoft (anciennement appelé « LiveID ») ou ID d’organisation. Une fois connecté, vous pouvez recevoir avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations de confiance et appuyez sur **terminé**. 

    ![Page des invitations](./media/remoteapp-clients/Android4.png)

5. Après avoir accepté votre invitation, la liste des applications que vous avez accès est téléchargée vers votre appareil et mises à disposition dans le centre de connexion. Appuyez sur une des applications à l’utiliser.

    ![Centre de connexion avec un flux](./media/remoteapp-clients/Android5.png)

6. Si vous n’avez pas encore une invitation, vous pouvez toujours essayer le service. Pour ce faire, appuyez sur **accédez à la version d’évaluation gratuite** lorsque vous y êtes invité.

    ![Démo flux invite](./media/remoteapp-clients/Android6.png)

7. Cela vous donnera accès à un ensemble de base des applications pour vous aider à démarrer avec RemoteApp.

    ![Démo flux pour Azure RemoteApp](./media/remoteapp-clients/Android7.png)

## <a name="ios"></a>iOS

Une fois que vous avez installé l’application de bureau à distance Microsoft de l’App store, vous pouvez trouver dans votre liste des applications sous **Client de bureau à distance**.

1. Lancement de l’application vous donne à un centre de connexion vide, à moins que vous avez déjà été à l’aide de l’application. Pour commencer à utiliser RemoteApp Azure, appuyez sur le bouton d’ajouter **« « + » »** , puis appuyez sur **Ajouter Azure RemoteApp**.

    ![Centre de connexion vide](./media/remoteapp-clients/IOS1.png)

2. Vous avez besoin, à se connecter avec votre adresse de messagerie pour accéder au service, pour démarrer ce processus, entrez votre **adresse de messagerie** et appuyez sur **Continuer**.

    ![Se connecter invite de commandes](./media/remoteapp-clients/picture1.png)

3. Suivez les instructions à l’écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez recevoir avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations de confiance et appuyez sur **terminé**.

    ![Page des invitations](./media/remoteapp-clients/IOS3.png)

4. Après avoir accepté votre invitation, la liste des applications que vous avez accès est téléchargée vers votre appareil et mises à disposition dans le centre de connexion. Appuyez sur une des applications pour lancer la console et l’utiliser.

    ![Centre de connexion avec un flux](./media/remoteapp-clients/IOS4.png)

5. Si vous n’avez pas encore une invitation, vous pouvez toujours essayer le service. Pour ce faire, appuyez sur **accédez à la version d’évaluation gratuite** lorsque vous y êtes invité.

    ![Démo flux invite](./media/remoteapp-clients/IOS5.png)

6. Cela vous donnera accès à un ensemble de base des applications pour vous aider à démarrer avec RemoteApp.

    ![Démo flux pour Azure RemoteApp](./media/remoteapp-clients/IOS6.png)

## <a name="mac-os-x"></a>Mac OS X

Une fois que vous avez installé l’application de bureau à distance Microsoft de l’App store, vous pouvez trouver dans votre liste des applications sous **Bureau à distance Microsoft**.

1. Lancement de l’application vous donne à un centre de connexion vide, à moins que vous avez déjà été à l’aide de l’application. Pour commencer à utiliser RemoteApp Azure, cliquez sur le bouton **RemoteApp Azure** .

    ![Centre de connexion vide](./media/remoteapp-clients/Mac1.png)

2. Vous devez connecter avec votre adresse de messagerie pour accéder au service, pour démarrer le processus, appuyez sur **Prise en main**.

    ![Se connecter invite de commandes](./media/remoteapp-clients/Mac2.png)

3. Dans la page suivante, entrez votre **adresse de messagerie** et appuyez sur **Continuer**. Cela lance le processus à l’aide d’Azure Active Directory de connexion.

    ![Première page Azure Active Directory](./media/remoteapp-clients/picture2.png)

4. Suivez les instructions à l’écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez recevoir avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations de confiance et fermer la boîte de dialogue.

    ![Page des invitations](./media/remoteapp-clients/Mac4.png)

5. Après avoir accepté votre invitation, la liste des applications que vous avez accès est téléchargée vers votre appareil et mises à disposition dans le centre de connexion. Double-cliquez sur une des applications pour lancer la console et l’utiliser.

    ![Centre de connexion avec un flux](./media/remoteapp-clients/Mac5.png)

6. Si vous n’avez pas encore une invitation, vous pouvez toujours essayer le service. Pour ce faire, cliquez sur **Atteindre la version d’évaluation gratuite** lorsque vous y êtes invité.

    ![Démo flux invite](./media/remoteapp-clients/Mac6.png)

7. Cela vous donnera accès à un ensemble de base des applications pour vous aider à démarrer avec RemoteApp.

    ![Démo flux pour Azure RemoteApp](./media/remoteapp-clients/Mac7.png)

## <a name="windows-all-supported-versions-except-windows-phone"></a>Windows (pris en charge toutes les versions à l’exception de Windows Phone)

Le client lance automatiquement après l’installation, cependant lorsque vous avez besoin d’y accéder à nouveau ultérieurement il vous pouvez trouver dans votre liste des applications sous le nom **RemoteApp Azure**.

1. Lus tard lancez le client, la première page qui apparaît vous accédez à Azure RemoteApp. Pour continuer, cliquez sur **Mise en route**.

    ![Page d’accueil du client Azure RemoteApp](./media/remoteapp-clients/Windows1.png)

2. La page suivante démarre le signe dans le processus pour Azure RemoteApp à l’aide d’Azure Active Directory. Ce processus doit ressembler familier si vous utilisez les services Microsoft par le passé. Commencez par taper votre **adresse de messagerie** , cliquez sur **Continuer**.

    ![Première invite Azure Active Directory](./media/remoteapp-clients/Windows2.png)

3. Suivez les instructions à l’écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez recevoir avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations de confiance et cliquez sur **terminé**.

    ![Page invitations du client Azure RemoteApp](./media/remoteapp-clients/Windows3.png)

4. Après avoir accepté votre invitation, la liste des applications que vous avez accès est téléchargée vers votre appareil et mises à disposition dans le centre de connexion. Double-cliquez sur une des applications pour lancer la console et l’utiliser.

    ![Centre de connexion du client Azure RemoteApp](./media/remoteapp-clients/Windows4.png)

5. Si personne vous a envoyé une invitation encore, ne vous inquiétez pas nous avons ce qu’il vous faut ! Vous aurez toujours accès à une collection de démonstration pour vous pouvez de tester le service.

    ![Démo flux pour Azure RemoteApp](./media/remoteapp-clients/Windows5.png)

## <a name="windows-phone-81"></a>Windows Phone 8.1

Une fois que vous avez installé l’application de bureau à distance Microsoft à partir du magasin de Windows Phone 8.1, vous pouvez trouver dans votre liste des applications sous **Bureau à distance**.

1. Lancement de l’application pour accéder directement à un centre de connexion vide, à moins que vous avez déjà été à l’aide de l’application. Pour commencer à utiliser RemoteApp Azure, appuyez sur l’ajouter bouton **« « + » »** dans la partie inférieure de l’écran.

    ![Centre de connexion vide](./media/remoteapp-clients/WinPhone1.png)

2. Appuyez ensuite sur **Azure RemoteApp**.

    ![Ajouter une page d’élément](./media/remoteapp-clients/WinPhone2.png)

3. Vous devez connecter avec votre adresse de messagerie pour accéder au service, pour démarrer le processus, appuyez sur **se connecter**.

    ![Se connecter invite de commandes](./media/remoteapp-clients/WinPhone3.png)

4. Dans la page suivante, entrez votre **adresse de messagerie** et appuyez sur **Continuer**. Cela lance le processus à l’aide d’Azure Active Directory de connexion.

    ![Première page Azure Active Directory](./media/remoteapp-clients/WinPhone4.png)

5. Suivez les instructions à l’écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre organisation. Une fois connecté, vous pouvez recevoir avec une page répertoriant toutes les invitations que vous avez reçu. Si vous êtes, sélectionnez les invitations de confiance et appuyez sur **Enregistrer**.

    ![Page des invitations](./media/remoteapp-clients/WinPhone5.png)

6. Après avoir accepté votre invitation, la liste des applications que vous avez accès est téléchargée vers votre appareil et mises à disposition dans le centre de connexion. Appuyez sur une des applications pour lancer la console et l’utiliser.

    ![Centre de connexion avec un flux](./media/remoteapp-clients/WinPhone6.png)

7. Si vous n’avez pas encore une invitation, vous pouvez toujours essayer le service. Pour ce faire, appuyez sur **Oui** lorsque vous y êtes invité.

    ![Démo flux invite](./media/remoteapp-clients/WinPhone7.png)

8. Cela vous donnera accès à un ensemble de base des applications pour vous aider à démarrer avec RemoteApp.

    ![Démo flux pour Azure RemoteApp](./media/remoteapp-clients/WinPhone8.png)
 