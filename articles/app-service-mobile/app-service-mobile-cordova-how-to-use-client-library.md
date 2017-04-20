<properties
    pageTitle="Comment utiliser le plug-in de Apache Cordova pour les applications mobiles Azure"
    description="Comment utiliser le plug-in de Apache Cordova pour les applications mobiles Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Comment utiliser la bibliothèque Apache Cordova Client pour les applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous apprend à effectuer des scénarios courants à l’aide de la dernière [Plug-in de Cordova Apache pour applications Mobile Azure]. Si vous débutez dans les applications Mobile Azure, première complète [Azure Mobile applications Quick Start] pour créer un serveur principal, créez une table, puis télécharger un projet Apache Cordova prédéfinie. Dans ce guide, nous concentrer sur le plug-in côté client Apache Cordova.

## <a name="supported-platforms"></a>Plateformes prises en charge

Ce SDK prend en charge Cordova Apache v6.0.0 et version ultérieure sur iOS, Android et Windows appareils.  Plates-formes prises en charge est la suivante :

* API Android (KitKat via la commande) 19-24
* iOS 8.0 et versions ultérieures.
* Windows Phone 8.0
* Windows Phone 8.1
* Plateforme Windows universel

##<a name="Setup"></a>Le programme d’installation et configuration requise

Ce guide suppose que vous avez créé un serveur principal avec une table. Ce guide part du principe que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que vous avez ajouté le plug-in de Cordova Apache à votre code.  Si vous n'avez pas fait, vous pouvez ajouter le plug-in Cordova Apache à votre projet sur la ligne de commande :

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Pour plus d’informations sur la création de [votre première application Cordova Apache], consultez leur documentation.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Comment : authentification des utilisateurs

Azure Application Service prend en charge l’authentification et l’autorisation des utilisateurs de l’application à l’aide de divers fournisseurs d’identité externes : Facebook, Google, Account Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables à limiter l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation scripts de serveur. Pour plus d’informations, consultez le didacticiel [prise en main d’authentification] .

Lorsque vous utilisez l’authentification dans une application Cordova Apache, les plug-ins Cordova suivants doivent être disponibles :

* [Dispositif de plug-in cordova]
* [plug-in-cordova-inappbrowser]

Deux flux d’authentification sont prises en charge : un flux de serveur et un flux de client.  Le flux server fournit l’expérience d’authentification la plus simple, car il repose sur l’interface du fournisseur web d’authentification. Le flux du client autorise pour intégration plus étroite avec des fonctionnalités spécifiques au périphérique, tel que seul-authentification car il repose sur spécifiques du fournisseur spécifique à l’appareil SDK.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Comment : configurer votre Service d’application Mobile pour les URL de redirection externes.

Plusieurs types d’applications Apache Cordova utilisent une fonctionnalité de boucle pour gérer les flux OAuth UI.  Flux OAuth UI sur hôte local entraîner des problèmes dans la mesure où le service d’authentification sait uniquement comment utiliser votre service par défaut.  Voici quelques exemples de flux OAuth UI problématiques :

- L’émulateur ondulation.
- Instantané recharger avec ioniques.
- Exécutez la version serveur mobile localement
- Exécuter le système principal mobile dans un autre Service d’application Azure à l’authentification fournissant un.

Suivez ces instructions pour ajouter vos paramètres locaux à la configuration :

1. Connectez-vous au [portail Azure]
2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre application Mobile.
3. Cliquez sur **Outils**
4. Cliquez sur **Explorateur de ressources** dans le menu observer, puis cliquez sur **OK**.  Un onglet ou une nouvelle fenêtre s’ouvre.
5. Développez la **configuration de la**, **authsettings** nœuds de votre site Web dans le volet de navigation gauche.
6. Cliquez sur **Modifier**
7. Recherchez l’élément « allowedExternalRedirectUrls ».  Elle peut être définie sur null ou un tableau de valeurs.  Modifier la valeur à la valeur suivante :

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Remplacer les URL avec l’URL de votre service.  Les exemples sont « http://localhost:3000 » (par exemple de service Node.js), ou « http://localhost:4400 » (pour le service ondulation).  Toutefois, ces URL est exemples - votre situation, notamment pour les services mentionnés dans les exemples, peuvent être différents.
8. Cliquez sur le bouton **En lecture/écriture** dans le coin supérieur droit de l’écran.
9. Cliquez sur le vert **placer** le bouton.

Les paramètres sont enregistrés à ce stade.  Ne fermez pas la fenêtre du navigateur jusqu'à ce que les paramètres ont terminé l’enregistrement.
Ajoutez également ces URL en boucle les paramètres CORS à votre Service d’application :

1. Connectez-vous au [portail Azure]
2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre application Mobile.
3. La carte de paramètres s’ouvre automatiquement.  Si elle n’est pas, cliquez sur **Tous les paramètres**.
4. Cliquez sur **CORS** sous le menu de l’API.
5. Entrez l’URL que vous souhaitez ajouter dans la zone fournie, puis appuyez sur ENTRÉE.
6. Entrez l’URL supplémentaires selon vos besoins.
7. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

Il faut environ 10 à 15 secondes pour les nouveaux paramètres prennent effet.

##<a name="register-for-push"></a>Comment : s’inscrire pour les Notifications Push

Installer le [plug-in-phonegap-push] pour gérer les notifications push.  Ce plug-in peut être facilement ajouté à l’aide de la `cordova plugin add` commande sur la ligne de commande, ou via le programme d’installation du plug-in Git dans Visual Studio.  Le code suivant dans votre application Apache Cordova enregistre votre appareil pour les notifications push :

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilisez le Kit de développement de Hubs de Notification pour envoyer les notifications push à partir du serveur.  Ne jamais envoyer de notifications push directement à partir de clients. Cela peut servir à déclencher un refus de service contre Hubs de Notification ou la solution.  La solution pourrait interdire le trafic de votre résulte ces attaques.

<!-- URLs. -->
[Portail Azure]: https://portal.azure.com
[Guide de démarrage rapide d’applications mobiles Azure]: app-service-mobile-cordova-get-started.md
[Prise en main d’authentification]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in de Cordova Apache d’applications mobiles Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[votre application Apache Cordova première]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[plug-in-PhoneGap-push]: https://www.npmjs.com/package/phonegap-plugin-push
[Dispositif de plug-in cordova]: https://www.npmjs.com/package/cordova-plugin-device
[plug-in-cordova-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
