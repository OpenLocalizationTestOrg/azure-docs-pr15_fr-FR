<properties
   pageTitle="Analyser les données des données Lake Store à l’aide de Power BI | Microsoft Azure"
   description="Utiliser Power BI pour analyser des données stockées dans Azure données Lake Store"
   services="data-lake-store" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analyser les données des données Lake Store à l’aide de Power BI

Dans cet article, vous allez apprendre à utiliser Power BI Desktop pour analyser et visualiser des données stockées dans Azure données Lake sécurisée.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Compte azure données Lake Store**. Suivez les instructions sur la [prise en main Azure données Lake Store à l’aide du portail Azure](data-lake-store-get-started-portal.md). Cet article suppose que vous avez déjà créé un compte de données Lake Store, appelé **mybidatalakestore**et téléchargé un exemple de fichier de données (**Drivers.txt**) lui. Cet exemple de fichier est disponible au téléchargement à partir [d’Azure données Lake Git référentiel](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Power BI Desktop**. Vous pouvez le télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Créer un rapport dans Power BI Desktop

1. Lancez Power BI Desktop sur votre ordinateur.

2. À partir du ruban **accueil** , cliquez sur **Obtenir des données**, puis cliquez sur plus. Dans la boîte de dialogue **Obtenir des données** , cliquez sur **Azure**et cliquez sur **Azure données Lake Store**, puis cliquez sur **se connecter**.

    ![Se connecter au magasin Lake de données] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Se connecter au magasin Lake de données")

3. Si vous voyez une boîte de dialogue sur le lien en cours d’une phase de développement, opter pour continuer.

4. Dans la boîte de dialogue **Microsoft Azure données Lake Store** fournir l’URL à votre compte Data Lake Store, puis cliquez sur **OK**.

    ![URL de données Lake Store] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL de données Lake Store")

5. Dans la boîte de dialogue suivante, cliquez sur **se connecter** pour vous connecter à un compte de données Lake Store. Vous êtes redirigé vers la page de connexion de votre organisation. Suivez les invites pour vous connecter au compte.

    ![Connectez-vous à données Lake Store] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Connectez-vous à données Lake Store")

6. Une fois que vous avez correctement connecté, cliquez sur **se connecter**.

    ![Se connecter au magasin Lake de données] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Se connecter au magasin Lake de données")

7. La boîte de dialogue suivante montre le fichier que vous avez téléchargée à votre compte Data Lake Store. Vérifier les informations et puis cliquez sur **charger**.

    ![Charger les données à partir de données Lake Store] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Charger les données à partir de données Lake Store")

8. Une fois les données chargées dans Power BI, vous verrez les champs suivants dans l’onglet **champs** .

    ![Champs importés] (./media/data-lake-store-power-bi/imported-fields.png "Champs importés")

    Pour visualiser et analyser les données, nous préfèrent les données soient disponibles par les champs suivants

    ![Champs souhaité] (./media/data-lake-store-power-bi/desired-fields.png "Champs souhaité")

    Dans la procédure suivante, nous met à jour la requête pour convertir les données importées dans le format souhaité.

9. À partir du ruban **accueil** , cliquez sur **Modifier les requêtes**.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/edit-queries.png "Modifier des requêtes")

10. Dans l’éditeur de requête, sous la colonne **contenu** , cliquez sur **binaire**.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/convert-query1.png "Modifier des requêtes")

11. Vous verrez une icône de fichier, qui représente le fichier **Drivers.txt** que vous avez téléchargée. Cliquez sur le fichier, puis cliquez sur **CSV**.  

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/convert-query2.png "Modifier des requêtes")

12. Vous devriez voir un résultat comme illustré ci-dessous. Vos données sont désormais disponibles dans un format que vous pouvez utiliser pour créer des visualisations.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/convert-query3.png "Modifier des requêtes")

13. À partir du ruban **accueil** , cliquez sur **Fermer et appliquer**, puis cliquez sur **Fermer et appliquer**.

    ![Modifier des requêtes] (./media/data-lake-store-power-bi/load-edited-query.png "Modifier des requêtes")

14. Une fois que la requête est mis à jour, l’onglet **champs** affiche les nouveaux champs disponibles pour la visualisation.

    ![Mises à jour les champs] (./media/data-lake-store-power-bi/updated-query-fields.png "Mises à jour les champs")

15. Laissez-nous créer un graphique en secteurs pour représenter les pilotes dans chaque ville d’un pays donné. Pour ce faire, effectuez les sélections suivantes.

    1. Sous l’onglet visualisations, cliquez sur le symbole d’un graphique en secteurs.

        ![Graphique en secteurs créer] (./media/data-lake-store-power-bi/create-pie-chart.png "Graphique en secteurs créer")

    2. Les colonnes que nous allons utiliser sont **colonne 4** (nom de la ville) et **7 colonne** (nom du pays). Faites glisser ces colonnes à partir de l’onglet **champs** **visualisations** onglet comme illustré ci-dessous.

        ![Créer des visualisations] (./media/data-lake-store-power-bi/create-visualizations.png "Créer des visualisations")

    3. Le graphique en secteurs doit maintenant ressembler à similaire à celui illustré ci-dessous.

        ![Graphique en secteurs] (./media/data-lake-store-power-bi/pie-chart.png "Créer des visualisations")

16. En sélectionnant un pays spécifique dans filtres au niveau de la page, vous pouvez maintenant voir le nombre de pilotes dans chaque ville du pays sélectionné. Par exemple, sous l’onglet **visualisations** , sous **filtres au niveau de Page**, sélectionnez **Brésil**.

    ![Sélectionnez un pays] (./media/data-lake-store-power-bi/select-country.png "Sélectionnez un pays")

17. Graphique en secteurs est automatiquement mis à jour pour afficher les pilotes dans les villes du Brésil.

    ![Pilotes dans un pays] (./media/data-lake-store-power-bi/driver-per-country.png "Pilotes par pays")

18. Dans le menu **fichier** , cliquez sur **Enregistrer** pour enregistrer un fichier Power BI Desktop la visualisation.

## <a name="publish-report-to-power-bi-service"></a>Publier le rapport service Power BI

Une fois que vous avez créé les visualisations dans Power BI Desktop, vous pouvez le partager avec d’autres personnes en le publiant sur le service Power BI. Pour obtenir des instructions sur la procédure à suivre, voir [publier à partir de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Voir aussi

* [Analyser les données des données Lake Store à l’aide de données Lake Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
