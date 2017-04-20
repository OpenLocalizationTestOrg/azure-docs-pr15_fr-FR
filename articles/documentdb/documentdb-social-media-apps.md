<properties 
    pageTitle="Modèle de conception DocumentDB : applications médias sociaux | Microsoft Azure" 
    description="Obtenir des informations sur un modèle de conception de réseaux sociaux en tirant parti de la flexibilité de stockage de DocumentDB et d’autres services Azure." 
    keywords="applications de médias sociaux"
    services="documentdb" 
    authors="ealsur" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="mimig"/>

# <a name="going-social-with-documentdb"></a>Allez mise en réseau avec DocumentDB

Courantes dans une société considérablement interconnectés signifie que, à un moment donné dans la vie, vous Devenez membre d’un **réseau social**. Nous utilisons des réseaux sociaux pour rester en contact avec vos collègues, vos amis, la famille, ou parfois partager notre passion avec des personnes ayant des intérêts communs.

Comme ingénieurs ou développeurs, nous pouvons ont demandé comment ces réseaux stocker et interconnexion nos données, ou peut avoir même chargé pour créer ou concevoir un nouveau réseau social pour un marché derniers vous-même. Qui est lorsque la question volumineux se pose : comment toutes ces données sont stockées ?

Supposons que nous allons créer un nouveau et brillant réseau, où les utilisateurs peuvent envoyer des articles avec média connexes tels que des images, des vidéos ou des pair musique. Les utilisateurs peuvent commenter les publications et attribuer des points à évaluations. Il y ait flux des billets que verront les utilisateurs et être en mesure d’interagir avec sur la page de lancement de site Web principale. Cela ne son vraiment complexe (au début), mais pour simplifier la démonstration, nous allons vous arrêtez pas là (nous avons approfondir flux utilisateur personnalisées affectées par les relations, mais qu’il dépasse l’objectif de cet article).

Par conséquent, comment nous cela stocker et où ?

Bon nombre d'entre vous peuvent avoir expérience sur les bases de données SQL ou au moins avoir notion de [modélisation de données relationnelles](https://en.wikipedia.org/wiki/Relational_model) et vous pourriez être tenté de commencer à dessiner ressemblant à ceci :

![Diagramme illustrant un modèle relationnel relatif](./media/documentdb-social-media-apps/social-media-apps-sql.png) 

Une structure de données parfaitement centrée réduite et belles... qui ne l’échelle. 

Ne vous y incorrect, j’ai travaillé les bases de données SQL tous les ma vie, ils sont très pratiques, mais comme chaque plateforme motif, relatives aux exercices pratiques et les logiciels, il n’est pas idéal pour tous les scénarios.

Pourquoi n’est-il pas SQL du meilleur choix dans ce scénario ? Nous allons étudier la structure d’une seule publication, si je souhaite afficher ce billet dans un site Web ou une application, qu'exécutez une requête avec dois-je... 8 jointures de table ( !) tout simplement pour afficher une seule publication, à présent, image d’un flux de billets dynamiquement charger qui apparaissent sur l’écran et que vous pouvez voir où je vais.

Nous pourrions, bien entendu, utiliser une instance SQL humongous avec alimentation suffisante pour résoudre des milliers de requêtes avec ces de jointures pour répondre à notre contenu, mais véritablement, pourquoi nous lorsqu’il existe une solution plus simple ?

## <a name="the-nosql-road"></a>Déplacement NoSQL

