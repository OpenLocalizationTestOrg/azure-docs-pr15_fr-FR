
1. Visitez le [portail Azure]. Cliquez sur **Rechercher tout** > **Applications Mobile** > le système principal que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **démarrage rapide** > **Cordova**. Sous **configurer votre application cliente**, sélectionnez **créer une nouvelle application**, puis cliquez sur **Télécharger**. Ceci permet de télécharger un projet complet Cordova pour une application préconfigurée pour vous connecter à votre serveur principal.

2. Décompresser le fichier ZIP téléchargé dans un répertoire sur votre disque dur, naviguez jusqu’au fichier solution (.sln) et ouvrez-le à l’aide de Visual Studio.

5. Dans Visual Studio, choisissez la plateforme de la solution (Android, iOS ou Windows) dans le menu déroulant en regard de la flèche de début, puis sélectionnez un périphérique de déploiement spécifique ou émulateur en cliquant sur le menu déroulant sur la flèche verte. Notez que vous pouvez utiliser la plateforme Android par défaut et émulateur ondulation. Didacticiels plus avancées vous demandera de sélectionner un périphérique pris en charge ou émulateur. 

6. Appuyez sur F5 ou cliquez sur la flèche verte pour générer et et exécuter votre application Cordova. Si vous voyez une boîte de dialogue de sécurité dans l’émulateur demande d’accès au réseau, l’accepter.   

7. Après le démarrage de l’application sur le périphérique ou émulateur, tapez du texte significatif dans le **nouveau texte d’entrée**, telles que _le didacticiel achevé_ et puis cliquez sur le bouton **Ajouter** .  
Cela envoie une demande de publication sur le serveur principal Azure que vous déployé précédemment. La version serveur l’insertion de données à partir de la demande est dans la table TodoItem de la base de données SQL et renvoie des informations sur les éléments nouvellement stockées revenir à l’application mobile. L’application mobile affiche ces données dans la liste.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Répétez les trois étapes précédentes pour chaque plateforme de l’appareil que vous prévoyez de prendre en charge.

[Portail Azure]: https://portal.azure.com/
