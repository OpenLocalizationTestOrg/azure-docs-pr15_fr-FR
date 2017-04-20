<properties
    pageTitle="Comment activer l’authentification unique entre application sur iOS à l’aide du terme ADAL | Microsoft Azure"
    description="Découvrez comment utiliser les fonctionnalités du Kit de développement ADAL pour activer l’authentification unique au sein de vos applications. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Comment activer l’authentification unique entre application sur iOS à l’aide du terme ADAL


Fournir l’authentification unique (SSO) afin que les utilisateurs devront taper leurs informations d’identification une seule fois et que ces informations d’identification automatiquement travailler sur les applications est maintenant attendue par les clients. La difficulté à entrer leur nom d’utilisateur et mot de passe sur un petit écran, souvent heures combinés avec un facteur complémentaire (2FA) comme un appel téléphonique ou un code texted, résultats dans insatisfaction rapide si un utilisateur doit effectuer l’opération plusieurs fois à votre produit.

En outre, si vous utilisez une plateforme identité susceptibles d’utiliser d’autres applications telles que Microsoft Accounts ou d’un compte de travail à partir d’Office 365, clients s’attendre que les informations d’identification pour être utilisé toutes leurs applications quel que soit le fournisseur.

La plateforme Microsoft Identity, ainsi que de notre SDK identité Microsoft, tout cela fonctionne-t-il dur effectue automatiquement et vous donne la possibilité de satisfaire pleinement vos clients avec l’authentification unique dans votre propre suite d’applications ou en tant qu’avec notre intermédiaire et applications authentificateur, sur l’ensemble de l’unité.

Cette procédure pas à pas vous indique comment configurer notre kit de développement logiciel au sein de votre application pour mettre cet avantage à vos clients.

Cette procédure s’applique à :

* Azure Active Directory
* B2C Azure Active Directory
* B2B Azure Active Directory
* Accès à conditionnelle Azure Active Directory


Notez que le document ci-dessous suppose que vous savez comment [mise en service des applications dans le portail héritée pour Azure Active Directory](./develop/active-directory-how-to-integrate.md) ainsi que des intègrent votre application avec l' [identité Microsoft iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concepts de l’authentification unique de la plateforme Microsoft des identités

### <a name="microsoft-identity-brokers"></a>Identité Microsoft fournit

Microsoft fournit des applications pour toutes les plateformes mobiles qui permettent de la transition des informations d’identification entre les applications provenant de différents fournisseurs ainsi que les permet de fonctionnalités améliorées spéciale nécessitant un emplacement sécurisé unique à partir de laquelle valider les informations d’identification. Nous appelons ces **agents**. Sur iOS et Android ceux-ci sont fournis par le biais des applications téléchargeables que clients installer séparément ou peuvent être envoyés à l’appareil par une société qui gère tout ou partie de l’appareil pour leur employé. Ces agents prend en charge la gestion de la sécurité uniquement pour certaines applications ou l’ensemble de l’unité selon qu’elles recherchent les administrateurs informatiques. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré au système d’exploitation, connu techniquement en tant que l’intermédiaire d’authentification Web.

Pour mieux comprendre comment nous utilisons ces agents et comment vos clients peuvent les afficher dans leur flux de connexion pour la plateforme Microsoft Identity poursuivez votre lecture pour plus d’informations.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Modèles pour la connexion sur les appareils mobiles

Accès aux informations d’identification sur les appareils suivez deux modèles de base pour la plateforme Microsoft Identity :

* Connexions assistées non intermédiaire
* Broker connexions assistées

#### <a name="non-broker-assisted-logins"></a>Connexions assistées non intermédiaire

Connexions assistées non intermédiaire sont expériences login qui se produisent en ligne avec l’application et utilisent le stockage local sur le périphérique pour cette application. Ce stockage peut être partagé entre les applications, mais les informations d’identification sont étroitement liées à l’application ou la suite des applications à l’aide de ces informations d’identification. Il s’agit de l’expérience que vous avez probablement été confronté dans de nombreuses applications mobiles dans laquelle vous entrez un nom d’utilisateur et mot de passe dans l’application proprement dite.

Ces noms d’accès ont les avantages suivants :

-  Expérience utilisateur existe entièrement dans l’application.
-  Informations d’identification peuvent être partagées entre les applications qui a signé par le même certificat, fournir une expérience d’authentification unique à votre suite d’applications.
-  Contrôle autour de l’expérience de connexion est fournie à l’application avant et après la connexion.

