<properties 
    pageTitle="À l’aide de régression linéaire dans l’apprentissage automatique | Microsoft Azure" 
    description="Une comparaison des modèles de régression linéaire dans Excel et dans Azure Machine apprentissage Studio" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>À l’aide de régression linéaire dans l’apprentissage automatique Azure

> *Baroni Marie* et *Jean Philippe Boatman* sont architecture de solution enterprise données Insights centre d’Excellence de Microsoft. Dans cet article, qui est décrit ici leur expérience migration une suite d’analyse de régression existante vers une solution basée sur le cloud à l’aide d’apprentissage automatique Azure.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Objectif

Notre projet a démarré avec deux objectifs à l’esprit :  

1. Permet d’améliorer la précision des prévisions de recettes mensuel de notre organisation prédictive analytique  
2. Utiliser Azure ML pour confirmer, optimiser, augmenter la vitesse et l’échelle de nos résultats.  

Comme beaucoup d’entreprises, l’organisation parcourt un chiffre d’affaires mensuel processus de prévisions. Notre équipe petite d’analystes a été chargé d’à l’aide d’apprentissage automatique pour prendre en charge le processus et améliorer l’exactitude de prévision.  L’équipe passé plusieurs mois collecte des données de plusieurs sources et les attributs de données en cours d’exécution par le biais des analyses statistiques identifier les attributs de clé pertinents pour la prévision des ventes services.  Étapes suivantes a été pour commencer des modèles de statistiques de régression prototypage sur les données dans Excel.  Quelques semaines nous disposions d’un modèle de régression Excel qui a été efforçons du champ actif et des finances réaliser des prévisions processus. Ceci est devenu le résultat de prédiction de référence.  


Nous avons ensuite l’étape suivante pour transfert notre analytique prédictive sur Azure ML pour savoir comment ML Azure peut améliorer les performances prédictive.


## <a name="achieving-predictive-performance-parity"></a>Atteindre les disparités qui existent performances prédictive

Notre priorité était atteindre les disparités qui existent entre les modèles de régression Azure ML et Excel.  Étant donné les mêmes données exactes et le fractionnement même formation et test des données nous voulons obtenir des performances prédictive entre Excel et Azure ML.   Au départ nous a échoué. Le modèle Excel surpassé le modèle ML Azure.   L’échec a été en raison d’un manque de compréhension du paramètre outils de base dans Azure ML. Après une synchronisation avec l’équipe du produit ML Azure, nous acquise mieux comprendre la configuration requise pour notre jeux de données de base et atteint les disparités qui existent entre les deux modèles.  

### <a name="create-regression-model-in-excel"></a>Créer un modèle de régression dans Excel
Notre régression Excel utilisé le modèle de régression linéaire standard figurant dans l’utilitaire d’analyse Excel. 

Nous avons calculé *signifie absolue % d’erreur* et utilisé en tant que la mesure de performance pour le modèle.  L’opération était 3 mois d’arriver à un modèle de travail à l’aide d’Excel.  Nous avons grande partie de l’apprentissage dans l’expérience ML Azure qui était finalement utile de comprendre les impératifs.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Créer une expérience comparable dans l’apprentissage automatique Azure  
Nous suivi ces étapes pour créer notre expérience dans Azure ML :  

1.  Télécharger le jeu de données dans un fichier csv à Azure ML (fichier très petit)
2.  Création d’une nouvelle expérience et l’utilisation la [Sélection des colonnes dans le jeu de données] [ select-columns] module pour sélectionner les mêmes fonctionnalités de données utilisées dans Excel   
3.  Utiliser les [Données fractionnées] [ split] module (avec le mode de *Expression Relative* ) pour diviser les données en exacte jeux train même comme avait été traité dans Excel  
4.  Expérimenté avec la [Droite de régression linéaire] [ linear-regression] module (options par défaut uniquement), présentées et comparer les résultats à notre modèle de régression Excel

### <a name="review-initial-results"></a>Examiner les résultats initiales
Tout d’abord, le modèle Excel surpassé clairement le modèle Azure ML :  

