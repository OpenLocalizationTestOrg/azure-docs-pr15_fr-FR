<properties
   pageTitle="Requête SQL Azure Data Warehouse (sqlcmd) | Microsoft Azure"
   description="Interrogation de magasin de données SQL Azure avec l’utilitaire de ligne de commande sqlcmd."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Requête SQL Azure Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Cette procédure pas à pas utilise l’utilitaire [sqlcmd][] pour interroger un magasin de données SQL Azure.  

## <a name="1-connect"></a>1. Connectez-vous

Pour commencer à utiliser [sqlcmd][], ouvrez l’invite de commandes et tapez **sqlcmd** suivi par la chaîne de connexion pour votre base de données SQL Data Warehouse. La chaîne de connexion requiert les paramètres suivants :

+ **Server (-S) :** Serveur sous la forme `<`nom du serveur`>`. database.windows.net
+ **Base de données (-d) :** Nom de la base de données.
+ **Activer les identificateurs entre guillemets (-je) :** Identificateurs entre guillemets doivent être activés pour vous connecter à une instance SQL Data Warehouse.

Pour utiliser l’authentification SQL Server, vous devez ajouter les paramètres de nom d’utilisateur et mot de passe :

+ **Utilisateur (-U) :** Utilisateur Server dans le formulaire `<`utilisateur`>`
+ **Mot de passe (-P) :** Mot de passe associé à l’utilisateur.

Par exemple, la chaîne de connexion peut se présenter comme suit :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Pour utiliser l’authentification Azure Active Directory intégré, vous devez ajouter les paramètres Azure Active Directory :

+ **Authentification azure Active Directory (-G) :** utiliser Azure Active Directory pour l’authentification

Par exemple, la chaîne de connexion peut se présenter comme suit :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Vous devez [Activer l’authentification Azure Active Directory](sql-data-warehouse-authentication.md) s’authentifier à l’aide d’Active Directory.

## <a name="2-query"></a>2. requête

Une fois la connexion, vous pouvez émettre les instructions Transact-SQL prises en charge par rapport à l’instance.  Dans cet exemple, les requêtes sont envoyés en mode interactif.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Ces exemples suivantes montrent comment vous pouvez exécuter vos requêtes en mode de traitement par lots à l’aide de l’option -Q ou tuyauterie votre SQL pour sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les options disponibles dans sqlcmd, consultez [documentation sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[Sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
