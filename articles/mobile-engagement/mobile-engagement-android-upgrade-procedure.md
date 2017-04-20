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


#<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure de notre SDK dans votre application, vous devez prendre en compte les points suivants lorsque le Kit de développement de la mise à niveau.

Vous devrez quelques procédures à suivre si vous avez manqué plusieurs versions du Kit de développement. Par exemple, si vous migrez 1.4.0 à 1.6.0 que vous devez tout d’abord, procédez comme « de 1.4.0 à 1.5.0 » puis la procédure « de 1.5.0 à 1.6.0 ».

Quelle que soit la version mise à niveau à partir de, vous devez remplacer la `mobile-engagement-VERSION.jar` par la nouvelle.

##<a name="from-420-to-421"></a>À partir de 4.2.0 à 4.2.1

Cette étape peut réellement être effectuée sur n’importe quelle version du Kit de développement, il est une amélioration de la sécurité lorsque vous intégrez les activités accessibles.

Vous devriez maintenant ajouter `exported="false"` à toutes les activités accessibles.

Activités portée doivent maintenant ressembler à ceci votre `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

##<a name="from-400-to-410"></a>À partir de 4.0.0 à 4.1.0

Kit de développement logiciel maintenant poignée nouvelle autorisation modèle M Android.

Si vous utilisez les fonctionnalités de l’emplacement ou les notifications de vue d’ensemble, consultez [Cette section](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Outre le nouveau modèle d’autorisation, nous prennent désormais en charge des fonctionnalités emplacement configuration en cours d’exécution.
Nous sommes toujours compatibles avec les paramètres manifeste d’emplacement, mais il est désormais déconseillée. Pour utiliser la configuration de l’exécution, supprimez les sections suivantes à partir de votre ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

et lire [cette procédure mis à jour](mobile-engagement-android-integrate-engagement.md#location-reporting) pour utiliser à la place de configuration de l’exécution.

##<a name="from-300-to-400"></a>À partir de 3.0.0 à 4.0.0

### <a name="native-push"></a>Push natif

Push native (GCM/ADM) est désormais également utilisé pour dans les notifications d’application afin que vous devez configurer les informations d’identification native push pour n’importe quel type de campagne push.

Si pas déjà, suivez [cette procédure](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Intégration de portée a été modifiée dans ``AndroidManifest.xml``.

Remplacez ceci :

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Par

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Il est probablement un écran de chargement maintenant lorsque vous cliquez sur une annonce (avec texte/contenu web) ou un sondage.
Vous devez ajouter pour ces campagnes à travailler dans 4.0.0 :

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Ressources

Incorporer la nouvelle `res/layout/engagement_loading.xml` fichier dans votre projet.

##<a name="from-240-to-300"></a>À partir de 2.4.0 à 3.0.0

Voici comment migrer une intégration SDK à partir du service Capptain offert par Capptain SAS dans une application optimisée par Azure Mobile Engagement. Si vous effectuez une migration depuis une version antérieure, veuillez consulter le site web Capptain pour migrer vers 2.4.0 tout d’abord, puis appliquez la procédure suivante.

>[AZURE.IMPORTANT] Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous surligne uniquement comment effectuer une migration de l’application client. Migrer le Kit de développement dans l’application ne doit pas migrer vos données à partir des serveurs Capptain aux serveurs Mobile Engagement.

### <a name="jar-file"></a>Fichier JAR

Remplacer `capptain.jar` par `mobile-engagement-VERSION.jar` dans votre `libs` dossier.

### <a name="resource-files"></a>Fichiers de ressources

Chaque fichier de ressources que nous fournies (précédé `capptain_`) doit être remplacés par les nouveaux (le préfixe `engagement_`).

Si vous avez personnalisé ces fichiers, vous devez appliquer à nouveau votre personnalisation sur les nouveaux fichiers, **tous les identificateurs dans les fichiers de ressources également ont été renommés**.

### <a name="application-id"></a>ID de l’application

Maintenant Engagement utilise une chaîne de connexion pour configurer les identificateurs SDK telles que l’identificateur d’application.

Vous devez utiliser `EngagementAgent.init` méthode dans votre activité Lanceur comme suit :

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion pour votre application s’affiche dans le portail Azure.

Veuillez supprimer tout appel à `CapptainAgent.configure` en tant que `EngagementAgent.init` remplace cette méthode.

La `appId` n’est plus peut être configuré avec `AndroidManifest.xml`.

Veuillez supprimer cette section à partir de votre `AndroidManifest.xml` si vous avez :

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API Java

Chaque appel à n’importe quelle classe Java de notre kit de développement logiciel doit donc être changé ; par exemple, `CapptainAgent.getInstance(this)` doit être renommé `EngagementAgent.getInstance(this)`, `extends CapptainActivity` doit être renommé `extends EngagementActivity` etc....

Si vous ont été intégré aux fichiers de préférences de l’agent par défaut, le nom de fichier par défaut est désormais `engagement.agent` et la clé est `engagement:agent`.

Lorsque vous créez des annonces web, le classeur Javascript est désormais `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

De nombreuses modifications s’est-il passé, le service n’est pas partagé plus et un grand nombre de destinataires ne peuvent pas être exportés plus.

La déclaration du service est simplifiée ; Supprimez le filtre intention et toutes les métadonnées qu’elle contient et ajoutez `exportable=false`.

Ainsi que tout est renommé pour utiliser engagement.

Il se présente désormais comme :

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Lorsque vous souhaitez activer les journaux de test, les métadonnées maintenant a été déplacée vers la balise de l’application et a été renommée :

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Toutes les autres données meta simplement ont été renommées, voici la liste complète (cours de modification de nom uniquement celles que vous utilisez) :

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play et SmartAd suivi a été supprimé de SDK, il vous suffit de supprimer ce sans remise :

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Les activités accessibles sont désormais déclarées comme suit :

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Si vous avez des activités personnalisées accessibles, vous devez uniquement modifier les actions prévues pour correspondre à des options `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Les récepteurs de diffusion ont été renommés, ainsi que nous ajoutons maintenant `exported=false`. Voici la liste complète des récepteurs avec la nouvelle spécification, (cours de modification de nom uniquement celles que vous utilisez) :

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Récepteur de suivi a été supprimé, afin que vous devez supprimer cette section :

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Notez que la déclaration de votre implémentation de la diffusion récepteur **EngagementMessageReceiver** a changé dans la `AndroidManifest.xml`. C’est parce que l’API pour envoyer ou supprimer des messages XMPP arbitraires d’arbitraires entités XMPP et l’API pour envoyer et recevoir des messages entre les périphériques ont été supprimés. Par conséquent, vous devez également supprimer les rappels suivants à partir de votre implémentation **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

et

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

supprimer puis n’importe quel appel sur **EngagementAgent** pour :

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

et

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Configuration proguard peut être affectée par redésignation, les règles sont maintenant ressembler à :

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
