## <a name="create-a-device-management-enabled-iot-hub"></a>Créer un périphérique gestion activée IoT concentrateur

Gestion des périphériques IoT concentrateur étant dans l’aperçu, vous devez créer un concentrateur de IoT appareil gestion activée. Lors de la gestion des périphériques IoT concentrateur atteint officielle, ce didacticiel est mise à jour. Les étapes suivantes vous montrent comment effectuer cette tâche à l’aide du portail Azure.

1.  Connectez-vous au [portail Azure].
2.  Dans la Jumpbar, cliquez sur **Nouveau**, puis cliquez sur **Internet des objets**, puis cliquez sur **Azure IoT concentrateur**.

    ![][img-new-hub]

3.  Dans la carte **IoT concentrateur** , choisissez la configuration pour votre plateforme IoT.

    ![][img-configure-hub]

  -   Dans la zone **nom** , entrez un nom pour votre plateforme IoT. Si le **nom** est valide et disponible, une coche verte s’affiche dans la zone **nom** .
  -   Sélectionnez un **niveau de tarification et d’échelle**. Ce didacticiel ne nécessite pas un niveau spécifique.
  -   Dans le **groupe de ressources**, créer un nouveau groupe de ressources ou sélectionnez-en un. Pour plus d’informations, voir [utilisation des groupes de ressources pour gérer vos ressources Azure].
  -   Cochez la case pour **Activer la gestion des périphériques - aperçu**.
  -   Dans un **emplacement**, sélectionnez l’emplacement pour héberger votre concentrateur IoT. Gestion des périphériques IoT concentrateur est disponible uniquement dans les États-Unis, Europe du Nord et Asie de l’est au cours de la version d’évaluation.

    > [AZURE.NOTE]Si vous n’activez la case **Activer la gestion des périphériques**, les exemples ne fonctionnent pas.<br/>En vérifiant **Activer la gestion des périphériques**, vous créez un aperçu IoT concentrateur pris en charge uniquement dans les États-Unis, Europe du Nord et Asie de l’est et pas destinée aux scénarios de production. Vous ne peut pas déplacer des appareils dans et se déconnecter de hubs de gestion activée appareil.

4.  Lorsque vous avez choisi votre concentrateur IoT options de configuration, cliquez sur **créer**. Il peut prendre quelques minutes pour Azure créer votre concentrateur IoT. Pour vérifier l’état, vous pouvez suivre la progression de la **Startboard** ou dans le panneau de configuration de **Notifications** .

    ![][img-monitor]

5.  Lorsque le IoT Hub a été créé, la carte pour votre plateforme s’ouvrent automatiquement. Notez le **nom d’hôte**, puis cliquez sur **stratégies d’accès partagé**.

    ![][img-keys]

6.  Cliquez sur la stratégie **iothubowner** , puis copiez et notez la chaîne de connexion dans la carte **iothubowner** . Copier dans un emplacement que vous pouvez accéder à plus tard, car vous en avez besoin pour effectuer ce didacticiel.

    > [AZURE.NOTE] Dans les scénarios de production, veillez à éviter d’utiliser les informations d’identification **iothubowner** .

    ![][img-connection]

Vous venez de créer un périphérique gestion activée IoT concentrateur. Vous devez la chaîne de connexion pour effectuer ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de l’appareil

Dans cette section, vous utilisez un outil de nœud appelé [IoT concentrateur Explorer] [ iot-hub-explorer] pour créer une identité appareil pour ce didacticiel.

1. Dans votre environnement de ligne de commande, exécutez la procédure suivante :

    npm installer -giothub-explorer@latest

2. Ensuite, exécutez la commande suivante pour vous connecter à votre concentrateur, n’oubliez pas de remplacer par `{service connection string}` avec la chaîne de connexion IoT concentrateur que vous avez copiée précédemment :

    connexion iothub explorer « {chaîne de connexion service} »

3. Enfin, créez une nouvelle identité appareil appelée `myDeviceId` avec la commande :

    Explorateur d’iothub créer myDeviceId--chaîne de connexion

Prenez note de la chaîne de connexion appareil à partir du résultat. Cette chaîne de connexion est utilisée par l’application d’appareil pour vous connecter à votre plateforme IoT en tant que périphérique.

![][img-identity]

Reportez-vous à la [mise en route avec concentrateur IoT] [ lnk-getstarted] pour créer des identités des appareils par programme.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Portail Azure]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[À l’aide de groupes de ressources pour gérer vos ressources Azure]: ../articles/azure-portal/resource-group-portal.md
