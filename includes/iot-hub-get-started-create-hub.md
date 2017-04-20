## <a name="create-an-iot-hub"></a>Créer un concentrateur IoT

Créer un concentrateur IoT pour votre périphérique simulé pour vous connecter à. Les étapes suivantes vous montrent comment effectuer cette tâche à l’aide du portail Azure.

1. Connectez-vous au [portail Azure][lnk-portal].

2. Dans la Jumpbar, cliquez sur **Nouveau** > **Internet des objets** > **Azure IoT concentrateur**.

    ![Portail Azure Jumpbar][1]

3. Dans la carte **IoT concentrateur** , choisissez la configuration pour votre plateforme IoT.

    ![Carte de concentrateur IoT][2]

    * Dans la zone **nom** , entrez un nom pour votre plateforme IoT. Si le **nom** est valide et disponible, une coche verte s’affiche dans la zone **nom** .
    * Sélectionnez un [niveau de prix et les proportions de][lnk-pricing]. Ce didacticiel ne nécessite pas un niveau spécifique. Pour ce didacticiel, utilisez la couche F1 gratuite.
    * Dans le **groupe de ressources**, créer un nouveau groupe de ressources ou sélectionnez-en un. Pour plus d’informations, voir [utilisation des groupes de ressources pour gérer vos ressources Azure][lnk-resource-groups].
    * Dans un **emplacement**, sélectionnez l’emplacement pour héberger votre concentrateur IoT. Pour ce didacticiel, sélectionnez votre emplacement le plus proche.

4. Lorsque vous avez choisi votre concentrateur IoT options de configuration, cliquez sur **créer**.  Il peut prendre quelques minutes pour Azure créer votre concentrateur IoT. Pour vérifier l’état, vous pouvez suivre la progression du Startboard ou dans le panneau de configuration de Notifications.

    ![Nouveau statut concentrateur IoT][3]

5. Lorsque le hub IoT a été créé avec succès, cliquez sur la vignette nouvelle pour votre plateforme IoT dans le portail Azure pour ouvrir la carte pour le nouveau concentrateur IoT. Notez le **nom d’hôte**, puis cliquez sur **stratégies d’accès partagé**.

    ![Nouvelle carte de concentrateur IoT][4]

6. Dans la carte de **stratégies d’accès partagé** , cliquez sur la stratégie **iothubowner** , puis copiez et notez la chaîne de connexion dans la carte **iothubowner** . Pour plus d’informations, consultez [contrôle d’accès] [ lnk-access-control] dans le « guide du développeur Azure IoT concentrateur ».

    ![Carte de stratégies d’accès partagé][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
