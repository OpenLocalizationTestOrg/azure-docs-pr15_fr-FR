<properties 
    pageTitle="Exemples d’utilisation courantes DocumentDB | Microsoft Azure" 
    description="En savoir plus sur la partie supérieure cinq exemples d’utilisation pour DocumentDB : du contenu, journalisation des événements, les données du catalogue, données de préférences utilisateur et Internet des objets (IoT) générés par l’utilisateur." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Scénarios d’utilisation DocumentDB courants
Cet article fournit une vue d’ensemble de plusieurs scénarios d’utilisation courants pour DocumentDB.  Les recommandations de cet article servent de point de départ lorsque vous développez votre application avec DocumentDB.   

Lisez cet article, vous serez en mesure de répondre aux questions suivantes : 
 
- Quels sont les scénarios d’utilisation courants pour DocumentDB ?
- Quels sont les avantages de l’utilisation de DocumentDB en tant qu’utilisateur généré banque de contenu ?
- Quels sont les avantages de l’utilisation de DocumentDB en tant que données de catalogue stocker ?
- Quels sont les avantages de l’utilisation de DocumentDB comme un journal des événements stocker ?
- Quels sont les avantages de l’utilisation de DocumentDB comme un utilisateur des données préférences stocker ?
- Quels sont les avantages de l’utilisation de DocumentDB comme base de données pour les systèmes Internet des objets (IoT) ?

## <a name="common-use-cases-for-documentdb"></a>Scénarios d’utilisation courants pour DocumentDB
DocumentDB Azure est une base de données NoSQL objectif général est utilisée dans un large éventail d’applications et de cas d’utilisation. Il est judicieux de n’importe quelle application qui a besoin de temps de réponse ordre des millisecondes faibles et doit pouvoir évoluer rapidement. Voici certains attributs de DocumentDB qui la rendent parfaitement adaptée aux applications High performance.

- DocumentDB partitions en mode natif vos données de disponibilité et extensibilité élevées.
- DocumentDB comprend un stockage SSD sauvegardée avec faible latence ordre des millisecondes réactivité.
- Prise en charge de DocumentDB des niveaux de cohérence comme finale, session et obsolescence délimitée permet de faible coût-à-rapport performances. 
- DocumentDB dispose d’un modèle de tarification compatible avec données flexible qui mètres indépendamment de stockage et débit.
- Débit réservés modèle de DocumentDB vous permet de penser en termes de nombre de lecture/écriture à la place du processeur/mémoire/sorties par du matériel sous-jacent.
- Conception vous permet de DocumentDB de qu'augmenter à volumes importants demande dans l’ordre des milliards de requêtes par jour.

Les attributs sont particulièrement utiles lorsqu’il s’agit dans le site web, mobile, jeux et applications IoT que vous avez besoin de temps de réponse faibles et que vous devez gérer des volumes importants de lecture et d’écriture. 

## <a name="user-generated-content"></a>Contenu générés par l’utilisateur
Un exemple d’utilisation courantes DocumentDB consiste à stocker et utilisateur de requête de contenu (UGC) généré pour le web et les applications mobiles, applications médias sociaux en particulier.  Quelques exemples de UGC sont des sessions de conversation, tweets, billets de blog, évaluations et les commentaires.  Souvent, la UGC dans les applications de médias sociaux est un mélange de texte de forme libre, propriétés, les balises et les relations qui ne sont pas limitées par structure rigide.   

Contenu tels que des salles de conversation, les commentaires et les publications pouvant être stockés dans DocumentDB sans transformations ou objet complexe aux calques mappage relationnel.  Propriétés de données peuvent être ajoutées ou modifiées facilement en fonction des besoins comme les développeurs parcourir le code de l’application, et donc promouvoir le développement rapid.  

Applications qui s’intègrent à différents des réseaux sociaux doivent répondre à l’évolution des schémas à partir de ces réseaux.  Comme données sont automatiquement indexées par défaut dans DocumentDB, les données sont prêtes à être interrogées à tout moment.  Par conséquent, ces applications ont la possibilité de récupérer projections conformément à leurs besoins respectifs.       

