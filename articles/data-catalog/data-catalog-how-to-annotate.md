<properties
   pageTitle="Comment annoter des sources de données | Microsoft Azure"
   description="Procédure article comment annoter les éléments de données dans le catalogue de données Azure, y compris des experts, balises, description et un nom convivial de mise en surbrillance."
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
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="how-to-annotate-data-sources"></a>Comment annoter des sources de données

## <a name="introduction"></a>Introduction
**Catalogue de données Microsoft Azure** est un service cloud entièrement géré qui sert à un système d’inscription et système de recherche pour les sources de données d’entreprise. En d’autres termes, le catalogue de données consiste à aider les utilisateurs à découvrir, comprendre et utiliser des sources de données et aider les entreprises pour obtenir plus de valeur à partir de leurs données existantes. Lorsqu’une source de données est enregistrée avec le catalogue de données, ses métadonnées sont copiée et indexées par le service, mais le scénario ne se termine pas il. Catalogue de données permet aux utilisateurs de fournir des leurs propres métadonnées descriptives – tels que des descriptions et des marqueurs – à compléter les métadonnées extraites de la source de données et à effectuer la source de données plus compréhensible à d’autres personnes.

## <a name="annotation-and-crowdsourcing"></a>Annotation et marketing participatif
Tout le monde a un avis. Et Voici une bonne chose.
Catalogue de données reconnaît que différents utilisateurs ont différentes perspectives sur les sources de données d’entreprise, et que chacune de ces perspectives peut être utile. Considérez le scénario suivant :

* L’administrateur système connaît le contrat de niveau de service pour les serveurs ou services hébergeant la source de données.
* L’administrateur de base de données connaît le planning de sauvegarde pour chaque base de données et les fenêtres de traitement ETL autorisés.
* Le propriétaire du système connaît le processus permettant aux utilisateurs de demander l’accès à la source de données.
* Les gestionnaires de données sait comment les biens et les attributs dans la source de données établir une correspondance entre le modèle de données d’entreprise.
* L’analyste sait comment les données sont utilisées dans le contexte des processus métiers qu'il prend en charge.

Chacun de ces perspectives est précieux et catalogue de données utilise une approche marketing participatif des métadonnées qui permet de chacun d'entre eux d’être capturé et utilisé pour fournir une image complète des sources de données enregistrées. À l’aide du portail de catalogue de données, chaque utilisateur peut ajouter et modifier son propre annotations, tout en étant en mesure d’afficher les annotations fournies par d’autres utilisateurs.

## <a name="different-types-of-annotations"></a>Différents types d’annotations
Catalogue de données prend en charge les types d’annotations suivants :

| Annotation     | Notes                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom convivial  | Nom convivial peut être fourni au niveau des biens données, pour rendre les biens de données plus facilement compréhensibles. Nom convivial est très utiles lorsque le nom de l’objet sous-jacent est appui, abrégé ou dans le cas contraire pas significatif pour les utilisateurs.                                                                                                                            |
| Description    | Descriptions peuvent être fournies au bien de données et l’attribut / niveaux de colonne. Descriptions sont des annotations texte court forme libre qui décrivent l’utilisateur perspective sur les biens de données ou de son utilisation.                                                                                                                                                              |
| Tags (balises d’utilisateur)          | Balises peuvent être fournis à la ressource de données et l’attribut / niveaux de colonne. Les balises utilisateur sont étiquettes définies par l’utilisateur qui peuvent être utilisés pour classer les données de l’entreprise ou des attributs.                                                                                                                                                                                                    |
| Tags (balises glossaire)          | Balises peuvent être fournis à la ressource de données et l’attribut / niveaux de colonne. Glossaire balises sont définies de manière centralisée glossaire qui peut être utilisé pour classer les données de l’entreprise ou des attributs à l’aide d’une classification d’entreprise courants. Pour plus d’informations, voir [comment configurer la glossaire Business pour régis marquage](data-catalog-how-to-business-glossary.md)                                                                                                                                                                                                    |
| Experts        | Experts peuvent être fournis au niveau des biens données. Experts identifier les utilisateurs ou groupes des perspectives experts sur les données et peuvent servir de point de contact pour les utilisateurs de découvrir les sources de données enregistré et avez des questions qui ne sont pas traitées par les annotations existantes.  |
| Demander l’accès | Informations de demande d’accès peuvent être fournies au niveau des biens données. Ces informations sont destinées aux utilisateurs qui découvrir une source de données qu’ils n’ont pas encore des autorisations d’accès. Les utilisateurs peuvent entrer l’adresse de messagerie de l’utilisateur ou groupe qui accorde l’accès, l’URL de l’outil que les utilisateurs doivent pouvoir accéder, ou un processus ou peuvent entrer le processus de lui-même sous forme de texte. |
| Documentation | Documentation peut être fournie au niveau des biens données. Documentation de biens est information de texte enrichi qui peut inclure des liens et des images et qui peut fournir les informations ne véhiculées pas par le biais des descriptions et balises. |


## <a name="annotating-multiple-assets"></a>Annotées plusieurs actifs
Lorsque vous sélectionnez plusieurs éléments de données dans le portail du catalogue de données, les utilisateurs peuvent annoter tous les éléments sélectionnés en une seule opération. Annotations seront appliquent à tous les actifs sélectionnés, ce qui facilite sélectionner et fournir une description cohérente et jeux de balises et des experts pour les éléments de données associées.

> [AZURE.NOTE] Balises et des experts peuvent également être fournis lorsque inscription ressources de données en utilisant les données de catalogue de données source outil d’inscription.

Lorsque la sélection de plusieurs tables et des vues, seules les colonnes que toutes les données biens ont en commun sélectionnées apparaît dans le portail du catalogue de données. Cela permet aux utilisateurs de fournir des balises et les descriptions de toutes les colonnes portant le même nom pour tous les éléments sélectionnés.

## <a name="annotations-and-discovery"></a>Découverte et annotations
Tout comme les métadonnées extraites de la source de données lors de l’enregistrement sont ajoutée à l’index de recherche de catalogue de données, métadonnées fourni par l’utilisateur sont également indexée. Cela signifie que non seulement annotations facilement aux utilisateurs de comprendre les données qu’ils découvrent, annotations également facilement aux utilisateurs de découvrir les biens données annotés en effectuant une recherche en utilisant les termes représentatives leur.

## <a name="summary"></a>Résumé
Enregistrement d’une source de données avec le catalogue de données rend ces données détectable en copiant métadonnées structurelles et descriptive à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été enregistrée, les utilisateurs peuvent fournir des annotations à rendre plus facile à découvrir et de comprendre à partir du portail de catalogue de données.

## <a name="see-also"></a>Voir aussi
- [Prise en main catalogue de données Azure](data-catalog-get-started.md) didacticiel pour plus d’informations détaillées sur la façon d’annoter des sources de données.