Ces noms d’accès ont les inconvénients suivants :

- Utilisateur ne peut pas être confronté à l’authentification unique sur toutes les applications qui utilisent un Microsoft Identity uniquement à travers ces Identities Microsoft possède et avez configuré votre application.
- Votre application ne peut pas être utilisé avec des fonctionnalités plus avancées comme accès conditionnel ou utilisez la gamme InTune de produits.
- Votre application ne peut pas en charge authentification par certificat pour les utilisateurs professionnels.

Voici une représentation du fonctionnement des kits de développement logiciel Microsoft identité avec le stockage de vos applications pour activer l’authentification unique partagé :

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Broker connexions assistées

Connexions assistée intermédiaire sont expériences de connexion qui se produisent dans l’application intermédiaire et utilisent le stockage et la sécurité de l’intermédiaire pour partager des informations d’identification pour toutes les applications sur l’appareil qui s’appuient sur la plateforme Microsoft Identity. Cela signifie que vos applications s’appuient sur l’intermédiaire afin de connecter les utilisateurs. Sur iOS et Android, ceux-ci sont fournis par le biais des applications téléchargeables que clients installer séparément ou peuvent être envoyés à l’appareil par une entreprise qui gère le périphérique pour leur utilisateur. Exemple de ce type d’application est l’application Azure authentificateur sur iOS. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré au système d’exploitation, connu techniquement en tant que l’intermédiaire d’authentification Web.
L’expérience varie selon la plate-forme et peut parfois être dangereuses pour les utilisateurs si elle n’est pas géré correctement. Vous êtes probablement plus familiarisé avec ce modèle si vous disposez de l’application Facebook installée et utilisez la fonctionnalité connexion Facebook dans une autre application. La plateforme Microsoft Identity s’appuie sur le même modèle.

Pour iOS que cela permet d’accéder à une transition » » animation où votre application est envoyée à l’arrière-plan pendant que les applications Azure authentificateur est fourni au premier plan pour l’utilisateur à sélectionner le compte ils souhaiteraient pour vous connecter à l’aide.  

Pour Android et Windows le sélecteur de compte est affiché en haut de votre application qui est moins dangereuses pour l’utilisateur.

#### <a name="how-the-broker-gets-invoked"></a>Comment l’intermédiaire est appelé

Si un agent compatible est installé sur l’appareil, comme l’application Azure authentificateur, kits de développement logiciel Microsoft identité effectuera automatiquement le travail de l’appel à l’intermédiaire pour vous lorsqu’un utilisateur indique qu’ils souhaitent se connecter à l’aide de n’importe quel compte à partir de la plateforme Microsoft Identity. Cela peut être un Account Microsoft un travail ou compte scolaire, ou un compte que vous fournissez et hôte dans Azure à l’aide de nos produits B2C et B2B. À l’aide de chiffrement et des algorithmes extrêmement sécurisés nous Assurez-vous que les informations d’identification sont demandées et remies à votre application de façon sécurisée. Les détails techniques exacte de ces mécanismes n’est pas publiée mais ont été développés avec collaboration par Apple et Google.

**Le développeur a le choix entre si le Kit de développement Microsoft identité l’intermédiaire des appels ou utilise le flux assisté non intermédiaire.** Cependant si le développeur choisit de ne pas utiliser le flux assistée intermédiaire ils perdent l’avantage d’exploiter les informations d’identification de l’authentification unique que l’utilisateur a peut-être déjà ajouté sur le périphérique ainsi qu’empêche leur application utilisée avec les fonctionnalités que Microsoft fournit ses clients tel que Access conditionnelle, les fonctionnalités de gestion des Intune, et l’authentification basée sur le certificat.

Ces noms d’accès ont les avantages suivants :

-  Utilisateur des expériences d’authentification unique de toutes leurs applications quel que soit le fournisseur.
-  Votre application peut tirer parti des fonctionnalités métier plus avancées comme accès conditionnel ou utilisez la gamme InTune de produits.
-  Votre application peut prendre en charge authentification par certificat pour les utilisateurs professionnels.
- Expérience de connexion beaucoup plus sûre que l’identité de l’application et de l’utilisateur sont vérifiées par l’application intermédiaire avec chiffrement et des algorithmes de sécurité supplémentaires.

Ces noms d’accès présentent les inconvénients suivants :

