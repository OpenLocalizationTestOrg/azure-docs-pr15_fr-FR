#### <a name="prerequisites"></a>Conditions préalables
- Un compte Azure ; Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free)
- Un compte [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Avant d’utiliser votre compte Dynamics dans une application logique, autoriser l’application logique pour vous connecter à votre compte CRM Online. Vous pouvez le faire facilement au sein de votre application logique sur le portail Azure. 

Autoriser votre application logique pour vous connecter à votre compte CRM Online en utilisant les étapes suivantes :

1. Créer une application logique. Dans le concepteur logique applications, sélectionnez **afficher Microsoft managed API** dans la liste déroulante, puis entrez « dynamics » dans la zone de recherche. Sélectionnez une des actions déclencheurs :  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Si vous n’avez pas encore créé toutes les connexions à Dynamics, vous êtes invité à se connecter à l’aide de vos informations d’identification Dynamics :  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Sélectionnez **se connecter**, puis entrez votre nom d’utilisateur et mot de passe. Sélectionnez **se connecter**. 

    Ces informations sont utilisées pour autoriser votre application logique se connecter à, et accéder aux données dans votre compte Dynamics. 
4. Notez que la connexion a été créée. À présent, effectuez les autres étapes dans votre application logique :  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)
