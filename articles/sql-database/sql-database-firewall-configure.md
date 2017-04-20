<properties
   pageTitle="Configurer une présentation de SQL server pare-feu | Microsoft Azure"
   description="Découvrez comment configurer un pare-feu de base de données SQL avec les règles de pare-feu au niveau du serveur et au niveau de la base de données afin de gérer l’accès."
   keywords="pare-feu de base de données"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Configurer les règles de pare-feu de base de données SQL Azure \- vue d’ensemble


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Microsoft Azure fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour mieux vous protéger vos données, pare-feux empêche tout accès à votre serveur de base de données jusqu'à ce que vous spécifiez les ordinateurs ayant une autorisation. Le pare-feu accorde l’accès aux bases de données en fonction de l’adresse IP d’origine de chaque requête.

Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses IP acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur et de base de données.

- **Règles de pare-feu au niveau du serveur :** Les règles suivantes permettent aux clients accéder à votre serveur SQL Azure entière, c'est-à-dire que toutes les bases de données au sein du même serveur logique. Les règles suivantes sont stockées dans la base de données **principale** . Règles de pare-feu au niveau du serveur peuvent être configurés à l’aide du portail ou en utilisant des instructions Transact-SQL.
- **Règles de pare-feu au niveau de la base de données :** Ces règles permettent aux clients d’accéder à des bases de données au sein de votre serveur de base de données SQL Azure. Vous pouvez créer les règles suivantes pour chaque base de données et s’ils sont stockés dans les bases de données individuels. (Vous pouvez créer des règles de pare-feu au niveau de la base de données pour la base de données **principale** ). Ces règles peuvent être utiles pour restreindre l’accès à certaines bases de données (sécurisés) au sein du même serveur logique. Règles de pare-feu au niveau de la base de données ne peuvent être configurés à l’aide des instructions Transact-SQL.

**Recommandation :** Microsoft recommande l’utilisation de règles de pare-feu au niveau de la base de données dès que possible pour améliorer la sécurité et pour rendre votre base de données plus portable. Utiliser des règles de pare-feu au niveau du serveur pour les administrateurs et lorsque vous avez plusieurs bases de données qui ont les mêmes conditions d’accès et vous ne voulez pas de temps configuration individuellement chaque base de données.


## <a name="firewall-overview"></a>Vue d’ensemble du pare-feu

Au départ, tous les accès Transact-SQL à votre serveur SQL Azure est bloqué par le pare-feu. Pour commencer à utiliser votre serveur SQL Azure, vous devez accéder au portail Azure et spécifiez une ou plusieurs règles de pare-feu au niveau du serveur permettant d’accéder à votre serveur SQL Azure. Utiliser les règles de pare-feu pour spécifier les plages d’adresses IP à partir d’Internet sont autorisés, et indique si les applications Azure peuvent essayer pour vous connecter à votre serveur SQL Azure.

Pour accorder l’accès à un seul des bases de données de votre serveur SQL Azure de manière sélective, vous devez créer une règle de niveau de base de données pour la base de données requise. Spécifier une plage d’adresses IP pour la règle de pare-feu de base de données qui se trouve au-delà de la plage d’adresses IP spécifiée dans la règle de pare-feu au niveau du serveur, puis vérifiez que l’adresse IP du client se situe dans la plage spécifiée dans la règle au niveau de la base de données.

Tentatives de connexion à partir d’Internet Azure doivent tout d’abord passer à travers le pare-feu avant d’atteindre votre serveur Azure SQL ou une base de données SQL, comme le montre l’illustration suivante.

   ![Configuration du pare-feu décrivant diagramme.][1]

## <a name="connecting-from-the-internet"></a>Connexion à partir d’Internet

Quand un ordinateur essaie de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie d’abord l’adresse IP d’origine de la demande par rapport à l’ensemble de règles de pare-feu :

- Si l’adresse IP de la demande est comprise dans une des plages spécifiés dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur de base de données SQL Azure.

- Si l’adresse IP de la requête n’est pas parmi les plages spécifiées dans la règle de pare-feu au niveau du serveur, les règles de pare-feu au niveau de la base de données sont vérifiées. Si l’adresse IP de la demande est comprise dans une des plages spécifiés dans les règles de pare-feu au niveau de la base de données, la connexion est accordée uniquement à la base de données qui comporte une règle de niveau de base de données correspondante.

