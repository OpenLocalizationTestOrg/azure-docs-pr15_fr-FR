<properties
    pageTitle="Créer et déployer une application API Java dans le Service d’application Azure"
    description="Découvrez comment créer un package d’application API Java et déployer Azure application service."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Créer et déployer une application API Java dans le Service d’application Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ce didacticiel montre comment créer une application Java et déployer aux applications de l’API Azure Application Service à l’aide de [Git]. Vous pouvez suivre les instructions présentées dans ce didacticiel sur les systèmes d’exploitation est en mesure de l’exécution de Java. Le code dans ce didacticiel est généré à l’aide de [Maven]. [JAX-r] est utilisé pour créer le Service RESTful et est généré basée sur la spécification de métadonnées [Swagger] à l’aide de l' [Éditeur Swagger].

## <a name="prerequisites"></a>Conditions préalables

1. [Kit de développement des Java 8] \(ou version ultérieure)
1. [Maven] installé sur votre ordinateur de développement
1. [Git] installé sur votre ordinateur de développement
1. Un abonnement payé ou [version d’évaluation gratuite] à [Microsoft Azure]
1. Une application de test HTTP comme [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold l’API à l’aide de Swagger.IO

À l’aide de l’éditeur en ligne swagger.io, vous pouvez entrer code JSON Swagger ou YAML représentant la structure de votre API. Une fois que la surface d’API conçue, vous pouvez exporter une variété de plateformes et de structures de code. Dans la section suivante, le code avec génération de modèles sera modifié pour inclure les fonctionnalités factice. 

Cette démonstration va commencer avec un corps JSON Swagger vous allez copier dans l’éditeur de swagger.io qui servira à générer un code ce qui permet d’accéder à un point de terminaison API REST de JAX-r. Puis, vous devez modifier le code avec génération de modèles pour renvoyer les données factices, simuler un API REST basées sur un mécanisme de persistance de données.  

1. Copiez le code suivant JSON Swagger dans votre Presse-papiers :

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Accédez à la [Swagger en ligne de l’éditeur]. Une fois, cliquez sur l’élément de menu **fichier -> Coller JSON** .

    ![Élément de menu Coller JSON][paste-json]

1. Collez les Contacts liste API Swagger JSON préalablement copiée. 

    ![Collage de code JSON dans Swagger][pasted-swagger]

1. Afficher les pages de documentation et le résumé API affiché dans l’éditeur. 

    ![Affichage Swagger généré documents][view-swagger-generated-docs]

1. Sélectionnez l’option de menu **serveur Générer-> JAX RS** à scaffold le code côté serveur, vous devez modifier ultérieurement pour ajouter l’implémentation factice. 

    ![Générer un élément de Menu du Code][generate-code-menu-item]

    Une fois que le code est généré, vous recevrez un fichier ZIP à télécharger. Ce fichier contient le code avec génération de modèles par le Générateur de code Swagger et toutes à créer des scripts. Décompressez toute la bibliothèque dans un répertoire sur votre poste de développement. 

## <a name="edit-the-code-to-add-api-implementation"></a>Modifier le Code pour ajouter l’implémentation de l’API

Dans cette section, vous devez remplacer l’implémentation du code généré Swagger côté serveur avec votre code personnalisé. Le nouveau code renverra une entités liste de tableaux de Contact au client appelant. 

1. Ouvrez le fichier de modèle *Contact.java* , qui se trouve dans le dossier de *génération/src/java/swagger/io/modèle* , à l’aide de [Code de Visual Studio] ou votre éditeur de texte. 

    ![Fichier de modèle de Contact ouvert][open-contact-model-file]

1. Ajoutez le constructeur suivant à la classe **Contact** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Ouvrez le fichier d’implémentation *ContactsApiServiceImpl.java* service, qui se trouve dans le dossier *src/principaux/java/io/swagger/api / l’implémentation* , à l’aide de [Code de Visual Studio] ou votre éditeur de texte.

    ![Fichier de Code de Service de Contact ouvert][open-contact-service-code-file]

1. Remplacer le code dans le fichier avec ce nouveau code pour ajouter une mise en œuvre factice au code de service. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Ouvrez une invite de commandes et placez-vous dans le dossier racine de votre application.

1. Exécutez la commande Maven suivante pour générer le code et exécuter à l’aide du serveur application jetée localement. 

        mvn package jetty:run

1. Vous devriez voir la fenêtre de commande refléter que jetée a démarré votre code sur le port 8080. 

    ![Fichier de Code de Service de Contact ouvert][run-jetty-war]

1. [Postman] permet d’effectuer une demande de « obtenir tous les contacts » méthode API http://localhost : 8080/api/contacts.

    ![Appelez les Contacts API][calling-contacts-api]

1. Utilisez [Postman] pour effectuer une requête à la méthode de l’API « obtenir contact spécifique » située à http://localhost : 8080/api/contacts/2.

    ![Appelez les Contacts API][calling-specific-contact-api]

1. Pour finir, créez le fichier WAR Java (ARchive Web) en exécutant la commande suivante Maven dans votre console. 

        mvn package war:war

1. Une fois le fichier WAR est créé, il est placé dans le dossier **cible** . Naviguer dans le dossier **cible** et renommez le fichier WAR **ROOT.war**. (Assurez-vous que la casse correspond à ce format).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Pour finir, exécutez la commande suivante à partir du dossier racine de votre application pour créer un dossier à utiliser pour déployer le fichier WAR sur Azure **déployer** . 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publier la sortie au Service d’application Azure

Dans cette section, vous allez apprendre à créer une nouvelle application API à l’aide du portail Azure, préparer cette application API pour héberger les applications Java et déployez le fichier WAR nouvellement créé dans Azure Application Service pour exécuter votre nouvelle application API. 

1. Créer une nouvelle application API dans le [portail Azure], en cliquant sur l’élément de menu **Nouveau -> Web + Mobile -> application API** , entrant les détails de votre application, puis sur **créer**.

    ![Créer une nouvelle API application][create-api-app]

1. Une fois que votre application API a été créée, ouvrez la carte de **paramètres** de votre application, puis cliquez sur l’élément de menu **paramètres de l’Application** . Sélectionnez les dernières versions Java parmi les options disponibles, puis sélectionnez la dernière Tomcat dans le menu **Web conteneur** et puis cliquez sur **Enregistrer**.

    ![Configurer la Java dans la carte API application][set-up-java]

1. Cliquez sur l’élément de menu paramètres **informations d’identification de déploiement** et fournir un nom d’utilisateur et mot de passe que vous souhaitez utiliser pour la publication de fichiers dans votre application API. 

    ![Définir les informations d’identification du déploiement][deployment-credentials]

1. Cliquez sur l’élément de menu paramètres **source du déploiement** . Une fois, cliquez sur le bouton **Choisir les sources de** , sélectionnez l’option **Référentiel Git Local** , puis cliquez sur **OK**. Cela créera un référentiel Git en cours d’exécution dans Azure, qui comporte une association avec votre application API. Chaque fois que vous vous engagez code à la branche *maître* de votre référentiel Git, votre code sera publié dans votre instance API application en cours d’exécution en temps réel. 

    ![Configurer un nouveau référentiel Git local][select-git-repo]

1. Copier l’URL du nouveau Git référentiel dans votre Presse-papiers. Enregistrer cette comme il sera important dans un moment. 

    ![Configurer un nouveau référentiel Git pour votre application][copy-git-repo-url]

1. GIT distribuer le fichier WAR vers le référentiel en ligne. Pour ce faire, naviguez jusqu’au dossier **déployer** que vous avez créée afin que vous pouvez facilement valider le code jusqu'à le référentiel en cours d’exécution dans votre Service d’application. Une fois que vous êtes dans la fenêtre de la console et navigation dans le dossier où se trouve le dossier applications Web, exécutez les commandes Git suivantes pour lancer le processus et lancer un déploiement. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Une fois que vous exécutez la requête **push** , vous serez invité pour le mot de passe que pour les informations d’identification de déploiement que vous avez créée. Après avoir entré vos informations d’identification, vous devez voir votre portail afficher que la mise à jour a été déployée.

1. Si vous utilisez une nouvelle fois Postman atteint l’application API nouvellement déployé en cours d’exécution dans le Service d’application Azure, vous verrez que le comportement est cohérent et que maintenant elle renvoie les données de contact comme prévu, et à l’aide des modifications du code simple pour la Swagger.io avec génération de modèles code Java. 

    ![Utilisation de votre API REST de Contacts Java live dans Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez réussi à démarrer avec un fichier Swagger JSON et certaines avec génération de modèles code Java obtenu à partir de l’éditeur Swagger.io. À partir de là, vos modifications simples et une Git déploiement processus a donné lieu à avoir une application API fonctionnelle écrite en Java. Le didacticiel suivant montre comment [utiliser des applications API JavaScript clients, à l’aide de CORS][App Service API CORS]. Dans la série de didacticiels suivants montrent comment implémenter l’authentification et l’autorisation.

Pour créer dans cet exemple, vous pouvez en savoir plus sur le [Stockage SDK pour Java] pour conserver les objets BLOB JSON. Ou, le [Document DB Java SDK] vous permet d’enregistrer vos données de Contact dans Azure Document DB. 

Pour plus d’informations sur l’utilisation de Java dans Azure, voir le [Centre de développement Java].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portail Azure]: https://portal.azure.com/
[Document de base de données Java SDK]: ../documentdb/documentdb-java-application.md
[version d’évaluation gratuite]: https://azure.microsoft.com/pricing/free-trial/
[GIT]: http://www.git-scm.com/
[Centre de développement Java]: /develop/java/
[Kit de développement des Java 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[JAX-r]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Éditeur de Swagger en ligne]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Stockage SDK pour Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Éditeur de swagger]: http://editor.swagger.io/
[Code de Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
