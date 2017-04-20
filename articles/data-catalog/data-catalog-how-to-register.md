<properties
   pageTitle="Comment enregistrer des sources de données | Microsoft Azure"
   description="Procédure article comment enregistrer des sources de données avec le catalogue de données Azure, y compris les champs de métadonnées extraites lors de l’inscription de mise en surbrillance."
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


# <a name="how-to-register-data-sources"></a>Comment enregistrer des sources de données

## <a name="introduction"></a>Introduction
**Catalogue de données Microsoft Azure** est un service cloud entièrement géré qui sert à un système d’inscription et système de recherche pour les sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** consiste à aider les utilisateurs à découvrir, comprendre et utiliser des sources de données et aider les entreprises pour obtenir plus de valeur à partir de leurs données existantes. Et la première étape pour concevoir une source de données détectable via le **Catalogue de données Azure** consiste à enregistrer cette source de données.
## <a name="registering-data-sources"></a>Enregistrement des sources de données
L’enregistrement est le processus d’extraction de métadonnées à partir de la source de données et copiez ces données dans le service de **Catalogue de données Azure** . Les données restent où elle actuellement réside et reste sous le contrôle des administrateurs et les stratégies du système actuel.

Pour vous inscrire une source de données, lancez simplement l’outil **Catalogue de données Azure** données source d’enregistrement à partir du portail de **Catalogue de données Azure** . Connectez-vous à l’aide de votre téléphone professionnel ou compte scolaire (les mêmes Azure Active Directory informations d’identification que vous utilisez pour vous connecter au portail) et sélectionnez la source de données que vous souhaitez enregistrer.
Consultez le didacticiel [Prise en main catalogue de données Azure](data-catalog-get-started.md) pour plus d’informations détaillées.

Une fois la source de données a été enregistrée, le catalogue effectue le suivi de son emplacement et les index ses métadonnées, afin que les utilisateurs peuvent rechercher, parcourir et découvrez la source de données et puis utiliser son emplacement pour vous connecter à l’aide de l’application ou l’outil de votre choix.

## <a name="sources-supported"></a>Sources prises en charge
Reportez-vous au [DSR de catalogue de données](data-catalog-dsr.md) pour la liste des sources de données prises en charge actuellement.
<br/>


## <a name="structural-metadata"></a>Métadonnées structurelles
Lorsque vous voulez inscrivez une source de données, l’outil d’inscription extraire des informations sur la structure des objets que vous sélectionnez : il s’agit de métadonnées visées aux comme structurels.

Pour tous les objets, ces métadonnées structurelles inclura emplacement de l’objet, afin que les utilisateurs qui découvrir les données peuvent utiliser ces informations pour vous connecter à l’objet dans les outils clients de votre choix. Autres métadonnées structurelles incluent type et nom de l’objet et nom de l’attribut ou de colonne et type de données.

## <a name="descriptive-metadata"></a>Métadonnées descriptives
Outre les métadonnées structurels core extraites de la source de données, l’outil de l’enregistrement de source de données extrait également ainsi que les métadonnées descriptives. Pour SQL Server Analysis Services et SQL Server Reporting Services, il est considérée à partir des propriétés Description exposées par ces services. Pour SQL Server, les valeurs fournies à l’aide de la ms_description propriété étendue seront extraits. Base de données Oracle, l’outil de l’enregistrement de source de données extrait la colonne commentaires à partir de la vue ALL_TAB_COMMENTS.

Outre les métadonnées descriptive extraites de la source de données, les utilisateurs peuvent également entrer des métadonnées descriptives à l’aide de l’outil de l’enregistrement de source de données. Les utilisateurs peuvent ajouter des balises et peuvent identifier des experts pour les objets en cours d’inscription. Toutes ces métadonnées descriptive est copié dans le service de **Catalogue de données Azure** ainsi que les métadonnées structurels.

## <a name="including-previews"></a>Y compris les aperçus

Par défaut, seules les métadonnées sont extraite de sources de données et copiée dans le service de **Catalogue de données Azure** , mais comprendre qu'une source de données est souvent avancée plus facilement par s’affichent un exemple de données qu’il contient.

L’outil de l’enregistrement de source de données **Catalogue de données Azure** permet aux utilisateurs inclure un aperçu instantané des données de chaque table et vue enregistrée. Si l’utilisateur opte complémentaire pour inclure les aperçus lors de l’enregistrement, l’outil d’inscription doit inclure jusqu'à 20 enregistrements à partir de chaque table et la vue. Cet instantané est ensuite copié dans le catalogue ainsi que les métadonnées structurelles et descriptif.


> [AZURE.NOTE]  Tables larges comportant un grand nombre de colonnes peuvent avoir moins de 20 enregistrements inclus dans leur preview.


## <a name="including-data-profiles"></a>Y compris les profils de données

Tout comme notamment aperçus peuvent fournir du contexte précieux pour les utilisateurs de rechercher des sources de données dans le **Catalogue de données Azure**, notamment dans un profil de données peut également facilement à comprendre les sources de données découvertes.

L’outil de l’enregistrement de source de données **Catalogue de données Azure** permet aux utilisateurs d’inclure un profil de données pour chaque table et vue enregistrée. Si l’utilisateur choisit inclure un profil de données lors de l’enregistrement, l’outil d’inscription inclura des statistiques sur les données dans chaque table et la vue, y compris :

* Le nombre de lignes et de taille des données dans l’objet
* La date de la dernière mise à jour des données et le schéma d’objet
* Le nombre d’enregistrements null et des valeurs distinctes pour les colonnes
* Les valeurs minimum, maximum, moyenne et l’écart type de colonnes

Ces statistiques sont alors copiés dans le catalogue ainsi que les métadonnées structurelles et descriptif.

> [AZURE.NOTE]  Colonnes de texte et la date n’incluent pas les statistiques de moyenne ou l’écart-type dans son profil de données.

## <a name="updating-registrations"></a>Mise à jour des enregistrements

Enregistrement d’une source de données facilitera détectable pour la recherche dans le **Catalogue de données Azure** à l’aide de métadonnées et facultatif preview extrait pendant l’enregistrement. Si la source de données doit être mis à jour dans le catalogue (par exemple, si le schéma d’un objet a changé, ou exclus à l’origine de tables doivent être inclus, ou un utilisateur souhaite mettre à jour les données incluses dans les aperçus) l’outil de l’enregistrement de source de données peut être réexécuter.

Inscrit à nouveau une source de données déjà inscrit effectue une opération de fusion « upsert » : objets existants seront mise à jour, tandis que les nouveaux objets seront créés. Toutes les métadonnées fournies par les utilisateurs à partir du portail de **Catalogue de données Azure** seront conservées.

## <a name="summary"></a>Résumé
Enregistrement d’une source de données avec le **Catalogue de données Azure** simplifie cette source de données découvrir et comprendre, en copiant métadonnées structurelles et descriptive à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été enregistrée, il peut ensuite être annotée, gérée et apprendre à l’aide du portail de **Catalogue de données Azure** .

## <a name="see-also"></a>Voir aussi
- [Prise en main catalogue de données Azure](data-catalog-get-started.md) didacticiel pour plus d’informations détaillées sur l’enregistrement des sources de données.
