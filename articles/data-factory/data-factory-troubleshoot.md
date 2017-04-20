<properties 
    pageTitle="Résoudre les problèmes d’usine de données Azure" 
    description="Découvrez comment résoudre les problèmes liés à l’aide d’Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Résoudre les problèmes de données par défaut
Cet article fournit des conseils de dépannage pour les problèmes lors de l’utilisation Azure Data Factory. Cet article ne répertorie pas tous les éventuels problèmes lors de l’utilisation du service, mais il recouvre certains problèmes et des conseils de dépannage générales.   

## <a name="troubleshooting-tips"></a>Conseils de dépannage

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Erreur : L’abonnement n’est pas enregistré pour utiliser l’espace de noms 'Microsoft.DataFactory'
Si vous recevez cette erreur, le fournisseur de ressources Azure Data Factory n’a pas été enregistré sur votre ordinateur. Procédez comme suit : 

1. Lancez PowerShell Azure. 
2. Connectez-vous à votre compte Azure à l’aide de la commande suivante.
        Connexion AzureRmAccount 
3. Exécutez la commande suivante pour enregistrer le fournisseur Azure Data Factory.
        Registre AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problème : Non autorisé erreur lors de l’exécution d’une applet de commande Data Factory
Vous n’utilisez probablement pas le compte Azure vers la droite ou abonnement avec la session PowerShell Azure. Utiliser les applets de commande suivantes pour sélectionner le compte Azure vers la droite et abonnement à utiliser avec la session PowerShell Azure. 

1. Connexion-AzureRmAccount - utiliser l’ID d’utilisateur et mot de passe
2. Get-AzureRmSubscription - afficher tous les abonnements pour le compte. 
3. Sélectionnez AzureRmSubscription &lt;nom de l’abonnement&gt; -sélectionnez l’abonnement. Utiliser le même que celui que vous utilisez pour créer une usine de données dans le portail Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problème : Ne parviennent pas à lancer le programme d’installation Express du passerelle de gestion des données à partir de portail Azure
L’installation Express pour la passerelle de gestion des données requiert Internet Explorer ou dans un navigateur web compatible avec Microsoft ClickOnce. Si l’option installation rapide ne démarre pas, effectuez l’une des opérations suivantes : 

- Utilisez Internet Explorer ou dans un navigateur web compatible avec Microsoft ClickOnce.

    Si vous utilisez Chrome, accédez à la [Boutique Chrome](https://chrome.google.com/webstore/), rechercher avec le mot clé « ClickOnce », choisissez une des extensions ClickOnce et installez-le. 
    
    Procédez de même pour Firefox (complément installer). Cliquez sur Menu Ouvrir la barre d’outils (trois lignes horizontales dans le coin supérieur droit), cliquez sur modules complémentaires, rechercher avec le mot clé « ClickOnce », choisissez une des extensions ClickOnce et installez-le. 

- Cliquez sur le lien **D’installation manuelle** apparaît dans la même carte dans le portail. Cette approche vous permet de télécharger le fichier d’installation et l’exécuter manuellement. Une fois l’installation terminée, vous voyez la boîte de dialogue Configuration de passerelle de gestion des données. Copiez la **clé** à partir de l’écran portail et l’utiliser dans le Gestionnaire de configuration pour enregistrer manuellement la passerelle avec le service.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problème : Ne parviennent pas à se connecter à locale SQL Server 
Lancer le **Gestionnaire de Configuration de passerelle de gestion des données** sur l’ordinateur passerelle et utilisez l’onglet **résolution des problèmes** pour tester la connexion à SQL Server à partir de l’ordinateur passerelle. Voir [passerelle résoudre des problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle problèmes correspondants.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problème : Entrée secteurs sont en attente état par jamais

Les secteurs peuvent être dans l’état **en attente** pour diverses raisons. Une des raisons courantes est que la propriété **externes** n’est pas définie sur **true**. Un dataset produit à l’extérieur de la portée d’Azure Data Factory doit être marqué avec propriété **externe** . Cette propriété indique que les données sont externes et pas sauvegardés par les pipelines dans l’usine de données. Les secteurs de données sont marqués comme **prêt** une fois que les données sont disponibles dans le magasin correspondant. 

Voir l’exemple suivant pour l’utilisation de la propriété **externe** . Vous pouvez éventuellement spécifier **externalData*** lorsque vous définissez externes sur true.

Consultez l’article de [jeux de données](data-factory-create-datasets.md) pour plus d’informations sur cette propriété.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Pour résoudre l’erreur, ajoutez la propriété **externes** et la section facultative **externalData** à la définition de JSON de la table d’entrée et recréez le tableau. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problème : Hybride copie échoue
Voir [résoudre les problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir la procédure pour résoudre les problèmes avec la copie vers ou à partir d’une banque de données locale à l’aide de la passerelle de gestion des données. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problème : À la demande HDInsight site non disponible
Lorsque vous utilisez un service de type HDInsightOnDemand lié, vous devez spécifier un linkedServiceName qui pointe vers un stockage d’objets Blob Azure. Un service de données par défaut utilise cet espace de stockage pour stocker les journaux et les fichiers pris en charge pour votre cluster HDInsight à la demande.  Parfois mise en service d’un cluster de HDInsight à la demande échoue avec l’erreur suivante :

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Cette erreur indique généralement que l’emplacement du compte de stockage spécifié dans le linkedServiceName n’est pas au même endroit de centre de données où la mise en service HDInsight neuf. Exemple : Si votre usine de données est aux États-Unis Ouest et le stockage Azure est Extrême-Orient aux États-Unis, la mise en service à la demande échoue aux États-Unis Ouest.

Par ailleurs, il n’est une deuxième additionalLinkedServiceNames de propriété JSON où comptes d’espace de stockage supplémentaire peuvent être spécifiés dans HDInsight à la demande. Ces comptes d’espace de stockage supplémentaire liée doivent être au même emplacement que le cluster HDInsight, ou elle échoue avec la même erreur.

### <a name="problem-custom-net-activity-fails"></a>Problème : Personnalisé activité .NET échoue
Pour plus d’informations, voir [Déboguer un pipeline avec une activité personnalisée](data-factory-use-custom-activities.md#debug-the-pipeline) . 

## <a name="use-azure-portal-to-troubleshoot"></a>Portail Azure permet de résoudre les problèmes 

### <a name="using-portal-blades"></a>À l’aide de cartes portails
Voir [pipeline moniteur](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) pour obtenir la procédure. 

### <a name="using-monitor-and-manage-app"></a>Utilisation du moniteur et gérer l’application
Voir [moniteur et gérer les pipelines de factory de données à l’aide de surveiller et gérer une application](data-factory-monitor-manage-app.md) pour plus d’informations. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Utiliser PowerShell Azure pour résoudre les problèmes

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Utiliser PowerShell Azure pour résoudre une erreur  
Pour plus d’informations, voir [Moniteur Data Factory pipelines à l’aide de PowerShell Azure](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) . 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 