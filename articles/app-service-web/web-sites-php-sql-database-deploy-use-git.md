<properties 
    pageTitle="Créer une application web PHP SQL et déployer au Service d’application Azure à l’aide de Git" 
    description="Didacticiel qui montre comment créer une application web PHP qui stocke les données dans la base de données SQL Azure et utilisez déploiement Git Azure application service." 
    services="app-service\web, sql-database" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="create-a-php-sql-web-app-and-deploy-to-azure-app-service-using-git"></a>Créer une application web PHP SQL et déployer au Service d’application Azure à l’aide de Git

Ce didacticiel vous montre comment créer une application web PHP dans [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) qui se connecte à la base de données SQL Azure et comment déployer à l’aide de Git. Ce didacticiel suppose que vous avez [PHP][install-php], [SQL Server Express][install-SQLExpress], les [Pilotes Microsoft SQL Server pour PHP](http://www.microsoft.com/download/en/details.aspx?id=20098)et [Git] [ install-git] installé sur votre ordinateur. À la fin de ce guide, vous avez une application web PHP SQL en cours d’exécution dans Azure.

> [AZURE.NOTE]
> Vous pouvez installer et configurer PHP, SQL Server Express et les Drivers Microsoft pour SQL Server pour PHP à l’aide [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).

Vous allez apprendre :

* Comment créer une application web Azure et une base de données SQL à l’aide du [Portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Étant donné que PHP est activée par défaut dans l’application Service Web Apps, rien de spécial est requis pour exécuter votre code PHP.
* Découvrez comment publier et publier à nouveau votre application Azure à l’aide de Git.
 
En suivant ce didacticiel, vous allez générer une application web d’inscription simple dans PHP. L’application sera hébergée dans un site Web Azure. Capture d’écran de l’application terminée est inférieur à :

![Site Web PHP Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

##<a name="create-an-azure-web-app-and-set-up-git-publishing"></a>Créer une application web Azure et configurer la publication Git

Suivez ces étapes pour créer une application web Azure et une base de données SQL :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Ouvrir la Azure Marketplace en cliquant sur l’icône **Nouveau** dans le coin supérieur gauche du tableau de bord, cliquez sur **Sélectionner tout** en regard de Marketplace et en sélectionnant **Web + Mobile**.
    
3. Sur le marché, sélectionnez **Web + Mobile**.

4. Cliquez sur l’icône **application Web + SQL** .

5. Après la lecture de la description de l’application Web + application SQL, sélectionnez **créer**.

6. Cliquez sur chaque partie (**Groupe de ressources**, **Dans le navigateur**, **base de données**et **abonnement**) et entrez ou sélectionnez les valeurs pour les champs requis :
    
    - Entrez un nom d’URL de votre choix   
    - Configurer les informations d’identification du serveur de base de données
    - Sélectionnez le plus proche de vous la région

    ![configurer votre application](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)

7. Lorsque vous avez terminé la définition de l’application web, cliquez sur **créer**.

    Lorsque l’application web a été créée, le bouton **Notifications** clignote verte **Réussite** et la carte de groupe de ressources ouvrir pour afficher l’application web et la base de données SQL dans le groupe.

4. Cliquez sur icône de l’application web dans la carte du groupe de ressources pour ouvrir la carte de l’application web.

    ![groupe de ressources de l’application Web](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)

5. Dans **paramètres** , cliquez sur **continue déploiement** > **configurer les paramètres requis**. Sélectionnez **Référentiel Git Local** et cliquez sur **OK**.

    ![où se trouve votre code source](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)

    Si vous n’avez pas configuré un référentiel Git avant, vous devez fournir un nom d’utilisateur et mot de passe. Pour ce faire, cliquez sur **paramètres** > **informations d’identification de déploiement** dans la carte de l’application web.

    ![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)

6. Dans **paramètres** , cliquez sur **Propriétés** pour afficher l’URL à distance Git vous devez utiliser pour déployer votre application PHP ultérieurement.

##<a name="get-sql-database-connection-information"></a>Obtenir des informations de connexion de base de données SQL

Pour vous connecter à l’instance SQL de base de données qui est lié à votre application web, votre besoin les informations de connexion que vous avez spécifiée lorsque vous avez créé la base de données. Pour obtenir les informations de connexion de base de données SQL, procédez comme suit :

1. Dans la carte du groupe de ressources, cliquez sur icône de la base de données SQL.

2. Dans la carte de la base de données SQL, cliquez sur **paramètres** > **Propriétés**, puis cliquez sur **Afficher les chaînes de connexion de base de données**. 

    ![Afficher les propriétés de base de données](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
    
3. Dans la section **PHP** de la boîte de dialogue, prenez note des valeurs pour `Server`, `SQL Database`, et `User Name`. Vous allez utiliser ces valeurs ultérieurement lors de la publication de votre application web PHP au Service d’application Azure.

##<a name="build-and-test-your-application-locally"></a>Créer et tester votre application localement

La demande d’enregistrement est une application PHP simple qui permet de vous inscrire pour un événement en fournissant votre nom et adresse e-mail. Informations sur inscrites précédente sont affiche dans un tableau. Informations d’inscription sont stockées dans une instance de base de données SQL. L’application se compose de deux fichiers (code de copier/coller ci-dessous) :

* **index.php**: affiche un formulaire pour l’enregistrement et une table contenant des informations de l’abonné.
* **CreateTable.php**: crée la table de base de données SQL pour l’application. Ce fichier servira uniquement une fois.

Pour exécuter l’application localement, suivez les étapes ci-dessous. Notez que cette procédure suppose que vous avez PHP et SQL Server Express configurer sur votre ordinateur local, et que vous avez activé l' [extension AOP pour SQL Server][pdo-sqlsrv].

1. Créer une base de données SQL Server appelé `registration`. Vous pouvez le faire à partir de la `sqlcmd` invite de commandes avec ces commandes :

        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO   


2. Dans le répertoire racine de votre application, créez deux fichiers dedans - un appelé `createtable.php` et l’autre appelé `index.php`.

3. Ouvrir le `createtable.php` de fichier dans un éditeur de texte ou IDE et ajoutez le code ci-dessous. Ce code permet de créer la `registration_tbl` de tableau dans le `registration` base de données.

        <?php
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
            id INT NOT NULL IDENTITY(1,1) 
            PRIMARY KEY(id),
            name VARCHAR(30),
            email VARCHAR(30),
            date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    Notez que vous devez mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur SQL Server local et le mot de passe.

4. Dans un terminal dans le répertoire racine de l’application, tapez la commande suivante :

        php -S localhost:8000

4. Ouvrez un navigateur web et accédez à **http://localhost:8000/createtable.php**. Cela créera la `registration_tbl` table dans la base de données.

5. Ouvrez le fichier **index.php** dans un éditeur de texte ou IDE et ajoutez le code HTML, CSS base pour la page (le code PHP est ajouté dans les étapes suivantes).

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6. Dans les balises PHP, ajoutez le code PHP pour vous connecter à la base de données.

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    Là encore, vous devez mettre à jour les valeurs pour <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur MySQL local et le mot de passe.

7. Après le code de connexion de base de données, ajouter du code pour insérer des informations d’enregistrement dans la base de données.

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8. Enfin, selon le code ci-dessus, ajoutez du code pour extraire des données à partir de la base de données.

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

Vous pouvez désormais accéder à **http://localhost:8000/index.php** pour tester l’application.

##<a name="publish-your-application"></a>Publiez votre application

Une fois que vous avez testé votre application localement, vous pouvez le publier sur application Service Web Apps à l’aide de Git. Toutefois, vous devez tout d’abord mettre à jour les informations de connexion de base de données dans l’application. À l’aide des informations de connexion de base de données que vous avez obtenu précédemment (dans la section **informations de connexion de base de données SQL obtenir** ), mettre à jour les informations suivantes dans **à la fois** le `createdatabase.php` et `index.php` fichiers avec les valeurs appropriées :

    // DB connection info
    $host = "tcp:<value of Server>";
    $user = "<value of User Name>";
    $pwd = "<your password>";
    $db = "<value of SQL Database>";

> [AZURE.NOTE]
> Dans la <code>$host</code>, la valeur du serveur doit être précédée de <code>tcp:</code>.


À présent, vous êtes prêt à configurer la publication Git et publier l’application.

> [AZURE.NOTE]
> Voici les mêmes étapes de noter à la fin de la section **créer une application web Azure et configurer la publication Git** ci-dessus.


1. Ouvrez GitBash (ou un terminal, si Git se trouve dans votre `PATH`), accédez à la racine de votre application (répertoire **d’enregistrement** ) et exécutez les commandes suivantes :

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Vous demandera le mot de passe que vous avez créée.

2. Accédez à **http://[web application name].azurewebsites.net/createtable.php** pour créer la table de base de données SQL pour l’application.
3. Accédez à **http://[web application name].azurewebsites.net/index.php** pour commencer à utiliser l’application.

Une fois que vous avez publié votre application, vous pouvez commencer à apporter des modifications à celui-ci et Git permet de les publier. 

##<a name="publish-changes-to-your-application"></a>Publier les modifications apportées à votre application

Pour publier les modifications apportées à l’application, procédez comme suit :

1. Apporter des modifications à votre application localement.
2. Ouvrez GitBash (ou un terminal informatique Git se trouve dans votre `PATH`), accédez à la racine de votre application et exécutez les commandes suivantes :

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Vous demandera le mot de passe que vous avez créée.

3. Accédez à **http://[web application name].azurewebsites.net/index.php** pour voir les modifications.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)




[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
 
