
1. Sur l’ordinateur local, connectez-vous au [Portail de gestion Azure](http://manager.windowsazure.com) (il s’agit de l’ancien portail).

2. Dans la partie inférieure du volet de navigation, sélectionnez **+ Nouveau** > **Application Services** > **BizTalk Service** > **Créer personnalisé**.

3. Indiquez un **Nom de Service BizTalk** et sélectionnez une **Édition**. 

    Ce didacticiel utilise **mobile1**. Vous aurez besoin d’un nom unique pour votre nouveau BizTalk Service.

4. Une fois que le BizTalk Service a été créé, sélectionnez l’onglet **Connexions hybride** , puis cliquez sur **Ajouter**.

    ![Ajouter une connexion hybride](./media/hybrid-connections-create-new/3.png)

    Cela crée une nouvelle connexion hybride.

5. Fournir un **nom** et un **Nom d’hôte** pour votre connexion hybride et définir **Port** sur `1433`. 
  
    ![Configurer la connexion hybride](./media/hybrid-connections-create-new/4.png)

    Le nom d’hôte est le nom du serveur local. Cela permet de configurer la connexion hybride pour accéder à SQL Server s’exécutant sur le port 1433. Si vous utilisez une instance de SQL Server nommée, utilisez à la place le port statique que vous avez défini précédemment.

6. Une fois la nouvelle connexion est créée, l’état de la de la nouvelle connexion affiche **local Configuration incomplète**.

7. Revenir à votre service mobile, cliquez sur **configurer**, faites défiler jusqu'à **connexions hybride** et cliquez sur **Ajouter une connexion hybride**, puis sélectionnez la connexion hybride que vous venez de créer et cliquez sur **OK**.

    Cela permet à votre service mobile à utiliser votre nouvelle connexion hybride.

Ensuite, vous devez installer le Gestionnaire de connexions hybride sur l’ordinateur local.