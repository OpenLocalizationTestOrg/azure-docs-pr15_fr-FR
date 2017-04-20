<properties
    pageTitle="Exemples de requête Lucene pour la recherche Azure | Recherche de Microsoft Azure"
    description="Syntaxe de requête Lucene de recherche partielle, recherche de proximité, augmentation du magasin de termes, recherche d’expressions régulières et caractères génériques."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemples de syntaxe de requête Lucene pour créer des requêtes de recherche Azure

Lors de la création de requêtes pour la recherche Azure, vous pouvez utiliser par défaut la [syntaxe de la requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou [l’Analyseur de requête Lucene dans Azure recherche](https://msdn.microsoft.com/library/azure/mt589323.aspx)remplacement. L’Analyseur de requête Lucene prend en charge les structures de requête plus complexes, tels que requêtes à portée d’un champ de recherche partielle, recherche proximité, augmentation de magasin de termes et recherche de l’expression reqular.

Dans cet article, vous pouvez parcourir exemples d’affichent la syntaxe de requête Lucene et résultats côte à côte. Exemples de s’exécuter par rapport à un index de recherche prédéfini dans [JSFiddle](https://jsfiddle.net/), un éditeur de code en ligne pour tester les scripts et HTML. 

Un clic droit sur les requête exemples d’URL pour ouvrir JSFiddle dans une fenêtre distincte du navigateur.

> [AZURE.NOTE] Les exemples suivants utilisent un index de recherche composé des travaux disponibles basé sur un dataset fourni par l’initiative [Ville de New York OpenData](https://nycopendata.socrata.com/) . Ces données ne doivent pas être considérées actuelle ou complète. L’index est un service sandbox fourni par Microsoft. Vous n’avez pas besoin d’un abonnement Azure ou recherche Azure pour essayer de ces requêtes.

## <a name="viewing-the-examples-in-this-article"></a>Afficher les exemples de cet article

Tous les exemples dans cet article spécifier l’Analyseur de requête Lucene via le paramètre de recherche**queryType** . Lorsque vous utilisez l’Analyseur de requête Lucene à partir de votre code, vous allez spécifier la **queryType** à chaque demande.  Les valeurs valides incluent **simple**|**complète**, avec **simple** en tant que la valeur par défaut et **complète** pour l’Analyseur de requête Lucene. Pour plus d’informations sur la spécification des paramètres de requête, voir [Rechercher des Documents (API REST Service Azure recherche)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

**Exemple 1** : clic droit de la requête suivante extrait de code pour l’ouvrir dans une nouvelle page de navigateur qui charge JSFiddle et exécute la requête :
- [& queryType = complète & Rechercher = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Cette requête renvoie les documents à partir de notre index travaux (chargée sur un service sandbox)

Dans la nouvelle fenêtre de navigateur, vous verrez source JavaScript et sortie HTML côte à côte. Le script fait référence à une requête, qui est fournie par l’URL d’exemple dans cet article. Par exemple, dans l' **exemple 1**, la requête sous-jacente est la suivante :

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Notification de la requête utilise un index de recherche Azure préconfiguré appelé nycjobs. Le paramètre **searchFields** restreint la recherche pour le champ de titre d’entreprise. La **queryType** est défini sur **complète**, qui indique à la recherche Azure pour utiliser l’Analyseur de requête Lucene pour cette requête.

### <a name="fielded-query-operation"></a>Opération de requête saisies

Vous pouvez modifier les exemples de cet article en spécifiant une construction **fieldname:searchterm** pour définir une opération de requête saisies, où le champ est un seul mot, et le terme de recherche est également un seul mot ou une phrase, vous pouvez également avec les opérateurs booléens. Voici quelques exemples :

- business_title :(senior NOT junior)
- état : (« New York » et « Nouveau Jersey »)

Veillez à placer plusieurs chaînes entre guillemets si vous souhaitez que les deux chaînes doit être évaluée comme une seule entité, comme dans ce cas recherchant deux villes distincts dans le champ emplacement. Assurez-vous également que l’opérateur est mise en majuscule comme vous le voir avec NOT et and.

Le champ spécifié dans **fieldname:searchterm** doit être un champ de recherche. Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champ, voir [Créer un Index (API REST Service Azure recherche)](https://msdn.microsoft.com/library/azure/dn798941.aspx) .

## <a name="fuzzy-search"></a>Recherche partielle

Une recherche partielle trouve les correspondances en termes ayant une construction similaire. Par [Lucene documentation](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), recherches floues basés sur [Damerau Levenshtein Distance](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Pour effectuer une recherche partielle, utilisez le tilde « ~ » symbole à la fin d’un mot unique avec un paramètre facultatif, une valeur comprise entre 0 et 2, qui indique la distance modifier. Par exemple, « bleu ~ » ou « bleu ~ 1 » retournerait bleu, de bleus et de collage.

**Exemple 2** : clic droit l’extrait de requête suivant pour faire un essai. La requête suivante recherche pour des titres d’entreprise avec la senior terme dans leur, mais pas subalterne :

- [& queryType = complète & Rechercher = business_title:senior NOT subalterne](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Recherche de proximité

Recherches de proximité sont utilisées pour rechercher des termes qui se trouvent près de l’autre dans un document. Insérer un tilde « ~ » symbole à la fin d’une phrase suivi par le nombre de mots que créez la limite de proximité. Par exemple, « aéroport hôtel » ~ 5 recherche l’hôtel de termes et aéroport dans les 5 mots uns des autres dans un document.

**Exemple 3** : clic droit l’extrait de requête suivant. La requête suivante recherche pour les projets avec l’associer terme (où il est mal orthographiée) :

- [& queryType = complète & Rechercher = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Exemple 4** : avec le bouton droit la requête. Rechercher des tâches avec le terme « analyste » où il est séparé par word ne plusieurs :

- [& queryType = complète & Rechercher = business_title : « analyste » ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemple 5** , essayez à nouveau supprimer les mots entre le terme « analyste ».

- [& queryType = complète & Rechercher = business_title : « analyste » ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Amélioration des termes

Augmentation de magasin de termes fait référence à un document plus élevé si elle contient le terme augmentée, par rapport aux documents qui ne contiennent pas le terme de classement. La différence entre les profils de notation dans la mesure où les profils score améliorer l’aspect de certains champs, plutôt que sur des termes spécifiques. L’exemple suivant vous permet de montrer les différences.

Pensez à un profil score qui augmente correspond à un champ particulier, par exemple **genre** dans l’exemple musicstoreindex. Augmentation de magasin de termes peut être utilisée pour améliorer encore certain recherche termes supérieures à celles d’autres personnes. Par exemple, « rock ^ 2 électronique » augmentera documents contenant les termes de recherche dans le champ **genre** supérieur à celles d’autres champs de recherche dans l’index. En outre, les documents contenant le terme de recherche « rock » sont classés plus élevé que le terme de recherche « électronique » en raison de la valeur d’Ampli termes (2).

Pour améliorer l’aspect un terme, utilisez le point d’insertion, « ^ », symbole avec un facteur d’Ampli (nombre) à la fin de l’expression recherchée. Plus le facteur Ampli, plus pertinents le terme sera par rapport à d’autres termes de recherche. Par défaut, le facteur Ampli est 1. Bien que le facteur Ampli doit être positif, il peut être inférieur à 1 (par exemple, 0,2).

**Exemple 6** --avec le bouton droit la requête. Rechercher des tâches avec le terme « analyste d’ordinateur » dans laquelle nous voir aucun résultat avec ordinateur mots et analyste encore analyste tâches sont en haut des résultats.

- [& queryType = complète & Rechercher = business_title:computer analyste](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemple 7** --essayez à nouveau, cette fois augmentation résultats avec le terme ordinateur via l’analyste terme si les deux mots n’existent pas.

- [& queryType = complète & Rechercher = business_title:computer ^ 2 analyste](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Expressions régulières

Une recherche d’expression régulière trouve une correspondance en fonction du contenu entre barres obliques « / », comme la documentation de la [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemple 8** --avec le bouton droit la requête. Rechercher des tâches avec soit le terme Senior ou Junior.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L’URL de cet exemple s’affiche pas correctement dans la page. Pour résoudre ce problème, copiez l’URL ci-dessous et collez-le dans l’adresse URL du navigateur :    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Caractères génériques

Vous pouvez utiliser syntaxe généralement reconnu pour plusieurs (\*) ou unique (?) caractère astérisque dans les recherches. Remarque l’Analyseur de requête Lucene prend en charge l’utilisation de ces symboles avec un seul terme et non une phrase.

**Exemple 9** --avec le bouton droit la requête. Rechercher des tâches qui contiennent le préfixe « programme », c'est-à-dire les titres d’entreprise avec les termes de programmation et programmeur qu’il contient.

- [& queryType = plein & $select = business_title & recherche = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Vous ne pouvez pas utiliser un * ou ? symbole comme premier caractère d’une recherche.


## <a name="next-steps"></a>Étapes suivantes

Essayez de spécifier l’Analyseur de requête Lucene dans votre code. Les liens suivants expliquent comment configurer les requêtes de recherche pour .NET et l’API REST. Les liens utilisent la syntaxe de la simple par défaut afin que vous devez appliquer ce que vous avez appris à partir de cet article pour spécifier la **queryType**.

- [Interroger votre Index de recherche Azure à l’aide du Kit de développement .NET](search-query-dotnet.md)
- [Interroger votre Index de recherche Azure à l’aide de l’API REST](search-query-rest-api.md)


 