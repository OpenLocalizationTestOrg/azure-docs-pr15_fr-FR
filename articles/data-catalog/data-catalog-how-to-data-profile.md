<properties
    pageTitle="Comment les sources de données de profil de données"
    description="Article procédure montrant comment faire pour inclure des profils de données au niveau de la table et de la colonne lors de l’inscription des sources de données dans le catalogue de données Azure et comment utiliser des profils de données pour comprendre les sources de données."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Sources de données de profil de données

## <a name="introduction"></a>Introduction

**Catalogue de données Microsoft Azure** est un service cloud entièrement géré qui sert à un système d’inscription et système de recherche pour les sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** consiste à aider les utilisateurs à découvrir, comprendre et utiliser des sources de données et aider les entreprises pour obtenir plus de valeur à partir de leurs données existantes. Lorsqu’une source de données est enregistrée avec le **Catalogue de données Azure**, ses métadonnées sont copiée et indexées par le service, mais le scénario ne se termine pas il.

La fonctionnalité de **Profil de données** de **Catalogue de données Azure** examine les données provenant de sources de données prises en charge dans votre catalogue et collecte de statistiques et les informations relatives à ces données. Il est facile d’inclure un profil de vie de vos données. Lorsque vous enregistrez un élément de données, sélectionnez **Profil de données inclure** dans l’outil de l’enregistrement de source de données.

## <a name="what-is-data-profiling"></a>Quelles sont les données de profil

Profil de données examine les données dans la source de données en cours d’inscription et collecte de statistiques et les informations relatives à ces données. Lors de la découverte de source de données, ces statistiques peuvent vous aider à déterminer la pertinence des données à résoudre leur problème de l’entreprise.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Sources de données suivantes prennent en charge le profil de données :

- Vues et les tables SQL Server (y compris la base de données SQL Azure et Azure SQL Data Warehouse)
- Vues et les tables oracle
- Teradata tables et affichages
- Tables Hive

Y compris des profils de données lors de l’enregistrement des données actifs permet aux utilisateurs de répondre aux questions sur les sources de données, notamment :

-   Peut être utilisé pour résoudre le problème de mon entreprise ?
-   Les données sont conformes aux normes particuliers ou des motifs ?
-   Quelles sont les anomalies de la source de données ?
-   Quels sont les défis possibles de l’intégration de ces données dans mon application ?

> [AZURE.NOTE] Vous pouvez également ajouter documentation à un bien pour décrire comment les données peuvent être intégrées à une application. Découvrez [comment documenter des sources de données](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Comment faire pour inclure un profil de données lors de l’enregistrement d’une source de données

Il est facile d’inclure un profil de votre source de données. Lorsque vous enregistrez une source de données, dans le volet **objets pour être enregistrés** de l’outil de l’enregistrement de source de données, sélectionnez **Inclure un profil de données**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Pour plus d’informations sur l’inscription des sources de données, voir [comment enregistrer des sources de données](data-catalog-how-to-register.md) et [commencer à utiliser le catalogue de données Azure](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrage sur les éléments de données qui incluent des profils de données
Pour découvrir les ressources de données qui incluent un profil de données, vous pouvez inclure `has:tableDataProfiles` ou `has:columnsDataProfiles` dans l’un des termes recherchés.

> [AZURE.NOTE] Sélection de **Profil de données inclure** dans l’outil de l’enregistrement de source de données inclut les tables et les informations de profil au niveau des colonnes. Toutefois, l’API de catalogue de données permet aux données de l’entreprise accepte qu’un seul ensemble d’informations de profil incluses.

## <a name="viewing-data-profile-information"></a>Affichage des informations de profil de données

Une fois que vous avez trouvé une source de données appropriée avec un profil, vous pouvez afficher les détails de profil de données. Pour afficher le profil de données, sélectionnez un élément de données, dans la fenêtre du portail de catalogue de données, sélectionnez **Profil de données** .

![](media\data-catalog-data-profile\data-catalog-view.png)

Un profil de données dans le **Catalogue de données Azure** illustre la table et des informations de profil de colonne, y compris :

### <a name="object-data-profile"></a>Profil de données objet

-   Nombre de lignes
-   Taille du tableau
-   Lorsque l’objet a été mis à jour

### <a name="column-data-profile"></a>Profil de données de colonne

- Type de données de colonne
- Nombre de valeurs distinctes
- Nombre de lignes avec des valeurs NULL
- Minimum, maximum, moyenne et l’écart type pour les valeurs de colonne

## <a name="summary"></a>Résumé
Données profil fournissent des statistiques et des informations sur les éléments de données enregistré pour vous aider à déterminer la pertinence des données pour résoudre les problèmes d’entreprise. En même temps qu’annotées et la documentation de sources de données, des profils de données permettent aux utilisateurs d’une bonne compréhension de vos données.


## <a name="see-also"></a>Voir aussi
-   [Comment enregistrer des sources de données](data-catalog-how-to-register.md)
-   [Prise en main catalogue de données Azure](data-catalog-get-started.md)
