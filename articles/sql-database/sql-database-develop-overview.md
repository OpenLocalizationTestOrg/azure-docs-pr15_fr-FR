<properties
    pageTitle="Base de données SQL développer présentation | Microsoft Azure"
    description="En savoir plus sur les bibliothèques de connectivité disponibles et des pratiques recommandées pour les applications qui se connectent à la base de données SQL."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>Vue d’ensemble de développement de base de données SQL
Cet article décrit les considérations base un développeur doit être informé lors de l’écriture de code pour vous connecter à la base de données SQL Azure.

## <a name="language-and-platform"></a>Langage et votre plateforme
Exemples de code sont disponibles pour différentes langues et plateformes de programmation. Vous trouverez des liens vers les exemples de code en : 

* Plus d’informations : [les bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitations de ressources
Base de données SQL Azure gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : gouvernance de ressources et de l’application de limites.

* Plus d’informations : [les limites des ressources de base de données SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Sécurité
Base de données SQL Azure fournit des ressources pour limiter l’accès, la protection des données et contrôler les activités dans une base de données SQL.

* Informations supplémentaires : [protéger votre base de données SQL](sql-database-security.md)

## <a name="authentication"></a>Authentification
* Base de données SQL Azure prend en charge les utilisateurs de l’authentification SQL Server et les connexions, ainsi que [l’authentification Azure Active Directory](sql-database-aad-authentication.md) utilisateurs et connexions.
* Vous devez spécifier une base de données particulière, au lieu d’utilisation de la base de données *maître* par défaut.
* Vous ne pouvez pas utiliser l’instruction Transact-SQL **utiliser myDatabaseName ;** base de données SQL pour basculer vers une autre base de données.
* Plus d’informations : [sécurité de base de données SQL : gérer la sécurité access et de la connexion de base de données](sql-database-manage-logins.md)

## <a name="resiliency"></a>Résilience
En cas d’une erreur temporaire lors de la connexion à la base de données SQL, votre code doit reprendre l’appel.  Nous vous recommandons de qui réessayer logique d’intervalle logique utilisation, afin qu’il ne pas surcharger la base de données SQL avec une nouvelle tentative simultanée de plusieurs clients.

* Exemples de code : pour les exemples de code qui illustrent logique de nouvelle tentative, consultez Exemples pour la langue de votre choix à : [les bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)
* Plus d’informations : [les messages d’erreur pour les programmes clients de base de données SQL](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gestion des connexions
* Dans la logique de connexion client, remplacer le délai par défaut pour être 30 secondes.  La valeur par défaut de 15 secondes est trop petite pour connexions qui dépendent d’internet.
* Si vous utilisez un [regroupement de connexion](http://msdn.microsoft.com/library/8xx3tyca.aspx), veillez à fermer la connexion le moment votre programme n’utilise pas activement il et n’est pas préparation réutiliser.

## <a name="network-considerations"></a>Considérations sur le réseau
* Sur l’ordinateur qui héberge votre programme client, vérifiez que le pare-feu autorise la communication TCP sortante sur le port 1433.  Plus d’informations : [configurer un pare-feu de base de données SQL Azure](sql-database-configure-firewall-settings.md)
* Si votre programme client se connecte à V12 de base de données SQL pendant l’exécution de votre client sur une Azure machine virtuelle (), vous devez ouvrir certaines plages de port sur l’ordinateur virtuel. Plus d’informations : [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
* Connexions client à V12 de base de données SQL Azure parfois ignorer le proxy et d’interagissent directement avec la base de données. Ports différent de 1433 sont importants. Plus d’informations : [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Données ont avec une échelle élastique
Échelle élastique simplifie le processus de mise à l’échelle extraction (et). 

* [Modèles de conception pour les Applications SaaS client à plusieurs avec la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
* [Prise en main aperçu d’élastique échelle de base de données SQL Azure](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Étapes suivantes

Explorez toutes les [fonctionnalités de base de données SQL](https://azure.microsoft.com/services/sql-database/).
