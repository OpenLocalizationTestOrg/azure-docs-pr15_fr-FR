<properties
   pageTitle="Activer le chiffrement Transparent des données (TDE) pour la base de données SQL Server étirement sur Azure | Microsoft Azure"
   description="Activer le chiffrement Transparent des données (TDE) pour la base de données SQL Server étirement sur Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Activer le chiffrement Transparent des données (TDE) pour une base de données étirement sur Azure
> [AZURE.SELECTOR]
- [Portail Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Chiffrement de données transparent (TDE) permet de protéger contre la menace d’activité malveillante en effectuant en temps réel chiffrer et déchiffrer la base de données, les sauvegardes associées et les fichiers journaux des transactions inactives sans nécessiter des modifications à l’application.

TDE chiffre le stockage d’une base de données entière en utilisant une clé symétrique appelée la clé de chiffrement de base de données. La clé de chiffrement de base de données est protégée par un certificat de serveur intégrés. Le certificat de serveur intégré est unique pour chaque serveur Azure. Microsoft fait automatiquement pivote ces certificats au moins tous les 90 jours. Pour une description générale du TDE, voir [Le chiffrement Transparent des données (TDE)].

##<a name="enabling-encryption"></a>L’activation de chiffrement

Pour activer TDE pour un Azure base de données qui stocke les données migrées à partir d’une base de données compatibles avec étirement de SQL Server, effectuer les opérations suivantes :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans la carte de base de données, cliquez sur le bouton **paramètres**
3. Sélectionnez l’option de **chiffrement de données Transparent**![][1]
4. Sélectionnez le paramètre **sur** , puis **Enregistrer**
![][2]


##<a name="disabling-encryption"></a>Désactivation de chiffrement

Pour désactiver TDE pour un Azure base de données qui stocke les données migrées à partir d’une base de données compatibles avec étirement de SQL Server, effectuer les opérations suivantes :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans la carte de base de données, cliquez sur le bouton **paramètres**
3. Sélectionnez l’option de **chiffrement de données Transparent**
4. Sélectionnez le paramètre **désactiver** , puis **Enregistrer**




<!--Anchors-->
[Chiffrement transparent des données (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
