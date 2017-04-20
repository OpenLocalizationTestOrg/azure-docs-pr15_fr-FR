<properties
    pageTitle="L’utilisation de l’API Engagement sur Android"
    description="Dernière SDK Android - l’utilisation de l’API Engagement sur Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>L’utilisation de l’API Engagement sur Android

Ce document est un composant additionnel au document [options avancées création de rapports pour Android Kit de développement Mobile Engagement](mobile-engagement-android-advanced-reporting.md). Il propose dans Détails de l’axe de profondeur sur l’utilisation de l’API Engagement pour signaler vos statistiques d’applications.

N’oubliez pas que si vous voulez uniquement Engagement pour signaler votre application sessions, les activités, pannes et des informations techniques, puis le plus simple consiste à tous les votre `Activity` sous-adresse classes héritent de la correspondant `EngagementActivity` cours.

Si vous voulez faire plus d’informations, par exemple, si vous avez besoin signaler des événements spécifiques de l’application, les erreurs et les tâches, ou si vous avez signaler des activités de votre application d’une autre manière que celle implémentée dans les `EngagementActivity` classes, vous devez utiliser l’API Engagement.

L’API Engagement est fourni par le `EngagementAgent` cours. Une instance de ce cours peut être récupérée en appelant le `EngagementAgent.getInstance(Context)` méthode statique (Notez que la `EngagementAgent` objet retourné est un singleton).

##<a name="engagement-concepts"></a>Concepts d’engagement

Les composants suivants affiner les courantes [Concepts Engagement Mobile](mobile-engagement-concepts.md), de la plateforme Android.

### <a name="session-and-activity"></a>`Session`et`Activity`

Si l’utilisateur reste plus de quelques secondes inactif entre deux *activités*, sa séquence *d’activités* est fractionné en deux distinct *sessions*. Ces quelques secondes sont appelées « délai de session ».

Une *activité* est généralement associé à un écran de l’application, c'est-à-dire l' *activité* démarre lorsque l’écran s’affiche et s’arrête lorsque l’écran se ferme : c’est le cas lorsque le Kit de développement Engagement est intégré à l’aide de la `EngagementActivity` cours.

Mais *activités* peut également être contrôlée manuellement à l’aide de l’API Engagement. Cela permet de fractionner un écran donné dans plusieurs composants sub pour obtenir plus d’informations sur l’utilisation de l’écran (par exemple à la fréquence à laquelle connus et combien de temps les boîtes de dialogue sont utilisés à l’intérieur de cet écran).

##<a name="reporting-activities"></a>Activités de création de rapports

> [AZURE.IMPORTANT] Vous n’avez pas besoin au rapport activités telles que décrite dans cette section si vous utilisez la `EngagementActivity` cours et ses variantes comme indiqué dans la comment intégrer Engagement sur document Android.

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Vous devez appeler `startActivity()` chaque fois que les modifications d’activité utilisateur. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

L’endroit idéal pour appeler cette fonction se trouve sur chaque activité `onResume` rappel.

### <a name="user-ends-his-current-activity"></a>Utilisateur termine son activité actuelle.

            EngagementAgent.getInstance(this).endActivity();

Vous devez appeler `endActivity()` au moins une fois lorsque l’utilisateur termine à sa dernière activité. Cela vous informe le Kit de développement Engagement que l’utilisateur est actuellement inactif et arrive à expiration que la session utilisateur doivent être fermés une fois le délai d’expiration de session (si vous appelez `startActivity()` avant l’expiration de session, la session est simplement reprise).

L’endroit idéal pour appeler cette fonction se trouve sur chaque activité `onPause` rappel.

##<a name="reporting-events"></a>Création de rapports d’événements

### <a name="session-events"></a>Événements de session

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors de sa session.

**Exemple sans données supplémentaires :**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exemple avec des données supplémentaires :**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Événements autonome

Contrairement aux événements de session, événements autonome peuvent se produire en dehors du contexte d’une session.

**Exemple :**

Supposons que vous souhaitez rapport événements se produisant lorsqu’un récepteur de diffusion se déclenche :

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Signalement d’erreurs

