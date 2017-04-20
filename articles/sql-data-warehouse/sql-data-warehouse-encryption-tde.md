<properties
   pageTitle="Chiffrement transparent des données SQL Data Warehouse (portail) | Microsoft Azure"
   description="Chiffrement transparent des données (TDE) dans Data Warehouse SQL"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Prise en main avec transparente données chiffrement (TDE) dans Data Warehouse SQL

> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Autorisations nécessaires

Pour activer le chiffrement Transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## <a name="enabling-encryption"></a>L’activation de chiffrement

Pour activer TDE pour un Data Warehouse SQL, procédez comme suit :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans la carte de base de données, cliquez sur le bouton **paramètres**
3. Sélectionnez l’option de **chiffrement de données Transparent**![][1]
4. Sélectionnez le paramètre **sur**![][2]
5. Sélectionnez **Enregistrer**
![][3]  

## <a name="disabling-encryption"></a>Désactivation de chiffrement

Pour désactiver TDE pour un Data Warehouse SQL, procédez comme suit :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans la carte de base de données, cliquez sur le bouton **paramètres**
3. Sélectionnez l’option de **chiffrement de données Transparent**![][1]
4. Sélectionnez le paramètre **désactiver**![][4]
5. Sélectionnez **Enregistrer**
![][5]  

## <a name="encryption-dmvs"></a>Chiffrement DMV

Confirmation de chiffrement avec DMV suivantes :

- [Sys.Databases]
- [Sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
