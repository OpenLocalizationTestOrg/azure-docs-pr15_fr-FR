<properties
    pageTitle="Collecte de données pour former votre modèle : recommandations API d’apprentissage de l’ordinateur | Microsoft Azure"
    description="Machine Azure recommandations d’apprentissage - collecte des données pour former votre modèle"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>Collecte de données pour l’apprentissage votre modèle #

L’API recommandations apprend au fur à partir de vos transactions passées pour rechercher des éléments doivent être recommandés pour un utilisateur particulier.

Après avoir créé un modèle, vous devrez fournir deux éléments d’information avant de pouvoir effectuer toutes les formations : un fichier de catalogue et les données d’utilisation.

>   Si vous n’avez pas déjà fait, nous vous invitons pour terminer le [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Les données de catalogue ##

### <a name="catalog-file-format"></a>Format de fichier catalogue ###

Le fichier catalogue contient des informations sur les éléments que vous proposez à votre client.
Les données du catalogue doivent suivre le format suivant :

- Sans fonctionnalités-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Fonctionnalités-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Exemples de lignes dans un fichier de catalogue

Sans fonctionnalités :

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Avec des fonctionnalités :

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Détails sur le format

| Nom | Obligatoire | Type |  Description |
|:---|:---|:---|:---|
| Id d’élément |Oui | [A-z], [a-z], [0-9], [_] & #40 ; Trait de soulignement & #41 ; [-] & #40 ; tiret & #41 ;<br> Longueur maximale : 50 | Identificateur unique d’un élément. |
| Nom de l’élément | Oui | Tous les caractères alphanumériques<br> Longueur maximale : 255 | Nom de l’élément. |
| Catégorie de l’élément | Oui | Tous les caractères alphanumériques <br> Longueur maximale : 255 | Catégorie à laquelle cet élément appartient (par exemple, cuisine carnets films...) ; peut être vide. |
| Description | Non, sauf si les fonctionnalités sont présenter (mais ne peut être vide) | Tous les caractères alphanumériques <br> Longueur maximale : et 4 000 | Description de cet article. |
| Liste des fonctionnalités | N° | Tous les caractères alphanumériques <br> Longueur maximale : 4000 ; Nombre maximal de fonctionnalités : 20 | Liste séparées par des virgules de nom de la fonctionnalité = valeur fonctionnalité qui peut être utilisé pour améliorer les recommandations de modèle.|

#### <a name="uploading-a-catalog-file"></a>Téléchargement d’un fichier de catalogue

Examinez la [référence de l’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) pour le téléchargement d’un fichier de catalogue.  

Notez que le contenu du fichier catalogue doit être passé en tant que corps de la demande.

Si vous téléchargez plusieurs fichiers de catalogue vers le même modèle avec plusieurs appels, nous insère les nouveaux éléments de catalogue. Éléments existants restent avec les valeurs d’origine. Vous ne pouvez pas mettre à jour les données du catalogue à l’aide de cette méthode.

>   Remarque : La taille de fichier maximale est de 200 Mo.
>   Le nombre maximal d’éléments dans le catalogue pris en charge est 100 000 éléments.


## <a name="why-add-features-to-the-catalog"></a>Pourquoi ajouter des fonctionnalités dans le catalogue ?

Le moteur de recommandations crée un modèle statistique qui vous indique quels éléments sont susceptibles d’être aimé ou acheté par un client. Lorsque vous avez un nouveau produit qui a jamais été possible d’interagir avec celui-ci n’est pas possible de créer un modèle dans les occurrences de co-création seul. Supposons que vous démarrez offrant une nouvelle « violon enfants » dans votre magasin, étant donné que vous n’avez jamais vendu ce violon avant que vous ne savez pas quelles autres éléments à recommander avec ce violon.

Ceci étant dit, si le moteur sait d’informations sur cette violon (c'est-à-dire qu’il est un instrument de musique, il est pour enfants âges 7-10, il n’est pas un violon coûteux, etc.), puis le moteur pouvez Découvrez d’autres produits avec des fonctionnalités similaires. Par exemple, vous avez vendu de violon par le passé et en règle générale, les personnes qui achètent violons tendent à acheter CD de musique classique et béquilles musique feuille.  Le système peut trouver ces connexions entre les fonctions et des recommandations basées sur les fonctionnalités alors que votre nouveau violon a peu de l’utilisation.

Fonctionnalités sont importées dans le cadre des données de catalogue, et puis les leur rang (ou l’importance de la fonctionnalité dans le modèle) est associée à un classement. Fonction rang modifiables selon le modèle de données d’utilisation et le type d’éléments. Mais pour l’utilisation cohérentes/éléments, le rang doit avoir uniquement petites variations. Le rang des fonctionnalités est un nombre non négatif. Le chiffre 0 signifie que la fonctionnalité n’a pas classée (qui se produit si vous appelez cette API avant la fin de la première génération de classement). La date à laquelle le rang a été attribué est appelée l’actualisation score.


###<a name="features-are-categorical"></a>Fonctionnalités sont Categorical

Cela signifie que vous devez créer des fonctionnalités ressemblant à une catégorie. Par exemple, prix = 9.34 n’est pas une fonctionnalité par catégorie. En revanche, une fonctionnalité comme priceRange = Under5Dollars est une fonctionnalité par catégorie. Un autre courante consiste à utiliser le nom de l’élément comme une fonctionnalité. Cela entraînera le nom d’un élément à être unique afin qu’il décrivent pas une catégorie. Vérifiez que les fonctionnalités représentent des catégories d’éléments.


###<a name="how-manywhich-features-should-i-use"></a>Fonctionnalités comment plusieurs/qui dois-je utiliser ?


En définitive les recommandations créer prend en charge la création d’un modèle avec jusqu'à 20 fonctionnalités. Vous pouvez affecter plus de 20 fonctionnalités pour les éléments dans votre catalogue, mais vous devez effectuer une génération de classement, puis sélectionnez uniquement les fonctionnalités figurant haute. (Une fonctionnalité avec un classement de 2.0 ou plus est une fonctionnalité très bonne utiliser !). 


###<a name="when-are-features-actually-used"></a>Lorsque les fonctionnalités réellement utilisées ?

Fonctionnalités sont utilisées par le modèle lorsqu’il y n'a pas suffisamment de données transaction pour fournir des recommandations sur informations sur les transactions uniquement. Donc fonctionnalités peuvent avoir le plus grand impact sur « éléments froid » – articles avec peu de transactions. Si tous vos éléments dispose de suffisamment d’informations transaction vous devrez pas enrichir votre modèle avec des fonctionnalités.


###<a name="using-product-features"></a>À l’aide des fonctionnalités de produit

Utilisation des fonctionnalités dans le cadre de votre génération, que vous devez :

1. Vérifiez que votre catalogue comporte des fonctionnalités lorsque vous le télécharger.

2. Déclencher la lecture d’une version de classement. Cela ne l’analyse de l’importance/rang des fonctionnalités.

3. Déclencher la lecture d’une version de recommandations, paramètres de génération définissant les éléments suivants : allowColdItemPlacement vrai, vrai, la valeur useFeaturesInModel et modelingFeatureList doit être définie avec la liste séparées par des virgules des fonctionnalités que vous souhaitez utiliser pour améliorer votre modèle. Pour plus d’informations, voir [recommandations générer les paramètres de type](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Données d’utilisation ##
Un fichier de l’utilisation contient des informations sur la façon dont ces éléments sont utilisés, ou les transactions à partir de votre entreprise.

#### <a name="usage-format-details"></a>Détails de Format de l’utilisation
Un fichier de l’utilisation est un fichier CSV (valeurs séparées par des virgules) où chaque ligne dans un fichier de l’utilisation représente une interaction entre un utilisateur et un élément. Chaque ligne est mis en forme comme suit :<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Nom  | Obligatoire | Type | Description
|-------|------------|------|---------------
|Id d’utilisateur|         Oui|[A-z], [a-z], [0-9], [_] & #40 ; Trait de soulignement & #41 ; [-] & #40 ; tiret & #41 ;<br> Longueur maximale : 255 |Identificateur unique d’un utilisateur.
|Id d’élément|Oui|[A-z], [a-z], [0-9], [& #95 ;] & #40 ; Trait de soulignement & #41 ; [-] & #40 ; tiret & #41 ;<br> Longueur maximale : 50|Identificateur unique d’un élément.
|Heure|Oui|Date au format : AAAA/MM/ddTHH (par exemple, 2013/06/20T10:00:00)|Heure de données.
|Événement|N° | Une des opérations suivantes :<br>• Cliquez sur<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Achat| Le type de transaction. |

#### <a name="sample-rows-in-a-usage-file"></a>Exemples de lignes dans un fichier de l’utilisation

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Téléchargement d’un fichier de l’utilisation

Examinez la [référence de l’API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) pour le téléchargement de fichiers d’utilisation.
Notez que vous devez passer le contenu du fichier de l’utilisation en tant que corps de l’appel HTTP.

>  Remarque :

>  Taille de fichier maximale : 200 Mo. Vous risquez de télécharger plusieurs fichiers d’utilisation.

>  Vous avez besoin de télécharger un fichier de catalogue avant de commencer à ajouter des données d’utilisation pour votre modèle. Uniquement les éléments dans le fichier catalogue seront utilisés lors de la phase de formation. Tous les autres éléments sont ignorées.

## <a name="how-much-data-do-you-need"></a>La quantité de données avez-vous besoin ?

La qualité de votre modèle est très dépendante de la qualité et la quantité de vos données.
Le système apprend lorsque les utilisateurs qui achètent différents éléments (nous appelons cette occurrences co-création). Pour les versions FBT, il est important de savoir quels éléments sont achetées dans ces mêmes transactions. 

Règle générale est d’utiliser la plupart des éléments de toutes les 20 transactions plus d’informations, afin que si vous avez 10 000 éléments dans votre catalogue, nous recommande que vous disposez au moins 20 heures ce nombre de transactions ou d’environ 200 000. Là encore, il s’agit d’une règle de base. Vous avez besoin tester avec vos données.

Une fois que vous avez créé un modèle, vous pouvez effectuer une [évaluation en mode hors connexion](cognitive-services-recommendations-buildtypes.md) pour vérifier le fonctionnement de votre modèle est susceptible d’effectuer.
