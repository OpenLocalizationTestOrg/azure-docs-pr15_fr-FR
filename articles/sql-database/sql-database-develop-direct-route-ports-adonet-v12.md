<properties 
    pageTitle="Ports au-delà 1433 pour la base de données SQL | Microsoft Azure"
    description="Connexions client ADO.NET vers V12 de base de données SQL Azure parfois ignorer le proxy et d’interagissent directement avec la base de données. Ports différent de 1433 sont importants."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL


Cette rubrique décrit les modifications V12 de base de données SQL Azure réunissant le comportement de connexion des clients qui utilisent ADO.NET 4.5 ou une version ultérieure.


## <a name="v11-of-sql-database-port-1433"></a>11 de base de données SQL : Port 1433


Lorsque votre programme client utilise ADO.NET 4.5 pour vous connecter et de la requête avec 11 de base de données SQL, la séquence interne est la suivante :


1. ADO.NET essaie de se connecter à la base de données SQL.

2. ADO.NET utilise le port 1433 pour appeler un module logiciels intermédiaires et logiciels intermédiaires se connecte à la base de données SQL.

3. Base de données SQL renvoie sa réponse au logiciels intermédiaires, qui transmet la réponse à ADO.NET au port 1433.


**Terminologie :** Nous décrire la séquence précédente en indiquant que ADO.NET interagit avec SQL de base de données en utilisant le *proxy Router*. Si aucun logiciels intermédiaires ont été impliquer, indiquer que l' *itinéraire direct* a été utilisé.


## <a name="v12-of-sql-database-outside-vs-inside"></a>V12 de base de données SQL : à l’extérieur de vs à l’intérieur


Pour les connexions à V12, nous devons demander si votre programme client s’exécute *à l’extérieur* ou *à l’intérieur de* la limite de cloud Azure. Les sous-sections discuter deux scénarios courants.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Extérieurs :* Client s’exécute sur votre ordinateur de bureau


Port 1433 est le seul port qui doit être ouvert sur votre ordinateur de bureau qui héberge votre application cliente de base de données SQL.


#### <a name="inside-client-runs-on-azure"></a>*à l’intérieur :* Client s’exécute sur Azure


Lorsque votre client s’exécute à l’intérieur de la limite de cloud Azure, il utilise ce que nous pouvons appeler un *itinéraire direct* pour interagir avec le serveur de base de données SQL. Une fois une connexion établie, interactions entre le client et la base de données n’associe en outre aucun proxy logiciels intermédiaires.


La séquence se présente comme suit :


1. ADO.NET 4.5 (ou version ultérieure) à l’origine une brève interaction avec le cloud Azure et reçoit un numéro de port dynamiquement identifié.
 - Le numéro de port dynamiquement identifié est dans la plage de 11000 11999 ou 14000 14999.

2. ADO.NET puis se connecte au serveur de base de données SQL directement, avec aucune logiciels intermédiaires entre les deux.

3. Requêtes sont envoyés directement à la base de données, et les résultats sont retournés directement au client.


Vérifiez que le port plages de 11000 11999 et 14000-14999 sur votre ordinateur client Azure restent disponibles pour les interactions de client ADO.NET 4.5 avec V12 de base de données SQL.

- En particulier, les ports de la plage doivent être gratuits de n’importe quel autres BLOQUEURS sortants.

- Sur votre ordinateur virtuel Azure, le **Pare-feu Windows avec fonctions avancées de sécurité** contrôle les paramètres de port.
 - Vous pouvez utiliser l' [interface utilisateur du pare-feu](http://msdn.microsoft.com/library/cc646023.aspx) pour ajouter une règle pour laquelle vous spécifiez le protocole ainsi que d’une plage de ports **TCP** avec la syntaxe de la comme **11000 11999**.


## <a name="version-clarifications"></a>Précisions de version


Cette section clarifie les noms qui font référence aux versions du produit. Il répertorie également certaines paires des versions entre les produits.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 prend en charge le protocole TDS 7.3, mais pas 7.4.
- ADO.NET 4.5 et versions ultérieures prend en charge le protocole TDS 7.4.


#### <a name="sql-database-v11-and-v12"></a>V12 et 11 de base de données SQL


Les client connexion différences 11 de base de données SQL et V12 sont mis en surbrillance dans cette rubrique.


*Remarque :* L’instruction Transact-SQL `SELECT @@version;` retourne une valeur qui commencent par un numéro, comme « 11. » ou '12', ainsi qu’aux correspond à des noms de notre version 11 et V12 pour la base de données SQL.


## <a name="related-links"></a>Liens connexes


- ADO.NET 4.6 a été publié le 20 juillet 2015. Une annonce de blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 a été publié le 15 août 2012. Une annonce de blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Un billet de blog sur ADO.NET 4.5.1 est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Liste des versions protocole TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Vue d’ensemble de développement de base de données SQL](sql-database-develop-overview.md)


- [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md)


- [Comment : configurer les paramètres de pare-feu sur base de données SQL](sql-database-configure-firewall-settings.md)

