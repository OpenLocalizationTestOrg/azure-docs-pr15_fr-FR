### <a name="prerequisites"></a>Conditions préalables

- Un compte [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Avant de pouvoir utiliser votre compte SMTP dans une application logique, vous devez autoriser l’application logique pour vous connecter à votre compte SMTP. Peut être effectué en, vous pouvez faire ceci facilement à partir d’au sein de votre application logique sur le portail Azure.  

Voici les étapes pour autoriser votre application logique pour vous connecter à votre compte SMTP :  
1. Pour créer une connexion à SMTP, dans le Concepteur d’application logique, sélectionnez **afficher Microsoft managed API** dans la liste déroulante, puis entrez *SMTP* dans la zone de recherche. Sélectionnez l’ou les actions que vous allez utiliser :  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Si vous n’avez pas créé de toutes les connexions au service SMTP avant, vous devez obtenir invité à fournir vos informations d’identification SMTP. Ces informations d’identification seront utilisées pour autoriser votre application logique pour vous connecter à et accéder aux données de votre compte SMTP :  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Avez-vous remarqué la connexion a été créée et vous êtes maintenant libres de continuer avec les autres étapes dans votre application logique :  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  

