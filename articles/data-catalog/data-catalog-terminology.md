<properties
   pageTitle="Terminologie de catalogue de données Azure | Microsoft Azure"
   description="Cet article fournit une introduction aux concepts et termes utilisés dans la documentation de catalogue de données Azure."
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

# <a name="azure-data-catalog-terminology"></a>Terminologie de catalogue de données Azure

## <a name="catalog"></a>Catalogue

Le catalogue de données Azure est un référentiel de métadonnées sur le nuage dans lequel les données sources et aux données actifs peuvent être enregistrés. Le catalogue constitue un emplacement de stockage central pour les métadonnées structurelles extraites des sources de données et pour les métadonnées descriptive ajoutées par les utilisateurs.

## <a name="data-source"></a>Source de données

Une source de données est un système ou un conteneur qui gère les données de l’entreprise. Exemples bases de données SQL Server, les bases de données Oracle, les bases de données SQL Server Analysis Services (tabulaires ou multidimensionnels) et les serveurs de SQL Server Reporting Services.

## <a name="data-asset"></a>Ressources de données

Ressources de données sont des objets contenus dans les sources de données qui peuvent être enregistrés avec le catalogue. Exemples de tables SQL Server et vues, tables Oracle et affichages, SQL Server Analysis Services mesures, les dimensions et indicateurs de performance clés et rapports SQL Server Reporting Services.

## <a name="data-asset-location"></a>Emplacement de biens de données

Le catalogue stocke l’emplacement d’une source de données ou un élément de données, qui peut être utilisé pour vous connecter à la source à l’aide d’une application cliente. La forme et les détails de l’emplacement varient en fonction du type de source de données. Par exemple, une table SQL Server peut être identifiée par son nom quatre parties – nom du serveur, nom de la base de données, schéma, nom de l’objet – pendant un rapport SQL Server Reporting Services peut être identifié par son URL.

## <a name="structural-metadata"></a>Métadonnées structurelles

Métadonnées structurelles sont les métadonnées extraites d’une source de données qui décrivent la structure d’un bien de données. Cela inclut l’emplacement de biens, son nom de l’objet et type et autres caractéristiques spécifiques au type. Par exemple, les métadonnées pour les tables et affichages structurelles incluent les noms et les types de données pour les colonnes de l’objet.

## <a name="descriptive-metadata"></a>Métadonnées descriptives

