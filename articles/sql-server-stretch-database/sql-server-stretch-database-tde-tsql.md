<properties
   pageTitle="Activer le chiffrement Transparent des données (TDE) pour la base de données SQL Server étirement sur TSQL Azure | Microsoft Azure"
   description="Activer le chiffrement Transparent des données (TDE) pour la base de données SQL Server étirement sur TSQL Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Activer le chiffrement Transparent des données (TDE) pour une base de données étirement sur Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Portail Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Chiffrement de données transparent (TDE) permet de protéger contre la menace d’activité malveillante en effectuant en temps réel chiffrer et déchiffrer la base de données, les sauvegardes associées et les fichiers journaux des transactions inactives sans nécessiter des modifications à l’application.

TDE chiffre le stockage d’une base de données entière en utilisant une clé symétrique appelée la clé de chiffrement de base de données. La clé de chiffrement de base de données est protégée par un certificat de serveur intégrés. Le certificat de serveur intégré est unique pour chaque serveur Azure. Microsoft fait automatiquement pivote ces certificats au moins tous les 90 jours. Pour une description générale du TDE, voir [Le chiffrement Transparent des données (TDE)].

##<a name="enabling-encryption"></a>L’activation de chiffrement

Pour activer TDE pour un Azure base de données qui stocke les données migrées à partir d’une base de données compatibles avec étirement de SQL Server, effectuer les opérations suivantes :

1. Se connecter à la base de données *maître* sur le serveur Azure qui héberge la base de données à l’aide d’une connexion qui est un administrateur ou un membre du rôle **dbmanager** dans la base de données principale
2. Exécutez l’instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>Désactivation de chiffrement

Pour désactiver TDE pour un Azure base de données qui stocke les données migrées à partir d’une base de données compatibles avec étirement de SQL Server, effectuer les opérations suivantes :

1. Se connecter à la base de données *principale* à l’aide d’une connexion qui est un administrateur ou un membre du rôle **dbmanager** dans la base de données principale
2. Exécutez l’instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Vérification de chiffrement

Pour vérifier l’état de chiffrement pour une base de données Azure qui stocke les données migrées à partir d’une base de données compatibles avec étirement de SQL Server, effectuer les opérations suivantes :

1. Se connecter à la base de données *principale* ou instance à l’aide d’une connexion qui est un administrateur ou un membre du rôle **dbmanager** dans la base de données principale
2. Exécutez l’instruction suivante pour chiffrer la base de données.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Un résultat de ```1``` indique une base de données chiffrée, ```0``` indique une base de données non chiffré.


<!--Anchors-->
[Chiffrement transparent des données (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
