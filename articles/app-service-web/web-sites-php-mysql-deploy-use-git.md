<properties
    pageTitle="Créer une application web PHP MySQL dans le Service d’application Azure et déployer à l’aide de Git"
    description="Didacticiel qui montre comment créer une application web PHP qui stocke les données dans MySQL et utilisez déploiement Git vers Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Créer une application web PHP MySQL dans le Service d’application Azure et déployer à l’aide de Git

Ce didacticiel vous montre comment créer une application web PHP MySQL et comment déployer au [Service d’application](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de Git. Vous allez utiliser [PHP][install-php], l’outil de ligne de commande MySQL (partie de [MySQL][install-mysql]) et [Git] [ install-git] installé sur votre ordinateur. Vous pouvez suivre les instructions présentées dans ce didacticiel sur tous les systèmes d’exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous avez une application web PHP/MySQL en cours d’exécution dans Azure.

Vous allez apprendre :

* Comment créer une application web et une base de données MySQL à l’aide du [Portail Azure][management-portal]. Étant donné que PHP est activée par défaut dans [L’application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) , rien de spécial est requis pour exécuter votre code PHP.
* Découvrez comment publier et publier à nouveau votre application Azure à l’aide de Git.
* Comment activer l’extension Composer automatiser Composer les tâches à chaque `git push`.

En suivant ce didacticiel, vous allez créer une application web d’inscription simple dans PHP. L’application sera hébergée dans les applications Web. Capture d’écran de l’application terminée est inférieur à :

![Site web de PHP Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

Ce didacticiel suppose que vous avez [PHP][install-php], l’outil de ligne de commande MySQL (partie de [MySQL][install-mysql]) et [Git] [ install-git] installé sur votre ordinateur.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Créer une application web et configurer la publication Git

Suivez ces étapes pour créer une application web et une base de données MySQL :

1. Connexion au [portail Azure][management-portal].
2. Cliquez sur l’icône **Nouveau** .

3. Cliquez sur **Afficher tous les** en regard de **Marketplace**. 

4. Cliquez sur **Web + Mobile**, puis **Web application + MySQL**. Ensuite, cliquez sur **créer**.

4. Entrez un nom pour votre groupe de ressources valide.

    ![Nom de groupe de ressources de jeu][resource-group]

5. Entrez des valeurs pour votre nouvelle application web.

    ![Créer dans le navigateur][new-web-app]

6. Entrez des valeurs pour votre nouvelle base de données, y compris les acceptez les conditions juridiques.

    ![Créer la nouvelle base de données MySQL][new-mysql-db]

7. Lorsque l’application web a été créée, vous verrez la nouvelle carte d’application web.

7. Dans **paramètres** cliquez sur **Déploiement continu**, puis cliquez sur _configurer les paramètres requis_.

    ![Configurer la publication Git][setup-publishing]

8. Sélectionnez **Référentiel Git Local** pour la source.

    ![Configurer la Git référentiel][setup-repository]


9. Pour activer la publication Git, vous devez fournir un nom d’utilisateur et mot de passe. Notez le nom d’utilisateur et mot de passe que vous créez. (Si vous avez configuré un référentiel Git avant, cette étape est ignorée.)

    ![Créer des informations d’identification de publication][credentials]


## <a name="get-remote-mysql-connection-information"></a>Obtenir des informations de connexion à distance MySQL

Pour vous connecter à la base de données MySQL est en cours d’exécution dans les applications Web, votre besoin des informations de connexion. Pour obtenir des informations de connexion MySQL, procédez comme suit :

1. À partir de votre groupe de ressources, cliquez sur la base de données :

    ![Sélectionnez la base de données][select-database]

2. Dans la base de données **paramètres**, cliquez sur **Propriétés**.

    ![Sélectionnez Propriétés][select-properties]

2. Prenez note des valeurs pour `Database`, `Host`, `User Id`, et `Password`.

    ![Propriétés de note][note-properties]

## <a name="build-and-test-your-app-locally"></a>Créer et tester votre application localement

Maintenant que vous avez créé une application web, vous pouvez développer votre application localement, puis déployer après le test.

La demande d’enregistrement est une application PHP simple qui permet de vous inscrire pour un événement en fournissant votre nom et adresse e-mail. Informations sur inscrites précédente sont affiche dans un tableau. Informations d’inscription sont stockées dans une base de données MySQL. L’application est composée d’un même fichier (code de copier/coller ci-dessous) :

* **index.php**: affiche un formulaire pour l’enregistrement et une table contenant des informations de l’abonné.

Pour créer et exécuter l’application localement, suivez les étapes ci-dessous. Notez que cette procédure suppose que vous disposez des PHP et MySQL Line Tool (partie MySQL) configurer sur votre ordinateur local, et que vous avez activé l' [extension AOP pour MySQL][pdo-mysql].

1. Se connecter au serveur MySQL distant, à l’aide de la valeur de `Data Source`, `User Id`, `Password`, et `Database` que vous avez récupérée précédemment :

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. L’invite MySQL s’affichent :

        mysql>

3. Collage dans ce qui suit `CREATE TABLE` commande pour créer la `registration_tbl` table dans votre base de données :

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Dans la racine de votre dossier application locale Créer fichier **index.php** .

5. Ouvrez le fichier **index.php** dans un éditeur de texte ou IDE et ajoutez le code suivant et effectuer les modifications nécessaires marquées avec `//TODO:` commentaires.


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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

4.  Dans un terminal accédez à votre dossier de l’application et tapez la commande suivante :

        php -S localhost:8000

Vous pouvez maintenant, accédez à **http://localhost:8000 /** pour tester l’application.


## <a name="publish-your-app"></a>Publier votre application

Une fois que vous avez testé votre application localement, vous pouvez le publier aux applications Web à l’aide de Git. Vous initialisation votre référentiel Git local et publier l’application.

> [AZURE.NOTE]
> Voici les mêmes étapes présentées dans le portail Azure à la fin de la créer une application web et définissez la section publication Git ci-dessus.

1. (Facultatif)  Si vous avez oublié ou perdu votre URL à distance repostitory Git, accédez aux propriétés de l’application web sur le portail Azure.

1. Ouvrez GitBash (ou un terminal, si Git se trouve dans votre `PATH`), accédez à la racine de votre application et exécutez les commandes suivantes :

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Vous demandera le mot de passe que vous avez créée.

    ![Envoi vers Azure via Git][git-initial-push]

2. Accédez à **http://[site name].azurewebsites.net/index.php** pour commencer à utiliser l’application (ces informations seront stockées sur votre tableau de bord compte) :

    ![Site web de PHP Azure][running-app]

Une fois que vous avez publié votre application, vous pouvez commencer à apporter des modifications à celui-ci et Git permet de les publier.

## <a name="publish-changes-to-your-app"></a>Publier les modifications apportées à votre application

Pour publier les modifications apportées à votre application, procédez comme suit :

1. Apporter des modifications à votre application localement.
2. Ouvrez GitBash (ou un terminal informatique Git se trouve dans votre `PATH`), accédez à la racine de votre application et exécutez les commandes suivantes :

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Vous demandera le mot de passe que vous avez créée.

    ![Distribution de modifications de site vers Azure via Git][git-change-push]

3. Accédez à **http://[site name].azurewebsites.net/index.php** pour afficher votre application et les modifications que vous avez apportées :

    ![Site web de PHP Azure][running-app]

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Automatisation des Composer avec l’extension Composer

Par défaut, le processus de déploiement git dans le Service d’application ne fait rien avec composer.json, si vous en avez dans votre projet PHP. Vous pouvez activer composer.json traitement pendant `git push` en activant l’extension Composer.

1. Dans votre PHP web carte de l’application dans le [portail Azure][management-portal], cliquez sur **Outils** > **Extensions**.

    ![Paramètres de l’Extension composer][composer-extension-settings]

2. Cliquez sur **Ajouter**, puis cliquez sur **Composer**.

    ![Ajouter de composer le numéro de poste][composer-extension-add]
    
3. Cliquez sur **OK** pour accepter les conditions juridiques. Cliquez sur **OK** pour ajouter l’extension.

    La carte **extensions installé** s’affichera l’extension Composer.  
    ![Composer Extension Affichage][composer-extension-view]
    
4. À présent, effectuer `git add`, `git commit`, et `git push` comme dans la section précédente. Vous voyez maintenant que Composer installe des dépendances définies dans composer.json.

    ![Composer Extension succès][composer-extension-success]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
