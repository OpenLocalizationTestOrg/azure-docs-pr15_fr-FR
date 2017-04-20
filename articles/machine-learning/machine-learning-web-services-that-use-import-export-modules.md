<properties
    pageTitle="Déploiement des services web ML Azure qui utilisent des modules d’importation de données et d’exportation de données | Microsoft Azure"
    description="Découvrez comment utiliser les modules de données d’importation et exportation de données pour envoyer et recevoir des données à partir d’un service web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Déploiement des services web ML Azure qui utilisent l’importation des données et des modules d’exportation de données 

Lorsque vous créez une expérience prédictive, vous ajoutez généralement une entrée de service web et la sortie. Lorsque vous déployez l’expérience, consommateurs peuvent envoyer et recevoir des données à partir du service web via les entrées et sorties. Pour certaines applications, données consommateurs est disponible à partir d’un flux de données ou se trouvent déjà dans une source de données externe, comme le stockage Blob Azure. Dans ce cas, ils n’avez pas besoin de lire et écrire des données à l’aide des sorties et les entrées de service web. Au lieu de cela, ils peuvent utiliser le Service de l’exécution de lot (BES) pour lire les données à partir de la source de données à l’aide d’un module d’importer des données et écrire les résultats de la notation dans un emplacement de données à l’aide d’un module d’exporter les données.

L’importation de données et les modules d’exportation des données, peuvent lire et écrire à un nombre de données fournissent des emplacements tels qu’une URL Web via HTTP, une requête ruche, une base de données SQL Azure, stockage de Table Azure, stockage d’objets Blob Azure, un flux de données ou une base de données SQL locales.

Cette rubrique utilise le « exemple 5 : évaluer Train, Test, pour le classement binaire : gros Dataset « exemple et suppose que le jeu de données a déjà été chargée dans une table SQL Azure nommée censusdata.

## <a name="create-the-training-experiment"></a>Créer l’expérience de formation 
 
Lorsque vous ouvrez le « exemple 5 : évaluer Train, Test, pour le classement binaire : Dataset gros « exemple qu’il utilise l’exemple de dataset gros recensement revenus binaire Classification. Et l’expérience dans la zone de dessin est semblable à l’image suivante.

