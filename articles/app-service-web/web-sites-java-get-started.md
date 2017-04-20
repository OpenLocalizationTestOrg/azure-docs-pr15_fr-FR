<properties
    pageTitle="Créer une application web Java dans le Service d’application Azure | Microsoft Azure"
    description="Ce didacticiel montre comment déployer une application web Java sur Azure Application Service."
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Créer une application web Java dans le Service d’application Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel montre comment créer une [application web dans le Service d’application Azure] Java à l’aide du [Portail Azure]. Le portail Azure est une interface web que vous pouvez utiliser pour gérer vos ressources Azure.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [activer vos avantages d’abonné Visual Studio] ou [s’inscrire à une version d’évaluation gratuite].
>
> Si vous voulez commencer avec le Service d’application Azure avant de vous inscrivez à un compte Azure, accédez à [Essayer le Service application]. Vous pouvez créer une application web starter courte immédiatement dans le Service d’application ; Aucune carte de crédit n’est nécessaire et aucun engagements.

## <a name="java-application-options"></a>Options de l’application Java

Il existe plusieurs façons, que vous pouvez configurer une application Java dans une application web application Service. 

1. Créer une application, puis configurez les **paramètres de l’Application**.

    Application Service propose plusieurs versions Tomcat et jetée, avec une configuration par défaut. Si l’application que vous allez héberger fonctionnent avec une des versions intégrées, cette méthode de configuration d’un conteneur web est le plus simple et est idéale lorsque vous souhaitez uniquement est télécharger un fichier war dans un conteneur web. Pour cette méthode, vous créez une application dans le portail Azure et puis allez à la carte de **paramètres de l’Application** de votre application choisir votre version de Java ainsi que le conteneur web Java souhaité. Lorsque vous utilisez cette méthode Java et le conteneur de votre web sont exécutés à partir de fichiers programmes. Les autres méthodes de placent le conteneur web et éventuellement la machine virtuelle Java dans l’espace disque. Lorsque vous utilisez ce modèle, vous n’avez pas accès à modifier des fichiers dans cette partie du système de fichiers. Cela signifie que vous ne pouvez pas effectuer les actions à configurer le fichier *server.xml* ou placer des fichiers de bibliothèque dans le dossier */lib* . Pour plus d’informations, voir la [créer et configurer une application web Java](#appsettings) section plus loin dans ce didacticiel.
    
2. Utiliser un modèle à partir de la Azure Marketplace.

    La Azure Marketplace comprend des modèles qui automatiquement créer et configurer des applications web Java avec des conteneurs web Tomcat ou jetée. Les conteneurs de web créent les modèles peuvent être configurés. Pour plus d’informations, consultez la section [utiliser un modèle Java depuis la Azure Marketplace](#marketplace) de ce didacticiel.
  
3. Créer une application, puis copiez manuellement et modifier des fichiers de configuration 

    Vous souhaiterez peut-être héberger une application Java personnalisée qui ne déploie pas dans un des conteneurs web fournies par le Service d’application. Par exemple :
    
    * Votre application Java nécessite une version de Tomcat ou jetée qui n’est pas pris en charge par le Service d’application directement ou fournie dans la galerie.
    * Votre application Java prend les demandes HTTP et ne déploie pas comme une guerre dans un conteneur web existant.
    * Vous voulez configurer vous-même le conteneur web à partir de zéro. 
    * Vous souhaitez utiliser une version de Java qui n’est pas pris en charge dans le Service d’application et à télécharger vous-même.

    Dans les cas comme ceux-ci, vous pouvez créer une application à l’aide du portail Azure et puis fournir les fichiers d’exécution approprié manuellement. Dans ce cas, les fichiers par rapport à votre espace quotas de stockage pour votre plan de services d’application à compter. Pour plus d’informations, voir [télécharger une application web Java personnalisée à Azure].

## <a name="portal"></a>Créer et configurer une application web Java

Cette section montre comment créer une application web et configurez-le pour Java à l’aide de la carte de **paramètres de l’Application** du portail.

1. Connectez-vous au [portail Azure].

2. Cliquez sur **Nouveau > Web + Mobile > Web application**.

    ![Nouvelle application Web][newwebapp]

4. Entrez un nom pour l’application web dans la zone **dans le navigateur** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge apparaît dans la zone de texte.

5. Sélectionnez un **Groupe de ressources** ou créez-en une.

    Pour plus d’informations sur les groupes de ressources, voir [l’aide du portail Azure pour gérer vos ressources Azure].

6. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en une.

    Pour plus d’informations sur les offres de Service d’application, voir [vue d’ensemble des plans Azure Application Service].

7. Cliquez sur **créer**.

    ![Créer dans le navigateur][newwebapp2]
 
8. Lorsque l’application web a été créée, cliquez sur **Web Apps > {votre application web}**.
 
    ![Sélectionnez Web App][selectwebapp]

9. Dans la carte **dans le navigateur** , cliquez sur **paramètres**.

10. Cliquez sur **paramètres de l’Application**.

11. Choisir la **version Java**de votre choix. 

12. Choisir la **version mineure Java**souhaité. Si vous sélectionnez **plus récent**, votre application utilise la dernière version mineure est disponible dans le Service d’application pour cette version majeure Java. L’élément **plus récent** est unique aux applications Java créées à partir des **paramètres de l’Application**. Si vous créez votre application Java à partir de la galerie, vous devez gérer vos propres conteneur et les modifications de la machine virtuelle Java. 

12. Cliquez sur le **conteneur Web**souhaité. Si vous sélectionnez un nom de conteneur qui commence par le **plus récent**, votre application resteront à la dernière version de cette version majeure de conteneur web qui n’est disponible dans le Service d’application. 

    ![Versions de conteneur Web][versions]

13. Cliquez sur **Enregistrer**.

    Au sein d’un certain temps, votre application web devient basé sur Java et configuré pour utiliser le conteneur web que vous avez sélectionné.

14. Cliquez sur **Web Applications > {votre nouvelle application web}**.

15. Cliquez sur l' **URL** pour naviguer vers le nouveau site.

    La page web confirme que vous avez créé une application web basée sur Java.

## <a name="marketplace"></a>Utiliser un modèle Java depuis la Azure Marketplace

Cette section indique comment utiliser la Azure Marketplace pour créer une application web Java. Le même flux général pouvant également être utilisé pour créer un basé sur Java mobile ou une application de l’API. 

1. Connectez-vous au [portail Azure]

2. Cliquez sur **Nouveau > Marketplace**.

    ![Nouvelle Marketplace][newmarketplace]

3. Cliquez sur **Web + Mobile**.

    Vous devrez peut-être faire défiler de gauche pour afficher la carte **Marketplace** dans laquelle vous pouvez sélectionner **Web + Mobile**.

4. Dans la zone de texte Rechercher, entrez le nom de serveur d’applications Java, tels que **Apache Tomcat** ou **jetée**et appuyez sur ENTRÉE.

5. Dans les résultats de recherche, cliquez sur le serveur d’applications Java.

    ![Jetée Mobile Web][webmobilejetty]

6. Dans la première carte **Apache Tomcat** ou **jetée** , cliquez sur **créer**.

    ![Carte portail jetty][jettyblade]

7. Dans la carte suivante **Apache Tomcat** ou **jetée** , entrez un nom pour l’application web dans la zone **dans le navigateur** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge apparaît dans la zone de texte.

8. Sélectionnez un **Groupe de ressources** ou créez-en une.

    Pour plus d’informations sur les groupes de ressources, voir [l’aide du portail Azure pour gérer vos ressources Azure].

9. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en une.

    Pour plus d’informations sur les offres de Service d’application, voir [vue d’ensemble des plans Azure Application Service].

10. Cliquez sur **créer**.

    ![Portail jetty créer][jettyportalcreate2]

    En peu de temps, généralement inférieure à une minute, Azure termine la création de la nouvelle application web.

11. Cliquez sur **Web Applications > {votre nouvelle application web}**.

12. Cliquez sur l' **URL** pour naviguer vers le nouveau site.

    ![URL jetty][jettyurl]

    Tomcat est fourni avec un ensemble par défaut des pages ; Si vous avez choisi Tomcat, vous verrez une page similaire à l’exemple suivant.

    ![Application Web à l’aide de Apache Tomcat][tomcat]

    Si vous avez choisi jetée, vous verrez une page similaire à l’exemple suivant. Jetée n’est un ensemble de la page par défaut, afin que le même JSP utilisé pour un site Java vide est réutilisé ici.

    ![Application Web à l’aide de jetée][jetty]

À présent que vous avez créé l’application web avec un conteneur de l’application, consultez la section [étapes suivantes](#next-steps) pour plus d’informations sur la façon de télécharger votre application à l’application web.

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous avez un serveur d’applications Java en cours d’exécution dans votre application web dans le Service d’application Azure. Pour déployer votre propre code à l’application web, voir [Ajouter une application ou page Web pour votre application web Java].

Pour plus d’informations sur le développement d’applications Java dans Azure, voir le [Centre de développement Java].

<!-- URL List -->

[Ajouter une application ou une page Web à votre application web Java]: ./web-sites-java-add-app.md
[Vue d’ensemble les programmes de Service application Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portail Azure]: https://portal.azure.com/
[activer vos avantages d’abonné Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[s’inscrire à une version d’évaluation gratuite]: http://go.microsoft.com/fwlink/?LinkId=623901
[Essayez de Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751
[dans le navigateur dans le Service d’application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centre de développement Java]: /develop/java/
[Utiliser le portail Azure pour gérer vos ressources Azure]: ../azure-portal/resource-group-portal.md
[Télécharger une application web Java personnalisée dans Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
