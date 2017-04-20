<properties
    pageTitle="Prise en main recherche Azure dans NodeJS | Microsoft Azure | Service de recherche cloud hébergé"
    description="Passez en revue la création d’une application de recherche sur un service de recherche cloud hébergé sur Azure à l’aide de NodeJS en tant que votre langage de programmation."
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

# <a name="get-started-with-azure-search-in-nodejs"></a>Prise en main recherche Azure dans NodeJS
> [AZURE.SELECTOR]
- [Portail](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Apprenez à créer une application de recherche NodeJS personnalisée qui utilise la recherche Azure pour son expérience de recherche. Ce didacticiel utilise l' [API REST du Service de recherche Azure](https://msdn.microsoft.com/library/dn798935.aspx) pour construire les objets et les opérations utilisées dans cet exercice.

Nous avons utilisé [NodeJS](https://nodejs.org) et NPM, [Sublime texte 3](http://www.sublimetext.com/3)et Windows PowerShell sur Windows 8.1 pour développer et tester ce code.

Pour exécuter cet exemple, vous devez disposer d’un service recherche Azure, vous pouvez vous connecter pour le [Portail Azure](https://portal.azure.com). Pour obtenir des instructions détaillées, voir [créer un service de recherche Azure dans le portail](search-create-service-portal.md) .

## <a name="about-the-data"></a>Sur les données

Cet exemple d’application utilise des données à partir de [États-Unis géologique Services (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtrés sur l’état de l’île Rhode pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui retourne bâtiments de point de repère tels que les hôpitaux et les établissements scolaires, ainsi que les fonctionnalités géologiques comme flux, lacs et sommets.

Dans cette application, le programme **DataIndexer** crée et charge l’index à l’aide d’une construction [indexeur](https://msdn.microsoft.com/library/azure/dn798918.aspx) , extraction du dataset USG filtré à partir d’une base de données SQL Azure public. Informations d’identification et de connexion informations à la source de données en ligne sont fournies dans le code du programme. Aucune autre configuration n’est nécessaire.

> [AZURE.NOTE] Nous avons appliqué un filtre dans ce groupe de données afin de rester sous la limite de 10 000 documents de la couche tarification gratuite. Si vous utilisez la couche standard, cette limite ne s’applique pas. Pour plus d’informations sur la capacité pour chaque niveau de tarification, voir [limites de service de recherche](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trouver le nom de service et la clé d’api de votre service de recherche d’Azure

Après avoir créé le service, revenez au portail pour obtenir l’URL ou `api-key`. Connexions à votre service de recherche requièrent que vous ayez à la fois l’URL et un `api-key` s’authentifier à l’appel.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre de raccourcis, cliquez sur le **service de recherche** pour répertorier tous les services de recherche Azure mis en service pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4. Dans le tableau de bord de service, vous verrez les informations essentielles des vignettes, ainsi que l’icône de clé pour accéder à l’aide des touches d’administration.

    ![][3]

5. Copiez l’URL du service, une clé d’administration et une clé de requête. Vous devez les trois plus tard, lorsque vous les ajoutez au fichier config.js.

## <a name="download-the-sample-files"></a>Télécharger les fichiers d’exemples

Utilisez l’une des approches suivantes pour télécharger l’échantillon.

1. Accédez à [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Cliquez sur **Télécharger le code postal**, enregistrez le fichier .zip et puis extraire tous les fichiers qu’il contient.

Toutes les modifications ultérieures et exécuter les instructions sont effectuées par rapport aux fichiers dans ce dossier.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Mettre à jour le config.js. URL du service de recherche et clé d’api

À l’aide de l’URL et clé api que vous avez copiée précédemment, spécifiez l’URL, clé d’administration et clé de requête dans le fichier de configuration.

Touches d’administration accorder le contrôle total sur les opérations de service, notamment la création ou suppression d’un index et chargement de documents. En revanche, les clés de requête servent appliquent aux opérations en lecture seule, généralement utilisées par les applications clientes qui se connectent à Azure recherche.

Dans cet exemple, nous inclure la clé de requête pour renforcer la meilleure pratique de l’utilisation de la clé de requête dans les applications clientes.

La capture d’écran suivante montre **config.js** ouvert dans un texte éditeur, avec les entrées pertinentes délimitées afin que vous puissiez voir où mettre à jour le fichier avec les valeurs qui sont valides pour votre service de recherche.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Héberger un environnement d’exécution pour l’échantillon

L’exemple nécessite un serveur HTTP, que vous pouvez installer globalement à l’aide de npm.

Utiliser une fenêtre PowerShell pour les commandes suivantes.

1. Accédez au dossier qui contient le fichier **package.json** .
2. Type de `npm install`.
2. Type de `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Générer l’index et exécutez l’application

1. Type de `npm run indexDocuments`.
2. Type de `npm run build`.
3. Type de `npm run start_server`.
4. Indiquer votre navigateur à`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Effectuer une recherche sur les données de groupes de sécurité universels

Le jeu de données USG inclut des enregistrements pertinents pour l’état de l’île Rhode. Si vous cliquez sur **Rechercher** dans une zone de recherche vide, vous obtenez les 50 premières entrées, qui représente la valeur par défaut.

Entrer un terme de recherche donne le moteur de recherche quelque chose pour accéder sur. Vous pouvez entrer un nom régionaux. « Roger Williams » a été le premier gestionnaire de Rhode île. Nombreux parcs, bâtiments et les établissements scolaires sont nommés après lui.

![][9]

Vous pouvez également essayer une de ces conditions :

- Pawtucket
- Pembroke
- OIE + maison


## <a name="next-steps"></a>Étapes suivantes

Il s’agit de la première didacticiel Azure recherche basé sur NodeJS et le dataset USG. Dans le temps, nous allons étendre ce didacticiel pour illustrer les fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous avez déjà une idée dans Azure recherche, vous pouvez utiliser cet exemple faire rebondir pour essayer suggesters (requêtes semi-automatique ou la saisie semi-automatique), les filtres et la navigation par facettes. Vous pouvez également améliorer lors de la page de résultats de recherche en ajoutant des nombres et le traitement par lots de documents afin que les utilisateurs peuvent parcourir les résultats.

Vous débutez avec recherche Azure ? Nous vous recommandons d’essayer d’autres didacticiels pour développer une compréhension de ce que vous pouvez créer. Visitez notre [page de la documentation](https://azure.microsoft.com/documentation/services/search/) pour trouver d’autres ressources. Vous pouvez également afficher les liens dans notre [vidéo et didacticiel liste](search-video-demo-tutorial-list.md) pour accéder à plus d’informations.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
