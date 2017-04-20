<properties
   pageTitle="Quel est le magasin de données SQL Azure ? | Microsoft Azure"
   description="Échelle de l’entreprise distribué capable de traiter les volumes pétaoctet de données relationnelles et non relationnelles de base de données. Il s’agit première cloud data warehouse du secteur avec agrandir, réduire et faites glisser le pointeur en secondes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>Quel est le magasin de données SQL Azure ?

Magasin de données SQL Azure est une base de données sur le nuage, horizontale capable de traiter les volumes importants de données, relationnelles et non relationnelles. Grâce à notre architecture de traitement hautement parallèle (MPP), SQL Data Warehouse peut gérer votre charge de travail d’entreprise.

Data Warehouse SQL :

- Combine la base de données relationnel SQL Server avec capacités de déploiement Azure cloud. Vous pouvez augmenter, réduire, suspendre ou reprendre cluster en secondes. Vous enregistrez les coûts en mise à l’échelle du processeur lorsque vous en avez besoin et réduire l’utilisation des heures non pointe.
- S’appuie sur la plateforme Azure. Il est facile à déployer, conservées en toute transparence et entièrement fault tolérance en raison de sauvegardes automatiques.
- Complète du réseau SQL Server. Vous pouvez développer avec familier SQL Server de Transact-SQL (T-SQL) et les outils.

Cet article décrit les principales fonctionnalités de Data Warehouse SQL.

## <a name="massively-parallel-processing-architecture"></a>Architecture de traitement hautement parallèle

SQL Data Warehouse est qu'un traitement hautement parallèle (MPP) distribué système de base de données. En divisant les données et capacités de traitement entre plusieurs nœuds, SQL Data Warehouse peuvent proposer d’extensibilité élevées considérable - au-delà de n’importe quel système unique.  Les coulisses, SQL Data Warehouse répartit vos données sur de nombreux stockage sans partage et unités de traitement. Les données sont stockées dans le stockage localement redondant Premium et liées pour calculer des nœuds pour l’exécution des requêtes. Avec cette architecture, SQL Data Warehouse propose une approche « diviser et relever » à l’exécution de charges et des requêtes complexes. Demandes sont reçues par le nœud de contrôle, optimisés et puis passés aux nœuds de calcul pour travailler en parallèle.

En combinant architecture MPP et les capacités de stockage Azure, SQL Data Warehouse peut :

- Agrandir ou réduire le stockage indépendamment de calcul.
- Agrandir ou réduire le cluster sans déplacement de données.
- Suspendre la capacité de calcul tout en conservant les données.
- Reprendre la capacité en un clin de calcul.

Le diagramme suivant illustre l’architecture plus en détail.

![Architecture de magasin de données SQL][1]


**Nœud contrôle :** Le nœud contrôle gère et optimise les requêtes. Il est le serveur frontal interagit avec toutes les applications et les connexions. Dans SQL Data Warehouse, le nœud contrôle est optimisé par base de données SQL et y connecter recherche et estime la même. Sous la surface, le nœud contrôle de coordonnées toutes le déplacement de données et certains calculs requis pour exécuter des requêtes en parallèle sur vos données distribuées. Lorsque vous envoyez une requête T-SQL pour SQL Data Warehouse, le nœud contrôle transforme en requêtes distinctes qui s’exécutent sur chaque nœud de calcul en parallèle.

**Nœuds de calcul :** Les nœuds de calcul servent de la puissance de Data Warehouse SQL. Il s’agit des bases de données SQL qui stockent vos données et traiter votre requête. Lorsque vous ajoutez des données, Data Warehouse SQL distribue les lignes à votre nœuds de calcul. Les nœuds de calcul sont les travailleurs qui s’exécutent les requêtes parallèles sur vos données. Après le traitement, ils transmettent les résultats vers le nœud de contrôle. Pour terminer la requête, le nœud contrôle regroupe les résultats et retourne le résultat final.

**Stockage :** Vos données sont stockées dans le stockage Blob Azure. Lorsque les nœuds de calcul interagissent avec vos données, ils écrire et lire directement vers et depuis le stockage blob. Dans la mesure où stockage Azure développe considérablement et en toute transparence, SQL Data Warehouse peut faire de même. Dans la mesure où informatiques et stockage sont indépendants, SQL Data Warehouse peut ajuster automatiquement stockage séparément de mise à l’échelle cluster et vice versa. Stockage d’objets Blob Azure est également entièrement tolérance de panne et simplifie le processus de sauvegarde et de restauration.

