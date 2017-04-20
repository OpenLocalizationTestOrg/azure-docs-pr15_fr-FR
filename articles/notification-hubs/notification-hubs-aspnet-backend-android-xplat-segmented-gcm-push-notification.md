<properties
    pageTitle="Hubs notification dernières actualités Tutorial - Android"
    description="Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer la rupture news notifications aux appareils Android."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs permet d’envoyer des informations de dernière minute

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment utiliser Azure Notification Hubs pour diffuser la rupture des notifications de news pour une application Android. Lorsque vous avez terminé, vous pourrez inscrire à la rupture des catégories d’actualités que qui vous intéresse et recevoir des notifications de transmission uniquement pour les catégories. Ce scénario est un modèle commun pour les nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs qui ont précédemment déclarés intérêt qu’ils, par exemple, lecteur RSS, applications pour les passionnés de musique, etc..

Scénarios de diffusion sont activés en incluant une ou plusieurs _indicateurs_ lorsque vous créez un enregistrement dans le hub de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont enregistrés pour la balise reçoivent une notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à mettre en service à l’avance. Pour plus d’informations sur les balises, reportez-vous à [Notification Hubs routage et Expressions de balise](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans [prise en main avec Hubs de Notification][get-started]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [prise en main avec Hubs de Notification][get-started].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection d’une catégorie à l’application

La première étape consiste à ajouter les éléments d’interface à votre activité principale existante qui permettent à l’utilisateur de sélectionner les catégories d’enregistrer. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, l’inscription d’une appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

1. Ouvrez votre fichier res/layout/activity_main.xml et remplacez le contenu avec les éléments suivants :

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Ouvrez votre fichier res/values/strings.xml et ajoutez les lignes suivantes :

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    Votre disposition de graphique main_activity.xml doit maintenant ressembler à ceci :

    ![][A1]

3. Créez maintenant une classe **Notifications** dans le même package que votre cours **MainActivity** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Ce cours utilise le stockage local pour stocker les catégories de news cet appareil doit s’afficher. Elle contient également des méthodes pour vous inscrire à ces catégories.


4. Dans votre cours **MainActivity** supprimer vos champs privés pour **NotificationHub** et **GoogleCloudMessaging**, et ajouter un champ pour les **Notifications**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. Puis, dans la méthode **onCreate** , supprimez l’initialisation du champ **concentrateur** et la méthode **registerWithNotificationHubs** . Puis ajoutez les lignes suivantes qui initialisation d’une instance de la classe **les Notifications** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`et `HubListenConnectionString` doit déjà être définie avec la `<hub name>` et `<connection string with listen access>` espaces réservés avec votre nom de concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment.

    > [AZURE.NOTE] Informations d’identification sont fournis avec une application client ne sont pas généralement sécurisées, distribuez uniquement la clé pour l’accès écouter avec votre application client. Écoutez access Active votre application pour vous inscrire à notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La touche d’accès complet est utilisée dans un service en aval sécurisée pour envoyer des notifications et modifier les enregistrements existants.


6. Ensuite, ajoutez les importations ci-après et `subscribe` méthode pour gérer le bouton s’abonner cliquez sur l’événement :
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Cette méthode crée une liste des catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et enregistrer les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

Votre application est désormais stocker un ensemble de catégories dans le stockage local sur l’appareil et enregistrer avec le hub de notification chaque fois que l’utilisateur change la sélection de catégories.

##<a name="register-for-notifications"></a>S’inscrire pour les notifications

Ces étapes vous inscrire auprès du concentrateur de notification sur démarrage à l’aide de catégories qui ont été stockées dans le stockage local.

> [AZURE.NOTE] Étant donné que les attributs de registrationId affectée par Google Cloud messagerie (GCM) peut modifier à tout moment, vous devez enregistrer pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit à notification chaque fois que l’application démarre. Pour les applications qui sont exécutent fréquemment, plusieurs fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.


1. Ajoutez le code suivant à la fin de la méthode **onCreate** dans la classe **MainActivity** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Cela permet de garantir que chaque fois que l’application démarre, il récupère les catégories du stockage local et demande une registeration pour ces catégories. 

2. Mettez à jour la `onStart()` méthode de le `MainActivity` classe comme suit :

    @Overrideprotégé void onStart() {super.onStart() ;      isVisible = vrai ;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Cela met à jour l’activité principale basée sur l’état des catégories enregistrées précédemment.

L’application est terminée et peut stocker un ensemble de catégories dans le stockage local appareil utilisé pour inscrire dans le hub de notification chaque fois que l’utilisateur change la sélection de catégories. Ensuite, nous allons définir un serveur principal qui peut envoyer des notifications de catégorie pour cette application.

##<a name="sending-tagged-notifications"></a>Envoi des notifications balises

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Exécutez l’application et générer des notifications

1. Dans Studio Android, créer l’application et démarrer sur un appareil ou un émulateur.

    Notez que l’interface utilisateur de l’application propose un ensemble de bascule qui vous permet de choisir les catégories pour vous abonner à.

2. Activer Active ou désactive une ou plusieurs catégories, puis cliquez sur **s’abonner**.

    L’application convertit les catégories sélectionnées en balises et les demandes d’un nouvel enregistrement d’appareil pour les marqueurs sélectionnés à partir du hub de notification. Les catégories inscrits sont retournés et affichés dans une notification toast.

4. Envoyer une notification de nouveau en exécutant l’application Console .NET.  Par ailleurs, vous pouvez envoyer des notifications de modèle balisé à l’aide de l’onglet déboguer de votre concentrateur de notification dans le [Portail classique Azure].

    Notifications pour les catégories sélectionnées s’affichent sous forme de notifications toast.

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser des informations de dernière minute par catégorie. Tenez compte des effectuant l’une des didacticiels suivants qui mettent en surbrillance autres scénarios Hubs Notification avancés :

+ [Permet de diffuser des informations de dernière minute localisés Hubs de Notification]

    Apprenez à développer l’application de news rupture pour activer les notifications localisées envoi.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Permet de diffuser des informations de dernière minute localisés Hubs de Notification]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Portail classique Azure]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
