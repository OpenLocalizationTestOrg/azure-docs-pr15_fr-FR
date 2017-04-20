<properties
    pageTitle="Mise à niveau à partir de Services mobiles au Service application Azure - Node.js"
    description="Découvrez comment mettre à niveau facilement votre application de Services mobiles pour une application application Service Mobile"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Mise à niveau de Service Mobile Azure Node.js existant au Service d’application

Application Service Mobile est une nouvelle méthode pour créer des applications mobiles à l’aide de Microsoft Azure. Pour plus d’informations, voir [Quelles sont les applications Mobile ?].

Cette rubrique décrit comment mettre à niveau une application de serveur principal Node.js existante à partir d’Azure Mobile Services à un nouveau les applications application Service Mobile. Pendant que vous effectuez cette mise à niveau, votre application de Services mobiles existante peut continuer à fonctionner.  Si vous devez mettre à niveau une application principale Node.js, reportez-vous à [la mise à niveau vos Services Mobile .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Lorsqu’un serveur principal mobile est mis à niveau vers Azure Application Service, il a accès à toutes les fonctionnalités de Service d’application et facturés en fonction de l' [application Service tarifs], pas les Services mobiles tarifs.

## <a name="migrate-vs-upgrade"></a>Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Il est recommandé d' [effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant d’utiliser une mise à niveau. Ainsi, vous pouvez placer les deux versions de votre application sur le même Plan de Service de l’application et subir sans frais supplémentaires.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Améliorations dans Mobile applications Node.js server (SDK)

Mise à niveau vers le nouveau [Kit de développement Mobile applications](https://www.npmjs.com/package/azure-mobile-apps) fournit de nombreuses améliorations, notamment :

- En fonction de [Express framework](http://expressjs.com/en/index.html), le nouveau SDK nœud est légers et conçu pour être informé des nouvelles versions nœud lorsqu’ils se. Vous pouvez personnaliser le comportement de l’application avec les logiciels intermédiaires Express.

- Amélioration des performances significative par rapport au Kit de développement de Services mobiles.

- Vous pouvez désormais héberger un site Web avec votre version de serveur mobile ; de même, il est facile d’ajouter le Kit de développement Mobile Azure à n’importe quelle application express.v4 existant.

- Conçu pour le développement disponibilité sur plusieurs plateformes et local, le Kit de développement des applications mobiles peuvent être développé et exécuté localement sur les plateformes Windows, Linux et OSX. Il est facile d’utilisation techniques de développement nœud courantes telles que l’exécution des tests [Mocha](https://mochajs.org/) avant le déploiement.

## <a name="overview"></a>Vue d’ensemble de la mise à niveau simple

Pour faciliter la mise à niveau d’un serveur principal Node.js, Azure Application Service a donné un package de compatibilité.  Après mise à niveau, vous aurez accès à un site niew qui peut être déployé vers un nouveau site de Service d’application.

Le client de Services mobiles SDK sont **pas** compatibles avec le nouveau serveur d’applications Mobile SDK. Afin d’assurer la continuité de service pour votre application, vous devez publier pas modifications à un site qui utilise des clients publiés. À la place, vous devez créer une nouvelle application mobile qui sert un doublon. Vous pouvez placer cette application sur le même plan de Service d’application pour éviter entraîner coût financier supplémentaire.

Vous aurez alors deux versions de l’application : celle reste la même et gère publiés applications dans la nature et l’autre dont vous pouvez ensuite mettre à niveau et cibles avec une nouvelle version du client. Vous pouvez déplacer et testez votre code à votre rythme, mais vous devez vous assurer que les correctifs que vous rendre appliqués aux deux. Une fois que vous estimez qu’un nombre client applications dans la nature ont mis à jour vers la dernière version de votre choix, vous pouvez supprimer l’application a été déplacée d’origine si vous le souhaitez. Il ne payantes toute monétaire, si hébergé dans le même plan de Service d’application que votre application Mobile.

Le plan complè pour le processus de mise à niveau se présente comme suit :

1. Téléchargez votre Service de Mobile Azure (migré) existant.
2. Convertir le projet pour une application Mobile Azure en utilisant le package de compatibilité.
3. Corrigez les éventuelles différences (par exemple, les paramètres d’authentification).
4. Déployez votre projet de l’application Mobile Azure converti vers un nouveau Service d’application.
4. Publiez une nouvelle version de votre application cliente qui utilisent la nouvelle application Mobile.
5. (Facultatif) Supprimer votre application de service mobile a été déplacée d’origine.

Suppression peut se produire lorsque vous ne voyez pas le trafic de votre service mobile a été déplacée d’origine.

## <a name="install-npm-package"></a>Installer les conditions préalables

Vous devez installer [nœud] sur votre ordinateur local.  Vous devez également installer le package de compatibilité.  Une fois que le nœud est installé, vous pouvez exécuter la commande suivante à partir d’un nouveau cmd ou Invite PowerShell :

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Obtenir vos Scripts de Services mobiles Azure

- Connectez-vous au [portail Azure].
- À l’aide de **Toutes les ressources** ou les **Services d’application**, recherchez votre site de Services mobiles.
- Au sein du site, cliquez sur **Outils** -> **Kudu** -> **accédez** pour ouvrir le site Kudu.
- Cliquez sur **Console de débogage** -> **PowerShell** pour ouvrir la console de débogage.
- Accédez à `site/wwwroot/App_Data/config` en cliquant sur chaque répertoire à son tour
- Cliquez sur l’icône de téléchargement à côté du `scripts` répertoire.

Cela va télécharger les scripts au format ZIP.  Créer un nouveau répertoire sur votre ordinateur local et décompresser le `scripts.ZIP` fichier dans l’annuaire.  Cela créera un `scripts` répertoire.

## <a name="scaffold-app"></a>Structure le système principal de nouvelles applications Mobile Azure

Exécutez la commande suivante à partir du répertoire contenant le répertoire scripts :

```scaffold-mobile-app scripts out```

Cela créera un serveur principal Azure Mobile applications avec génération de modèles dans le `out` répertoire.  Bien que ne pas obligatoire, il est recommandé de vérifier la `out` répertoire dans un référentiel de code source de votre choix.

## <a name="deploy-ama-app"></a>Déployer la principale applications Mobile Azure

Au cours du déploiement, vous devez effectuer les opérations suivantes :

1. Créer une nouvelle application Mobile dans le [Portail Azure].
2. Exécuter le `createViews.sql` script dans votre base de données connectée.
3. Lien de la base de données qui est lié à votre Service Mobile vers votre nouveau Service d’application.
4. Lien d’autres ressources (par exemple, de Notification Hubs) vers le nouveau Service d’application.
5. Déployer le code généré vers votre nouveau site.

### <a name="create-a-new-mobile-app"></a>Créer une nouvelle application Mobile

1. Ouvrez une session sur le [portail Azure].

2. Cliquez sur **+ Nouveau** > **Web + Mobile** > **Application Mobile**, puis attribuez un nom à votre version de serveur de l’application Mobile.

3. Pour le **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un nouveau (en utilisant le même nom en tant que votre application.) 
 
    Vous pouvez sélectionner un autre plan de services d’application ou créez-en une. Pour plus d’informations sur les Services d’application plans et comment créer un plan dans un autre tarifs niveau et dans l’emplacement souhaité, voir [vue d’ensemble détaillée de Service d’application Azure offres](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Pour le **plan de services d’application**, le plan par défaut (dans la [couche Standard](https://azure.microsoft.com/pricing/details/app-service/)) est sélectionné. Vous pouvez également sélectionner un autre plan ou [créez-en une](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Paramètres de l’offre Service d’application déterminent les [emplacement, fonctionnalités, des coûts et ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associé à votre application. 

    Après avoir décidé du plan, cliquez sur **créer**. Cela crée le système principal de l’application Mobile. 


### <a name="run-createviewssql"></a>Exécuter CreateViews.SQL

L’application avec génération de modèles contient un fichier nommé `createViews.sql`.  Ce script doit être exécuté sur la base de données cible.  Chaîne de connexion pour la base de données cible peut provenir de votre service mobile a été déplacée à partir de la carte de **paramètres** sous **Chaînes de connexion**.  Il est nommé `MS_TableConnectionString`.

Vous pouvez exécuter ce script à partir de SQL Server Management Studio ou Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Lien de la base de données à votre Service d’application

Lier la base de données existante à votre Service d’application :

- Dans le [Portail Azure], ouvrez votre application de Service.
- Sélectionnez **tous les paramètres** -> **des connexions de données**.
- Cliquez sur **+ Ajouter**.
- Dans le menu déroulant, sélectionnez **Base de données SQL**
- Sous **Base de données SQL**, sélectionnez votre base de données existante, puis cliquez sur **Sélectionner**.
- Sous la **chaîne de connexion**, entrez le nom d’utilisateur et mot de passe pour la base de données, puis cliquez sur **OK**.
- Dans la carte **Ajouter des connexions de données** , cliquez sur **OK**.

Le nom d’utilisateur et mot de passe, vous pouvez trouver en affichant la chaîne de connexion pour la base de données cible dans votre Service Mobile a été déplacée.


### <a name="set-up-authentication"></a>Configurer l’authentification

Azure applications Mobile vous permet de configurer Azure Active Directory, Facebook, Google, Microsoft et Twitter l’authentification au sein du service.  Authentification personnalisée doit être développée séparément.  Consultez la documentation de [Concepts d’authentification] et [Démarrage rapide d’authentification] pour plus d’informations.  

## <a name="updating-clients"></a>Mettre à jour les clients mobiles

Une fois que vous avez un serveur principal application Mobile opérationnel, vous pouvez travailler sur une nouvelle version de votre application cliente qui consomme. Applications Mobile inclut également une nouvelle version du client SDK et similaire à la mise à niveau du serveur ci-dessus, vous devrez supprimer toutes les références aux kits de développement logiciel Mobile Services avant d’installer les versions applications Mobile.

Un des principaux changements entre les versions est que les constructeurs n’avez plus besoin d’une touche application. Vous maintenant simplement passer dans l’URL de votre application Mobile. Par exemple, sur les clients .NET, la `MobileServiceClient` constructeur est maintenant :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Vous pouvez lire sur l’installation du nouveau SDK et à l’aide de la nouvelle structure via les liens ci-dessous :

- [Android 2.2 ou version ultérieure](app-service-mobile-android-how-to-use-client-library.md)
- [iOS version 3.0.0 ou version ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 ou version ultérieure](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Cordova Apache version 2.0 ou version ultérieure](app-service-mobile-cordova-how-to-use-client-library.md)

Si votre application effectue utiliser des notifications push, notez les instructions d’enregistrement spécifique pour chaque plate-forme, comme des modifications ont été certaines il également.

Lorsque vous avez la nouvelle version du client prête, essayez-le par rapport à votre projet serveur mis à niveau. Après avoir validé qu’il fonctionne, vous pouvez lancer une nouvelle version de votre application à des clients. Au final, une fois que vos clients ont pu recevoir ces mises à jour, vous pouvez supprimer la version de Services mobiles de votre application. À ce stade, vous avez complètement mis à niveau vers un Service Mobile application à l’aide de la dernière server applications Mobile SDK.

<!-- URLs. -->

[Portail Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Quelles sont les applications Mobile ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Service d’application tarifs]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Concepts d’authentification]: ../app-service/app-service-authentication-overview.md
[Démarrage rapide d’authentification]: app-service-mobile-auth.md

[Portail Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