### <a name="session-errors"></a>Erreurs de session

Erreurs de session sont généralement utilisés pour signaler les erreurs ayant un impact sur l’utilisateur lors de sa session.

**Exemple :**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Erreurs autonome

Contrairement aux erreurs de session, erreurs autonome peuvent se produire en dehors du contexte d’une session.

**Exemple :**

L’exemple suivant montre comment signaler une erreur chaque fois que la mémoire devient faible sur le téléphone pendant l’exécution d’un processus de votre application.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Tâches de création de rapports

### <a name="example"></a>Exemple

Supposons que vous voulez signaler la durée de votre processus de connexion :

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Signaler les erreurs lors d’un travail

Erreurs peuvent être associés à une tâche en cours d’exécution au lieu d’être liés à la session utilisateur en cours.

**Exemple :**

Supposons que vous souhaitez signaler un message d’erreur pendant que vous connecter processus :

[...] Connexion void publique (contexte contexte,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Création de rapports d’événements au cours d’une tâche

Événements peuvent être liés à une tâche en cours d’exécution au lieu d’être liés à la session utilisateur en cours.

**Exemple :**

Supposons que nous avons un réseau social et nous utilisons une tâche de rapport la durée totale pendant laquelle l’utilisateur est connecté au serveur. L’utilisateur peut rester en contact en arrière-plan même quand il utilise une autre application ou lorsque le téléphone de demi-lune, donc il n’existe aucune session.

L’utilisateur peut recevoir des messages à partir de ses amis, il s’agit d’un événement de travail.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Paramètres supplémentaires

Données arbitraires peuvent être attachées événements, les erreurs, les activités et les tâches.

Ces données peuvent être structurées, elle utilise pour les cours de Android faisceau (en réalité, elle fonctionne comme des paramètres supplémentaires dans les modes Android). Notez qu’un lot peut contenir les tableaux ou une autre offre groupée instances.

> [AZURE.IMPORTANT] Si vous stockez dans les paramètres parcelable ou sérialisables, vérifiez que leurs `toString()` méthode est implémentée pour renvoyer une chaîne lisible. Les classes sérialisables qui contiennent des champs non transitoires qui ne sont pas sérialisables fera blocage Android lorsque vous appelez`bundle.putSerializable("key",value);`

> [AZURE.WARNING] Tableaux incomplets dans Paramètres supplémentaires ne sont pas prises en charge, c'est-à-dire qu’il ne sérialisé sous forme de tableau. Vous devez les convertir en tableaux standard avant d’utiliser dans les paramètres supplémentaires.

### <a name="example"></a>Exemple

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Touches

Chaque clé dans la `Bundle` doit correspondre à l’expression suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les touches doivent commencer par au moins une lettre, suivie de lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="size"></a>Taille

Suppléments sont limitées à **1024** caractères par appel (une fois codé au format JSON par le service Engagement).

Dans l’exemple précédent, le JSON envoyé au serveur est 58 caractères :

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informations sur l’Application de création de rapports

Vous pouvez le signaler manuellement le suivi d’informations (ou toute autre information spécifique application) à l’aide de la `sendAppInfo()` fonction.

Notez que ces informations peuvent être envoyés par incréments : resteront uniquement la dernière valeur pour une clé donnée pour un périphérique donné.

Comme suppléments d’événement, la classe offre est utilisée pour extraire des informations sur l’application, notez que tableaux ou offres sous-adresse seront traités comme des chaînes plats (à l’aide de sérialisation JSON).

### <a name="example"></a>Exemple

Voici un exemple de code pour envoyer la date de naissance et sexe utilisateur :

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Touches

Chaque clé dans la `Bundle` doit correspondre à l’expression suivante :

`^[a-zA-Z][a-zA-Z_0-9]*`

Cela signifie que les touches doivent commencer par au moins une lettre, suivie de lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à **1024** caractères par appel (une fois codé au format JSON par le service Engagement).

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

            {"expiration":"2016-12-07","status":"premium"}
