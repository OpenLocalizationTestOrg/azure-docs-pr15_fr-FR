<properties
    pageTitle="Intégration de SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Comment intégrer GCM Engagement Mobile

> [AZURE.IMPORTANT] Vous devez suivre la procédure d’intégration décrite dans la comment intégrer Engagement sur Android document avant de suivre ce guide.
>
> Ce document est utile uniquement si vous avez déjà intégré le module accessibles et le plan pour pousser appareils Google Play. Pour intégrer des campagnes accessibles dans votre application, lisez d’abord comment intégrer Engagement atteindre sur Android.

##<a name="introduction"></a>Introduction

Intégration GCM permet à votre application de poussée.

Charges GCM poussées vers le Kit de développement toujours contient la `azme` clés dans l’objet de données. Ainsi, si vous utilisez GCM pour un autre objet dans votre application, vous pouvez filtrer push basé sur cette touche.

> [AZURE.IMPORTANT] Uniquement les appareils exécutant Android 2.2 ou au-dessus, ayant Google Play installé et ayant Google connexion arrière-plan activé peut être envoyée à l’aide de GCM ; Toutefois, vous pouvez intégrer ce code en toute sécurité sur les appareils non pris en charge (il utilise simplement modes).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Créer un projet Google Cloud messagerie avec clé API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Intégration du Kit de développement

### <a name="managing-device-registrations"></a>Gestion des enregistrements appareil

Chaque appareil doit envoyer une commande d’inscription sur les serveurs de Google, sinon ils ne peuvent pas être atteintes.

Un périphérique peut également annuler l’enregistrement à partir de notifications GCM (l’appareil est supprimée automatiquement si l’application est désinstallée).

Si vous n’utilisez pas [Google Play SDK] ou vous ne déjà envoyez l’objectif de l’enregistrement, vous pouvez apporter Engagement enregistre automatiquement le périphérique à votre place.

Pour ce faire, ajoutez les éléments suivants à votre `AndroidManifest.xml` fichier, à l’intérieur du `<application/>` balise :

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Communiquer les id de l’inscription au service Engagement transmission et recevoir des notifications

Afin de communiquer l’id de l’enregistrement du périphérique au service Engagement transmission et recevoir des notifications de son, ajoutez les éléments suivants à votre `AndroidManifest.xml` fichier, à l’intérieur du `<application/>` ajouter des balises (même si vous gérez les enregistrements appareil vous-même) :

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Assurez-vous que vous disposez des autorisations suivantes votre `AndroidManifest.xml` (une fois la `</application>` balise).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Accorder l’accès Mobile Engagement à votre clé d’API GCM

Suivez [ce guide](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) pour accorder l’accès Mobile Engagement à votre clé API GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
