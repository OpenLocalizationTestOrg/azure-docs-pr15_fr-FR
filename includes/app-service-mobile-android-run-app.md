
1. Visitez le [portail Azure]. Cliquez sur **Rechercher tout** > **Applications Mobile** > le système principal que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **démarrage rapide** > **Android)**. Sous **configurer votre application cliente**, cliquez sur **Télécharger**. Ceci permet de télécharger un projet complet Android pour une application préconfigurée pour vous connecter à votre serveur principal. 

2. Ouvrez le projet à l’aide de **Studio Android**, à l’aide **d’Importer un projet (ADT Eclipse, Gradle, etc.)**. Vérifiez que vous effectuez cette sélection importation afin d’éviter des erreurs JDK.

3. Appuyez sur le bouton **exécuter 'app'** pour générer le projet et lancer l’application dans le simulator Android.

4. Dans l’application, tapez du texte significatif, tel que de _Terminer le didacticiel_ , puis sur le bouton « Ajouter ». Cela envoie une demande de publication sur le serveur principal Azure que vous déployé précédemment. La version serveur l’insertion de données à partir de la demande est dans la table TodoItem SQL et renvoie des informations sur les éléments nouvellement stockées revenir à l’application mobile. L’application mobile affiche ces données dans la liste. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portail Azure]: https://portal.azure.com/