- Si l’adresse IP de la requête n’est pas inclus dans la plage spécifiée dans une des règles de pare-feu au niveau du serveur ou au niveau de la base de données, l’échec de demande de connexion.

> [AZURE.NOTE] Pour accéder à base de données SQL Azure à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et de l’ordinateur local autorise les communications sortantes sur le port TCP 1433.


## <a name="connecting-from-azure"></a>Connexion à partir d’Azure

Lorsqu’une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Un pare-feu affectant de début et de fin adresse égale à 0.0.0.0 indique que ces connexions sont autorisées. Si la tentative de connexion n’est pas autorisée, la demande ne parvient pas au serveur de base de données SQL Azure.

> [AZURE.IMPORTANT] Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, y compris les connexions à partir d’abonnements d’autres clients. Lorsque vous sélectionnez cette option, assurez-vous que votre nom d’utilisateur et les autorisations des utilisateurs limitent l’accès aux utilisateurs autorisés uniquement.

Vous pouvez activer les connexions à partir d’Azure de deux façons :

- Dans le [portail Microsoft Azure](https://portal.azure.com/), activez la case à cocher **Autoriser les services azure au serveur d’accès** lors de la création d’un serveur.

- Dans le [portail classique](http://go.microsoft.com/fwlink/p/?LinkID=161793), sous l’onglet **configurer** sur un serveur, sous la section **Services autorisés** , cliquez sur **Oui** pour **Microsoft Azure Services**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Création de la première règle de pare-feu au niveau du serveur

Le premier paramètre de pare-feu au niveau du serveur peut être créé à l’aide du [portail Azure](https://portal.azure.com/) ou par programme à l’aide de l’API REST ou PowerShell Azure. Règles de pare-feu au niveau du serveur suivantes peuvent être créées et gérées à l’aide de ces méthodes comme via Transact-SQL. Pour améliorer les performances, les règles de pare-feu au niveau du serveur sont temporairement mis en cache au niveau de la base de données. Pour actualiser le cache, voir [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Pour plus d’informations sur les règles de pare-feu au niveau du serveur, voir [Comment : configurer un pare-feu de serveur SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Créer des règles de pare-feu au niveau de la base de données

Après avoir configuré le premier pare-feu au niveau du serveur, vous souhaiterez peut-être limiter l’accès à certaines bases de données. Si vous spécifiez une plage d’adresses IP dans la règle de pare-feu au niveau de la base de données qui se trouve en dehors de la plage spécifiée dans la règle de pare-feu au niveau du serveur, seuls les clients qui ont des adresses IP dans la plage de niveau de base de données peuvent accéder à la base de données. Vous pouvez avoir jusqu'à 128 au niveau de la base de données de règles de pare-feu pour une base de données. Règles de pare-feu au niveau de la base de données pour les bases de données principale et utilisateur peuvent être créés et gérés par le biais Transact-SQL. Pour plus d’informations sur la configuration des règles de pare-feu au niveau de la base de données, voir [sp_set_database_firewall_rule (bases de données Azure SQL)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Gestion des règles de pare-feu par programme

Outre le portail Azure, les règles de pare-feu pouvant être gérées par programme à l’aide de PowerShell Azure, API REST et Transact-SQL. Les tableaux suivants décrivent l’ensemble des commandes disponibles pour chaque méthode.


### <a name="transact-sql"></a>Transact-SQL

| Procédure stockée ou la vue de catalogue                                                           | Niveau     | Description                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [Sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Serveur    | Affiche les règles de pare-feu au niveau du serveur en cours     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Serveur    | Crée ou met à jour des règles de pare-feu au niveau du serveur       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Serveur    | Supprime les règles de pare-feu au niveau du serveur                  |
| [Sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Base de données  | Affiche les règles de pare-feu au niveau de la base de données Active   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Base de données  | Crée ou met à jour les règles de pare-feu au niveau de la base de données |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de données | Supprime les règles de pare-feu au niveau de la base de données                |

### <a name="rest-api"></a>API REST

| API                  | Niveau  | Description                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Règles de pare-feu de liste](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Serveur | Affiche les règles de pare-feu au niveau du serveur en cours                 |
| [Créer des règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Serveur | Crée ou met à jour des règles de pare-feu au niveau du serveur                   |
| [Définir des règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Serveur | Mises à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Supprimer la règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Serveur | Supprime les règles de pare-feu au niveau du serveur                              |


### <a name="azure-powershell"></a>PowerShell Azure

| Applet de commande                                                                                              | Niveau  | Description                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Serveur | Renvoie les règles de pare-feu au niveau du serveur en cours                  |
| [Nouvelle AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Serveur | Crée une nouvelle règle de pare-feu au niveau du serveur                         |
| [Jeu de AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Serveur | Mises à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Supprimer AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Serveur | Supprime les règles de pare-feu au niveau du serveur                              |

> [AZURE.NOTE] On peut observer des autant que retarder de cinq minutes pour les modifications apportées aux paramètres du pare-feu prenne effet.

## <a name="troubleshooting-the-database-firewall"></a>Dépannage du pare-feu de base de données

Lors de l’accès au service de base de données SQL Microsoft Azure ne se comporte pas comme prévu, tenez compte des points suivants :

- **Configuration du pare-feu local :** Avant de base de données SQL Azure peut accéder à votre ordinateur, vous devrez peut-être créer une exception de pare-feu sur votre ordinateur pour les ports TCP 1433. Si vous apportez des connexions à l’intérieur de la limite Azure cloud, vous devrez peut-être ouvrir des ports supplémentaires. Pour plus d’informations, voir la **V12 de base de données de SQL : à l’extérieur de vs à l’intérieur de** section de [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Adresse traduction réseau (NAT) :** En raison de NAT, l’adresse IP utilisée par votre ordinateur pour vous connecter à la base de données SQL Azure peut être différent de l’adresse IP affichée dans les paramètres de configuration IP de votre ordinateur. Pour afficher l’adresse IP à l’aide de votre ordinateur pour vous connecter à Azure, connectez-vous au portail et accédez à l’onglet **configurer** sur le serveur qui héberge votre base de données. Sous la section **Adresses IP autorisées** , l' **Adresse IP du Client en cours** s’affiche. Cliquez sur **Ajouter** aux **Adresses IP autorisées** pour autoriser cet ordinateur pour accéder au serveur.

- **Les modifications apportées à la liste Autoriser pas encore effectif :** Il peut y avoir autant que retarder de cinq minutes pour les modifications apportées à la configuration du pare-feu de base de données SQL Azure prenne effet.

- **Le nom de connexion n’est pas autorisé ou un mot de passe a été utilisé :** Si une connexion n’a pas d’autorisations sur le serveur de base de données SQL Azure ou le mot de passe est incorrect, la connexion au serveur de base de données SQL Azure est refusée. Création d’un paramètre de pare-feu fournit uniquement des clients à une opportunité pour essayer de vous connecter à votre serveur ; chaque client doit fournir les informations d’identification de sécurité nécessaires. Pour plus d’informations sur la préparation de connexions, voir gérer les bases de données, les connexions et les utilisateurs de base de données SQL Azure.

- **Adresse IP dynamique :** Si vous disposez d’une connexion Internet avec adressage IP dynamique et que vous rencontrez des difficultés à travers le pare-feu, vous pouvez essayer une des solutions suivantes :

 - Demandez à votre fournisseur de services Internet (ISP) pour la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur de base de données SQL Azure et ajoutez la plage d’adresses IP comme une règle de pare-feu.

 - Obtenir les adresses IP statiques à la place de vos ordinateurs clients et ajoutez les adresses IP sous forme de règles de pare-feu.

## <a name="next-steps"></a>Étapes suivantes

Sur la façon d’articles sur la création de règles de pare-feu au niveau du serveur et au niveau de la base de données, voir :

- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer les règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour un didacticiel sur la création d’une base de données, voir [créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md).
Pour vous connecter à une base de données SQL Azure d’ouvrir la source ou des applications tierces, voir [démarrage rapide Client exemples de code pour la base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour mieux comprendre comment accéder aux bases de données, voir [Gérer la sécurité access et de la connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Ressources supplémentaires

- [Protéger votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
