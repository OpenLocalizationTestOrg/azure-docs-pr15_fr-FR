<properties
   pageTitle="Analyser des données avec apprentissage automatique Azure | Microsoft Azure"
   description="Utilisez Azure Machine d’apprentissage pour créer une machine prédictive apprentissage du modèle basé sur des données stockées dans le magasin de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analyser des données avec apprentissage automatique Azure

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ce didacticiel utilise Azure Machine d’apprentissage pour créer une machine prédictive apprentissage du modèle basé sur des données stockées dans le magasin de données SQL Azure. Plus précisément, cela crée une campagne marketing ciblée pour Adventure Works, le magasin de vélo, si un client est susceptible d’acheter un vélo ou non la prédiction.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Conditions préalables
Pour lancer ce didacticiel, vous devez :

- Un Data Warehouse SQL préchargé avec des exemples de données AdventureWorksDW. Pour ce service, voir [créer un Data Warehouse SQL][] et choisir de charger les exemples de données. Si vous possédez un data warehouse déjà mais n’avez pas de données d’exemple, vous pouvez [charger les exemples de données manuellement][].

## <a name="1-get-data"></a>1. obtenir des données
Les données sont dans l’affichage dbo.vTargetMail dans la base de données AdventureWorksDW. Pour lire ces données :

1. Connectez-vous à [apprentissage automatique Azure studio][] , puis cliquez sur Mes expériences.
2. Cliquez sur **+ Nouveau** et sélectionnez **Expérience vide**.
3. Entrez un nom pour votre expérience : destinée aux Marketing.
4. Faites glisser le module de **lecture** à partir du volet de modules dans la zone de dessin.
5. Dans le volet Propriétés, spécifiez les détails de votre base de données SQL Data Warehouse.
6. Spécifiez la **requête** de base de données pour lire les données utiles.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Exécuter l’expérience en cliquant sur **exécuter** dans la zone de dessin expérience.
![Exécuter l’expérience][1]


Une fois l’expérience est terminée avec succès, cliquez sur le port de sortie en bas du module lecteur et sélectionnez **visualiser** pour afficher les données importées.
![Afficher les données importées][3]


## <a name="2-clean-the-data"></a>2. nettoyage des données
Pour effacer les données, déplacer certaines colonnes ne sont pas pertinentes pour le modèle. Pour cela :

1. Faites glisser le module de **Colonnes de projet** dans la zone de dessin.
2. Cliquez sur **sélecteur de colonne lancement** dans le volet de propriétés pour spécifier les colonnes que vous souhaitez supprimer.
![Colonnes de projet][4]

3. Exclure les deux colonnes : CustomerAlternateKey et GeographyKey.
![Supprimer les colonnes inutiles][5]


## <a name="3-build-the-model"></a>3. créer le modèle
Nous fractionne les données 80-20 : 80 % pour former un apprentissage modèle et 20 % pour tester le modèle de l’ordinateur. Nous nous engageons à utiliser des algorithmes « Classe deux » pour ce problème classification binaire.

1. Faites glisser le module de **fractionnement** dans la zone de dessin.
2. Entrez 0,8 de Fraction de lignes dans le premier ensemble de données de sortie dans le volet Propriétés.
![Fractionner des données dans un jeu de formation et d’essai][6]
3. Faites glisser le module de **Classe de deux augmentée un arbre de décision** dans la zone de dessin.
4. Faites glisser le module **Train modèle** dans la zone de dessin et spécifier les entrées. Ensuite, cliquez sur **lancer le sélecteur de colonne** dans le volet Propriétés.
      - Première entrée : algorithme ML.
      - Deuxième entrée : données former l’algorithme sur.
![Connectez le module Train modèle][7]
5. Sélectionnez la colonne **BikeBuyer** comme étant la colonne à prévoir.
![Sélectionnez la colonne à prévoir][8]


## <a name="4-score-the-model"></a>4. score du modèle
À présent, nous allez tester comment le modèle s’exécute sur les données de test. Nous allons comparer l’algorithme de notre choix avec un autre algorithme pour voir ce qui est plus performante.

1. Faites glisser module **Score modèle** dans la zone de dessin.
    Première entrée : formé la deuxième entrée du modèle : tester des données ![Score du modèle][9]
2. Faites glisser l' **Ordinateur de deux cours Bayes Point** dans la zone de dessin expérience. Nous allons comparer comment cet algorithme fonctionne par rapport à l’arbre de décision deux cours augmentée.
3. Copiez et collez les modules Train modèle et le modèle de note dans la zone de dessin.
4. Faites glisser le module **Évaluer un modèle** dans la zone de dessin pour comparer les deux algorithmes.
5. **Exécuter** l’expérience.
![Exécuter l’expérience][10]
6. Cliquez sur le port de sortie en bas du module évaluer le modèle, puis cliquez sur visualiser.
![Visualiser les résultats de l’évaluation][11]

Les métriques fournies sont la courbe ROC, la courbe de diagramme et levée du rappel de précision. Nous prenons les mesures, nous voyons que le premier modèle effectuée supérieures à l’autre. Pour examiner le que le premier modèle prédit, cliquez sur port de sortie du modèle de note, cliquez sur visualiser.
![Visualiser les résultats de la note][12]

Vous verrez deux colonnes supplémentaires ajoutés à votre groupe de données de test.

- Probabilités évaluées : la probabilité qu’un client est un achats vélo.
- Étiquettes évaluées : la classification effectuée par le modèle – achats vélo (1) ou non (0). Ce seuil de probabilité pour les étiquettes est défini sur 50 % et peut être ajusté.

Comparaison de la colonne BikeBuyer (réelle) avec les étiquettes a obtenu (prévision), vous pouvez voir comment le modèle a effectué. Dans les étapes suivantes, vous pouvez utiliser ce modèle pour réaliser des prévisions pour les nouveaux clients et publier ce modèle comme un service web ou écrire les résultats sur Data Warehouse SQL.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création prédictive d’apprentissage automatique des modèles, reportez-vous à [Introduction aux Machine d’apprentissage sur Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure studio d’apprentissage automatique]:https://studio.azureml.net/
[Introduction à la formation sur Azure de l’ordinateur]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[charger les exemples de données manuellement]: sql-data-warehouse-load-sample-databases.md
[Créer un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md