- Dans iOS passe l’utilisateur se déconnecter de l’expérience de votre application tandis que les informations d’identification sont choisies.
- Perte de la possibilité de gérer l’expérience de connexion pour vos clients dans votre application.



Voici une représentation du fonctionnement des kits de développement logiciel Microsoft identité avec les applications intermédiaire pour activer l’authentification unique :

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Grâce à ces informations en arrière-plan que vous devriez pouvoir pour mieux comprendre et implémenter l’authentification unique au sein de votre application à l’aide de la plateforme Microsoft Identity et SDK.


## <a name="enabling-cross-app-sso-using-adal"></a>L’activation de l’authentification unique entre-app à l’aide du terme ADAL

Ici, nous allons utiliser l’iOS ADAL SDK à :

- Activer l’authentification unique assisté non intermédiaire votre gamme d’applications
- Activer la prise en charge pour l’authentification unique assistée intermédiaire

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Activation de l’authentification unique pour non intermédiaire assisté l’authentification unique

Pour l’authentification unique assisté non intermédiaire entre les applications kits de développement logiciel Microsoft identité gère la complexité de l’authentification unique pour vous. Cela inclut la recherche de l’utilisateur dans le cache et mise à jour une liste des utilisateurs connectés vous permettant de la requête.

Pour activer l’authentification unique entre les applications que vous êtes propriétaire que vous avez besoin d’effectuer les opérations suivantes :

1. Vérifier que tous les votre utilisateur applications la même ID Client ou ID d’Application.
* Vous assurer que toutes vos applications partagent le même certificat de signature à partir d’Apple afin que vous puissiez partager les trousseaux
* Demander le bénéfice trousseau même pour chacun de vos applications.
* Informer les kits de développement logiciel Microsoft identité de la trousse partagée que vous souhaitez que nous.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>À l’aide de l’ID de Client même / ID de l’Application pour toutes les applications dans la suite des applications

Dans l’ordre de la plate-forme Microsoft Identity savoir qu’il a autorisé à partager des jetons entre vos applications, chacune de vos applications devrez partager la même ID Client ou ID d’Application. Il s’agit de l’identificateur unique qui vous a été fourni lorsque vous avez enregistré votre première application dans le portail.

Vous vous demandez peut-être comment vous allez identifier différentes applications au service Microsoft Identity si elle utilise la même ID d’Application. La réponse est avec les **Rediriger URI**. Chaque application peut avoir plusieurs URI rediriger enregistré dans le portail d’intégration. Chaque application dans votre suite a un autre URI de redirection. Exemple d’à quoi cela ressemble est inférieur à :

URI de redirection App1 :`x-msauth-mytestiosapp://com.myapp.mytestapp`

URI de redirection App2 :`x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI de redirection App3 :`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Celles-ci sont imbriquées sous le même ID client / ID de l’application et recherchée en fonction de la redirection URI vous revenez à nous dans votre configuration SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Notez que le format de ces URI rediriger sont décrites ci-dessous. Vous pouvez utiliser n’importe quel URI rediriger, sauf si vous voulez prendre en charge l’intermédiaire, auquel cas ils doivent ressembler à ce qui précède*



#### <a name="create-keychain-sharing-between-applications"></a>Créer le partage entre les applications de trousseau

