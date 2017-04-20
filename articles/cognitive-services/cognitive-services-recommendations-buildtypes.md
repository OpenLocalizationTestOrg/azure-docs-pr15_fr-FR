<properties
    pageTitle="Types de build et la qualité de modèle : recommandations API | Microsoft Azure"
    description="Recommandations pour apprentissage ordinateurs Azure : guide de démarrage rapide"
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
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Types de build et la qualité de modèle #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Types de build pris en charge ##

L’API recommandations prend actuellement en charge les deux types de build : *recommandation* et *FBT*. Chaque intégré à l’aide de différents algorithmes, et chacune contient des fonctions différentes. Ce document décrit chacun de ces versions préliminaires et techniques pour la comparaison de la qualité des modèles générée.

Si vous n’avez pas déjà fait, nous vous recommandons d’effectuer le [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Type de build recommandation ###

Le type de build recommandation utilise une factorisation matrice pour fournir des recommandations. Il génère [une fonctionnalité](https://en.wikipedia.org/wiki/Latent_variable) vecteurs basés sur vos transactions pour décrire chaque élément et puis utilise ces vecteurs une pour comparer les éléments sont similaires.

Si vous formez le modèle basé sur des achats effectués dans votre magasin electronics et fournissez un téléphone 650 Lumia comme l’entrée dans le modèle, le modèle renvoie un ensemble d’éléments susceptibles d’être achetés par des personnes qui sont susceptibles d’acheter un téléphone 650 Lumia. Les éléments ne peuvent pas être complémentaires. Dans cet exemple, il est possible que le modèle renverra autres téléphones, car les personnes qui aiment le 650 Lumia aiment autres téléphones.

La génération de recommandations comporte deux fonctionnalités qui le rendent attrayant :

* *Prend en charge la génération de recommandations *froid élément* position **

Éléments qui n’ont pas de l’utilisation significative sont appelés éléments froid. Par exemple, si vous recevez une livraison d’un téléphone que vous n’avez jamais vendu avant, le système ne peut pas déduire recommandations relatives à ce produit sur transactions uniquement. Cela signifie que le système doit apprendre à partir des informations sur le produit lui-même.

Si vous souhaitez utiliser le placement d’articles froid, vous devez fournir des informations de fonctionnalités pour chacun de vos éléments du catalogue. Voici ce que les premières lignes de votre catalogue peuvent ressembler à (format note la clé = valeur pour les fonctionnalités).

>Pro2 6CX 00001, Surface, Surface, tapez = matériel, stockage = 128 Go de mémoire = 4G, fabricant = Microsoft

>73H-00013, réactivation Xbox 360, jeu,, Type = logiciel, langue = anglais, évaluation = maturité

>WAH 0F05, Minecraft Xbox 360, jeu,, * Type = logiciel, langue = espagnol, évaluation = jeunesse

Vous devez également définir les paramètres de génération suivants :

| Le paramètre de génération         | Notes
|------------------     |-----------
|*useFeaturesInModel*     | Valeur **true**.  Indique si les fonctionnalités peuvent être utilisées pour améliorer le modèle de recommandations.
|*allowColdItemPlacement*   | Valeur **true**. Indique si la recommandation doit également transmission éléments froid via similitudes entre fonctionnalité.
| *modelingFeatureList*   | Liste séparées par des virgules des noms de fonctionnalités pour être utilisés dans la génération de recommandations pour améliorer la recommandation. Par exemple, « Language, stockage » pour l’exemple précédent.

**Prend en charge la génération de recommandation recommandations utilisateur**

Une build recommandation prend en charge les [recommandations utilisateur](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Cela signifie qu’il peut fournir des recommandations personnalisées pour les utilisateurs en fonction de leurs historiques transaction. Pour obtenir des recommandations utilisateur, vous pouvez fournir l’ID d’utilisateur ou de l’historique récent des transactions pour cet utilisateur.

Un exemple classique de l’endroit où vous souhaiterez peut-être appliquer les recommandations utilisateur est à se connecter dans la page Bienvenue. Il vous pouvez promouvoir les contenus qui s’applique à l’utilisateur spécifique.

Vous souhaiterez également appliquer un type de build recommandations lorsque l’utilisateur est sur le point d’extraire. À ce stade, vous disposez de la liste des éléments de que l’utilisateur se trouve sur le point d’acheter et vous pouvez fournir des recommandations basées sur le panier marché actuel.

#### <a name="recommendations-build-parameters"></a>Paramètres de génération de recommandations

| Nom  |   Description |    Tapez, <br>  valeurs valides, <br> (valeur par défaut)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   Le nombre d’itérations que le modèle s’exécute est reflété par le temps cluster global et la précision du modèle. Est élevé plus précis, le numéro du modèle, mais l’heure cluster prend plus de temps.  |   Nombre entier, <br>  10 à 50 <br>(40)
| *NumberOfModelDimensions* |   Le nombre de dimensions se rapporte au nombre de fonctionnalités que le modèle va tenter de trouver dans vos données. Augmenter le nombre de dimensions permettra de mieux affiner les résultats en clusters plus petits. Toutefois, trop de dimensions empêchera le modèle de recherche corrélations entre les éléments. |  Nombre entier, <br> 10 à 40 <br>(20) |
| *ItemCutOffLowerBound* |  Définit le nombre minimal de points de l’utilisation qu'un élément doit être en pour qu’il puisse être considéré comme faisant partie du modèle. |        Nombre entier, <br> 2 ou plus <br> (2) |
| *ItemCutOffUpperBound* |  Définit le nombre maximal de points de l’utilisation qu'un élément doit être en pour qu’elle considéré comme faisant partie du modèle. |  Nombre entier, <br>2 ou plus<br> (2147483647) |
|*UserCutOffLowerBound* |   Définit le nombre minimal de transactions qu'un utilisateur doit avoir effectuée pour être considéré comme appartenant au modèle. | Nombre entier, <br> 2 ou plus <br> (2)
| *UserCutOffUpperBound* |  Définit le nombre maximal de transactions qu'un utilisateur doit avoir effectuée pour être considéré comme appartenant au modèle. | Nombre entier, <br>2 ou plus <br> (2147483647)|
| *UseFeaturesInModel* |    Indique si les fonctionnalités peuvent être utilisées pour améliorer le modèle de recommandations. |     Valeur booléenne<br> Par défaut : True
|*ModelingFeatureList* |    Liste séparées par des virgules des noms de fonctionnalités pour être utilisés dans la génération de recommandations pour améliorer la recommandation. Tout dépend les fonctionnalités qui sont importantes. |    Chaîne jusqu'à 512 caractères
| *AllowColdItemPlacement* |    Indique si la recommandation doit également transmission éléments froid via similitudes entre fonctionnalité. | Valeur booléenne <br> Par défaut : False
| *EnableFeatureCorrelation*    | Indique si les fonctionnalités peuvent être utilisées dans les motifs. | Valeur booléenne <br> Par défaut : False
| *ReasoningFeatureList* |  Liste séparées par des virgules des noms de fonctions à utiliser pour raisonnement phrases, telles que des explications recommandation. Tout dépend les fonctionnalités qui sont importantes aux clients. | Chaîne jusqu'à 512 caractères
| *EnableU2I* | Activer les recommandations personnalisées, également appelées utilisateur à l’article recommandations (U2I). | Valeur booléenne <br>Par défaut : True
|*EnableModelingInsights* | Définit si évaluation en mode hors connexion doit être effectuée pour rassembler des perspectives modélisation (autrement dit, les mesures de précision et de la diversité). Si défini sur true, un sous-ensemble des données ne doivent pas être utilisé pour formation, car il devra être réservé pour le test du modèle. En savoir plus sur [les évaluations en mode hors connexion](#OfflineEvaluation). | Valeur booléenne <br> Par défaut : False
| *SplitterStrategy* | Si Activer modélisation insights est défini sur *true*, voici comment fractionner des données à des fins d’évaluation.  | Chaîne, *RandomSplitter* ou *LastEventSplitter* <br>Par défaut : RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Type de build FBT ###

La génération (FBT) gagnante fréquemment achetée effectue une analyse qui compte le nombre d’occurrences de deux ou trois différents produits co-création ensemble. Il trie ensuite les jeux basés sur une fonction similitudes entre (**occurrences co-création**, **Jaccard**, **lever**).

Considérez **Jaccard** et **lever** comme méthodes pour centrer les co-création occurrences.  Cela signifie que les éléments seront retournés uniquement si elles où acheté avec l’élément de valeur de départ.

Dans notre exemple de téléphone 650 Lumia, téléphonique X est renvoyé uniquement si vous avez acheté téléphonique X dans la même session que le téléphone 650 Lumia. Étant donné que cela peut être peu, nous pouvons prévoir éléments complémentaire pour le 650 Lumia doit être retourné ; par exemple, une protection d’écran ou une carte power pour le 650 Lumia.

Pour l’instant, deux éléments sont supposés être acheté dans la même session s’ils se produisent dans une transaction avec le même nom d’utilisateur et un horodatage.

Versions préliminaires FBT ne sont pas compatibles froid éléments, car par définition ils attendent deux articles à acheter dans la même transaction. Tandis que les versions préliminaires FBT peuvent retourner des jeux d’éléments (groupes de trois chiffres), ils ne sont pas compatibles recommandations personnalisées, car ils acceptent un élément de la valeur de départ unique comme entrée.


#### <a name="fbt-build-parameters"></a>Paramètres de génération FBT

| Nom  |   Description |       Tapez,  <br> valeurs valides, <br> (valeur par défaut)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Le niveau de risque le modèle est. Nombre d’occurrences de co-création d’éléments à prendre en compte pour une modélisation. |  Nombre entier, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Le nombre d’éléments d’une série de fréquente aux limites.| Nombre entier  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Score minimal qui sont autorisés à un ensemble fréquent à inclure dans les résultats renvoyés. Est élevée que possible. | Double <br> 0 et versions ultérieures <br> (0)
| *FbtSimilarityFunction* | Définit la fonction similitudes entre devant être utilisé par la génération. **Lever** privilégie serendipity, **occurrence co-création** privilégie prévisibilité et **Jaccard** est un compromis entre les deux. | Chaîne,  <br>  <i>cooccurrence, levée, jaccard</i><br> Par défaut : <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Évaluation de build et la sélection ##

Ce guide peut vous aider à déterminer si vous devez utiliser un recommandations ou une génération FBT, mais elle ne propose pas une réponse définitive dans les cas où vous pouvez utiliser un d'entre eux. En outre, même si vous savez que vous souhaitez utiliser un type de build FBT, vous souhaiterez toujours choisir **Jaccard** ou **lever** en tant que la fonction similitudes entre.

La meilleure façon de choisir entre deux différentes générations consiste à tester dans la réalité (évaluation en ligne) et effectuer le suivi d’un taux de conversion pour les différentes versions. Le taux de conversion peut être mesuré en fonction des recommandations clics, le nombre réel des achats à partir de recommandations affichées ou même sur les montants achat réel lorsque les recommandations différentes ont été affichées. Vous pouvez sélectionner votre métrique de taux de conversion en fonction de votre objectif stratégique.

Dans certains cas, vous souhaiterez évaluer le modèle en mode hors connexion avant de mettre en production. Évaluation en mode hors connexion n’est pas remplacement d’évaluation en ligne, il peut servir une métrique.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Évaluation en mode hors connexion  ##

L’objectif d’une évaluation en mode hors connexion est de prédire précision (le nombre d’utilisateurs qui acheter un des éléments recommandés) et la diversité des recommandations (le nombre d’éléments qu’il est recommandé).
Dans le cadre de l’évaluation de mesures précision et de la diversité, le système trouve un échantillon d’utilisateurs et fractionner les transactions pour les utilisateurs en deux groupes : le dataset de formation et le jeu de données de test.

> [AZURE.NOTE]Pour utiliser les mesures en mode hors connexion, vous devez disposer des horodatages dans vos données d’utilisation.
> Données de date exigeant nécessaires pour fractionner l’utilisation correctement entre la formation et test des jeux de données.

> En outre, évaluation en mode hors connexion ne peut-être pas produire des résultats pour les fichiers de l’utilisation de petite. Pour l’évaluation à être complet, doit être un nombre minimal de 1 000 points de l’utilisation du groupe de données de test.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Précision à k ###
Le tableau suivant représente le résultat de l’évaluation en mode hors connexion précision à k.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Pourcentage |   13,75 | 18.04   | 21 |  24.31 | 26.61
|Utilisateurs de test |    10 000 |    10 000 |    10 000 |    10 000 |    10 000
|Utilisateurs considérés | 10 000 |    10 000 |    10 000 |    10 000 |    10 000
|Utilisateurs ne pas pris en compte | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
Dans le tableau ci-dessus, *k* représente le nombre de recommandations affichées pour le client. Le tableau est modifié comme suit : « Si pendant la période d’essai, qu’une recommandation a été affichée pour les clients, 13,75 uniquement des utilisateurs serait acheté cette recommandation. » Cette déclaration est basée sur l’hypothèse que le modèle a été formé avec les données d’achat. Une autre façon dit est que la précision à 1 est 13,75.

Vous remarquerez que comme davantage d’éléments est affichés au client, la probabilité des clients achat d’un article recommandé remonte. Pour l’expérience précédente, la probabilité est presque double pour cent 26.61 lorsque 5 éléments sont recommandés.

#### <a name="percentage"></a>Pourcentage
Le pourcentage d’utilisateurs d’interagir avec au moins une des recommandations *k* est affiché. Le pourcentage est calculé en divisant le nombre d’utilisateurs qui interagit avec au moins une recommandation par le nombre total d’utilisateurs considérés. Voir les utilisateurs pris en compte pour plus d’informations.

#### <a name="users-in-test"></a>Utilisateurs de test
Données dans cette ligne représentent le nombre total d’utilisateurs dans le groupe de données de test.

#### <a name="users-considered"></a>Utilisateurs considérés
Un utilisateur est considéré comme uniquement si le système recommandé au moins éléments *k* basé sur le modèle généré à l’aide de la formation dataset.

#### <a name="users-not-considered"></a>Utilisateurs ne pas pris en compte
Données dans cette ligne représentent tous les utilisateurs ne pas pris en compte. Les utilisateurs n’ayant pas reçu au moins *k* éléments recommandés.

Utilisateur ne pas considérées comme = utilisateurs de test – considérées comme des utilisateurs

<a name="Diversity"></a>
### <a name="diversity"></a>Diversité ###
Indicateurs de diversité mesurent le type d’éléments recommandés. Le tableau suivant représente le résultat de l’évaluation en mode hors connexion diversité.

|Peinture centile |    0-90|  90-99| 99-100
|------------------|--------|-------|---------
|Pourcentage        | 34.258 | 55.127| 10.615


Total des éléments recommandés : 100 000

Éléments uniques recommandés : 954

#### <a name="percentile-buckets"></a>Plages centile
Chaque plage centile est représenté par une plage spécifique (minimales et maximales valeurs comprises entre 0 et 100). Les éléments près de 100 sont les éléments les plus populaires et les éléments à proximité 0 sont les moins populaires. Par exemple, si la valeur de pourcentage de la plage de centile 99-100 est 10.6, il signifie que pourcentage 10.6 des recommandations renvoyées les premiers % celui éléments les plus populaires. La valeur minimale de peinture centile est incluse, et la valeur maximale est exclusive, à l’exception de 100.
#### <a name="unique-items-recommended"></a>Éléments uniques recommandés
Les éléments uniques recommandés métrique indique le nombre d’éléments distincts qui ont été retournées pour l’évaluation.
#### <a name="total-items-recommended"></a>Nombre total d’éléments recommandé
Le nombre total d’éléments recommandé métrique indique le nombre d’éléments recommandés. Certaines peuvent être des doublons.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Mesures d’évaluation en mode hors connexion ###
Les précision et diversité des mesures en mode hors connexion peuvent être utiles lorsque vous sélectionnez quelle build à utiliser. Au moment de créer, dans le cadre du FBT ou recommandation paramètres de génération :

-   Définissez le paramètre de génération *enableModelingInsights* **true**.
-   Vous pouvez également sélectionner la *splitterStrategy* (soit *RandomSplitter* ou *LastEventSplitter*).
*RandomSplitter* fractionner les données d’utilisation dans un train et test définit selon le pourcentage de test donné *randomSplitterParameters* et aléatoires graines de valeurs.
*LastEventSplitter* divise les données d’utilisation dans un train et tester les jeux basés sur la dernière opération pour chaque utilisateur.

Cela déclenche une build qui utilise uniquement un sous-ensemble des données pour la formation et le reste des données pour calculer les mesures d’évaluation.  Une fois la génération terminée, vous devez appeler l' [API de mesures build Get](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), en passant le respectifs *modelId* et *buildId*pour obtenir le résultat de l’évaluation.

 Voici le résultat JSON pour l’évaluation d’exemple.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
