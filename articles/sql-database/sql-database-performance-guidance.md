<properties
    pageTitle="Base de données SQL Azure et de performances des bases de données unique | Microsoft Azure"
    description="Cet article peut vous aider à déterminer le niveau de service à sélectionner pour votre application. Il recommande également des solutions d’optimisation de votre application pour tirer le meilleur parti de base de données SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Base de données SQL Azure et de performances des bases de données unique

Base de données SQL Azure propose trois [niveaux de service](sql-database-service-tiers.md): basique, Standard et Premium. Chaque niveau de service isole strictement les ressources que votre base de données SQL peut utiliser et garantit des performances prévisibles pour ce niveau de service. Dans cet article, nous proposons des conseils qui peuvent vous aider à choisir le niveau de service pour votre application. Abordent également méthodes que vous pouvez régler votre application pour tirer le meilleur parti de base de données SQL Azure.

>[AZURE.NOTE] Cet article traite des conseils pour les performances des bases de données unique dans une base de données SQL Azure. Pour des instructions de performances liées à des pools élastique de base de données, voir [Considérations prix et des performances pour des pools élastique de base de données](sql-database-elastic-pool-guidance.md). Notez, cependant, que vous pouvez la plupart des recommandations réglage dans cet article s’appliquent aux bases de données dans un pool de base de données élastique et vos performances similaires.

