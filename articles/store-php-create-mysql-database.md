<properties
    pageTitle="Créer et vous connecter à une base de données MySQL dans Azure"
    description="Découvrez comment utiliser le portail Azure pour créer une base de données MySQL et puis connecter à partir d’une application web PHP dans Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Créer et vous connecter à une base de données MySQL dans Azure

Ce didacticiel vous montre comment créer une base de données MySQL dans le [portail Azure](https://portal.azure.com) (fournisseur est [ClearDB](http://www.cleardb.com/)) et comment vous connecter à partir d’une application web PHP en cours d’exécution dans le [Service d’application Azure](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] Vous pouvez également créer une base de données MySQL dans le cadre d’un [modèle d’application Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Créer une base de données MySQL portail Azure

Pour créer une base de données MySQL dans le portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, cliquez sur **Nouveau** > **données + stockage** > **Base de données MySQL**.

    ![Créer une base de données MySQL dans Azure - début](./media/store-php-create-mysql-database/create-db-1-start.png)

2. Dans la nouvelle base de données MySQL [carte](azure-portal-overview.md), configurez votre nouvelle base de données MySQL comme suit (*carte*: une page de portail qui s’affiche horizontalement) :

    - **Nom de la base de données**: tapez un nom identifiable de façon unique
    - **Abonnement**: sélectionnez l’abonnement à utiliser
    - **Type de base de données**: sélectionnez **partagé** pour les niveaux de faible coût ou gratuits ou **dédié** obtenir des ressources dédiées. 
    - **Groupe de ressources**: ajouter la base de données MySQL à un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) existant ou le placer dans une nouvelle visualisation. Ressources dans le même groupe peuvent être facilement gérées ensemble.
    - **Emplacement**: sélectionnez un emplacement près de vous. Lorsque vous ajoutez à un groupe de ressources existant, vous êtes verrouillé à l’emplacement du groupe de ressources.
    - **Niveau de prix**: cliquez sur **Couche tarifs**, puis sélectionnez une option de tarification (niveau de**mercure** est gratuite), puis cliquez sur **Sélectionner**. 
    - **Conditions juridiques**: cliquez sur **Conditions juridiques**, passez en revue les détails de fournisseur, puis cliquez sur **acheter**.
    - **Épingler au tableau de bord**: sélectionnez si vous voulez accéder directement à partir du tableau de bord. Ceci est particulièrement utile si vous n’êtes pas familiarisé avec la navigation du portail encore.
    
    La capture d’écran suivante est qu’un exemple de la façon dont vous pouvez configurer votre base de données MySQL.  
    ![Créer une base de données MySQL dans Azure : configurer](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Lorsque vous avez terminé configuration, cliquez sur **créer**.

    ![Créer une base de données MySQL dans Azure - créer](./media/store-php-create-mysql-database/create-db-3-create.png)

    Vous verrez une notification contextuelle pour vous informer que déploiement a démarré.

    ![Créer une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Vous obtenez un autre contextuel une fois que le déploiement a réussi. Le portail s’ouvre également automatiquement votre carte de base de données MySQL.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Se connecter à votre base de données MySQL

Pour afficher les informations de connexion pour votre nouvelle base de données MySQL, cliquez sur **Propriétés** dans la carte de votre application web.
    
![Créer une base de données MySQL dans Azure - carte de base de données MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Vous pouvez désormais utiliser ces informations de connexion dans une application web. Un exemple qui indique comment utiliser les informations de connexion à partir d’une application PHP simple est disponible [ici](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Se connecter à une application web Laravel (dans le didacticiel PHP get mise en route)

Supposons que vous venez de terminer le didacticiel [créer, configurer et déployer une application web PHP vers Azure](./app-service-web/app-service-web-php-get-started.md) et disposez d’une application web [Laravel](https://www.laravel.com/) en cours d’exécution dans Azure. Vous pouvez facilement ajouter des fonctionnalités de base de données dans votre application Laravel. Il suffit de suivre les étapes suivantes :

>[AZURE.NOTE] Les étapes suivantes impliquent que vous avez terminé le didacticiel [créer, configurer et déployer une application web PHP vers Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configurer l’application Laravel dans votre environnement de développement local pour pointer vers la base de données MySQL. Pour ce faire, ouvrez `.env` à partir de votre application Laravel répertoire racine et configurez les options de base de données MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] Dans la carte de **Propriétés** , le nom de votre base de données MySQL peut ou peut ne pas être celle présentée dans le champ **Nom de la base de données** . Il est préférable vérifier le paramètre de base de données dans le champ **Chaîne de connexion** . 
    >
    >![Créer une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. Vérifiez que vous avez accès MySQL maintenant le moyen consiste à utiliser [la structure de l’authentification de Laravel par défaut](https://laravel.com/docs/5.2/authentication#authentication-quickstart). Dans le terminal de ligne de commande, exécutez les commandes suivantes à partir du répertoire racine de votre application Laravel :

         php artisan migrate
         php artisan make:auth

    La première commande crée les tables dans Azure basé sur des migrations prédéfinies dans le `database/migrations` directory et la deuxième commande micro-capsules affichages base et des itinéraires pour l’authentification et d’inscription des utilisateurs.

3. Exécuter le serveur de développement maintenant :

        php artisan serve

4. Dans le navigateur, accédez à http://localhost:8000 et enregistrer un nouvel utilisateur comme indiqué :

    ![Se connecter à la base de données MySQL dans Azure - inscrire un utilisateur](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Suivez l’invite de l’interface utilisateur terminé l’enregistrement. Une fois l’inscription terminée, vous serez connecté.
    
    ![Se connecter à la base de données MySQL dans Azure - inscrire un utilisateur](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Vous développez votre application sur la base de données MySQL dans Azure maintenant.

5. À présent, il vous suffit répliquer votre `.env` paramètres dans votre application web Azure. Exécutez les commandes Azure infrastructure du langage commun suivantes :

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Découvrez comment cela fonctionne dans [configurer l’application web Azure](./app-service-web/app-service-web-php-get-started.md#configure).

6. Ensuite, valider et les pousser vers Azure les modifications locales apportées précédemment en cours d’exécution `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Accédez à l’application web Azure.

        azure site browse

8. Connectez-vous en utilisant les informations d’identification utilisateur que vous avez créée.

    ![Se connecter à la base de données MySQL dans Azure - accédez à Azure dans le navigateur](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Après que vous être connecté, vous devez voir l’écran de connexion après convivial.
    
    ![Se connecter à la base de données MySQL dans Azure - connecté](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Félicitations, votre application web PHP dans Azure est désormais l’accès aux données de votre base de données MySQL. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement PHP](/develop/php/).
