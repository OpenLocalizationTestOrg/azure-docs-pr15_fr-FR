<properties 
    pageTitle="Se connecter à un compte DocumentDB avec prise en charge du protocole de MongoDB | Microsoft Azure" 
    description="Découvrez comment vous connecter à un compte DocumentDB avec prise en charge du protocole de MongoDB, désormais disponible pour l’aperçu. Se connecter à l’aide de la chaîne de connexion MongoDB." 
    keywords="chaîne de connexion MongoDB"
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
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Comment se connecter à un compte DocumentDB avec prise en charge du protocole de MongoDB

Découvrez comment vous connecter à un compte Azure DocumentDB avec prise en charge du protocole de MongoDB utilisant le format d’URI de chaîne de connexion MongoDB standard.  

## <a name="get-the-accounts-connection-string-information"></a>Obtenir des informations de chaîne de connexion de compte

1. Dans une nouvelle fenêtre, connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre de **Navigation de gauche** de la cuillère de compte, cliquez sur **Chaîne de connexion**. Pour accéder à la **Carte de compte**, sur le Jumpbar cliquez sur **d’Autres Services**et cliquez sur **DocumentDB (NoSQL)** , puis sélectionnez le compte DocumentDB avec prise en charge du protocole de MongoDB.

    ![Capture d’écran de la cuillère tous les paramètres](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. La carte **d’Informations sur la chaîne de connexion** s’ouvre et comporte toutes les informations nécessaires pour vous connecter à ce compte à l’aide d’un pilote pour MongoDB, y compris une chaîne de connexion préalable construit.

    ![Capture d’écran de la cuillère de chaîne de connexion](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Configuration requise de chaîne de connexion

Il est important de noter que DocumentDB prend en charge le MongoDB connexion chaîne URI format standard, avec quelques exigences spécifiques : comptes DocumentDB requièrent l’authentification et communication sécurisée via SSL.  Par conséquent, le format de chaîne de connexion est la suivante :

    mongodb://username:password@host:port/[database]?ssl=true

Où les valeurs de cette chaîne sont disponibles dans la carte de chaîne de connexion ci-dessus.

- Nom d’utilisateur (obligatoire)
    - Nom du compte DocumentDB
- Mot de passe (obligatoire)
    - Mot de passe du compte DocumentDB
- Host (obligatoire)
    - Nom de domaine complet de DocumentDB compte
- Port (obligatoire)
    - 10250
- Base de données (facultatif)
    - La base de données par défaut utilisé par la connexion
- SSL = true (obligatoire)

Par exemple, considérez le compte affiché dans les informations de chaîne de connexion ci-dessus.  Une chaîne de connexion valide est la suivante :
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>Connectez le pilote c# pour MongoDB
Comme indiqué précédemment, tous les comptes DocumentDB nécessitent l’authentification et communication sécurisée via SSL. Si le format d’URI MongoDB connexion chaîne prend en charge une connexion ssl = paramètre de chaîne de requête true, travailler avec MongoDB c# pilote nécessite l’utilisation de l’objet MongoClientSettings lors de la création d’un MongoClient.  Étant donné les informations de compte ci-dessus, l’extrait de code suivant montre comment vous connecter à ce compte et travailler avec la base de données « Tâches ».

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Étapes suivantes


- Découvrez comment [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte DocumentDB avec protocole prise en charge de MongoDB.
- Explorer DocumentDB avec prise en charge du protocole de MongoDB [exemples](documentdb-mongodb-samples.md).

 
