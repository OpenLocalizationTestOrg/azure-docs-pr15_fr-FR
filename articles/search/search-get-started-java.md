<properties
    pageTitle="Prise en main recherche Azure dans Java | Microsoft Azure | Service de recherche cloud hébergé"
    description="Découvrez comment créer une application de recherche cloud hébergé sur Azure à l’aide de Java comme langage de programmation."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-java"></a>Prise en main recherche Azure dans Java
> [AZURE.SELECTOR]
- [Portail](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Apprenez à créer une application de recherche Java personnalisée qui utilise la recherche Azure pour son expérience de recherche. Ce didacticiel utilise l' [API REST du Service de recherche Azure](https://msdn.microsoft.com/library/dn798935.aspx) pour construire les objets et les opérations utilisées dans cet exercice.

Pour exécuter cet exemple, vous devez disposer d’un service recherche Azure, vous pouvez vous connecter pour le [Portail Azure](https://portal.azure.com). Pour obtenir des instructions détaillées, voir [créer un service de recherche Azure dans le portail](search-create-service-portal.md) .

Nous avons utilisé les logiciels suivants pour générer et tester cet exemple :

- [Eclipse IDE pour les développeurs Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Veillez à télécharger la version EE. Effectuez les étapes de vérification nécessite une fonctionnalité qui se trouvent uniquement dans cette édition.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Sur les données

Cet exemple d’application utilise des données à partir de [États-Unis géologique Services (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtrés sur l’état de l’île Rhode pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui retourne bâtiments de point de repère tels que les hôpitaux et les établissements scolaires, ainsi que les fonctionnalités géologiques comme flux, lacs et sommets.

Dans cette application, le programme **SearchServlet.java** crée et charge l’index à l’aide d’une construction [indexeur](https://msdn.microsoft.com/library/azure/dn798918.aspx) , extraction du dataset USG filtré à partir d’une base de données SQL Azure public. Informations d’identification prédéfinies et informations de connexion à la source de données en ligne sont fournis dans le code du programme. En termes d’accès aux données, aucune configuration supplémentaire n’est nécessaire.

> [AZURE.NOTE] Nous avons appliqué un filtre dans ce groupe de données afin de rester sous la limite de 10 000 documents de la couche tarification gratuite. Si vous utilisez la couche standard, cette limite ne s’applique pas, et vous pouvez modifier ce code pour utiliser un jeu de données plus grande. Pour plus d’informations sur la capacité pour chaque niveau de tarification, voir [contraintes et limites](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Sur les fichiers de programme

La liste suivante décrit les fichiers pertinents pour cet exemple.

- Search.jsp : Fournit l’interface utilisateur
- SearchServlet.java : Fournit des méthodes (semblables à un contrôleur MVC)
- SearchServiceClient.java : Demandes de poignées HTTP
- SearchServiceHelper.java : Une classe d’assistance qui fournit des méthodes statiques
- Document.Java : Fournit le modèle de données
- config.Properties : définit la clé d’api et l’URL du service de recherche
- Pom.XML : Une dépendance Maven

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trouver le nom de service et la clé d’api de votre service de recherche d’Azure

Tous les appels API REST de recherche Azure nécessitent que vous fournissez l’URL du service et une touche de l’api. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre de raccourcis, cliquez sur le **service de recherche** pour répertorier tous les services de recherche Azure mis en service pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Dans le tableau de bord de service, vous verrez les informations essentielles des vignettes, ainsi que l’icône de clé pour accéder à l’aide des touches d’administration.

    ![][3]

5. Copiez l’URL du service et une clé d’administration. Vous en aurez besoin ultérieurement, lorsque vous les ajoutez au fichier **config.Properties** .

## <a name="download-the-sample-files"></a>Télécharger les fichiers d’exemples

1. Accédez à [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) Github.

2. Cliquez sur **Télécharger le code postal**, enregistrer le fichier .zip sur le disque et puis extraire tous les fichiers qu’il contient. Vous pouvez extraire les fichiers dans votre espace de travail Java afin de pouvoir retrouver le projet plus facilement.

3. Les fichiers d’exemples sont en lecture seule. Cliquez sur Propriétés des dossiers et désactivez l’attribut lecture seule.

Toutes les modifications ultérieures et exécuter les instructions sont effectuées par rapport aux fichiers dans ce dossier.  

## <a name="import-project"></a>Importer un projet

1. Dans Eclipse, sélectionnez **fichier** > **Importer** > **Général** > **Projets existants dans l’espace de travail**.

    ![][4]

2. Dans la zone **Sélectionnez répertoire racine**, accédez au dossier contenant les fichiers d’exemples. Sélectionnez le dossier qui contient le dossier .project. Le projet doit apparaître dans la liste des **projets** comme un élément sélectionné.

    ![][12]

3. Cliquez sur **Terminer**.

4. Utilisez **l’Explorateur de projets** pour afficher et modifier les fichiers. S’il n’est pas déjà ouvert, cliquez sur **fenêtre** > **Afficher la vue** > **Explorateur de projets** ou utilisez le raccourci pour l’ouvrir.

## <a name="configure-the-service-url-and-api-key"></a>Configurer l’URL du service et la clé d’api

1. Dans l' **Explorateur de projets**, double-cliquez sur **config.Properties** pour modifier les paramètres de configuration contenant le nom du serveur et la clé d’api.

2. Consultez les étapes décrites précédemment dans cet article, où vous avez trouvé l’URL du service et la clé de l’api dans le [Portail Azure](https://portal.azure.com), pour obtenir les valeurs que vous êtes prêt à entrer dans **config.Properties**.

3. Dans **config.Properties**, remplacez « Clé Api » avec la touche de l’api de votre service. Ensuite, nom du service (le premier composant de l’URL http://servicename.search.windows.net) remplace « nom du service » dans le même fichier.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurer les environnements de projet, de création et de runtime

1. Dans Eclipse, dans l’Explorateur de projets, cliquez sur projet > **Propriétés** > **Facettes de projet**.

2. Sélectionnez **Web dynamique Module**, **Java**et **JavaScript**.

    ![][6]

3. Cliquez sur **Appliquer**.

4. **Fenêtre**sélectionner > **Préférences** > **Server** > **environnements Runtime** > **Add.**.

5. Développez Apache et sélectionnez la version du serveur Apache Tomcat que vous avez déjà installé. Dans notre système, nous avons installé version 8.

    ![][7]

6. Dans la page suivante, spécifiez le répertoire d’installation Tomcat. Sur un ordinateur Windows, il s’agit probablement C:\Program Files\Apache Software Foundation\Tomcat *version*.

6. Cliquez sur **Terminer**.

7. **Fenêtre**sélectionner > **Préférences** > **Java** > **installé JRE** > **Ajouter**.

8. Dans **Ajouter JRE**, sélectionnez **Machine virtuelle Standard**.

10. Cliquez sur **suivant**.

11. Dans la définition de JRE dans JRE accueil, cliquez sur **l’annuaire**.

12. Accédez à **Program Files** > **Java** et sélectionnez JDK vous avez déjà installé. Il est important de sélectionner le JDK en tant que l’environnement.

13. Dans JRE installé, choisissez le **JDK**. Vos paramètres doivent ressembler à la capture d’écran suivante.

    ![][9]

14. **Fenêtre**sélectionner éventuellement > **Navigateur Web** > **Internet Explorer** pour ouvrir l’application dans une fenêtre de navigateur externes. À l’aide d’un navigateur externe vous offre une meilleure expérience d’application Web.

    ![][8]

Vous avez maintenant terminé les tâches de configuration. Ensuite, vous allez créer et exécuter le projet.

## <a name="build-the-project"></a>Générez le projet

1. Dans l’Explorateur de projets, cliquez sur le nom du projet et sélectionnez **Exécuter en tant que** > **Maven créer...** pour configurer le projet.

    ![][10]

8. Modifier de configuration, objectifs, tapez « nouvelle installation », puis cliquez sur **exécuter**.

Messages d’état sont affichés dans la fenêtre console. Vous devriez voir générer réussite indiquant que le projet créé sans erreur.

## <a name="run-the-app"></a>Exécuter l’application

Dans cette dernière étape, vous exécuterez l’application dans un environnement d’exécution serveur local.

Si vous n’avez pas encore spécifié un environnement d’exécution serveur dans Eclipse, vous devez commencer par ceci.

1. Dans l’Explorateur de projets, développez le **contenu Web**.

5. Avec le bouton droit **Search.jsp** > **Exécuter en tant que** > **s’exécutent sur le serveur**. Sélectionnez le serveur Apache Tomcat, puis cliquez sur **exécuter**.

> [AZURE.TIP] Si vous avez utilisé un espace de travail par défaut pour stocker votre projet, vous devez modifier la **Configuration d’exécution** pour qu’il pointe vers l’emplacement du projet pour éviter une erreur de démarrage de serveur. Dans l’Explorateur de projets, avec le bouton droit **Search.jsp** > **Exécuter en tant que** > **Les Configurations de série**. Sélectionnez le serveur Apache Tomcat. Cliquez sur **Arguments**. Cliquez sur **espace de travail** ou **Système de fichiers** pour définir le dossier contenant le projet.

Lorsque vous exécutez l’application, vous devez voir une fenêtre de navigateur, une zone de recherche permettant d’entrer des termes.

Patientez environ une minute avant de cliquer sur **recherche** pour obtenir la durée de service pour créer et charger l’index. Si vous obtenez une erreur 404 HTTP, vous devez attendre un peu plus longtemps avant de réessayer.

## <a name="search-on-usgs-data"></a>Effectuer une recherche sur les données de groupes de sécurité universels

Le jeu de données USG inclut des enregistrements pertinents pour l’état de l’île Rhode. Si vous cliquez sur **Rechercher** dans une zone de recherche vide, vous obtenez les 50 premières entrées, qui représente la valeur par défaut.

Entrer un terme de recherche donne le moteur de recherche quelque chose pour accéder sur. Vous pouvez entrer un nom régionaux. « Roger Williams » a été le premier gestionnaire de Rhode île. Nombreux parcs, bâtiments et les établissements scolaires sont nommés après lui.

![][11]

Vous pouvez également essayer une de ces conditions :

- Pawtucket
- Pembroke
- OIE + maison

## <a name="next-steps"></a>Étapes suivantes

Il s’agit de la première didacticiel Azure recherche basé sur Java et le dataset USG. Dans le temps, nous allons étendre ce didacticiel pour illustrer les fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous avez déjà une idée dans Azure recherche, vous pouvez utiliser cet exemple faire rebondir pour davantage d’essais, peut-être augmentation de la [page de recherche](search-pagination-page-layout.md), ou implémentation de la [navigation par facettes](search-faceted-navigation.md). Vous pouvez également améliorer lors de la page de résultats de recherche en ajoutant des nombres et le traitement par lots de documents afin que les utilisateurs peuvent parcourir les résultats.

Vous débutez avec recherche Azure ? Nous vous recommandons d’essayer d’autres didacticiels pour développer une compréhension de ce que vous pouvez créer. Visitez notre [page de la documentation](https://azure.microsoft.com/documentation/services/search/) pour trouver d’autres ressources. Vous pouvez également afficher les liens dans notre [vidéo et didacticiel liste](search-video-demo-tutorial-list.md) pour accéder à plus d’informations.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
