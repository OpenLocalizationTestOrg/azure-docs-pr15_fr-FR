<properties 
    pageTitle="Comment utiliser le service de messagerie SendGrid (.NET) | Microsoft Azure" 
    description="Découvrez comment envoyer des messages électroniques avec le service de messagerie SendGrid sur Azure. Écrit en c# des exemples de code et utiliser l’API .NET." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Comment envoyer des messages électroniques à l’aide de SendGrid avec Azure


## <a name="overview"></a>Vue d’ensemble

Ce guide montre comment effectuer des tâches de programmation courantes avec le service de messagerie SendGrid sur Azure. Les exemples sont écrits en C\#
et utiliser l’API .NET. Les scénarios présentés incluent **construction de courrier électronique**, **envoi de messages électroniques**, **Ajouter des pièces jointes**et **à l’aide de filtres**. Pour plus d’informations sur les SendGrid et envoi de courrier électronique, voir la section [étapes suivantes][] .

## <a name="what-is-the-sendgrid-email-service"></a>Qu’est le service de messagerie SendGrid ?

SendGrid est un [service de messagerie sur le nuage] qui fournit une [remise de courrier électronique transactions]fiable et extensibilité élevées analytique en temps réel ainsi que des API flexible qui facilitent l’intégration personnalisée. Scénarios d’utilisation SendGrid courants sont les suivantes :

-   Envoyer automatiquement des confirmations aux clients.
-   Administration de distribution répertorie pour l’envoi de clients e-prospectus mensuels et des offres spéciales.
-   Collecte des mesures en temps réel pour des éléments tels que le courrier électronique bloqué et réactivité pour les clients.
-   Génération de rapports pour aider à identifier les tendances.
-   Recherches dans les clients le transfert.
-   Traitement des messages électroniques entrants.