|   |Excel|ML Azure|
|---|:---:|:---:|
|Performances|   |  |
|<ul style="list-style-type: none;"><li>Ajusté R carré</li></ul>| 0.96 |N/A|
|<ul style="list-style-type: none;"><li>Coefficient de <br />Détermination</li></ul>|N/A|   0.78<br />(précision faible)|
|Erreur absolue moyenne |  $9. 5M|  $ 19.4 M|
|Erreur absolue (%) moyenne|   6.03 %|  12.2 %

En exécutant notre processus et les résultats obtenus par les développeurs et les données scientifiques de l’équipe Azure ML, ils fourni rapidement des conseils utiles.  

* Lorsque vous utilisez la [Régression linéaire] [ linear-regression] module dans Azure ML, deux méthodes sont proposées :
    *  Descente dégradé en ligne : Peut être plus appropriée pour des problèmes plus grande échelle
    *  Ordinaire moindres carrés : Il s’agit de la méthode que la plupart des personnes s’ils entendre régression linéaire. Pour les petits groupes de données, ordinaire moindres carrés peut être une solution plus optimale.
*  Vous pouvez ajuster le paramètre de niveau 2 régularisation épaisseur pour améliorer les performances. Il est défini sur 0.001 par défaut et pour notre petit jeu de données, nous attribuez-lui 0,005 pour améliorer les performances.    

### <a name="mystery-solved"></a>Mystery résolu !
Quand nous avons appliqué les recommandations, nous atteint les mêmes performances de référence dans Azure ML en tant qu’avec Excel :   

|| Excel|Azure ML (initiale)|Azure ML avec moindres carrés|
|---|:---:|:---:|:---:|
|Valeur libellée  |Valeurs réelles (numérique)|même|même|
|Étudiant  |Excel-données > Analyse -> régression|Régression linéaire.|Régression linéaire.|
|Options de l’étudiant|N/A|Valeurs par défaut|ordinaires moindres carrés<br />NIVEAU 2 = 0,005|
|Série de données.|26 lignes 3 éléments, 1 étiquette.   Tous les numérique.|même|même|
|Fractionnement : Train|Excel une formation sur les premier 18 lignes, testés sur les dernière 8 lignes.|même|même|
|Fractionnement : Test|Formule de régression Excel appliquée aux dernière 8 lignes|même|même|
|**Performances**||||
|Ajusté R carré|0.96|N/A||
|Coefficient de détermination|N/A|0.78|0.952049|
|Erreur absolue moyenne |$9. 5M|$ 19.4 M|$9. 5M|
|Erreur absolue (%) moyenne|<span style="background-color: 00FF00;">6.03 %</span>|12.2 %|<span style="background-color: 00FF00;">6.03 %</span>|

En outre, les coefficients Excel comparée également aux pondérations fonctionnalité dans le modèle formé Azure :

||Coefficients d’Excel|Poids fonctionnalité Azure|
|---|:---:|:---:|
|Ordonnée à l’origine/décalage|19470209.88|19328500|
|Fonctionnalité A|0.832653063|0.834156|
|Fonctionnalité B|11071967.08|11007300|
|Fonctionnalité C|25383318.09|25140800|

## <a name="next-steps"></a>Étapes suivantes

Nous voulons consommer Azure ML web service dans Excel.  Notre analystes s’appuient sur Excel et nous avons besoin d’une méthode pour appeler le service web Azure ML avec une ligne de données Excel et de sorte qu’elle retourne la valeur prévue vers Excel.   

Nous avons également souhaite optimiser notre modèle, avec les options et algorithmes disponibles dans Azure ML.

### <a name="integration-with-excel"></a>Intégration à Excel
Notre solution était d’effectuent notre modèle de régression Azure ML en créant un service web à partir du modèle formé.  Après quelques minutes, le service web a été créé et nous pouvons l’appeler directement à partir d’Excel pour renvoyer une valeur de chiffre d’affaires prévue.    

La section *Du tableau de bord Services Web* inclut un classeur Excel à télécharger.  Le classeur est fourni déjà mis en forme avec les informations de schéma et API de service web incorporées.   Lorsque vous cliquez sur *Télécharger un classeur Excel*, il s’ouvre et vous pouvez l’enregistrer sur votre ordinateur local.    

