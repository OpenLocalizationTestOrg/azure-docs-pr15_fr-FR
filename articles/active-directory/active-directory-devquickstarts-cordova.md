<properties
    pageTitle="Azure AD Cordova mise en route | Microsoft Azure"
    description="Découvrez comment créer une application Cordova qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Intégrer Azure AD un Apache Cordova application

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova permet de développer des applications HTML5/JavaScript qui peuvent être exécutée sur les appareils mobiles que des applications natives à part entière.
Avec Azure Active Directory, vous pouvez ajouter des fonctionnalités d’authentification entreprise notes à vos applications Cordova. Grâce à un plug-in Cordova habillage SDK native d’Azure AD sur iOS, Android, du Windows Store et Windows Phone, vous pouvez améliorer votre application prend en charge se sur avec les comptes Active Directory vos utilisateurs, accéder à Office 365 et Azure API et même protéger des appels à votre propre API Web personnalisée.

Dans ce didacticiel, nous utiliserons le plug-in Cordova Apache pour Active Directory authentification bibliothèque (terme ADAL) pour améliorer une application simple avec les fonctionnalités suivantes :

-   Avec quelques lignes de code, permettent d’authentifier un utilisateur AD et obtenir un jeton pour appeler l’API Azure AD Graph.
-   Utilisation de ce jeton d’appeler l’API Graph pour ce répertoire de la requête et afficher les résultats  
-   Utiliser le cache de jetons ADAL pour réduire les invites d’authentification de l’utilisateur.

Pour ce faire, vous devez :

2. Enregistrer une application avec Azure Active Directory
2. Ajouter du code à votre application demande jetons
3. Ajouter du code pour utiliser le jeton pour l’interrogation de l’API Graph et afficher des résultats.
4. Créer le projet de déploiement Cordova avec toutes les plateformes que vous souhaitez cibler et le plug-in Cordova ADAL et tester la solution dans émulateurs.

## <a name="0--prerequisites"></a>*0. conditions préalables*

Pour effectuer ce didacticiel, que vous devez :

- Un client Azure AD dans lequel vous avez un compte disposant de droits de développement d’application
- Un environnement de développement configuré pour utiliser Cordova Apache  

Si vous avez deux déjà configuré, passez directement à l’étape 1.

Si vous n’avez pas un client Azure AD, vous pouvez trouver [obtenir des instructions sur comment obtenir un ici](active-directory-howto-tenant.md).

Si vous n’avez pas Cordova Apache configurer sur votre ordinateur, installez les éléments suivants :