**Service de déplacement de données :** Service de mouvement de données (DMS) déplace les données entre les nœuds. DMS donne l’accès de nœuds cluster à dont elles ont besoin pour les jointures et les agrégations de données. DMS n’est pas un service Azure. Il est un service Windows qui s’exécute à côté de la base de données SQL sur tous les nœuds. Dans la mesure où DMS s’exécute en arrière-plan, vous n’interagir avec elle directement. Toutefois, lorsque vous examinez les plans de requête, vous remarquerez qu’ils comprennent certaines opérations DMS puisque déplacement de données est nécessaire pour exécuter chaque requête en parallèle.


## <a name="optimized-for-data-warehouse-workloads"></a>Optimisée pour les charges de magasin de données

L’approche MPP est facilitée par un nombre de data warehouse optimisations de performances spécifiques, y compris :

- Un optimiseur de requêtes distribuées et un jeu de calcul complexes au sein des données. À l’aide d’informations sur la répartition et la taille des données, le service est en mesure d’optimiser les requêtes par évaluer le coût des opérations de requête distribué spécifique.

- Algorithmes avancés et techniques intégrés dans le processus de déplacement de données pour déplacer efficacement des données entre les ressources informatiques nécessaire pour exécuter la requête. Ces opérations de déplacement de données sont intégrées, et toutes les optimisations au Service de déplacement de données s’exécutent automatiquement.

- Groupé **columnstore** index par défaut. En utilisant le stockage en colonnes, SQL Data Warehouse obtient en moyenne 5 gains de compression x par rapport au stockage en ligne traditionnel et jusqu'à 10 x ou plus performant de requête. Requêtes Analytique qui doivent analyser un grand nombre de lignes travail excellent columnstore à partir d’index.


## <a name="predictable-and-scalable-performance"></a>Performances prévisibles et scalable

Data Warehouse SQL sépare stockage et calcul, qui permet à chaque mise à l’échelle séparément. Data Warehouse SQL peut évoluer rapidement et simplement pour ajouter des ressources supplémentaires cluster en un clin de. Compléter cela est l’utilisation du stockage Blob Azure. Des objets BLOB prévoient stockage répliqué stable, mais également l’infrastructure d’extension sans effort à faible coût. À l’aide de cette combinaison de stockage cloud à l’échelle et cluster Azure, SQL Data Warehouse vous permet de payer pour le stockage et les performances des requêtes quand vous en avez besoin. Modifier la quantité de calcul est aussi simple que le curseur dans le portail Azure vers la gauche ou la droite, ou elle peut également être planifiée à l’aide de T-SQL et PowerShell.

Ainsi que la possibilité de contrôler totalement la quantité de cluster indépendamment du stockage, SQL Data Warehouse vous permet en pause entièrement votre data warehouse, ce qui signifie que vous ne pas payer pour le cluster lorsque vous n’avez pas besoin. Tout en poursuivant votre espace de stockage en place, cluster tous les est publié au pool principale d’Azure, vous épargner. Lorsque cela est nécessaire, il vous suffit reprendre le cluster et que vos données et calculer disponibles pour votre charge de travail.

## <a name="data-warehouse-units"></a>Unités de magasin de données

Allocation des ressources à votre Data Warehouse SQL est mesurée en unités de magasin de données (DWUs). DWUs sont une mesure de ressources sous-jacentes comme processeur, mémoire, e/s, qui sont attribuées à votre Data Warehouse SQL. Augmenter le nombre de DWUs accroît ressources et des performances. Plus précisément, DWUs vous assurer que :

- Vous ne pouvez mettre à l’échelle votre data warehouse facilement, sans vous soucier de matériel ou du logiciel sous-jacente.

- Vous pouvez prévoir amélioration des performances pour un niveau DWU avant de modifier la taille de votre data warehouse.

- La configuration matérielle et logicielle de votre instance sous-jacente pour modifier ou déplacer sans affecter les performances de votre charge de travail.

- Microsoft peut apporter des corrections à l’architecture sous-jacente du service sans affecter les performances de votre charge de travail.

- Microsoft peut rapidement améliorer les performances dans SQL Data Warehouse, d’une manière qui s’adapte et les effets de manière égale le système.

Données Warehouse unités fournissent une mesure des trois mesures précises qui vous sont très associés data warehouse performances de charge de travail. L’objectif est que les mesures de la charge de travail clés suivantes proportionnelles avec DWUs que vous avez choisi pour votre data warehouse.

