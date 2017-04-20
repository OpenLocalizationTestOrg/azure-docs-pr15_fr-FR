<properties
    pageTitle="Créer un texte modèles analytique dans Azure Machine apprentissage Studio | Microsoft Azure"
    description="Comment créer des modèles d’analytique du texte dans Azure Machine apprentissage Studio à l’aide de modules pour texte prétraitement, N-g ou fonctionnalité hachage"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Créer un texte modèles analytique dans Azure Machine apprentissage Studio

Vous pouvez utiliser l’apprentissage automatique Azure pour générer et effectuent des modèles analytique de texte. Ces modèles peuvent vous aider à résoudre les problèmes d’analyse classification ou opinion de document, par exemple.

Dans une expérience d’analytique du texte, vous devez généralement :

 1. Nettoyer et prétraitement jeu de données texte
 2. Extraire vecteurs fonctionnalité numérique de traitement préalable texte
 3. Modèle de classement ou régression train
 4. Score général et valider le modèle
 5. Déployez le modèle production

Dans ce didacticiel, vous Découvrez ces étapes que nous expliquant en détail un modèle d’analyse opinion à l’aide du jeu de données critiques Amazon (voir ce document recherche « Biographies, films indiens, zones de flèche et mélangeurs : Adaptation de domaine pour la Classification opinion » par John Blitzer, Mark Dredze et Fernando Pereira ; Association de calcul linguistiques (et), 2007.) Ce dataset comprend des scores de révision (1-2 ou 4 et 5) et un texte en forme libre. L’objectif est de prédire le résultat de la révision : faible (1 - 2) ou haute (4-5).

Vous pouvez trouver des expériences décrits dans ce didacticiel au Cortana Intelligence galerie :

[Prédire critiques] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Prédire critiques - expérience prédictive] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Étape 1 : Nettoyer et prétraitement jeu de données texte