- [GIT](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Infrastructure du langage commun Cordova](https://cordova.apache.org/) (peut être installé aisément via le Gestionnaire de package NPM : `npm install -g cordova`)

Notez qu’ils doivent fonctionnent à la fois sur le PC et Mac.

Chaque plate-forme cible comporte différentes conditions requises.

- Créer et exécuter Windows Tablet/PC ou app version du téléphone
    - [Visual Studio 2013 pour Windows avec la mise à jour 2 ou version ultérieure](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou une autre version).
- Créer et exécuter pour iOS
    -   Xcode 5.x ou version ultérieure. Télécharger en http://developer.apple.com/downloads ou sur le [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios Assistant](https://www.npmjs.org/package/ios-sim) – vous permet de lancer des applications iOS sur iOS Simulator à partir de la ligne de commande (peut être installé aisément via le terminal : `npm install -g ios-sim`)

- Pour créer et exécuter application pour Android
    - Installez le [Kit de développement de Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou version ultérieure. Vérifiez que `JAVA_HOME` (Variable d’environnement) est correctement définie en fonction du chemin d’installation JDK (par exemple C:\Program Files\Java\jdk1.7.0_75).
    - Installer [SDK Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) et ajouter `<android-sdk-location>\tools` emplacement (par exemple, C:\tools\Android\android-sdk\tools) à votre `PATH` Variable d’environnement.
    - Ouvrez le Gestionnaire de SDK Android (par exemple, via terminal : `android`) et installer
    - *Android 5.0.1 (21 API)* plateforme SDK
    - *Outils de génération android SDK* version 19.1.0 ou une version ultérieure
    - *Prise en charge Android référentiel* (Suppléments)

  Kit de développement logiciel Android ne fournit pas toute instance émulateur par défaut. Créer un autre en exécutant `android avd` à partir de Terminal Server, puis en sélectionnant *créer …* si vous souhaitez exécuter application Android sur émulateur. Recommandé *Niveau Api* est supérieur ou égal à 19 voir [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html) pour plus d’informations sur les options de création et émulateur Android.


## <a name="1--register-an-application-with-azure-ad"></a>*1. enregistrer une application avec Azure Active Directory*

Remarque : cette __étape est facultative__. Le didacticiel fourni mis en service des valeurs qui vous permet de voir l’échantillon en action sans effectuer une mise en service dans votre propre client. Toutefois, il est recommandé que vous cette étape et vous familiariser avec le processus, car il sera requis lorsque vous créerez vos propres applications.

Azure AD émet uniquement jetons aux applications connues. Avant de pouvoir utiliser Azure AD depuis votre application, vous devez créer une entrée de celui-ci dans votre client.  Pour enregistrer une nouvelle application dans votre client,

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez le client de l’endroit où vous souhaitez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application cliente Native** (bien que Cordova applications sont basé en HTML, nous créons application cliente native ici aussi `Native Client Application` doit être activée ; sinon, l’application ne fonctionne pas).
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   **Rediriger URI** est l’URI utilisé pour retourner les jetons dans votre application. Entrez `http://MyDirectorySearcherApp`.

Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes : vous pouvez le trouver dans l’onglet **configuration** de l’application nouvellement créée.

Pour pouvoir exécuter `DirSearchClient Sample`, accorder l’autorisation d’application nouvellement créé à l' _API Azure AD graphique_de la requête :
-   Dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation **d’accès du répertoire en tant que l’utilisateur connecté** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. cloner le référentiel application exemple requis pour le didacticiel*

À partir de votre shell ou une ligne de commande, tapez la commande suivante :

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. créer l’application Cordova*

Il existe plusieurs méthodes de création d’applications Cordova. Dans ce didacticiel, nous allons utiliser l’interface de ligne de commande Cordova (infrastructure du langage commun).
À partir de votre shell ou une ligne de commande, tapez la commande suivante :


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Qui crée la structure de dossiers et la structure du projet Cordova, copie du contenu du projet de démarrage dans le sous-dossier www.
Déplacer vers le nouveau dossier DirSearchClient.

    cd .\DirSearchClient

Ajouter le plug-in d’autorisation, nécessaire pour l’appel de l’API de graphique.

     cordova plugin add cordova-plugin-whitelist

Ensuite, ajoutez toutes les plateformes que vous souhaitez prendre en charge. Pour disposer d’un échantillon de travail, vous devez exécuter au moins une des commandes ci-dessous. Notez que vous ne serez pas en mesure d’émuler iOS sous Windows ou Windows/Windows Phone sur un Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Enfin, vous pouvez ajouter le terme ADAL Cordova du plug-in à votre projet.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. ajouter du code pour authentifier les utilisateurs et obtenir des jetons à partir de AAD*

L’application que vous développez dans ce didacticiel fournit une fonctionnalité de recherche répertoire OS sans système d’exploitation, où l’utilisateur final peuvent taper l’alias d’un utilisateur dans le répertoire et visualiser des attributs de base.  Le projet de démarrage contient la définition de l’interface utilisateur de base de l’application (dans www/index.html) et la structure associe événement base application navigue, liaisons d’interface utilisateur et les résultats de logique d’affichage (dans www/js/index.js). Rendues gauche pour vous consiste à ajouter la logique d’implémentation des tâches d’identité.

La toute première chose à que faire est d’introduire dans votre code les valeurs de protocole utilisés par AAD d’identification de votre application et les ressources que vous ciblez. Ces valeurs seront utilisées pour créer les requêtes jetons ultérieurement. Insérez l’extrait de code ci-dessous tout en haut du fichier index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

La `redirectUri` et `clientId` valeurs doivent correspondre aux valeurs décrivant votre application dans AAD. Vous pouvez trouver ceux de l’onglet Configurer dans le portail Azure, comme décrit dans l’étape 1 précédemment dans ce didacticiel.
Remarque : Si vous avez opté pour inscrit ne pas une nouvelle application dans votre propre client, vous pouvez simplement Coller les valeurs préconfigurés ci-dessus en l’état - qui vous permettra d’afficher l’exécution de l’exemple, bien que vous devez toujours créer votre propre entrée pour vos applications destinées à production.

Ensuite, nous devons ajouter le code de demande de jeton réel. Insérer l’extrait suivant entre la `search `et `renderdata `définitions.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinons cette fonction en divisant vers le bas dans son deux parties principales.
Cet exemple est conçu pour fonctionner avec n’importe quel client, et non pour être lié à l’un d'entre eux. Il utilise le « / courantes » point de terminaison, qui permet à l’utilisateur d’entrer n’importe quel compte au moment de l’authentification et dirige la requête pour le client il appartient.
La première partie de la méthode examine le cache ADAL pour savoir s’il existe déjà un jeton stocké - et s’il existe, elle utilise les clients qu'il provient d’initialisation nouveau terme ADAL. Cela est nécessaire pour éviter les invites supplémentaires, comme l’utilisation de « / courantes » résulte toujours en demandant à l’utilisateur pour entrer un nouveau compte.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La deuxième partie de la méthode effectue la demande tokewn correcte.
La `acquireTokenSilentAsync` méthode vous invite à indiquer à terme ADAL pour renvoyer un jeton de la ressource spécifiée sans affichant tout expérience utilisateur. Qui peut se produire si le cache a déjà un jeton d’accès appropriés stocké, ou s’il existe un jeton d’actualisation peut être utilisé pour obtenir un nouveau jeton d’accès sans shwoing n’importe quelle invite.
Si celle-ci échoue, nous reporte sur `acquireTokenAsync` -qui demande renvoyé à la ligne à l’utilisateur s’authentifier.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Maintenant que nous avons le jeton, nous puissions enfin appeler l’API Graph et exécuter la requête de recherche que nous voulons. Insérer l’extrait de code suivante juste en dessous du `authenticate` définition.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Les fichiers de point de départ fourni un notre UX pour entrer des alias d’un utilisateur dans une zone de texte. Cette méthode utilise cette valeur pour créer une requête, combiner avec le jeton d’accès, envoyez-le à Graph et analyser les résultats. La méthode renderData, déjà présente dans le fichier de point de départ, prend en charge pour visualiser les résultats.

## <a name="4-run"></a>*4. exécuter*
Votre application est maintenant prête à exécuter ! Faire fonctionner est très simple : une fois que l’application démarre, entrez dans la zone de texte l’alias de l’utilisateur que vous voulez rechercher -, puis cliquez sur le bouton. Vous demandera pour l’authentification. Lors de l’authentification et recherche réussie, les attributs de l’utilisateur recherchée seront affiche. Les exécutions suivantes effectue la recherche sans afficher de n’importe quelle invite, grâce à la présence dans le cache du jeton acquis précédemment.
Les étapes concrets pour l’exécution de l’application varient selon la plateforme.

####<a name="windows-10"></a>Windows 10 :

   Tablette/PC :`cordova run windows --archs=x64 -- --appx=uap`

   Mobile (nécessite un périphérique Mobile Windows10 connecté au PC) :`cordova run windows --archs=arm -- --appx=uap --phone`

   __Remarque__: lors du premier démarrage, vous pouvez être invité à se connecter pour une licence développeur. Pour plus d’informations, consultez la [licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-81-tabletpc"></a>Tablette Windows 8.1/PC :

   `cordova run windows`

   __Remarque__: lors du premier démarrage, vous pouvez être invité à se connecter pour une licence développeur. Pour plus d’informations, consultez [licence développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-phone-81"></a>Windows Phone 8.1 :

   Pour exécuter sur appareil connecté :`cordova run windows --device -- --phone`

   Pour exécuter sur émulateur par défaut :`cordova emulate windows -- --phone`

   Utiliser `cordova run windows --list -- --phone` pour afficher toutes les cibles disponibles et `cordova run windows --target=<target_name> -- --phone` pour exécuter l’application sur le périphérique spécifique ou émulateur (par exemple, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android :

   Pour exécuter sur appareil connecté :`cordova run android --device`

   Pour exécuter sur émulateur par défaut :`cordova emulate android`

   __Remarque__: Assurez-vous que vous avez créé instance émulateur à l’aide de *Gestionnaire AVD* comme il est indiqué dans la section *conditions préalables* .

   Utiliser `cordova run android --list` pour afficher toutes les cibles disponibles et `cordova run android --target=<target_name>` pour exécuter l’application sur le périphérique spécifique ou émulateur (par exemple, `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>iOS :

   Pour exécuter sur appareil connecté :`cordova run ios --device`

   Pour exécuter sur émulateur par défaut :`cordova emulate ios`

   __Remarque__: Vérifiez que vous disposez `ios-sim` package installé pour s’exécuter sur émulateur. Pour plus d’informations, reportez-vous à la section *conditions préalables* .

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Utiliser `cordova run --help` voir supplémentaire créer et exécuter des options.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  Vous pouvez maintenant atteindre des scénarios plus avancés (OK et plus intéressants).  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web Node.js API avec Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
