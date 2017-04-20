<properties
    pageTitle="Sélection dans le processus d’équipe données scientifique de fonctions | Microsoft Azure" 
    description="Explique la fonction de sélection de fonctionnalités et fournit des exemples de leur rôle dans le processus d’enrichissement des données d’apprentissage automatique."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Sélection de fonctionnalités dans le processus de Science de données (TDSP) d’équipe

Cet article explique dans le cadre de sélection de fonctionnalités et fournit des exemples de son rôle dans le processus d’enrichissement des données d’apprentissage automatique. Ces exemples sont dessinées Azure Machine apprentissage Studio. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cette rubrique explique l’objectif de sélection de fonctionnalités et fournit des exemples de son rôle dans le processus d’enrichissement des données d’apprentissage automatique. Ces exemples sont dessinées Azure Machine apprentissage Studio. 

L’ingénierie et la sélection des fonctionnalités est une partie de la TDSP sous forme de plan dans le [Quel est le processus d’équipe données scientifique ?](data-science-process-overview.md). Fonctionnalité ingénierie et sélection sont des parties de l’étape de **développer les fonctionnalités** de la TDSP.

* **ingénierie fonctionnalité**: tente de ce processus pour créer des fonctionnalités pertinentes supplémentaires dans les fonctionnalités de brutes existantes dans les données et augmenter prédictive power à l’algorithme d’apprentissage.

* **sélection de fonctionnalités**: ce processus sélectionne le sous-ensemble clé des fonctionnalités de données d’origine dans le but de réduire la dimension du problème formation.

En règle générale, **ingénierie fonctionnalité** est appliquée tout d’abord pour générer des fonctionnalités supplémentaires, et l’étape de **sélection de fonctionnalités** est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou hautement en corrélation.


## <a name="filtering-features-from-your-data---feature-selection"></a>Fonctions de filtrage de vos données - sélection de fonctionnalité 

Sélection de fonctionnalités est un processus qui est couramment appliqué pour la construction de jeux de données de formation pour les tâches de modélisation prédictive tel que tâches classification ou d’une droite de régression. L’objectif consiste à sélectionner un sous-ensemble des fonctionnalités du jeu de données d’origine que réduire ses dimensions à l’aide d’un ensemble de fonctionnalités minimal pour représenter la quantité maximale de variation dans les données. Ce sous-ensemble des fonctionnalités sont, puis, seules les fonctionnalités pour être inclus pour former le modèle. Sélection de fonctionnalités remplit deux fonctions principales.

* Tout d’abord, sélection de fonctionnalités souvent augmente précision classification en supprimant inutiles, redondantes ou hautement en corrélation fonctionnalités.
* Ensuite, il diminue le nombre de fonctionnalités qui rend le processus de formation du modèle plus efficace. Ceci est particulièrement important pour les apprentis sont coûteuses former tels que des ordinateurs vecteur de prise en charge.

Bien que la sélection de fonctionnalités valeur cible réduire le nombre de fonctionnalités dans le jeu de données utilisé pour former le modèle, il n'est pas généralement désigné par la mention « réduction une dimension ». Méthodes de sélection de fonctionnalité extraire un sous-ensemble des fonctionnalités d’origine dans les données sans les modifier.  Méthodes de réduction d’une dimension utilisent ingénieries fonctionnalités que vous peuvent transformer les fonctionnalités d’origine et par conséquent les modifier. Analyse par composantes principales, l’analyse de corrélation canonique et au singulier valeur décomposition des exemples de méthodes de réduction d’une dimension.

Entre autres, une catégorie largement appliquée des méthodes de sélection des fonctions dans un contexte contrôlé est appelée « sélection des fonctionnalités filtre basé ». Pour évaluer la corrélation entre chaque fonctionnalité et l’attribut cible, ces méthodes appliquent une mesure statistique pour attribuer une note à chaque fonctionnalité. Les fonctionnalités sont puis classées selon le score, qui peut être utilisé pour aider à définir le seuil de conservation ou suppression d’une fonctionnalité spécifique. Les exemples des mesures statistiques utilisées dans ces méthodes de corrélation, l’information réciproque et le test au carré Chi personne.

Dans Azure Machine apprentissage Studio, il existe modules fournis pour la sélection de fonctionnalité. Comme indiqué dans l’illustration suivante, ces modules incluent la [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] et [Analyse discriminante linéaire Fisher][fisher-linear-discriminant-analysis].

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection.png)


Vous pouvez, par exemple, l’utilisation de la [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] module. Pour faciliter la tâche, nous continuer à utiliser l’exemple d’exploration de données texte décrite ci-dessus. Supposons que nous voulons créer un modèle de régression après avoir créé un ensemble de 256 fonctionnalités via la [Fonctionnalité hachage] [ feature-hashing] module et que la variable de réponse est la « Col1 » et représentant un livre passez en revue les évaluations compris entre 1 et 5. En définissant « Fonctionnalité méthode score » à « Corrélation de Pearson », la colonne « cible » « Col1 » et le « nombre de fonctionnalités souhaitées » à 50. Puis le module de [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] produira un dataset contenant 50 fonctionnalités ainsi que l’attribut cible « Col1 ». La figure suivante illustre le déroulement de cette expérience et les paramètres d’entrée que nous décrites ci-dessus.

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection1.png)

La figure suivante illustre les jeux de données qui en résulte. Chaque fonctionnalité est un score en fonction de la corrélation de Pearson entre lui-même et l’attribut cible « Col1 ». Les fonctionnalités dont le score supérieure sont conservées.

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection2.png)

Les résultats correspondants des fonctions sélectionnées sont représentées dans l’illustration suivante.

![Exemple de sélection de fonctionnalité](./media/machine-learning-data-science-select-features/feature-Selection3.png)

En appliquant cette [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] module, 50 de 256 fonctionnalités sont sélectionnées, car ils ont les fonctionnalités plus en corrélation avec la variable cible « Col1 », selon la méthode notation « Corrélation de Pearson ».

## <a name="conclusion"></a>Conclusion
Ingénierie fonctionnalité et sélection de fonctionnalités sont deux conçu fréquemment et fonctionnalités sélectionnées accroître l’efficacité de la formation qui tentatives pour extraire les informations clées contenues dans les données. Ils améliorent également la puissance de ces modèles pour classer les données d’entrée avec précision et pour prévoir de manière plus fiable issues d’intérêt. Fonctionnalité ingénierie et sélection peuvent également combiner pour rendre l’apprentissage plus de calculs souple. Il fait en améliorer et puis réduire le nombre de fonctionnalités nécessaires pour étalonner ou former un modèle. Strictement mathématique, les fonctionnalités sélectionnées pour former le modèle sont un ensemble minimal de variables indépendantes qui expliquent les modèles dans les données et puis prédire issues avec succès.

Notez qu’il n’est pas toujours nécessairement pour effectuer une sélection de fonctionnalité ingénierie ou une fonctionnalité. S’il est nécessaire ou non dépend de celles que nous ont ou collecte, l’algorithme que nous sélectionnez et l’objectif de l’expérience.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