Métadonnées descriptives sont métadonnées qui décrivent l’objectif ou l’intention d’un bien de données. En règle générale métadonnées descriptives sont ajoutée par les utilisateurs de catalogue à l’aide du portail de catalogue de données Azure, mais il peut également extrait à partir de la source de données lors de l’enregistrement. Par exemple, l’outil d’inscription catalogue de données Azure extrait des descriptions à partir de la propriété Description dans SQL Server Analysis Services et SQL Server Reporting Services et de la [propriété étendue de ms_description](https://technet.microsoft.com/library/ms190243.aspx) dans les bases de données SQL Server, si ces propriétés ont été remplies avec des valeurs.

## <a name="request-access"></a>Demander l’accès

Métadonnées décrivant d’un élément données peuvent inclure des informations sur la façon de demander l’accès à la source de données ou bien des données. Ces informations sont présentées à l’emplacement de biens de données et peuvent inclure une ou plusieurs des options suivantes :

- L’adresse de messagerie de l’utilisateur ou l’équipe en charge pour accorder l’accès à la source de données.
- L’URL du processus de documentation que les utilisateurs doivent suivre pour accéder à la source de données.
- L’URL d’un outil de gestion des identités et des accès (tel que Microsoft identité Manager) pouvant être utilisées pour accéder à la source de données.
- Une entrée de texte libre qui décrit comment les utilisateurs peuvent accéder à la source de données.

## <a name="preview"></a>Aperçu

Un aperçu dans le catalogue de données Azure est un instantané de jusqu'à 20 enregistrements qui peuvent être extraits de la source de données lors de l’inscription et stockés dans le catalogue avec les métadonnées de biens de données. L’aperçu peut aider les utilisateurs qui découvrir un élément de données mieux comprennent fonction et son objectif. En d’autres termes, voir des exemples de données peut être plus utile que s’affichent les noms de colonnes et des types de données.
Aperçus sont uniquement pris en charge pour les tables et affichages et doivent être explicitement sélectionnés par l’utilisateur lors de l’enregistrement.

## <a name="data-profile"></a>Profil de données

Un profil de données dans le catalogue de données Azure est un instantané de niveau de table et au niveau des colonnes métadonnées relatives à un élément de données enregistré qui peut être extraits de la source de données lors de l’inscription et stocké dans le catalogue avec les métadonnées de biens de données. Le profil de données peut aider les utilisateurs qui découvrir un élément de données mieux comprennent fonction et son objectif. Comme pour les aperçus, des profils de données doivent être explicitement sélectionnés par l’utilisateur lors de l’enregistrement.

> [AZURE.NOTE] Extraire un profil de données peut être une opération coûteuse pour les affichages et les grands tableaux et peut augmenter considérablement le temps nécessaire pour enregistrer une source de données.

## <a name="user-perspective"></a>Point de vue utilisateur

Dans le catalogue de données Azure, tous les utilisateurs peuvent fournir des métadonnées descriptives pour un élément de données enregistré. Chaque utilisateur possède une perspective distincte sur les données et son utilisation. Par exemple, l’administrateur responsable à un serveur peut fournir les détails de son niveau accord de service () ou les fenêtres de sauvegarde ; un gestionnaires de données peuvent fournir des liens vers la documentation de l’entreprise traite le prend en charge de données ; et un analyste peut-être fournir une description dans les conditions qui peuvent être utiles à d’autres analystes et qui peut être plus précieux aux utilisateurs qui ont besoin de découvrir et de comprendre les données.

Chacun de ces perspectives sont fondamentalement utiles, puis avec le catalogue de données Azure chaque utilisateur peut fournir les informations qui sont un sens pour les, tandis que tous les utilisateurs peuvent utiliser ces informations pour comprendre les données et son objectif.

## <a name="expert"></a>Expert

Un expert est un utilisateur qui a été identifié comme ayant une perspective informée « experte » pour un élément de données. Tous les utilisateurs peuvent ajouter eux-mêmes ou un autre utilisateur en tant qu’un expert pour un bien. Être répertoriée en tant qu’un expert a-t-elle pas les privilèges supplémentaires dans le catalogue de données Azure ; Il permet aux utilisateurs de trouver facilement ces perspectives sont susceptibles de s’avérer utile lorsque vous examinez les métadonnées descriptives d’un élément.

## <a name="owner"></a>Propriétaire

Un propriétaire est un utilisateur qui possède des privilèges supplémentaires pour la gestion d’un élément de données dans le catalogue de données Azure. Les utilisateurs peuvent prendre possession d’éléments de données enregistré et propriétaires peuvent ajouter d’autres utilisateurs en tant que collègue propriétaires. Pour plus d’informations, voir [comment gérer les ressources de données](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] La propriété et la gestion sont disponibles uniquement dans le Standard Edition de Azure catalogue de données.

## <a name="registration"></a>Enregistrement

L’enregistrement consiste à extraire des données biens métadonnées à partir d’une source de données et copiez-le dans le service de catalogue de données Azure. Ressources de données qui ont été enregistrés peuvent être annotés et découvert.

## <a name="see-also"></a>Voir aussi

- [Quel est le catalogue de données Azure ?](data-catalog-what-is-data-catalog.md) -Cet article fournit une vue d’ensemble du service catalogue de données Azure, les avantages et les scénarios que qui prend en charge.

- [Prise en main catalogue de données Azure](data-catalog-get-started.md) : cet article fournit un didacticiel de bout en bout qui vous montre comment utiliser le catalogue de données Azure pour la découverte de source de données.  
