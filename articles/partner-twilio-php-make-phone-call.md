<properties
    pageTitle="Comment effectuer un appel téléphonique à partir de Twilio (PHP) | Microsoft Azure"
    description="Découvrez comment effectuer un appel téléphonique et envoyer un message SMS avec le service Twilio API sur Azure. Exemples sont appliquent aux application PHP."
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Comment effectuer un appel téléphonique à l’aide de Twilio dans une Application PHP sur Azure

L’exemple suivant vous montre comment vous pouvez utiliser Twilio pour passer un appel à partir d’une page web PHP hébergé dans Azure. L’application qui en résulte invitera l’utilisateur pour les valeurs d’un appel téléphonique, comme le montre la capture d’écran suivante.

![Formulaire appel Azure à l’aide de Twilio et PHP][twilio_php]

Vous devez procéder comme suit pour utiliser le code dans cette rubrique :

1. Obtenir un compte Twilio et l’authentification jeton. Pour commencer à utiliser Twilio, évaluer les tarifs en [http://www.twilio.com/pricing][twilio_pricing]. Vous pouvez vous inscrire à un compte d’évaluation en [https://www.twilio.com/try-twilio][try_twilio]. Pour plus d’informations sur l’API fournie par Twilio, consultez [http://www.twilio.com/api][twilio_api].
2. Obtenir la [bibliothèque Twilio pour PHP](https://github.com/twilio/twilio-php) ou installer un package POIRIERS. Pour plus d’informations, voir le [fichier Lisez-moi](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installez le Kit de développement Azure pour PHP. Pour une vue d’ensemble du Kit de développement logiciel et des instructions sur son installation, voir [configurer le Kit de développement Azure pour PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Créer un formulaire web pour effectuer un appel

Le code HTML suivant montre comment créer une page web (**callform.html**) qui Récupère des données utilisateur d’un appel :

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Créer le code pour passer l’appel
Le code suivant montre comment créer une page web (**makecall.php**) qui est appelé lorsque l’utilisateur envoie le formulaire affiché par **callform.html**. Le code ci-dessous crée le message d’appel et génère l’appel. (Utilisez votre compte Twilio et l’authentification des jetons au lieu des valeurs d’espace réservé affectés à **$sid** et **$token** dans le code ci-dessous).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Outre l’appel, **makecall.php** affiche des métadonnées d’appel (exemple illustré ci-dessous). Pour plus d’informations sur les métadonnées de l’appel, voir [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Réponse appel Azure à l’aide de Twilio et PHP][twilio_php_response]

## <a name="run-the-application"></a>Exécuter l’application
L’étape suivante consiste à déployer votre application aux sites Web Azure. Les articles suivants contiennent les informations pour créer un site Web et déployer votre code avec Git, FTP ou WebMatrix (même si toutes les informations de chaque article sont pertinentes) :

* [Créer un Site Web de Azure PHP MySQL et déployer à l’aide de Git][website-git]
* [Créer un Site Web Azure PHP MySQL et déployer à l’aide de FTP][website-ftp]

## <a name="next-steps"></a>Étapes suivantes
Ce code a été fourni pour vous montrer les fonctionnalités de base à l’aide de Twilio dans PHP sur Azure. Avant de déployer sur Azure en production, vous souhaiterez peut-être ajouter plus de gestion des erreurs ou d’autres fonctionnalités. Par exemple :

* Au lieu d’utiliser un formulaire web, vous pouvez utiliser des objets BLOB Azure stockage ou base de données SQL pour stocker les numéros de téléphone et texte l’appel. Pour plus d’informations sur l’utilisation d’objets BLOB Azure stockage dans PHP, voir [Utilisation du stockage Azure avec les Applications PHP][howto_blob_storage_php]. Pour plus d’informations sur l’utilisation de la base de données SQL dans PHP, reportez-vous [à l’aide de la base de données SQL avec les Applications PHP][howto_sql_azure_php].
* Le code **makecall.php** utilise URL fourni par Twilio ([http://twimlets.com/message][twimlet_message_url]) pour fournir une réponse Twilio Markup Language (TwiML) qui vous informe Twilio comment procéder à l’appel. Par exemple, le TwiML est renvoyé peut contenir un `<Say>` verbe qui résulte en texte prononcer au destinataire de l’appel. Au lieu d’utiliser l’URL Twilio condition, vous pouvez créer votre propre service pour répondre à la demande de Twilio ; Pour plus d’informations, voir [comment utiliser Twilio pour la voix et de fonctionnalités SMS dans PHP][howto_twilio_voice_sms_php]. Vous trouverez plus d’informations sur TwiML à [http://www.twilio.com/docs/api/twiml][twiml]et plus d’informations sur `<Say>` et autres verbes Twilio trouverez [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lisez les instructions de sécurité Twilio sur [https://www.twilio.com/docs/security][twilio_docs_security].

Pour plus d’informations sur Twilio, voir [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Voir aussi
* [Comment utiliser Twilio pour la voix et de fonctionnalités SMS dans PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
