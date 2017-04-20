<properties
    pageTitle="Étape 4 : Former et évaluer les modèles analytiques prédictives | Microsoft Azure"
    description="Étape 4 de la développer une procédure pas à pas solution prédictive : Train, score général et évaluer plusieurs modèles dans Azure Machine apprentissage Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Procédure pas à pas étape 4 : Former et d’évaluer les modèles d’analyse prédictives

Cette rubrique contient la quatrième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Former et évaluer les modèles**
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Un des avantages de l’utilisation d’Azure Machine d’apprentissage Studio pour la création de modèles de formation des machines est la fonctionnalité permettant d’essayer plusieurs types de modèle à la fois en une expérience et comparer les résultats. Ce type d’expérience vous aide à trouver la meilleure solution pour résoudre votre problème.

À l’expérience que nous développons dans cette procédure pas à pas, nous allons créer deux types de modèles, puis comparez leurs résultats score pour déterminer quel algorithme nous à utiliser dans notre expérience final.  

Il existe plusieurs modèles que nous pouvons opérer. Pour afficher les modèles disponibles, développez le nœud **d’Apprentissage automatique** dans la palette de module, puis puis **Modèle initialisation** et les nœuds en dessous. Aux fins de cette expérience, nous permet de sélectionner la Machine vecteur prise en charge (SVM) et les modules arborescences de décision augmentée deux cours.    

