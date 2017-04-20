<properties 
    pageTitle="Comment mettre en œuvre la navigation par facettes dans Azure recherche | Microsoft Azure | Rechercher des catégories de navigation" 
    description="Ajouter la navigation par facettes aux applications qui s’intègrent avec la recherche Azure, un service de recherche cloud hébergé sur Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Comment mettre en œuvre la navigation par facettes dans recherche Azure

Navigation par facettes est un mécanisme de filtrage qui fournit une navigation exploration dirigés vers le bas dans les applications de recherche. Alors que le terme « navigation par facettes » est inconnu, il est presque un étant donné que vous avez utilisé une avant. Comme l’exemple suivant illustre, la navigation par facettes est rien d’autre que les catégories utilisées pour filtrer les résultats.

## <a name="what-it-looks-like"></a>Quoi il ressemble

 ![][1]
  
Facettes peuvent vous aider à trouver ce que vous recherchez, tout en garantissant que vous ne recevez aucun résultats. En tant que développeur, facettes vous permettent d’exposer les critères de recherche plus utiles pour naviguer sur votre corpus de recherche. Dans les applications de vente en ligne, la navigation par facettes repose souvent sur marques, départements (chaussures pour enfants), la taille, prix, popularité et évaluations. 

Implémentation de la navigation par facettes diffère à travers des technologies de recherche et peut être très complexe. Dans Rechercher Azure, la navigation par facettes intégrée au moment de la requête, à l’aide de champs avec attributs spécifiés dans le schéma. Dans les requêtes qui crée de votre application, une requête doit envoyer des *paramètres de la requête facette* afin de recevoir les valeurs de filtre facette disponibles pour ce jeu de résultats de document. Pour rogner réellement le résultat de document définie, l’application doit s’appliquer un `$filter` expression.

En termes de développement d’applications, écrire du code qui crée les requêtes constitue la majeure partie du travail. La plupart des comportements application que vous souhaiteriez à partir de la navigation par facettes est fourni par le service, y compris la prise en charge intégrée pour définir des plages d’et obtenir des nombres pour obtenir les résultats facette. Le service inclut également des valeurs par défaut sensibles pour vous aider à éviter les structures de navigation complexe. 

Cet article contient les sections suivantes :

