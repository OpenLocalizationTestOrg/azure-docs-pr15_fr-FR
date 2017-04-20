<properties
    pageTitle="Comment utiliser le Kit de développement de JavaScript pour les applications mobiles Azure"
    description="Comment utiliser v pour applications Mobile Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>L’utilisation de la bibliothèque Client JavaScript pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous apprend à effectuer des scénarios courants à l’aide de la dernière [SDK JavaScript pour applications Mobile Azure]. Si vous débutez dans les applications Mobile Azure, tout d’abord terminer [Azure Mobile applications Quick Start] pour créer un serveur principal et créer une table. Dans ce guide, nous concentrer sur l’utilisation de la version serveur mobile dans les applications Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plateformes prises en charge

Nous limiter les navigateurs pris en charge aux versions actuelles et dernières des navigateurs principales : Google Chrome, Microsoft Edge, Microsoft Internet Explorer et Mozilla Firefox.  Nous prévoyons le Kit de développement pour fonctionner avec n’importe quel navigateur relativement moderne.

Le package est distribué comme un JavaScript Module universel, afin qu’il prend en charge globales, AMD, et CommonJS met en forme.

##<a name="Setup"></a>Le programme d’installation et configuration requise

Ce guide suppose que vous avez créé un serveur principal avec une table. Ce guide part du principe que la table a le même schéma que les tables dans ces didacticiels.

Installer le SDK Azure Mobile applications JavaScript peut être effectuée via la `npm` commande :

```
npm install azure-mobile-apps-client --save
```

Une fois installée, la bibliothèque se trouve dans `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copiez ce fichier dans la zone des web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La bibliothèque peut également être utilisée comme un module ES2015, dans les environnements CommonJS tels que Browserify et Webpack et sous la forme d’une bibliothèque de AMD.  Par exemple :

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Comment : authentification des utilisateurs

Azure Application Service prend en charge l’authentification et l’autorisation des utilisateurs de l’application à l’aide de divers fournisseurs d’identité externes : Facebook, Google, Account Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables à limiter l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation scripts de serveur. Pour plus d’informations, consultez le didacticiel [prise en main d’authentification] .

Deux flux d’authentification sont prises en charge : un flux de serveur et un flux de client.  Le flux server fournit l’expérience d’authentification la plus simple, car il repose sur l’interface du fournisseur web d’authentification. Le flux du client autorise pour intégration plus étroite avec des fonctionnalités spécifiques au périphérique, tel que seul-authentification car il repose sur SDK spécifiques du fournisseur.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Comment : configurer votre Service d’application Mobile pour les URL de redirection externes.

Plusieurs types d’applications JavaScript utilisent une fonctionnalité de boucle pour gérer les flux OAuth UI.  Ces fonctionnalités sont les suivantes :

* Exécuter votre service localement
* À l’aide de recharger Live avec l’infrastructure IONIQUE
* Redirection au Service d’application pour l’authentification. 

En cours d’exécution localement peut entraîner des problèmes car, par défaut, l’authentification du Service d’application est uniquement configurée pour autoriser l’accès à partir de votre serveur principal application Mobile. Procédez comme suit pour modifier les paramètres de Service d’application pour activer l’authentification lors de l’exécution du serveur local :

1. Connectez-vous au [portail Azure]
2. Accédez à votre serveur principal application Mobile.
3. Dans le menu **Outils de développement** , sélectionnez **Explorateur de ressources** .
4. Cliquez sur **Go** pour ouvrir l’Explorateur de ressources pour la principale application Mobile dans une fenêtre ou un nouvel onglet.
5. Développez la **configuration de la** > **authsettings** nœud de votre application.
6. Cliquez sur le bouton **Modifier** pour activer la modification de la ressource.
7. Recherchez l’élément **allowedExternalRedirectUrls** , qui doit être null. Ajouter votre URL dans un tableau :

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Remplacer les URL dans le tableau avec l’URL de votre service, c'est-à-dire dans cet exemple `http://localhost:3000` pour le service d’exemple Node.js local. Vous pouvez également utiliser `http://localhost:4400` pour le service ondulation ou certaines URL selon la configuration de votre application.

8. En haut de la page, cliquez sur **Lecture/écriture**, puis cliquez sur **placer** pour enregistrer vos mises à jour.

Vous devez également ajouter les mêmes URL en boucle les paramètres d’autorisation CORS :

1. Naviguer sur le [portail Azure].
2. Accédez à la principale de l’application Mobile.
3. Cliquez sur **CORS** dans le menu de **l’API** .
4. Entrez chaque URL dans la zone de texte vide **Origines autorisée** .  Une nouvelle zone de texte est créée.
5. Cliquez sur **Enregistrer**
    
Une fois que le serveur principal met à jour, vous ne pourrez pas utiliser les nouvelles URL en boucle dans votre application.

<!-- URLs. -->
[Guide de démarrage rapide d’applications mobiles Azure]: app-service-mobile-cordova-get-started.md
[Prise en main d’authentification]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portail Azure]: https://portal.azure.com/
[Kit de développement de JavaScript pour les applications mobiles Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

