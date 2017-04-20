<properties
    pageTitle="Étape 3 : Créer une nouvelle expérience d’apprentissage automatique | Microsoft Azure"
    description="Étape 3 de la développer une procédure pas à pas solution prédictive : créer une nouvelle expérience de formation dans Azure Machine apprentissage Studio."
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
    ms.date="10/05/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Procédure à étape 3 : Créer une nouvelle expérience d’apprentissage automatique Azure

Il s’agit de la troisième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  **Créer une nouvelle expérience**
4.  [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Dans cette procédure pas à pas, l’étape suivante consiste à créer une expérience dans Studio apprentissage Machine qui utilise le dataset que nous téléchargés.  

1.  Dans Studio, cliquez sur **+ Nouveau** en bas de la fenêtre.
2.  Sélectionnez **expérience**, puis « Expérience vide ». Sélectionnez le nom d’expérience par défaut en haut de la zone de dessin et attribuer un nom significatif

    > [AZURE.TIP] Il est recommandé pour renseigner **Résumé** et une **Description** pour l’expérience dans le volet **Propriétés** . Ces propriétés vous offrent la possibilité de l’expérience de document afin que toutes les personnes qui examine ultérieurement l’attention vos objectifs et la méthodologie.

3.  Dans la palette de module à gauche de l’expérience toile, développez **Les groupes de données enregistré**.
4.  Recherchez le jeu de données que vous avez créé sous **Mes jeux de données** et faites-la glisser vers la zone de dessin. Vous trouverez également le jeu de données en entrant le nom dans la zone **Rechercher** située au-dessus de la palette.  

##<a name="prepare-the-data"></a>Préparer les données
Vous pouvez afficher les 100 premières lignes de données et certaines informations statistiques pour le jeu de données entière en cliquant sur le port de sortie du jeu de données (le petit cercle en bas) et en sélectionnant **visualiser**.  

Étant donné que le fichier de données n’a pas été fournis avec en-têtes de colonne, Studio a fourni titres génériques (Col1, Col2, *etc.*). Bonne en-têtes ne sont pas nécessaires pour créer un modèle, mais ils facilitent de travailler avec les données à l’expérience. En outre, quand nous publier finalement ce modèle dans un service web, les titres aidera à identifier les colonnes à l’utilisateur du service.  

Nous pouvons ajouter des en-têtes de colonnes en utilisant les [Modifier les métadonnées] [ edit-metadata] module.
Vous utilisez les [Modifier les métadonnées] [ edit-metadata] module pour modifier les métadonnées associées à un groupe de données. Dans ce cas, il peut fournir des autres noms conviviales pour les en-têtes de colonne. 

Utiliser [Modifier les métadonnées][edit-metadata], vous commencez par spécifiez les colonnes à modifier (dans ce cas, tous les.) Ensuite, vous spécifiez l’action à effectuer sur ces colonnes (dans ce cas, modification des en-têtes de colonne.)

1.  Dans la palette de module, tapez « métadonnées » dans la zone de **recherche** . Vous verrez [Modifier les métadonnées] [ edit-metadata] s’affichent dans la liste du module.
2.  Cliquez et faites glisser les [Modifier les métadonnées] [ edit-metadata] module sur la zone de dessin et déposez-le sous le dataset nous ajoutées précédemment.
3.  Connecter le jeu de données pour [Modifier les métadonnées][edit-metadata]: cliquez sur le port de sortie du jeu de données (le petit cercle en bas du groupe de données.) Ensuite, faites glisser vers le port d’entrée de [Modifier les métadonnées] [ edit-metadata] (le petit cercle en haut du module), puis relâchez le bouton de la souris. Le jeu de données et le module de resteront connectés même si vous soit déplacez dans la zone de dessin.

    L’expérience doit maintenant ressembler à ceci :  

    ![Ajout de métadonnées modifier][2]
    
    Le point d’exclamation rouge indique que nous n’avons pas encore définir les propriétés de ce module. Nous allons faire maintenant.
    
    > [AZURE.TIP] Vous pouvez ajouter un commentaire à un module en double-cliquant sur le module et entrer du texte. Cela peut vous aider à suivre l’en un clin de œil que le module dans votre expérience. Dans ce cas, double-cliquez sur les [Modifier les métadonnées] [ edit-metadata] module et type de commentaire « ajouter en-têtes de colonne ». Cliquez sur ailleurs dans la zone de dessin pour fermer la zone de texte. Pour afficher le commentaire, cliquez sur la flèche vers le bas sur le module.

4.  Sélectionnez [Modifier les métadonnées][edit-metadata], puis, dans le volet de **Propriétés** à droite de la zone de dessin, cliquez sur **lancer le sélecteur de colonne**.
5.  Dans la boîte de dialogue **Sélectionner les colonnes** , sélectionnez toutes les lignes dans **Les colonnes disponibles** , cliquez sur > pour les déplacer vers les **Colonnes sélectionnées**.
La boîte de dialogue doit ressembler à ceci : ![sélecteur de colonne avec toutes les colonnes sélectionnées][4]
7.  Cliquez sur la coche **OK** .
8.  Dans le volet **Propriétés** , recherchez le paramètre de **nouveaux noms de colonne** . Dans ce champ, entrez une liste de noms pour les 21 colonnes dans le groupe de données, séparés par des virgules et dans l’ordre des colonnes. Vous pouvez obtenir les noms de colonnes de la documentation de jeu de données sur le site Web UCI, ou pour faciliter la tâche, vous pouvez copier et coller la liste suivante :  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Le volet de propriétés ressemble à ceci :

    ![Propriétés pour modifier les métadonnées][1]

> [AZURE.TIP] Si vous souhaitez vérifier les en-têtes de colonne, exécutez l’expérience (cliquez sur **exécuter** située sous la zone expérience). Lorsqu’elle termine en cours d’exécution (une coche verte apparaîtront sur [Modifier les métadonnées][edit-metadata]), cliquez sur le port de sortie de [Modifier les métadonnées] [ edit-metadata] module et sélectionnez **visualiser**. Vous pouvez afficher le résultat de n’importe quel module de la même façon pour afficher la progression des données via l’expérience.

##<a name="create-training-and-test-datasets"></a>Création de formation et test des groupes de données

L’étape suivante de l’expérience est pour générer des jeux de données distinct que nous utiliserons de formation et test notre modèle.

Pour ce faire, nous utilisons les [Données fractionnées] [ split] module.  

1.  Trouver les [Données fractionnées] [ split] module, faites-le glisser vers la zone de dessin et connectez-la à la dernière [Modifier les métadonnées] [ edit-metadata] module.
2.  Par défaut, le coefficient de fractionnement est 0,5 et le paramètre **Randomized fractionner** est défini. Cela signifie qu’une partie aléatoire des données est sortie via un seul port les [Données fractionnées] [ split] module et la moitié via l’autre. Vous pouvez ajuster ces éléments, ainsi que le paramètre de **valeur de départ aléatoire** , pour modifier la séparation entre la formation et test des données. Dans cet exemple, nous allons les laisser en tant que-est.
    
    > [AZURE.TIP] La propriété **Fraction de lignes dans le premier ensemble de données de sortie** détermine la quantité de données est sortie via le port de sortie gauche. Par exemple, si vous définissez le rapport entre 0,7, est de 70 % des données est sortie via le port gauche et 30 % via le port approprié.  
    
3. Double-cliquez sur les [Données fractionnées] [ split] module et entrez le commentaire, « données formation/test fractionnement 50 % ». 

Nous pouvons utiliser les sorties les [Données fractionnées] [ split] module nous comme Toutefois, nous allons choisir d’utiliser le résultat de la gauche en tant que données d’apprentissage et de droite comme test de données de résultat.  

Comme indiqué sur le site Web UCI, le coût de classer par erreur entrées un risque élevé crédit comme faible est cinq fois plus grand que le coût d’un risque de crédit faible élevé de classer par erreur entrées. Pour prendre en compte, nous allons générer un nouveau groupe de données qui reflète cette fonction de coût. Dans le nouveau groupe de données, chaque exemple risque élevé est répliquée cinq fois, tandis que chaque exemple faible risque n’est pas répliquée.   

Nous pouvons effectuer cette réplication à l’aide de code R :  

1.  Rechercher et faites glisser le [Exécuter le Script R] [ execute-r-script] module sur l’expérience toile et connectez le port de sortie gauche des [Données fractionnées] [ split] module sur le premier port d’entrée (« Dataset1 ») du [Exécuter le Script R] [ execute-r-script] module.
2. Double-cliquez sur le [Exécuter le Script R] [ execute-r-script] module, entrez le commentaire, « Set ajustement des coûts ».
2.  Dans le volet **Propriétés** , supprimez le texte par défaut dans le paramètre de **Script R** et entrez ce script :

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Nous avons besoin effectuer cette opération de réplication même pour chaque entrée des [Données fractionnées] [ split] module afin que les données de formation et d’essais aient le même ajustement des coûts.

1.  Avec le bouton droit de l' [Exécuter le Script R] [ execute-r-script] module et sélectionnez **Copier**.
2.  Avec le bouton droit de la zone de dessin expérience et sélectionnez **Coller**.
3.  Connectez le premier port d’entrée de ce [Exécuter le Script R] [ execute-r-script] module à droite de sortie port les [Données fractionnées] [ split] module. 
4.  En bas de la zone de dessin, cliquez sur **exécuter**. 

> [AZURE.TIP] La copie du module exécuter le Script R contient le même script que le module d’origine. Lorsque vous copiez et collez un module dans la zone de dessin, la copie conserve toutes les propriétés de la cellule d’origine.  

Notre expérience maintenant ressemble à ceci :

![Ajout de module fractionnée et scripts R][3]

Pour plus d’informations sur l’utilisation des scripts R dans vos expériences, voir [prolonger votre expérience avec R](machine-learning-extend-your-experiment-with-r.md).

**Suivant : [Train évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
