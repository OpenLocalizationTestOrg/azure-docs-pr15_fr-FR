<properties
   pageTitle="Gérer l’alimentation cluster dans SQL Azure Data Warehouse (vue d’ensemble) | Microsoft Azure"
   description="Échelle de performances des fonctionnalités dans le magasin de données SQL Azure. Évoluer en ajustant DWUs ou interrompre et reprendre cluster ressources pour réduire les coûts."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gérer l’alimentation cluster dans SQL Azure Data Warehouse (vue d’ensemble)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

L’architecture de Data Warehouse SQL sépare le stockage et cluster, permettant à chacun à l’échelle séparément. Par conséquent, vous pouvez optimiser les performances lors de l’enregistrement de coûts en payant uniquement pour les performances lorsque vous en avez besoin. 

Cette vue d’ensemble décrit les capacités horizontale suivants de Data Warehouse SQL et propose des recommandations sur comment et quand les utiliser. 

- Échelle de calculer power en ajustant [du data warehouse unités (DWUs)][]
- Interrompre ou reprendre des ressources de calcul

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Adapter les performances

Dans SQL Data Warehouse, vous pouvez rapidement adapter les performances ou revenir à croissantes ou décroissantes cluster ressources du processeur, la mémoire et de la bande passante. Pour adapter les performances, il vous souhaitez est ajuster le nombre d' [unités (DWUs) du data warehouse][] que SQL Data Warehouse affecte à votre base de données. Data Warehouse SQL modifie les rapidement et gère toutes les modifications sous-jacentes de matériel ou de logiciel.

Disparaît sont les jours où vous devez rechercher le type de processeurs, la quantité de mémoire ou type de stockage vous devez disposer des performances dans votre data warehouse. En plaçant votre Data Warehouse dans le cloud, vous n’avez plus de traiter les problèmes liés au matériel faisceau. Au lieu de cela, SQL Data Warehouse vous demandant à cette question : la vitesse à laquelle vous voulez analyser vos données ? 

### <a name="how-do-i-scale-performance"></a>Comment adapter les performances ?

Pour ELASTIQUEMENT augmenter ou réduire votre puissance de calcul, modifiez simplement le paramètre [du data warehouse unités (DWUs)][] pour votre base de données. Performances augmente linéaire lorsque vous l’ajoutez plus DWU.  À un niveau supérieur DWU, vous devez ajouter plus de 100 DWUs pour vous constatez une amélioration importante des performances. Pour vous aider à choisir des déviations significatives dans DWUs, nous proposons les niveaux DWU qui donnent de meilleurs résultats.
 
Pour ajuster DWUs, vous pouvez utiliser une des méthodes suivantes individuels.

- [Échelle de calculer power avec Azure portail][]
- [Échelle de calculer power avec PowerShell][]
- [Échelle cluster power avec l’API REST][]
- [Échelle de calculer power avec TSQL][]

### <a name="how-many-dwus-should-i-use"></a>Combien DWUs dois-je utiliser ?
 
Évolution linéaire des performances dans Data Warehouse SQL, puis changer d’échelle une cluster autre (par exemple de 100 DWUs à DWUs 2000) s’est-il passé dans secondes. Cela vous donne la possibilité de tester les différents paramètres DWU jusqu'à ce que vous trouviez mieux de votre scénario.

Pour mieux comprendre quelle est la valeur de votre DWU idéale, essayez mise à l’échelle monter et Descendre et en cours d’exécution des requêtes quelques après le chargement de vos données. Mise à l’échelle étant rapide, vous pouvez essayer un nombre de différents niveaux de performance en une heure ou moins. Gardez à l’esprit que SQL Data Warehouse est conçu pour traiter les grandes quantités de données et pour afficher ses capacités trues pour suivre l’évolution, en particulier au niveau des plus grande échelles que nous proposons, vous souhaiterez utiliser un jeu de données volumineux qui approche ou dépasse 1 To.

Recommandations pour trouver la meilleure DWU pour votre charge de travail :

1. Pour un data warehouse de développement, commencez par sélectionner un petit nombre de DWUs.  Un bon point de départ est DW400 ou DW200.
2. Surveiller les performances de votre application, observez que le nombre de DWUs sélectionné par rapport à la performance que vous observez.
3. Déterminer la quantité performances plus ou moins rapidement doivent être pour vous atteindre le niveau des performances optimales pour vos besoins en avec pour Assomption l’échelle linéaire.
4. Augmenter ou diminuer le nombre de DWUs au prorata comment beaucoup plus ou moins rapidement vous souhaitez que votre charge de travail à effectuer. Le service répondre rapidement et ajuster les ressources de calcul pour les nouvelles exigences DWU.
5. Continuez à effectuer des ajustements jusqu'à atteindre un niveau de performances optimales pour vos besoins.

### <a name="when-should-i-scale-dwus"></a>Quand dois-je échelle DWUs ?

Lorsque vous avez besoin plus rapidement des résultats, augmenter votre DWUs et payer pour de meilleures performances.  Lorsque vous avez besoin moins calculer power, réduire votre DWUs et payer uniquement pour ce dont vous avez besoin. 

Recommandations pour quand à l’échelle DWUs :

1. Si votre application comporte une charge de travail fluctuante, échelle DWU niveaux vers le haut ou vers le bas pour accueillir pointes et bas points. Par exemple, si votre charge de travail est généralement optimale à la fin du mois, prévoyez d’ajouter plus DWUs pendant ces jours pointe, puis mettre à l’échelle vers le bas une fois que la période de pointe.
2. Avant d’effectuer une opération de chargement ou de transformation de données dense, évoluer DWUs afin que vos données sont disponibles plus rapidement.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez une des méthodes suivantes individuels.

- [Pause cluster avec Azure portail][]
- [Pause cluster avec PowerShell][]
- [Pause cluster avec l’API REST][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV cluster

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour reprendre une base de données, utilisez une de ces méthodes individuels.

- [Cluster CV avec Azure portail][]
- [Cluster CV avec PowerShell][]
- [Cluster CV avec l’API REST][]

## <a name="permissions"></a>Autorisations

Mise à l’échelle de la base de données nécessite les autorisations décrites dans [Modifier base de données][].  Pause et Resume requièrent l’autorisation [Collaboration de base de données SQL][] , spécifiquement Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour vous aider à comprendre quelques concepts de performance clés supplémentaires :

- [Gestion de la charge de travail et la concurrence][]
- [Vue d’ensemble de création de table][]
- [Distribution de tables][]
- [L’indexation de table][]
- [Partition de table][]
- [Statistiques de la table][]
- [Meilleures pratiques][]

<!--Image reference-->

<!--Article references-->
[unités de magasin de données (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Échelle de calculer power avec Azure portail]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Échelle de calculer power avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Échelle cluster power avec l’API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Échelle de calculer power avec TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause cluster avec Azure portail]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause cluster avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause cluster avec l’API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Cluster CV avec Azure portail]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Cluster CV avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Cluster CV avec l’API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gestion de la charge de travail et la concurrence]: ./sql-data-warehouse-develop-concurrency.md
[Vue d’ensemble de création de table]: ./sql-data-warehouse-tables-overview.md
[Distribution de tables]: ./sql-data-warehouse-tables-distribute.md
[L’indexation de table]: ./sql-data-warehouse-tables-index.md
[Partition de table]: ./sql-data-warehouse-tables-partition.md
[Statistiques de la table]: ./sql-data-warehouse-tables-statistics.md
[Meilleures pratiques]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[Collaboration de base de données SQL]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[MODIFIER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
