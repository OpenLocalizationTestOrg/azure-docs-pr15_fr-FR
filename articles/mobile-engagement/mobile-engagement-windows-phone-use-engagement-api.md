<properties 
    pageTitle="Comment utiliser l’Engagement API sur Windows Phone Silverlight" 
    description="Comment utiliser l’Engagement API sur Windows Phone Silverlight"    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" /> 

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Comment utiliser l’Engagement API sur Windows Phone Silverlight

Ce document est un composant additionnel pour le document [comment intégrer Engagement Mobile dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md). Il propose dans Détails de l’axe de profondeur sur l’utilisation de l’API Engagement pour signaler vos statistiques d’applications.

Si vous voulez uniquement Engagement pour signaler votre application sessions, les activités, pannes et des informations techniques, puis la façon la plus simple consiste à tous les votre `PhoneApplicationPage` sous-adresse classes héritent de la `EngagementPage` cours.

Si vous voulez faire plus d’informations, par exemple, si vous avez besoin signaler des événements spécifiques de l’application, les erreurs et les tâches, ou si vous avez signaler des activités de votre application d’une autre manière que celle implémentée dans les `EngagementPage` classes, vous devez utiliser l’API Engagement.

L’API Engagement est fourni par le `EngagementAgent` cours. Vous pouvez accéder à ces méthodes via `EngagementAgent.Instance`.

Même si le module de l’agent n’a pas été initialisé, chaque appel à l’API est différé et sera exécutée à nouveau lorsque l’agent est disponible.

##<a name="engagement-concepts"></a>Concepts d’engagement

Les composants suivants affiner les Concepts Engagement Mobile pour la plateforme Windows Phone.

### <a name="session-and-activity"></a>`Session`et`Activity`

Une *activité* est généralement associé à une page de l’application, c'est-à-dire l' *activité* démarre lorsque la page s’affiche et s’arrête lorsque la page est fermée : c’est le cas lorsque le Kit de développement Engagement est intégré à l’aide de la `EngagementPage` cours.

Mais *activités* peut également être contrôlée manuellement à l’aide de l’API Engagement. Cela permet de fractionner une page dans plusieurs composants sub pour obtenir plus d’informations sur l’utilisation de cette page (par exemple pour la fréquence à laquelle connus et combien de temps les boîtes de dialogue sont utilisés à l’intérieur de cette page).

##<a name="reporting-activities"></a>Activités de création de rapports

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

#### <a name="reference"></a>Référence

            void StartActivity(string name, Dictionary<object, object> extras = null)

Vous devez appeler `StartActivity()` chaque fois que les modifications d’activité utilisateur. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

> [AZURE.IMPORTANT] Le Kit de développement appeler automatiquement la méthode ActivitéFin lorsque l’application est fermée. Par conséquent, il est recommandé d’appeler la méthode StartActivity chaque fois que l’activité de la modification de l’utilisateur, puis sur jamais appeler la méthode ActivitéFin, étant donné que l’appel de cette méthode force la session active à terminer.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Utilisateur termine son activité actuelle.

#### <a name="reference"></a>Référence

            void EndActivity()

Vous devez appeler `EndActivity()` au moins une fois lorsque l’utilisateur termine à sa dernière activité. Cela vous informe le Kit de développement Engagement que l’utilisateur est actuellement inactif et arrive à expiration que la session utilisateur doivent être fermés une fois le délai d’expiration de session (si vous appelez `StartActivity()` avant l’expiration de session, la session est simplement suite).

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Tâches de création de rapports

### <a name="start-a-job"></a>Démarrage d’une tâche

#### <a name="reference"></a>Référence

            void StartJob(string name, Dictionary<object, object> extras = null)

Vous pouvez utiliser la tâche pour effectuer le suivi de certains tâches sur une période donnée.

#### <a name="example"></a>Exemple

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Mettre fin à une tâche

#### <a name="reference"></a>Référence

            void EndJob(string name)

Dès qu’une tâche suivie par une tâche est terminée, vous devez appeler la méthode EndJob pour cette tâche, en fournissant le nom de la tâche.

#### <a name="example"></a>Exemple

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Création de rapports d’événements

Il existe trois types d’événements :

-   Événements autonome
-   Événements de session
-   Événements de tâche

### <a name="standalone-events"></a>Événements autonome

#### <a name="reference"></a>Référence

            void SendEvent(string name, Dictionary<object, object> extras = null)

Événements autonome peuvent se produire en dehors du contexte d’une session.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Événements de session

#### <a name="reference"></a>Référence

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors de sa session.

#### <a name="example"></a>Exemple

**Sans données :**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Avec les données :**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Événements de tâche

#### <a name="reference"></a>Référence

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Événements de tâche sont généralement utilisés pour signaler les actions exécutées par un utilisateur lors d’un travail.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Signalement d’erreurs

Il existe trois types d’erreurs :

-   Erreurs autonome
-   Erreurs de session
-   Erreurs de travaux

