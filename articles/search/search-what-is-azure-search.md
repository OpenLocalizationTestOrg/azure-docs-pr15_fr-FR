<properties
    pageTitle="Qu’est recherche Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="Recherche Azure est un service de recherche entièrement gérés hébergé cloud. Pour plus d’informations cette vue d’ensemble de la fonctionnalité."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>Qu’est recherche Azure ?

Recherche Azure est une solution de recherche sous forme de service cloud que délégués gestion du serveur et infrastructure à Microsoft, entraînant l’affichage d’un service de prêts à l’emploi que vous pouvez remplir avec vos données, puis utilisez pour ajouter la recherche à vos web ou une application mobile. Azure recherche vous permet d’ajouter facilement une expérience de recherche robuste à vos applications à l’aide d’un simple [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou [Kit de développement .NET](search-howto-dotnet-sdk.md) sans gestion de l’infrastructure de recherche ou devenir un expert dans Rechercher.

## <a name="give-your-users-a-powerful-search-experience"></a>Donnez à vos utilisateurs une expérience de recherche puissantes

**Requêtes puissantes** peut être préparé en utilisant la [syntaxe de la requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx), qui offre les opérateurs logiques, des opérateurs de recherche une phrase, opérateurs suffixe, opérateurs priorité. En outre, la [syntaxe de la requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) pouvez activer recherche partielle, recherche de proximité, augmentation du magasin de termes et des expressions régulières. Recherche Azure prend également en charge les analyseurs lexicaux personnalisés pour permettre à votre application gérer les requêtes de recherche complexe utilisant la correspondance phonétique et expressions régulières.

**Prise en charge de la langue** est [inclus pour 56 différentes langues](https://msdn.microsoft.com/library/azure/dn879793.aspx). À l’aide de Lucene analyseurs et analyseurs Microsoft (affinés par nombre d’années de langage naturel de traitement dans Office et Bing), recherche Azure permet d’analyser du texte dans la zone de recherche de votre application pour gérer intelligemment linguistiques spécifiques à une langue notamment verbaux, sexe, noms plurielles irréguliers (par exemple, « souris » et « souris »), word désélectionnez la composition, césure (pour les langues sans espace) et plus encore.

**Suggestions de recherche** peut être activé pour les requêtes semi-automatique et barres de recherche de saisie semi-automatique. [Documents réel dans votre index sont proposés](https://msdn.microsoft.com/library/azure/dn798936.aspx) en tant qu’utilisateurs Entrez recherche partielle d’entrée.

**Appuyez sur la mise en surbrillance** [permet](https://msdn.microsoft.com/library/azure/dn798927.aspx) aux utilisateurs de voir l’extrait de texte dans chaque résultat qui contient les correspondances pour leur requête. Vous pouvez sélectionner quels champs retournent extraits mis en surbrillance.

**Navigation par facettes** est facilement ajouté à votre page de résultats de recherche avec la recherche Azure. Utilisez [uniquement un paramètre de requête](https://msdn.microsoft.com/library/azure/dn798927.aspx), recherche Azure renvoie toutes les informations nécessaires pour créer une expérience de recherche par facettes dans l’interface utilisateur de votre application pour permettre à vos utilisateurs pour une exploration des niveaux inférieurs et filtrer les résultats de la recherche (par exemple, filtre catalogue éléments par gamme de prix ou d’une marque).

Prise en charge **géospatiales** vous permet intelligemment traiter, filtrer et afficher des emplacements géographiques. Recherche Azure permet à vos utilisateurs à Explorer les données basée sur la proximité d’un résultat de recherche à l’emplacement spécifié ou sur une zone géographique spécifique. Cette vidéo vous explique comment procéder : [Channel 9 : données Azure recherche et géospatiales](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtres** peuvent être utilisés pour facilement incorporer la navigation par facettes dans l’interface utilisateur de votre application, améliorer formulation de requête et filtre basé sur spécifié par l’utilisateur ou développeur-critères. Créer des filtres importants à l’aide de la [syntaxe de la OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Procureront vos développeurs avec un service facile à utiliser

**Disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsque mise à l’échelle correctement, [recherche Azure propose un SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ne **entièrement gérés** sous forme d’une solution de bout en bout, Azure recherche nécessite absolument aucune gestion de l’infrastructure. Votre service peut être facilement adapté à vos besoins en mise à l’échelle à deux dimensions pour gérer plus de stockage de document, une version ultérieure charges de requêtes ou les deux.

**Intégration de données** à l’aide de [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) permet de recherche Azure analyse automatiquement de base de données SQL Azure, Azure DocumentDB ou [stockage d’objets Blob Azure](search-howto-indexing-azure-blob-storage.md) pour synchroniser du contenu de votre index de recherche avec votre banque de données principale.

**Piratage de document** est disponible (actuellement en preview) [pour lire et formats de fichier principales d’index](search-howto-indexing-azure-blob-storage.md) , y compris Microsoft Office, ainsi que les documents PDF et HTML.

**Analytique le trafic de recherche** sont [collectées et analyser les facilement](search-traffic-analytics.md) à déverrouiller analyses à partir de ce que les utilisateurs sont à taper dans la zone de recherche.

**Notation simple** est des principaux avantages de la recherche Azure. [Notation profils](https://msdn.microsoft.com/library/azure/dn798928.aspx) sont utilisés pour autoriser les organisations à la pertinence de modèle en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous souhaiterez nouveaux produits ou d’une remise produits pour apparaître plus haut dans les résultats de recherche. Vous pouvez également créer des profils score à l’aide de balises pour notation personnalisée basée sur les préférences de recherche client que vous avez suivi et stocké séparément.

**Tri** est proposée pour plusieurs champs via le schéma d’index et puis activée ou désactivée au moment de la requête avec un paramètre de recherche unique.

**Pagination** et la limitation de vos résultats de recherche est [simple avec le contrôle précises](search-pagination-page-layout.md) qui recherche Azure offre par rapport à vos résultats de recherche.  

**Recherche explorer** vous permet pour effectuer des requêtes par rapport à toutes vos index droite du portail Azure de votre compte afin de pouvoir facilement tester des requêtes et affiner des profils score.

## <a name="how-it-works"></a>Mode de fonctionnement

### <a name="1-provision-service"></a>1. service de mise en service
Vous pouvez lancer un service de recherche Azure à l’aide du [Portail Azure](https://portal.azure.com/) ou l' [API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Selon la façon dont vous configurez le service de recherche, vous allez utiliser le niveau de service qui a partagé avec d’autres abonnés Azure recherche gratuit, ou un [payé couche](https://azure.microsoft.com/pricing/details/search/) qui dédie ressources devant être utilisé uniquement par votre service. Lors de la configuration de votre service, vous choisissez également la région du centre de données qui héberge votre service.

Selon le niveau de service que vous choisissez, vous pouvez mettre à l’échelle votre service à deux dimensions : 1) ajoutez des réplicas pour accroître votre capacité à gérer les charges de la requête et (2) ajouter des Partitions pour ajouter d’espace de stockage pour plusieurs documents. En gérant séparément débit de stockage et de requête de document, vous pouvez personnaliser votre service de recherche correspondant à vos besoins.

### <a name="2-create-index"></a>2. créer l’index
Avant que vous pouvez télécharger votre contenu à votre service de recherche Azure, vous devez d’abord définir un index de recherche Azure. Un index est semblable à une table de base de données qui contient vos données et peut accepter des requêtes de recherche. Vous définissez le schéma d’index pour établir une correspondance entre la structure des documents que vous souhaitez effectuer une recherche, similaires aux champs d’une base de données.

Le schéma de ces index soit peut être créé dans le portail Azure, ou par programme en [utilisant le Kit de développement .NET](search-howto-dotnet-sdk.md) ou [API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Une fois que l’index est défini, vous pouvez ensuite télécharger vos données au service de recherche Azure où il est indexé par la suite.

### <a name="3-index-data"></a>3. données d’Index
Une fois que vous avez défini les champs et les attributs de votre index, vous êtes prêt à télécharger votre contenu dans l’index. Vous pouvez utiliser un modèle envoi ou d’extraction pour télécharger des données à l’index.

Le modèle d’extraction est fourni par le biais indexeurs qui peuvent être configurés pour la demande ou des mises à jour planifiées (voir [les opérations d’indexeur (API REST Service Azure recherche)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), ce qui vous pour ajouter facilement des données et modification des données à partir d’une DocumentDB Azure, base de données SQL Azure, stockage d’objets Blob Azure hébergé dans un ordinateur virtuel Azure SQL Server.

Le modèle push est fourni par le Kit de développement logiciel ou API REST utilisées pour l’envoi de documents mis à jour un index. Vous pouvez distribuer des données à partir de pratiquement n’importe quel groupe de données en utilisant le format JSON. Voir [Ajouter, mettre à jour, ou supprimer les Documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [comment utiliser le Kit de développement .NET)](search-howto-dotnet-sdk.md) pour des instructions sur le chargement des données.

### <a name="4-search"></a>4. recherche
Une fois que vous avez rempli votre index de recherche Azure, vous pouvez désormais [effectuer des requêtes de recherche](https://msdn.microsoft.com/library/azure/dn798927.aspx) à votre point de terminaison du service à l’aide de requêtes HTTP simples avec l’API REST ou le Kit de développement .NET.

## <a name="try-it-now-for-free"></a>Essayez-le maintenant (gratuitement !)
Vous pouvez essayer Azure recherche aujourd'hui ! Si vous possédez déjà un compte Azure, vous pouvez [la mise en service un service dans la couche gratuit](search-create-service-portal.md).

Si vous n’avez pas un compte Azure, que vous pouvez essayer une session gratuite de 60 minutes avec aucun Inscrivez-vous requis. Accédez au [Service d’application Azure essayez](http://go.microsoft.com/fwlink/p/?LinkId=618214) et sélectionnez « Web App ». Puis sélectionnez le modèle « ASP.NET + Azure recherche » pour commencer.
