<properties 
    pageTitle="Utilisez MongoChef avec un compte DocumentDB avec prise en charge du protocole de MongoDB | Microsoft Azure" 
    description="Découvrez comment utiliser MongoChef avec un compte DocumentDB avec prise en charge du protocole de MongoDB, désormais disponible pour l’aperçu." 
    keywords="mongochef"
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
    ms.date="08/25/2016" 
    ms.author="anhoh"/>

# <a name="use-mongochef-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Utilisez MongoChef avec un compte DocumentDB avec prise en charge du protocole de MongoDB

Pour vous connecter à un compte Azure DocumentDB avec prise en charge du protocole de MongoDB à l’aide de MongoChef, vous devez :

- Téléchargez et installez [MongoChef](http://3t.io/mongochef)
- Disposez votre compte DocumentDB avec prise en charge du protocole pour les informations de la [chaîne de connexion](documentdb-connect-mongodb-account.md) MongoDB

## <a name="create-the-connection-in-mongochef"></a>Créer la connexion dans MongoChef  

Pour ajouter votre compte DocumentDB avec prise en charge du protocole de MongoDB pour le Gestionnaire de connexions MongoChef, procédez comme suit.

1. Récupérer votre DocumentDB avec prise en charge du protocole MongoDB informations de connexion en suivant les instructions [ici](documentdb-connect-mongodb-account.md).

    ![Capture d’écran de la cuillère de chaîne de connexion](./media/documentdb-mongodb-mongochef/ConnectionStringBlade.png)

2. Cliquez sur **se connecter** pour ouvrir le Gestionnaire de connexions, puis cliquez sur **Nouvelle connexion**

    ![Capture d’écran du Gestionnaire de connexion MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
    
2. Dans la fenêtre **Nouvelle connexion** , sous l’onglet **serveur** , entrez l’hôte (FQDN) du compte DocumentDB avec prise en charge du protocole pour MongoDB et le PORT.
    
    ![Capture d’écran de l’onglet MongoChef connexion Gestionnaire de serveur](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. Dans la fenêtre **Nouvelle connexion** , sous l’onglet **authentification** , choisissez le Mode d’authentification **Standard (MONGODB CR ou SCARM-ça-1)** et entrez le nom d’utilisateur et mot de passe.  Acceptez la base de données d’authentification par défaut (admin) ou indiquez votre propre valeur.

    ![Capture d’écran de l’onglet MongoChef connexion gestionnaire d’authentification](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. Dans la fenêtre **Nouvelle connexion** , sous l’onglet **SSL** , cochez la case à cocher **protocole utiliser SSL pour se connecter** et le bouton d’option **certificats SSL auto-signé accepter** .

    ![Capture d’écran de l’onglet MongoChef connexion gestionnaire SSL](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. Cliquez sur le bouton **Tester la connexion** pour valider les informations de connexion, cliquez sur **OK** pour revenir à la fenêtre Nouvelle connexion, puis cliquez sur **Enregistrer**.

    ![Capture d’écran de la fenêtre de connexion de test MongoChef](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>MongoChef permet de créer une base de données, collection de sites et des documents  

Pour créer une base de données, la collection de sites et des documents à l’aide de MongoChef, effectuent les opérations suivantes.

1. Dans le **Gestionnaire de connexions**, sélectionnez la connexion, puis cliquez sur **se connecter**.

    ![Capture d’écran du Gestionnaire de connexion MongoChef](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. Cliquez avec le bouton droit sur l’hôte et choisissez **Ajouter une base de données**.  Indiquez un nom de base de données, cliquez sur **OK**.
    
    ![Capture d’écran de l’option de base de données MongoChef ajouter](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. Cliquez avec le bouton droit sur la base de données et cliquez sur **Ajouter la collection de sites**.  Indiquez un nom de la collection de sites, cliquez sur **créer**.

    ![Capture d’écran de l’option Ajouter la Collection MongoChef](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. Cliquez sur l’élément de menu **collection de sites** , puis cliquez sur **Ajouter un Document**.

    ![Capture d’écran de l’élément de menu MongoChef ajouter un Document](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. Dans la boîte de dialogue Ajouter un Document, collez les éléments suivants, puis sur **Ajouter un Document**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
            { "firstName": "Thomas" },
            { "firstName": "Mary Kay"}
        ],
        "children": [
        {
            "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
            "pets": [{ "givenName": "Fluffy" }]
        }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }

    
6. Ajouter un autre document, cette fois avec le contenu suivant.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                "givenName": "Lisa", 
                "gender": "female", 
                "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }

7. Exécuter un exemple de requête. Par exemple, recherchez familles avec le nom « Andersen » et retourner les parents et les champs d’état.

    ![Capture d’écran des résultats de la requête Mongo Chef](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
    

## <a name="next-steps"></a>Étapes suivantes

- Explorer DocumentDB avec prise en charge du protocole de MongoDB [exemples](documentdb-mongodb-samples.md).

 
