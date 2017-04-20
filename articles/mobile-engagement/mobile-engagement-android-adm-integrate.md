<properties
    pageTitle="Intégration de SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-integrate-adm-with-engagement"></a>Comment intégrer ADM Engagement

> [AZURE.IMPORTANT] Vous devez suivre la procédure d’intégration décrite dans la comment intégrer Engagement sur Android document avant de suivre ce guide.
>
> Ce document est utile uniquement si vous avez intégré déjà le module accessibles et le plan pour pousser appareils Amazon. Pour intégrer des campagnes accessibles dans votre application, lisez d’abord comment intégrer Engagement atteindre sur Android.

##<a name="introduction"></a>Introduction

Intégration ADM permet à votre application de poussée lorsque vous ciblez appareils Android Amazon.

Charges ADM poussées vers le Kit de développement toujours contient la `azme` clés dans l’objet de données. Ainsi, si vous utilisez ADM pour un autre objet dans votre application, vous pouvez filtrer push basé sur cette touche.

> [AZURE.IMPORTANT] Seuls les périphériques Amazon Kindle exécutant Android 4.0.3 ou ci-dessus sont pris en charge par Amazon appareil messagerie ; Toutefois, vous pouvez intégrer ce code en toute sécurité sur les autres appareils.

##<a name="sign-up-to-adm"></a>S’inscrire à ADM

Si vous n’est déjà fait, vous devez activer ADM sur votre compte Amazon.

La procédure est détaillée sur : [<https://developer.amazon.com/sdk/adm/credentials.html>].

À la fin de la procédure, vous obtenez :

-   OAuth les informations d’identification (un ID de Client et un code Secret Client) pour Engagement doivent pouvoir effectuer vos périphériques de transmission.
-   Une clé API doit être intégré à votre application.

##<a name="sdk-integration"></a>Intégration du Kit de développement

### <a name="managing-device-registrations"></a>Gestion des enregistrements appareil

Chaque appareil doit envoyer une commande d’inscription aux serveurs ADM, sinon ils ne peuvent pas être atteintes.

Si vous utilisez déjà la [bibliothèque cliente ADM]et que vous disposez déjà [ADM intégrée] , vous pouvez accéder directement à recevoir d’android-sdk-adm.

Si vous n’avez pas encore intégré ADM, Engagement comporte un moyen plus simple d’activer dans votre application :

Modifier votre `AndroidManifest.xml` fichier :

-   Ajouter de l’espace de noms Amazon, ce fichier doit commencer à ceci :

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   À l’intérieur de la `<application/>` , ajoutez cette section :

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   Après avoir ajouté la balise amazon, vous pouvez avoir une erreur de génération si votre cible Build du projet est inférieur à 2.1 Android. Vous devez utiliser une cible de génération **Android 2.1 +** (ne vous inquiétez pas, vous pouvez toujours avoir un `minSdkVersion` la valeur 4).
-   Intégrer la clé d’API ADM en tant qu’actif en suivant [cette procédure].

Suivez ensuite les instructions des sections suivantes.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Communiquer les id de l’inscription au service Engagement transmission et recevoir des notifications

Afin de communiquer l’id de l’enregistrement du périphérique au service Engagement transmission et recevoir des notifications de son, ajoutez les éléments suivants à votre `AndroidManifest.xml` fichier, à l’intérieur du `<application/>` ajouter des balises (même si vous utilisez ADM sans Engagement) :

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Assurez-vous que vous disposez des autorisations suivantes votre `AndroidManifest.xml` (avant la `</application>` balise).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>Informations d’identification accorder l’Engagement OAuth

Envoyer vos informations d’identification OAuth (ID Client et Secret Client) dans le portail d’Engagement.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[Bibliothèque cliente ADM]:https://developer.amazon.com/sdk/adm/setup.html
[ADM intégrée]:https://developer.amazon.com/sdk/adm/integrating-app.html
[Cette procédure]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