Nous commençons l’expérience en divisant les scores de révision en plages par catégorie de seuils formuler le problème en tant que deux cours classification. Nous utilisons (modifier les métadonnées) (https://msdn.microsoft.com/library/azure/dn905986.aspx) et les modules [groupe par catégorie Values] (https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Créer des étiquettes](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Ensuite, nous nettoyer le texte à l’aide du module [prétraitement texte] (https://msdn.microsoft.com/library/azure/mt762915.aspx). Le nettoyage permet de réduire le bruit dans le groupe de données, vous permettent de rechercher des fonctionnalités les plus importantes et améliorer la précision du modèle final. Supprimer les mots vides - mots courants tel que « » ou « a » - et nombres, des caractères spéciaux, caractères dupliqués, adresses de messagerie et URL. Nous également de convertir le texte en minuscules, et lemmatize les mots et détecter des limites de phrase puis indiquées par « rouges. » symbole en texte traitement préalable.

![Prétraitement du texte](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

Que se passe-t-il si vous souhaitez utiliser une liste personnalisée de mots vides ? Vous pouvez passer dans comme entrée facultative. Vous pouvez également utiliser des expressions régulières personnalisées c# syntaxe pour remplacer sous-chaînes et supprimer des mots en partie du message : noms, verbes ou adjectifs.

Une fois le prétraitement terminée, nous fractionner les données en train et tester les jeux.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Étape 2 : Extraire vecteurs fonctionnalité numérique de traitement préalable texte

Pour créer un modèle de données texte, vous devez généralement convertir le texte en forme libre en vecteurs fonctionnalité numérique. Dans cet exemple, nous utilisons [caractéristiques de N-g extraire du texte] module (https://msdn.microsoft.com/library/azure/mt762916.aspx) pour transformer les données de texte dans ce format. Ce module prend une colonne de mots séparés par des espaces blancs et calcule un dictionnaire de mots ou N-g de mots, qui s’affichent dans votre jeu de données. Ensuite, il compte compte combien heures chacune word ou N-g, apparaît dans chaque enregistrement et crée vecteurs fonctionnalité de celles associées. Dans ce didacticiel, nous valeur Taille N-g 2, donc notre vecteurs fonctionnalité mots et combinaisons de deux mots suivants.

![Extraire N-g](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Nous appliquer TF * compte fil (terme fréquence Inverse Document fréquence) pondération à N-g. Cette approche ajoute une épaisseur de mots qui apparaissent fréquemment dans un seul enregistrement mais sont rares au sein de l’ensemble du dataset. Autres options incluent binaire, TF et un poids du graphique.

Ces fonctionnalités de texte ont souvent une dimension haute. Par exemple, si votre corpus comporte 100 000 mots uniques, votre espace de stockage fonctionnalité aurait 100 000 dimensions, ou plus si N-g est utilisés. Le module Extraire N-g fonctionnalités vous propose un ensemble d’options pour réduire la dimension. Vous pouvez choisir d’exclure les mots courte ou longue, ou dialogues trop ou trop fréquents pour ont une valeur prédictive significative. Dans ce didacticiel, nous exclure N-g qui s’affichent dans moins de 5 enregistrements ou plus de 80 % des enregistrements.

En outre, vous pouvez utiliser sélection de fonctionnalités pour sélectionner uniquement les fonctionnalités qui sont plus corrélation avec votre cible de prévision. Sélection de fonctionnalités Khi-deux nous permet de sélectionner 1000 fonctionnalités. Vous pouvez afficher le vocabulaire de mots sélectionnés ou N-g en cliquant sur le résultat approprié du module extrait N-g.

En guise d’alternative à l’aide des fonctionnalités de N-g extraire, vous pouvez utiliser la fonctionnalité hachage module. Remarque Bien que ce [fonctionnalité hachage] (https://msdn.microsoft.com/library/azure/dn906018.aspx) n’a pas les fonctionnalités de sélection de fonctionnalité intégré ou TF * fil un poids.

## <a name="step-3-train-classification-or-regression-model"></a>Étape 3 : Former le modèle de classement ou de régression

Maintenant le texte est transformé en colonnes numériques fonctionnalité. Le dataset contient toujours des colonnes de type chaîne à partir des étapes précédentes, afin que nous utilisons sélectionner les colonnes dans le jeu de données pour les exclure.

Nous utilisons ensuite [deux cours régression logistique] (https://msdn.microsoft.com/library/azure/dn905994.aspx) afin de prédire notre cible : score révision haute ou faible. À ce stade, le problème analytique texte a été transformé en un problème de classification normal. Vous pouvez utiliser les outils disponibles dans l’apprentissage automatique Azure pour améliorer le modèle. Par exemple, vous pouvez tester les différents classifieurs pour déterminer leur donnent des résultats comment précis, ou utiliser hyperparameter réglages pour améliorer la précision.

![Train et Score](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Étape 4 : Score général et valider le modèle

Comment vous le feriez pour valider le modèle formé ? Nous score contre le jeu de données de test et d’évaluer la précision. Toutefois, le modèle de vérification des acquis le vocabulaire N-g et leurs poids du DataSet formation. Par conséquent, nous devons utiliser ce vocabulaire et les pondérations lors de l’extraction des fonctionnalités à partir des données de test, au lieu de créer le vocabulaire unenouvelle. Par conséquent, nous ajouter module Extraire N-g fonctionnalités à la branche notation de l’expérience, connectez le vocabulaire sortie de branche de formation et définir le mode vocabulaire en lecture seule. Nous avons également désactiver le filtrage de N-g par fréquence en affectant la valeur minimale 1 instance et au maximum à 100 % et désactiver la sélection de fonctionnalité.

Une fois que la colonne de données de test texte est transformée en colonnes numériques fonctionnalité, nous exclure les colonnes de type chaîne étapes précédentes comme dans branche formation. Nous utilisons ensuite modules Score modèle à réaliser des prévisions et évaluer le modèle pour évaluer la précision.

## <a name="step-5-deploy-the-model-to-production"></a>Étape 5 : Déployez le modèle production

Le modèle est presque prêt à être déployée en production. Lorsque déployé comme un service web, il prend la chaîne de texte en forme libre comme entrée et renvoyer une prévision « haute » ou « faible ». Il utilise le vocabulaire N-g appris pour transformer le texte aux fonctionnalités et le modèle de régression logistique qualifiés pour établir une prévision à partir de ces fonctionnalités. 

Pour configurer l’expérience prédictive, nous enregistrer le vocabulaire N-g en tant que jeu de données et le modèle de régression logistique formées à partir de la branche formation de l’expérience. Ensuite, nous enregistrer l’expérience par « Enregistrer sous » pour créer un graphique d’expérience pour expérience prédictive. Nous supprimer le module de données fractionnée et la branche formation de l’expérience. Nous puis connectez le vocabulaire N-g et le modèle enregistré précédemment pour extraire les fonctionnalités de N-g et modules modèle Score, respectivement. Nous avons également supprimer le module évaluer le modèle.

Nous insérez sélectionner des colonnes dans le module Dataset avant de module texte prétraitement pour supprimer la colonne étiquette et désélectionner l’option « Colonne de score ajout au groupe de données » dans le Module Score. De cette façon, le service web ne demande pas l’étiquette qu'il tente de prédire et ne pas l’écho fonctionnalités de l’entrée en réponse.

![Expérience prédictive](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Nous avons une expérience pouvant être publiée sous la forme d’un service web et appelée à l’aide de l’exécution de requête-réponse ou un lot d’API.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les modules analytique du texte à partir de [documentation MSDN] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