- [Comment le créer](#howtobuildit)
- [Créer la couche de présentation](#presentationlayer)
- [Générer l’index](#buildindex)
- [Vérifier la présence de la qualité des données](#checkdata)
- [Créer une requête](#buildquery)
- [Conseils contrôler la navigation par facettes](#tips)
- [Navigation par facettes basée sur les valeurs de la plage](#rangefacets)
- [Navigation par facettes basée sur GeoPoints](#geofacets)
- [Essayez-le](#tryitout)

##<a name="why-use-it"></a>Pourquoi l’utiliser
Les applications de recherche plus efficaces ont plusieurs modèles d’interaction outre une zone de recherche. Navigation par facettes est un autre point d’entrée de la recherche, offrant une alternative pratique à la saisie des expressions de recherche complexe manuellement.

##<a name="know-the-fundamentals"></a>Connaître les notions de base

Si vous débutez pour rechercher le développement, la meilleure façon de penser à la navigation par facettes est qu’elle indique les possibilités de recherche autonome. Il s’agit d’un type de l’expérience de recherche de l’exploration des niveaux inférieurs, en fonction des filtres prédéfinis, utilisés pour restreindre rapidement les résultats de résultats de recherche à l’aide du point et cliquez sur. 

**Modèle d’interaction**

L’expérience de recherche pour la navigation par facettes est itératif, donc nous allons commencer par comprendre comme une séquence de requêtes déroulement en réponse aux actions des utilisateurs.

Le point de départ est une page d’application qui inclut la navigation par facettes, généralement placée sur la périphérie. Navigation par facettes est souvent une arborescence avec des cases à cocher pour chaque valeur, ou texte interactif. 

1.  Une requête envoyée à Azure recherche spécifie la structure de la navigation par facettes via un ou plusieurs paramètres de requête facette. Par exemple, la requête peut comporter `facet=Rating`, par exemple avec une `:values` ou `:sort` option pour affiner la présentation.
2.  La couche présentation affiche une page de recherche qui inclut la navigation par facettes, à l’aide de facettes spécifiées dans la demande.
3.  Étant donné une structure de navigation par facettes qui inclut l’évaluation, l’utilisateur clique sur « 4 » pour indiquer que seuls les produits avec un classement de 4 ou plus doivent s’afficher. 
4.  En réponse, l’application envoie une requête qui inclut`$filter=Rating ge 4` 
5.  La page qui affiche un jeu de résultats réduite, contenant uniquement les éléments qui répondent aux critères de nouveau les mises à jour la couche de présentation (dans ce cas, les produits classés 4 et jusqu'à).

Une facette est un paramètre de requête, mais ne la confondez pas avec la requête. Il n’est jamais utilisé comme critères de sélection dans une requête. Réfléchissez à la place, facette paramètres de requête comme entrées de la structure de navigation revient dans la réponse. Pour chaque paramètre de requête facette que vous fournissez, recherche Azure va évaluer le nombre de documents se trouvent dans les résultats partielles pour chaque valeur de la facette.

Notez le `$filter` à l’étape 4. Il s’agit d’un aspect important de la navigation par facettes. Bien que facettes et filtres soient indépendantes de l’API, vous devez les deux, à l’expérience que vous avez l’intention. 

**Modèles de conception**

Dans le code de l’application, le modèle consiste à utiliser des paramètres de requête facette pour renvoyer la structure de navigation par facettes ainsi que les résultats facette, plus une expression $filter qui gère l’événement click. Considérez la `$filter` expression comme le code derrière la limitation réel de résultats de recherche renvoyé à la couche de présentation. Étant donné une facette de couleurs, cliquez sur la couleur rouge est implémentée par une `$filter` expression qui sélectionne uniquement les éléments ayant une couleur rouge. 

**Concepts de base de requête de recherche Azure**

Dans Rechercher Azure, une demande est spécifiée via un ou plusieurs paramètres de requête (voir [Rechercher des Documents](http://msdn.microsoft.com/library/azure/dn798927.aspx) pour une description de chacun d’eux). Aucune des paramètres de requête est requis, mais vous devez disposer au moins une dans l’ordre d’une requête valide.

Précision, généralement entendue comme la possibilité de filtrer les correspondances inutiles, par le biais moins l’une de ces expressions :

- **recherche =**<br/>
La valeur de ce paramètre constitue l’expression de recherche. Il peut être un seul élément de texte ou une expression de recherche complexe qui inclut plusieurs termes et les opérateurs. Sur le serveur, une expression de recherche est utilisée pour la recherche en texte intégral, interroger des champs de recherche dans l’index pour la correspondance des termes, retourner des résultats par ordre. Si vous définissez `search` null, la requête exécution a dépassé la totalité d’un index (autrement dit, `search=*`). Dans ce cas, d’autres éléments de la requête, par exemple un `$filter` ou score profil, seront les principaux facteurs qui peuvent affecter les documents sont retournés `($filter`) et dans quel ordre (`scoringProfile` ou `$orderb`y).

- **$filter =**<br/>
Un filtre est un mécanisme puissant pour limiter la taille des résultats de recherche basée sur les valeurs des attributs de document spécifiques. A `$filter` est évaluée en premier, suivi d’une logique de faceting qui génère les valeurs disponibles et le nombre correspondant pour chaque valeur

Expressions de recherche complexe diminue les performances de la requête. Si possible, utiliser des expressions de filtre bien construite pour augmenter la précision et améliorer les performances des requêtes.

Pour mieux comprendre comment un filtre ajoute une plus grande précision, comparez une expression de recherche complexe pour qu’il inclut une expression de filtre :

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Bien que les deux requêtes sont valides, le second est supérieur si vous recherchez non motels avec parking de Seattle. La première requête s’appuie sur ces cours mentionnées ou non mentionnées dans les champs de chaîne, tels que nom, Description et un autre champ contenant les données que vous pourrez recherches des mots spécifiques. La deuxième requête recherche des correspondances précis aux données structurées et est susceptible d’être beaucoup plus précis.

Dans les applications qui incluent la navigation par facettes, vous souhaiterez pour vous assurer que chaque action de l’utilisateur sur une structure de navigation par facettes accompagnée d’une restrictive des résultats de recherche, grâce à une expression de filtre.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Comment le créer

La navigation par facettes dans Azure recherche est activée dans votre code d’application qui crée la demande, mais s’appuie sur des éléments prédéfinis dans votre schéma.

Prédéfinis dans votre recherche index est la `Facetable [true|false]` attribut d’index, définissez sur les champs sélectionnés pour activer ou désactiver leur utilisation dans une structure de navigation par facettes. Sans `"Facetable" = true`, un champ ne peuvent pas être utilisé dans la navigation facette.

Au moment de la requête, votre code d’application crée une requête qui inclut `facet=[string]`, un paramètre de requête qui fournit le champ à facette par. Une requête peut avoir plusieurs facettes, tels que `&facet=color&facet=category&facet=rating`, chacun d'entre eux séparés par un caractère esperluette (&).

Code de l’application doit également construire une `$filter` expression pour gérer les événements click dans la navigation par facettes. A `$filter` réduit les résultats de la recherche à l’aide de la valeur de la facette en tant que critères de filtre.

Recherche Azure renvoie les résultats de recherche, par les mots entrés par l’utilisateur, ainsi que des mises à jour de la structure de navigation par facettes. Dans Rechercher Azure, la navigation par facettes est une construction à un niveau, avec les valeurs de facette et compte du nombre de résultats trouvés pour chacun d’eux.

La couche de présentation dans votre code fournit l’expérience utilisateur. Il doit répertorier les parties de la navigation par facettes, tels que l’étiquette, valeurs, cases à cocher et le nombre. L’API REST de recherche Azure est indépendant des plates-formes, aussi soyez quelle langue et plateforme souhaitée. Le plus important consiste à inclure des éléments d’interface utilisateur qui prennent en charge incrémentiel Actualiser, avec l’état de l’interface utilisateur mis à jour que chaque facette supplémentaire est sélectionnée. 

Dans les sections suivantes, nous allons étudier comment générer chaque composant, en commençant par la couche de présentation.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Créer la couche de présentation

Utilisation de la couche de présentation peut vous aider à découvrir exigences manqués dans le cas contraire et à comprennent les fonctionnalités sont essentielles à l’expérience de recherche.

En ce qui concerne la navigation par facettes, votre page web ou une application affiche la structure de navigation par facettes détecte une entrée de l’utilisateur dans la page et insère les éléments modifiés. 

Pour les applications web, AJAX est généralement utilisée dans la couche présentation car elle permet d’actualiser les modifications incrémentielles. Vous pouvez également utiliser ASP.NET MVC ou toute autre plateforme de visualisation qui peut se connecter à un service de recherche Azure sur HTTP. L’exemple d’application mentionné dans cet article-- **Catalogue AdventureWorks** – se trouve qu’une application ASP.NET MVC.

L’exemple suivant, provenant du fichier **index.cshtml** de l’exemple d’application crée une structure HTML dynamique pour afficher la navigation par facettes dans votre page de résultats de recherche. Dans l’échantillon, la navigation par facettes intégrée à la page de résultats de recherche et elle apparaît une fois que l’utilisateur a envoyé un terme de recherche.

Notez que chaque facette a une étiquette (couleurs, catégories, prix), une liaison à un champ par facettes (couleur, nom de la catégorie, listPrice) et un `.count` paramètre, utilisé pour renvoyer le nombre d’éléments trouvés pour ce résultat facette.

  ![][2]
 

> [AZURE.TIP] Lorsque vous créez la page de résultats de recherche, pensez à ajouter un mécanisme de la désactivation de facettes. Si vous utilisez les cases à cocher, les utilisateurs peuvent facilement caractère intuitif comment faire pour effacer les filtres. Pour les autres dispositions, vous devrez peut-être un motif de barre de navigation ou une autre approche créatif. Par exemple, dans l’exemple d’application AdventureWorks catalogue, vous pouvez cliquer sur le titre, catalogue AdventureWorks, pour rétablir la page de recherche.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Générer l’index

Faceting est activé sur une base de champ par champ dans l’index, via cet attribut index : `"Facetable": true`.  
Tous les types de champ pouvant être utilisé dans la navigation par facettes sont `Facetable` par défaut. Incluent ces types de champs `Edm.String`, `Edm.DateTimeOffset`et tous les types de champ numérique (pour l’essentiel, tous les types de champ sont facetable à l’exception de `Edm.GeographyPoint`, qui ne peuvent pas être utilisé dans la navigation par facettes). 

Lorsque vous créez un index, une meilleure pratique pour la navigation par facettes consiste à explicitement désactiver faceting pour les champs qui ne doivent jamais être utilisés comme une facette.  En particulier, les champs de type chaîne pour les valeurs singleton, par exemple un nom d’ID ou produit, doivent être définies sur `"Facetable": false` pour empêcher leur utilisation accidentelle (et inefficace) dans la navigation par facettes.

Voici le schéma pour l’application d’exemple AdventureWorks catalogue (ajustée de certains attributs pour réduire la taille totale) :

 ![][3]
 
Notez que `Facetable` est désactivé pour les champs de type chaîne qui ne doivent pas être utilisés comme facettes, par exemple un ID ou un nom. Activation faceting désactiver l’endroit où vous n’en avez permet de conserver une petite taille de l’index et généralement améliore les performances.

> [AZURE.TIP] Pour obtenir les meilleurs résultats, inclure l’ensemble des attributs d’index pour chaque champ. Bien que `Facetable` est activée par défaut pour les champs presque toutes les, intentionnellement définissant chaque attribut peut vous aider à imaginer les implications de chaque décision schéma. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Vérifier la présence de la qualité des données 

Lorsque vous développez n’importe quelle application centré sur les données, préparation des données est souvent l’un des composants supérieure de la tâche. Applications de recherche ne font pas exception. La qualité de vos données a une incidence directe sur indique si la structure de navigation par facettes réalisation que vous attendez, ainsi que son efficacité pour vous aider à créer des filtres réduisant le résultat définir.

Recherche d’Azure, le corpus de recherche est constitué à partir de documents qui remplissent un index. Documents fournissent les valeurs qui sont utilisées pour calculer facettes. Si vous voulez facette par marque ou prix, chaque document doit contenir des valeurs pour *BrandName* et *ProductPrice* qui sont valides, cohérentes et productif comme option de filtre.

Vous trouverez ci-dessous quelques rappels que vous devez faites défiler pour :

- Pour chaque champ que vous souhaitez facette par, demandez-vous si elle contient des valeurs qui conviennent en tant que filtres dans recherche autonome. Les valeurs doivent être courte et descriptif suffisamment distinctive pour offrir un choix parmi les options concurrents effacer.
- Fautes d’orthographe ou de valeurs presque identiques. Si vous facette sur couleur et les valeurs de champ Inclure Orange et Ornage (une faute d’orthographe), les deux choisit une facette basée sur le champ de couleur.
- Texte cas mixte peut également entraîner des problèmes dans la navigation par facettes, avec orange et Orange qui apparaissent sous forme de deux valeurs différentes. 
- Une ou plusieurs versions de la même valeur peuvent entraîner une facette séparée pour chacun.

Comme vous pouvez l’imaginer, diligence préparation des données est un aspect essentiel de la navigation par facettes efficace.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Créer une requête

Le code que vous écrivez pour créer des requêtes doit spécifier tous les composants d’une requête valide, y compris des expressions de recherche, facettes, filtres, notation profils – rien servent à formuler une demande. Dans cette section, nous allons découvrir où interviennent les facettes dans une requête, et comment les filtres sont utilisés avec facettes pour présenter un jeu de résultats réduite.

Un exemple est souvent idéal pour commencer. L’exemple suivant, provenant du fichier **CatalogSearch.cs** , crée une requête qui crée facette navigation basée sur la couleur, de catégorie et de prix. 

Notez que facettes soient intègrent dans cet exemple d’application. L’expérience de recherche dans le catalogue AdventureWorks est conçu autour des filtres et la navigation par facettes. Ceci est évident dans la position de la navigation par facettes dans la page évidentes. L’exemple d’application inclut paramètres URI pour facettes (couleur, catégorie, prix) en tant que propriétés de la méthode de recherche (comme construit dans l’exemple d’application).

  ![][4]
 
Un paramètre de requête facette est défini sur un champ et selon le type de données, peut être plus paramétrée en liste délimitée par des virgules qui inclut `count:<integer>`, `sort:<>`, `intervals:<integer>`, et `values:<list>`. Une liste de valeurs est prise en charge pour les données numériques lors de la configuration des plages. Pour plus de détails, voir [Rechercher des Documents (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Ainsi que des facettes, la demande préparée par votre application doit également créer des filtres pour affiner l’ensemble de documents candidate basé sur une sélection de valeur facette. Pour un magasin vélo, la navigation par facettes fournit des indices aux questions telles que « les couleurs, les fabricants et les types de vélos sont disponibles », tout en filtrant les réponses aux questions tels que « les vélos exactes apparaissent en rouges, vélos mountain, dans ce prix plage ».

Lorsqu’un utilisateur clique sur « Rouge » pour indiquer que seuls les produits rouge doivent s’afficher, la requête suivante l’application envoie inclut `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Meilleures pratiques pour la navigation par facettes

La liste suivante résume les meilleures pratiques.

- **Précision**<br/>
Utilisez les filtres. Si vous avez recours uniquement les expressions de recherche uniquement, radical peuvent provoquer un document qui est renvoyé qui n’a la valeur facette précis de chacun de ses champs. 

- **Champs cibles**<br/>
Dans par facettes exploration vers le bas, vous souhaitez généralement inclure uniquement les documents qui ont la valeur de facette dans un champ (facettes) spécifique, pas n’importe où dans tous les champs que vous pourrez recherches. Ajout d’un filtre renforce le champ cible en redirigeant le service pour rechercher uniquement dans le champ par facettes pour une valeur correspondante.

- **Efficacité d’index**<br/>
Si votre application utilise la navigation par facettes en mode exclusif (autrement dit, aucune zone de recherche), vous pouvez marquer le champ en tant que `searchable=false`, `facetable=true` produire un indice plus compact. En outre, l’indexation se produit uniquement sur les valeurs facette entier avec aucune coupure de mots ou de l’indexation des composants d’une valeur de plusieurs mot.

- **Performances**<br/>
Les filtres affiner l’ensemble de documents candidats pour la recherche et les exclure de classement. Si vous avez un grand ensemble de documents, à l’aide d’une exploration des niveaux très sélectif facette déroulant souvent vous donne bien meilleures performances.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Conseils contrôler la navigation par facettes

Vous trouverez ci-dessous une feuille d’info-bulle avec des conseils sur les problèmes spécifiques.

**Ajouter des étiquettes pour chaque champ dans la navigation facette**

Étiquettes sont généralement définis dans l’écran (**index.cshtml** dans l’exemple d’application) ou HTML. Il existe aucune API dans Azure rechercher les étiquettes de navigation facette ou tout autre type de métadonnées.

**Définir les champs peuvent être utilisés en tant que facette**

Rappelez-vous que le schéma de l’index détermine quels champs sont disponibles à utiliser comme une facette. En supposant que facetable est un champ, la requête spécifie les champs à facette par. Le champ selon lequel vous êtes faceting fournit les valeurs qui s’affichent sous l’étiquette. 

Les valeurs qui s’affichent sous chaque étiquette sont récupérées à partir de l’index. Par exemple, si le champ facette est *couleur*, les valeurs disponibles pour le filtrage supplémentaires seront les valeurs de ce champ (rouge, noir et ainsi de suite).

Pour les valeurs numériques et de date/heure uniquement, vous pouvez définir explicitement des valeurs dans le champ facette (par exemple, `facet=Rating,values:1|2|3|4|5`). Une liste de valeurs est autorisée pour ces types de champs simplifier la séparation des résultats facette dans des plages contiguës (deux plages basées sur des valeurs numériques ou des périodes de temps). 

**Découper les résultats facette**

Résultats facette sont les documents figurant dans les résultats de recherche qui correspondent à un terme facette. Dans l’exemple suivant, dans les résultats de recherche de *cloud computing*, 254 éléments également ont *spécification interne* comme un type de contenu. Éléments ne sont pas forcément mutuellement. Si un élément répond aux critères des deux filtres, il est compté dans chacun d’eux. Cela est possible lorsque faceting sur `Collection(Edm.String)` champs, qui sont souvent utilisés pour implémenter le marquage du document.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

En règle générale, si vous trouvez que facette résultats sont régulièrement est trop volumineux, nous vous recommandons vous ajoutez plus de filtres, comme expliqué dans les sections précédentes, pour donner à vos utilisateurs application davantage d’options pour affiner la recherche.

**Limiter les éléments dans le volet de navigation facette**

Pour chaque champ dans l’arborescence de navigation par facettes, il existe une limite par défaut de 10 valeurs. Cette valeur par défaut est préférable pour les structures de navigation, car elle conserve la liste de valeurs à une taille gérable. Vous pouvez ignorer la valeur par défaut en assignant une valeur à compter.

- `&facet=city,count:5`Spécifie que tout d’abord 5 villes figurant dans l’angle supérieur classé résultats sont retournés sous forme d’un résultat facette. Étant donné un terme de recherche de « aéroport » et 32 correspondances, si la requête spécifie `&facet=city,count:5`, uniquement les cinq premiers villes uniques avec le meilleur parti des documents dans les résultats de recherche sont inclus dans les résultats de facette.

Notez la distinction entre les résultats de recherche et de résultats facette. Résultats de la recherche sont tous les documents qui correspondent à la requête. Résultats facette sont les correspondances pour chaque valeur de la facette. Dans l’exemple, les résultats de la recherche doit inclure des noms de ville se trouvant pas dans la liste de classification facette (5 dans notre exemple). Résultats sont filtrés via la navigation par facettes deviennent visible à l’utilisateur lorsqu’il pourra efface facettes ou choisit autres facettes outre ville. 

> [AZURE.NOTE] Limiter l’accès `count` lorsqu’il y a plusieurs types peut prêter à confusion. Le tableau suivant propose un bref résumé de la façon dont le terme est utilisé dans Azure recherche API, exemples de code et la documentation. 

- `@colorFacet.count`<br/>
Dans le code de présentation, vous devriez voir un paramètre count dans la facette, affiche le nombre de résultats facette. Dans les résultats de facette count indique le nombre de documents qui correspondent à la plage ou du magasin de termes facette dans.

- `&facet=City,count:12`<br/>
Dans une requête facette, vous pouvez définir nombre à une valeur.  La valeur par défaut est 10, mais vous pouvez le configurer supérieur ou inférieur. Définition de `count:12` obtient haut 12 correspond à dans les résultats de facette par nombre de document.

- "`@odata.count`"<br/>
Dans la réponse à la requête, cette valeur indique le nombre d’éléments correspondantes dans les résultats de recherche. En moyenne, il est supérieure à la somme de tous les résultats facette combiné, en raison de la présence d’éléments qui correspondent au terme de recherche, mais ne qu’aucune correspondance de valeur facette.


**Niveaux dans la navigation par facettes** 

Comme indiqué, il n’est pas prise en charge directe imbrication facettes dans une hiérarchie. Prêts à l’emploi, la navigation par facettes prend uniquement en charge un niveau de filtres. Cependant, les solutions de contournement existent. Vous pouvez coder une structure hiérarchique facette dans un `Collection(Edm.String)` avec une entrée point par hiérarchie. Mise en œuvre de cette solution de contournement est dépasse le cadre de cet article, mais vous pouvez lire sur les collections dans [OData par exemple](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validation des champs**

Si vous créez la liste de facettes dynamiquement basée sur les informations utilisateur non fiable, vous devez soit valider que les noms des champs à facettes sont valides, soit d’échappement les noms lors de la création d’URL à l’aide de des options `Uri.EscapeDataString()` dans .NET ou l’équivalent dans votre plateforme de choix.

**Compte dans les résultats de facette**

Lorsque vous ajoutez un filtre à une requête à facettes, vous souhaiterez peut-être conserver l’instruction facette (par exemple, `facet=Rating&$filter=Rating ge 4`). Techniquement, facette = évaluation n’est pas nécessaire, mais conserver renvoie le nombre de valeurs de facettes pour évaluations 4 ou version ultérieure. Par exemple, si un utilisateur clique sur « 4 » et la requête inclut un filtre pour supérieure ou égale à « 4 », nombres sont retournés pour chaque classement est 4 et vers le haut.  

**Implications ont sur le nombre de facette**

Dans certaines circonstances, il peut arriver que facette nombres ne correspondent pas les jeux de résultats (voir [la navigation par facettes dans Azure recherche (billet)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Nombre de facette peut être inexactes en raison de l’architecture ont. Chaque index de recherche comporte plusieurs milieu des fragments, chacun d'entre eux les rapports et les facettes N premiers par nombre de document, qui est ensuite combiné dans un seul résultat. Si certaines milieu des fragments de nombreuses correspondance des valeurs, tandis que d’autres personnes ont moins, il est possible que certaines valeurs facette sont manquants ou sous comptés dans les résultats.

Bien que ce comportement peut modifier à tout moment, si vous rencontrez ce comportement aujourd'hui, vous pouvez contourner en gonfler artificiellement le nombre :<number> à un grand nombre d’appliquer reporting complet de chaque partagé. Si la valeur de nombre : est supérieur ou égal au nombre de valeurs uniques dans le champ, vous avez la garantie des résultats précis. Toutefois, lorsque le nombre de document est réellement élevé, qu'il existe une baisse des performances, donc utilisé cette option judicieuse.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Navigation facette basée sur une plage de valeurs

Faceting sur des plages est une obligation d’application recherche courantes. Plages sont prises en charge pour des données numériques et les valeurs de date/heure. Vous pouvez en savoir plus sur chaque approche dans [Rechercher des Documents (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Recherche Azure simplifie construction plage en fournissant deux approches pour calculer une plage. Pour les deux approches, recherche Azure crée les plages appropriées attribuées les entrées que vous avez fourni. Par exemple, si vous spécifiez des valeurs de la plage de 10 | 20 | 30, crée automatiquement des plages de 0 -10, 10-20, 20 à 30. L’exemple d’application supprime les intervalles sont vides. 

**Méthode 1 : Utiliser le paramètre d’intervalle**<br/>
Pour définir les facettes prix par incréments de 10 $, vous devez spécifier :`&facet=price,interval:10`


**Méthode 2 : Utiliser une liste de valeurs**<br/>
Pour les données numériques, vous pouvez utiliser une liste de valeurs.  Vous pouvez la plage facette pour listPrice, rendu comme suit :

  ![][5]

La plage est spécifiée dans le fichier **CatalogSearch.cs** à l’aide d’une liste de valeurs :

    facet=listPrice,values:10|25|100|500|1000|2500

Chaque plage est créé à l’aide de 0 comme point de départ, une valeur dans la liste sous forme d’un point de terminaison, puis de la plage précédente pour créer des intervalles discrètes. Recherche Azure fera dans le cadre de la navigation par facettes. Vous n’avez pas à écrire du code pour structurer chaque intervalle.

### <a name="build-a-filter-for-facet-ranges"></a>Créer un filtre pour les plages facette ###

Pour filtrer les documents basés sur une plage sélectionnée par l’utilisateur, vous pouvez utiliser la `"ge"` et `"lt"` filtrer des opérateurs dans une expression de deux parties qui définit les points de terminaison de la plage. Par exemple, si l’utilisateur sélectionne la plage 10-25, le filtre serait `$filter=listPrice ge 10 and listPrice lt 25`.

Dans l’exemple d’application, l’expression de filtre utilise les paramètres **prix** et **priceTo** pour définir les points de terminaison. La méthode **BuildFilter** dans **CatalogSearch.cs** contient l’expression de filtre qui vous donne les documents dans une plage.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Navigation filtrée selon GeoPoints

Il est courant pour afficher des filtres qui vous aident à choisir un magasin, restaurant ou la destination en fonction de sa proximité à votre emplacement actuel. Bien que ce type de filtre peut ressembler à la navigation par facettes, il est en fait juste un filtre. Nous mentionner ici pour ceux qui recherchent spécifiquement des conseils d’implémentation pour ce problème conception particulière.

Il existe deux fonctions géospatiales dans recherche Azure, **geo.distance** et **geo.intersects**.

- La fonction **geo.distance** renvoie la distance en kilomètres entre deux points, un seul un champ et l’autre une constante passé dans le cadre du filtre. 

- La fonction **geo.intersects** renvoie vraie si un moment donné se trouve dans un polygone donné, où le point est un champ et polygone est spécifié en tant que liste constante des coordonnées passées dans le cadre du filtre.

Vous trouverez des exemples de filtre dans [la syntaxe des expressions OData (Azure recherche)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Essayez-le

Azure recherche Adventure Works démo sur Codeplex contient les exemples référencés dans cet article. Lorsque vous travaillez avec les résultats de la recherche, regardez l’URL les modifications de construction d’une requête. Cette application se passe-t-il pour ajouter des facettes à l’URI que vous sélectionnez chacun d’eux.

1.  Configurer l’application d’exemple à utiliser votre URL du service et la clé d’api. 

    Notez le schéma défini dans le fichier Program.cs du projet CatalogIndexer. Il indique champs facetable de couleur, listPrice, taille, épaisseur, nom de catégorie et modelName.  Seulement quelques-unes de ces (couleur, listPrice, nom de la catégorie) sont réellement implémentées dans la navigation par facettes.

3.  Exécutez l’application. 

    Tout d’abord, simplement la zone de recherche est visible. Vous pouvez cliquez sur le bouton Rechercher immédiatement pour afficher tous les résultats ou tapez un terme de recherche.

    ![][7]
 
4.  Entrez un terme de recherche, tels que vélo, puis cliquez sur **Rechercher**. La requête s’exécute rapidement.
 
    Une structure de navigation par facettes est également retournée avec les résultats de recherche.  Dans l’URL, facettes de couleurs, les catégories et les prix ont la valeur null. Dans la page de résultats de recherche, la structure de navigation par facettes inclut des nombres pour chaque résultat facette.

     ![][8]
 
5.  Cliquez sur une couleur, une catégorie et une gamme de prix. Facettes sont null sur une recherche initiale, mais lorsqu’ils prennent des valeurs, les résultats de recherche sont supprimés des éléments qui ne correspondent plus. Notez que l’URI chercher les modifications apportées à votre requête.

    ![][9]
 
6.  Pour effacer la requête par facettes afin que vous pouvez essayer des comportements autre requête, cliquez sur **Catalogue AdventureWorks** en haut de la page.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Étape suivante

Pour tester vos connaissances, vous pouvez ajouter un champ de facette *%{modelname/}*. L’index est déjà configuré pour cette facette, afin qu’aucune modification à l’index est nécessaire. Mais vous devez modifier le code HTML pour inclure une nouvelle facette pour les modèles et ajoutez le champ facette au constructeur requête.

Pour plus d’informations sur les principes de conception pour la navigation par facettes, nous vous recommandons des liens suivants :

- [Conception de la recherche par facettes](http://www.uie.com/articles/faceted_search/)
- [Motifs de conception : Navigation par facettes](http://alistapart.com/article/design-patterns-faceted-navigation)

Vous pouvez également visionner [Azure recherche approfondie](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). En 45:25, vous trouverez une démonstration sur la mise en œuvre facettes.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 