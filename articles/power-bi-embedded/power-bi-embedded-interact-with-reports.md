<properties
   pageTitle="Interagir avec des rapports à l’aide de l’API JavaScript | Microsoft Azure"
   description="Power BI incorporé, interagissez avec des rapports à l’aide de l’API JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir avec les rapports Power BI à l’aide de l’API JavaScript

L’API Power BI JavaScript vous permet d’incorporer facilement des rapports Power BI dans vos applications. Avec l’API, vos applications peuvent interagir avec différents éléments de rapport, telles que les pages et filtres. Cette interactivité rend les rapports Power BI une partie plus intégrée de votre application.

Vous incorporez un rapport Power BI dans votre application à l’aide d’un iframe est hébergé dans le cadre de l’application. L’iframe agit comme une limite entre votre application et le rapport, comme vous pouvez le voir dans l’image suivante. 

![Power BI incorporé iframe sans API Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

L’iframe rend le processus d’incorporation beaucoup plus facile, mais sans l’API JavaScript le rapport et votre application ne peut pas interagir avec eux. Ce manque d’interaction peut rendre le sentiment le rapport n’est pas réellement partie de l’application. Le rapport et l’application vraiment besoin communiquer et inversement, comme dans l’image suivante.

![Power BI incorporé iframe avec l’API Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

L’API Power BI JavaScript vous permet d’écrire du code qui peut s’écouler en toute sécurité à travers la limite iframe. Cela permet à votre application par programme effectue une action dans un rapport et écouter des événements à partir d’actions qui rendent les utilisateurs dans le rapport.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Que pouvez-vous faire avec l’API de JavaScript Power BI ?
Avec l’API JavaScript vous pouvez gérer les rapports, naviguer vers les pages dans un rapport, filtrer un rapport et gérer l’incorporation d’événements. Le diagramme suivant illustre la structure de l’API.

![Diagramme de l’API JavaScript BI Power](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Gérer les rapports
L’API Javascript permet de gérer le comportement au niveau de la page et de rapport :

- Incorporer un rapport Power BI en toute sécurité dans votre application - essayez les [incorporer application démo](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Jeu de jeton d’accès
- Configurer le rapport
  - Activer et désactiver le volet de filtre et le volet de navigation : essayez des [mettre à jour les paramètres Démo application](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Définir les valeurs par défaut pour les pages et filtres - réessayer la [définir démo les valeurs par défaut](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Entrer et quitter le mode plein écran

[En savoir plus sur l’incorporation d’un rapport](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Naviguer vers les pages dans un rapport
L’enbales API JavaScript à découvrir toutes les pages dans un rapport et de définir la page active. Essayez de l' [application de démonstration de navigation](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Pour plus d’informations sur la navigation de page](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrer un rapport
L’API JavaScript fournit base et avancés de fonctionnalités pour les rapports incorporés et les pages du rapport de filtrage. Essayez du [filtrage de démonstration application](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)et examinez du code introduction ici.  


#### <a name="basic-filters"></a>Filtres de base
Un filtre de base est placé sur un niveau de hiérarchie ou de colonne et contient une liste de valeurs pour inclure ou exclure.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Filtres avancés
Filtres avancés utilisent l’opérateur logique et ou ou et accepter une ou deux conditions, chacune avec leur propre opérateur et une valeur. Conditions pris en charge sont :

- Aucun
- LessThan
- LessThanOrEqual
- Supérieur
- GreaterThanOrEqual
- Contient
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Est
- IsNot
- ESTVIDE
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Pour plus d’informations sur le filtrage](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Gestion des événements
En plus d’envoyer des informations dans l’iframe, votre application peut recevoir également des informations sur les événements suivants en provenance de l’iframe :

- Incorporer
  - chargé
  - erreur
- Rapports
  - Certaines
  - dataSelected (bientôt disponible)

[Pour plus d’informations sur la gestion des événements](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’API Power BI JavaScript, consultez les liens suivants :

- [Wiki d’API JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Référence du modèle objet](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Exemples
  - [Angulaire](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Démo en direct](https://microsoft.github.io/PowerBI-JavaScript/demo/)