Activer le partage de trousseau dépasse le cadre de ce document et couverts par Apple dans son document [Ajout de fonctionnalités](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Ce qui est important est que vous décidez que votre trousseau à appeler et ajoutez cette fonctionnalité dans toutes vos applications.

Lorsque vous êtes droits de configurer correctement vous devraient voir un fichier dans le répertoire de votre projet intitulé `entitlements.plist` qui contient quelque chose qui ressemble à ce qui suit :

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Une fois que vous avez les droits de trousseau activées dans chacune de vos applications, et vous êtes prêt à utiliser l’authentification unique, informer le Kit de développement Microsoft identité des votre trousseau en utilisant le paramètre suivant dans votre `ADAuthenticationSettings` avec le paramètre suivant :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Lorsque vous partagez un trousseau au sein de vos applications n’importe quelle application peut supprimer des utilisateurs ou pire supprimer tous les jetons au sein de votre application. Il s’agit particulièrement désastreux si vous disposez d’applications qui s’appuient sur les jetons de travail en arrière-plan. Partage d’un trousseau signifie que vous devez être très prudent dans toutes les supprime opérations par le biais du SDK identité Microsoft.

Voilà ! Le Kit de développement Microsoft identité partagera maintenant les informations d’identification dans toutes vos applications. La liste des utilisateurs est également partagée pour plusieurs instances de l’application.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Activation de l’authentification unique pour intermédiaire assisté l’authentification unique

La possibilité pour une application peut utiliser n’importe quel intermédiaire est installé sur l’appareil est **désactivée par défaut**. Pour pouvoir utiliser votre application par l’intermédiaire, vous devez effectuer une configuration supplémentaire et ajouter du code à votre application.

Les étapes à suivre sont :

1. Activer le mode intermédiaire d’appel du code de votre application au SDK MS.
2. Établir une nouvelle redirection URI et qui fournissent à l’application et l’inscription de votre application.
3. Enregistrer un modèle d’URL.
4. prise en charge iOS9 : ajouter une autorisation à votre fichier info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Étape 1 : Activer le mode intermédiaire dans votre application
La possibilité pour votre application pour utiliser l’intermédiaire est activée lorsque vous créez le « contexte » ou la configuration initiale de votre objet d’authentification. Pour cela, en définissant le type d’informations d’identification de votre code :

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
La `AD_CREDENTIALS_AUTO` paramètre permettra le Kit de développement Microsoft identité tenter de faire appel à l’intermédiaire, `AD_CREDENTIALS_EMBEDDED` empêchera le Kit de développement Microsoft identité d’appel à l’intermédiaire.

#### <a name="step-2-registering-a-url-scheme"></a>Étape 2 : Inscription d’un modèle d’URL
La plateforme Microsoft Identity utilise des URL d’appeler l’intermédiaire et rendre ensuite le contrôle à votre application. Pour terminer cette aller-retour vous avez besoin d’un modèle d’URL enregistré pour votre application la plateforme Microsoft Identity saurez sur. Il peut s’agir en plus des autres schémas application que vous pouvez avoir précédemment enregistré avec votre application.

> [AZURE.WARNING]
Nous vous recommandons d’établir le schéma d’URL relativement unique pour réduire les risques d’une autre application utilisant le même schéma d’URL. Apple n’applique pas l’unicité des schémas d’URL qui sont enregistrés dans l’app store.

Voici un exemple de comment cela apparaît dans la configuration de votre projet. Vous pouvez également le faire dans XCode ainsi :

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Étape 3 : Établir une nouvelle redirection URI avec votre modèle d’URL

Afin de vous assurer que nous retournent toujours les jetons d’informations d’identification pour l’application correcte, nous avons besoin pour vous assurer que nous rappeler à votre application de manière à vérifier le système d’exploitation iOS. Le système d’exploitation iOS signale aux applications intermédiaire Microsoft l’ID ensemble de guides de l’application appeler. Cela ne peut pas être falsifié par une application non fiables. Par conséquent, nous Exploitez Ceci ainsi que l’URI de notre application intermédiaire pour vous assurer que les jetons sont retournés à l’application correcte. Nous vous demander d’établir cette redirection unique URI à la fois dans votre application et de définir en tant que rediriger URI dans notre portail pour les développeurs.

Votre URI de redirection doit être placé dans la forme correcte de :

`<app-scheme>://<your.bundle.id>`

ex : *x-msauth-mytestiosapp://com.myapp.mytestapp*

Cet URI rediriger doit être indiquée dans votre inscription de l’application à l’aide du [portail classique Azure](https://manage.windowsazure.com/). Pour plus d’informations sur l’inscription de l’application Azure AD, voir [intégration avec Azure Active Directory](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Étape 3 : ajouter une redirection URI dans votre portail d’application et de développement pour prendre en charge d’authentification par certificat

Pour prendre en charge le certificat d’authentification basée sur les qu'une deuxième « msauth » doit être enregistré dans votre application et le [portail classique Azure](https://manage.windowsazure.com/) pour gérer l’authentification par certificat si vous souhaitez ajouter qui prennent en charge dans votre application.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex : *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Étape 4 : iOS9 : ajouter un paramètre de configuration dans votre application

Terme ADAL utilise – canOpenURL : pour vérifier si l’intermédiaire est installé sur l’appareil. Dans iOS Apple 9 verrouillés que pouvez interroger jeux une application. Vous devrez ajouter « msauth » à la section LSApplicationQueriesSchemes de votre `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Vous avez configuré l’authentification unique !

Maintenant le Kit de développement Microsoft identité automatiquement à la fois partager des informations d’identification entre vos applications et appeler l’intermédiaire s’il existe sur leur appareil.
