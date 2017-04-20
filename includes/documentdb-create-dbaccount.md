1.  Connectez-vous au [portail Azure](https://portal.azure.com/).
2.  Dans la Jumpbar, cliquez sur **Nouveau**, cliquez sur **données + stockage**, puis cliquez sur **DocumentDB (NoSQL)**.

    ![Capture d’écran du portail Azure, mise en surbrillance des autres Services et DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. Dans la carte de **nouveau compte** , spécifiez la configuration du compte DocumentDB souhaitée.

    ![Capture d’écran de la cuillère DocumentDB nouveau](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    - Dans la zone **ID** , entrez un nom pour identifier le compte DocumentDB.  Lorsque l' **ID** est validé, une coche verte s’affiche dans la zone **ID** . La valeur **ID** devient le nom d’hôte dans l’URI. L' **ID** peut contenir uniquement des lettres minuscules, nombres et le «- » de caractères et doit être comprise entre 3 et 50 caractères. Notez que *documents.azure.com* est ajouté au nom du point de terminaison choisie, dont le résultat devient votre point de terminaison DocumentDB compte.

    - Dans la zone **NoSQL API** , sélectionnez **DocumentDB**.  

    - Pour l' **abonnement**, sélectionnez l’abonnement Azure que vous souhaitez utiliser pour le compte DocumentDB. Si votre compte est un abonnement, ce compte est sélectionné par défaut.

    - Dans le **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte DocumentDB.  Par défaut, un nouveau groupe de ressources est créé. Pour plus d’informations, voir [l’aide du portail pour gérer vos ressources Azure Azure](../articles/azure-portal/resource-group-portal.md).

    - **Emplacement** permet de spécifier l’emplacement géographique dans lesquelles vous pouvez héberger votre compte DocumentDB. 

4.  Une fois que les nouvelles options de compte DocumentDB sont configurées, cliquez sur **créer**. Pour vérifier l’état du déploiement, activez le hub de Notifications.  

    ![Créer rapidement des bases de données - capture d’écran du hub de Notifications, indiquant que le compte DocumentDB est en cours de création](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Capture d’écran du concentrateur Notifications, indiquant que le compte DocumentDB a été correctement créé et déployé sur un groupe de ressources - notification créateur de base de données en ligne](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  Une fois le compte DocumentDB est créé, il est prêt à utiliser avec les paramètres par défaut. La cohérence par défaut du compte DocumentDB est définie à la **Session**.  Vous pouvez ajuster la cohérence par défaut en cliquant sur **La cohérence par défaut** dans le menu de la ressource. Pour en savoir plus sur les niveaux de cohérence offertes par DocumentDB, voir [niveaux de cohérence dans DocumentDB](../articles/documentdb/documentdb-consistency-levels.md).

    ![Capture d’écran de la carte du groupe de ressources : commencez développement d’applications](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Capture d’écran de la cuillère de niveau de cohérence - cohérence de Session](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md