Il existe des bases de données graph spécial qui peuvent [s’exécuter sur Azure](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure) mais qu’ils ne sont pas peu coûteux et nécessitent IaaS services (Infrastructure en tant que Service, Machines virtuelles principalement) et la maintenance. Je vais visent cet article en une solution économique qui est prises en charge la plupart des scénarios, s’exécutant sur base de données de Azure NoSQL [DocumentDB](https://azure.microsoft.com/services/documentdb/). À l’aide d’une approche [NoSQL](https://en.wikipedia.org/wiki/NoSQL) , le stockage de données au format JSON et en appliquant [la dénormalisation](https://en.wikipedia.org/wiki/Denormalization), notre billet précédemment complexe peut être transformé en un seul [Document](https://en.wikipedia.org/wiki/Document-oriented_database):

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Et il peut être obtenu avec une seule requête et aucune jointure. Il est beaucoup plus simple et rapide et budget-wise, elle nécessite moins de ressources pour un meilleur résultat.

DocumentDB Azure permet de s’assurer que toutes les propriétés sont indexées avec son [l’indexation automatique](documentdb-indexing.md), qui peut même être [utilisé](documentdb-indexing-policies.md). L’approche exempt de schéma nous permet de stocker des Documents avec différents et dynamiques structures, peut-être demain que nous voulons publications dans une liste de catégories ou hashtags leur sont associées, DocumentDB gérera les nouveaux Documents avec les attributs ajoutés sans aucun travail supplémentaire requis par nous.

Des commentaires dans un billet peuvent être considérées comme n’importe quel autres billets avec une propriété parent (Cela simplifie notre mappage d’objets). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Et toutes les interactions sociales peuvent être stockées sur un objet distinct en tant que compteurs :

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Création de flux est simplement une question de création de documents contenant une liste des ID de publication avec un indice de pertinence donné :

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Nous pouvons être « dernière » à un flux avec billets classés par date de création, un flux de « plu » avec ces billets avec aime plus dans les dernières 24 heures, nous pouvons même implémenter un flux de données personnalisé pour chaque utilisateur en fonction de logique comme suivent et centres d’intérêt et il est toujours une liste des billets. Il s’agit de la façon de créer ces listes, mais les performances de lecture restent sans gêne. Une fois que nous acquérir une de ces listes, nous envoyer une requête unique à DocumentDB à l’aide de l' [opérateur](documentdb-sql-query.md#where-clause) pour obtenir des pages de billets à la fois.

Les flux flux peuvent être créées à l’aide de processus d’arrière-plan [Azure application Services](https://azure.microsoft.com/services/app-service/) : [Webjobs](../app-service-web/web-sites-create-web-jobs.md). Une fois créé un billet, traitement en arrière-plan peut être déclenché à l’aide de [Stockage Azure](https://azure.microsoft.com/services/storage/) [files d’attente](../storage/storage-dotnet-how-to-use-queues.md) et Webjobs qui s’affiche suite à l’aide du [Kit de développement logiciel Azure Webjobs](../app-service-web/websites-dotnet-webjobs-sdk.md), mise en œuvre la propagation billet à l’intérieur de flux basés sur notre propre logique personnalisée. 

Points et j’aime sur une publication peut être traité de manière différée à l’aide de cette même technique pour créer un environnement finalement cohérent.

Suivent est plus compliqué. DocumentDB propose une limite de taille de document de 512 Ko, de sorte que vous pensez que sur le stockage suivent en tant que document avec cette structure :

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Cela peut fonctionner pour un utilisateur avec des milliers quelques suivent, mais si certains célébrités joint nos rangs, suivant apparaît approche finalement atteint l’extrémité de la taille du document.

Pour résoudre ce problème, nous pouvons utiliser une approche mixte. Dans le cadre du document statistiques utilisateur nous pouvons stocker le nombre d’adeptes :

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Et le graphique réel des adeptes peut être stocké sur des Tables de stockage Azure à l’aide d’une [Extension](https://github.com/richorama/AzureStorageExtensions#azuregraphstore) permettant de stockage « A-suit-B » simple et la récupération. De cette façon, nous pouvons déléguer le processus d’extraction de la liste de suivent exacte (quand nous avons besoin de celui-ci) dans les Tables de stockage Azure mais pour une recherche rapide de nombres, nous utiliserons à l’aide de DocumentDB.

## <a name="the-ladder-pattern-and-data-duplication"></a>La duplication « Échelle » motif et des données

Comme vous avez probablement remarqué dans le document JSON qui fait référence à un billet, il existe plusieurs occurrences d’un utilisateur. Et vous devez deviner droite, que cela signifie que les informations qui représente un utilisateur, compte tenu de cette dénormalisation, peuvent être présentes dans plusieurs endroits.

Pour permettre les requêtes plus rapides, nous subir des doublons. Le problème avec cet effet secondaire est que si par une action, les modifications des données d’un utilisateur, nous devons trouver toutes les activités il jamais avez-vous et mettre à jour tous les. Ne son très pratique, à droite ?

Bases de données de graphique le résoudre à leur façon, nous allons résoudre en identifiant les attributs de clé d’un utilisateur qui nous affichons dans notre application pour chaque activité. Si nous visuellement afficher un billet dans notre application et afficher uniquement le créateur nom et image, pourquoi toutes les données de l’utilisateur de stocker dans l’attribut « createdBy » ? Si pour chaque commentaire nous afficher uniquement l’image de l’utilisateur, nous n’avez pas particulièrement besoin le reste de ses informations. C’est là quelque chose que j’appelle le modèle « échelle » en lecture.

Nous allons récupérer des informations utilisateur par exemple :

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }
    
En consultant ces informations, nous pouvons rapidement détecter des informations critiques et qui n’est pas, ce qui crée une « échelle » :

![Diagramme d’un modèle d’échelle](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

L’étape plus petit est appelée un UserChunk, l’information minimale qui identifie un utilisateur et qu’il est utilisé pour des doublons. En réduisant la taille des données dupliquées pour que les informations qui nous « affiche », nous réduire les risques de mises à jour groupées.

L’étape intermédiaire est appelée l’utilisateur, il s’agit des données complètes qui seront utilisées dans la plupart des requêtes dépendant de performances sur DocumentDB, les plus consulté et critique. Il inclut les informations représentées par un UserChunk.

Le plus grand est l’utilisateur étendu. Il inclut toutes les informations utilisateur critiques ainsi que les autres données qui ne nécessitent pas réellement à lire rapidement ou son utilisation est éventuelle (par exemple, le processus de connexion). Ces données peuvent être stockées en dehors de DocumentDB, dans la base de données SQL Azure ou les Tables de stockage Azure.

Pourquoi serait fractionner l’utilisateur et même stocker ces informations à des endroits différents ? Espace de stockage en DocumentDB étant [pas infini](documentdb-limits.md) et à partir de des performances point de vue, plus les documents, les costlier les requêtes. Conserver les documents mince, avec les informations appropriées pour effectuer toutes vos requêtes dépendant de performances de votre réseau social et stocker les autres informations supplémentaires pour les scénarios éventuelle comme modifications de profil complet, connexions, y compris l’exploration de données analytique de l’utilisation des initiatives Big Data. Nous ne s’occupe pas vraiment si la collecte des données par l’exploration de données est plus lente, car il s’exécute sur base de données SQL Azure, nous ont concernent bien que nos utilisateurs ont une expérience rapide et compacte. Un utilisateur, que qui se trouve sur DocumentDB, présente comme suit :

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

Et donnera un billet :

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Lorsqu’une modification se produit lorsque un des attributs du segment est affecté, il est facile rechercher les documents affectés à l’aide de requêtes qui pointent vers les attributs indexés (sélectionnez * FROM publie p où p.createdBy.id == « edited_user_id ») puis mettez à jour les segments.

## <a name="the-search-box"></a>La zone de recherche

Utilisateurs générera par chance, un grand nombre de contenu. Et nous devons être en mesure de fournir la possibilité de rechercher du contenu qui n’est pas directement dans leurs flux de données de contenu, peut-être parce que nous ne suivez pas les créateurs ou peut-être nous essayons simplement rechercher ancien billet nous l’avons fait 6 mois.

Heureusement, et parce que nous utilisons DocumentDB Azure, nous pouvons facilement implémenter un moteur de recherche à l’aide de [Azure rechercher](https://azure.microsoft.com/services/search/) en quelques minutes et sans avoir à taper une seule ligne de code (autres que visiblement, le processus de recherche et l’interface utilisateur).

Pourquoi est-ce facile ?

Recherche Azure mettent en œuvre, qu’ils appellent [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx), processus en arrière-plan crochet dans vos référentiels de données et State ajouter, mettre à jour ou supprimer vos objets dans l’index. Ils prennent en charge une [base de données SQL Azure indexeurs](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [des objets BLOB Azure indexeurs](../search/search-howto-indexing-azure-blob-storage.md) et Heureusement, [DocumentDB Azure indexeurs](../documentdb/documentdb-search-indexer.md). La transition des informations à partir de DocumentDB à la recherche Azure est simple, en tant que les deux banque d’informations au format JSON, nous devons créer [notre Index](../search/search-create-index-portal.md) et carte quels attributs à partir de notre Documents nous voulons indexées et c’est tout, en quelques minutes (dépend de la taille de nos données), tout le contenu notre seront disponible pour effectuer des recherches, en la meilleure solution de recherche sous forme de Service dans infrastructure cloud. 

Pour plus d’informations sur la recherche Azure, vous pouvez visiter le [Guide d’auto-stoppeur recherche](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>La base de connaissances sous-jacents

Après avoir enregistré tous ces contenus qui se développe et évolue tous les jours, nous pouvons retrouver nous penser : que puis-je faire avec tous les ce flux des informations à partir de mes utilisateurs ?

La réponse est simple : placez-le pour travailler et apprenez à partir de celui-ci.

Mais que pouvons nous apprenons ? Quelques exemples faciles incluent [analyse opinion](https://en.wikipedia.org/wiki/Sentiment_analysis), recommandations contenues basées sur les préférences d’un utilisateur ou même une automatisé modérateur contenu qui garantit que tout le contenu publié par notre réseau social est suffisamment pour la famille.

Maintenant que je reçois vous connecté, vous devez penser vous devez certaines faisant pivoter en sciences mathématiques pour extraire ces modèles et les informations à partir de bases de données simples et des fichiers, mais vous devez être erroné.

L' [Apprentissage automatique azure](https://azure.microsoft.com/services/machine-learning/), partie de la [Suite de Intelligence Cortana](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), est l’un service cloud entièrement gérée qui vous permet de créer des flux de travail à l’aide des algorithmes dans une interface de glisser-déplacer simple, code vos propres algorithmes dans [R](https://en.wikipedia.org/wiki/R_(programming_language)) ou utilisent une partie de la déjà créé et vous êtes prêt à utiliser comme API : [Texte Analytique](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Modérateur contenu](https://www.microsoft.com/moderator) ou [recommandations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Pour obtenir un de ces scénarios d’apprentissage automatique, nous puissions utiliser [Azure données Lake](https://azure.microsoft.com/services/data-lake-store/) pour ajouter les informations de différentes sources et utiliser [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) pour traiter les informations et générer une sortie qui peut être traitée par apprentissage automatique Azure.

Une autre option disponible consiste à utiliser [Microsoft cognitifs Services](https://www.microsoft.com/cognitive-services) pour analyser notre contenu aux utilisateurs ; non seulement pouvons nous comprendre les meilleures (en analysant qu’ils écrivent avec [Texte Analytique API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), mais nous pouvons également détecter le contenu indésirable ou maturité et agir en conséquence avec [L’API Vision ordinateur](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Les Services cognitifs incluent un grand nombre de solutions de prédéfinies qui ne nécessitent pas tous les types de connaissances d’apprentissage automatique à utiliser.

## <a name="conclusion"></a>Conclusion

Cet article tente voile des alternatives de créer des réseaux sociaux complètement sur Azure avec les services de faible coût et de fournir des résultats par encourager l’utilisation d’une distribution stockage multicouches solution et données appelée « Échelle ».

![Diagramme d’interaction entre les services Azure pour des réseaux sociaux](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

La vérité est qu’il n’existe aucune puce argent pour ce type de scénarios, il est synergie créée par la combinaison des services excellents qui nous permettent de créer des expériences exceptionnelles : la vitesse et liberté de DocumentDB Azure pour fournir une application mise en réseau, l’intelligence derrière une solution première classe recherche comme recherche Azure, et la possibilité de Services d’application Azure à l’hôte ne même pas indépendant du langage applications mais processus en arrière-plan puissantes et le stockage Azure extensible et base de données SQL Azure pour stockage des volumes importants de données et la puissance analytique d’apprentissage automatique Azure pour créer des connaissances et intelligence qui peut fournir des commentaires à notre processus et les aider à nous fournir le contenu approprié aux utilisateurs appropriés.

## <a name="next-steps"></a>Étapes suivantes

Plus d’informations sur les données de modélisation en lisant l’article de [la modélisation des données dans DocumentDB](documentdb-modeling-data.md) . Si vous êtes intéressé par d’autres exemples d’utilisation pour DocumentDB, voir [exemples d’utilisation DocumentDB courantes](documentdb-use-cases.md).

Ou en savoir plus sur DocumentDB en suivant le [DocumentDB rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/).
