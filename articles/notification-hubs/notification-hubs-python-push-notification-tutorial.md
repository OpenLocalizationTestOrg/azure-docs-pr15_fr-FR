<properties 
    pageTitle="Comment utiliser Notification Hubs avec Python" 
    description="Découvrez comment utiliser Azure Notification Hubs à partir d’un principale Python." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>L’utilisation de références de Notification de Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
Vous pouvez accéder à toutes les fonctionnalités de Notification Hubs à partir d’un Java/PHP/Python/Ruby principale à l’aide de l’interface de Notification concentrateur reste comme décrit dans la rubrique MSDN [Notification Hubs reste API](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Ceci est un exemple d’implémentation de référence pour implémenter l’envoie notification dans Python et n’est pas le Kit de développement bénéficiant Notifications concentrateur Python.
>
> Cet exemple est écrit à l’aide de Python 3.4.

Dans cette rubrique, nous allons vous montrer comment :

* Créer un client reste pour les fonctionnalités de Notification Hubs dans Python.
* Envoyer des notifications à l’aide de l’interface Python aux API REST Notification concentrateur. 
* Obtenir une image de la demande HTTP reste/réponse fins débogage/scolaires. 

Vous pouvez suivre le [didacticiel mise en route Get](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) pour votre plateforme mobile de choix, l’implémentation de la partie principale dans Python.

> [AZURE.NOTE] L’étendue de l’échantillon est limité uniquement pour envoyer des notifications et il ne fait pas une gestion de l’enregistrement.

## <a name="client-interface"></a>Interface client
L’interface client principale peut fournir les mêmes méthodes sont disponibles dans le [Kit de développement .NET Notification Hubs](http://msdn.microsoft.com/library/jj933431.aspx). Cela vous permet de traduire directement les didacticiels et les exemples actuellement disponibles sur ce site et de contribution de la Communauté sur internet.

Vous pouvez trouver le code disponible dans l' [exemple d’emballage reste Python].

Par exemple, pour créer un client :

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
Pour envoyer une notification toast de Windows :
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>Mise en œuvre
Si vous n’avez pas encore, suivez notre [didacticiel mise en route Get] jusqu'à la dernière section où vous disposez pour mettre en œuvre la principale.

Vous trouverez tous les détails pour implémenter un emballage reste total sur [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Dans cette section nous décrira l’implémentation Python des principales étapes requises pour accéder aux points de terminaison REST Hubs de Notification et envoyer des notifications

1. Analyser la chaîne de connexion
2. Générer le jeton d’autorisation
3. Envoyer une notification à l’aide de l’API REST HTTP

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

Voici la classe principale implémentation du client, dont le constructeur analyse la chaîne de connexion :

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Créer du jeton de sécurité
Les détails de la création de jetons de sécurité sont disponibles [ici](http://msdn.microsoft.com/library/dn495627.aspx).
Les méthodes suivantes doivent être ajoutés à la classe **NotificationHub** pour créer le jeton en fonction de l’URI de la requête en cours et les informations d’identification extraites de la chaîne de connexion.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Envoyer une notification à l’aide de l’API REST HTTP
Tout d’abord, laisser utiliser définir une classe représentant une notification.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Ce cours est un conteneur pour un corps de la notification native ou un ensemble de propriétés dans le cas d’une notification de modèle, un ensemble d’en-têtes qui contient le format (plateforme native ou modèle) et des propriétés spécifiques à la plateforme (par exemple, les propriétés d’expiration de Apple et en-têtes WNS).

Reportez-vous à la [documentation de Notification Hubs reste API](http://msdn.microsoft.com/library/dn495827.aspx) et les formats des plateformes notification spécifique pour toutes les options disponibles.

Maintenant avec ce cours, nous pouvons écrire l’envoyer les méthodes de notification à l’intérieur de la classe **NotificationHub** .

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Les méthodes ci-dessus envoient une demande HTTP POST au point de terminaison /messages de votre concentrateur de notification, avec le corps correcte et les en-têtes d’envoyer la notification.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Activer la journalisation détaillée à l’aide de la propriété de débogage
L’activation de propriété de débogage lors de l’initialisation de la Notification concentrateur écrit les informations de journalisation détaillées sur la requête HTTP et vidage de réponse, ainsi que message de Notification détaillé envoyer résultat. Nous avons ajouté récemment cette propriété appelée [Notification Hubs TestSend propriété](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) qui renvoie des informations détaillées sur le résultat d’envoi de notification. Pour utiliser - initialisation à l’aide de ce qui suit :

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

La Notification concentrateur envoyer la demande HTTP URL obtient ajoutée avec une chaîne de requête « test » en conséquence. 

##<a name="complete-tutorial"></a>Utiliser le didacticiel
Vous pouvez maintenant terminer le didacticiel prise en main en envoyant la notification à partir d’un principale Python.

Initialisation votre client Notification Hubs (substitue le nom de chaîne et concentrateur de connexion comme indiqué dans le [didacticiel mise en route Get]) :

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Ajoutez ensuite le code envoyer selon votre plateforme mobile cible. Cet exemple ajoute également des méthodes de niveau supérieurs pour activer les notifications envoi en fonction de la plateforme, par exemple send_windows_notification pour windows. send_apple_notification (pour apple) etc.. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store et Windows Phone 8.1 (non Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 et 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire d’Amazon
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Exécuter votre code Python doit faire apparaître une notification qui apparaissent sur votre appareil cible.

## <a name="examples"></a>Exemples :

### <a name="enabling-debug-property"></a>L’activation de propriété de débogage
Lorsque vous activez le débogage indicateur lors de l’initialisation de la NotificationHub ne s’affiche demande HTTP détaillée et vidage de réponse ainsi que les NotificationOutcome à ce qui suit l’endroit où vous aider à comprendre les en-têtes HTTP sont passés dans la demande et réponse HTTP a été reçu à partir du Hub de Notification :    ![][1]

Vous verrez détaillées résultat concentrateur de Notification, par exemple 

- Lorsque le message est envoyé au Service de Notification Push. 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- S’il n’y a aucune cible trouvée pour les notifications push puis que vous êtes probablement en pour voir ce qui suit dans la réponse (ce qui indique qu’il n’existe aucun enregistrement trouvé pour effectuer la notification probablement parce que les enregistrements avaient certaines balises qui ne correspondent pas)

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Diffusion de notification toast pour Windows 

Notez les en-têtes obtenir envoyées lorsque vous envoyez une notification toast de diffusion au client Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Envoyer une notification indiquant un indicateur (ou une expression de l’étiquette)

Notez l’en-tête de balises HTTP qui est ajouté à la demande HTTP (dans l’exemple ci-dessous, nous allons envoi de la notification uniquement pour les enregistrements avec la charge utile « sport »)

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Envoyer une notification spécifiant plusieurs indicateurs

Notez comment l’en-tête HTTP balises change lorsque plusieurs indicateurs sont envoyées. 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Notification basé sur un modèle

Notez que les modifications de l’en-tête de Format HTTP et le corps de la charge utile sont envoyés dans le cadre du corps de la demande HTTP :

**Côté client - modèle enregistré**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Côté serveur - envoyer la charge utile**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>Étapes suivantes
Dans cette rubrique nous a montré comment créer un client reste Python simple pour Hubs de Notification. À partir de là, vous pouvez :

* Télécharger les complet d' [exemples d’emballage reste Python], qui contient le code ci-dessus.
* Actualisez régulièrement vos connaissances sur la Notification Hubs marquage fonctionnalité dans le [didacticiel transmettre actualités]
* Actualisez régulièrement vos connaissances sur la fonctionnalité de Notification Hubs modèles dans le [didacticiel de localisation d’actualités]

<!-- URLs -->
[Exemple d’emballage Python reste]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Didacticiel mise en route Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Annulation de didacticiel de News]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Localisation didacticiel de News]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
