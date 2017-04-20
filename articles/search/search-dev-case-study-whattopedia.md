<properties 
    pageTitle="Étude de cas développeur recherche Azure : Comment WhatToPedia générés un portail infomedia sur Microsoft Azure | Microsoft Azure | Service de recherche cloud hébergé" 
    description="Apprenez à créer un informations portail meta moteur de recherche et utilisation de la recherche Azure, un service de recherche cloud hébergé pour les développeurs." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Étude de cas Microsoft Azure recherche développeur

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>Comment [WhatToPedia.com](http://whattopedia.com/) créé un portail infomedia sur Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">L’idée</font> 


Notre objectif est de créer un portail d’informations qui vous permet de clients se connecter avec revendeurs par le biais une expérience très pertinents, recherche inclus dans l’étendue, similaire à comment remonter les portails correspondance touristes avec l’hébergement, les restaurants et loisirs en territoire contrée. 

Le portail que nous prévoyons fournira une expérience de recherche d’une qualité exceptionnelle sur les données revendeur dans un marché donné, aider les clients de rechercher stores emplacement et autres équipements le revendeur fournit. Nous amorcera le moteur de recherche avec un groupe de données initial, mais hausse de la valeur doit être généré au fil du temps, à l’aide d’abonnés revendeur qui publient des informations sur leurs activités. Promotions, nouvelle marchandise, marques courantes, services spécialisés en interne-– toutes les quelques exemples de données qui ajoute la valeur à notre site. Ces données sont signalées automatique et intégrées au corpus de recherche, une fois que le revendeur s’inscrit en tant qu’abonné. Publicitaires, ainsi que le modèle d’abonnement, fournit le chiffre d’affaires entreprise notre nouveau.

Recherche sera le modèle d’interaction utilisateur prédominance, sur une plateforme cloud intégral. À des fins d’échelle et faible coûts, presque tout ce que nous faisons, à partir de l’utilisation du portail au contrôle de source, sera grâce à un service en ligne. À l’aide du moteur de recherche qui fournit les fonctionnalités que nous avons besoin de prêts à l’emploi, nous pouvons créer rapidement une application de recherche, sans avoir à créer et gérer un moteur de recherche nous.

## <a name="what-we-built"></a>Ce que nous avons créé

WhatToPedia est une société d’infomedia de démarrage. Nous avons créé [WhatToPedia.com](http://whattopedia.com/) – - actuellement dans marché test en Europe du Nord dont la date mise en service le 2 février 2015. Notre clientèle est principalement les magasins de bloc traditionnels qui ont besoin d’une présence en ligne économique facile à gérer et mettre à jour.

Notre tâche consiste à attirer des clients, grâce à un excellent recherche en ligne, augmentation de résultats en fonction de ville ou Club, marques, stocker des noms, ou stocker des types. Attirer les clients a des répercussions, revendeurs motivation pour vous abonner à notre site portail. Les abonnements sont payantes, à un rythme économique.

 ![][7] 

Après vous inscrire à un abonnement, un revendeur prend en charge son profil existant (créé initialement en nous à partir des données que vous avez achetées), mettre à jour avec les données supplémentaires sur les promotions, marques proposées ou annonces. Les fonctionnalités internes, telles que les langues parlées, devises accepté, effectuer des achats telle peut être automatique signalés pour mieux attirer les clients qui recherchent ces équipements.

## <a name="who-we-are"></a>Qui sommes-nous

Mon nom est Thomas Segato (Microsoft Consulting) et j’ai travaillé avec Jesper Boelling, développeur prospect en WhatToPedia, pour créer la solution. 

WhatToPedia est un démarrage, test marketing sa nouvelle entreprise portail en Suède, où la plupart des 60 000 revendeurs sont bloc traditionnels PME (petites et moyennes entreprises). Étant donné que nous sûr qu’une personne vos achats en Europe parle plusieurs langues et comporte plusieurs devises, nous créer des solutions et gérer un client multilingue. Nous nécessaires et trouvé, un moteur de recherche qui prend en charge nos besoins multilingues dans Azure recherche.

Recherche Azure a un changeur de jeu pour notre projet. Avant la disponibilité de la recherche Azure, nous fiche énergétique considérable parcourt les multiplier les tests de la création de notre propre moteur de recherche. Avoir Azure recherche comme un service en ligne supprimé la plus grande difficulté technique et d’administration de notre solution, ce qui signifie prise sur le marché plus rapidement et avec une expérience de recherche plus robuste.  

## <a name="how-we-did-it"></a>Comment nous l’avons fait

Notre objectif était de créer une infrastructure complète basée sur les services de cloud uniquement. Microsoft a été choisi comme la plateforme stratégique, car il a été le fournisseur proposé nécessaires services (pour collaboration et développement), mettre à l’échelle à la demande et tarifs économique.
 
### <a name="high-level-components"></a>Composants de haut niveau

Nous avons créé une entreprise, pas seulement un site. Prise en charge tous les efforts requis une gamme complète d’outils et d’applications. Nous adopté Visual Studio et Visual Studio Team Services pour le développement, Online Service Team Foundation (TFS) pour le contrôle de code source et la gestion de scrum, Office 365 pour les communications et de collaboration et bien entendu Microsoft Azure pour toutes les opérations relatives au site et le stockage. Avec Visual Studio, l’IDE fourni mise en service direct sur Azure, avec l’intégration à TFS Online fournissant un gain de productivité supplémentaires.

L’illustration ci-dessous montre les composants de haut niveau utilisés dans l’infrastructure WhatToPedia.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>Utilisation de Microsoft Azure

Nous prenons les zones verts dans le diagramme précédent, vous verrez que la solution WhatToPedia s’appuie sur ces services :

- [Recherche Azure](https://azure.microsoft.com/services/search/)
- [Sites Web Azure à l’aide de MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs pour les tâches planifiées](../app-service-web/websites-webjobs-resources.md)
- [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/)
- [Remise de courrier électronique SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Cœur de la solution est les données et recherche. Le flux de données à partir du fournisseur revendeur à l’utilisateur final est illustré ci-dessous :

  ![][9]

Stockage de données principal est le revendeur et les données de gestion de base de données SQL Azure. Il s’agit de la dataset initial, en plus des données spécifiques à revendeur ajoutées au fil du temps. Nous utilisons une WebJob Azure pour publier des mises à jour à partir de la base de données SQL pour le corpus recherche dans recherche Azure.

### <a name="presentation-layer"></a>Couche de présentation

Le portail est un site Web Azure, implémentée dans MVC 4 et [Démarrage Twitter](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Nous avons choisi MVC, car il offre une approche beaucoup plus propre au format HTML à développement basée sur les formulaires ASP.NET. Pour éviter d’avoir à créer des applications pour plusieurs appareils et de gérer plusieurs plateformes mobiles, démarrage Twitter a été choisie pour prendre en charge tous les périphériques et plateformes.

### <a name="authentication-permissions-and-sensitive-data"></a>L’authentification, les autorisations et des données sensibles

Clients parcourir le site de manière anonyme. Par conséquent, aucune connexion est requise pour les clients, ni nous stocker les données sur les consommateurs. 

Revendeurs sont une autre histoire. Ici, nous stocker les informations de profil NOT, informations de facturation et du contenu multimédia qu’ils souhaitent exposer sur le site. Chaque revendeur qui s’est abonnée au site obtenir une connexion de l’utilisateur, utilisée pour authentifier l’utilisateur avant d’apporter des mises à jour au profil de l’abonné.  Nous stocker en toute sécurité toutes les données de l’abonné dans le stockage de base de données SQL Azure et BLOB Azure.
Nous avons choisi pour un modèle d’authentification basé sur l’authentification basée sur les formulaires .NET. Nous avons choisi cette approche pour sa simplicité ; Nous n’avions pas besoin les rôles, prise en charge de l’interface utilisateur et autres fonctionnalités superflues fournis avec d’autres approches. 

Pour vous assurer que revendeurs afficher uniquement les données appartenant à ces, nous avons créé un revendeur ID pour chacun d’eux qui est utilisé par la suite sur toutes les lire et écrire des opérations impliquant des données spécifiques au revendeur. Avec cette approche, nous avons trouvé que nous ne pas besoin d’accorder des autorisations de base de données aux revendeurs individuels. Tous les revendeurs interagissent avec le système sous un rôle de base de données unique, de l’ID de revendeur comme notre technique isolement de données.

Étant donné que notre mission les effets en aval (conduite d’entreprise plus aux revendeurs, création inciter à publier ou s’y abonner), nous pouvons dessiner la ligne dans la gestion des achats sur le web. Par conséquent, vous ne trouverez pas un panier sur notre site, ce qui simplifie la nos exigences de sécurité. 

Un autre simplification employée était de confier nos opérations payables facturation et les comptes. Par les informations de paiement client routage directement à un tiers ([SveaWebPay](http://www.sveawebpay.se/)), nous réduire les risques liés à associer le stockage et protection des données sensibles dans notre banques de données. 

### <a name="search-engine"></a>Moteur de recherche

Cœur de notre solution est le moteur de recherche basé sur le service de recherche Azure. Au départ, nous avons créé un moteur de recherche personnalisé, mais pendant ce processus, nous avons réalisé la complexité effort a été très haute bien et que vous y êtes invité nous envisager d’autres alternatives. 

Fonctionnalités de base qui ont été les plus importantes pour nous inclus :

- Filtres
- Navigation par facettes
- Augmentation de résultats
- Mise en page AJAX

Une recherche sur internet nous portées à la vidéo suivante, ce qui nous d’essayer Azure recherche inspired : [Approfondie à TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Après avoir regarder la vidéo, nous avons prêts à créer un prototype basé sur ce que nous l’avons vu. Nous avions déjà un modèle de données dans MVC, création du prototype n’a pas été simple, car les données contenues termes disponible pour la recherche, et nous avions déjà travaillé les exigences pour comment nous voulons trier, facette et filtrer les données. 

Voici comment nous avons créé le prototype.

**Configurer le Service de recherche Azure**

1. Connexion au portail classique Azure et ajouté le service de recherche à notre abonnement. Nous avons utilisé la version partagée (gratuite avec notre abonnement).
2. Créer un index. Pour le prototype, nous avons utilisé le portail de l’interface utilisateur pour définir les champs de recherche et créer les profils score. Notre profil score est basé sur les données d’emplacement : pays | Ville | adresse (voir : ajouter des profils notation).
3. Copiez l’URL du service et administrateur clé api nos fichiers de configuration. Cette clé est dans la page de service de recherche dans le portail et qu’il est utilisé pour authentifier le service.
    
**Développer un travail indexeur recherche – Console Windows**

1. Lire tous les revendeurs à partir de la base de données.
2. Appeler l’API de Service de recherche Azure pour télécharger des revendeurs un par un (voir : http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Définir une propriété dans la base de données pour l’indexation incrémentielles revendeur est indexé. Nous avons fait cela en ajoutant un champ 'indexeur' qui stocke l’état de l’index de chaque profil (indexée ou non). 

Consultez l’annexe pour l’extrait de code qui crée le travail indexeur.

**Développer un portail Web recherche – MVC**

1. Appelez le Service de recherche Azure pour accéder à tous les documents de la recherche (voir : http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extrait de suivi à partir de la réponse de service de recherche (en utilisant json.net http://james.newtonking.com/json)
   - Résultats
   - Facettes
   - Nombre de résultats
   - Développer une interface utilisateur pour l’affichage des résultats de recherche, facettes et le nombre (nous avions déjà cela).

C’est le code que nous avons utilisé pour obtenir les résultats de la recherche Azure :

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Augmentation par emplacement**

Probablement l’exigence les plus importantes pour vérifier dans le prototype notamment par l’ajout d’un mot clé de recherche emplacement à la requête. Il est essentiel de notre portail que si un utilisateur entre un nom de la ville dans la recherche de la requête, qui les revendeurs dans les villes donnera supérieur à celui de revendeur ayant le mot-clé ville dans la description. Pour cela, nous avons utilisé un profil score classement le champ Ville supérieur à celles d’autres champs.

**Prise en charge de plusieurs langues**

Nous nécessaires pour afficher les résultats de recherche corrects dans les langues correctes et fournir une option pour rechercher les mêmes résultats dans différentes langues. Les deux côtés à ce problème ont été : 

- Rechercher des mots dans plusieurs langues
- Afficher les résultats de recherche dans la langue correcte

Nous avons résolu la partie de la présentation en ajoutant un document pour chaque langue avec texte localisé et une propriété en utilisant la langue. Lorsqu’un utilisateur entre un terme de recherche, nous utilisateur `$filter` expressions pour filtrer sur la langue de l’utilisateur a choisi.

Chacun de ces documents possède une propriété masquée appelée « villes » basés sur le type de collection de sites. Cette propriété stocke des noms de ville dans toutes les langues, ce qui permet de rechercher dans plusieurs langues.

###<a name="data-storage"></a>Stockage des données

Toutes les données (profil, abonnement et comptabilité) sont stockées dans la base de données SQL. Tous les fichiers multimédias stockés dans le stockage BLOB Azure, y compris les images et des vidéos fournis par le revendeur. L’utilisation du stockage BLOB distinct isole les effets de téléchargement de fichiers ; fichiers ne sont jamais mixtes avec le site Web, afin que nous n’avez pas besoin de recréer le site chaque fois que nous ajouter des fichiers.

Un avantage important de notre conception du stockage est que plusieurs développeurs peuvent partager un stockage de développement unique. Effectuez la configuration requise pour le projet WhatToPedia a été peut créer un environnement de développement dans les 15 minutes, y compris des vidéos, des images et des données revendeur. En l’obtention de données les plus récentes à partir de Online TFS, exécution d’un script SQL et en exécutant la tâche d’importation, un ensemble de l’environnement peut être relevé pas du tout. Cette pratique améliore également le processus de mise en attente.

###<a name="webjobs"></a>WebJobs

Nous utilisons WebJobs Azure pour mettre à jour des données à l’index. En créant un travail d’indexeur de recherche, le composant d’indexation a été très facile d’intégrer à notre solution. La modification du code seulement nous avons apporté était pour s’adapter à la tâche indexeur pour ajouter un `Indexed` champ à notre modèle de données pour indiquer l’état de l’index. Lorsqu’un nouveau profil est ajouté ou mis à jour, le `Indexed` champ est défini sur false. Ces conditions s’appliquent si le revendeur modifie ses données profil via le portail.  

La tâche recherche toutes les lignes ayant `Indexed` définie sur false. Lorsqu’elle trouve la ligne, le document est publié dans Azure recherche, puis le `Indexed` champ est défini sur true. Nous n’avions pas à planifier l’ajout et mise à jour des données car le service de recherche Azure réellement prend en charge de cette. Si vous ajoutez un document qui est déjà présent, le service d’effectuer une mise à jour automatiquement.

Tous les travaux web ont été développés en tant qu’applications console qui peuvent être téléchargées sur des sites web Azure en tant que fichiers ZIP, décompressées et ensuite planifiées.

La tâche est planifiée pour exécuter chaque 5 minutes sous la forme d’une tâche planifiée web. Nous avons calculé que le service prend environ 3 minutes pour la télécharger 3 000 documents, qui se trouvait dans nos besoins. 

> [AZURE.NOTE] Il existe une fonctionnalité indexeur prototype qui a été récemment introduite dans Azure recherche. Cette fonctionnalité fourni trop tard pour que nous puissions utiliser dans notre first release, mais elle s’affiche pour résoudre le problème persiste, que nous avons utilisé notre travail indexeur, qui consiste à automatiser les opérations de chargement de données.


###<a name="backup-strategy"></a>Stratégie de sauvegarde

Nous avons conçu une stratégie de sauvegarde multicouches pour récupérer à partir d’une plage de scénarios, à partir de panne, vers le bas jusqu'à la récupération d’une transaction individuelle. Les éléments à protéger incluent trois types de données (site web, les données d’abonnés et fichiers multimédias). 

Tout d’abord, en conservant le code source de site web dans TFS en ligne, nous savoir que si le site s’arrête, nous pouvons le reconstruire à republier de TFS. 

Données de l’abonné dans la base de données SQL Azure sont bien plus sensibles. Nous revenir cela à l’aide de la prédéfinis fonction (voir la [sauvegarde de base de données SQL Azure et de restauration](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La planification de sauvegarde est sauvegarde complète une fois par semaine, sauvegardes différentielles une fois par jour et sauvegardes de journal des transactions toutes les 5 minutes.  Compte tenu de la taille des données, cette solution est plus adaptée à notre volumes de données immédiate et prévu.

Enfin, nous stocker des fichiers image et vidéo dans le stockage BLOB Azure. Nous sommes en cours d’évaluation du plan de sauvegarde intégrale pour ces données, envisagez d’Explorer mûre de marais pour Azure comme solution potentielle. Pour l’instant, nous utilisons une WebJob pour copier des images et des vidéos vers un autre emplacement.

##<a name="what-we-learned"></a>Ce que nous avons appris

Étant donné que nous était déjà des données, il est facile d’établir preuve de concept. Au cours des heures, nous avons rencontré un prototype à facettes et compteurs, pagination, classement profils des résultats de recherche. Les résultats de recherche ont été aussi précis, que nous avons décidé à supprimer certaines des filtres présentées à l’utilisateur final. 

Plus grande surprise pour que nous puissions a été la vitesse nous pourrions Découvrez Azure recherche, et combien nous avons obtenu. Littéralement, nous établie preuve de concept en quelques heures (voir la Remarque ci-dessous), le remplacement 500 lignes de code avec 3 lignes de code dans l’application frontale (plus une nouvelle WebJob) et obtenir de meilleurs résultats. 

Notre code implémenté auparavant, pagination, le nombre et les comportements qui sont standard à rechercher. Utiliser la recherche Azure, les résultats que nous revenir incluent les résultats de la recherche, facettes, données, nombre--tous les éléments nous nécessaires et ont été avoir à nous fournir la pagination. Il est également inclus augmentation et la navigation par facettes intégrée, ce qui nous n’avions pas dans notre solution d’origine.

Le plus grand défi lors de l’implémentation a été que c’était une version d’évaluation et trouver des informations et des expériences partagées a été difficile. Une fois que nous connecté quelques points, nous avons trouvé qu’à l’aide du Service de recherche Azure était assez simple en raison de son format de données API REST et JSON. Nous pouvons appeler le cadre directement à partir de plug-ins de source plus ouverts comme JQuery JSON.Net et nous pouvons utiliser des outils comme Fiddler pour essais rapide et le débogage. 

> [AZURE.NOTE] Outre les données prêt, il a permis que ceux d'entre nous créer le prototype déjà compris fonctionnement de la technologie, nous rendre plus productif et plus appreciative des fonctionnalités intégrées de la recherche. Si vous avez besoin de mettre en place de construction de requête de recherche, la navigation par facettes, filtres, etc. prototypage à prendre plus de temps doivent s’attendre. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Contrôle de facettes dans la page de présentation de la recherche

Un de nos appris au cours de la preuve de concept a été planifier facettes avec soin dès le départ. Après le chargement d’un grand nombre de données dans la solution, nous avons vu que le volume de facettes était trop élevé pour présenter aux utilisateurs. 

Nous avons résolu ceci en contraindre le paramètre count facette. Le paramètre count impose une disque limite du nombre de facettes renvoyé à l’utilisateur. Vous pouvez trouver un lien qui inclut une discussion du paramètre count [ici](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs pour la planification des tâches

Recherche Azure n’a pas été uniquement agréable surprise pour nous. Nous avons découvert qu’à l’aide de WebJobs pour automatiser nos opérations de chargement de données à la recherche Azure était largement supérieur à notre approche précédente, qui en résultent à l’aide d’un ordinateur dédié exécutant Windows planificateur, avec les tâches planifiées pour mettre à jour l’index de recherche. WebJobs a été plus facile à configurer et plus facile à déboguer et bien entendu beaucoup moins cher que d’avoir à payer pour une machine virtuelle dédiée.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Explorateur de stockage BLOB Azure pour mettre à jour des images

Nous avons trouvé qu’à l’aide d' [Azure BLOB Storage Explorer](https://azurestorageexplorer.codeplex.com/) (disponible sur codeplex) peuvent être très utiles dans la gestion des mises à jour d’image et vidéo sur le site. Nous l’utiliser comme un outil de développement pour mettre à jour manuellement des images et des vidéos qui font partie de notre site principal. Nous avons trouvé qu’elle soit plus flexible que le déploiement des modifications apportées au portail et élimine une itération de test complète chaque fois que nous avons besoin de mettre à jour une image. 

##<a name="a-few-final-words"></a>Quelques mots finales

Merci d’avoir aux gens excellent en WhatToPedia pour ce qui nous permet de partager leur histoire.  

Nous espérons que vous avez trouvé cette étude de cas utile. Si vous accédez sur utiliser la recherche Azure, je vous recommandons de quelques ressources pour accélérer le long :

- [Forum MSDN dédié à la recherche d’Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow possède également une balise](http://stackoverflow.com/questions/tagged/azure-search)
- [Page de la documentation sur Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Azure documentation de recherche sur MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Appendice : Recherche indexeur WebJob

Le code suivant crée l’indexeur mentionné dans la section sur la création du prototype.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
