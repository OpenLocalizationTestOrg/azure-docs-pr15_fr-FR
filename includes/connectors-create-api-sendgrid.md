### <a name="prerequisites"></a>Conditions préalables
- Un compte [SendGrid](https://www.SendGrid.com/) 

Avant de pouvoir utiliser votre compte SendGrid dans une application logique, vous devez autoriser l’application logique pour vous connecter à votre compte SendGrid. Peut être effectué en, vous pouvez faire ceci facilement à partir d’au sein de votre application logique sur le portail Azure. 

Voici les étapes pour autoriser votre application logique pour vous connecter à votre compte SendGrid :

1. Pour créer une connexion à SendGrid, dans le Concepteur d’application logique, sélectionnez **afficher Microsoft managed API** dans la liste déroulante, puis entrez *SendGrid* dans la zone de recherche. Sélectionnez l’ou les actions que vous allez utiliser :  
  ![SendGrid étape 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si vous n’avez pas créé de toutes les connexions à SendGrid avant, vous devez obtenir invité à fournir vos informations d’identification SendGrid. Ces informations d’identification seront utilisées pour autoriser votre application logique pour vous connecter à et accéder aux données de votre compte SendGrid :  
  ![SendGrid étape 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Avez-vous remarqué la connexion a été créée et vous êtes maintenant libres de continuer avec les autres étapes dans votre application logique :  
  ![SendGrid étape 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   