**Analyse/agrégation :** Cette métrique la charge de travail est un standard de data warehouse requête qui analyse un grand nombre de lignes, puis effectue une agrégation complexe. Il s’agit d’une opération intensive e/s et du processeur.

**Charge :** Cette métrique mesure la capacité pour ajouter des données dans le service. Charges sont effectuées avec PolyBase chargement d’un jeu de données représentatif depuis le stockage Blob Azure. Cette métrique est conçu pour contrainte réseau et aspects UC du service.

**Créer la Table comme sélectionner (SACT) :** SACT mesure la capacité pour copier une table. Cela implique la lecture de données à partir du stockage, distribution entre les nœuds du matériel et enregistrer à nouveau sur le stockage. Il est une opération intensive du processeur, IO et le réseau.

## <a name="pause-and-scale-on-demand"></a>Pause et l’échelle à la demande

Lorsque vous avez besoin plus rapidement des résultats, augmenter votre DWUs et payer pour de meilleures performances. Lorsque vous avez besoin moins calculer power, réduire votre DWUs et payer uniquement pour ce dont vous avez besoin. Vous pourriez penser sur la modification de votre DWUs dans ces scénarios :

- Lorsque vous n’avez pas besoin exécuter des requêtes, peut-être dans le soir ou week-end suspendre vos requêtes. Puis mettez en pause vos ressources cluster pour éviter de payer pour DWUs lorsque vous n’en avez pas besoin.

- Lorsque votre système possède basse à la demande, envisagez de réduire DWU à une taille réduite. Vous pouvez toujours accéder aux données, mais à des économies.

- Lorsque vous effectuez une opération de chargement ou de transformation de données dense, vous voudrez peut-être évoluer afin que vos données sont disponibles plus rapidement.

Pour mieux comprendre quelle est la valeur de votre DWU idéale, essayez mise à l’échelle monter et Descendre et en cours d’exécution des requêtes quelques après le chargement de vos données. Mise à l’échelle étant rapide, vous pouvez essayer un nombre de différents niveaux de performance en une heure ou moins.  Gardez à l’esprit que SQL Data Warehouse est conçu pour traiter les grandes quantités de données et pour afficher ses capacités trues pour suivre l’évolution, en particulier au niveau des plus grande échelles que nous proposons, vous souhaiterez utiliser un jeu de données volumineux qui approche ou dépasse 1 To.


## <a name="built-on-sql-server"></a>Basé sur SQL Server

SQL Data Warehouse est basé sur le moteur de base de données relationnelles SQL Server et comprend de nombreuses fonctionnalités que vous attendez d’un data warehouse d’entreprise. Si vous connaissez déjà T-SQL, il est facile transférer vos connaissances sur SQL Data Warehouse. Si vous sont avancées ou venez juste de commencer, les exemples dans la documentation aidera à commencer. En général, vous pouvez envisager sur la façon dont nous avons construite les éléments de langage de Data Warehouse SQL comme suit :

- Data Warehouse SQL utilise la syntaxe T-SQL pour de nombreuses opérations. Il prend également en charge un large éventail de traditionnels constructions SQL, tels que des procédures stockées, fonctions définies par l’utilisateur, table partition, index et classements.

- SQL Data Warehouse contient également un certain nombre de nouvelles fonctionnalités de SQL Server, y compris : groupé **columnstore** index PolyBase et l’intégration des données d’audit (complète avec l’évaluation des menaces).

- Certains éléments de langage T-SQL qui sont moins courante pour data warehouse charges de travail, ou plus récent à SQL Server, n’est peut-être pas disponibles actuellement. Pour plus d’informations, consultez la [documentation de Migration][].

Avec les composants Transact-SQL et fonctionnalité communs entre SQL Server, Data Warehouse SQL, base de données SQL et Analytique plateforme système, vous pouvez développer une solution adaptée à vos besoins en matière de données. Vous pouvez choisir l’emplacement conserver vos données, en fonction de performances, la sécurité et exigences relatives à l’échelle, puis le transférer données comme bon vous semble entre différents systèmes.

## <a name="data-protection"></a>Protection des données

SQL Data Warehouse stocke toutes les données dans le stockage localement redondant Azure Premium. Plusieurs copies synchrones des données sont conservées dans le centre de données locales afin de garantir la protection des données transparente en cas d’échecs localisés. En outre, SQL Data Warehouse sauvegarde automatiquement vos bases de données actives (reprises) à intervalles réguliers à l’aide d’instantanés de stockage Azure. Pour en savoir plus sur la sauvegarde et restauration fonctionne, consultez la [vue d’ensemble de sauvegarde et de restauration][].