Il s’agit des trois niveaux de service de base de données SQL Azure que vous avez le choix entre (performances sont mesuré en unités de débit de base de données ou [DTUs](sql-database-what-is-a-dtu.md):

- **Base**. Le service base couche offres bonnes performances prévisibilité pour chaque base de données, d’heure sur heure. Dans une base de données de base, des ressources suffisantes prend en charge les bonnes performances dans une base de données petite n’a pas plusieurs demandes simultanées.
- **Standard**. Le niveau de service Standard offre prévisibilité améliorer les performances et génère la barre des bases de données qui ont plusieurs demandes simultanées, comme les applications web et de groupe de travail. Lorsque vous choisissez une base de données de niveau de service Standard, vous pouvez redimensionner votre application de base de données en fonction des performances prévisibles, minute sur minute.
- **Premium**. Le niveau de service Premium fournit des performances prévisibles, deuxième sur ensuite, pour chaque base de données Premium. Lorsque vous choisissez le niveau de service Premium, vous pouvez dimensionner votre application de base de données en fonction de la charge maximale de cette base de données. Le plan supprime les performances variation peut entraîner des requêtes petites pour durer plus longtemps que prévu dans les opérations sensibles à la latence des cas. Ce modèle peut simplifier considérablement les cycles de validation de développement et de produit pour les applications que vous avez besoin de faire des observations fortes sur les besoins en ressources pointe, variation de performances ou la latence des requêtes.

À chaque niveau de service, vous définissez le niveau de performance, pour que vous avez la possibilité de payer uniquement pour la capacité que nécessaire. Vous pouvez [Ajuster la capacité](sql-database-scale-up.md), vers le haut ou vers le bas, en tant que les modifications de la charge de travail. Par exemple, si votre charge de travail de base de données est élevé au cours de la saison de courses retour à l’école, vous pourriez augmenter le niveau de performance pour la base de données pendant une période définie, juillet à septembre. Vous pouvez le réduire votre saison fin. Vous pouvez réduire ce que vous payez en optimisant votre environnement cloud pour la saisonnalité de votre entreprise. Ce modèle fonctionne également bien pour les cycles de lancement du produit logiciel. Une équipe de test peut allouer capacité pendant l’exécution de test et puis relâchez cette capacité lorsqu’ils ont fini de test. Dans un modèle de demande de capacité, vous payez pour la capacité que vous en avez besoin et évitez de perdre sur les ressources dédiées que vous utilisez rarement.

## <a name="why-service-tiers"></a>Pourquoi les niveaux de service ?

Bien que chaque charge de travail de base de données peut différer, l’objectif de niveaux de service est d’assurer la prévisibilité performances à différents niveaux de performances. Clients ayant des besoins en ressources de base de données à grande échelle peuvent travailler dans un environnement informatique plus dédié.

### <a name="common-service-tier-use-cases"></a>Exemples d’utilisation de niveau de service courantes

#### <a name="basic"></a>Base

- **Vous venez juste de commencer avec la base de données SQL Azure**. Les applications qui sont en développement souvent n’est pas nécessaire de niveaux de haute performance. Bases de données de base sont un environnement idéal pour le développement de base de données, à un prix faible.
- **Vous avez une base de données avec un seul utilisateur**. Applications qui associer un seul utilisateur à une base de données en général ne possèdent des exigences de performances et accès concurrentiels au haut. Ces applications sont candidats pour le niveau de service de base.

#### <a name="standard"></a>Standard

- **Votre base de données implique plusieurs demandes simultanées**. Les applications de service à plusieurs utilisateurs à la fois généralement doivent performances supérieures. Par exemple, les sites Web obtenir le trafic modéré ou par département applications qui nécessitent davantage de ressources sont adaptés pour le niveau de service Standard.

#### <a name="premium"></a>Premium

La plupart des cas d’utilisation du niveau de service Premium ont une ou plusieurs de ces caractéristiques :

- **Charger pointe haute**. Une application nécessitant un grand nombre de processeur, de mémoire ou sorties (e/s) d’achever ses opérations nécessite un niveau dédié et hautes performances. Par exemple, une opération de base de données appelée consommer plusieurs cœurs d’UC pendant une période prolongée est un candidat pour le niveau de service Premium.
- De **nombreuses demandes simultanées**. Certaines applications de base de données service nombreuses demandes simultanées, par exemple, lorsque servant d’un site Web qui comporte un volume de trafic élevé. Base et Standard niveaux de service limite le nombre de demandes simultanées par base de données. Applications qui exigent des connexions plus nécessaires pour choisir la taille de réservation appropriée pour gérer le nombre maximal de demandes nécessaires.
- **Latence faible**. Certaines applications doivent garantir une réponse à partir de la base de données dans un minimum de temps. Si une procédure stockée spécifique est appelée dans le cadre d’une opération de client plus large, vous pouvez avoir une obligation d’avoir un retour de cet appel dans pas plus de 20 millisecondes, 99 % du temps. Avantages de ce type d’application à partir du niveau de service Premium, pour vous assurer que les ressources informatiques requis sont disponible.

Le niveau de service dont vous avez besoin pour votre base de données SQL dépend des exigences de charge pointe pour chaque dimension ressource. Certaines applications utilisent une quantité importante d’une ressource unique, mais ont des exigences significatives pour d’autres ressources.

## <a name="service-tier-capabilities-and-limits"></a>Spécifications et limites des fonctionnalités de niveau de Service
Chaque niveau de niveau et les performances de service est associé à différentes limites et performances. Le tableau suivant décrit ces caractéristiques pour une seule base de données.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

Les sections suivantes ont plus d’informations sur l’affichage Utilisation associée à ces limites.

### <a name="maximum-in-memory-oltp-storage"></a>Stockage OLTP en mémoire maximale

Vous pouvez utiliser l’affichage **sys.dm_db_resource_stats** pour surveiller l’utilisation de stockage Azure en mémoire. Pour plus d’informations sur le contrôle, voir [le stockage moniteur en mémoire OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Pour l’instant, traitement preview (OLTP) des transactions en ligne Azure en mémoire sont prise en charge uniquement pour les bases de données unique. Vous ne pouvez pas l’utiliser dans les bases de données dans des pools élastique de base de données.

### <a name="maximum-concurrent-requests"></a>Nombre maximal de requêtes simultané

Pour afficher le nombre de demandes simultanées, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Pour analyser la charge de travail d’une base de données SQL Server en local, modifier cette requête pour filtrer sur la base de données que vous voulez analyser. Par exemple, si vous avez une base de données locale nommée MyDatabase, cette requête Transact-SQL renvoie le nombre de demandes simultanées dans cette base de données :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Il s’agit simplement un instantané à un moment unique dans le temps. Pour obtenir une meilleure compréhension de votre charge de travail en matière de demandes simultanées, vous devez recueillir les nombreux exemples au fil du temps.

### <a name="maximum-concurrent-logins"></a>Connexions simultanées maximales

Vous pouvez analyser vos modèles utilisateur et d’application pour avoir une idée de la fréquence des connexions. Vous pouvez également exécuter charges réelles dans un environnement de test pour vérifier que vous rencontrez pas cela ou autres limites qu'abordent dans cet article. Il n’est pas une requête unique ou de vue de gestion dynamique (vue) qui peut afficher simultanément compte de connexion ou de l’historique.

Si plusieurs clients utilisent la même chaîne de connexion, le service authentifie chaque connexion. Si 10 utilisateurs se connectent simultanément à une base de données en utilisant les mêmes nom d’utilisateur et mot de passe, il serait 10 connexions simultanées. Cette limite s’applique uniquement à la durée de la connexion et d’authentification. Si les mêmes 10 utilisateurs se connectent à la base de données dans un ordre séquentiel, le nombre de connexions simultanées soit jamais supérieur à 1.

>[AZURE.NOTE] Pour l’instant, cette limite ne s’applique pas aux bases de données dans des pools élastique de base de données.

### <a name="maximum-sessions"></a>Nombre maximal de sessions

Pour afficher le nombre de sessions actives en cours, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si vous êtes analyse une charge de travail de SQL Server en local, modifiez la requête pour vous concentrer sur une base de données. Cette requête vous permet de déterminer les besoins en matière possibles d’une session de la base de données si vous envisagez de déplacer à la base de données SQL Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Là encore, ces requêtes renvoient un compteur de point dans le temps. Si vous collectez plusieurs exemples au fil du temps, vous aurez la meilleure connaissance de votre session utiliser.

Pour l’analyse de la base de données SQL, vous pouvez obtenir les statistiques d’historique sur les sessions. Interrogez **sys.resource_stats**et utilisez la colonne **active_session_count** . Consultez la section suivante pour plus d’informations sur l’utilisation de cet affichage.

## <a name="monitor-resource-use"></a>Surveiller l’utilisation des ressources
Deux vues peuvent vous aider à surveiller l’utilisation des ressources pour une base de données SQL par rapport à son niveau de service :

- [Sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>Sys.dm_db_resource_stats
Vous pouvez utiliser l’affichage [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans chaque base de données SQL. La vue **sys.dm_db_resource_stats** contient des données d’utilisation ressource récentes par rapport au niveau de service. Moyennes pourcentages pour processeur, données e/s, journal des écritures et la mémoire sont enregistrées toutes les 15 secondes et sont conservées pendant 1 heure.

Étant donné que cet affichage fournit un aperçu plus précis de l’utilisation des ressources, utiliser **sys.dm_db_resource_stats** première pour une analyse de l’état actuel et de dépannage. Par exemple, cette requête montre l’utilisation des ressources moyen et maximal pour la base de données en cours sur la dernière heure :

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Pour les autres requêtes, voir les exemples dans [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>Sys.resource_stats

La vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) dans la base de données **principale** comporte des informations supplémentaires qui peuvent vous aider à analyser les performances de votre base de données SQL à son niveau de performances et de niveau de service spécifique. Les données sont collectées toutes les 5 minutes et conservées pendant environ 35 jours. Cet affichage est utile pour une analyse historique à long terme la manière dont votre base de données SQL utilise des ressources.

Le graphique suivant montre l’utilisation des ressources du processeur pour une base de données Premium avec le niveau de performance P2 pour chaque heure dans une semaine. Ce graphique démarre le lundi, montre 5 jours de travail et un week-end, lors de la moins importante se passe lors de l’application.

![Utilisation des ressources de base de données SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

À partir des données, cette base de données a actuellement une charge de l’UC pointe de simplement plus de 50 % d’utilisation du processeur par rapport au niveau de performance P2 (MIDI mardi). Si UC est le facteur principal dans le profil de ressources de l’application, vous pouvez décider que P2 est le niveau de performance droit afin de garantir que la charge de travail tienne toujours. Si vous prévoyez d’une application à évoluer au fil du temps, il est recommandé d’avoir un tampon ressource supplémentaire afin que l’application ne jamais atteint la limite de niveau de performance. Si vous augmentez le niveau de performance, vous pouvez éviter les erreurs client visible qui peuvent se produire lors d’une base de données n’a pas assez de puissance pour traiter les demandes de façon efficace, notamment dans les environnements sensibles à la latence. Un exemple est une base de données qui prend en charge d’une application qui peint des pages Web basées sur les résultats des appels de base de données.

Notez que d’autres types d’applications peuvent interpréter le même graphique différemment. Par exemple, si une application tente de traiter les données de paie chaque jour et a le même graphique, ce type de modèle « par lots » peut effectuer correctement à un niveau de performance P1. Le niveau de performance P1 a 100 DTUs par rapport à 200 DTUs au niveau de performance P2. Le niveau de performance P1 fournit la moitié les performances du niveau de performance P2. Par conséquent, 50 % de l’utilisation de l’UC dans P2 est égal à utilisation de l’UC à 100 pour cent dans P1. Si l’application n’a pas de délais d’expiration, il peut n’a aucune importance si une tâche dure 2 ou 2,5 heures pour terminer, s’il est traité aujourd'hui. Une application dans cette catégorie probablement peut utiliser un niveau de performance P1. Vous pouvez tirer parti du fait qu’il existe des périodes de temps pendant la journée lorsque l’utilisation des ressources est inférieure, afin que les « pointe volumineux » peut déborde sur dans un du creux plus tard dans la journée. Le niveau de performance P1 peut-être être bon pour ce type d’application (et économies), dans la mesure où les tâches peuvent se terminer dans les temps chaque jour.

Expose de base de données SQL Azure consommées des informations sur la ressource pour chaque base de données active dans la vue **sys.resource_stats** de la base de données **principale** dans chaque serveur. Les données du tableau sont agrégées pour intervalle de 5 minutes. Avec les niveaux de service Basique, Standard et Premium, les données peuvent prendre plus de 5 minutes pour apparaître dans la table, afin que ces données ne sont plus utiles pour l’analyse de l’historique au lieu d’analyse près en temps réel. Interrogez la vue **sys.resource_stats** pour afficher l’historique d’une base de données récente et vérifie si la réservation que vous avez choisi remis les performances souhaitée lorsque cela est nécessaire.

>[AZURE.NOTE] Vous devez être connecté à la base de données **principale** de votre serveur de base de données SQL logique pour interroger **sys.resource_stats** dans les exemples suivants.

Cet exemple montre comment les données dans cet affichage sont exposées :

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vue de catalogue sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L’exemple suivant vous montre différentes manières que vous pouvez utiliser la vue de catalogue **sys.resource_stats** pour obtenir des informations sur l’utilisation des ressources dans votre base de données SQL :

1. Pour examiner les ressources de la semaine dernière utiliser pour l’userdb1 de base de données, vous pouvez exécuter cette requête :

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. Pour évaluer la façon dont votre charge de travail s’adapte le niveau de performance, vous devez extraire vers le bas vers chaque aspect des mesures de ressources : UC, lectures, écritures, nombre de collaborateurs et nombre de sessions. Voici une requête révisée à l’aide de **sys.resource_stats** pour signaler les valeurs maximales et moyennes de ces métriques ressource :

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Grâce à ces informations sur les valeurs maximale et moyennes de chaque métrique ressource, vous pouvez évaluer degré votre charge de travail s’intègre le niveau de performance que vous avez choisi. En règle générale, moyenne de la valeur à partir de **sys.resource_stats** vous offre une bonne référence à utiliser par rapport à la taille cible. Il doit être votre clé primaire mesure. Pour obtenir un exemple, vous pouvez utiliser le niveau de service Standard avec le niveau de performance S2. La moyenne utiliser des pourcentages du processeur et e/s lectures et écritures sont au-dessous de 40 %, le nombre moyen de travailleurs est inférieur à 50 et le nombre moyen de sessions est inférieure à 200. Votre charge de travail peut-être être intégrées dans le niveau de performance S1. Il est facile de voir si votre base de données s’adapte dans les limites de travail et la session. Pour voir si une base de données s’adapte à un niveau de performances inférieur en ce qui concerne les UC, lit et écrit, divisez le nombre DTU du niveau inférieur performances par le nombre DTU de votre niveau de performance actuelle et puis multiplie le résultat par 100 :

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 **

    Le résultat est la différence de performances relatives entre les niveaux de deux performance sous forme de pourcentage. Si votre utilisation des ressources ne dépasse pas ce montant, votre charge de travail peut-être être intégrées dans le niveau de performance inférieur. Toutefois, vous devez examiner toutes les plages d’utiliser des valeurs de ressource et déterminer, en pourcentage, la fréquence à laquelle votre charge de travail de base de données serait adapte-t-il le niveau de performance inférieur. La requête suivante renvoie le pourcentage d’ajustement par dimension de ressource, selon le seuil de 40 % que nous calculées dans cet exemple :

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    En fonction de votre objectif de niveau de service de base de données (ralenti), vous pouvez décider si votre charge de travail s’intègre le niveau de performance inférieur. Si votre charge de travail de base de données ralenti est 99,9 % et que la requête précédente renvoie les valeurs supérieures à 99,9 % pour toutes les dimensions de trois ressources, votre charge de travail susceptible de promotion dans le niveau de performance inférieur.

    Consultant le pourcentage d’ajustement également vous donne un aperçu en vous disposiez atteindre un niveau plus élevé de performances avancé pour répondre à vos ralenti. Par exemple, userdb1 montre l’utilisation de l’UC suivante pour la semaine précédente :

  	| Pourcentage du processeur moyen | Pourcentage processeur maximal |
  	|---|---|
  	| 24,5 | 100,00 |

    Moyenne du processeur est sur un quart de la limite du niveau de performance, qui serait rentrent pas dans le niveau de performance de la base de données. Toutefois, la valeur maximale indique que la base de données atteint la limite du niveau de performance. Vous voulez déplacer vers le niveau de performance supérieur suivant ? Vous devez examiner comment autant de fois votre atteint la charge de travail à 100 % et comparer à votre charge de travail de base de données ralenti.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Si cette requête renvoie une valeur inférieure à 99,9 % pour une des dimensions trois ressources, envisagez de soit déplacer vers le niveau de performance supérieur suivant ou utiliser des techniques de réglage des applications pour réduire la charge sur la base de données SQL.

4. Cet exercice considère également votre augmentation de la charge de travail projeté à l’avenir.

## <a name="tune-your-application"></a>Régler votre application

Dans traditionnel SQL Server local, les processus de planification de la capacité initiale est souvent séparé à partir du processus de l’exécution d’une application en production. Matériel et produit des licences achetées tout d’abord, et optimisation des performances s’effectue par la suite. Lorsque vous utilisez la base de données SQL Azure, il est judicieux de sélectionner interweave le processus d’une application en cours d’exécution et paramétrage. Avec le modèle de capacité à la demande d’un abonnement payant, vous pouvez régler votre application pour utiliser les ressources minimales maintenant, au lieu d’overprovisioning sur matériel en fonction des propositions de prévisions de croissance pour une application, qui sont souvent incorrectes. Certains clients peuvent choisir de ne pas régler une application, puis choisissez à la place d’overprovision ressources matérielles. Cette approche peut s’avérer utile si vous ne voulez pas modifier une application clé pendant une période occupé (e). Mais, adapter une application pouvez réduire besoins en ressources et échéances mensuelles inférieurs lorsque vous utilisez les niveaux de service dans la base de données SQL Azure.

### <a name="application-characteristics"></a>Caractéristiques de l’application

Bien que les niveaux de service de base de données SQL Azure est conçues pour améliorer les performances stabilité et prévisibilité pour une application, certaines meilleures pratiques peuvent vous aider à optimiser votre application pour mieux tirer parti des ressources à un niveau de performances. Bien que de nombreuses applications ont des gains de performances significatifs simplement en optant pour un niveau de performances plus élevé ou niveau de service, certaines applications ont besoin de réglage supplémentaire pour bénéficier d’un niveau plus élevé du service. Pour améliorer les performances, vous pouvez réglage de l’application supplémentaire pour les applications qui présentent ces caractéristiques :

- **Applications auxquelles une baisse des performances en raison de comportement « amené »**. Applications amenées effectuer des opérations d’accès trop de données sont sensibles à la latence du réseau. Vous devrez peut-être modifier ces types d’applications pour réduire le nombre d’opérations d’accès aux données à la base de données SQL. Par exemple, vous pouvez améliorer les performances de l’application à l’aide de techniques telles que le traitement par lots de requêtes ad-hoc ou en déplaçant les requêtes à des procédures stockées. Pour plus d’informations, voir [requêtes par lots](#batch-queries).
- **Bases de données avec une charge de travail intensive qui ne peut pas être pris en charge par un seul ordinateur entière**. Bases de données qui dépassent les ressources de la plus élevée performances Premium peuvent bénéficier de mise à l’échelle la charge de travail. Pour plus d’informations, voir [bases de données croisées ont](#cross-database-sharding) et [fonctionnels partition](#functional-partitioning).
- **Applications qui ont des requêtes non optimaux**. Applications, notamment celles de la couche d’accès aux données, avez réglé mal les requêtes peuvent ne pas bénéficient d’un niveau de performance supérieur. Cela inclut les requêtes qui ne disposent pas d’une clause WHERE, ont index manquants ou déjà obsolètes statistiques. Ces applications bénéficient des techniques d’optimisation des performances des requêtes standard. Pour plus d’informations, voir [index manquant](#missing-indexes) et [requête optimisation et repères](#query-tuning-and-hinting).
- **Création d’accéder aux applications qui contiennent des données non optimaux**. Applications qui ont des problèmes de concurrence intrinsèque à données access, blocage par exemple, peuvent ne pas bénéficient d’un niveau de performance supérieur. Envisagez de réduire aller-retour par rapport à la base de données SQL Azure par mise en cache des données sur le côté client avec le service de mise en cache Azure ou une autre technologie de mise en cache. Voir [la mise en cache de niveau Application](#application-tier-caching).

## <a name="tuning-techniques"></a>Techniques de réglage
Dans cette section, nous examinons quelques techniques que vous pouvez utiliser pour optimiser les base de données SQL Azure pour obtenir les meilleures performances de votre application, exécutez-le au niveau des performances optimales inférieur. Certaines d'entre elles correspondent aux réglage meilleures pratiques de traditionnel SQL Server, mais d’autres sont spécifiques à la base de données SQL Azure. Dans certains cas, vous pouvez examiner les ressources consommées pour une base de données rechercher des zones pour optimiser les et étendre traditionnels techniques de SQL Server pour l’utiliser dans la base de données SQL Azure.

### <a name="azure-portal-tools"></a>Outils portails Azure
Vous trouverez deux outils dans le portail Azure qui peuvent vous aider à analyser et corriger les problèmes de performances avec votre base de données SQL :

- [Aperçu des performances de requête](sql-database-query-performance.md)
- [Conseiller de base de données SQL](sql-database-advisor.md)

Le portail Azure comporte plus d’informations sur les deux de ces outils et comment les utiliser. Pour efficacement diagnostiquer et corriger les problèmes, nous vous recommandons d’essaiera les outils dans le portail Azure. Nous vous conseillons d’utiliser le manuel d’optimisation des approches abordent ensuite manquants à partir d’index et réglage de requête, dans les cas particuliers.

### <a name="missing-indexes"></a>Index manquants
Un problème courant dans les performances de base de données OLTP est liée à la conception de base de données physique. Schémas de base de données sont souvent, conçus et expédiées sans essai à l’échelle (soit en charge ou en volume de données). Malheureusement, les performances d’un plan de requête peuvent être acceptable sur une échelle petite mais dégrader sensiblement sous volumes de données au niveau de production. La source la plus courante de ce problème est l’absence d’index appropriés pour satisfaire filtres ou autres restrictions dans une requête. Souvent, manquantes manifestes index sous forme de tableau numériser lorsqu’une recherche d’index pourrait suffit.

Dans cet exemple, le plan de la requête sélectionnée utilise une analyse lors de la recherche d’un suffit :

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Un plan de requête avec index manquants](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Base de données SQL Azure peut vous aider à rechercher et corriger courantes manquantes conditions d’index. DMV est intégrées à la base de données SQL Azure examiner compilations de requête dans lequel un index serait considérablement la valeur estimée pour exécuter une requête. Pendant l’exécution des requêtes, base de données SQL effectue le suivi de la fréquence à laquelle chaque plan de requête est exécutée et effectue le suivi de l’intervalle estimée entre le plan de requête en cours d’exécution et l’option fictif où cet index existant. Vous pouvez utiliser ces DMV à deviner rapidement les modifications apportées à la conception de base de données physique peuvent améliorer le coût total de la charge de travail pour une base de données et sa charge de travail réel.

Vous pouvez utiliser cette requête à évaluer potentiels index manquants :

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Dans cet exemple, la requête a donné lieu à cette suggestion :

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Après sa création, ce même instruction SELECT sélectionne une autre offre, qui utilise une recherche à la place d’une analyse et puis exécute plus efficacement le plan :

![Un plan de requête avec index corrigés](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Le point clé est que la capacité e/s d’un système de marchandise partagé, est plus limitée que celles d’un ordinateur serveur dédié. Il existe une prime sur réduction e/s inutiles pour profiter au maximum du système dans le DTU de chaque niveau de performance des niveaux de service de base de données SQL Azure. Création de base de données physique appropriée choix peut améliorer de manière significative la latence des requêtes individuelles, améliorer le débit de demandes simultanées traitées par unité d’échelle et réduire les coûts requis pour satisfaire la requête. Pour plus d’informations sur l’index manquant DMV, voir [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Optimisation de la requête et repères
L’optimiseur de requête de base de données SQL Azure est semblable à l’optimiseur de requêtes SQL Server traditionnel. La plupart des méthodes conseillées pour l’optimisation des requêtes et présentation de la logique des limitations du modèle l’optimiseur de requête s’appliquent également à la base de données SQL Azure. Si vous optimiser les requêtes de base de données SQL Azure, vous pouvez obtenir l’avantage supplémentaire de réduire les besoins en ressources agrégation. Votre application peut être en mesure d’exécuter un coût inférieur à celui d’un équivalent untuned, car elle peut être exécutée à un niveau de performances inférieur.

Voici un exemple qui est courante dans SQL Server et qui s’appliquent également à la base de données SQL Azure comment l’optimiseur de requêtes « détecte » paramètres. Lors de la compilation, l’optimiseur de requêtes évalue la valeur actuelle d’un paramètre pour déterminer si elle peut générer un plan de requête plus optimal. Bien que cette stratégie souvent peut entraîner un plan de requête est plus rapide qu’un plan compilé sans les valeurs de paramètre connus, elle travaille actuellement pourtour à la fois dans SQL Server et dans la base de données SQL Azure. Parfois le paramètre n’est pas détectée et parfois le paramètre est détectée mais le plan généré est non optimaux pour l’ensemble des valeurs de paramètre dans une charge de travail. Microsoft inclut les indicateurs de requête (directives) afin que vous pouvez spécifier l’intention plus délibérément et remplacer le comportement par défaut de détection des paramètres. Souvent, si vous utilisez des indications, vous pouvez résoudre cas dans lesquels le comportement par défaut SQL Server ou de base de données SQL Azure est imparfait pour une charge de travail client spécifique.

L’exemple suivant montre comment le processeur de requêtes peut générer un plan est non optimaux pour les performances et les besoins en ressources. Cet exemple montre également que si vous utilisez un indicateur de requête, vous pouvez réduire les exigences de temps et de ressources exécution de la requête pour votre base de données SQL :

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

Le code de configuration crée une table qui compte incliné distribution des données. Le plan de requête optimal varie selon le paramètre est sélectionné. Malheureusement, le plan de comportement de cache ne recompilation toujours la requête basée sur une valeur de paramètre la plus courante. Par conséquent, il est possible pour un plan non optimaux soit mis en cache et utilisé pour de nombreuses valeurs, même si un autre plan peut être un meilleur choix de plan en moyenne. Le plan de requête crée ensuite deux procédures stockées qui sont identiques, sauf qu’ait un indicateur de requête spécial.

**Exemple, partie 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Exemple, partie 2**

(Nous vous recommandons d’attendre au moins 10 minutes avant de commencer la partie 2 de l’exemple, afin que les résultats sont distincts dans les données de télémétrie qui en résulte.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Chaque partie de cet exemple tente d’exécuter une instruction insert paramétrées 1 000 fois (pour générer une charge suffisante à utiliser comme un jeu de données de test). Lors de l’exécution des procédures stockées, le processeur de requêtes examine la valeur du paramètre qui est passée à la procédure pendant sa première compilation (« détection des paramètres »). Processeur met en cache du plan qui en résulte et utilise pour les appels à une version ultérieure, même si la valeur du paramètre est différente. Le plan optimal ne peut-être pas être utilisé dans tous les cas. Vous pouvez parfois besoin guider l’optimiseur pour sélectionner un plan est préférable pour la casse moyenne plutôt que la casse spécifique à partir de lors de compilé tout d’abord la requête. Dans cet exemple, le plan initial génère un plan « analyse » qui lit toutes les lignes pour chaque valeur qui correspond au paramètre de rechercher :

![Optimisation en utilisant un plan d’analyse de la requête](./media/sql-database-performance-guidance/query_tuning_1.png)

Étant donné que nous exécuté la procédure à l’aide de la valeur 1, le plan qui en résulte n’était optimal pour la valeur 1 contrairement non optimaux pour toutes les autres valeurs de la table. Le résultat probable souhaitez ne vous convient pas si vous deviez sélectionnez chaque plan aléatoirement, car le plan s’exécute plus lentement et utilise plus de ressources.

Si vous exécutez le test avec `SET STATISTICS IO` défini sur `ON`, le travail d’analyse logique dans cet exemple est effectué en arrière-plan. Vous constatez qu’il n’y a 1,148 lectures terminés par le plan (qui n’est pas efficace, soit la casse moyenne pour renvoyer une ligne) :

![Optimisation à l’aide d’une analyse logique de requêtes](./media/sql-database-performance-guidance/query_tuning_2.png)

La deuxième partie de l’exemple utilise un indicateur de requête pour indiquer à l’optimiseur d’utiliser une valeur spécifique au cours du processus de compilation. Dans ce cas, il force le processeur de requêtes pour ignorer la valeur qui est passée comme paramètre, et à la place de supposer `UNKNOWN`. Cela fait référence à une valeur qui comporte la fréquence moyenne dans le tableau (en ignorant inclinaison). Le plan qui en résulte est un plan de recherche qui est plus rapide et utilise moins de ressources, moyenne, que le plan dans la partie 1 de cet exemple :

![Optimisation de requêtes à l’aide d’un indicateur de requête](./media/sql-database-performance-guidance/query_tuning_3.png)

Vous pouvez voir l’effet dans le tableau **sys.resource_stats** (il y a un délai à partir du moment que vous exécutez le test et quand les données remplissent la table). Pour cet exemple, partie 1 exécutés pendant la durée de validité 22:25:00 et 2e exécutée à 22:35:00. Notez que la fenêtre de temps antérieure utilisé davantage de ressources dans cette fenêtre de temps que celle ultérieure (en raison d’amélioration de l’efficacité de plan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Requêtes à l’exemple de résultats](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Bien que le volume dans cet exemple est intentionnellement petit, l’effet de paramètres non optimaux peut être substantiel, en particulier sur les bases de données volumineuses. La différence, dans certains cas, peut être compris entre secondes dans les cas rapides et les heures dans les cas lentes.

Vous pouvez examiner **sys.resource_stats** pour déterminer si la ressource d’un test utilise plus ou moins de ressources qu’un autre test. Lorsque vous comparez des données, séparer le minutage des tests afin qu’ils ne soient pas dans la même fenêtre de 5 minutes dans l’affichage **sys.resource_stats** . L’objectif de l’exercice est pour réduire le montant total des ressources utilisées et non à réduire les ressources de pointe. En règle générale, optimiser un morceau de code pour la latence permet également de réduire la consommation de ressources. Assurez-vous que les modifications apportées à l’application sont nécessaires, et que les modifications n’impact négatif sur l’expérience utilisateur de personne peut être utilisant des indicateurs de requête dans l’application.

Si une charge de travail possède un ensemble de requêtes se répètent, il est souvent préférable de capturer et à valider le caractère optimal de votre choix plan car elle détermine l’unité de taille minimale des ressources nécessaire pour héberger la base de données. Une fois que vous devez le valider, il peut arriver que réexaminer les offres pour vous aider à vous assurer qu’ils n'ont pas dégradés. Vous pouvez en savoir plus sur [les indicateurs de requête (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Bases de données croisées ont
Étant donné que la base de données SQL Azure s’exécute sur le matériel classique, les limites de capacité pour une seule base de données sont inférieures à celle d’une installation de SQL Server traditionnel en local. Certains clients utilisent techniques ont réparti opérations de base de données sur plusieurs bases de données lorsque les opérations ne tient à l’intérieur de la limite d’une seule base de données dans la base de données SQL Azure. La plupart des clients qui utilisent des techniques ont dans la base de données SQL Azure répartir leurs données sur une seule dimension sur plusieurs bases de données. Pour cette approche, vous devez comprendre que les applications OLTP effectuent souvent des transactions qui s’appliquent à une seule ligne ou à un petit groupe de lignes dans le schéma.

>[AZURE.NOTE] Base de données SQL propose désormais une bibliothèque pour vous aider à ont. Pour plus d’informations, voir [vue d’ensemble de base de données élastique client bibliothèque](sql-database-elastic-database-client-library.md).

Par exemple, si une base de données comporte le nom du client, ordre et détails de la commande (par exemple, l’exemple traditionnel base de données Northwind qui est fourni avec SQL Server), vous pouvez fractionner ces données en plusieurs bases de données en regroupant un client avec la commande associé et les informations détaillées de commande. Vous pouvez vous assurer que les données du client restent dans une seule base de données. L’application serait répartir différents clients sur bases de données, la répartition efficacement la charge sur plusieurs bases de données. Avec ont, non seulement clients peuvent éviter la limite de taille maximale de la base de données, mais base de données SQL Azure peut également traiter des charges de travail qui sont beaucoup plus important que les limites des niveaux de performances différentes, dans la mesure où chaque base de données individuel s’adapte à son DTU.

Bien qu’ont de base de données ne réduire la capacité de cumul des ressources pour une solution, il est très efficace en prenant en charge de très grandes solutions sont réparties sur plusieurs bases de données. Chaque base de données peut être exécutée à un niveau de performances différentes pour prendre en charge très volumineux ; ils « efficaces » bases de données avec des besoins en ressources haute.

### <a name="functional-partitioning"></a>Partition fonctionnel
Utilisateurs de SQL Server combinent souvent de nombreuses fonctions dans une seule base de données. Par exemple, si une application possède une logique à gérer un inventaire pour un magasin, cette base de données peut-être logique associée au stock, suivi des bons de commande, des procédures stockées et les vues indexées ou vue gérer les rapports de fin de mois. Cette technique rend plus facile à gérer la base de données pour les opérations telles que la sauvegarde, mais elle nécessite également ajuster la taille du matériel pour gérer la charge pointe sur toutes les fonctions d’une application.

Si vous utilisez une architecture horizontale dans une base de données SQL Azure, il est recommandé de fractionner des fonctions différentes d’une application en différentes bases de données. À l’aide de cette technique, chaque application évolue séparément. Comme une application devient plus occupée (et la charge sur la base de données augmente), l’administrateur peut choisir des niveaux de performance indépendante pour chaque fonction dans l’application. À la limite, avec cette architecture, une application peut être supérieure à un ordinateur même marchandise peut gérer parce que la charge s’étend sur plusieurs ordinateurs.

### <a name="batch-queries"></a>Requêtes par lots
Pour les applications qui accèdent aux données à l’aide de volume élevé, fréquents, interroger ad hoc, une quantité importante de temps de réponse est passée sur communication réseau entre la couche application et la base de données SQL Azure. Même lorsque l’application et la base de données SQL Azure sont dans le même centre de données, la latence du réseau entre les deux peut-être être agrandie opérations d’accès par un grand nombre de données. Pour réduire le réseau round voyages pour les opérations d’accès aux données, envisagez d’utiliser l’option soit lot les requêtes ad hoc, ou pour les compiler que des procédures stockées. Si vous lot les requêtes ad hoc, vous pouvez envoyer plusieurs requêtes comme lot volumineux en un seul déplacement à base de données SQL Azure. Si vous compilez les requêtes ad hoc dans une procédure stockée, vous pourriez obtenir le même résultat que si vous les commandes. À l’aide d’une procédure stockée vous offre également l’avantage de l’augmentation de la probabilité de mise en cache les plans de requête dans la base de données SQL Azure afin d’utiliser la procédure stockée à nouveau.

Certaines applications sont écriture accrue. Parfois, vous pouvez réduire la charge e/s total sur une base de données en tenant compte des comment traiter par lots écrit simultanément. Souvent, il est aussi simple que l’utilisation de transactions explicites au lieu de transactions validation automatique dans les procédures stockées et des lots ad hoc. Pour une évaluation de différentes techniques que vous pouvez utiliser, voir [Batching techniques pour les applications de base de données SQL dans Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Tester votre propre charge de travail pour trouver le modèle approprié pour le traitement par lots. Veillez à comprendre qu’un modèle il est possible que les garanties de cohérence légèrement différente. Recherche de la charge de travail droite réduit l’utilisation des ressources nécessite trouver la bonne combinaison de compromis la cohérence et les performances.

### <a name="application-tier-caching"></a>La mise en cache de couche application
Certaines applications de base de données ont des charges de travail comportant beaucoup de lecture. Couches de mise en cache peut-être réduire la charge sur la base de données et peut potentiellement réduire le niveau de performance doit prendre en charge d’une base de données à l’aide de base de données SQL Azure. Avec [Azure Redis Cache](https://azure.microsoft.com/services/cache/), si vous avez une charge de travail comportant beaucoup de lecture, vous pouvez lire les données une seule fois (ou peut-être une fois par ordinateur de la couche application, selon la manière dont il est configuré), puis stocker ces données à l’extérieur de votre base de données SQL. Il s’agit d’une façon de réduire la charge de la base de données (processeur et en lecture), mais il est un effet sur la cohérence étant donné que les données soient lues à partir du cache est peut-être pas synchronisées avec les données dans la base de données. Bien que dans de nombreuses applications un niveau d’incohérence est acceptable, qui n’est pas vrai pour toutes les charges de travail. Vous devez comprendre entièrement les exigences de l’application avant d’implémenter une stratégie de mise en cache de couche application.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de service, consultez [performances et options de base de données SQL](sql-database-service-tiers.md)
- Pour plus d’informations à propos des pools élastique de base de données, voir [qu’est un pool de base de données Azure élastique ?](sql-database-elastic-pool.md)
- Pour plus d’informations sur les performances et des pools élastique de base de données, voir [Quand envisager un pool élastique de base de données](sql-database-elastic-pool-guidance.md)