![][1]
 
Avec le classeur ouvert, copiez vos paramètres prédéfinis dans la section paramètre bleue comme illustré ci-dessous.  Une fois les paramètres entrés, Excel appelle le service web AzureML et la prévue a obtenu étiquettes seront affichent dans la section valeurs prédites verte.  Le classeur continuent à créer des prédictions pour paramètres basés sur votre modèle de formation pour tous les éléments de ligne entré sous l’onglet Paramètres.   Pour plus d’informations sur l’utilisation de cette fonctionnalité, voir [utilisation d’un Service Web de Azure Machine apprentissage à partir d’Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Optimisation et davantage d’essais
Maintenant que nous avons eu un planning de référence avec notre modèle Excel, nous déplacé en avance afin d’optimiser notre modèle de régression linéaire Azure ML.  Nous avons utilisé le module de [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] d’améliorer nos sélection de données initiales éléments et il nous permis d’assurer une amélioration des performances de 4,6 % signifie absolue erreur.   Pour de futurs projets, nous allons utiliser cette fonctionnalité qui nous peut enregistrer semaines lors de l’itération via les attributs de données pour rechercher l’ensemble des fonctionnalités à utiliser pour la modélisation de droite.  

Ensuite, nous prévoyons d’inclure des algorithmes supplémentaires comme [bayésien] [ bayesian-linear-regression] ou [Arborescences de décision augmentée] [ boosted-decision-tree-regression] dans notre expérience pour comparer les performances.    

Si vous voulez vous exercer avec la régression, un bon jeu de données pour essayer est l’exemple de régression de l’efficacité énergétique dataset, qui comporte de nombreuses attributs numériques. Le jeu de données est fourni dans le cadre des jeux de données exemple dans Studio ML.  Vous pouvez utiliser une variété de modules d’apprentissage pour prévoir la charge chauffage ou refroidissement charge.  Le tableau ci-dessous est qu'une comparaison des performances de régression différente apprend au fur contre l’efficacité énergétique dataset prédiction pour la variable cible refroidissement charge : 

|Modèle|Erreur absolue moyenne|Moyenne racine au carré d’erreur|Erreur absolue relative|Relatives au carré d’erreur|Coefficient de détermination
|---|---|---|---|---|---
|Arbre de décision augmentée|0.930113|1.4239|0.106647|0.021662|0.978338
|Régression linéaire (descente dégradé)|2.035693|2.98006|0.233414|0.094881|0.905119
|Réseau neuronal régression|1.548195|2.114617|0.177517|0.047774|0.952226
|Régression linéaire (ordinaire moindres carrés)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Points clés 

Nous beaucoup appris par de régression Excel en cours d’exécution et expériences d’apprentissage automatique Azure en parallèle. Création du modèle de base dans Excel et sur la comparaison de modèles à l’aide d’Azure ML [Régression linéaire] [ linear-regression] permis nous Découvrez Azure ML, et nous avons découvert des opportunités pour améliorer les performances de sélection et le modèle de données.         

Nous avons également constaté qu’il est conseillé d’utiliser la [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] à accélérer les prédiction de futurs projets.  En appliquant la sélection des fonctionnalités à vos données, vous pouvez créer un modèle amélioré dans Azure ML avec améliorer les performances globales. 

La capacité de transférer prédictive analytique prévisions à partir d’Azure ML vers Excel systémiques permet une augmentation significative dans la capacité à fournir correctement les résultats à un public opérationnelles.     


## <a name="resources"></a>Ressources
Certaines ressources sont répertoriées pour vous aider à qu'utiliser avec la régression :  

* Régression dans Excel.  Si vous avez essayé jamais régression dans Excel, ce didacticiel en toute simplicité : [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Vs régression réaliser des prévisions.  Tyler Chessman rédigé un article de blog expliquant comment temps série réaliser des prévisions dans Excel, qui contient la description du débutant bon de régression linéaire. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-Forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Régression linéaire ordinaire moindres carrés : Défauts, problèmes et pièges.  Pour une introduction et une discussion de régression : [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
