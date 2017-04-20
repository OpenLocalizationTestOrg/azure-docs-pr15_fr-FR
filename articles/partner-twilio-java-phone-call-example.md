<properties 
    pageTitle="Comment effectuer un appel téléphonique à partir de Twilio (Java) | Microsoft Azure" 
    description="Découvrez comment effectuer un appel téléphonique à partir d’une page web à l’aide de Twilio dans une application Java sur Azure." 
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

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Comment effectuer un appel téléphonique à l’aide de Twilio dans une Application Java sur Azure 

L’exemple suivant vous montre comment vous pouvez utiliser Twilio pour passer un appel à partir d’une page web hébergée dans Azure. L’application qui en résulte invitera l’utilisateur pour les valeurs d’un appel téléphonique, comme le montre la capture d’écran suivante.

![Formulaire appel Azure à l’aide de Twilio et Java][twilio_java]

Vous devez procéder comme suit pour utiliser le code dans cette rubrique :

1. Obtenir un compte Twilio et l’authentification jeton. Pour commencer à utiliser Twilio, évaluer les tarifs en [http://www.twilio.com/pricing][twilio_pricing]. Vous pouvez vous inscrire auprès de [https://www.twilio.com/try-twilio][try_twilio]. Pour plus d’informations sur l’API fournie par Twilio, consultez [http://www.twilio.com/api][twilio_api].
2. Obtenir le Twilio JAR. En [https://github.com/twilio/twilio-java][twilio_java_github], vous pouvez télécharger les sources GitHub et créer votre propre JAR ou télécharger un fichier JAR prédéfinie (avec ou sans dépendances).
Le code dans cette rubrique écrite à l’aide de la JAR TwilioJava 3.3.8 avec dépendances prédéfinie.
3. Ajouter le fichier JAR à votre chemin d’accès de build Java.
4. Si vous utilisez Eclipse pour créer cette application Java, incluez la Twilio JAR dans votre fichier de déploiement de l’application (WAR) à l’aide de la fonction d’assemblage du Éclipse déploiement. Si vous n’utilisez pas Eclipse pour créer cette application Java, vérifiez la Twilio JAR est inclus dans le même rôle Azure que votre application Java et ajouté le chemin d’accès de classe de votre application.
5. Assurez-vous que vos clés cacerts contient le certificat de certification Equifax Secure avec MD5 empreinte 67:CB:9 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est 35:DE:F4:CF et l’empreinte numérique SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0 D : 7F:9 D : 62:13:97:86:63:3A). Il s’agit de l’autorité de certification de certification pour la [https://api.twilio.com] [ twilio_api_service] service, qui est appelé lorsque vous utilisez Twilio APIs. Pour plus d’informations sur l’ajout de cette autorité de certification au magasin des cacert de votre JDK, voir [Ajout d’un certificat au magasin de certificats d’autorité de certification Java][add_ca_cert].

En outre, vous familiariser avec les informations à [créer un Hello World Application à l’aide de la boîte à outils Azure pour Éclipse][azure_java_eclipse_hello_world], ou avec d’autres techniques pour héberger les applications Java dans Azure si vous n’utilisez pas Eclipse, est vivement recommandé.

## <a name="create-a-web-form-for-making-a-call"></a>Créer un formulaire web pour effectuer un appel

Le code suivant montre comment créer un formulaire web pour récupérer les données utilisateur d’un appel. À des fins de cet exemple, un nouveau projet web dynamique, nommé **TwilioCloud**, a été créé et **callform.jsp** a été ajouté comme un fichier JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Créer le code pour passer l’appel
Le code suivant, qui est appelé lorsque l’utilisateur remplit le formulaire affiché par callform.jsp, crée le message d’appel et génère l’appel. À des fins de cet exemple, le fichier JSP est nommé **makecall.jsp** et a été ajouté au projet **TwilioCloud** . (Utilisez votre compte Twilio et l’authentification des jetons au lieu des valeurs d’espace réservé affectés à **accountSID** et **jeton d’authentification** dans le code ci-dessous).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Outre l’appel, makecall.jsp affiche le point de terminaison Twilio, la version de l’API et l’état de l’appel. La capture d’écran suivante est un exemple :

![Réponse appel Azure à l’aide de Twilio et Java][twilio_java_response]

## <a name="run-the-application"></a>Exécuter l’application
Voici les principales étapes à suivre pour exécuter votre application ; Détails pour ces étapes sont accessibles à [créer un Hello World Application à l’aide de la boîte à outils Azure pour Éclipse][azure_java_eclipse_hello_world].

1. Exporter votre guerre TwilioCloud vers le dossier Azure **approot** . 
2. Modifiez **startup.cmd** pour votre guerre TwilioCloud décompresser le fichier.
3. Compiler votre application pour l’émulateur cluster.
4. Démarrez votre déploiement dans l’émulateur cluster.
5. Ouvrez un navigateur, puis exécutez **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Entrer des valeurs dans le formulaire et cliquez sur **cet appel**, puis consultez les résultats dans makecall.jsp.

Lorsque vous êtes prêt à déployer Azure, recompilation pour le déploiement dans le cloud, déployer vers Azure et exécuter http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp dans le navigateur (Remplacez par votre valeur pour *your_hosted_name*).

## <a name="next-steps"></a>Étapes suivantes
Ce code a été fourni pour vous montrer les fonctionnalités de base à l’aide de Twilio dans Java sur Azure. Avant de déployer sur Azure en production, vous souhaiterez peut-être ajouter plus de gestion des erreurs ou d’autres fonctionnalités. Par exemple :

* Au lieu d’utiliser un formulaire web, vous pouvez utiliser des objets BLOB Azure stockage ou base de données SQL pour stocker les numéros de téléphone et texte l’appel. Pour plus d’informations sur l’utilisation d’objets BLOB Azure stockage dans Java, Découvrez [comment utiliser le Service de stockage Blob de Java][howto_blob_storage_java]. Pour plus d’informations sur l’utilisation de base de données SQL en Java, reportez-vous [à l’aide de la base de données SQL en Java][howto_sql_azure_java].
* Vous pouvez utiliser **RoleEnvironment.getConfigurationSettings** pour récupérer l’ID du compte Twilio et jeton d’authentification à partir des paramètres de configuration de votre déploiement, au lieu de manière irréversible les valeurs de makecall.jsp. Pour plus d’informations sur la classe **RoleEnvironment** , reportez-vous [à l’aide de la bibliothèque d’exécution Service Azure dans JSP] [ azure_runtime_jsp] et la documentation de package Azure Service d’exécution en [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Le code makecall.jsp affecte une URL fourni par Twilio, [http://twimlets.com/message][twimlet_message_url], à la variable **Url** . Cette URL fournit une réponse Twilio Markup Language (TwiML) qui vous informe Twilio comment procéder à l’appel. Par exemple, le TwiML est renvoyé peut contenir un ** &lt;et&gt; ** verbe qui résulte en texte prononcer au destinataire de l’appel. Au lieu d’utiliser l’URL Twilio condition, vous pouvez créer votre propre service pour répondre à la demande de Twilio ; Pour plus d’informations, voir [comment utiliser Twilio pour la voix et de fonctionnalités SMS dans Java][howto_twilio_voice_sms_java]. Vous trouverez plus d’informations sur TwiML à [http://www.twilio.com/docs/api/twiml][twiml]et plus d’informations sur ** &lt;et&gt; ** et autres verbes Twilio trouverez [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lisez les instructions de sécurité Twilio sur [https://www.twilio.com/docs/security][twilio_docs_security].

Pour plus d’informations sur Twilio, voir [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Voir aussi
* [Comment utiliser Twilio pour la voix et de fonctionnalités SMS dans Java][howto_twilio_voice_sms_java]
* [Ajout d’un certificat dans le magasin de certificats d’autorité de certification Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