> [AZURE.TIP] Pour obtenir plus d’informations avant le problème particulier que vous tentez de résoudre le mieux à l’algorithme d’apprentissage automatique meilleures, voir [comment choisir des algorithmes pour Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Former les modèles
Tout d’abord, nous allons définir le modèle d’arbre de décision augmentée :  

1.  Trouver l' [Arbre de décision augmentée deux cours] [ two-class-boosted-decision-tree] module dans la palette de module et faites-le glisser vers la zone de dessin.
2.  Trouver le [Modèle Train] [ train-model] module, faites-le glisser vers la zone de dessin et puis connectez la sortie du module d’arbre de décision augmentée pour le port d’entrée gauche (« non formé modèle ») du [Modèle de Train] [ train-model] module.
    
    L' [Arbre de décision augmentée deux cours] [ two-class-boosted-decision-tree] module initialise le modèle générique et le [Modèle former] [ train-model] utilise les données de formation pour former le modèle. 
     
3.  Connectez la sortie gauche (« résultat Dataset ») de la gauche [Exécuter le Script R] [ execute-r-script] module vers la droite d’entrée port (« Dataset ») du [Modèle Train] [ train-model] module.

    > [AZURE.TIP] Nous n’ont pas besoin deux des entrées à une des sorties du [Exécuter le Script R] [ execute-r-script] module pour cette expérience, afin que nous laisser non attachée. 

4.  Sélectionnez le [Modèle de Train] [ train-model] module. Dans le volet **Propriétés** , cliquez sur **lancer le sélecteur de colonne**, sélectionnez **Tous les Types** dans la liste déroulante vers le bas sous **Colonnes disponibles** et entrez « Risque de crédit » dans la zone de texte. Sélectionner **Tous les Types** dans la liste déroulante sous **Colonnes sélectionnées**. Sélectionnez « Risque de crédit » et cliquez sur la flèche en surbrillance pour le déplacer vers les **Colonnes sélectionnées**. 
5.  Cliquez sur **Enregistrer**.


Cette partie de l’expérience maintenant ressemble à ceci :  

![Formation d’un modèle][1]

Ensuite, nous définir le modèle SVM.  

Tout d’abord, une petite explication sur SVM. Arborescences de décision augmentée fonctionnent correctement avec les fonctionnalités de n’importe quel type. Toutefois, étant donné que le module SVM génère un classifieur linéaire, le modèle qu’il génère comporte l’erreur de test meilleures lorsque toutes les fonctions numériques ont la même échelle. Pour convertir toutes les fonctions numériques à la même échelle, nous utilisons une transformation « Tanh » (avec les [Données normaliser] [ normalize-data] module.) Cela transforme les numéros de la plage [0,1]. Fonctions de chaîne sont converties par le module SVM des fonctions par catégorie, puis aux fonctionnalités de 1/0 binaires, afin que nous n’avez pas besoin transformer manuellement les fonctions de chaîne. En outre, nous ne voulez pas transformer la risque de crédit colonne (21) - il est numérique, mais il est la valeur que nous allons formation du modèle de prédire, afin que nous avons besoin de ne fait rien.  

Pour configurer le modèle SVM, procédez comme suit :

1.  Rechercher la [Machine à vecteur Support deux cours] [ two-class-support-vector-machine] module dans la palette de module et faites-le glisser vers la zone de dessin.
2.  Avec le bouton droit le [Modèle Train] [ train-model] module, cliquez sur **Copier**, puis avec le bouton droit de la zone de dessin et cliquez sur **Coller**. La copie du [Modèle de Train] [ train-model] module possède la même sélection de colonne en tant que la cellule d’origine.
3.  Connectez la sortie du module SVM au port d’entrée gauche (« non formé modèle ») de la deuxième [Modèle Train] [ train-model] module.
4.  Trouver les [Données normaliser] [ normalize-data] module et faites-le glisser vers la zone de dessin.
5.  Se connecter l’entrée de ce module au résultat de la gauche de la gauche [Exécuter le Script R] [ execute-r-script] module (Notez que le port de sortie d’un module peut être connecté à plusieurs autres modules).
6.  Connectez le port de sortie gauche (« transformé Dataset ») des [Données normaliser] [ normalize-data] module vers la droite d’entrée port (« Dataset ») de la deuxième [Modèle Train] [ train-model] module.
7.  Dans le volet de **Propriétés** pour les [Données normaliser] [ normalize-data] module, sélectionnez **Tanh** pour le paramètre de la **méthode de Transformation** .
8.  Cliquez sur **lancer le sélecteur de colonne**, ne sélectionnez « Aucune colonne » pour **Commencer avec**, activez **inclure** dans la première liste déroulante, sélectionnez le **type de colonne** dans la deuxième zone déroulante, puis sélectionnez **numérique** dans la liste déroulante troisième. Cette option spécifie que toutes les colonnes numériques (et numérique uniquement) sont transformées.
9.  Cliquez sur le signe plus (+) à droite de cette ligne - cela crée une ligne de listes déroulantes. Sélectionnez **Exclure** dans la première liste déroulante, sélectionnez **les noms de colonne** dans la deuxième liste déroulante et cliquez sur le champ de texte et sélectionnez « Risque de crédit » dans la liste des colonnes. Cela indique que la colonne risque de crédit doit être ignorée (nous avons besoin effectuer cette action parce que cette colonne est numérique et par conséquent dans le cas contraire sera transformé).
10. Cliquez sur **OK**.  


Les [Données normaliser] [ normalize-data] module est maintenant défini pour exécuter une transformation Tanh sur toutes les colonnes numériques à l’exception de la colonne risque de crédit.  

Cette partie de notre expérience doit maintenant ressembler à ce qui suit :  

![Le deuxième modèle de formation][2]  

##<a name="score-and-evaluate-the-models"></a>Score général et que vous les modèles

Nous utilisons les données de tests qui a été séparées par les [Données fractionnées] [ split] module pour évaluer nos modèles formés. Nous pouvons puis comparez les résultats des deux modèles pour voir qui a généré meilleurs résultats.  

1.  Trouver le [Modèle de Score] [ score-model] module et faites-le glisser vers la zone de dessin.
2.  Se connecter au [Modèle Train] [ train-model] module qui est connecté à l' [Arbre de décision augmentée deux cours] [ two-class-boosted-decision-tree] module vers la gauche d’entrée port du [Modèle de Score] [ score-model] module.
3.  Connectez le port d’entrée droite du [Modèle de Score] [ score-model] module au résultat de la gauche du droit [D’exécuter le Script R] [ execute-r-script] module.

    Le [Modèle de Score] [ score-model] module peut désormais prendre les informations de crédit à partir des données tests, exécutez-le via le modèle et comparer les prévisions génère le modèle avec la colonne de risque de crédit réelle dans les données de test.

4.  Copier et coller le [Modèle de Score] [ score-model] module pour créer une deuxième copie ou un nouveau module de faire glisser la zone de dessin.
5.  Connectez le port d’entrée gauche de ce module au modèle SVM (autrement dit, se connecter au port de sortie du [Modèle Train] [ train-model] module qui est connecté à la [Machine à vecteur Support deux cours] [ two-class-support-vector-machine] module).
6.  Pour le modèle SVM, nous avons effectuer la transformation même pour les données de test comme nous l’avons fait aux données formation. Pour copier et coller les [Données normaliser] [ normalize-data] module pour créer une deuxième copie et le connecter au résultat de la gauche du droit [D’exécuter le Script R] [ execute-r-script] module.
7.  Connectez le port d’entrée droite du [Modèle de Score] [ score-model] module au résultat de la gauche des [Données normaliser] [ normalize-data] module.  

Pour évaluer les résultats de la notation deux, nous utilisons un [Modèle évaluer] [ evaluate-model] module.  

1.  Trouver le [Modèle évaluer] [ evaluate-model] module et faites-le glisser vers la zone de dessin.
2.  Connectez le port d’entrée gauche pour le port de sortie du [Modèle de Score] [ score-model] module associé au modèle d’arbre de décision augmentée.
3.  Connecter le port d’entrée droit à l’autre [Modèle de Score] [ score-model] module.  

Pour exécuter l’expérience, cliquez sur le bouton **exécuter** en dessous de la zone de dessin. Il peut prendre quelques minutes. Un indicateur de rotation sur chaque module indique qu’elle s’exécute, puis une coche verte lorsque vous avez terminé le module. Lorsque tous les modules ont une coche, l’expérience est terminée.

L’expérience doit maintenant ressembler à ceci :  

![L’évaluation des modèles][3]

Pour vérifier les résultats, cliquez sur le port de sortie du [Modèle évaluer] [ evaluate-model] module et sélectionnez **visualiser**.  

Le [Modèle doit être évaluée] [ evaluate-model] module génère une paire de courbes et les mesures que vous permettent de comparer les résultats des deux modèles évaluées. Vous pouvez afficher les résultats sous forme de courbes récepteur opérateur caractéristique (ROC), la précision/rappel courbes ou courbes levée. Autres données soient affichées incluent une matrice toute confusion, valeurs cumulées pour la zone sous la courbe (AUC) et d’autres mesures. Vous pouvez modifier la valeur de seuil en déplaçant le curseur de gauche ou la droite et voir comment il affecte l’ensemble des indicateurs.  

À droite du graphique, cliquez sur **Scored dataset** ou **dataset Scored pour comparer** pour mettre en surbrillance la courbe associée et pour afficher les mesures associées ci-dessous. Dans la légende pour les courbes, « A obtenu dataset » correspond au port d’entrée gauche du [Modèle évaluer] [ evaluate-model] module - dans notre exemple, c’est le modèle d’arbre de décision augmentée. « A obtenu dataset pour comparer » correspond au port d’entrée droite - le modèle SVM dans notre cas. Lorsque vous cliquez sur un de ces étiquettes, la courbe de ce modèle est mis en surbrillance et afficher les mesures correspondantes, comme illustré dans l’illustration suivante.  

![Courbes ROC pour les modèles][4]

En examinant ces valeurs, vous pouvez décider du modèle qui correspond le mieux à vous donnant les résultats que vous recherchez. Vous pouvez revenir en arrière et modifions votre expérience en modifiant les valeurs dans les différents modèles. 

> [AZURE.TIP] Chaque fois que vous exécutez l’expérience un enregistrement de cette itération est conservé dans l’historique de s’exécuter. Vous pouvez afficher ces itérations et revenir à un d’eux, en cliquant sur **Afficher l’historique de s’exécuter** en dessous de la zone de dessin. Vous pouvez également cliquer sur **Exécuter précédente** dans le volet de **Propriétés** pour revenir à l’itération précède celui que vous avez ouvert.
> 
Vous pouvez effectuer une copie d’une itération de votre expérience en cliquant sur **Enregistrer sous** en dessous de la zone de dessin. Propriétés de **synthèse** et une **Description** de l’expérience permet de conserver un enregistrement de que vous avez essayées dans vos itérations expérience.

>  Pour plus d’informations, voir [Gérer expérimenter itérations dans Azure Machine apprentissage Studio](machine-learning-manage-experiment-iterations.md).  


----------

**Suivant : [déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
