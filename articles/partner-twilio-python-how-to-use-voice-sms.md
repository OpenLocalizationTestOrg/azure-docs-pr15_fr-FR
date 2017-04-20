<properties
    pageTitle="Comment utiliser Twilio pour la voix et de SMS (PHP) | Microsoft Azure"
    description="Découvrez comment effectuer un appel téléphonique et envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrits en PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Comment utiliser Twilio pour la voix et de fonctionnalités SMS dans PHP
Ce guide montre comment effectuer des tâches de programmation courantes avec le service Twilio API sur Azure. Les scénarios présentés incluent un appel téléphonique et l’envoi d’un message court Message SMS (Service). Pour plus d’informations sur Twilio et l’utilisation des fonctionnalités vocales et SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Quelles sont les Twilio ?
Twilio est l’avenir de communications professionnelles, qui permet aux développeurs d’incorporer, les appels VoIP, messagerie vocale et dans les applications. Ils virtualisent de tous les infrastructure nécessaire dans un environnement global, sur le nuage, exposant via la plateforme de communications API Twilio. Applications sont simples à créer et format SVG. Profitez de flexibilité avec rémunération-comme-vous accédez tarifs et bénéficier de fiabilité de cloud.

**Twilio vocale** permet à vos applications passer et recevoir des appels téléphoniques. **Twilio SMS** permet à votre application envoyer et recevoir des messages texte. **Twilio Client** vous permet d’effectuer des appels VoIP à partir de n’importe quel téléphone, tablette ou un navigateur et prend en charge WebRTC.

## <a id="Pricing"></a>Twilio tarifs et offres spéciales

Clients Azure recevoir une [offre spéciale] 10 $ de Twilio crédit lorsque vous mettez à niveau votre compte Twilio. Ce Twilio crédit peuvent être appliqué à toutes les utilisations Twilio (crédit $10 équivalent à envoyer des SMS jusqu'à 1 000 ou de recevoir jusqu'à 1000 minutes vocale entrants, selon l’emplacement de votre destination d’appel ou un message et numéro de téléphone). Récupérer ce crédit Twilio et commencer à : [ahoy.twilio.com/azure].

Twilio est un service de paiement. Il existe sans frais de configuration, vous pouvez fermer votre compte à tout moment. Vous pouvez trouver plus d’informations sur [Twilio tarifs] [twilio_pricing].

## <a id="Concepts"></a>Concepts
L’API Twilio est une API RESTful qui fournit des fonctionnalités vocales et fonctionnalités SMS pour les applications. Les bibliothèques clientes sont disponibles dans plusieurs langues ; Pour obtenir une liste, voir [Bibliothèques API Twilio] [twilio_libraries].

Les aspects essentiels de l’API Twilio sont Twilio verbes et Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Verbes Twilio
L’API montre comment utiliser Twilio verbes ; par exemple, le ** &lt;et&gt; ** verbe indique à Twilio audible envoyer un message lors d’un appel.

