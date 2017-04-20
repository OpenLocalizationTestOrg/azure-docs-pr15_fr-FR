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

#<a name="how-to-integrate-engagement-reach-on-android"></a>Comment intégrer Engagement accessibles sur Android

> [AZURE.IMPORTANT] Vous devez suivre la procédure d’intégration décrite dans la comment intégrer Engagement sur Android document avant de suivre ce guide.

##<a name="standard-integration"></a>Intégration standard

Le Kit de développement atteigne requiert la **bibliothèque d’assistance Android (v4)**.

Le moyen le plus rapide pour ajouter la bibliothèque à votre projet dans **Eclipse** est `Right click on your project -> Android Tools -> Add Support Library...`.

Si vous n’utilisez pas Eclipse, vous pouvez lire les instructions [ici].

Copiez les fichiers de ressources accessibles le Kit de développement de votre projet :

-   Copiez les fichiers à partir de la `res/layout` dossier fourni avec le Kit de développement dans le `res/layout` dossier de votre application.
-   Copiez les fichiers à partir de la `res/drawable` dossier fourni avec le Kit de développement dans le `res/drawable` dossier de votre application.

Modifier votre `AndroidManifest.xml` fichier :

-   Ajoutez la section suivante (entre la `<application>` et `</application>` balises) :

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
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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

-   Vous avez besoin de cette autorisation à relire notifications système qui n’ont pas été activées au démarrage (dans le cas contraire ils sont conservées sur disque mais ne s’affichent plus, avez-vous réellement celui-ci).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Spécifier une icône utilisée pour les notifications (à la fois dans l’application et système sont ceux qui) en copiant et en modifiant la section suivante (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] Cette section est **obligatoire** si vous prévoyez d’utiliser des notifications système lors de la création des campagnes accessibles. Android empêche les notifications système sans icônes d’affiché. Si vous omettez cette section, vos utilisateurs finaux ne pourra pas donc pas pour les recevoir.