Pour plus d’informations, voir [https://sendgrid.com](https://sendgrid.com) ou notre [bibliothèque c#][sendgrid csharp]

## <a name="create-a-sendgrid-account"></a>Créer un compte SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Faire référence à la bibliothèque de classes .NET SendGrid

Le [package SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) est le moyen le plus simple pour obtenir de l’API SendGrid et configurez votre application avec toutes les dépendances. Serait une extension Visual Studio incluse avec Microsoft Visual Studio 2015 qui facilite l’installation et mettre à jour des bibliothèques et des outils. 

> [AZURE.NOTE] Pour installer NuGet si vous exécutez une version antérieure à Visual Studio 2015 de Visual Studio, visitez [http://www.nuget.org](http://www.nuget.org), puis cliquez sur le bouton **Installer NuGet** .

Pour installer le package SendGrid NuGet dans votre application, procédez comme suit :

1.  Créer un nouveau projet.

    ![Créer un projet][create-new-project]

2.  Sélectionnez un modèle.

    ![Sélectionner un modèle][select-a-template]

3.  Dans l' **Explorateur de solutions**, cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.

4.  Recherchez **SendGrid** et sélectionnez l’élément **SendGrid** dans la liste des résultats.

    ![Package SendGrid NuGet][SendGrid-NuGet-package]

5.  Cliquez sur **installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

Bibliothèque de classes .NET de SendGrid est appelée **SendGridMail**. Il contient des espaces de noms suivants :

-   **SendGridMail** pour créer et utiliser des éléments de courrier électronique.
-   **SendGridMail.Transport** pour l’envoi de messages électroniques à l’aide du protocole **SMTP** , ou le protocole HTTP 1.1 avec **Web/reste**.

Ajouter les déclarations d’espace de noms de code suivantes à la partie supérieure de tout C\# fichier dans lequel vous souhaitez accéder par programme le service de messagerie SendGrid.
**System.Net** et **System.Net.Mail** sont des espaces de noms .NET Framework qui sont incluses car ils comprennent types que vous utiliserez fréquemment avec les SendGrid APIs.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Comment : créer un message électronique

L’objet **SendGridMessage** permet de créer un message électronique. Une fois que l’objet du message est créé, vous pouvez définir les propriétés et les méthodes, y compris l’expéditeur de courrier électronique, le destinataire de courrier électronique et l’objet et le corps du message.

L’exemple suivant montre comment créer un objet du message électronique entièrement remplie :

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Pour plus d’informations sur toutes les propriétés et les méthodes pris en charge par le type **SendGrid** , voir [sendgrid csharp][] sur GitHub.

## <a name="how-to-send-an-email"></a>Comment : envoyer un message électronique

Après avoir créé un message électronique, vous pouvez l’envoyer à l’aide de l’API Web fournie par SendGrid. Vous pouvez également utiliser [. NET intégré bibliothèque](https://sendgrid.com/docs/Code_Examples/csharp.html).

Envoi de messages électroniques exige que vous fournissiez vos informations d’identification du compte SendGrid (nom d’utilisateur et mot de passe) ou la clé d’API SendGrid. Clé d’API est la méthode préférée. Si vous devez savoir comment configurer les clés de l’API, visitez notre [documentation](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Vous pouvez stocker ces informations d’identification via votre portail Azure en cliquant sur Configurer et en ajoutant les paires clé/valeur sous « paramètres de l’application ».

 ![Paramètres de l’application Azure][azure_app_settings]

 Puis, vous pouvez y accéder comme suit : 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

À l’aide des informations d’identification :
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

À l’aide de l’API clé :

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Les exemples ci-dessous illustrent comment envoyer un message à l’aide de l’API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Comment : ajouter une pièce jointe

Les pièces jointes peuvent être ajoutés à un message en appelant la méthode **AddAttachment** et en spécifiant le nom et le chemin d’accès du fichier que vous souhaitez joindre.
Vous pouvez inclure plusieurs pièces jointes en appelant cette méthode une fois pour chaque fichier que vous souhaitez joindre. L’exemple suivant illustre l’ajout d’une pièce jointe à un message :

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Vous pouvez également ajouter des pièces jointes à partir de données **flux**. Il peut être traité en appelant la même méthode que ci-dessus, **AddAttachment**, mais en passant dans le flux de données et le nom de fichier que vous voulez qu’il s’affiche comme dans le message. Dans ce cas, vous devrez ajouter la bibliothèque System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Comment : utiliser des applications pour activer des pieds de page, de suivi et analytique

SendGrid fournit les fonctionnalités de messagerie supplémentaires à l’aide d’applications. Il s’agit des paramètres qui peuvent être ajoutés à un message électronique à activer des fonctionnalités spécifiques telles que cliquez sur suivi, Google analytique et abonnement suivi, et ainsi de suite. Pour une liste complète des applications, voir [Paramètres de l’application][].

Applications peuvent être appliquées aux messages électroniques **SendGrid** à l’aide des méthodes implémentées dans le cadre de la classe **SendGrid** .

Les exemples suivants illustrent le pied de page et cliquez sur suivi des filtres :

### <a name="footer"></a>Pied de page

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Cliquez sur suivi

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Comment : utiliser des services supplémentaires SendGrid

SendGrid propose des API et webhooks qui vous permet de tirer parti des fonctionnalités SendGrid supplémentaires à partir de votre application Azure celles basées sur le web. Pour plus d’informations, consultez la [documentation de l’API SendGrid][].

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base du service de messagerie SendGrid, suivez ces liens pour en savoir plus.

*   SendGrid C\# mis en pension bibliothèque : [sendgrid csharp][]
*   Documentation de l’API SendGrid : <https://sendgrid.com/docs>
*   SendGrid offre spéciale pour les clients Azure : [https://sendgrid.com](https://sendgrid.com)

  [Étapes suivantes]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Paramètres de l’application]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentation de l’API SendGrid]: https://sendgrid.com/docs
  
  [service de messagerie sur le nuage]: https://sendgrid.com/email-solutions
  [remise de courrier électronique transactions]: https://sendgrid.com/transactional-email
 
