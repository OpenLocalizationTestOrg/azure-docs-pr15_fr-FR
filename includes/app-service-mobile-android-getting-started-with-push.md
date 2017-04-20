1. Dans votre projet **application** , ouvrez le fichier `AndroidManifest.xml`. Dans le code dans les deux étapes suivantes, remplacez _`**my_app_package**`_ par le nom du package d’application pour votre projet, qui est la valeur de la `package` attribut de la `manifest` balise.

2. Ajouter les nouvelles autorisations suivantes après l’existant `uses-permission` élément :

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Ajoutez le code suivant après la `application` balise d’ouverture :

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Ouvrez le fichier *ToDoActivity.java*et ajoutez l’instruction import suivante :

        import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Ajoutez la variable privée suivante à la classe : remplacer _`<PROJECT_NUMBER>`_ avec le numéro de projet affecté par Google à votre application dans la procédure précédente :

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Modifier la définition de la *MobileServiceClient* de **privé** **statiques publics**, afin qu’il se présente désormais ainsi :

        public static MobileServiceClient mClient;

7. Ensuite, nous devons ajouter une nouvelle classe pour gérer les notifications. Dans l’Explorateur de projets, ouvrez le **src** => **principale** => nœuds**java** et clic droit le nœud de nom du package : cliquez sur **Nouveau**, puis cliquez sur **Classe Java**.

8. Dans **nom** , tapez `MyHandler`, puis cliquez sur **OK**.


    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. Dans le fichier MyHandler, remplacez la déclaration de classe avec

        public class MyHandler extends NotificationsHandler {


10. Ajoutez les instructions d’importation suivantes pour la `MyHandler` cours :

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;


11. Ajoutez ensuite à ce membre la `MyHandler` cours :

        public static final int NOTIFICATION_ID = 1;


12. Dans la `MyHandler` de classes, ajoutez le code suivant pour remplacer la méthode **onRegistered** , qui enregistre votre appareil avec le service mobile concentrateur de Notification.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            new AsyncTask<Void, Void, Void>() {

                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                        return null;
                    }
                    catch(Exception e) {
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }


13. Dans la `MyHandler` de classes, ajoutez le code suivant pour remplacer la méthode **onReceive** , ce qui entraîne la notification afficher correctement après réception.

        @Override
        public void onReceive(Context context, Bundle bundle) {
                String msg = bundle.getString("message");

                PendingIntent contentIntent = PendingIntent.getActivity(context,
                        0, // requestCode
                        new Intent(context, ToDoActivity.class),
                        0); // flags

                Notification notification = new NotificationCompat.Builder(context)
                        .setSmallIcon(R.drawable.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                        .setContentText(msg)
                        .setContentIntent(contentIntent)
                        .build();

                NotificationManager notificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);
                notificationManager.notify(NOTIFICATION_ID, notification);
        }


14. Dans le fichier TodoActivity.java, mettez à jour la méthode **onCreate** de la classe *ToDoActivity* pour enregistrer la classe du Gestionnaire de notification. Veillez à ajouter ce code après que le *MobileServiceClient* instanciation.


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Votre application est désormais mis à jour pour prendre en charge les notifications push.
