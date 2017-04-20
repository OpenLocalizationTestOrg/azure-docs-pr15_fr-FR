<properties
   pageTitle="L’utilisation des sources de données « données volumineuses » | Microsoft Azure"
   description="Article de la mise en évidence des motifs pour l’utilisation du catalogue de données Azure des sources de données « données volumineuses », y compris stockage d’objets Blob Azure Azure données Lake et Hadoop HDFS."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>L’utilisation des sources de données volumineux dans le catalogue de données Azure

## <a name="introduction"></a>Introduction
**Catalogue de données Microsoft Azure** est un service cloud entièrement géré qui sert à un système d’inscription et système de recherche pour les sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** est tout sur aidant les utilisateurs à découvrir, comprendre et utiliser des sources de données et aider les entreprises pour obtenir plus de valeur à partir de leurs sources de données existantes, y compris les données volumineux.

**Catalogue de données Azure** prend en charge l’enregistrement des objets BLOB Azure Blog stockage et des répertoires ainsi que des fichiers Hadoop HDFS et des répertoires. La nature semi-structurées de ces sources de données offre une grande flexibilité, mais cela signifie également que les utilisateurs doivent envisager comment les sources de données sont organisées afin de tirer le meilleur parti de leur enregistrement dans le **Catalogue de données Azure**.

## <a name="directories-as-logical-data-sets"></a>Annuaires comme logique jeux de données

Un modèle commun pour organiser les sources de données volumineux consiste à traiter les répertoires comme logique jeux de données. Répertoires de niveau supérieur sont utilisées pour définir un jeu de données, alors que les sous-dossiers définissent des partitions, et les fichiers qu’ils contiennent stockent les données lui-même.

Un exemple de ce modèle peut être :

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

Dans cet exemple, vehicle_maintenance_events et location_tracking_events représentent des ensembles de données logiques. Chacun de ces dossiers contient des fichiers de données qui sont organisées par année et mois dans des sous-dossiers. Chacun de ces dossiers peut contenir des centaines ou des milliers de fichiers.

Dans ce modèle, enregistrement des fichiers individuels avec **Catalogue de données Azure** probablement inutile. Inscrivez-vous à la place, les répertoires qui représentent les jeux de données qui sera significatifs pour les utilisateurs de travailler avec les données.

## <a name="reference-data-files"></a>Fichiers de données de référence

Un modèle complémentaire consiste à stocker des jeux de données de référence sous forme de fichiers individuels. Ces jeux de données peut être considéré comme le côté « small » de données volumineux et sont souvent similaires aux dimensions dans un modèle de données analytique. Fichiers de données de référence contiennent des enregistrements qui sont utilisés pour fournir du contexte pour l’essentiel des fichiers de données stockées dans le magasin de données volumineux.

Un exemple de ce modèle peut être :

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Lorsqu’un scientifique analyste ou données fonctionne avec les données contenues dans les grandes structures de répertoire, les données dans ces fichiers référence peuvent être utilisées pour fournir des informations plus détaillées pour entités qui font référence uniquement par nom ou ID dans le jeu de données plus grande.

Dans ce modèle, cela facilitera préférable d’enregistrer les fichiers de données individuelle de référence avec le **Catalogue de données Azure**. Chaque fichier représente un jeu de données, et chacune d’elles peut être annoté et découvert individuellement.

## <a name="alternate-patterns"></a>Modèles de substitution

Les modèles ci-dessus simplement de deux manières possibles qu'un magasin de données volumineuses peut-être être organisé, mais chaque implémentation sera différente. Quelle que soit la façon dont vos sources de données sont structurées, lors de l’inscription des sources de données volumineux avec le **Catalogue de données Azure**, vous concentrer sur l’enregistrement de fichiers et des répertoires qui représentent les jeux de données qui sera de valeur à d’autres personnes au sein de votre organisation. Enregistrement de tous les fichiers et des répertoires peut encombrer le catalogue, ce qui complique permettant aux utilisateurs de trouver qu’ils ont besoin.

## <a name="summary"></a>Résumé
Enregistrement de sources de données avec le **Catalogue de données Azure** facilite les découvrir et de comprendre. En enregistrant et annotation des fichiers de données volumineux et des répertoires qui représentent des ensembles de données logiques, vous pouvez aider les utilisateurs à rechercher et utiliser les sources de données volumineux que dont elles ont besoin.
