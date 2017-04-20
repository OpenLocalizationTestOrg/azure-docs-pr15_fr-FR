<properties
    pageTitle="Prise en charge des clients de niveau inférieur de la base de données SQL et point de terminaison IP passe pour l’audit | Microsoft Azure"
    description="Découvrez les prise en charge des clients de niveau inférieur de la base de données SQL et IP modifications de point de terminaison pour l’audit."
    services="sql-database"
    documentationCenter=""
    authors="ronitr"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/10/2016"
    ms.author="ronitr"/>

# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>Base de données SQL - prise en charge des clients de niveau inférieur et les modifications de point de terminaison IP pour l’audit


[Audit](sql-database-auditing-get-started.md) fonctionne automatiquement avec les clients SQL qui prennent en charge la redirection TDS.


##<a id="subheading-1"></a>Prise en charge des clients de niveau inférieur

Tous les clients qui mettent en œuvre, TDS 7.4 doivent également en charge la redirection. Exceptions à cette règle incluent JDBC 4.0 dans lequel la fonctionnalité de redirection n’est pas entièrement prise en charge et fastidieuses pour Node.JS dans la redirection n’a pas été implémentée.

Pour « Clients de niveau inférieur », c'est-à-dire la support TDS version 7.3 et ci-dessous - le nom de domaine complet du serveur dans la connexion de la chaîne doit être modifiée :

Nom de domaine complet de serveur d’origine dans la chaîne de connexion : <*nom du serveur*>. database.windows.net

Serveur modifiés nom de domaine complet dans la chaîne de connexion : <*nom du serveur*> .database. **sécurisé**. Windows.NET

Une liste partielle des « Clients de niveau inférieur » inclut :

- .NET 4.0 et versions antérieures,
- ODBC 10.0 et ci-dessous.
- JDBC (JDBC ne prend pas en charge TDS 7.4, la fonctionnalité de redirection TDS n'est pas entièrement prise en charge)
- Fastidieuses (pour Node.JS)

**Remarque :** Le serveur ci-dessus modification FQDN peut être utile également pour appliquer une stratégie d’audit de SQL Server au niveau sans besoin d’une étape de configuration dans chaque base de données (minimisation temporaire).

##<a id="subheading-2"></a>Modifications de point de terminaison IP lors de l’activation d’audit

Veuillez noter que si vous activez l’audit, le point de terminaison IP de votre base de données changent. Si vous avez des paramètres du pare-feu strict, mettez à jour les paramètres du pare-feu en conséquence.

Le nouveau point de terminaison de base de données IP dépend de la région de base de données :

| Région de base de données | Points de terminaison IP possibles |
|----------|---------------|
| Amérique du Nord Chine  | 139.217.29.176, 139.217.28.254 |
| Moyen-Orient Chine  | 42.159.245.65, 42.159.246.245 |
| Australie est  | 104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australie sud-est. | 191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brésil sud  | 104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| États-Unis centre  | 104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Asie de l’est   | 23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| États-Unis Extrême-Orient 2 | 104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| États-Unis Extrême-Orient   | 23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Inde central  | 104.211.98.219, 104.211.103.71 |
| Inde du Sud   | 104.211.227.102, 104.211.225.157 |
| Inde ouest  | 104.211.161.152, 104.211.162.21 |
| Orient Japon   | 104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japon ouest    | 104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| États-Unis centre nord  | 191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europe du Nord  | 104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| États-Unis centre sud  | 191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Pays d’Asie du Sud-est  | 104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europe occidentale  | 104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| États-Unis Ouest  | 191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canada Central  | 13.88.248.106 |
| Canada Extrême-Orient  |  40.86.227.82 |