-   Si vous créez des campagnes marketing avec les notifications système à l’aide de la vue d’ensemble, vous devez ajouter les autorisations suivantes (une fois la `</application>` balise) s’il est manquant :

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   Sur M Android et si votre application cible niveau API Android 23 ou version ultérieure, ``WRITE_EXTERNAL_STORAGE`` autorisation exige l’approbation de l’utilisateur. Lisez [Cette section](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   Pour les notifications système vous pouvez également spécifier dans la campagne portée si le périphérique doit sonner et/ou vibrations. Pour qu’il fonctionne, vous disposez pour vous assurer que vous avez déclaré l’autorisation suivante (une fois la `</application>` balise) :

            <uses-permission android:name="android.permission.VIBRATE" />

    Sans cette autorisation, Android empêche les notifications système ne soient affichées si que vous avez coché la simultanée ou l’option a Vibreur dans le Gestionnaire de communiquer aux campagnes.

-   Si vous créez votre application à l’aide de **ProGuard** et que vous avez erreurs liées à la bibliothèque de prise en charge Android ou le fichier jar Engagement, ajoutez les lignes suivantes à votre `proguard.cfg` fichier :

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Push natif

À présent que vous avez configuré le module accessibles, vous devez configurer native push pour pouvoir recevoir les campagnes marketing sur l’appareil.

Nous prenons en charge deux services sur Android :

  - Appareils de Google Play : utiliser [Google Cloud messagerie] en suivant le guide [comment intégrer GCM avec guide d’Engagement](mobile-engagement-android-gcm-integrate.md) .
  - Appareils Amazon : utiliser [La messagerie appareil Amazon] en suivant le guide [comment intégrer ADM avec guide d’Engagement](mobile-engagement-android-adm-integrate.md) .

Si vous souhaitez cibler appareils Amazon et Google Play, son est possible d’avoir tous les éléments de 1 AndroidManifest.xml/APK pour le développement. Mais lors de l’envoi vers Amazon, ils peuvent rejeter votre demande s’il s’avère code GCM.

Vous devez utiliser plusieurs APKs dans ce cas.

**Votre application est maintenant prête à recevoir et afficher des campagnes accessibles !**

##<a name="how-to-handle-data-push"></a>Comment gérer les push de données

### <a name="integration"></a>Intégration

Si vous souhaitez que votre application doit pouvoir recevoir des campagnes de données accessibles, vous devez créer une classe sous-adresse de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` et de référence dans la `AndroidManifest.xml` fichier (entre la `<application>` et/ou `</application>` balises) :

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Vous pouvez remplacer le `onDataPushStringReceived` et `onDataPushBase64Received` rappels. Voici un exemple :

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Catégorie

Le paramètre category est facultatif lorsque vous créez une campagne Push de données et permet de que vous permettent de filtrer des données pousse. Cette option est utile si vous avez plusieurs récepteurs de diffusion gestion des différents types de données push, ou si vous souhaitez distribuer des différents types de `Base64` données et que vous souhaitez pour identifier leur type avant d’analyser les.

### <a name="callbacks-return-parameter"></a>Paramètre de retour des rappels

Voici quelques conseils pour gérer correctement le paramètre de retour de `onDataPushStringReceived` et `onDataPushBase64Received`:

-   Un récepteur de diffusion doit renvoyer `null` dans le rappel si elle ne sait pas comment gérer une campagne de données. Vous devez utiliser la catégorie pour déterminer si votre récepteur de diffusion doit gérer la diffusion de données ou non.
-   Une de du récepteur de diffusion doit renvoyer `true` dans le rappel si elle accepte la diffusion de données.
-   Une de du récepteur de diffusion doit renvoyer `false` dans le rappel s’il reconnaît la transmission de données, mais ignore pour une raison quelconque. Par exemple, retourner `false` lorsque les données reçues ne sont pas valides.
-   Si une diffusion de récepteur renvoie `true` tandis que vous une autre une renvoie `false` pour le même push de données, le comportement n’est pas défini, faites-le jamais.

Le type de retour est utilisé uniquement pour les statistiques de portée :

-   `Replied`est incrémenté si un des récepteurs diffusion renvoyées `true` ou `false`.
-   `Actioned`est incrémenté uniquement si une des récepteurs de diffusion renvoyées `true`.

##<a name="how-to-customize-campaigns"></a>La personnalisation des campagnes marketing

Pour personnaliser des campagnes marketing, vous pouvez modifier les mises en page fournis dans le SDK atteindre.

Vous devez garder tous les identificateurs utilisés dans les mises en page et conserver les types d’affichages qui utilisent un identificateur, en particulier pour les affichages de texte et image. Certains affichages servent uniquement à afficher ou masquer des zones afin de leur type peut être modifié. Vérifiez le code source si vous souhaitez modifier le type d’un affichage dans les mises en page fournies.

### <a name="notifications"></a>Notifications

Il existe deux types de notifications : notifications système et dans l’application qui utilisent des fichiers de mise en page différentes.

#### <a name="system-notifications"></a>Notifications système

Pour personnaliser les notifications système, vous devez utiliser les **catégories**. Vous pouvez accéder à des [catégories](#categories).

#### <a name="in-app-notifications"></a>Notifications dans l’application

Par défaut, une notification dans l’application est une vue dynamique ajouté à l’interface utilisateur activité actuelle grâce à la méthode Android `addContentView()`. Cette option est appelée une superposition de notification. Les superpositions de notification sont parfaites pour une intégration rapide car ils ne nécessitent pas vous permet de modifier une disposition dans votre application.

Pour modifier l’apparence de votre superpositions de notification, vous pouvez simplement modifier le fichier `engagement_notification_area.xml` à vos besoins.

> [AZURE.NOTE] Le fichier `engagement_notification_overlay.xml` est celui qui est utilisé pour créer une superposition de notification, notamment le fichier `engagement_notification_area.xml`. Vous pouvez également personnaliser selon vos besoins (par exemple pour positionner la zone de notification dans la superposition).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Inclure la mise en page de la notification dans le cadre d’une disposition d’activité

Superpositions sont parfaites pour une intégration rapide mais peuvent être pas pratique ou avoir des effets secondaires dans des cas particuliers. Le système superposition peut être personnalisé à un niveau d’activité, ce qui facilite empêcher les effets secondaires des activités spéciales.

Vous pouvez décider d’inclure notre disposition notification dans votre mise en page existante grâce à l’instruction Android **inclure** . Voici un exemple d’une modification `ListActivity` disposition contenant uniquement un `ListView`.

**Avant d’intégration d’Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Après l’intégration d’Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Dans cet exemple, nous avons ajouté un conteneur parent étant donné que la mise en page d’origine utilisé un affichage de liste en tant que l’élément de niveau supérieur. Nous avons également ajouté `android:layout_weight="1"` pour pouvoir ajouter une vue en dessous d’un affichage de liste configuré avec `android:layout_height="fill_parent"`.

Le Kit de développement atteigne Engagement détecte automatiquement que la mise en page de notification est inclus dans cette activité et ajoutera pas une superposition de cette activité.

> [AZURE.TIP] Si vous utilisez un ListActivity dans votre application, une superposition de portée visible vous empêchera de réponse à un clic sur des éléments dans la liste plus. Il s’agit d’un problème connu. Pour contourner ce problème, nous suggérer vous afin d’incorporer la mise en page de notification dans votre propre disposition activité liste comme dans l’exemple précédent.

##### <a name="disabling-application-notification-per-activity"></a>Désactivation de la notification d’application par activité

Si vous ne voulez pas la superposition à ajouter à votre activité, et si vous n’incluez pas la mise en page de notification dans votre propre mise en page, vous pouvez désactiver la superposition pour cette activité dans le `AndroidManifest.xml` en ajoutant un `meta-data` section comme dans l’exemple suivant :

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Catégories

Lorsque vous modifiez les mises en page fournies, vous modifiez l’apparence de toutes vos notifications. Les catégories permettent de définir différentes apparences ciblées (éventuellement comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne accessibles. N’oubliez pas que catégories également vous permettent de personnaliser les annonces et les sondages, qui est décrit plus loin dans ce document.

Pour enregistrer un gestionnaire de catégorie pour les notifications, vous devez ajouter un appel lors de l’initialisation de l’application.

> [AZURE.IMPORTANT] Lisez l’avertissement concernant l’attribut android : processus \<android-sdk-engagement-process\> dans la comment intégrer Engagement sur Android sujet avant de poursuivre.

L’exemple suivant suppose que vous l’avertissement précédent d’accusé de réception et que vous utilisez une classe sous-adresse de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

La `MyNotifier` objet est l’implémentation du Gestionnaire de catégorie de notification. Il s’agit d’une mise en œuvre de la `EngagementNotifier` interface ou une classe sub de l’implémentation par défaut : `EngagementDefaultNotifier`.

Notez que le même notificateur peut gérer plusieurs catégories, vous pouvez les enregistrer comme suit :

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Pour remplacer l’implémentation de catégorie par défaut, vous pouvez enregistrer votre implémentation comme dans l’exemple suivant :

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

La catégorie actuelle utilisée dans un gestionnaire est passée comme un paramètre dans la plupart des méthodes que vous pouvez ignorer dans `EngagementDefaultNotifier`.

Il est passé en tant qu’un `String` paramètre ou indirectement dans un `EngagementReachContent` objet qui compte un `getCategory()` méthode.

Vous pouvez modifier la plupart du processus de création d’une notification redéfinir les méthodes sur `EngagementDefaultNotifier`, pour la personnalisation plus avancée n’hésitez pas à consulter la documentation technique et le code source.

##### <a name="in-app-notifications"></a>Notifications dans l’application

Si vous voulez simplement utiliser d’autres dispositions pour une catégorie spécifique, vous pouvez implémenter ceci comme dans l’exemple suivant :

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemple de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Comme vous pouvez le voir, l’identificateur de mode superposition est différent de celui standard. Il est important que chaque mise en page utiliser un identificateur unique pour les superpositions.

**Exemple de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Comme vous pouvez le voir, l’identificateur de vue de zone de notification est différent de celui standard. Il est important que chaque mise en page utilise un identificateur unique pour la zone de notification.

Cet exemple simple de catégorie rend notifications d’application (ou dans l’application) affichées dans la partie supérieure de l’écran. Nous n’a pas modifié les identificateurs standards utilisés dans la zone de notification.

Si vous souhaitez modifier l’aspect, vous devez redéfinir le `EngagementDefaultNotifier.prepareInAppArea` méthode. Il est recommandé à consulter la documentation technique et le code source de `EngagementNotifier` et `EngagementDefaultNotifier` si vous le souhaitez au niveau de personnalisation avancée.

##### <a name="system-notifications"></a>Notifications système

En étendant `EngagementDefaultNotifier`, vous pouvez ignorer `onNotificationPrepared` pour modifier la notification qui a été préparée par l’implémentation par défaut.

Par exemple :

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Cet exemple montre une notification système pour un contenu affiché sous la forme d’un événement en cours lors de la catégorie « en cours » est utilisée.

Si vous voulez créer le `Notification` objet à partir de zéro, vous pouvez revenir `false` à la méthode et appel `notify` vous-même sur le `NotificationManager`. Dans ce cas, il est important de conserver un `contentIntent`, un `deleteIntent` et l’identificateur de notification utilisé par `EngagementReachReceiver`.

Voici un exemple d’une telle implémentation correct :

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Annonces uniquement de notification

La gestion du clic sur une notification annonce uniquement peut être personnalisée en substituant `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` pour modifier la préparé `Intent`. À l’aide de cette méthode vous permet de régler facilement les indicateurs.

Par exemple pour ajouter la `SINGLE_TOP` indicateur :

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Pour les utilisateurs d’Engagement hérités, notez que les notifications système sans action URL maintenant lance l’application s’il s’agissait en arrière-plan, afin que cette méthode peut être appelée avec une annonce sans URL de l’action. Vous devez prendre en compte que lors de la personnalisation de l’objectif.

Vous pouvez également implémenter `EngagementNotifier.executeNotifAnnouncementAction` à partir de zéro.

##### <a name="notification-life-cycle"></a>Cycle de vie de notification

Lorsque vous utilisez la catégorie par défaut, certaines méthodes de cycle de vie sont appelées sur le `EngagementReachInteractiveContent` objet pour communiquer les statistiques et mettre à jour de l’état de campagne :

-   Lorsque la notification est affichée dans l’application ou placer dans la barre d’état, le `displayNotification` méthode est appelée (qui signale les statistiques) par `EngagementReachAgent` si `handleNotification` renvoie `true`.
-   Si la notification est fermée, la `exitNotification` méthode est appelée, statistique est signalée et campagnes suivantes peuvent désormais être traités.
-   Si vous cliquez sur la notification, `actionNotification` est appelé statistique est signalée et l’objectif associée est lancée.

Si votre implémentation de `EngagementNotifier` ignore le comportement par défaut, vous avez besoin d’appeler ces méthodes de cycle de vie par vous-même. Les exemples suivants illustrent certains cas où le comportement par défaut est ignoré :

-   Vous ne dépassent pas `EngagementDefaultNotifier`, par exemple, vous avez implémenté gestion de catégorie à partir de zéro.
-   Pour les notifications système, vous a substitué le `onNotificationPrepared` et que vous avez modifiée `contentIntent` ou `deleteIntent` dans les `Notification` objet.
-   Pour les notifications dans l’application, vous a substitué `prepareInAppArea`, veillez à mapper au moins `actionNotification` à un de vos U.I contrôles.

> [AZURE.NOTE] Si `handleNotification` lève une exception, le contenu est supprimée et `dropContent` est appelée. Ceci est indiquée dans les statistiques et campagnes suivantes peuvent désormais être traités.

### <a name="announcements-and-polls"></a>Annonces et sondages

#### <a name="layouts"></a>Mises en page

Vous pouvez modifier la `engagement_text_announcement.xml`, `engagement_web_announcement.xml` et `engagement_poll.xml` fichiers pour personnaliser les annonces de texte, les annonces web et les sondages.

Ces fichiers partagent deux dispositions courantes pour la zone de titre et la zone du bouton. La mise en page pour le titre est `engagement_content_title.xml` et utilise le fichier drawable éponyme pour l’arrière-plan. La mise en page pour les boutons d’action et de sortie est `engagement_button_bar.xml` et utilise le fichier drawable éponyme pour l’arrière-plan.

Dans un sondage, la mise en page de question et leurs choix est gonflement dynamiquement à l’aide de plusieurs fois la `engagement_question.xml` fichier mise en page pour les questions et le `engagement_choice.xml` fichier pour les choix.

#### <a name="categories"></a>Catégories

##### <a name="alternate-layouts"></a>Autres mises en page

Telles que les notifications, catégorie de la campagne peut être utilisée pour que les autres mises en page pour vos annonces et les sondages.

Par exemple, pour créer une catégorie pour une annonce de texte, vous pouvez étendre `EngagementTextAnnouncementActivity` et le référencer la `AndroidManifest.xml` fichier :

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Notez que la catégorie dans le filtre intention est utilisée pour faire la différence avec l’activité annonce par défaut.

Le Kit de développement atteigne utilise le système prévu pour résoudre l’activité droite pour une catégorie spécifique et il revient de la catégorie par défaut en cas d’échec de la résolution.

Vous devrez implémenter `MyCustomTextAnnouncementActivity`, si vous voulez simplement modifier la mise en page (tout en conservant les mêmes identificateurs de vue), il vous suffit de définir la classe comme dans l’exemple suivant :

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Pour remplacer la catégorie par défaut des annonces de texte, simplement la remplacer `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` par votre implémentation.

Sondages et les annonces de web peuvent être personnalisées de manière similaire.

Pour les annonces web que vous pouvez étendre `EngagementWebAnnouncementActivity` et déclarer votre activité dans les `AndroidManifest.xml` comme dans l’exemple suivant :

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Pour les sondages, vous pouvez étendre `EngagementPollActivity` et déclarer votre dans le `AndroidManifest.xml` comme dans l’exemple suivant :

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Mise en œuvre à partir de zéro

Vous pouvez implémenter des catégories pour vos activités annonce (et de sondage) sans étendre une de la `Engagement*Activity` classes fournies par le Kit de développement atteindre. Par exemple cela est utile si vous souhaitez définir une mise en page qui n’utilise pas les mêmes vues en tant que les mises en page standards.

Comme pour la personnalisation avancées de notification, il est recommandé d’examiner le code source de la mise en œuvre standard.

Voici quelques éléments à garder à l’esprit : portée lancera l’activité avec un objectif spécifique (correspondant à l’intention filtre) plus un paramètre supplémentaire qui est l’identificateur de contenu.

Pour récupérer l’objet de contenu qui contiennent les champs que vous avez spécifié lors de la création de la campagne marketing sur le site web que vous pouvez procédez comme suit :

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Pour les statistiques, vous devez signaler le contenu est affiché dans le `onResume` événement :

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Ensuite, n’oubliez pas d’appeler des options `actionContent(this)` ou `exitContent(this)` sur l’objet de contenu avant l’activité passe en arrière-plan.

Si vous n’appelez pas des options `actionContent` ou `exitContent`, statistiques ne seront pas envoyées (c'est-à-dire aucun analytique sur la campagne) et plus important, les campagnes suivantes ne seront pas avertis jusqu'à ce que le processus d’application est redémarré.

Orientation ou autres modifications de configuration peuvent rendre le code difficile à déterminer si l’activité passe en arrière-plan ou non, la mise en œuvre standard vérifie que le contenu est signalé comme étant quitté si l’utilisateur quitte l’activité (en appuyant sur `HOME` ou `BACK`) mais n’est pas si modifie l’orientation.

Voici la partie intéressante de l’implémentation :

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Comme vous pouvez le voir, si vous avez appelé `actionContent(this)` puis terminé l’activité `exitContent(this)` peut être appelé en toute sécurité sans avoir aucun effet.

[ici]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud de messagerie]:http://developer.android.com/guide/google/gcm/index.html
[La messagerie appareil Amazon]:https://developer.amazon.com/sdk/adm.html
