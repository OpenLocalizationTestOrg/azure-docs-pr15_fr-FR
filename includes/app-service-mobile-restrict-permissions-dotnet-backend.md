
Par défaut, API dans une application Mobile principale peut être appelé de manière anonyme. Ensuite, vous devez limiter l’accès pour que les clients authentifiés.  

+ **Serveur principal Node.js (via le portail)** :  
    
    Dans de votre application Mobile **paramètres**, cliquez sur **Tables facile** et sélectionnez votre tableau. Cliquez sur **Modifier les autorisations**, sélectionnez **accès authentifié uniquement** pour toutes les autorisations, puis cliquez sur **Enregistrer**. 

+ **Serveur principal .NET (c#)**:  

    Dans le projet serveur, accédez à **contrôleurs** > **TodoItemController.cs**. Ajouter la `[Authorize]` attribut à la classe **TodoItemController** , comme suit. Pour limiter l’accès uniquement aux méthodes spécifiques, vous pouvez également appliquer cet attribut à ces méthodes au lieu de la classe. Publier à nouveau le projet serveur.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Serveur principal Node.js (par le biais Node.js code)** :  
    
    Pour exiger une authentification pour accéder à la table, ajoutez la ligne suivante pour le script serveur Node.js :


        table.access = 'authenticated';

    Pour plus d’informations, reportez-vous à [Comment : exiger une authentification pour l’accès aux tables](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Pour savoir comment télécharger le projet de code de démarrage rapide sur votre site, voir [Comment : télécharger le projet de code de démarrage rapide de serveur principal Node.js à l’aide de Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

