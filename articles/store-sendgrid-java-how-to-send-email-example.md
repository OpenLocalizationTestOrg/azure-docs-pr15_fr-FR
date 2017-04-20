<properties 
    pageTitle="Store-sendgrid-Java-How-to-Send-email-example" 
    description="Comment envoyer des messages électroniques à l’aide de SendGrid à partir de Java dans un déploiement d’Azure" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Comment envoyer des messages électroniques à l’aide de SendGrid à partir de Java dans un déploiement d’Azure

L’exemple suivant vous montre comment vous pouvez utiliser SendGrid pour envoyer des messages électroniques à partir d’une page web hébergée dans Azure. L’application qui en résulte invitera l’utilisateur pour les valeurs de la messagerie électronique, comme le montre la capture d’écran suivante.

![Formulaire de courrier électronique][emailform]

Le message électronique qui en résulte est semblable à la capture d’écran suivante.

![Message électronique][emailsent]

Vous devez procéder comme suit pour utiliser le code dans cette rubrique :

1. Obtenir les fichiers JAR javax.mail, par exemple à partir de <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Ajouter les fichiers JAR à votre chemin d’accès de build Java.
3. Si vous utilisez Eclipse pour créer cette application Java, vous pouvez inclure les bibliothèques SendGrid dans votre fichier de déploiement de l’application (WAR) à l’aide de la fonction d’assemblage du Éclipse déploiement. Si vous n’utilisez pas Eclipse pour créer cette application Java, vérifiez les bibliothèques sont inclus dans le même rôle Azure que votre application Java et ajoutés au chemin des classes de votre application.


Vous devez également vos propres SendGrid nom d’utilisateur et mot de passe, pour pouvoir envoyer le message électronique. Pour commencer à utiliser SendGrid, Découvrez [comment envoyer des messages électroniques à l’aide de SendGrid à partir de Java](store-sendgrid-java-how-to-send-email.md).

En outre, vous familiariser avec les informations relatives à la [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944), ou d’autres techniques pour héberger les applications Java dans Azure si vous n’utilisez pas Eclipse, est vivement recommandé.

## <a name="create-a-web-form-for-sending-email"></a>Créer un formulaire web pour l’envoi de messages électroniques

Le code suivant montre comment créer un formulaire web pour récupérer des données utilisateur pour l’envoi de messages électroniques. Aux fins de ce contenu, le fichier JSP est nommé **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Créer le code pour envoyer le message électronique

Le code suivant, qui est appelé lorsque vous remplissez le formulaire dans emailform.jsp, crée le message électronique et l’envoie. Aux fins de ce contenu, le fichier JSP est nommé **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Outre l’envoi du courrier électronique, emailform.jsp fournit un résultat de l’utilisateur ; la capture d’écran suivante est un exemple :

![Envoyer les résultats de la messagerie][emailresult]

## <a name="next-steps"></a>Étapes suivantes

Déployer votre application dans l’émulateur cluster dans un navigateur s’exécutent emailform.jsp entrer des valeurs dans le formulaire, cliquez sur **Envoyer ce courrier électronique**et, puis afficher les résultats dans sendemail.jsp.

Ce code a été fourni pour vous montrer comment utiliser SendGrid dans Java sur Azure. Avant de déployer sur Azure en production, vous souhaiterez peut-être ajouter plus de gestion des erreurs ou d’autres fonctionnalités. Par exemple : 

* Vous pouvez utiliser des objets BLOB Azure stockage ou base de données SQL pour stocker les adresses de messagerie et les messages électroniques, au lieu d’utiliser un formulaire web. Pour plus d’informations sur l’utilisation d’objets BLOB Azure stockage dans Java, voir [comment utiliser le Service de stockage Blob de Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Pour plus d’informations sur l’utilisation de base de données SQL en Java, reportez-vous [à l’aide de la base de données SQL en Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Vous pouvez aussi utiliser `RoleEnvironment.getConfigurationSettings` pour extraire le nom d’utilisateur SendGrid et le mot de passe à partir des paramètres de configuration de votre déploiement, au lieu d’utiliser le formulaire web pour récupérer ces valeurs. Pour plus d’informations sur la `RoleEnvironment` de cours, reportez-vous [à l’aide de la bibliothèque d’exécution Service Azure dans JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) et la documentation de package Runtime du Service Azure à <http://dl.windowsazure.com/javadoc>.
* Pour plus d’informations sur l’utilisation de SendGrid dans Java, Découvrez [comment envoyer des messages électroniques à l’aide de SendGrid à partir de Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
