<properties
    pageTitle="Créer un index de recherche Azure à l’aide du portail Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer un index à l’aide du portail Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Créer un index de recherche Azure à l’aide du portail Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)

Cet article vous guidera tout au long du processus de création d’une recherche Azure [index](search-what-is-an-index.md) à l’aide du portail Azure.

Avant de suivre ce guide et création d’un index, dont vous avez déjà [créé un service de recherche Azure](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>J’ai. Accédez à votre carte recherche Azure
1. Cliquez sur « Toutes les ressources » dans le menu sur le côté gauche du [Portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Sélectionnez votre service de recherche d’Azure

## <a name="ii-add-and-name-your-index"></a>II. Ajouter et nommer votre index
1. Cliquez sur le bouton « Ajouter index »
2. Nommez votre index de recherche Azure. Étant donné que nous allons créer un index pour effectuer une recherche pour l’hébergement dans ce guide, nous avons nommé notre index « hôtel ».
  * Le nom de l’index doit commencer par une lettre et contenir uniquement des lettres minuscules, des chiffres ou des tirets («- »).
  * Semblable au nom de votre service, le nom de l’index que vous avez choisi seront également partie de l’URL du point de terminaison où vous envoyez vos demandes HTTP pour l’API de recherche Azure
3. Cliquez sur l’entrée « Champs » pour ouvrir une nouvelle carte

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Créer et définir les champs de votre index
1. En sélectionnant l’entrée « Champs », une nouvelle carte s’ouvre avec un formulaire pour entrer la définition de votre index.
2. Ajoutez des champs à votre index à l’aide de l’écran.

  * Un champ de *clé* de type Edm.String est obligatoire pour chaque index de recherche Azure. Ce champ de clé est créé par défaut avec le champ nom « id ». Nous avons modifié celle-ci à « hotelId » dans notre index.
  * Certaines propriétés de votre schéma d’index ne peuvent être définies une fois et ne peut pas être mis à jour à l’avenir. Pour cette raison, des mises à jour de schéma qui nécessitent la réindexation telles que la modification des types de champs ne sont pas actuellement possibles après la configuration initiale.
  * Nous avons choisi avec soin les valeurs de propriété pour chaque champ basée sur la manière dont nous ils seront utilisés dans une application. N’oubliez pas votre recherche utilisateur expérience et des besoins lorsque vous créez votre index comme les [propriétés appropriées](https://msdn.microsoft.com/library/azure/dn798941.aspx)doit être affectée à chaque champ. Ces propriétés le contrôle qui fonctions (filtrage, faceting, tri, recherche de texte intégral, etc.) de recherche appliquer des champs. Par exemple, il est probable que les personnes recherchant hôtel seront intéressés par de mot clé correspondances trouvées dans le champ « description », afin de nous permettre de recherche en texte intégral de ce champ en définissant la propriété « Possibilité de recherche ».
    * Vous pouvez également définir l' [Analyseur de langue](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) pour chaque champ en cliquant sur l’onglet « Analyzer » dans la partie supérieure de la cuillère. Vous pouvez le voir ci-dessous que nous avons sélectionné un analyseur Français pour un champ dans notre index destiné à texte Français.

3. Cliquez sur **OK** dans la carte « Champs » pour confirmer vos définitions de champ
4. Cliquez sur **OK** dans la carte « Index Ajouter » pour enregistrer et créer l’index que vous venez de définir.

Dans les captures d’écran ci-dessous, vous pouvez voir comment nous avons nommé et défini les champs pour notre index « hôtel ».

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Suivant
Après avoir créé un index de recherche Azure, vous serez prêt à [télécharger votre contenu dans l’index](search-what-is-data-import.md) afin que vous pouvez démarrer la recherche de vos données.
