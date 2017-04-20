<properties
    pageTitle="Fonction ingénierie dans le processus Analytique Cortana | Microsoft Azure" 
    description="Explique les besoins d’ingénierie fonctionnalité et fournit des exemples de son rôle dans le processus d’enrichissement des données d’apprentissage automatique."
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


# <a name="feature-engineering-in-the-cortana-analytics-process"></a>Ingénierie fonctionnalité dans le processus Analytique Cortana 

Cette rubrique explique les besoins d’ingénierie fonctionnalité et fournit des exemples de son rôle dans le processus d’enrichissement des données d’apprentissage automatique. Les exemples utilisés pour illustrer ce processus sont dessinées Azure Machine apprentissage Studio. 

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Cette **menu** fournit des liens vers des rubriques qui décrivent comment créer des fonctions pour les données dans différents environnements. Cette tâche est une étape de l' [Équipe données scientifique processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Fonctionnalité ingénieries tentatives pour augmenter la puissance prédictive d’apprentissage algorithmes en créant des fonctionnalités à partir des données brutes pour facilitent le processus d’apprentissage. L’ingénierie et la sélection des fonctionnalités est une partie de la TDSP sous forme de plan dans le [Quel est le processus d’équipe données scientifique ?](data-science-process-overview.md) Fonctionnalité ingénierie et sélection sont des parties de l’étape de **développer les fonctionnalités** de la TDSP. 

* **ingénierie fonctionnalité**: tente de ce processus pour créer des fonctionnalités pertinentes supplémentaires dans les fonctionnalités de brutes existantes dans les données et augmentez la puissance de l’algorithme d’apprentissage prédictive.

* **sélection de fonctionnalités**: ce processus sélectionne le sous-ensemble clé des fonctionnalités de données d’origine dans le but de réduire la dimension du problème formation.

En règle générale, **ingénierie fonctionnalité** est appliquée tout d’abord pour générer des fonctionnalités supplémentaires, et l’étape de **sélection de fonctionnalités** est alors effectuée pour éliminer les fonctionnalités inutiles, redondantes ou hautement en corrélation.

Les données de formation utilisées dans l’apprentissage automatique peuvent souvent être améliorées par extraction des fonctionnalités à partir des données brutes collectées. La création d’un peu les carte densité construite à partir des données brutes bit distribution est un exemple d’une fonction d’ingénierie dans le contexte d’apprendre à classer les images de caractères manuscrites. Ce mappage permettent de localiser plus efficacement qu’utilisant simplement la distribution brute directement les bords des caractères.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="creating-features-from-your-data---feature-engineering"></a>Création de fonctions à partir de vos données - fonctionnalité ingénierie

Les données d’apprentissage se composent d’une matrice composée d’exemples (enregistrements ou observations stockées dans des lignes), chacun d'entre eux comporte un ensemble de fonctionnalités (variables ou les champs stockées dans des colonnes). Les fonctionnalités spécifiées dans la conception expérience devraient caractérisation les modèles dans les données. Bien que la plupart des champs de données brutes peuvent être incluses directement dans l’ensemble des fonctionnalités sélectionné utilisé pour former un modèle, il est souvent la casse que des fonctionnalités supplémentaires (ingénieries) besoin d’être construite dans les fonctionnalités dans les données brutes pour générer un dataset formation amélioré.

Quel type de fonctionnalités doit être créé afin d’améliorer le jeu de données lors de l’apprentissage d’un modèle ? Ingénierie fonctionnalités qui permettent d’améliorer la formation fournissent des informations qui mieux différencient les modèles dans les données. Nous prévoyons les nouvelles fonctionnalités pour fournir des informations supplémentaires qui ne sont pas clairement capturées ou facilement apparente dans l’ensemble des fonctionnalités d’origine ou existante. Mais ce processus est une image clipart. Son et productives décisions nécessitent souvent certaines compétences de domaine.

Lorsque vous démarrez avec apprentissage automatique Azure, il est plus facile à comprendre ce processus concrètement en utilisant les exemples fournis dans Studio. Deux exemples sont présentés ici :

