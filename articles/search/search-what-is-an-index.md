<properties
    pageTitle="Créer un index de recherche Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="Qu’est un index dans Azure recherche et comment l’utiliser ?"
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index"></a>Créer un index de recherche Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>Qu’est un index ?

Un *index* est une banque permanente des *documents* et autres constructions utilisées par un service de recherche Azure. Un document est une seule unité de données interrogeables dans votre index. Par exemple, un revendeur de commerce peut-être un document pour chaque élément qu'ils vendent, une organisation de news peut avoir un document pour chaque article, etc.. Mappage ces concepts plus convivial équivalents de base de données : un *index* est équivalent à une *table*et *les documents* sont globalement équivalentes à des *lignes* dans une table.

Lorsque vous ajoutez/télécharger des documents et soumettez des requêtes de recherche à la recherche Azure, vous soumettez vos demandes vers un index spécifique dans votre service de recherche.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Types de champs et les attributs dans un index de recherche Azure

Lorsque vous définissez votre schéma, vous devez spécifier le nom, type et attributs de chaque champ dans votre index. Le type de champ considère les données qui sont stockées dans ce champ. Attributs sont définis sur les champs individuels pour spécifier la manière dont le champ est utilisé. Les tableaux suivants énumérer les types et les attributs que vous pouvez spécifier.


### <a name="field-types"></a>Types de champs
|Type|Description|
|------------|-----------|
|*Edm.String*|Texte qui peut éventuellement être sous forme de jetons pour la recherche en texte intégral (césure, radical, etc.).|
|*Collection(EDM.String)*|Une liste de chaînes peuvent éventuellement être sous forme de jetons pour la recherche en texte intégral. Il existe une limite théorique sur le nombre d’éléments dans une collection de sites, mais la limite supérieure 16 Mo sur la taille de la charge utile s’applique aux collections de sites.|
|*Edm.Boolean*|Contient des valeurs vrai/faux.|
|*Edm.Int32*|valeurs d’entier 32 bits.|
|*Edm.Int64*|valeurs d’entier 64 bits.|
|*Edm.Double*|Double précision des données numériques.|
|*Edm.DateTimeOffset*|Les valeurs d’heure représentées au format OData V4 de date (par exemple, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Un point représentant un emplacement géographique dans le monde.|

Vous trouverez plus d’informations à propos [pris en charge les types de données sur MSDN de la recherche Azure](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Attributs d’un champ
|Attribut|Description|
|------------|-----------|
|*Clé*|Chaîne qui fournit l’identificateur unique de chaque document, utilisé pour la recherche de document. Chaque index doit avoir une clé. Qu’un seul champ peut être la touche et son type doit être définie sur Edm.String.|
|*Récupérables*|Indique si un champ peut être retourné dans un résultat de recherche.|
|*Filtrables*|Permet du champ à utiliser dans les requêtes de filtre.|
|*Triable*|Permet à une requête trier les résultats de recherche à l’aide de ce champ.|
|*Facetable*|Permet à un champ à utiliser dans une structure de [navigation par facettes](search-faceted-navigation.md) pour le filtrage automatique suggérés utilisateur. En règle générale champs qui contiennent des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents qui font partie de la catégorie de services ou une marque unique) fonctionnent mieux en tant que facettes.|
|*Disponible pour la recherche*|Marque le champ en tant que texte intégral que vous pourrez rechercher.|

Vous trouverez plus d’informations à propos de la recherche Azure [attributs d’index sur MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## <a name="guidance-for-defining-an-index-schema"></a>Conseils pour la définition d’un schéma d’index

Lorsque vous créez votre index, prenez votre temps dans la phase de planification pour y réfléchir chaque décision. Il est important de maintenir votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous créez votre index comme les [attributs corrects](https://msdn.microsoft.com/library/azure/dn798941.aspx)doit être affectée à chaque champ. Modifier un index après que qu’il est déployé implique reconstruction et recharger les données.


Si les besoins de stockage de données changent au fil du temps, vous pouvez augmenter ou diminuer la capacité en ajoutant ou en supprimant des partitions. Pour plus d’informations, voir [Gérer votre service de recherche dans Azure](search-manage.md) ou les [Limites de Service](search-limits-quotas-capacity.md).