![Configuration initiale de l’expérience.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

Pour lire les données à partir de la table SQL Azure :

1.  Supprimer le module dataset.
2.  Dans la zone de recherche des composants, tapez importer.
3.  À partir de la liste des résultats, ajouter un module *d’Importer des données* à la zone de dessin expérience.
4.  Connectez sortie du module *Importer des données* l’entrée du module *EPURAGE données manquant* .
5.  Dans le volet Propriétés, sélectionnez la **Base de données SQL Azure** dans la liste déroulante **Source de données** .
6.  Dans le **nom du serveur de base de données**, le **nom de la base de données**, **nom d’utilisateur**et **mot de passe** champs, entrez les informations appropriées pour votre base de données.
7.  Dans le champ de requête de base de données, entrez la requête suivante.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  Dans la partie inférieure de l’expérience la zone de dessin, cliquez sur **exécuter**.

## <a name="create-the-predictive-experiment"></a>Créer l’expérience prédictive

Ensuite, vous configurez l’expérience prédictive à partir de laquelle vous allez déployer votre service web.

1.  En bas de l’expérience de dessin, cliquez sur **Définir le serviceWeb** et sélectionnez **Service Web prédictive (recommandé)**.
2.  Supprimer les *Entrées de Service Web* et les *modules de sortie du Service Web* à partir de l’expérience prédictive. 
3.  Dans la zone de recherche des composants, tapez exporter.
4.  À partir de la liste des résultats, ajouter un module *d’Exporter les données* à la zone de dessin expérience.
5.  Connectez sortie du *Modèle de Score* module l’entrée du module *Exporter les données* . 
6.  Dans le volet Propriétés, sélectionnez la **Base de données SQL Azure** dans la liste déroulante destination de données.
7.  Dans le **nom du serveur de base de données**, le **nom de la base de données**, **le nom de compte d’utilisateur Server**et champs de **mot de passe utilisateur serveur** , entrez les informations appropriées pour votre base de données.
8.  Dans le champ **liste de colonnes soient enregistrées séparées par des virgules** , tapez a obtenu étiquettes.
9.  Dans le **champ nom de la table de données**, tapez dbo. ScoredLabels. Si la table n’existe pas, il est créé lors de l’exécution de l’expérience ou le service web est appelé.
10. Dans le champ **liste de colonnes de table de données séparées par des virgules** , tapez ScoredLabels.

Lorsque vous écrivez une application qui appelle le service web final, vous souhaiterez peut-être spécifier une autre requête ou table de destination en cours d’exécution. Pour configurer ces entrées et sorties, vous pouvez utiliser la fonctionnalité de paramètres de Service Web pour définir la propriété de *source de données* du module *Importer des données* et la propriété de destination *Exporter les données* en mode données.  Pour plus d’informations sur les paramètres de Service Web, consultez l' [entrée de paramètres de Service Web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) sur Cortana Intelligence et Machine apprentissage Blog.

Pour configurer les paramètres de Service Web pour la requête d’importation et de la table de destination :

1.  Dans le volet de propriétés pour le module *Importer des données* , cliquez sur l’icône dans la partie supérieure droite de la **requête de base de données** champ et que vous sélectionnez **définir en tant que paramètre du service web**.
2.  Dans le volet de propriétés pour le module *Exporter les données* , cliquez sur l’icône dans la partie supérieure droite du champ **nom de la table de données** et que vous sélectionnez **définir en tant que paramètre du service web**.
3.  En bas du volet de propriétés *d’Exporter les données* module dans la section **Paramètres de Service Web** , cliquez sur la requête de base de données, puis renommez-la requête.
4.  Cliquez sur le **nom de la table de données** , puis renommez-la **Table**.

Lorsque vous avez terminé, votre expérience doit ressembler à l’image suivante.
 
![Aspect final d’expérience.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Vous pouvez maintenant déployer l’expérience comme un service web.

## <a name="deploy-the-web-service"></a>Déployer le service web 
Vous pouvez déployer un classique ou le nouveau service web.

### <a name="deploy-a-classic-web-service"></a>Déployer un Service Web classique

Pour déployer comme un Service Web classique et créer une application pour l’utiliser :

1.  En bas de l’expérience de dessin, cliquez sur Exécuter.
2.  Une fois l’exécution terminée, cliquez sur **Déployer un Service Web** et sélectionnez **Déployer du Service Web [classique]**.
3.  Dans le tableau de bord de service web, recherchez votre clé de l’API. Copier et l’enregistrer pour une utilisation ultérieure.
4.  Dans la table de **Point de terminaison par défaut** , cliquez sur le lien de **L’exécution du lot** pour ouvrir la Page de l’aide de l’API.
5.  Dans Visual Studio, créez une application console c#.
6.  Dans la Page aide de l’API, recherchez la section **Exemples de Code** en bas de la page.
7.  Copiez et collez l’exemple de code c# dans votre fichier Program.cs et supprimez toutes les références au stockage blob.
8.  Mettre à jour la valeur de la variable *apiKey* avec la touche API enregistrée précédemment.
9.  Recherchez la déclaration de demande et mettre à jour les valeurs des paramètres de Service Web qui sont passés aux modules de *Données d’importation* et *Exportation de données* . Dans ce cas, vous sera utiliser la requête d’origine, mais définir un nouveau nom de table.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Exécutez l’application. 

À la fin de l’exécution, une nouvelle table est ajoutée à la base de données contenant les résultats de la notation.

### <a name="deploy-a-new-web-service"></a>Déployer un nouveau Service Web

Pour déployer comme un nouveau Service Web et créer une application pour l’utiliser :

1.  Dans la partie inférieure de l’expérience la zone de dessin, cliquez sur **exécuter**.
2.  Une fois l’exécution terminée, cliquez sur **Déployer un Service Web** et sélectionnez **Déployer du Service Web [nouveau]**.
3.  Dans la page déployer expérience, entrez un nom pour votre service web et sélectionnez un plan de tarification, puis cliquez sur **déployer**.
4.  Dans la page de **démarrage rapide** , cliquez sur **consommer**.
5.  Dans la section **Exemples de Code** , cliquez sur **commandes**.
6.  Dans Visual Studio, créez une application console c#.
7.  Copiez et collez l’exemple de code c# dans votre fichier Program.cs.
8.  Mettre à jour la valeur de la variable *apiKey* avec la **Clé primaire** située dans la section **informations de consommation de base** .
9.  Recherchez la déclaration *scoreRequest* et mettre à jour les valeurs des paramètres de Service Web qui sont passés aux modules de *Données d’importation* et *Exportation de données* . Dans ce cas, vous sera utiliser la requête d’origine, mais définir un nouveau nom de table.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Exécutez l’application. 
 

