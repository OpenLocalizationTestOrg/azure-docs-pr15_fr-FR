<properties
   pageTitle="Enregistrer les données à partir de données Lake Store dans le catalogue de données Azure | Microsoft Azure"
   description="Enregistrer les données à partir de données Lake Store dans le catalogue de données Azure"
   services="data-lake-store,data-catalog" 
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Enregistrer les données à partir de données Lake Store dans le catalogue de données Azure

Dans cet article, vous allez apprendre à intégrer Azure données Lake Store catalogue de données Azure à rendre vos données détectable au sein d’une organisation grâce à l’intégration avec le catalogue de données. Pour plus d’informations sur les données de catalogue, voir le [Catalogue de données Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Pour mieux comprendre scénarios dans lesquels vous pouvez utiliser le catalogue de données, voir [scénarios courants du catalogue de données Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Activer votre abonnement Azure** pour les données Lake Store Public Preview. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).

- **Compte azure données Lake Store**. Suivez les instructions sur la [prise en main Azure données Lake Store à l’aide du portail Azure](data-lake-store-get-started-portal.md). Pour ce didacticiel, laissez-nous créez un compte de données Lake Store appelé **datacatalogstore**. 

    Une fois que vous avez créé le compte, télécharger un jeu de données d’exemple vers celui-ci. Pour ce didacticiel, laissez-nous télécharger tous les fichiers .csv sous le dossier **AmbulanceData** dans le [Référentiel de Git Azure données Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Vous pouvez utiliser différents clients, tels que l' [Explorateur de stockage Azure](http://storageexplorer.com/), pour télécharger des données dans un conteneur blob.

- **Catalogue de données azure**. Votre organisation doit avoir déjà un catalogue de données Azure créés pour votre organisation. Qu’un seul catalogue est autorisé pour chaque organisation.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registre données Lake Store en tant que source de catalogue de données

>[AZURE.VIDEO adcwithadl] 

1. Accédez à `https://azure.microsoft.com/services/data-catalog`, puis cliquez sur **mise en route**.

2. Se connecter au portail de catalogue de données Azure, puis cliquez sur **publier les données**.

    ![Enregistrer une source de données] (./media/data-lake-store-with-data-catalog/register-data-source.png "Enregistrer une source de données")

3. Dans la page suivante, cliquez sur **Lancer l’Application**. Cela télécharge le fichier manifeste d’application sur votre ordinateur. Double-cliquez sur le fichier manifeste pour démarrer l’application.

4. Dans la page Bienvenue, cliquez sur **se connecter**et entrez vos informations d’identification.

    ![Écran d’accueil] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Écran d’accueil")

5. Dans la page Sélectionnez une Source de données, sélectionnez **Lake de données Azure**, puis cliquez sur **suivant**.

    ![Sélectionnez la source de données] (./media/data-lake-store-with-data-catalog/select-source.png "Sélectionnez la source de données")

6. Dans la page suivante, indiquez le nom de compte magasin Lake de données que vous voulez enregistrer dans le catalogue de données. Laisser les autres options par défaut, puis sur **se connecter**.

    ![Se connecter à la source de données] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Se connecter à la source de données")

7. La page suivante peut être divisée en les segments suivants.

    un. La zone **Serveur hiérarchie** représente la structure de dossiers de compte données Lake Store. **$Root** représente la racine de compte données Lake Store et **AmbulanceData** le dossier créé à la racine du compte données Lake Store.

    b. La zone **objets disponibles** répertorie les fichiers et dossiers sous le dossier **AmbulanceData** .

    c. **Les objets sur la case inscrits** répertorie les fichiers et dossiers que vous voulez enregistrer dans le catalogue de données Azure.

    ![Structure de données d’affichage] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Structure de données d’affichage")

8. Pour ce didacticiel, vous devez enregistrer tous les fichiers dans le répertoire. Pour ce faire, cliquez sur le bouton (![déplacer des objets de](./media/data-lake-store-with-data-catalog/move-objects.png "déplacer des objets")) pour déplacer tous les fichiers vers case **accepte les objets** . 

    Étant donné que les données seront enregistrées dans un catalogue de données à l’échelle de l’organisation, il est une approche recommandé pour ajouter des métadonnées que vous pouvez utiliser ultérieurement pour localiser rapidement les données. Par exemple, vous pouvez ajouter une adresse de messagerie pour le propriétaire de données (par exemple, celui qui est transfert des données) ou ajouter un indicateur pour identifier les données. La capture d’écran ci-dessous montre une balise que nous ajouter aux données.

    ![Structure de données d’affichage] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Structure de données d’affichage")

    Cliquez sur **Enregistrer**.

8. La capture d’écran suivante indique que les données sont correctement enregistrées dans le catalogue de données.

    ![Enregistrement terminé] (./media/data-lake-store-with-data-catalog/registration-complete.png "Structure de données d’affichage")

9. Cliquez sur le **Portail d’affichage** pour revenir en arrière sur le portail du catalogue de données et vérifiez que vous pouvez désormais accéder les données enregistrées à partir du portail. Pour rechercher les données, vous pouvez utiliser la balise que vous avez utilisé lors de l’enregistrement des données.

    ![Rechercher des données dans le catalogue] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Rechercher des données dans le catalogue")

10. Vous pouvez désormais effectuer les opérations, telles que l’ajout d’annotations et documentation aux données. Pour plus d’informations, consultez les liens suivants.
    * [Annoter des sources de données dans le catalogue de données](../data-catalog/data-catalog-how-to-annotate.md)
    * [Sources de données de document dans le catalogue de données](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Voir aussi

* [Annoter des sources de données dans le catalogue de données](../data-catalog/data-catalog-how-to-annotate.md)
* [Sources de données de document dans le catalogue de données](../data-catalog/data-catalog-how-to-documentation.md)
* [Intégrer des données Lake Store avec d’autres services Azure](data-lake-store-integrate-with-other-services.md)
