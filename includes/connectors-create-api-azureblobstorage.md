### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Un [compte de stockage d’objets Blob Azure](../articles/storage/storage-create-storage-account.md) , y compris le nom de compte de stockage et sa touche d’accès rapide. Ces informations sont répertoriées dans les propriétés du compte de stockage dans le portail Azure. En savoir plus sur [Le stockage Azure](../articles/storage/storage-introduction.md).

Avant d’utiliser votre compte de stockage d’objets Blob Azure dans une application logique, se connecter à votre compte de stockage d’objets Blob Azure. Vous pouvez le faire facilement au sein de votre application logique sur le portail Azure.  

Se connecter à votre compte de stockage d’objets Blob Azure en utilisant les étapes suivantes :  

1. Créer une application logique. Dans le Concepteur d’applications logique, ajoutez un déclencheur, puis ajoutez une action. Sélectionnez **afficher Microsoft managed API** dans la liste déroulante, puis entrez « blob » dans la zone de recherche. Sélectionnez une des actions :  

    ![Étape de la création de connexion de stockage d’objets Blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. Si vous n’avez pas encore créé toutes les connexions au stockage Azure, vous êtes invité aux détails de connexion :   

    ![Étape de la création de connexion de stockage d’objets Blob Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. Entrez les informations de compte de stockage. Propriétés d’un astérisque sont obligatoires.

    | Propriété | Plus d’informations |
|---|---|
| Nom de la connexion * | Entrez un nom pour votre connexion. |
| Nom de compte de stockage Azure * | Entrez le nom de compte de stockage. Le nom de compte de stockage est affiché dans les propriétés de stockage dans le portail Azure. |
| Touche d’accès rapide stockage Azure compte * | Entrez la clé de compte de stockage. Les touches d’accès sont affichent dans les propriétés de stockage dans le portail Azure. |

    Ces informations sont utilisées pour autoriser votre application logique pour se connecter et accéder à vos données. 

4. Sélectionnez **créer**.

5. Notez que la connexion a été créée. À présent, effectuez les autres étapes dans votre application logique : 

    ![Étape de la création de connexion de stockage d’objets Blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  
