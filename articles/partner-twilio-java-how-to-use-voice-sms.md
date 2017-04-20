<properties 
    pageTitle="Comment utiliser Twilio pour la voix et de SMS (Java) | Microsoft Azure" 
    description="Découvrez comment effectuer un appel téléphonique et envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrits en Java." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Comment utiliser Twilio pour la voix et de fonctionnalités SMS dans Java

Ce guide montre comment effectuer des tâches de programmation courantes avec le service Twilio API sur Azure. Les scénarios présentés incluent un appel téléphonique et l’envoi d’un message court Message SMS (Service). Pour plus d’informations sur Twilio et l’utilisation des fonctionnalités vocales et SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Quelles sont les Twilio ?
Twilio est une API de service web de la téléphonie qui vous permet d’utiliser vos compétences et langues web existante pour générer la voix et applications SMS. Twilio est un service tiers (pas une fonctionnalité Azure et non un produit Microsoft).

**Twilio vocale** permet à vos applications passer et recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications passer et recevoir des messages SMS. **Twilio Client** permet à vos applications activer la communication vocale à l’aide de connexions Internet existantes, y compris les connexions mobiles.

## <a id="Pricing"></a>Twilio tarifs et offres spéciales
Informations sur les tarifs Twilio sont disponibles au [Prix de Twilio] [twilio_pricing]. Clients Azure recevoir une [offre spéciale][special_offer]: un crédit gratuit de textes 1000 ou 1000 entrant minutes. Pour s’inscrire à cette offre ou obtenir plus d’informations, visitez le site [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concepts
L’API Twilio est une API RESTful qui fournit des fonctionnalités vocales et fonctionnalités SMS pour les applications. Les bibliothèques clientes sont disponibles dans plusieurs langues ; Pour obtenir une liste, voir [Bibliothèques API Twilio] [twilio_libraries].

Les aspects essentiels de l’API Twilio sont Twilio verbes et Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Verbes Twilio
L’API montre comment utiliser Twilio verbes ; par exemple, le ** &lt;et&gt; ** verbe indique à Twilio audible envoyer un message lors d’un appel. 

Voici une liste de verbes Twilio.

* ** &lt;Numérotation&gt;**: se connecte l’appelant vers un autre téléphone.
* ** &lt;Collecter&gt;**: collecte des chiffres entrés le pavé numérique du téléphone.
* ** &lt;Raccrocher&gt;**: met fin à un appel.
* ** &lt;Lire&gt;**: lit un fichier audio.
* ** &lt;Pause&gt;**: attend silencieusement pendant un nombre de secondes déterminé.
* ** &lt;Enregistrement&gt;**: enregistrements vocaux de l’appelant et retourne une URL d’un fichier qui contient l’enregistrement.
* ** &lt;Rediriger&gt;**: transfère le contrôle d’un appel ou SMS à la TwiML à une autre URL.
* ** &lt;Rejeter&gt;**: refuse un appel entrant à votre numéro de Twilio sans vous facturation
* ** &lt;Et&gt;**: convertit du texte en parole est effectué sur un appel.
* ** &lt;Sms&gt;**: envoie un message SMS.

### <a id="TwiML"></a>TwiML
TwiML est un jeu d’instructions basé sur XML basée sur les verbes Twilio qui informent Twilio de façon de traiter un appel ou SMS.

Par exemple, le TwiML suivant serait convertir le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l’API Twilio, un des paramètres de l’API est l’URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser URL Twilio communiquée à fournir les réponses TwiML utilisés par vos applications. Vous pouvez également héberger votre propre URL et produire les réponses TwiML et une autre option consiste à utiliser l’objet **TwiMLResponse** .

Pour plus d’informations sur Twilio verbes, leurs attributs et TwiML, voir [TwiML] [twiml]. Pour plus d’informations sur l’API Twilio, voir [API Twilio] [twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt à obtenir un compte Twilio, s’inscrire à [Essayer Twilio] [try_twilio]. Vous pouvez commencer avec un compte gratuit et mettre à niveau votre compte ultérieurement.

Lorsque vous vous inscrivez pour un compte Twilio, vous recevrez un ID de compte et un jeton d’authentification. Les deux seront nécessaires pour émettre des appels API Twilio. Pour empêcher tout accès non autorisé à votre compte, sécurisez votre jeton d’authentification. Votre ID de compte et l’authentification jeton peuvent être affichées dans la [page de compte Twilio] [twilio_account], dans les champs intitulée **identificateur de sécurité du compte** et **AUTH jeton**, respectivement.

## <a id="create_app"></a>Créer une Application Java
1. Obtenir le Twilio JAR et l’ajouter à votre chemin d’accès de la version Java et votre assembly de déploiement guerre. En [https://github.com/twilio/twilio-java][twilio_java], vous pouvez télécharger les sources GitHub et créer votre propre JAR ou télécharger un fichier JAR prédéfinie (avec ou sans dépendances).
2. Assurez-vous de clés de votre JDK **cacerts** contient le certificat de certification Equifax Secure avec MD5 empreinte 67:CB:9 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est 35:DE:F4:CF et l’empreinte numérique SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0 D : 7F:9 D : 62:13:97:86:63:3A). Il s’agit de l’autorité de certification de certification pour la [https://api.twilio.com] [ twilio_api_service] service, qui est appelé lorsque vous utilisez Twilio APIs. Pour plus d’informations sur la garantie de votre JDK **cacerts** combinaison de touches contient le certificat correct, voir [Ajout d’un certificat dans le magasin de certificats d’autorité de certification Java][add_ca_cert].

Obtenir des instructions détaillées pour l’utilisation de la bibliothèque de client Twilio Java sont disponibles sur [comment rendre un Twilio à l’aide d’un appel téléphonique dans une Application Java sur Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurez votre Application pour utiliser des bibliothèques Twilio
Au sein de votre code, vous pouvez ajouter des instructions **import** en haut de vos fichiers source pour les packages Twilio ou les classes que vous souhaitez utiliser dans votre application. 

Pour les fichiers source Java :

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Pour les fichiers source Java Server Page (JSP) :

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Selon les packages Twilio ou les classes que vous voulez utiliser, vos instructions **import** peuvent être différentes.

## <a id="howto_make_call"></a>Comment : effectuer un appel sortant
La figure suivante montre comment effectuer un appel sortant à l’aide de la classe **CallFactory** . Ce code utilise également un site Twilio fournis pour renvoyer la réponse Twilio Markup Language (TwiML). Remplacer les valeurs pour les numéros de téléphone **du** et **au** et vous assurer que vous vérifiez le numéro de téléphone **à partir** de votre compte Twilio avant d’exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Pour plus d’informations sur les paramètres passé à la méthode **CallFactory.create** , voir [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Comme indiqué, ce code utilise un site Twilio fournis pour retourner la réponse TwiML. Vous pouvez utiliser à la place de votre propre site pour fournir la réponse TwiML ; Pour plus d’informations, voir [comment fournir TwiML les réponses dans une Application Java sur Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Comment : envoyer un message SMS
La figure suivante montre comment envoyer un message SMS à l’aide de la classe **SmsFactory** . La zone **de** numéro, **4155992671**, est fourni par Twilio pour les comptes d’évaluation pour envoyer des messages SMS. **Le numéro** doit être vérifié pour votre compte Twilio avant d’exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Pour plus d’informations sur les paramètres passé à la méthode **SmsFactory.create** , voir [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Comment : fournir des réponses TwiML à partir de votre propre site Web
Lorsque votre application démarre un appel à l’API Twilio, par exemple via la méthode **CallFactory.create** , Twilio envoie votre demande à une URL est censée renvoyer une réponse TwiML. L’exemple ci-dessus utilise les URL fournies par Twilio [http://twimlets.com/message][twimlet_message_url]. (Si TwiML est conçu pour une utilisation par les services Web, vous pouvez afficher le TwiML dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message] [ twimlet_message_url] pour voir vide ** &lt;réponse&gt; ** élément ; Voici un autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] pour afficher un ** &lt;réponse&gt; ** élément qui contient un ** &lt;dites&gt; ** élément.)

Au lieu de l’URL Twilio condition, vous pouvez créer votre propre site URL qui renvoie les réponses HTTP. Vous pouvez créer le site dans n’importe quelle langue qui renvoie les réponses HTTP ; Cette rubrique suppose que vous pouvez héberger l’URL dans une page JSP.

Résultats de la page JSP suivante dans une réponse TwiML indiquant **Hello World** lors de l’appel.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

Résultats de la page JSP suivante dans une réponse TwiML qui indique que du texte, possède plusieurs pauses et indique les informations sur la version de l’API Twilio et le nom de rôle Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Le paramètre **ApiVersion** est disponible dans les requêtes de voix Twilio (pas les requêtes SMS). Pour afficher les paramètres de demande disponibles pour les demandes SMS et Twilio vocale, voir <https://www.twilio.com/docs/api/twiml/twilio_request> et <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivement. La variable d’environnement **RoleName** est disponible dans le cadre d’un déploiement d’Azure. (Si vous voulez ajouter des variables d’environnement personnalisés afin qu’ils peuvent être récupérés à partir de **System.getenv**, consultez la section de variables d’environnement à [Divers paramètres de Configuration de rôle][misc_role_config_settings].)

Une fois que vous avez votre page JSP configuré pour fournir des réponses TwiML, utilisez l’URL de la page JSP en tant que l’URL passé dans la méthode **CallFactory.create** . Par exemple, si vous avez une application Web nommée MyTwiML déployé sur un Azure hébergé service et le nom de la page JSP est mytwiml.jsp, l’URL peut être passée à **CallFactory.create** comme le montre l’exemple suivant :

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Une autre option pour répondre avec TwiML est via la classe **TwiMLResponse** , qui est disponible dans le package **com.twilio.sdk.verbs** .

Pour plus d’informations sur l’utilisation de Twilio dans Azure avec Java, voir [comment effectuer un Twilio à l’aide d’un appel téléphonique dans une Application Java sur Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Comment : utiliser les Services Twilio supplémentaires
Outre les exemples présentés ici, Twilio propose des API basées sur le web qui vous permet de tirer parti des fonctionnalités Twilio supplémentaires à partir de votre application Azure. Pour en savoir plus, voir la [documentation de l’API Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du service Twilio, suivez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio] [twilio_security_guidelines]
* [De Twilio HowTo et exemple de Code] [twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts] 
* [Twilio sur GitHub] [twilio_on_github]
* [Parler à Twilio prise en charge] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