* Un exemple de régression [prédiction du nombre de mensualités vélo](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) dans une expérience contrôlé où les valeurs cible sont connues
* Un exemple de classification d’exploration de données texte à l’aide de la [Fonctionnalité de hachage](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### <a name="example-1-adding-temporal-features-for-regression-model"></a>Exemple 1 : Ajouter des fonctionnalités temporelles pour modèle de régression ###

Nous allons utiliser l’expérience « prévision de la demande de vélos » dans Azure Machine apprentissage Studio pour vous montrer comment concevoir des fonctionnalités pour une tâche de régression. L’objectif de cette expérience consiste à prévoir la demande de vélos, c'est-à-dire le nombre de mensualités de vélo au sein d’une mois/jour/heure spécifique. Le jeu de données « location vélo UCI dataset » est utilisé comme les données d’entrée brutes. Ce groupe de données est basé sur des données réelles à partir de la société Bikeshare majuscule qui met à jour un réseau location vélo de Washington DC aux États-Unis. Le dataset représente le nombre de mensualités vélo au sein d’une heure spécifique d’un jour au cours des années 2011 et année 2012 et contient 17379 lignes et 17 colonnes. L’ensemble des fonctionnalités brutes contient les conditions météo (température/humidité/vent) et le type du jour (jour férié/weekday). Le champ à prévoir est « cnt », un nombre qui représente les mensualités vélo au sein d’une heure spécifique et qui est comprise entre 1 et 977 les plages.

Dans le but de construction efficaces fonctionnalités dans les données de formation, quatre régression modèles sont créées à l’aide de l’algorithme de même, mais avec quatre formation différents ensembles de données. Les quatre jeux de données représente les mêmes données d’entrée brutes, mais avec un nombre croissant de fonctionnalités définie. Ces fonctionnalités sont regroupées en quatre catégories :

1. A = météo congés + weekday + week-end fonctionnalités pour le jour prévu
2. B = nombre de vélos qui ont été louer dans chacune des 12 dernières heures
3. C = nombre de vélos qui ont été louer dans chacune des jours à la même heure 12 précédents
4. D = nombre de vélos qui ont été louer dans chacune des 12 semaines à la même heure et même jour précédents

Outre la fonctionnalité jeu A, qui existent déjà dans les données brutes d’origine, les trois autres ensembles de fonctionnalités sont créés via la fonctionnalité de processus d’ingénierie. Ensemble de fonctionnalités B captures très récente à la demande pour les vélos. Fonction définie C captures à la demande pour bikes en une heure spécifique. Fonction définie à la demande de captures D pour bikes en particulier heures et jours particuliers de la semaine. Le jeux de données quatre formation chaque inclut des fonctionnalité jeu A, A + B, A + B + C et A + B + C + D, respectivement.

Dans l’expérience d’apprentissage automatique Azure, ces quatre ensembles de données formation sont formées via quatre branches du jeu de données d’entrée traitement préalable. À l’exception de la gauche de la plupart des branche, chacun de ces branches contient un module [d’exécuter le Script R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , dans lequel un ensemble d’extraite fonctionnalités (fonctionnalité définies B, C et D) sont respectivement créés et ajoutés au groupe de données importée. La figure suivante illustre le script R permet de créer un ensemble de fonctionnalités B dans la deuxième branche gauche.

![créer des fonctions](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

La comparaison des résultats de performances des quatre modèles sont répertoriés dans le tableau suivant. Obtenir les meilleurs résultats sont représentées par des fonctionnalités A + B + C. Notez que le taux d’erreur diminue lorsque ensemble de fonctionnalités supplémentaires sont inclus dans les données de formation. Il vérifie notre présomption que l’ensemble des fonctionnalités B, C fournit des informations pertinentes supplémentaires pour la tâche de régression. Mais l’ajout de la fonctionnalité D ne semble pas fournir une réduction supplémentaire dans le taux d’erreur.

![comparaison des résultats](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a>Exemple 2 : Création de fonctionnalités d’exploration de données texte  

Fonctionnalité ingénierie est largement appliquée dans les tâches liées à l’exploration de texte, tel que document classification et opinion l’analyse. Par exemple, quand nous voulons classer des documents dans plusieurs catégories, un type est considéré que les mot/phrases inclus dans une catégorie de document sont moins susceptibles de se produire dans une autre catégorie de document. Autrement dit, la fréquence de la distribution de mots ou d’expressions est en mesure de caractérisation catégories autre document. Dans les applications d’exploration de données texte, car les éléments individuels du contenu de texte servent généralement les données d’entrée, la fonctionnalité de processus d’ingénierie est nécessaires pour créer les fonctionnalités de word/phrase fréquence.

Pour ce faire, une technique appelée **fonction de hachage** est appliquée pour transformer efficacement les fonctionnalités de texte arbitraire en indices. Au lieu d’associer chaque fonctionnalité de texte (mots/phrases) à un index particulier, cette méthode fonctionne en appliquant une fonction de hachage pour les fonctionnalités et à l’aide de leurs valeurs de hachage comme indices directement.

Dans l’apprentissage automatique Azure, il est un module [Fonctionnalité hachage](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) qui crée ces word/phrase fonctionnalités facilement. La figure suivante montre un exemple de l’utilisation de ce module. Le jeu de données d’entrée contient deux colonnes : l’évaluation de livre compris entre 1 et 5, ainsi que le contenu de révision proprement dite. L’objectif de ce module [Fonctionnalité hachage](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) est pour récupérer les de compléter plusieurs nouvelles fonctionnalités qui affichent la fréquence d’apparition du ou les mots correspondant / réviser ou les phrases dans le carnet de particulier. Pour utiliser ce module, nous avons besoin de suivre les étapes suivantes :

* Tout d’abord, sélectionnez la colonne qui contient le texte d’entrée (« Col2 » dans cet exemple).
* Ensuite, définissez la « hachage bitsize » 8, ce qui signifie 2 ^ 8 = 256 fonctionnalités seront créées. La phase/word dans tout le texte sera hachée 256 indices. Le paramètre « Hachage bitsize » est comprise entre 1 et 31. L’ou les mots / ou les phrases sont moins susceptibles d’être haché dans l’index même si définissez qu’il s’agit d’un plus grand nombre.
* Enfin, définissez le paramètre « N-g » à 2. Cette dernière Obtient la fréquence de l’occurrence d’unigrams (une fonctionnalité de chaque mot unique) et bigrams (une fonctionnalité pour chaque paire de mots adjacentes) à partir du texte d’entrée. Le paramètre « N-g » comprise entre 0 et 10, qui indique le nombre maximal de mots séquentiels à inclure dans une fonction.  

![Module « Fonctionnalité hachage »](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figure suivante montre ce que ces fonctionnalités apparence comme.

![Exemple de « Fonctionnalité hachage »](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## <a name="conclusion"></a>Conclusion

Fonctionnalités d’ingénierie et sélectionnées accroître l’efficacité de la formation qui tente d’extraire des informations clées contenues dans les données. Ils améliorent également la puissance de ces modèles pour classer les données d’entrée avec précision et pour prévoir de manière plus fiable issues d’intérêt. Fonctionnalité ingénierie et sélection peuvent également combiner pour rendre l’apprentissage plus de calculs souple. Il fait en améliorer et puis réduire le nombre de fonctionnalités nécessaires pour étalonner ou former un modèle. Strictement mathématique, les fonctionnalités sélectionnées pour former le modèle sont un ensemble minimal de variables indépendantes qui expliquent les modèles dans les données et puis prédire issues avec succès.

Notez qu’il n’est pas toujours nécessairement pour effectuer une sélection de fonctionnalité ingénierie ou une fonctionnalité. S’il est nécessaire ou non dépend de celles que nous ont ou collecte, l’algorithme que nous sélectionnez et l’objectif de l’expérience.
 
