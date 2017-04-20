<properties
   pageTitle="Sauvegardes de base de données SQL - automatique, geo redondants | Microsoft Azure" 
   description="Base de données SQL crée une sauvegarde de base de données locale toutes les cinq minutes automatiquement et utilise Azure accès en lecture le stockage geo redondantes (GRS RA) pour redondance geo. "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>En savoir plus sur les sauvegardes de base de données SQL

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

Base de données SQL crée une sauvegarde de base de données locale toutes les n minutes automatiquement et utilise le stockage geo redondants accès en lecture Azure geo redondance. Sauvegardes de base de données sont une partie essentielle de toute stratégie récupération continuité et de reprise d’entreprise, car ils protègent vos données contre toute corruption accidentelle ou la suppression. 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>Qu’est une sauvegarde de base de données SQL ?  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

Une sauvegarde de base de données SQL inclut les sauvegardes de base de données locale et geo redondants. Ces sauvegardes sont créées automatiquement et sans frais supplémentaires. Vous ne devez rien à faire pour les faire.

<!----------------- 
    Explains first component of the backup feature
------------------>

Pour des sauvegardes locales, base de données SQL utilise la technologie SQL Server pour créer des sauvegardes [complète](https://msdn.microsoft.com/library/ms186289.aspx), [différentielle](https://msdn.microsoft.com/library/ms175526.aspx )et [journal des transactions](https://msdn.microsoft.com/library/ms191429.aspx) . Les sauvegardes du journal des transactions se produisent toutes les cinq minutes, qui permet de vous permettent d’effectuer une restauration sur le même serveur qui héberge la base de données. Lorsque vous restaurez une base de données, le service effectue le journal plein, écart et transaction des sauvegardes doivent être restaurés.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Utiliser une sauvegarde de base de données locale à :

- Restaurer une base de données à un point dans le temps au sein de la période de rétention. Avec une sauvegarde de base de données, vous pouvez restaurer une base de données à un point dans le temps, restaurer une base de données supprimé à la fois qu’il a été supprimé ou restaurer une base de données dans une autre région géographique. Pour effectuer une restauration, voir [restaurer une base de données à partir d’une sauvegarde de base de données](sql-database-recovery-using-backups.md).

- Copier une base de données sur un serveur SQL dans la région identiques ou différent. La copie est cohérente avec la base de données SQL en cours. Pour effectuer une copie, voir [Copier de base de données](sql-database-copy.md).

- Archiver une sauvegarde de base de données au-delà de la période de rétention de sauvegarde. Pour effectuer un archivage, le fichier [d’exportation d’une base de données SQL à un DOS](sql-database-export.md) . Vous pouvez archiver les à dos de stockage à long terme et stocker au-delà de la période de rétention. Ou, utilisez l’à DOS pour transférer une copie de votre base de données vers SQL Server, localement ou dans une Azure machine virtuelle ().

<!----------------- 
    Explains first component of the backup feature
------------------>

Des sauvegardes geo redondantes, base de données SQL utilise la [réplication de stockage Azure](../storage/storage-redundancy.md). Base de données SQL stocke les fichiers de sauvegarde de base de données locale dans un compte de [Stockage Geo redondants accès en lecture (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) . Azure réplique les fichiers de sauvegarde sur un [Centre de données appariés](../best-practices-availability-paired-regions.md). 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Utiliser une sauvegarde geo redondants à :

- Restaurer une base de données pour une région géographique différente au cas où vous ne pouvez pas accéder la sauvegarde de la base de données à partir de votre région de base de données principale. 

>[AZURE.NOTE] Dans le stockage Azure, le terme *réplication* fait référence à la copie des fichiers d’un emplacement vers un autre. De SQL *réplication de base de données* fait référence à tout en conservant vers plusieurs bases de données secondaires synchronisées avec une base de données principale. 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quelle quantité de stockage sauvegarde incluse sans frais ?

Base de données SQL fournit jusqu'à 200 % de votre espace de stockage de base de données généré maximale sous forme de stockage de sauvegarde sans frais supplémentaires. Par exemple, si vous avez une instance de base de données Standard avec une taille de base de données générée de 250 Go, vous disposez 500 Go de stockage de sauvegarde sans frais supplémentaires. Si votre base de données dépasse le stockage de sauvegarde fourni, vous pouvez choisir de réduire la période de rétention en contactant le Support Azure. Une autre option consiste à payer pour le stockage de sauvegarde supplémentaire qui est facturée au taux standard accès en lecture géographiquement stockage redondants (RA GRS). 

## <a name="how-often-do-backups-happen"></a>La fréquence à laquelle des sauvegardes problème se produit-il ?

Des sauvegardes de base de données locale, des sauvegardes complètes arriver hebdomadaire, celles-ci s’exécutent toutes les heures et journal des transactions des sauvegardes se produisent toutes les cinq minutes. La première sauvegarde est planifiée immédiatement après qu’une base de données est créée. Elle se termine généralement dans les 30 minutes, mais cela peut prendre plus de temps lorsque la base de données est de taille importante. Par exemple, la sauvegarde initiale peut prendre plus de temps sur une base de données restaurée ou une copie de la base de données. Après la première sauvegarde complète, toutes les sauvegardes supplémentaires sont planifiées automatiquement et gérés silencieusement en arrière-plan. Le minutage exact des sauvegardes de base de données complète et [différentielle](https://msdn.microsoft.com/library/ms175526.aspx) est déterminé comme il équilibre la charge système globale. 

Des sauvegardes geo redondants, sauvegardes complète et différentielle sont copiées en fonction de la planification de réplication de stockage Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Combien de temps vous conserver mes sauvegardes ?

Chaque sauvegarde de base de données SQL a une période de rétention basée sur le [niveau de service](sql-database-service-tiers.md) de la base de données. La période de rétention pour une base de données dans le :

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- Niveau de service de base est sept jours.
- Niveau de service standard est 35 jours.
- Niveau de service Premium est 35 jours.


Si vous mettre à niveau votre base de données dans les niveaux de service Standard ou Premium de base, les sauvegardes sont enregistrées pendant sept jours. Toutes les sauvegardes existantes plus de sept jours ne sont plus disponibles. 

Si vous mettez à niveau votre base de données à partir du niveau de service de base en Standard ou Premium, base de données SQL conserve les sauvegardes existantes jusqu'à ce qu’ils soient 35 jours. Elle conserve les sauvegardes nouvelle qu’ils se produisent pour 35 jours.
 
Si vous supprimez une base de données, base de données SQL conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, supposons que vous supprimez une base de données simple qui comporte une période de rétention de sept jours. Une sauvegarde quatre jours est enregistrée pendant trois jours.

>[AZURE.IMPORTANT]
    Si vous supprimez le serveur SQL Azure qui héberge les bases de données SQL, toutes les bases de données qui appartiennent au serveur sont également supprimés et ne peuvent pas être récupérés. Vous ne pouvez pas restaurer un serveur supprimé.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Étapes suivantes

Sauvegardes de base de données sont une partie essentielle de toute stratégie récupération continuité et de reprise d’entreprise, car ils protègent vos données contre toute corruption accidentelle ou la suppression. Pour voir comment sauvegardes de bases de données dans une stratégie plus large, voir [vue d’ensemble de la continuité de l’activité entreprise](sql-database-business-continuity.md).


