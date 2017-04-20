### <a name="prerequisites"></a>Conditions préalables

- Un compte [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Avant de pouvoir utiliser votre compte SFTP dans une application logique, vous devez autoriser l’application logique pour vous connecter à votre compte SFTP. Peut être effectué en, vous pouvez faire ceci facilement à partir d’au sein de votre application logique sur le portail Azure.  

Voici les étapes pour autoriser votre application logique pour vous connecter à votre compte SFTP :  
1. Pour créer une connexion à SFTP, dans le Concepteur d’application logique, sélectionnez **afficher Microsoft managed API** dans la liste déroulante, puis entrez *SFTP* dans la zone de recherche. Sélectionnez le déclencheur **SFTP - lorsqu’un fichier est ajouté ou modifié** :  
![Image de connexion en ligne SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Si vous n’avez pas créé de toutes les connexions à SFTP avant, vous devez obtenir invité à fournir vos informations d’identification SFTP. Ces informations d’identification seront utilisées pour autoriser votre application logique pour vous connecter à et accéder aux données de votre compte SFTP :  
![Image de connexion en ligne SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Avez-vous remarqué la connexion a été créée et vous êtes maintenant libres de continuer avec les autres étapes dans votre application logique :   
 ![Image de connexion en ligne SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 
