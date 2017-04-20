<properties
    pageTitle="Tâches pour préparer des données pour l’amélioration de l’ordinateur apprentissage | Microsoft Azure"
    description="Prétraitement et effacer les données pour le préparer pour apprentissage automatique."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tâches pour préparer des données pour l’amélioration de l’ordinateur apprentissage

Prétraitement des et nettoyage des données sont des tâches importantes généralement doivent être effectués avant de dataset peut être utilisé efficacement pour apprentissage automatique. Données brutes sont souvent bruits et sources non fiables et peuvent être pas les valeurs. À l’aide de ce type de données pour une modélisation peut produire des résultats confusion. Ces tâches font partie du processus de Science de données (TDSP) d’équipe et suivent généralement une exploration initiale d’un dataset permet de découvrir et planifier le traitement préalable obligatoire. Pour obtenir des instructions plus détaillées sur le processus TDSP, consultez les étapes décrites dans le [Processus de scientifique des données d’équipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Prétraitement et nettoyage des tâches, telles que la tâche d’exploration de données, peuvent être exécutées dans un large éventail d’environnements, tels que SQL ou Hive ou Azure Machine apprentissage Studio et avec différents outils et langues, tels que R ou Python, selon l’emplacement de stockage de vos données et la mise en forme. Étant donné que TDSP est répétitive par nature, ces tâches peuvent avoir lieu à diverses étapes dans le flux de travail du processus.

Cet article présente les différentes tâches qui peuvent être entreprises avant ou après traiter les données en apprentissage automatique Azure et concepts de traitement des données.

Pour obtenir un exemple d’exploration de données et prétraitement effectuée à l’intérieur d’apprentissage automatique Azure studio, voir la vidéo [prétraitement des données dans Azure Machine apprentissage Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .


## <a name="why-pre-process-and-clean-data"></a>Pourquoi prétraitement et effacer les données ?

Données réelles sont collectées à partir de différentes sources et processus et il peuvent contenir l’irrégularité ou compromettre la qualité du dataset de données endommagées. Les problèmes de qualité de données type qui surviennent sont les suivantes :

* **Incomplet**: données ne contient pas les attributs ou contenant les valeurs manquantes.
* **Noisy**: données contient des enregistrements erronés ou aberrantes.
* **Incohérente**: données contiennent des enregistrements présentant un conflit ou des écarts.

Données de qualité sont requis pour les modèles prédictive qualité. Pour éviter « inutiles dans celle des entrées » et améliorer la qualité des données et par conséquent de modèle de performances, il est essentiel de diriger un écran d’intégrité des données pour identifier les problèmes de données au plus tôt et décider sur le traitement des données correspondante et les étapes de nettoyage.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quels sont certains écrans d’intégrité des données par défaut qui travaillent ?

Nous pouvons vérifier la qualité générale des données en vérifiant :

* Le nombre **d’enregistrements**.
* Le nombre de **attributs** (ou **fonctionnalités**).
* L’attribut de **types de données** (nominal, ordinal ou continue).
* Le nombre de **valeurs manquantes**.
* **Construction** des données.
    * Si les données sont dans TSV ou CSV, vérifiez que les séparateurs de colonne et les séparateurs de ligne toujours correctement séparent les colonnes et lignes.
    * Si les données sont au format HTML ou XML, vérifiez si les données sont correcte reposant sur leurs normes correspondantes.
    * L’analyse peut également être nécessaire pour extraire des informations à partir de données structurées ou semi-structurées.
* **Enregistrements de données n’est pas cohérente**. Vérifiez la plage de valeurs sont autorisées. par exemple, si les données contiennent moyenne générale étudiant, vérifiez si la moyenne générale se trouve dans la plage désignée, dites 0 ~ 4.

Lorsque vous rencontrez des problèmes avec les données, **traitement des étapes** sont nécessaires qui implique souvent nettoyage des valeurs manquantes, normalisation des données, discrétion, traitement pour supprimer et/ou remplacer des caractères incorporés qui peuvent affecter l’alignement des données de texte, types de données mixtes en commun champs et autres personnes.

**Apprentissage automatique azure utilise les données sous forme de tableau correcte**.  Si les données sont déjà sous forme tabulaire, prétraitement des données peut être effectuée directement avec Azure Machine apprentissage dans Studio apprentissage Machine.  Si les données ne sont pas sous forme de tableau, et il est au format XML, l’analyse peut-être être nécessaire pour convertir les données sous forme tabulaire.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quelles sont les principales tâches de prétraitement des données ?

* **Nettoyage des données**: renseignez ou valeurs manquantes, détecter et supprimer des données BRUITEES et aberrantes.
* **Transformation de données**: normaliser les données afin de réduire les dimensions et le bruit.
* **Réduction des données**: exemples d’enregistrements de données ou des attributs pour la gestion des données plus facilement.
* **Discrétion de données**: convertir les attributs continues à attributs par catégorie pour faciliter leur utilisation avec certaines méthodes d’apprentissage automatique.
* **Texte de nettoyage**: supprimer les caractères incorporés qui peuvent entraîner l’alignement des données, pour, par exemple, les onglets incorporés dans un fichier de données séparées par des tabulations, incorporé nouvelles lignes qui peuvent interrompre les enregistrements, etc..

Les sections ci-dessous en détail certaines de ces étapes de traitement des données.

## <a name="how-to-deal-with-missing-values"></a>Comment traiter les valeurs manquantes ?

Pour traiter les valeurs manquantes, il est préférable d’identifier tout d’abord la raison pour laquelle les valeurs manquantes à mieux gérer le problème. Méthodes de gestion de valeur manquant typiques sont :

* **Suppression**: supprimer les enregistrements avec les valeurs manquantes
* **Substitution DUMMY**: remplacer les valeurs manquantes avec une valeur factice : par exemple, _inconnu_ pour par catégorie ou égal à 0 pour les valeurs numériques.
* **Signifie substitution**: si les données manquantes sont numériques, remplacez les valeurs manquantes avec la moyenne.
* **Substitution fréquente**: si les données manquantes sont par catégorie, remplacez les valeurs manquantes avec l’élément plus fréquente
* **Substitution de régression**: permet de remplacer les valeurs manquantes des valeurs régressés une méthode de régression.  

## <a name="how-to-normalize-data"></a>Comment normaliser les données ?

Normalisation des données ré-nuances de valeurs numériques à une plage spécifiée. Méthodes de normalisation données courants sont les suivantes :

* **Max-Min normalisation**: linéaire transformer les données dans une plage, par exemple entre 0 et 1, où la valeur minimale est ajustée aux valeur max et 0 et 1.
* **Normalisation Z-score**: ajuster les données en fonction de la moyenne et l’écart-type : diviser la différence entre les données et la moyenne par l’écart type.
* **Mise à l’échelle décimal**: mettre les données à l’échelle en déplaçant la virgule décimale de la valeur de l’attribut.  

## <a name="how-to-discretize-data"></a>Comment discrétiser données ?

Données peuvent être discrétisées en convertissant les valeurs continues en attributs nominales ou des intervalles. Différents moyens d’y parvenir sont les suivantes :

* **Groupement de largeur égale**: diviser la plage de toutes les valeurs possibles d’un attribut en groupes N de la même taille et affecter les valeurs qui se trouvent dans un emplacement avec le numéro d’emplacement.
* **Groupement égal hauteur**: diviser la plage de toutes les valeurs possibles d’un attribut en groupes de N, chacune contenant le même nombre d’instances de, puis affecter les valeurs qui se trouvent dans un emplacement avec le numéro d’emplacement.  

## <a name="how-to-reduce-data"></a>Comment faire pour réduire les données ?

Il existe différentes méthodes pour réduire la taille des données pour la gestion des données plus facilement. Selon la taille des données et le domaine, les méthodes suivantes peuvent être appliquées :

* **Enregistrement d’échantillonnage**: exemple les enregistrements de données, puis choisissez uniquement le sous-ensemble représentatif à partir des données.
* **Attribut d’échantillonnage**: sélectionnez uniquement un sous-ensemble des attributs les plus importantes à partir des données.  
* **Agrégation**: diviser les données en groupes et stocker les nombres pour chaque groupe. Par exemple, les numéros de chiffre d’affaires quotidien d’une chaîne de restaurant sur les dernières années 20 peuvent être agrégées revenu mensuel pour réduire la taille des données.  

## <a name="how-to-clean-text-data"></a>Comment faire pour effacer les données de texte ?

**Champs de texte dans les données tabulaires** peut inclure des caractères qui affectent les limites des colonnes alignement et/ou enregistrement. Par exemple, incorporé onglets dans un alignement de colonne cause fichier séparé par des tabulations et incorporé caractères de nouvelle ligne rompre enregistrer des lignes. Gestion du codage texte incorrect lors de la rédaction/lecture du texte permet d’accéder à la perte d’informations par inadvertance introduction de caractères illisibles, par exemple, les valeurs NULL et peut également affecte-t-elle l’analyse. Attention l’analyse et la modification peuvent être nécessaires pour effacer les champs de texte pour un alignement correct et/ou à extraire structurée des données à partir des données texte non structurées ou semi-structurées.

**Exploration de données** offre une vue au plus tôt dans les données. Un certain nombre de problèmes de données soient détecté au cours de cette étape et méthodes correspondantes peuvent être appliqués pour répondre à ces problèmes.  Il est important de poser des questions telles que quelle est la source du problème et comment le problème peut avoir été introduit. Cela vous permet également de choisir les étapes de traitement des données qui doivent être prises pour les résoudre. Le type de perspectives une souhaite dérivent les données peut également servir à classer par priorité les efforts de traitement des données.

## <a name="references"></a>Références

>*L’exploration de données : Concepts et Techniques*, troisième édition, Kaufmann Durand, 2011, Jiawei Han, Micheline Kamber et Pei Jian