Voici une liste de verbes Twilio. En savoir plus sur les autres verbes et les capacités via [documentation Twilio Markup Language] [http://www.twilio.com/docs/api/twiml].

* ** &lt;Numérotation&gt;**: se connecte l’appelant vers un autre téléphone.
* ** &lt;Collecter&gt;**: collecte des chiffres entrés le pavé numérique du téléphone.
* ** &lt;Raccrocher&gt;**: met fin à un appel.
* ** &lt;Lire&gt;**: lit un fichier audio.
* ** &lt;Pause&gt;**: attend silencieusement pendant un nombre de secondes déterminé.
* ** &lt;Enregistrement&gt;**: enregistre la voix de l’appelant et retourne une URL d’un fichier qui contient l’enregistrement.
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

Lorsque vous vous inscrivez pour un compte Twilio, vous recevez un ID de compte et un jeton d’authentification. Les deux seront nécessaires pour émettre des appels API Twilio. Pour empêcher tout accès non autorisé à votre compte, sécurisez votre jeton d’authentification. Votre ID de compte et l’authentification jeton peuvent être affichées dans la [page de compte Twilio] [twilio_account], dans les champs intitulée **identificateur de sécurité du compte** et **AUTH jeton**, respectivement.

## <a id="create_app"></a>Créer une Application PHP
Une application PHP qui utilise le service Twilio et s’exécute dans Azure n’est pas différente de toute autre application PHP qui utilise le service Twilio. Tandis que Twilio services sont basée sur REST et peuvent être appelés à partir de PHP de différentes manières, cet article sera centrée sur l’utilisation de services Twilio avec [Twilio bibliothèque pour PHP à partir de GitHub][twilio_php]. Pour plus d’informations sur l’utilisation de la bibliothèque Twilio pour PHP, voir [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Obtenir des instructions détaillées pour créer et déployer une application Twilio/PHP vers Azure sont disponibles à [comment rendre un Twilio à l’aide d’un appel téléphonique dans une Application PHP sur Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurez votre Application pour utiliser des bibliothèques Twilio
Vous pouvez configurer votre application pour utiliser la bibliothèque Twilio pour PHP de deux façons :

1. Télécharger la bibliothèque Twilio pour PHP à partir de GitHub ([https://github.com/twilio/twilio-php][twilio_php]) et ajoutez le répertoire des **Services** à votre application.

    -OU-

2. Installer la bibliothèque Twilio pour PHP sous forme de package POIRIERS. Il peut être installé avec les commandes suivantes :

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Une fois que vous avez installé la bibliothèque Twilio pour PHP, vous pouvez ensuite ajouter une instruction **require_once** en haut de vos fichiers PHP pour faire référence à la bibliothèque :

        require_once 'Services/Twilio.php';

Pour plus d’informations, voir [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Comment : effectuer un appel sortant
La figure suivante montre comment effectuer un appel sortant à l’aide de la classe **Services_Twilio** . Ce code utilise également un site Twilio fournis pour renvoyer la réponse Twilio Markup Language (TwiML). Remplacer les valeurs pour les numéros de téléphone **du** et **au** et vous assurer que vous vérifiez le numéro de téléphone **à partir** de votre compte Twilio avant d’exécuter le code.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Comme indiqué, ce code utilise un site Twilio fournis pour retourner la réponse TwiML. Vous pouvez utiliser à la place de votre propre site pour fournir la réponse TwiML ; Pour plus d’informations, voir [comment fournir les réponses TwiML à partir de votre propre Site Web](#howto_provide_twiml_responses).


- **Remarque**: pour résoudre les erreurs de validation de certificat SSL, voir [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Comment : envoyer un message SMS
La figure suivante montre comment envoyer un message SMS à l’aide de la classe **Services_Twilio** . Le nombre **** d’est fourni par Twilio pour les comptes d’évaluation pour envoyer des messages SMS. **Le numéro** doit être vérifié pour votre compte Twilio avant d’exécuter le code.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Comment : fournir des réponses TwiML à partir de votre propre site Web
Lorsque votre application démarre un appel à l’API Twilio, Twilio envoie votre demande à une URL est censée renvoyer une réponse TwiML. L’exemple ci-dessus utilise les URL fournies par Twilio [http://twimlets.com/message][twimlet_message_url]. (Si TwiML est conçu pour une utilisation par Twilio, vous pouvez afficher le service informatique dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message] [ twimlet_message_url] pour voir vide `<Response>` élément ; Voici un autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] pour afficher une `<Response>` élément qui contient une `<Say>` élément.)

Au lieu de l’URL Twilio condition, vous pouvez créer votre propre site renvoyant les réponses HTTP. Vous pouvez créer le site dans n’importe quelle langue qui renvoie les réponses XML ; Cette rubrique suppose que vous utiliserez PHP pour créer la TwiML.

La page suivante PHP se traduit par une réponse TwiML indiquant **Hello World** lors de l’appel.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Comme vous pouvez voir dans l’exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio pour PHP contient des classes qui généreront TwiML pour vous. L’exemple suivant génère la réponse équivalente comme indiqué ci-dessus, mais utilise le **Services\_Twilio\_Twiml** classe dans la bibliothèque Twilio pour PHP :

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Pour plus d’informations sur TwiML, voir [https://www.twilio.com/docs/api/twiml][twiml_reference].

Une fois que vous avez votre page PHP configuré pour fournir des réponses TwiML, utilisez l’URL de la page PHP comme l’URL passé dans les `Services_Twilio->account->calls->create` méthode. Par exemple, si vous avez une application Web nommée **MyTwiML** déployé sur un Azure hébergé service et le nom de la page PHP est **mytwiml.php**, l’URL peut être passée à **Services_Twilio -> compte -> appels -> créer** comme le montre l’exemple suivant :

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Pour plus d’informations sur l’utilisation de Twilio dans Azure avec PHP, voir [comment effectuer un Twilio à l’aide d’un appel téléphonique dans une Application PHP sur Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Comment : utiliser les Services Twilio supplémentaires
Outre les exemples présentés ici, Twilio propose des API basées sur le web qui vous permet de tirer parti des fonctionnalités Twilio supplémentaires à partir de votre application Azure. Pour en savoir plus, voir la [documentation de l’API Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du service Twilio, suivez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio] [twilio_security_guidelines]
* [Comment faire pour Twilio repères et exemple de Code] [twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts]
* [Twilio sur GitHub] [twilio_on_github]
* [Parler à Twilio prise en charge] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

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