### <a name="standalone-errors"></a>Erreurs autonome

#### <a name="reference"></a>Référence

            void SendError(string name, Dictionary<object, object> extras = null)

Contrairement aux erreurs de session, erreurs autonome peuvent se produire en dehors du contexte d’une session.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Erreurs de session

#### <a name="reference"></a>Référence

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Erreurs de session sont généralement utilisés pour signaler les erreurs ayant un impact sur l’utilisateur lors de sa session.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Erreurs de travaux

#### <a name="reference"></a>Référence

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Erreurs peuvent être associés à une tâche en cours d’exécution au lieu d’être liés à la session utilisateur en cours.

#### <a name="example"></a>Exemple

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Création de rapports se bloque

L’agent fournit deux méthodes de traiter se bloque.

### <a name="send-an-exception"></a>Envoyer une exception

#### <a name="reference"></a>Référence

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exemple

Vous pouvez envoyer une exception à tout moment en appelant :

            EngagementAgent.Instance.SendCrash(aCatchedException);

Vous pouvez également utiliser un paramètre facultatif pour mettre fin à la session d’engagement en même temps que l’envoi de l’incident. Pour ce faire, appelez :

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si vous effectuez cette opération, la session et les travaux sera fermé juste après l’envoi de l’incident.

### <a name="send-an-unhandled-exception"></a>Envoyer une exception non gérée

#### <a name="reference"></a>Référence

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement fournit également une méthode pour envoyer les exceptions non gérées. Ceci est particulièrement utile lorsqu’il est utilisé dans le Gestionnaire d’événements UnhandledException silverlight.

Cette méthode ne **toujours** mettre fin à la session d’engagement et les travaux après son appel.

#### <a name="example"></a>Exemple

Vous pouvez l’utiliser pour implémenter votre propre gestionnaire UnhandledException (surtout si vous avez désactivé la fonctionnalité d’Engagement de rapport d’incident automatique). Par exemple, dans le `Application_UnhandledException` méthode de le `App.xaml.cs` fichier :

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Référence

            void OnActivated(ActivatedEventArgs e)

Lorsque l’utilisateur navigue vers l’avant, en dehors d’une application, une fois que l’événement désactivé est élevé, le système d’exploitation tentera de placer l’application dans un état en sommeil. Ensuite, l’application est la désactivation. Dans ce processus une application est terminée, mais certaines données relatives à l’état de l’application et les pages individuelles dans l’application sont conservées.

Vous devez insérer `EngagementAgent.Instance.OnActivated(e)` dans les `Application_Activated` méthode à partir du fichier App.xaml.cs pour réinitialiser l’Agent Engagement lorsque l’application a été désactivé.

### <a name="example"></a>Exemple

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Id de périphérique

            String GetDeviceId()

Vous pouvez obtenir l’id du périphérique engagement en appelant cette méthode.

##<a name="extras-parameters"></a>Paramètres de suppléments

Données arbitraires peuvent être attachées à un événement, une erreur, une activité ou une tâche. Ces données peuvent être structurées à l’aide d’un dictionnaire. Clés et les valeurs peuvent être de n’importe quel type.

Données suppléments sont sérialisées, si vous souhaitez insérer votre propre type avec les Bonus vous aurez à ajouter un contrat pour ce type de données.

### <a name="example"></a>Exemple

Nous créer une nouvelle classe « Personne ».

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Ensuite, nous allons ajouter un `Person` instance à un supplémentaire.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Si vous insérez d’autres types d’objets, vérifiez que leur méthode ToString() est activée pour renvoyer une chaîne explicite.

### <a name="limits"></a>Limites

#### <a name="keys"></a>Touches

Chaque clé dans l’objet doit correspondre à l’expression suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les touches doivent commencer par au moins une lettre, suivie de lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="size"></a>Taille

Suppléments sont limitées à **1024** caractères par appel.

##<a name="reporting-application-information"></a>Informations sur l’Application de création de rapports

### <a name="reference"></a>Référence

            void SendAppInfo(Dictionary<object, object> appInfos)

Vous pouvez signaler manuellement le suivi d’informations (ou toute autre information spécifique application) à l’aide de la SendAppInfo() fonction.

Notez que ces informations peuvent être envoyés par incréments : resteront uniquement la dernière valeur pour une clé donnée pour un périphérique donné. Comme événement suppléments, utiliser un dictionnaire\<objet, objet\> pour joindre des informations.

### <a name="example"></a>Exemple

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Touches

Chaque clé dans l’objet doit correspondre à l’expression suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les touches doivent commencer par au moins une lettre, suivie de lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à **1024** caractères par appel.

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Journalisation
###<a name="enable-logging"></a>Activer la journalisation

Le Kit de développement peut être configuré pour générer les journaux de test dans la console IDE.
Ces fichiers journaux n’est pas activées par défaut. Pour personnaliser ce, mettre à jour la propriété `EngagementAgent.Instance.TestLogEnabled` à un de la valeur disponible à partir de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
