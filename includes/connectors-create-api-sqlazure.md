### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Une [Base de données SQL Azure](../articles/sql-database/sql-database-get-started.md) de ses données de connexion, y compris le nom du serveur, le nom de base de données et le nom d’utilisateur et mot de passe. Ces informations sont incluses dans la chaîne de connexion de base de données SQL :
  
    Serveur = tcp :*yoursqlservername*. database.windows.net,1433;Initial catalogue =*yourqldbname*; Conserver les informations de sécurité = False ; ID d’utilisateur = {utilisateur} ; Mot de passe = {your_password} ; MultipleActiveResultSets = False ; Chiffrer = vrai ; TrustServerCertificate = False ; Délai de connexion = 30 ;

    En savoir plus sur [Les bases de données SQL Azure](https://azure.microsoft.com/services/sql-database).

> [AZURE.NOTE] Lorsque vous créez une base de données SQL Azure, vous pouvez également créer les bases de données exemple inclus avec SQL. 



Avant d’utiliser votre base de données SQL Azure dans une application logique, se connecter à votre base de données SQL. Vous pouvez le faire facilement au sein de votre application logique sur le portail Azure.  

Se connecter à votre base de données SQL Azure procédant comme suit :  

1. Créer une application logique. Dans le Concepteur d’applications logique, ajoutez un déclencheur, puis ajoutez une action. Sélectionnez **afficher Microsoft managed API** dans la liste déroulante, puis entrez « sql » dans la zone de recherche. Sélectionnez une des actions :  

    ![Étape de la création de connexion SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)

2. Si vous n’avez pas encore créé les connexions de base de données SQL, vous êtes invité aux détails de connexion :  

    ![Étape de la création de connexion SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 

3. Entrez les détails de la base de données SQL. Propriétés d’un astérisque sont obligatoires.

    | Propriété | Plus d’informations |
|---|---|
| Se connecter via une passerelle | Laissez désactivée. Il est utilisé lors de la connexion à un serveur SQL local. |
| Nom de la connexion * | Entrez un nom pour votre connexion. | 
| Nom du serveur SQL * | Entrez le nom du serveur ; qui est s’intitule *servername.database.windows.net*. Le nom du serveur est affiché dans les propriétés de base de données SQL dans le portail Azure et également affiché dans la chaîne de connexion. | 
| Nom de base de données SQL * | Entrez le nom que vous avez donné votre base de données SQL. Ceci est répertorié dans les propriétés de base de données SQL dans la chaîne de connexion : catalogue Initial =*yoursqldbname*. | 
| Nom d’utilisateur * | Entrez le nom d’utilisateur que vous avez créé lors de la création de la base de données SQL. Ceci est répertorié dans les propriétés de base de données SQL dans le portail Azure. | 
| Mot de passe * | Entrez le mot de passe que vous avez créé lors de la création de la base de données SQL. | 

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter et accéder à vos données SQL. Une fois terminé, vos informations de connexion se présenter comme suit :  

    ![Étape de la création de connexion SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 

4. Sélectionnez **créer**. 

5. Notez que la connexion a été créée. À présent, effectuez les autres étapes dans votre application logique : 

    ![Étape de la création de connexion SQL Azure](./media/connectors-create-api-sqlazure/table.png)