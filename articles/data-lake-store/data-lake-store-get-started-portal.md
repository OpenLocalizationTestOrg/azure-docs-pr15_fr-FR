<properties 
   pageTitle="Prise en main données Lake Store | Azure" 
   description="Utiliser le portail pour créer un compte Data Lake Store et effectuer les opérations de base du magasin de données Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Prise en main Azure données Lake Store à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser le portail Azure pour créer un compte Azure données Lake Store et effectuer des opérations de base telles que la création de dossiers, chargement et téléchargement des fichiers de données, supprimez votre compte, etc.. Pour plus d’informations sur les données Lake Store, voir [Vue d’ensemble du magasin de Lake des données Azure](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>Vous découvrez rapidement avec vidéos ?

Regardez les vidéos suivantes pour commencer à utiliser les données Lake Store.

* [Créer un compte Data Lake Store](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gérer les données dans le magasin de Lake de données à l’aide de l’Explorateur de données](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure données Lake Store

1. Ouverture de session au nouveau [Portail Azure](https://portal.azure.com).

2. Cliquez sur **Nouveau**et cliquez sur **données + stockage**, puis cliquez sur **Azure données Lake Store**. Lisez les informations contenues dans la carte **Azure données Lake Store** , puis cliquez sur **créer** dans le coin inférieur gauche de la cuillère.

3. Dans la carte de **Nouveau données Lake magasin** , fournissent les valeurs comme indiqué dans la capture d’écran ci-dessous :

    ![Créer un nouveau compte Azure données Lake Store] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Créer un nouveau compte Lake de données Azure")

    - **Abonnement**. Sélectionnez l’abonnement dans lesquelles vous voulez créer un nouveau compte de données Lake Store.
    - **Groupe de ressources**. Sélectionnez un groupe de ressources existant, ou cliquez sur **créer un groupe de ressources** pour en créer un. Un groupe de ressources est un conteneur qui conserve les ressources associées à une application. Pour plus d’informations, voir [Groupes de ressources dans Azure](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Emplacement**: sélectionnez un emplacement où vous souhaitez créer le compte de données Lake Store.

4. Sélectionnez **attacher aux Startboard** si vous souhaitez que le compte Lake données soient accessibles à partir de la Startboard.

5. Cliquez sur **créer**. Si vous avez choisi épingler le compte à le startboard, vous revenez à la startboard et vous pouvez voir l’avancement de votre compte de données Lake Store mise en service. Une fois que le compte de données Lake Store est mis en service, la carte de compte s’affiche.

6. Développez **Essentials** menu déroulant pour afficher les informations relatives à votre compte magasin Lake de données telles que la ressource regroupez sont un composant de, l’emplacement, etc.. Cliquez sur l’icône de **Démarrage rapide** pour afficher des liens vers d’autres ressources liées aux données Lake Store.

    ![Compte de votre Boutique Lake de données Azure] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Compte de votre Lake de données Azure")

## <a name="createfolder"></a>Créer des dossiers dans un compte Azure données Lake Store

Vous pouvez créer des dossiers sous votre compte de données Lake Store pour gérer et stocker des données.

1. Ouvrez le compte magasin Lake de données que vous venez de créer. Dans le volet gauche, cliquez sur **Parcourir**et cliquez sur **Données Lake Store**, puis à partir de la carte de données Lake Store, cliquez sur le nom du compte sous lequel vous souhaitez créer des dossiers. Si vous épinglé le compte à le startboard, cliquez sur cette vignette compte.

2. Dans votre carte de compte données Lake Store, cliquez sur **Explorateur de données**.

    ![Création des dossiers dans un compte données Lake Store] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Création des dossiers dans un compte données Lake Store")

3. Dans votre carte de compte données Lake Store, cliquez sur **Nouveau dossier**, entrez un nom pour le nouveau dossier, puis cliquez sur **OK**.
    
    ![Création des dossiers dans un compte données Lake Store] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Création des dossiers dans un compte données Lake Store")
    
    Le nouveau dossier créé apparaît dans la carte de **L’Explorateur de données** . Vous pouvez créer des dossiers imbriqués jusqu'à n’importe quel niveau.

    ![Création des dossiers dans un compte Lake de données] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Création des dossiers dans un compte Lake de données")


## <a name="uploaddata"></a>Télécharger des données à compte Azure données Lake Store

Vous pouvez télécharger vos données à un compte Azure données Lake Store directement au niveau de la racine ou vers un dossier que vous avez créée dans le compte. Dans la capture d’écran ci-dessous, suivez les étapes pour télécharger un fichier dans un sous-dossier à partir de la carte de **L’Explorateur de données** . Dans cette capture d’écran, le fichier est téléchargé dans un sous-dossier affichée dans la barre de navigation (marqué dans un cadre rouge).

Si vous cherchez des exemples de données à télécharger, vous pouvez obtenir le dossier **Ambulance données** à partir du [Référentiel de Git Azure données Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Télécharger des données] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Télécharger des données")


## <a name="properties"></a>Propriétés et actions disponibles pour les données stockées

Cliquez sur le fichier nouvellement ajouté pour ouvrir la carte de **Propriétés** . Les propriétés associées le fichier et les actions que vous pouvez effectuer sur le fichier sont disponibles dans cette carte. Vous pouvez également copier le chemin d’accès complet au fichier dans votre compte Azure données Lake Store, mise en évidence dans la zone rouge dans la capture d’écran ci-dessous.

![Propriétés des données] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriétés des données")

* Cliquez sur **Aperçu** pour afficher un aperçu du fichier, directement à partir du navigateur. Vous pouvez spécifier le format de l’aperçu ainsi. Cliquez sur **Aperçu**, cliquez sur **Format** dans la carte de **l’Aperçu du fichier** et dans la carte de **Format du fichier de prévisualisation** spécifier les options telles que nombre de lignes à afficher, le codage à utiliser, séparateur à utiliser, etc..

  ![Format d’aperçu de fichier] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Format d’aperçu de fichier")

* Cliquez sur **Télécharger** pour télécharger le fichier sur votre ordinateur.

* Cliquez sur **Renommer le fichier** pour renommer le fichier.

* Cliquez sur **Supprimer le fichier** pour supprimer le fichier.


## <a name="secure-your-data"></a>Protéger vos données

Vous pouvez sécuriser les données stockées dans votre compte sur magasin de Lake données Azure Azure Active Directory et contrôle d’accès (utilisateurs). Pour obtenir des instructions sur la façon de procéder, voir [sécurisation des données Azure données Lake Store](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Compte de supprimer le magasin Lake données Azure

Pour supprimer un compte Azure données Lake Store, à partir de votre carte de données Lake Store, cliquez sur **Supprimer**. Pour confirmer l’action, vous serez invité à entrer le nom du compte que vous voulez supprimer. Entrez le nom du compte, puis cliquez sur **Supprimer**.

![Supprimer des données Lake compte] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Supprimer des données Lake compte")


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Journaux de diagnostic d’accès de données Lake Store](data-lake-store-diagnostic-logs.md)
