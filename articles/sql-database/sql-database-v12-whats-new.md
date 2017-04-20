<properties
    pageTitle="Quelles sont les nouveautés dans SQL de base de données V12 | Microsoft Azure"
    description="Explique pourquoi les systèmes d’entreprise qui sont à l’aide de base de données SQL Azure dans le cloud lui facilitera par la mise à niveau vers la version V12."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>Quelles sont les nouveautés dans V12 de base de données SQL


Cette rubrique décrit les nombreux avantages contenant la nouvelle version V12 de base de données SQL Azure par rapport à la version 11.


Nous continuer à ajouter des fonctionnalités à V12. Afin que nous vous invitons à consulter notre page de mises à jour de Service Web pour Azure et d’utiliser ses filtres :


- Filtré au [service de base de données SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtré officielle les [annonces de (disponibilité générale)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) pour les fonctionnalités de base de données SQL.


Les dernières informations sur les limites des ressources pour la base de données SQL présentées en :<br/>[Limites de ressources de base de données SQL azure](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Compatibilité des applications accrue avec SQL Server


Un objectif clé de V12 de base de données SQL a été pour améliorer la compatibilité avec Microsoft SQL Server 2014 et conserver la compatibilité lors de la nouvelle version de SQL Server est disponible. Entre les autres zones, V12 atteint disparités qui existent avec SQL Server dans la zone importante de programmabilité. Par exemple :

- [Prise en charge JSON intégrée](https://msdn.microsoft.com/library/dn921897.aspx)

- [Fonctions de fenêtre](http://msdn.microsoft.com/library/ms189798.aspx), avec [le](http://msdn.microsoft.com/library/ms189461.aspx)

- [Index XML](http://msdn.microsoft.com/library/bb934097.aspx) et [sélectives index XML](http://msdn.microsoft.com/library/jj670104.aspx)

- [Suivi des modifications](http://msdn.microsoft.com/library/bb933875.aspx)

- [SÉLECTIONNEZ... DANS](http://msdn.microsoft.com/library/ms188029.aspx)

- [Recherche de texte intégral](http://msdn.microsoft.com/library/ms142571.aspx)

- [MODIFIER la CONFIGURATION d’inclus dans l’étendue de base de données (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Voir [ici](sql-database-transact-sql-information.md) pour l’ensemble des fonctionnalités non prises en charge dans la base de données SQL.


### <a name="compatibility-level-130"></a>Niveau de compatibilité 130


> [AZURE.IMPORTANT] À partir de **juin 2016**, *nouvellement* créé des bases de données sur V12 de base de données SQL Azure que leur niveau de compatibilité démarrent en 130, qui correspond à Microsoft SQL Server 2016 GA.
> 
> Vous pouvez utiliser `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` si vous préférez.
> 
> Bases de données créées avant juin 2016 n’ont pas de leur niveau de compatibilité modifiée par cette modification de la valeur par défaut. Ni le niveau d’une base de données modifiée en mettant à niveau à partir de 11 à V12.



Pour obtenir une explication de comparer vos requêtes les plus importantes entre les dernières actualités et précédent niveau de compatibilité, voir :

- [Meilleures performances des requêtes avec le niveau de compatibilité 130 dans la base de données SQL Azure](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Plus de performances premium, nouveaux niveaux de performances


Dans V12, nous augmentée les unités de Transaction de base de données (DTUs) affecté à tous les niveaux de performances Premium à 25 % sans frais supplémentaires. Encore plus performant possible avec les nouvelles fonctionnalités telles que :


- Prise en charge en mémoire [columnstore index](http://msdn.microsoft.com/library/gg492153.aspx).
- [Table partition par lignes](http://msdn.microsoft.com/library/ms187802.aspx) avec les améliorations apportées aux liées à [Tronquer une TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- La disponibilité de la gestion dynamique des vues [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) pour aider à contrôler et optimiser les performances.


### <a name="reliable-performance"></a>Performances fiables


Si votre programme client se connecte à V12 de base de données SQL pendant l’exécution de votre client sur une Azure machine virtuelle (), vous devez ouvrir les plages de ports suivants sur l’ordinateur virtuel :

- 11000 11999
- 14000 14999


Cliquez [ici](sql-database-develop-direct-route-ports-adonet-v12.md) pour plus d’informations sur les ports pour V12 de base de données SQL. Les ports sont requis par les améliorations des performances de V12 de base de données SQL.


## <a name="better-support-for-cloud-saas-vendors"></a>Optimiser le support pour cloud SaaS fournisseurs


Uniquement en V12, nous avons publié le nouveau niveau de performances Standard S3 et la version d’évaluation des [pools élastique de base de données](sql-database-elastic-pool.md). Des pools élastique de base de données est une solution conçue pour cloud SaaS fournisseurs.  Avec des pools élastique de base de données, vous pouvez :


- Partager DTUs entre les bases de données pour réduire les coûts pour un grand nombre de bases de données.
- Exécuter les [tâches de base de données élastique](sql-database-elastic-jobs-overview.md) pour gérer les bases de données à l’échelle.


## <a name="security-enhancements"></a>Améliorations de la sécurité


Sécurité est un problème principal pour toutes les personnes qui s’exécute de leurs activités dans le cloud. Les dernières fonctionnalités de sécurité publiées dans V12 sont les suivantes :


- [Sécurité au niveau de la ligne](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Masquage de l’échange dynamique de données](sql-database-dynamic-data-masking-get-started.md)
- [Des bases de données](http://msdn.microsoft.com/library/ff929188.aspx)
- Gestion des [rôles d’application](http://msdn.microsoft.com/library/ms190998.aspx) avec accorder, refuser, RÉVOQUER
- [Chiffrement de données transparent](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Connexion à la base de données SQL à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md)
 - Base de données SQL prend désormais en charge l’authentification Azure Active Directory, un mécanisme de connexion à la base de données SQL à l’aide des identités dans Azure Active Directory (AD Azure). Avec l’authentification Azure Active Directory, vous pouvez centraliser gérer l’identité des utilisateurs de base de données et d’autres services Microsoft dans un emplacement central.
- [Toujours chiffrées](https://msdn.microsoft.com/library/mt163865.aspx) (en mode Aperçu avant) rend le chiffrement transparent pour les applications et permet aux clients chiffrer des données sensibles à l’intérieur des applications clientes sans partager les clés de chiffrement avec la base de données SQL.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>Augmenter la continuité des activités lors de la récupération est nécessaire


V12 point de récupération améliorés (RPO) et récupération estimée fois (ERTs) :


| Fonctionnalité de continuité Professionnel | Version antérieure | V12 |
| :-- | :-- | :-- |
| Geo-restaurer | • RPO < 24 heures.<br/>• Insér < 12 heures. | • RPO < 1 heure.<br/>• Insér < 12 heures. |
| Geo-réplication Active | • RPO < 5 minutes.<br/>• Insér < 1 heure. | • Secondes de 5 < RPO.<br/>• Secondes de 30 < Insér. |


Pour plus d’informations, consultez [continuité des activités de base de données SQL](sql-database-business-continuity.md) .


## <a name="more-reasons-to-upgrade-now"></a>Pourquoi mettre à jour maintenant


Il existe plusieurs bonnes raisons pourquoi clients doivent mettre à jour maintenant V12 de base de données SQL Azure à partir de 11 :


- V12 de base de données SQL possédant une longue liste de fonctionnalités au-delà de 11.
- Nous continuer à ajouter de nouvelles fonctionnalités pour V12, mais aucuns nouvelles fonctionnalités ne soient ajoutés au 11.
- La plupart des nouvelles fonctionnalités sont publiées V12 de base de données SQL avant qu’ils sont diffusés pour Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>Utilisez-vous V12 déjà ?


Pour vérifier si vous disposez d’une base de données ou le serveur logique s’exécutant sur une version antérieure du service de base de données SQL une méthode simple consiste à effectuer les opérations suivantes :


1. Accédez au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**.
3. Cliquez sur **SQL Server**.
4. L’icône en regard de votre serveur ou votre base de données indique le scénario :
 - ![Icône pour un serveur v12](./media/sql-database-v12-whats-new/v12_icon.png) **serveur logique V12**
 - ![Icône pour la version antérieure server](./media/sql-database-v12-whats-new/earlier_icon.png) **serveur logique version antérieure**


Une autre technique pour déterminer la version consiste à exécuter la `SELECT @@version;` instruction dans votre base de données et afficher des résultats semblables aux :


- **12**.0.2000.10 &nbsp; *(version V12)*
- **11**.0.9228.18 &nbsp; *(version 11)*


Une base de données V12 peut être hébergé uniquement sur un serveur logique V12. Et un serveur V12 peut héberger uniquement V12 bases de données.


Si vous ne soyez pas encore exécuté sur V12, vous pouvez mettre à niveau votre serveur logique en suivant les étapes de [mise à niveau vers V12 de base de données SQL en place](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Générales régions de disponibilité


- Le 31 juillet 2015, toutes les régions avaient été promues à disponibilité générale.
- V12 a été publiée en décembre 2014, mais seulement à l’état de l’aperçu.

[Conditions supplémentaires d’utilisation de la prévisualisation de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
