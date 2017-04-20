<properties 
    pageTitle="Créer un compte DocumentDB avec prise en charge du protocole de MongoDB | Microsoft Azure" 
    description="Apprenez à créer un compte DocumentDB avec prise en charge du protocole de MongoDB, désormais disponible pour l’aperçu." 
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="anhoh"/>

# <a name="how-to-create-a-documentdb-account-with-protocol-support-for-mongodb-using-the-azure-portal"></a>Comment créer un compte DocumentDB avec prise en charge du protocole de MongoDB à l’aide du portail Azure

Pour créer un compte Azure DocumentDB avec prise en charge du protocole de MongoDB, vous devez :

- Posséder un compte Azure. Vous pouvez obtenir un [compte Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà.

## <a name="create-the-account"></a>Créer le compte  

Pour créer un compte DocumentDB avec prise en charge du protocole de MongoDB, procédez comme suit.

1. Dans une nouvelle fenêtre, connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**et cliquez sur **données + stockage**, cliquez sur **Afficher toutes les**, puis recherchez la catégorie de **données + stockage** pour « Protocole DocumentDB ». Cliquez sur **DocumentDB - prise en charge du protocole de MongoDB**.

    ![Capture d’écran du Marketplace et données + stockage recherche des cartes, mise en surbrillance DocumentDB - prise en charge du protocole de MongoDB, base de données Mongo](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. Par ailleurs, dans la catégorie de **données + stockage** , sous **stockage**, cliquez sur **plus**, puis cliquez sur **charger plus** une ou plusieurs fois pour afficher les **DocumentDB - prise en charge du protocole de MongoDB**. Cliquez sur **DocumentDB - prise en charge du protocole de MongoDB**.

    ![Capture d’écran de la Marketplace et de données + de stockage cartes, mise en surbrillance DocumentDB - prise en charge du protocole de MongoDB, base de données Mongo](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. Dans la carte **DocumentDB - prise en charge du protocole de MongoDB (preview)** , cliquez sur **créer** pour lancer le processus d’inscription (version préliminaire).

    ![La DocumentDB - prise en charge du protocole de carte MongoDB dans le portail Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. Dans la carte **DocumentDB compte** , cliquez sur **s’inscrire pour afficher un aperçu**. Lisez les informations, puis sur **OK**.

    ![Le signe jusqu'à cartes aujourd'hui Aperçu pour DocumentDB - prise en charge du protocole de MongoDB dans le portail Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  Après avoir accepté les termes du contrat preview, vous revenez à la carte de créer.  Dans la carte de **compte DocumentDB** , spécifiez la configuration souhaitée pour le compte.

    ![Capture d’écran de la nouvelle DocumentDB avec prise en charge du protocole de carte MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


    - Dans la zone **ID** , entrez un nom pour identifier le compte.  Lorsque l' **ID** est validé, une coche verte s’affiche dans la zone **ID** . La valeur **ID** devient le nom d’hôte dans l’URI. L' **ID** peut contenir uniquement des lettres minuscules, nombres et le «- » de caractères et doit être comprise entre 3 et 50 caractères. Notez que *documents.azure.com* est ajouté au nom du point de terminaison choisie, dont le résultat devient le point de terminaison de votre compte.

    - Pour l' **abonnement**, sélectionnez l’abonnement Azure que vous souhaitez utiliser pour le compte. Si votre compte est un abonnement, ce compte est sélectionné par défaut.

    - Dans le **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour le compte.  Par défaut, un groupe de ressources existant sous l’abonnement Azure sera choisi.  Vous pouvez, cependant, choisir de sélectionnez cette option pour créer un nouveau groupe de ressources auquel vous voulez ajouter le compte. Pour plus d’informations, voir [l’aide du portail pour gérer vos ressources Azure Azure](resource-group-portal.md).

    - **Emplacement** permet de spécifier l’emplacement géographique dans lesquelles vous pouvez héberger le compte.
    
    - Facultatif : À cocher **Ajouter au tableau de bord**. Si elle est épinglée au tableau de bord, suivez **étape 8** ci-dessous pour afficher la navigation à gauche de votre nouveau compte.

7.  Une fois que les nouvelles options de compte sont configurées, cliquez sur **créer**.  Il peut prendre quelques minutes pour créer le compte.  Si elle est épinglée au tableau de bord, vous pouvez surveiller l’avancement de mise en service sur le Startboard.  
    ![Capture d’écran de la mosaïque de création sur le Startboard - créateur de base de données en ligne](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)  

    Si ne pas épinglée au tableau de bord, vous pouvez surveiller l’avancement à partir du hub les Notifications.  

    ![Créer rapidement des bases de données - capture d’écran du hub de Notifications, indiquant que le compte DocumentDB est en cours de création](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)  

    ![Capture d’écran du concentrateur Notifications, indiquant que le compte DocumentDB a été correctement créé et déployé sur un groupe de ressources - notification créateur de base de données en ligne](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.  Pour accéder à votre nouveau compte, cliquez sur **DocumentDB (NoSQL)** dans le menu de gauche. Dans votre liste de DocumentDB normal et DocumentDB avec des comptes de prise en charge de protocole Mongo, cliquez sur nom de votre nouveau compte.

9.  Il est désormais prêt à utiliser avec les paramètres par défaut. 

    ![Capture d’écran de la cuillère compte par défaut](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
    

## <a name="next-steps"></a>Étapes suivantes


- Découvrez comment [vous connecter](documentdb-connect-mongodb-account.md) à un compte DocumentDB avec protocole prise en charge de MongoDB.

 