## <a name="integrated-with-microsoft-tools"></a>Intégrée aux outils de Microsoft

SQL Data Warehouse s’intègre également la plupart des outils que les utilisateurs peuvent être familiarisés avec SQL Server. Parmi lesquels :

**Outils traditionnel SQL Server :** Data Warehouse SQL est totalement intégré avec SQL Server Analysis Services, Integration Services et Reporting Services.

**Outils basés sur le cloud :** Data Warehouse SQL peut être utilisé en parallèle avec un nombre de nouveaux outils dans Azure, notamment Data Factory, flux Analytique, apprentissage automatique et Power BI. Pour obtenir une liste plus complète, voir [vue d’ensemble des outils intégrés][].

**Outils tiers :** Un grand nombre de fournisseurs tiers outil ont certifiés l’intégration de leurs outils avec SQL Data Warehouse. Pour obtenir la liste complète, voir [partenaires solution SQL Data Warehouse][].

## <a name="hybrid-data-sources-scenarios"></a>Scénarios de sources de données hybride

À l’aide de Data Warehouse SQL avec PolyBase utilisateurs sans précédent permet de déplacer des données entre leur réseau, déverrouillage la possibilité de scénarios avancées hybride avec non relationnelles d’installation et sources de données locales.

Polybase vous permet d’exploiter les données provenant de différentes sources à l’aide des commandes familières T-SQL. Polybase vous permet de consulter les données non relationnelles contenus dans le stockage Blob Azure comme s’il s’agit d’un tableau normal. Utilisez Polybase pour interroger des données non relationnelles, ou pour importer des données non relationnelles dans Data Warehouse SQL.

- PolyBase utilise des tables externes pour accéder aux données non relationnelles. Les définitions de table sont stockées dans Data Warehouse SQL et vous pouvez y accéder à l’aide de SQL et outils tels que vous voulez accéder aux données relationnelles normales.

- Polybase est indépendante dans son intégration. Il présente les mêmes fonctionnalités et fonctionnalités à toutes les sources pris en charge. Les données lues par Polybase peuvent se trouver dans une variété de formats, notamment des fichiers délimités ou fichiers ORC.

- PolyBase peut être utilisé pour accéder au stockage blob qui est également utilisé comme espace de stockage pour un cluster HDInsight. Cela permet d’accéder aux mêmes données avec des outils relationnelles et non relationnelles.

## <a name="sla"></a>SLA

SQL Data Warehouse propose un produit niveau de service accord niveau () dans le cadre de Microsoft Online Services SLA. Pour plus d’informations, consultez [SLA pour Data Warehouse SQL][]. Pour plus d’informations SLA sur tous les autres produits, vous pouvez visiter le Azure [Contrats de niveau de Service] de page ou les télécharger dans la page [Licences en Volume][] . 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez un peu Data Warehouse SQL, découvrez comment rapidement [créer un Data Warehouse SQL][] et [charger les exemples de données][]. Si vous débutez dans Azure, vous trouverez peut-être le [Glossaire Azure][] intéressantes ceux que vous pouvez rencontrer la terminologie nouvelle. Ou bien, jetez un coup de œil à certaines de ces autres ressources de magasin de données SQL.  

- [Témoignages de clients]
- [Blogs]
- [Demandes de fonctionnalités]
- [Vidéos]
- [Blogs de l’équipe consultatif client]
- [Créer des tickets de support]
- [Forum MSDN]
- [Forum de dépassement de capacité de pile]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Créer des tickets de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[charger les exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[créer un Data Warehouse SQL]: ./sql-data-warehouse-get-started-provision.md
[Documentation de migration]: ./sql-data-warehouse-overview-migrate.md
[Partenaires solution SQL Data Warehouse]: ./sql-data-warehouse-partner-business-intelligence.md
[Vue d’ensemble des outils intégrés]: ./sql-data-warehouse-overview-integrate.md
[Vue d’ensemble de sauvegarde et de restauration]: ./sql-data-warehouse-restore-database-overview.md
[Glossaire Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Témoignages de clients]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe consultatif client]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum de dépassement de capacité de pile]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA pour Data Warehouse SQL]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Licence en volume]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contrats de niveau de service]: https://azure.microsoft.com/en-us/support/legal/sla/
