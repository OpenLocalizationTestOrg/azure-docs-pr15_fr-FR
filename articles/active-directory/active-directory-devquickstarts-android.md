<properties
    pageTitle="Azure AD Android prise en main | Microsoft Azure"
    description="Découvrez comment créer une application Android qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Intégrer Azure AD dans une application Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Si vous créez une application de bureau, Azure AD rend simple et rapide pour vous authentifier vos utilisateurs à l’aide de leur compte Active Directory.  Il permet également de votre application consomme en toute sécurité tout web API protégé par Azure AD, tels que les API d’Office 365 ou l’API Azure.

Pour les clients Android qui doivent accéder aux ressources protégées, Azure AD fournit la bibliothèque de l’authentification Active Directory, ou terme ADAL.  But du terme ADAL dans vie consiste à faciliter pour votre application obtenir des jetons d’accès.  Pour montrer simplement combien il est facile, ici nous allons créer une application de la liste des tâches Android qui :

-   Obtient jetons pour appeler une API de liste des tâches en utilisant le [protocole d’authentification 2.0 jeton](https://msdn.microsoft.com/library/azure/dn645545.aspx)d’accès.
-   Obtient la liste des tâches d’un utilisateur
-   Signes les utilisateurs.

Pour commencer, vous aurez besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous n’avez pas déjà un client, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

> [AZURE.TIP] Essayez de l’aperçu de notre nouveau [portail pour les développeurs](https://identity.microsoft.com/Docs/Android) qui vous aideront à être opérationnel avec Azure Active Directory en quelques minutes !  Le portail de développement vous guidera tout au long du processus d’enregistrement une application et intégration Azure AD dans votre code.  Lorsque vous avez terminé, vous aurez accès une application simple qui peut authentifier les utilisateurs dans votre client et un serveur principal qui peuvent accepter des jetons et effectuer la validation. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Étape 1 : Télécharger et exécuter l’exemple de TODO Node.js reste API serveur

Cet exemple est écrit spécifiquement pour fonctionner par rapport à notre exemple existant pour la création d’un seul client API REST des tâches pour Microsoft Azure Active Directory. Il s’agit d’un préalable pour le démarrage rapide.

Pour plus d’informations sur la façon de configurer ce paramètre, visitez notre exemples existants ici :

* [Service Microsoft Azure Active Directory exemple reste API pour Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Étape 2 : Enregistrer votre site Web API auprès de votre client AD Microsoft Azure

**Ce que je fais ?**

*Microsoft Active Directory prend en charge l’ajout de deux types d’applications. Web API qui proposent des services à des utilisateurs et applications (soit sur le web ou une application en cours d’exécution sur un appareil) qui accèdent à ces API Web. Dans cette étape vous enregistrez l’API Web que vous exécutez localement pour le test de cet exemple. Normalement, cette API Web est un service reste qui propose des fonctionnalités que vous souhaitez une application pour accéder à. Microsoft Azure Active Directory pouvez protéger un point de terminaison !*

*Nous supposons ici vous enregistrez l’API REST TODO référencé ci-dessus, mais cela fonctionne pour n’importe quelle API Web Azure Active Directory pour protéger souhaité.*

Étapes à suivre pour enregistrer une API Web auprès de Microsoft Azure AD

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans le NAV gauche.
3. Cliquez sur le client de l’annuaire dans lequel vous souhaitez enregistrer l’exemple d’application.
4. Cliquez sur l’onglet Applications.
5. Dans le bac d’alimentation, cliquez sur Ajouter.
6. Cliquez sur « Ajouter une application mon organisation développe ».
7. Entrez un nom convivial pour la sélectionner de l’application, par exemple « TodoListService », « API Web et/ou Web Application », puis cliquez sur Suivant.
8. Pour l’URL authentification, entrez l’URL de base pour l’échantillon, qui est par défaut `https://localhost:8080`.
9. Pour l’URI ID de l’application, entrez `https://<your_tenant_name>/TodoListService`, en remplaçant `<your_tenant_name>` avec le nom de votre client Azure AD.  Cliquez sur OK pour terminer l’enregistrement.
10. Dans le portail Azure, cliquez sur l’onglet Configuration de votre application.
11. **Rechercher la valeur de l’ID de Client et copiez-le côté**, vous devrez Ceci ultérieurement lors de la configuration de votre application.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Étape 3 : Enregistrer l’exemple d’application Android Native Client

Enregistrement de votre application web est la première étape. Ensuite, vous devez informer Azure Active Directory ainsi que votre application. Cela permet à votre application communiquer avec l’API Web simplement enregistré

**Ce que je fais ?**  

*Comme indiqué ci-dessus, Microsoft Azure Active Directory prend en charge l’ajout de deux types d’applications. Web API qui proposent des services à des utilisateurs et applications (soit sur le web ou une application en cours d’exécution sur un appareil) qui accèdent à ces API Web. Dans cette étape vous enregistrez l’application dans cet exemple. Vous devez le faire dans l’ordre de cette application puissent demande à accéder à l’API Web que vous venez d’enregistrer. Azure Active Directory refuse permettre à votre application demander la connexion, sauf si elle est enregistrée même ! Qui fait partie de la sécurité du modèle.*

*Nous supposons ici vous enregistrez cet exemple d’application référencé ci-dessus, mais cela fonctionne pour n’importe quelle application que vous développez.*

**Pourquoi ai-je plaçant une application et une API Web dans un client ?**

*Comme vous pouvez le deviner, vous pourriez créer une application qui accède à une API externe qui est enregistrée dans Azure Active Directory à partir d’un autre client. Si vous effectuez cette opération, vos clients devra consentement pour l’utilisation de l’API dans l’application. Le composant piles est, bibliothèque de l’authentification Active Directory pour iOS prend en charge votre consentement pour vous ! En nous rejoindre des fonctionnalités plus avancées, vous verrez qu'il s’agit d’une partie importante du travail nécessaire pour accéder à la gamme de APIs Microsoft à partir d’Azure et Office ainsi que tout autre fournisseur de service. Pour l’instant, étant donné que vous avez enregistré votre API Web et l’application sous le même client vous ne verrez pas les invites de consentement. Il s’agit généralement le cas si vous développez une application simplement pour votre société à utiliser.*

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans le NAV gauche.
3. Cliquez sur le client de l’annuaire dans lequel vous souhaitez enregistrer l’exemple d’application.
4. Cliquez sur l’onglet Applications.
5. Dans le bac d’alimentation, cliquez sur Ajouter.
6. Cliquez sur « Ajouter une application mon organisation développe ».
7. Entrez un nom convivial pour l’application, par exemple, « TodoListClient-Android », sélectionnez « Native Application cliente », puis cliquez sur Suivant.
8. Pour l’URI rediriger, entrez `http://TodoListClient`.  Cliquez sur Terminer.
9. Cliquez sur l’onglet Configuration de l’application.
10. Rechercher la valeur de l’ID de Client et copiez-le effectuées, vous en avez besoin ultérieurement lors de la configuration de votre application.
11. Dans « Autorisations pour les autres Applications », cliquez sur « Ajouter Application ».  Sélectionnez « Autres » dans la liste déroulante « Afficher », puis cliquez sur la coche supérieure.  Recherchez et cliquez sur le TodoListService, puis cliquez sur la coche en bas pour ajouter l’application.  Sélectionnez « Accès TodoListService » dans le menu déroulant « Délégué les autorisations », puis enregistrer la configuration.



Pour générer avec Maven, vous pouvez utiliser la pom.xml au niveau supérieur

  * Dupliquer cette mis en pension dans un répertoire de votre choix :

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Suivez les étapes présentées [section Prerequests pour configurer votre maven pour android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Le programme d’installation émulateur avec SDK 19
  * Accédez au dossier racine dans lequel vous cloner le mis en pension
  * Exécutez la commande : installer mvn EPURAGE
  * Placez-vous à l’exemple de démarrage rapide : cd samples\hello
  * Exécutez la commande : mvn android : déployer android : exécuter
  * Vous devriez voir Lancement d’application
  * Entrez les informations d’identification utilisateur de test pour essayer !

JAR packages seront également soumis à côté du package aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Étape 4 : Téléchargez ADAL Android et l’ajouter à votre espace de travail Éclipse

Nous avons simplifié vous disposez de plusieurs options pour utiliser cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer cette bibliothèque Eclipse et insérer un lien à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser format du package *aar* et référencer les fichiers binaires.

####<a name="option-1-source-zip"></a>Option 1 : Source Zip

Pour télécharger une copie du code source, cliquez sur « Télécharger le code postal » sur le côté droit de la page ou sur [ici](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Option 2 : Source via Git

Pour obtenir le code source du Kit de développement via git tapez seulement :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Option 3 : Binaires via Gradle

Vous pouvez récupérer les fichiers binaires mis en pension centrale Maven ; Package AAR peut être inclus dans votre projet dans AndroidStudio comme suit :

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Option 4 : aar via Maven

Si vous utilisez le plug-in m2e dans Eclipse, vous pouvez spécifier la dépendance dans votre fichier pom.xml :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Option 5 : jar package à l’intérieur de dossier et les bibliothèques
Vous pouvez obtenir le fichier jar à partir de la mis en pension maven et glisser vers le dossier *et les bibliothèques* dans votre projet. Vous devez copier les ressources nécessaires à votre projet ainsi que dans la mesure où les packages jar ne contiennent pas les.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Étape 5 : Ajouter des références à Android ADAL à votre projet


2. Ajoutez une référence à votre projet et définissez-le comme étant une bibliothèque Android. Si vous ne savez pas comment faire pour ce faire, [Cliquez ici pour plus d’informations] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Ajouter la dépendance de projet pour le débogage dans vos paramètres de projet

4. Mettre à jour votre fichier de projet AndroidManifest.xml à inclure :

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Créer une instance de AuthenticationContext auprès de votre activité principale. Les détails de cet appel n’entrent pas dans l’étendue de ce fichier Lisez-moi, mais vous pouvez obtenir un bon point de départ en recherchant dans l' [Exemple Client natif Android](https://github.com/AzureADSamples/NativeClient-Android). Voici un exemple :

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Remarque : mContext est un champ dans votre activité

8. Copiez ce bloc de code pour gérer la fin de AuthenticationActivity après que utilisateur entre des informations d’identification et reçoit le code d’autorisation :

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Pour demander un jeton, vous définissez un rappel

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Enfin, demandez pour un jeton à l’aide de ce rappel :

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explication des paramètres :

  * Ressource est requise et la ressource que vous essayez d’accéder.
  * Identifiant du client est requis et est fourni à partir du portail AzureAD.
  * Vous pouvez configurer redirectUri en tant que votre packagename. Il n’est pas nécessaire à fournir pour l’appel acquireToken.
  * PromptBehavior permet de demander des informations d’identification ignorer cache et des cookies.
  * Rappel est appelé après que le code d’autorisation est échangée pour un jeton.

  Le rappel aura un objet de AuthenticationResult c'est-à-dire accesstoken, date d’expiration et informations idtoken.

Facultatif : **acquireTokenSilent**

Vous pouvez appeler **acquireTokenSilent** pour gérer la mise en cache, puis actualisez jeton. Il fournit également version de synchronisation. Elle accepte les ID d’utilisateur en tant que paramètre.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Intermédiaire**: application du portail d’entreprise de Microsoft Intune constituent le composant intermédiaire. ADAL utiliser le compte intermédiaire, s’il existe un compte d’utilisateur est créé à cette authentificateur et développeur choisiront ne pas d’ignorer. Développeur pouvez ignorer l’utilisateur intermédiaire avec :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Développeur doit s’inscrire redirectUri spécial pour réduire l’utilisation intermédiaire. RedirectUri se présente sous la forme msauth://packagename/Base64UrlencodedSignature. Vous pouvez obtenir votre redirecturi pour votre application en utilisant le script « brokerRedirectPrint.ps1 » ou utilisez API appel mContext.getBrokerRedirectUri. Signature est liée à vos certificats de signature.

 Modèle intermédiaire actuel est pour un utilisateur. AuthenticationContext fournit la méthode de l’API pour obtenir de l’utilisateur intermédiaire.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Utilisateur intermédiaire sera retournée si le compte est valide.

 Votre manifeste d’application doit disposer d’autorisations d’utiliser des comptes AccountManager qui suit : http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * UTILISER_DES_RÉFÉRENCES
 * MANAGE_ACCOUNTS


À l’aide de cette procédure pas à pas, vous devez disposer des éléments requis intégrer correctement avec Azure Active Directory. Pour plus d’exemples de cela fonctionne correctement, visitez le AzureADSamples / référentiel de GitHub.

## <a name="important-information"></a>Informations importantes

### <a name="customization"></a>Personnalisation

Ressources du projet bibliothèque peuvent être remplacés par vos ressources d’application. Cela se produit lors de la création de votre application. Pour cette raison, vous pouvez personnaliser mise en page de l’activité d’authentification comme vous le souhaitez. Vous devez veillez à conserver l’id des contrôles qui uses(Webview) ADAL.

### <a name="broker"></a>Intermédiaire

Composant intermédiaire est remis avec l’application portail d’entreprise de Microsoft Intune. Compte est créé dans le Gestionnaire de compte. Type de compte est « com.microsoft.workaccount ». Il n’autorise seul compte de l’authentification unique. Il crée des cookies l’authentification unique pour cet utilisateur HIP périphérique pour l’une des applications à la fin.

### <a name="authority-url-and-adfs"></a>AD FS et Url autorité

ADFS n’est pas reconnu comme production STS, vous devez activer de découverte instance et passez la valeur false au constructeur AuthenticationContext.

Url autorité doit STS instance et le nom de client : https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>Interroger des éléments du cache

Terme ADAL fournit des fonctions de requête de cache par défaut dans SharedPreferences avec certaines cache simple. Vous pouvez obtenir le cache en cours de AuthenticationContext avec :
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Vous pouvez également fournir votre implémentation de cache, si vous voulez personnaliser.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

Terme ADAL offre la possibilité de spécifier comportement de l’invite. Interface utilisateur s’affiche PromptBehavior.Auto si jeton d’actualisation n’est pas valide et informations d’identification utilisateur sont requises. PromptBehavior.Always ignore l’utilisation de la mémoire cache et toujours afficher l’interface utilisateur.

### <a name="silent-token-request-from-cache-and-refresh"></a>Demande de jeton en mode silencieux à partir du cache et actualiser

Cette méthode n’utilise pas pop de l’interface utilisateur vers le haut et pas demander une activité. Il retournera jeton de cache, le cas échéant. Si jeton a expiré, il essaiera d’actualiser. Si jeton d’actualisation est expiré ou a échoué, elle renvoie AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Vous pouvez également agrandir synchronisation appeler avec cette méthode. Vous pouvez définir null pour rappel ou utilisez acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnostics

Les éléments suivants sont les sources principales d’informations pour diagnostiquer les problèmes :

+ Exceptions
+ Journaux
+ Traces réseau

Notez également que l’ID de corrélation est central pour les diagnostics dans la bibliothèque. Vous pouvez définir votre corrélation ID sur une base par demande si vous voulez faire correspondre un terme ADAL demande avec d’autres opérations dans votre code. Si vous ne définissez un id corrélations puis terme ADAL générera un aléatoire une et tous les messages de journal et les appels de réseau portent l’id de corrélation. L’automatique générée modifications id à chaque demande.

#### <a name="exceptions"></a>Exceptions

Il s’agit visiblement le premier test de diagnostic. Nous essayez de fournir des messages d’erreur utiles. Si vous trouvez un qui n’est pas utile Veuillez soumettre un problème et nous le faire savoir. Veuillez également fournissent des informations de périphérique comme modèle et SDK #.

#### <a name="logs"></a>Journaux

Vous pouvez configurer la bibliothèque pour générer les messages du journal que vous pouvez utiliser pour diagnostiquer les problèmes. Configurer la journalisation à l’aide de l’appel suivant pour configurer un rappel terme ADAL utilisera remettre désactiver chaque message dans le journal lorsqu’elles sont générées.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Les messages peuvent être écrites dans un fichier journal personnalisé comme indiqué ci-dessous. Malheureusement, il n’existe aucun moyen standard d’accès aux journaux à partir d’un appareil. Il existe certains des services qui peuvent vous aider à ceci. Vous pouvez également stock votre propre, tels qu’envoyer le fichier à un serveur.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Niveaux d’enregistrement

+ Error(exceptions)
+ Warn(Warning)
+ Informations (à titre d’Information)
+ Commentaires (plus de détails)

Vous définissez le niveau de journalisation à ceci :
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Tous les messages du journal d’expédition logcat en plus de tous les rappels journal personnalisé.
Vous pouvez obtenir un logcat formulaire fichier journal comme indiqué belog :

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Obtenir d’autres exemples adb commandes : https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Traces réseau

Vous pouvez utiliser différents outils pour capturer le trafic HTTP qui génère du terme ADAL.  Ceci est particulièrement utile si vous êtes habitué au protocole OAuth ou si vous avez besoin fournir des informations de diagnostic à Microsoft ou d’autres chaînes de prise en charge.

Fiddler est l’outil de suivi HTTP plus simple.  Utilisez les liens suivants pour configurer jusqu'à le trafic réseau ADAL correctement enregistrement.  Pour pouvoir être utiles, il est nécessaire de configurer fiddler ou tout autre outil tel que Charles, d’enregistrer le trafic SSL non chiffré.  Remarque : Les Traces générées de cette façon peuvent contenir des informations hautement privilégiées tels que les jetons d’accès, les noms d’utilisateur et les mots de passe.  Si vous utilisez des comptes de production, ne pas partager ces traces avec les parties 3e.  Si vous avez besoin fournir une trace à une personne afin d’obtenir une assistance technique, reproduire le problème avec un compte temporaire avec les noms d’utilisateur et mots de passe que vous êtes prêt à partager.

+ [La configuration Fiddler pour Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurer les règles de Fiddler pour terme ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Mode de la boîte de dialogue
méthode d’acquireToken sans activité prend en charge invite de boîte de dialogue.

### <a name="encryption"></a>Chiffrement

Terme ADAL chiffre les jetons et store dans SharedPreferences par défaut. Vous pouvez consulter la classe StorageHelper pour afficher les détails. Android introduite AndroidKeyStore 4.3(API18) sécurité de stockage des clés privées. Terme ADAL qui utilise pour API18 et version ultérieure. Si vous souhaitez utiliser terme ADAL pour les versions SDK inférieures, vous devez fournir une clé secrète en AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>HIP oauth2 ne PORTEUR

Classe AuthenticationParameters fournit les fonctionnalités pour obtenir l’authorization_uri à partir de défi de porteur oauth2 ne.

### <a name="session-cookies-in-webview"></a>Cookies de session en mode Web

Affichage en mode Web Android n’efface pas les cookies de session après la fermeture d’application. Vous pouvez le gérer avec des exemples de code ci-dessous :
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
En savoir plus sur les cookies : http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Remplacements de ressources

La bibliothèque ADAL inclut des chaînes en anglais pour les messages ProgressDialog deux suivants.

Votre application doit remplacer les éventuellement des chaînes localisées.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>Boîte de dialogue NTLM
Version adal 1.1.0 prend en charge NTLM boîte de dialogue traitée par le biais événement onReceivedHttpAuthRequest à partir de WebViewClient. Chaînes et mise en page de la boîte de dialogue peuvent être personnalisées.

### <a name="cross-app-sso"></a>Authentification unique entre-app
Découvrez [comment activer l’authentification unique entre application sur Android à l’aide du terme ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
