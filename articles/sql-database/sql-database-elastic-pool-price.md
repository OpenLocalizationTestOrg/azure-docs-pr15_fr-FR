<properties
    pageTitle="Performances et prix élastique pool de base de données SQL"
    description="Informations de tarification spécifiques pour des pools élastique de base de données."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Facturation du pool élastique de base de données et les prix des informations

Des pools élastique de base de données sont à payer par les caractéristiques suivantes :

- Un pool élastique est facturé lors de sa création, même lorsqu’il n’y a aucune base de données dans le pool.
- Un pool élastique est facturé toutes les heures. Il s’agit de la fréquence de contrôle de même en ce qui concerne les niveaux de performances des bases de données unique.
- Si un pool élastique est redimensionné à un nouveau montant d’eDTUs, puis le pool n’est pas facturé selon le volume d’eDTUS jusqu'à la fin de l’opération de redimensionnement. Cela suit le même modèle en modifiant le niveau de performances des bases de données autonome.


- Le prix d’un pool élastique est basé sur le nombre d’eDTUs du pool. Le prix d’un pool élastique est indépendant du nombre et l’utilisation des bases de données élastiques qu’il contient.
- Prix est calculé par (nombre de pool eDTUs) x (prix unitaire par eDTU).

Prix unitaire eDTU pour un pool élastique est supérieur à prix unitaire DTU pour une base de données autonome dans le même niveau de service. Pour plus d’informations, voir [tarifs de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 


Pour mieux comprendre les niveaux de service et eDTUs, consultez [performances et options de base de données SQL](sql-database-service-tiers.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un pool élastique de base de données](sql-database-elastic-pool-create-portal.md)
- [Surveiller, gérer et dimensionner un pool élastique de base de données](sql-database-elastic-pool-manage-portal.md)
- [Options de base de données SQL et de performances : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md)
- [Script PowerShell pour identifier les bases de données appropriés pour un pool élastique de base de données](sql-database-elastic-pool-database-assessment-powershell.md)
