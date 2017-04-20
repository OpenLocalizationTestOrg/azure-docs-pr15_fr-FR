<properties
    pageTitle="Azure B2C annuaire Active : Appeler un API web à partir d’une application Android | Microsoft Azure"
    description="Cet article vous montrent comment créer un appareil Android « liste des tâches » application qui appelle un site web Node.js API en utilisant les jetons PORTEUR OAuth 2.0. L’application Android et le web API permet Azure Active Directory B2C de gestion des identités d’utilisateur et l’authentification des utilisateurs."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C : Appeler un API web à partir d’une application Android

> [AZURE.WARNING] Ce didacticiel requiert certaines mises à jour importantes, en particulier pour supprimer l’utilisation de ADAL Android pour B2C.  Publier des instructions FRAICHES d’utilisation Azure AD B2C dans les applications Android dans la semaine suivante, nous allons et nous vous recommandons maintenant en attendant.  Mais si vous voulez simplement faire des tests, n’hésitez pas à poursuivre l’article ci-dessous.



À l’aide de B2C Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à vos applications Android et web API en quelques étapes. Cet article vous montrent comment créer un appareil Android « liste de tâches » application qui appelle un site web Node.js API en utilisant les jetons PORTEUR OAuth 2.0. L’application Android et le web API permet Azure AD B2C de gestion des identités d’utilisateur et l’authentification des utilisateurs.

Ce démarrage rapide requiert que vous disposiez d’un site web API qui est protégé par Azure AD avec B2C à fonctionner correctement. Nous avons créé un pour .NET et Node.js pour pouvoir utiliser. Cette procédure suppose que l’échantillon API web Node.js est configuré. Pour plus d’informations, voir le [Azure AD B2C web API pour Node.js didacticiel](active-directory-b2c-devquickstarts-api-node.md).

Pour les clients Android qui doivent accéder aux ressources protégées, Azure AD fournit la bibliothèque terme (Active Directory authentification ADAL). Le seul but du terme ADAL consiste à faciliter pour votre application obtenir des jetons d’accès. Pour montrer combien il est facile, dans ce guide nous allons créer une application de la liste des tâches Android :

