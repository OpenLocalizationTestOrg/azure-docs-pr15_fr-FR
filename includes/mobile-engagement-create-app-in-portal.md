1. Connectez-vous au [Portail classique Azure](https://manage.windowsazure.com), puis cliquez sur **+ Nouveau** dans la partie inférieure de l’écran.

2. Cliquez sur **Services d’application**, puis **Engagement Mobile**, puis sur **créer**.

    ![](./media/mobile-engagement-create-app-in-portal/create-mobile-engagement-app.png)

3. Dans la fenêtre contextuelle qui s’affiche, entrez les informations suivantes :

    ![](./media/mobile-engagement-create-app-in-portal/create-azme-popup.png)

    - **Nom de l’application**: nom de votre application. 
    - **Plateforme**: plate-forme cible pour l’application. Vous devez créer une application Mobile Engagement par plateforme que vous ciblez pour votre application mobile. 
    - **Nom de la ressource application**: nom par lequel cette application est accessible via l’API et URL. 
    - **Emplacement**: centre de données de la région où cette application et la collection de sites app seront hébergées.
    - **Collection de sites**: sélectionnez une collection de sites créée précédemment ou sélectionnez « Nouveau collecte ».
    - **Nom de la collection**: représente votre groupe d’applications. Cela garantit également que toutes vos applications sont dans un groupe qui permet de calculs agrégés des indicateurs. Vous devez utiliser votre nom de la société ou service ici le cas échéant.

4. Sélectionnez l’application que vous venez de créer sous l’onglet **Applications** .

5. Cliquez sur **Informations sur la connexion** afin d’afficher les paramètres de connexion pour mettre en l’intégration SDK dans votre application mobile.

6. Copiez la **Chaîne de connexion** : il s’agit de ce que vous devrez identifier cette application code de l’Application et vous connecter avec Engagement Mobile depuis votre application.

    ![](./media/mobile-engagement-create-app-in-portal/app-connection-info-page.png)

