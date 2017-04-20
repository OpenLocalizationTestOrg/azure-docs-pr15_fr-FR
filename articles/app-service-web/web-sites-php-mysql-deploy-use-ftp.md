<properties 
    pageTitle="Créer une application web PHP MySQL dans le Service d’application Azure et déployer à l’aide de FTP" 
    description="Didacticiel qui montre comment créer une application web PHP qui stocke les données MySQL et l’utilisation déploiement FTP à Azure." 
    services="app-service\web" 
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


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Créer une application web PHP MySQL dans le Service d’application Azure et déployer à l’aide de FTP

Ce didacticiel vous montre comment créer une application web PHP MySQL et comment déployer à l’aide de FTP. Ce didacticiel suppose que vous avez [PHP][install-php], [MySQL][install-mysql], un serveur web et un client FTP installé sur votre ordinateur. Vous pouvez suivre les instructions présentées dans ce didacticiel sur tous les systèmes d’exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous avez une application web PHP/MySQL en cours d’exécution dans Azure.
 
Vous allez apprendre :

* Comment créer une application web et une base de données MySQL à l’aide du portail Azure. Étant donné que PHP est activé dans les applications Web par défaut, rien de spécial est requis pour exécuter votre code PHP.
* Découvrez comment publier votre application sur Azure à l’aide de FTP.
 
En suivant ce didacticiel, vous allez créer une application web d’inscription simple dans PHP. L’application sera hébergée dans une application Web. Capture d’écran de l’application terminée est inférieur à :

![Site Web PHP Azure][running-app]

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit n’obligatoire, aucune engagements. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Créer une application web et configurer la publication FTP

Suivez ces étapes pour créer une application web et une base de données MySQL :

1. Connexion au [portail Azure][management-portal].
2. Cliquez sur l’icône **+ Nouveau** dans le coin supérieur gauche du portail Azure.

    ![Créer le nouveau Site Web Azure][new-website]

3. Dans la recherche, appuyez **sur application Web + MySQL** et cliquez sur **application Web + MySQL**.

    ![Personnalisé créer un nouveau Site Web][custom-create]

4. Cliquez sur **créer**. Entrez un nom de service d’application unique, un nom valide pour le groupe de ressources et une nouvelle offre de service.

    ![Nom de groupe de ressources de jeu][resource-group]


6. Entrez des valeurs pour votre nouvelle base de données, y compris les acceptez les conditions juridiques.

    ![Créer la nouvelle base de données MySQL][new-mysql-db]
    
7. Lorsque l’application web a été créée, vous verrez la nouvelle carte de service application.


6. Cliquez sur **paramètres** > **informations d’identification de déploiement**. 

    ![Définir les informations d’identification du déploiement][set-deployment-credentials]

7. Pour activer la publication FTP, vous devez fournir un nom d’utilisateur et mot de passe. Enregistrer les informations d’identification et notez le nom d’utilisateur et mot de passe que vous créez.

    ![Créer des informations d’identification de publication][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Créer et tester votre application localement

La demande d’enregistrement est une application PHP simple qui permet de vous inscrire pour un événement en fournissant votre nom et adresse e-mail. Informations sur inscrites précédente sont affiche dans un tableau. Informations d’inscription sont stockées dans une base de données MySQL. L’application se compose de deux fichiers :

* **index.php**: affiche un formulaire pour l’enregistrement et une table contenant des informations de l’abonné.
* **CreateTable.php**: crée la table MySQL pour l’application. Ce fichier servira uniquement une fois.

Pour créer et exécuter l’application localement, suivez les étapes ci-dessous. Notez que ces étapes présupposent que vous PHP, MySQL, et un serveur web configurer sur votre ordinateur local et que vous avez activé l' [extension AOP pour MySQL][pdo-mysql].

1. Créer une base de données MySQL appelé `registration`. Pour cela, accédez à l’invite de commande MySQL avec cette commande :

        mysql> create database registration;

2. Dans le répertoire racine de votre serveur web, créez un dossier nommé `registration` et créer deux fichiers dedans - un appelé `createtable.php` et l’autre appelé `index.php`.

3. Ouvrir le `createtable.php` de fichier dans un éditeur de texte ou IDE et ajoutez le code ci-dessous. Ce code permet de créer la `registration_tbl` de tableau dans le `registration` base de données.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
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

    > [AZURE.NOTE] 
    > Vous devez mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur MySQL local et le mot de passe.

4. Ouvrez un navigateur web et accédez à [http://localhost/registration/createtable.php][localhost-createtable]. Cela créera la `registration_tbl` table dans la base de données.

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
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > Là encore, vous devez mettre à jour les valeurs pour <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur MySQL local et le mot de passe.

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

Vous pouvez maintenant, accédez à [http://localhost/registration/index.php] [ localhost-index] pour tester l’application.

##<a name="get-mysql-and-ftp-connection-information"></a>Obtenir des informations de connexion MySQL et FTP

Pour vous connecter à la base de données MySQL est en cours d’exécution dans les applications Web, votre besoin des informations de connexion. Pour obtenir des informations de connexion MySQL, procédez comme suit :

1. À partir du service d’application carte d’application web cliquez sur le lien de groupe de ressources :

    ![Sélectionner le groupe de ressources][select-resourcegroup]

1. À partir de votre groupe de ressources, cliquez sur la base de données :

    ![Sélectionnez la base de données][select-database]

2. À partir de la base de données récapitulative, sélectionnez **paramètres** > **Propriétés**.

    ![Sélectionnez Propriétés][select-properties]
    
2. Prenez note des valeurs pour `Database`, `Host`, `User Id`, et `Password`.

    ![Propriétés de note][note-properties]

3. À partir de votre application web, cliquez sur le lien de **téléchargement publier le profil** dans le coin inférieur droit de la page :

    ![Téléchargement publier le profil][download-publish-profile]

4. Ouvrir le `.publishsettings` fichier dans un éditeur XML. 

3. Trouver la `<publishProfile >` élément avec `publishMethod="FTP"` qui ressemble à ceci :

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Notez la `publishUrl`, `userName`, et `userPWD` attributs.

##<a name="publish-your-app"></a>Publier votre application

Une fois que vous avez testé votre application localement, vous pouvez le publier dans votre application web à l’aide de FTP. Toutefois, vous devez tout d’abord mettre à jour les informations de connexion de base de données dans l’application. À l’aide des informations de connexion de base de données que vous avez obtenu précédemment (dans la section **obtenir MySQL et informations de connexion FTP** ), de mettre à jour les informations suivantes dans **à la fois** le `createdatabase.php` et `index.php` fichiers avec les valeurs appropriées :

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Vous êtes maintenant prêt à publier votre application à l’aide de FTP.

1. Ouvrez votre client FTP de choix.

2. Entrez la *portion de nom d’hôte* à partir de la `publishUrl` attribut ci-dessus dans votre client FTP.

3. Entrez la `userName` et `userPWD` attributs que vous avez noté ci-dessus inchangée dans votre client FTP.

4. Établir une connexion.

Une fois que vous avez connectés vous ne pourrez pas charger et télécharger des fichiers selon vos besoins. N’oubliez pas que vous téléchargez des fichiers dans le répertoire racine, qui est `/site/wwwroot`.

Après avoir téléchargé les deux `index.php` et `createtable.php`, accédez à **http://[site name].azurewebsites.net/createtable.php** pour créer la table MySQL pour l’application, puis naviguez vers **http://[site name].azurewebsites.net/index.php** pour commencer à utiliser l’application.
 
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