- Obtient les jetons d’accès nécessitant une liste de tâches API en utilisant le [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Obtient les listes des tâches des utilisateurs.
- Signes des utilisateurs.

> [AZURE.NOTE] Cet article n’aborde comment mettre en œuvre se connecter, d’abonnement et gestion des profils à l’aide de Azure AD B2C. Il se concentre sur l’appel API web une fois l’utilisateur authentifié. Si vous n’avez pas déjà, vous devez commencer par le [.NET web app en route](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les concepts de base d’Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou du client. Un répertoire est un conteneur pour toutes vos utilisateurs, applications, groupes et plus encore. Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de vous continuez dans ce guide.

## <a name="create-an-application"></a>Créer une application

Ensuite, vous devez créer une application dans votre annuaire B2C. Ces informations vous permet d’Azure AD dont il a besoin pour communiquer en toute sécurité avec votre application. L’application et l’API web sont représentées dans ce cas, par un seul **ID de l’Application** , car ils constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). N’oubliez pas :

- Inclure une **application web**/**API web** dans l’application.
- Entrez `urn:ietf:wg:oauth:2.0:oob` sous forme d’une **URL de réponse**. Il est l’URL par défaut pour cet exemple de code.
- Créer un **code secret d’Application** pour votre application et le copier. Vous en aurez besoin ultérieurement. Notez que cette valeur doit être [une séquence d’échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant de l’utiliser.
- Copiez l' **ID de l’Application** qui est affectée à votre application. Vous devez également ceci plus tard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient trois expériences d’identité : s’inscrire, se connecter et se connecter à l’aide de Facebook.  Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez vos stratégies de trois, veillez à :

- Sélectionnez le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription.
- Cliquez sur les revendications application **nom d’affichage** et **l’ID de l’objet** dans chaque stratégie. Vous pouvez choisir d’autres réclamations également.
- Copier le **nom** de chaque stratégie après sa création. Il doit avoir le préfixe `b2c_1_`.  Vous aurez besoin ultérieurement ces noms de stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé les trois stratégies, vous êtes prêt à créer votre application.

Notez que cet article n’aborde comment utiliser les stratégies que vous venez de créer. Pour découvrir comment fonctionnent les stratégies dans Azure AD B2C, commencez par le [.NET web application mise en route didacticiel](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [étant conservées sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Pour créer l’exemple fur, vous pouvez [Télécharger le projet de structure sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Vous devez télécharger la structure pour effectuer ce didacticiel.** En raison de la complexité de l’implémentation d’une application Android entièrement fonctionnelle, la structure a code UX qui continuera après avoir terminé ce didacticiel. Il s’agit d’une mesure de gagner du temps pour les développeurs. Le code UX n’est pas pertinentes pour la rubrique comment ajouter B2C à l’application Android.

L’application terminée est également [disponible sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou sur la `complete` branche du même référentiel.

Pour créer par Maven, vous pouvez utiliser `pom.xml` au niveau supérieur.

  1. Suivez les étapes décrites dans [la section conditions préalables pour configurer Maven pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configurer un émulateur avec SDK 21.
  3. Accédez au dossier racine dans lequel vous cloner le mis en pension.
  4. Exécutez la commande `mvn clean install`.
  5. Placez-vous à l’exemple de démarrage rapide `cd samples\hello`.
  6. Exécutez la commande `mvn android:deploy android:run`.

Vous devriez voir l’application de lancement. Entrez les informations d’identification utilisateur de test pour l’essayer.

Packages JAR (Java Archive) seront également soumis à côté du package Android Archive anticipé.

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Télécharger le ADAL Android et l’ajouter à votre espace de travail Studio Android

Vous disposez des options pour l’utilisation de cette bibliothèque dans votre projet Android :

* Vous pouvez utiliser le code source pour importer la bibliothèque dans Eclipse et insérer un lien à votre application.
* Si vous utilisez Android Studio, vous pouvez utiliser le format du package AAR et référencer les fichiers binaires.

### <a name="option-1-binaries-via-gradle-recommended"></a>Option 1 : Binaires via Gradle (recommandé)

Vous pouvez obtenir les fichiers binaires à partir de la mis en pension Maven central. Le package AAR peut être inclus dans votre projet dans Studio Android (par exemple, dans `build.gradle`) de cette façon :

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Option 2 : AAR via Maven

Si vous utilisez la `m2e` plug-in Eclipse, vous pouvez spécifier la dépendance dans votre `pom.xml` fichier :

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Option 3 : Source via Git (le dernier recours)

Pour obtenir le code source du Kit de développement via Git, entrez :

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilisez la branche **convergence.**

## <a name="set-up-your-configuration-file"></a>Configurer votre fichier de configuration

Utiliser la configuration que vous avez configuré précédemment dans le portail B2C pour configurer le projet Android.

Ouvrir `helpes/Constants.java` et remplir les valeurs pour les éléments suivants :

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: Les étendues que vous passez au serveur que vous souhaitez demander à partir du serveur lorsqu’un utilisateur se connecte. Pour l’aperçu B2C, vous transférez `client_id`. Toutefois, il est supposé changer à `read scopes` à l’avenir. Ce document est mise à jour lorsque qui se produit.
- `ADDITIONAL_SCOPES`: Étendues supplémentaires que vous souhaitez utiliser pour votre application. Ils doivent être utilisés à l’avenir.
- `CLIENT_ID`: L’ID de l’Application vous avez obtenu à partir du portail.
- `REDIRECT_URL`: La redirection dans laquelle vous pensez le jeton soit publié.
- `EXTRA_QP`: Quoi vous voulez passer au serveur dans un format codée au format URL.
- `FB_POLICY`: La stratégie que vous appelez. Il s’agit de la partie plus importante pour cette procédure.
- `EMAIL_SIGNIN_POLICY`: La stratégie que vous appelez. Il s’agit de la partie plus importante pour cette procédure.
- `EMAIL_SIGNUP_POLICY`: La stratégie que vous appelez. Il s’agit de la partie plus importante pour cette procédure.

## <a name="add-references-to-android-adal-to-your-project"></a>Ajouter des références à Android ADAL à votre projet

> [AZURE.NOTE]  Terme ADAL pour Android utilise un modèle basé sur l’intention d’appeler d’authentification. Modes de « mise en page « l’application pour votre travail. Cet exemple entière et toutes les terme ADAL pour Android, centres comment gérer les modes et transmettre les informations entre elles.

Tout d’abord, indiquez Android à la mise en page de votre application, y compris les modes que vous voulez utiliser. Ces modes seront expliqués en détail plus loin dans ce didacticiel.

Mettre à jour votre projet `AndroidManifest.xml` fichier pour inclure toutes vos modes :

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Comme vous pouvez le voir, vous définissez cinq activités. Vous allez utiliser tous ces éléments.

- `AuthenticationActivity`: Cette information provient du terme ADAL et fournit l’affichage web de connexion.
- `LoginActivity`: Cela permet d’afficher vos stratégies de connexion et les boutons pour chaque stratégie.
- `SettingsActivity`: Vous permet de modifier les paramètres de l’application en cours d’exécution.
- `AddTaskActivity`: Vous permet d’ajouter des tâches à votre API REST qui sont protégés par Azure AD.
- `ToDoActivity`: Il s’agit de l’activité principale qui affiche les tâches.

## <a name="create-the-sign-in-activity"></a>Créer l’activité de connexion

Créer une activité principale et appelez-le `LoginActivity`.

Créer un fichier nommé `LoginActivity.java`.

Vous devez initialisation l’activité et ajouter des boutons qui contrôlent votre interface utilisateur. Il s’agit familier si vous avez écrit du code Android avant.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Vous venez de créer les boutons appeler votre `ToDoActivity` intention (qui appelle terme ADAL lorsque vous avez besoin d’un jeton). Pour cela, ils en utilisant votre activité comme une référence et un paramètre supplémentaire. Ce paramètre supplémentaire est passé par le `intent.putExtra()` méthode. Vous définissez `"thePolicy"` à l’aide de ce que vous avez spécifié dans `Constants.java`. Cette option indique à l’intention de la stratégie à appeler lors de l’authentification.

## <a name="create-the-settings-activity"></a>Création de l’activité de paramètres

Il s’agit d’une activité qui remplit les paramètres de l’interface utilisateur.

Créer un fichier nommé `SettingsActivity.java` simples créer, lire, mettre à jour et supprimer des opérations.

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Création de l’activité de la tâche Ajouter

Vous pouvez utiliser cette activité pour ajouter une tâche à votre point de terminaison API REST.

Créer un fichier nommé `AddTaskActivity.java` et écrire ce qui suit.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Créer l’activité de la liste des tâches

Il s’agit de l’activité les plus importantes. Vous pouvez employer à obtenir un jeton à partir d’Azure AD pour une stratégie et ensuite utiliser ce jeton pour appeler le serveur de l’API REST de tâche.

Créer un fichier nommé `ToDoActivity.java` et écrire ce qui suit. (Les appels seront expliqués ultérieurement.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Vous avez peut-être remarqué que cela repose sur les méthodes qui n’ont pas encore été écrit. Ils incluent `updateLoggedInUser()`, `clearSessionCookie()`, et `getTasks()`. Vous allez écrire plus loin dans ce guide. Vous pouvez ignorer les erreurs dans Studio Android pour l’instant.

Explication des paramètres :

  - `SCOPES`: Obligatoire, les étendues que vous voulez demander l’accès. Pour l’aperçu B2C, c’est la même en tant que `client_id`, mais il est supposé changer à l’avenir.
  - `POLICY`: La stratégie pour lorsque vous souhaitez authentifier l’utilisateur.
  - `CLIENT_ID`: Obligatoire, est fourni à partir du portail Azure AD.
  - `redirectUri`: Peut être configuré comme nom de package. Il n’est pas nécessaire pour être fourni pour le `acquireToken` appeler.
  - `getUserInfo()`: Permet de rechercher si un utilisateur est déjà dans le cache. Le paramètre décrit également comment inviter un utilisateur si cet utilisateur n’est pas trouvé ou si jeton d’accès de l’utilisateur n’est pas valide. Cette méthode sera écrit plus loin dans ce guide.
  - `PromptBehavior.always`: Permet de demander des informations d’identification permet d’ignorer le cache et les cookies.
  - `Callback`: Appelé après qu’un code d’autorisation est échangé pour un jeton. Il aura un objet `AuthenticationResult`, qui contient les informations d’ID de jeton jeton d’accès et date d’expiration.

> [AZURE.NOTE]  L’application portail d’entreprise Intune Microsoft fournit le composant intermédiaire, et elle peut être installée sur périphérique de l’utilisateur. L’application fournit unique (SSO) accès à toutes les applications sur l’appareil. Les développeurs doivent être préparés afin de permettre Intune. Terme ADAL pour Android utilisera le compte intermédiaire s’il existe un seul compte d’utilisateur créé dans l’authentificateur. Pour utiliser l’intermédiaire, le développeur doit s’inscrire un spécial `redirectUri` pour l’intermédiaire à utiliser. `redirectUri`se présente sous la forme msauth://packagename/Base64UrlencodedSignature. Vous pouvez obtenir `redirectUri` pour votre application en utilisant le script `brokerRedirectPrint.ps1` ou à l’aide de l’appel API `mContext.getBrokerRedirectUri()`. La signature est liée à votre signature des certificats à partir de Google Play store.

 Vous pouvez ignorer l’utilisateur intermédiaire financier à l’aide de :

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Afin de réduire la complexité de ce démarrage rapide B2C, nous avons choisi d’ignorer l’intermédiaire dans notre exemple.

Ensuite, créez des méthodes d’assistance qui obtenir le jeton uniquement au cours d’appels d’authentification la tâche API.

Dans la même `ToDoActivity.java` de fichiers, écrire ce qui suit.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Également ajouter des méthodes qui seront « set » et « get » `AuthenticationResult` (qui contient votre jeton) au modèle global `Constants`. Même si `ToDoActivity.java` utilise `sResult` dans son flux, vous devez ajouter ces méthodes. Si vous n’est pas, vos autres activités n’ont pas accès au jeton pour effectuer le travail (par exemple en ajoutant une tâche dans `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Créer une méthode pour retourner un identificateur d’utilisateur

ADAL pour Android représente l’utilisateur sous la forme d’un `UserIdentifier` objet. Cela gère l’utilisateur. Vous pouvez utiliser l’objet pour déterminer si le même utilisateur est utilisé dans vos appels. À l’aide de ces informations, vous pouvez s’appuient sur le cache, plutôt que passer un appel de nouveau sur le serveur. Pour ce faire, nous avons créé le `getUserInfo()` méthode renvoie `UserIdentifier`. Vous pouvez l’utiliser avec `acquireToken()`. Nous avons également créé un `getUniqueId()` méthode qui renvoie l’identificateur de `UserIdentifier` dans le cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Écrire des méthodes d’assistance

Ensuite, écrivez des méthodes d’assistance pour vous aider à clarifier les cookies et de fournir `AuthenticationCallback`. Ces méthodes sont utilisées purement pour l’échantillon pour vous assurer que vous êtes dans un état vierge lorsque vous appelez votre `ToDo` activité.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Appeler la tâche API

Une fois que vous avez votre activité prête à récupérer les jetons, vous pouvez écrire votre API pour accéder au serveur de la tâche.

`getTasks`fournit un tableau qui représente les tâches de votre serveur.

Précédez `getTasks`.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Également écrire une méthode qui sera initialisation vos tableaux à la première exécution.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

Vous pouvez voir que ce code requiert des méthodes supplémentaires son travail. Écrire ces suivant.

### <a name="create-an-endpoint-url-generator"></a>Créer un générateur d’URL du point de terminaison

Vous avez besoin générer l’URL du point de terminaison que vous êtes connecté à. Que faire dans le même fichier de classe.

**Dans le même fichier** appelé `ToDoActivity.java`, écrire ce qui suit.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Notez que vous ajoutez le jeton d’accès à la demande dans le code mentionné dans la section suivante.

## <a name="write-some-ux-methods"></a>Écrire des méthodes UX

Android nécessite vous permet de gérer des rappels pour l’application de fonctionner. Il s’agit des `createAndShowDialog` et `onResume()`. Il s’agit familier si vous avez écrit du code Android avant.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Ensuite, gérer les rappels de votre boîte de dialogue.

Dans le même fichier appelé `ToDoActivity.java`, écrire ce qui suit.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Vous disposez à présent une `ToDoActivity.java` fichier compile. L’ensemble du projet doit également compiler à ce stade.

## <a name="run-the-sample-app"></a>Exécuter l’application d’exemple

Enfin, générez et exécutez l’application dans Studio Android ou Eclipse. Inscrit ou connecté dans l’application. Créer des tâches pour l’utilisateur connecté. Déconnectez-vous et reconnectez-vous en tant qu’un autre utilisateur, puis créer des tâches pour cet utilisateur.

Notez que les tâches sont stockées par utilisateur sur l’API, car l’API extrait l’identité de l’utilisateur du jeton d’accès qu’il reçoit.

Pour référence, l’exemple complet [est fourni sous forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Vous pouvez également cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Informations importantes


### <a name="encryption"></a>Chiffrement

Terme ADAL chiffre les jetons et store dans `SharedPreferences` par défaut. Vous pouvez examiner les `StorageHelper` cours pour afficher les détails. Android introduite **AndroidKeyStore pour 4.3(API18)** un stockage sécurisé des clés privées. Terme ADAL qui utilise pour API18 et version ultérieure. Si vous souhaitez utiliser terme ADAL pour les versions SDK inférieures, vous devez fournir une clé secrète en `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookies de session dans l’affichage web

Affichage web Android n’efface pas les cookies de session après la fermeture de l’application. Vous pouvez le gérer avec le code suivant.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[En savoir plus sur les cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).
