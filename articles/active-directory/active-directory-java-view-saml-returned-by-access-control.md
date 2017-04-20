<properties
    pageTitle="Affichage SAML renvoyée par le Service de contrôle d’accès (Java)"
    description="Découvrez comment afficher SAML renvoyée par le Service de contrôle d’accès dans les applications Java hébergées sur Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Comment afficher SAML renvoyée par le Service de contrôle d’accès Azure

Ce guide vous montrent comment afficher les sous-jacent sécurité SAML Assertion Markup Language () renvoyé à votre application par Azure Access Control Service (ACS). Le guide s’appuie sur la rubrique [comment authentifier des utilisateurs Web avec Azure accès contrôle Service à l’aide de Eclipse][] , en fournissant du code qui affiche les informations SAML. L’application terminée est semblable à ce qui suit.

![Exemple de sortie SAML][saml_output]

Pour plus d’informations sur ACS, consultez la section [étapes suivantes](#next_steps) .

> [AZURE.NOTE]
> Le filtre de contrôle Azure Access Services est un aperçu de la technologie de communauté. En tant que logiciel précommercial, il n'est pas auparavant pris en charge par Microsoft.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les tâches dans ce guide, effectuez l’échantillon comment [authentifier des utilisateurs Web avec Azure accès contrôle Service à l’aide de Eclipse][] et utiliser comme point de départ pour ce didacticiel.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Ajouter la bibliothèque JspWriter à votre assembly de chemin d’accès et déploiement de génération

Ajouter la bibliothèque qui contient la classe **javax.servlet.jsp.JspWriter** pour votre assembly de chemin d’accès et déploiement de génération. Si vous utilisez Tomcat, la bibliothèque est **jsp api.jar**, qui se trouve dans le dossier de **bibliothèque** Apache.

1. Dans l’Explorateur de projets de Eclipse, avec le bouton droit **MyACSHelloWorld**et cliquez sur **Créer un chemin d’accès**, cliquez sur **Configurer le tracé créer**, cliquez sur l’onglet **bibliothèques** , puis cliquez sur **Ajouter externes JARs**.
2. Dans la boîte de dialogue **Sélection JAR** , naviguer de la nécessaire coller sur, sélectionnez-le, puis cliquez sur **Ouvrir**.
3. Boîte de dialogue de **Propriétés pour MyACSHelloWorld** encore ouvert, cliquez sur **Ensemble de déploiement**.
4. Dans la boîte de dialogue **Assembly de déploiement Web** , cliquez sur **Ajouter**.
5. Dans la boîte de dialogue **Nouvelle Directive Assembly** **Java générer des entrées de chemin d’accès** , puis cliquez sur **suivant**.
6. Sélectionnez la bibliothèque appropriée et cliquez sur **Terminer**.
7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Modifier le fichier JSP pour afficher SAML

Modifiez **index.jsp** pour utiliser le code suivant.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Exécuter l’application

1. Exécuter votre application dans l’émulateur ordinateur ou déployer sur Azure, en suivant les étapes présentées comment [authentifier des utilisateurs Web avec Azure accès contrôle Service à l’aide de Eclipse][].
2. Lancez un navigateur et ouvrez votre application web. Après que vous être connecté à votre application, vous verrez informations SAML, y compris l’assertion de sécurité fournie par le fournisseur d’identité.

## <a name="next-steps"></a>Étapes suivantes

Pour approfondir Explorer des fonctionnalités de ACS et pour tester les scénarios plus sophistiqués, consultez [accès contrôle Service 2.0][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Service de contrôle d’accès 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[L’authentification des utilisateurs Web avec le Service de contrôle d’accès Azure à l’aide d’Éclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 