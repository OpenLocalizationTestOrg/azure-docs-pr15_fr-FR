<properties 
    pageTitle="Comment utiliser Twilio pour la voix et de SMS (Ruby) | Microsoft Azure" 
    description="Découvrez comment effectuer un appel téléphonique et envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrites en Ruby." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Comment utiliser Twilio pour la voix et de fonctionnalités SMS dans Ruby
Ce guide montre comment effectuer des tâches de programmation courantes avec le service Twilio API sur Azure. Les scénarios présentés incluent un appel téléphonique et l’envoi d’un message court Message SMS (Service). Pour plus d’informations sur Twilio et l’utilisation des fonctionnalités vocales et SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Quelles sont les Twilio ?
Twilio est une API de service web de la téléphonie qui vous permet d’utiliser vos compétences et langues web existante pour générer la voix et applications SMS. Twilio est un service tiers (pas une fonctionnalité Azure et non un produit Microsoft).

**Twilio vocale** permet à vos applications passer et recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications passer et recevoir des messages SMS. **Twilio Client** permet à vos applications activer la communication vocale à l’aide de connexions Internet existantes, y compris les connexions mobiles.

## <a id="Pricing"></a>Twilio tarifs et offres spéciales
Informations sur les tarifs Twilio sont disponibles au [Prix de Twilio] [twilio_pricing]. Clients Azure recevoir une [offre spéciale][special_offer]: un crédit gratuit de textes 1000 ou 1000 entrant minutes. Pour s’inscrire à cette offre ou obtenir plus d’informations, visitez le site [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concepts
L’API Twilio est une API RESTful qui fournit des fonctionnalités vocales et fonctionnalités SMS pour les applications. Les bibliothèques clientes sont disponibles dans plusieurs langues ; Pour obtenir une liste, voir [Bibliothèques API Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML est un jeu d’instructions basé sur XML qui informe Twilio de façon de traiter un appel ou SMS.

Par exemple, le TwiML suivant serait convertir le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tous les documents TwiML ont `<Response>` que leur élément racine. À partir de là, vous utilisez verbes Twilio pour définir le comportement de votre application.

### <a id="Verbs"></a>Verbes TwiML
Verbes Twilio sont des balises XML spécifiant de quelle Twilio qu’à **faire**. Par exemple, le ** &lt;et&gt; ** verbe indique à Twilio audible envoyer un message lors d’un appel. 

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

Pour plus d’informations sur Twilio verbes, leurs attributs et TwiML, voir [TwiML] [twiml]. Pour plus d’informations sur l’API Twilio, voir [API Twilio] [twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt à obtenir un compte Twilio, s’inscrire à [Essayer Twilio] [try_twilio]. Vous pouvez commencer avec un compte gratuit et mettre à niveau votre compte ultérieurement.

Lorsque vous vous inscrivez pour un compte Twilio, vous recevrez un numéro de téléphone gratuit pour votre application. Vous recevrez également un identificateur de sécurité du compte et un jeton d’authentification. Les deux seront nécessaires pour émettre des appels API Twilio. Pour empêcher tout accès non autorisé à votre compte, sécurisez votre jeton d’authentification. Votre compte identificateur de sécurité et auth jeton peuvent être affichées dans la [page de compte Twilio][twilio_account], dans les champs intitulée **identificateur de sécurité du compte** et **AUTH jeton**, respectivement.

### <a id="VerifyPhoneNumbers"></a>Vérifier les numéros de téléphone
Outre le nombre vous soyez fournis par Twilio, vous pouvez également vérifier les numéros de contrôle (c'est-à-dire que votre téléphone portable ou accueil numéro de téléphone) à utiliser dans vos applications. 

Pour plus d’informations sur la vérification d’un numéro de téléphone, voir [Gérer les nombres] [verify_phone].

## <a id="create_app"></a>Créer une Application prononciation
Une application prononciation qui utilise le service Twilio et s’exécute dans Azure n’est pas différente de toute autre application prononciation qui utilise le service Twilio. Tandis que Twilio services sont RESTful et peuvent être appelés depuis Ruby de différentes manières, cet article sera centrée sur l’utilisation de services Twilio avec [une bibliothèque d’assistance de Twilio pour Ruby][twilio_ruby].

Tout d’abord, [configurer un nouvel ordinateur virtuel Azure Linux] [ azure_vm_setup] pour l’utiliser comme un hôte pour votre nouvelle application web prononciation. Ignorer les étapes impliquant la création d’une application Rails, configuration simplement la machine virtuelle. Vérifiez que vous créez un point de terminaison avec un port externe 80 et un port interne de 5 000.

Dans les exemples ci-dessous, nous allons utiliser [Sinatra][sinatra], un cadre web très simple pour Ruby. Mais vous pouvez certainement utiliser la bibliothèque d’assistance Twilio pour Ruby avec n’importe quelle autre infrastructure web, y compris Ruby on Rails.

SSH dans votre nouvelle machine virtuelle et créez un répertoire pour votre nouvelle application. Dans ce répertoire, créez un fichier nommé Gemfile et copiez le code suivant :

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Sur la ligne de commande exécuter `bundle install`. Cette opération installe les dépendances ci-dessus. Ensuite créer un fichier nommé `web.rb`. Il s’agit de l’endroit où se trouve le code de votre application web. Collez le code suivant dedans :

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

À ce stade vous devriez pouvoir exécuter la commande `ruby web.rb -p 5000`. Toupie à un serveur web petite sur le port 5000 sont haut. Vous devez être en mesure de naviguer de cette application dans votre navigateur, visitez l’URL vous configuration correcte pour votre machine virtuelle Azure. Une fois que vous pouvez contacter votre application web dans le navigateur, vous êtes prêt à commencer à créer une application Twilio.

## <a id="configure_app"></a>Configurez votre Application pour utiliser Twilio
Vous pouvez configurer votre application web pour utiliser la bibliothèque Twilio en mettant à jour votre `Gemfile` pour inclure cette ligne :

    gem 'twilio-ruby'

Sur la ligne de commande, exécutez `bundle install`. Ouvrez maintenant `web.rb` et incluant cette ligne dans la partie supérieure :

    require 'twilio-ruby'

Vous êtes maintenant prêt à utiliser la bibliothèque d’assistance Twilio pour Ruby dans votre application web.

## <a id="howto_make_call"></a>Comment : effectuer un appel sortant
La figure suivante montre comment effectuer un appel sortant. Concepts clés incluent à l’aide de la bibliothèque d’assistance Twilio pour Ruby pour émettre des appels API REST et un rendu TwiML. Remplacer les valeurs pour les numéros de téléphone **du** et **au** et vous assurer que vous vérifiez le numéro de téléphone **à partir** de votre compte Twilio avant d’exécuter le code.

Ajoutez cette fonction à `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Si vous à distance ouvrir `http://yourdomain.cloudapp.net/make_call` dans un navigateur, qui se déclenche l’appel à l’API Twilio pour passer l’appel téléphonique. Les deux premiers paramètres dans `client.account.calls.create` sont relativement explicites : le numéro de l’appel est `from` et le numéro de l’appel est `to`. 

Le troisième paramètre (`url`) est l’URL que les demandes de Twilio pour obtenir des instructions sur la procédure à suivre une fois que l’appel est connecté. Dans ce cas nous configuration correcte une URL (`http://yourdomain.cloudapp.net`) qui retourne un document TwiML simple et utilise le `<Say>` verbe à suivre quelques synthèse vocale et dites « Hello singe » pour la personne qui reçoit l’appel.

## <a id="howto_recieve_sms"></a>Comment : recevoir un message SMS
Dans l’exemple précédent, nous avons lancé un appel téléphonique **sortant** . Cette fois, nous allons utiliser le numéro de téléphone Twilio nous a offert pendant l’inscription de traiter un message SMS **entrant** .

Tout d’abord, reconnectez-vous à votre [tableau de bord Twilio][twilio_account]. Cliquez sur « Numéros » dans le volet de navigation supérieure, puis cliquez sur le numéro de Twilio que vous ont été fournis. Vous verrez deux URL que vous pouvez configurer. URL de la requête une URL de demande de voix et un SMS. Voici les URL Twilio appelle chaque fois qu’un appel téléphonique est effectué ou un SMS est envoyé à votre numéro. Les URL sont également connu sous le nom « crochets web ».

Nous souhaitons traiter les messages SMS entrants, nous pouvons donc mettre à jour l’URL à `http://yourdomain.cloudapp.net/sms_url`. Continuez et cliquez sur Enregistrer les modifications dans la partie inférieure de la page. À présent, consignez `web.rb` nous allons programmer notre application gérer cette situation :

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Après avoir apporté cette modification, assurez-vous de redémarrer votre application web. À présent, sortir votre téléphone et envoyer un SMS à votre numéro de Twilio. Vous devez obtenir rapidement une réponse SMS indiquant « Hey, Merci de la commande ping ! Rock Twilio et Azure ! ».

## <a id="additional_services"></a>Comment : utiliser les Services Twilio supplémentaires
Outre les exemples présentés ici, Twilio propose des API basées sur le web qui vous permet de tirer parti des fonctionnalités Twilio supplémentaires à partir de votre application Azure. Pour en savoir plus, voir la [documentation de l’API Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du service Twilio, suivez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio] [twilio_security_guidelines]
* [Twilio HowTos et exemple de Code] [twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts] 
* [Twilio sur GitHub] [twilio_on_github]
* [Parler à Twilio prise en charge] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
