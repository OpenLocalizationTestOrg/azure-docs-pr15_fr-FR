<properties
   pageTitle="Chiffrement transparent des données SQL Data Warehouse (T-SQL) | Microsoft Azure"
   description="Chiffrement transparent des données (TDE) SQL Data Warehouse (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>Prise en main avec chiffrement Transparent des données (TDE)


> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Autorisations nécessaires

Pour activer le chiffrement Transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## <a name="enabling-encryption"></a>L’activation de chiffrement

Procédez comme suit pour activer TDE pour un Data Warehouse SQL :

1. Se connecter à la base de données *maître* sur le serveur qui héberge la base de données à l’aide d’une connexion qui est un administrateur ou un membre du rôle **dbmanager** dans la base de données principale
2. Exécutez l’instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Désactivation de chiffrement

Suivez ces étapes pour désactiver TDE pour un Data Warehouse SQL :

1. Se connecter à la base de données *principale* à l’aide d’une connexion qui est un administrateur ou un membre du rôle **dbmanager** dans la base de données principale
2. Exécutez l’instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Un suspendus SQL Data Warehouse doit être relancé avant d’apporter des modifications aux paramètres TDE.

## <a name="verifying-encryption"></a>Vérification de chiffrement

Pour vérifier l’état de chiffrement pour un Data Warehouse SQL, procédez comme suit :

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

## <a name="encryption-dmvs"></a>Chiffrement DMV  

- [Sys.Databases][] 
- [Sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