La plupart des applications sociales exécuté à l’échelle internationale et peuvent présenter des modèles d’utilisation inattendus.  Flexibilité lors de la mise à l’échelle du magasin de données est essentiel comme nuances pour correspondre à la demande de l’utilisation de la couche d’application.  Vous pouvez évoluer en ajoutant des partitions de données supplémentaires sous un compte DocumentDB.  En outre, vous pouvez également créer des comptes supplémentaires DocumentDB dans plusieurs régions. DocumentDB disponibilité des services région, voir [Régions Azure](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Les données de catalogue
Scénarios d’utilisation de données de catalogue impliquent le stockage et interroger un ensemble d’attributs d’entités telles que les personnes, les emplacements et les produits.  Quelques exemples de données du catalogue sont les comptes d’utilisateurs, catalogues produit, bureaux d’ordre appareil pour IoT et facture des systèmes de supports de cours.  Attributs de ces données peuvent varier et peuvent changer au fil du temps en fonction des besoins de l’application.  

Prenons l’exemple d’un catalogue produit pour un fournisseur de composants véhicule. Chaque partie peut avoir ses propres attributs en plus des attributs courants qui partagent toutes les parties.  En outre, les attributs pour une partie spécifique peuvent changer l’année suivante lorsqu’un nouveau modèle est disponible.  En tant que JSON document magasin, DocumentDB prend en charge des schémas flexibles et vous permet de représenter les données avec des propriétés imbriquées, et il est donc parfaitement pour stocker les données du catalogue.       

## <a name="logging-and-time-series-data"></a>Journalisation et les données de la série chronologique.
Journalisation de l’application est souvent émise dans les volumes importants et peuvent avoir des attributs variables basées sur la version de l’application déployée ou les événements d’enregistrement de composant.  Données du journal ne sont pas délimitées par des relations complexes ou de structures rigides. De plus, les données du journal sont conservées dans format JSON JSON étant légère et facile pour les personnes à lire.
   
Il existe généralement deux cas d’utilisation principales liées aux données du journal des événements.  Le cas d’utilisation première consiste à effectuer des requêtes ad-hoc sur un sous-ensemble de données pour la résolution des problèmes.  Pendant le dépannage, un sous-ensemble de données est d’abord récupéré à partir des journaux, en général en série chronologique.  Ensuite, une exploration des niveaux inférieurs est exécutée en filtrant le jeu de données avec des niveaux d’erreur ou messages d’erreur. Il s’agit de l’emplacement dans lequel stocker les journaux d’événements dans DocumentDB est le des avantages. Données du journal stockées dans DocumentDB sont automatiquement indexées par défaut, et par conséquent, il est prêt à être interrogées à tout moment. En outre, les données du journal peuvent être conservées dans l’ensemble des partitions de données sous forme d’une série chronologique. Anciens journaux peuvent être transférées vers le stockage de froid par votre stratégie de rétention.          

Le deuxième cas d’utilisation implique longues tâches analytique des données effectuées en mode hors connexion sur un grand nombre de données du journal.  Analyse de disponibilité de serveur, analyse des erreurs des applications et analyse de données parcours des exemples de ce cas d’utilisation.  En règle générale, Hadoop est utilisé pour effectuer ces types d’analyse.  Avec le connecteur Hadoop pour DocumentDB, bases de données DocumentDB fonctionneront comme sources de données et récepteurs pour les travaux cochon, Hive et carte/réduire. Pour plus d’informations sur le connecteur Hadoop pour DocumentDB, voir [exécuter une tâche Hadoop avec DocumentDB et HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Jeu
Le niveau de base de données est un composant essentiel des applications de jeu. Les jeux modernes effectuent traitement graphique sur les clients mobiles/console, mais s’appuient sur le cloud pour présenter un contenu personnalisé et personnalisé, tels que les statistiques du jeu, l’intégration des médias sociaux et ajouterons scores élevés. Jeux requièrent extrêmement faible latence lectures écritures à fournir un attrayants expérience du jeu et le niveau de base de données doit gérer les valeurs minimale et maximale des taux de requête lors de lancement de jeu nouveaux et mises à jour de la fonctionnalité.

DocumentDB est utilisé par les jeux de grande échelle comme [le parcours inactive : contexte d’aucune homme](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) par [Jeux suivante](http://www.nextgames.com/), et [Halo 5 : tenir](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Dans les deux cas d’utilisation, principaux avantages de DocumentDB ont été les éléments suivants :

- DocumentDB permet de performance à l’échelle vers le haut ou vers le bas ELASTIQUEMENT. Cela permet de jeux gérer les profil et les statistiques de dizaines à des millions de sonnerie joueurs mettent à jour à l’aide d’un seul appel d’API.
- DocumentDB prend en charge les millisecondes lectures et écrit afin d’éviter les retards pendant le jeu.
- L’indexation automatique de DocumentDB permet le filtrage par rapport à plusieurs différentes propriétés en temps réel, recherchez, par exemple joueurs par leur joueur interne ID, ou leur GameCenter, Facebook, ID de Google ou requête basée sur l’appartenance aux lecteur un guild. Cela est possible sans générer l’indexation complexes ou infrastructure ont.
- Fonctionnalités sociales notamment les messages de conversation dans le jeu, appartenances au lecteur guild, défis terminées, ajouterons scores élevés et graphiques mise en réseau sont plus faciles d’implémenter un schéma flexibles.
- DocumentDB en tant que géré plateforme-comme-a-service (PaaS) requis gestion et configuration minimale travaillent pour autoriser l’itération rapide et réduisent le temps de marché.


## <a name="user-preferences-data"></a>Données de préférences utilisateur
Aujourd'hui, web plus moderne et les applications mobiles sont fournis avec des expériences et des vues complexes. Ces affichages et expériences sont généralement dynamiques, les préférences d’utilisateur ou humeur de cuisine et besoins de personnalisation.  Par conséquent, les applications doivent être en mesure de récupérer efficacement les paramètres personnalisés afin de restituer rapidement des expériences et éléments d’interface utilisateur. 

JSON est un format efficace pour représenter les données de mise en page de l’interface utilisateur tel qu’il n’est pas seulement légère, mais également pouvant être interprété facilement par JavaScript.  DocumentDB propose des niveaux de cohérence ACCORDABLE acceptant lectures rapides avec faible latence écrit. Par conséquent, le stockage de données de mise en page de l’interface utilisateur, y compris des paramètres personnalisés en tant que documents JSON dans DocumentDB est un moyen efficace pour obtenir ces données sur le réseau.

## <a name="iot-and-device-sensor-data"></a>Données capteur IoT et appareil
Exemples d’utilisation IoT partagent fréquemment certains modèles dans comment ils d’acquisition, traitent et stockent des données.  Tout d’abord, ces systèmes permettent pour l’incorporation de données qui peut traiter des paquets de données à partir de capteurs appareil de différents paramètres régionaux.  Ensuite, ces systèmes traitent et analyser des données de diffusion en continu pour tirer des leçons en temps réel. Et enfin, la plupart si ce n’est pas toutes les données est alors finalement placé dans un magasin de données pour ad hoc en mode hors connexion et interrogation analytique.    

Microsoft Azure propose des exemples d’utilisation des services riches qui peuvent être exploitées pour IoT.  Azure IoT services constituent un ensemble de services, notamment Azure événement Hubs, DocumentDB Azure, Azure flux Analytique, Azure Notification concentrateur, apprentissage automatique Azure, Azure HDInsight et PowerBI. 

Rupture de données et peut être transférées par Azure événement centraux que ceux proposés réception de données haut débit avec une faible latence. Données ingérées qui doivent être traité en temps réel aperçu peuvent être utilisé pour garantir qu’à Azure flux Analytique pour analytique en temps réel. Données peuvent être chargées dans DocumentDB pour interroger ad hoc. Une fois que les données chargées DocumentDB, les données sont prêtes à être interrogées.  Les données dans DocumentDB peuvent être utilisées en tant que données de référence dans le cadre d’analytique en temps réel. En outre, données peuvent plus être affinées et traitées par la connexion de données de DocumentDB à HDInsight pour les travaux cochon, Hive ou carte/réduire.  Données abouties sont chargées puis à nouveau sur DocumentDB création de rapports.   

Pour un exemple de solution IoT à l’aide de DocumentDB, EventHubs et vague de, voir les [exemples de hdinsight vague d’espace de stockage sur GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Pour plus d’informations sur les offres Azure pour IoT, voir [créer l’Internet des objets de votre](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx).

## <a name="next-steps"></a>Étapes suivantes
 
Pour commencer à utiliser DocumentDB, vous pouvez créer un [compte](https://azure.microsoft.com/pricing/free-trial/) , puis suivez notre [rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour en savoir plus sur DocumentDB et connaître les informations que nécessaires. 

Ou, si vous voulez en savoir plus sur les clients à l’aide de DocumentDB les témoignages de clients suivants sont disponibles :

- [Jeux suivante](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). L’inactive cannes : jeu N° homme populaire Land soars 1 # pris en charge par DocumentDB Azure.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Comment Halo 5 implémentée jeu mise en réseau à l’aide de DocumentDB Azure.
- [Galerie d’Analytique Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytique galerie - un site communautaire scalable basé sur Azure DocumentDB.
- [Clin de œil](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Les zéros non intégration donne un aperçu de la Global d’entreprises multinationales en Minutes avec les Technologies de Cloud souple.
- [République de news](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Ajout d’intelligence à fournir des informations avec effet de citoyens engagés les actualités. 
- Aéroport [International de groupes de stockage](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Principales marques cohérente des couleurs dans le monde, optez pour groupes de stockage. Et groupes de stockage se transforme en Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Points de suite global Telenor utilise le cloud pour déplacer avec la vitesse d’un démarrage. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Le magasin de l’avenir s’exécute sur recherche rapide et le flux de données simple.
