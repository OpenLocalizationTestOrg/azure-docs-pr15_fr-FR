La Azure Marketplace met à disposition un large éventail d’applications web les plus courants sont développés par Microsoft, des entreprises tierces et initiatives logiciel open source. Applications Web créées à partir de la Azure Marketplace ne nécessitent pas l’installation d’un logiciel autre que le navigateur utilisé pour vous connecter au [Portail Preview Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

Dans ce didacticiel, vous allez découvrir :

- Comment créer une nouvelle application web par le biais du Azure Marketplace.

- Comment déployer l’application web via le portail d’aperçu Azure.
 
Vous allez créer un blog WordPress qui utilise un modèle par défaut. L’illustration suivante montre l’application terminée :


![Blog WordPress][13]

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="create-a-web-app-in-the-portal"></a>Créer une application web dans le portail

1. Connectez-vous au portail Azure Preview.

2. Ouvrez le Azure Marketplace en cliquant sur l’icône **Marketplace** :

    ![Icône Marketplace][marketplace]

    Ou en cliquant sur l’icône **Nouveau** dans le coin supérieur droit du tableau de bord et en sélectionnant **Marketplace** en la bottow de la liste.
    
    ![Créer une nouvelle][5]
    
3. Sélectionnez **Web + Mobile**. Rechercher **WordPress** et cliquez sur l’icône **WordPress** .

    ![WordPress de liste][7]
    
5. Après la lecture de la description de l’application WordPress, sélectionnez **créer**.

6. Cliquez sur **application Web**et fournir les valeurs requises pour configurer votre application web.
    
    ![configurer votre application][8]

7. Cliquez sur **base de données**et fournir les valeurs requises pour configurer votre base de données MySQL. 

    ![configurer la base de données][database]

8. Attribuez un nom à un groupe de ressources.

    ![Définir le groupe de ressources][groupname]

8. Si nécessaire, cliquez sur **l’abonnement**et spécifier l’abonnement à utiliser. 

7. Lorsque vous avez terminé de définir l’application web, cliquez sur **créer**et patienter pendant la création de la nouvelle application web.

   Lorsque l’application a été créée, vous verrez le groupe de ressources contenant la base de données et web app.

   ![afficher un groupe][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Lancer et gérer votre application web WordPress
    
1. Cliquez sur votre nouvelle application web pour afficher des détails sur votre application.

    ![lancer tableau de bord][10]

2. Dans la page **Essentials** , cliquez sur **Parcourir** ou sur le lien sous **Url** pour ouvrir la page d’accueil de l’application web.

    ![URL du site][browse]

3. Si vous n’avez pas installé WordPress, entrez les informations de configuration appropriées requises WordPress, puis cliquez sur **Installer WordPress** pour finaliser la configuration et ouvrez la page de connexion de l’application web.

4. Cliquez sur **connexion** , entrez vos informations d’identification.  

5. Vous aurez une nouvelle application web WordPress qui ressemble à l’application web ci-dessous.    

    ![votre site WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
