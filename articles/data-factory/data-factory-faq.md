<properties 
    pageTitle="Données Azure Factory - Forum aux Questions" 
    description="Forum aux questions sur Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---frequently-asked-questions"></a>Données Azure Factory - Forum aux Questions

## <a name="general-questions"></a>Questions d’ordre générales

### <a name="what-is-azure-data-factory"></a>Qu’est Azure Data Factory ?

Données par défaut est basé sur un nuage intégration des données de service cette **automatisation déplacement et transformation de données**. À l’instar d’usine qui s’exécute équipement pour prendre mat et leur transformation en produits finis, Data Factory orchestre services existants qui collectant des données brutes et transforment en informations prêts à l’emploi. 
 
Usine de données vous permet de créer pilotées par les données de flux de travail pour déplacer des données entre à la fois en local et banques de données cloud ainsi que les processus/transformer les données à l’aide de services de cluster tels que Azure HDInsight et Azure données Lake Analytique. Après avoir créé un pipeline qui exécute l’action dont vous avez besoin, vous pouvez planifier qu’elle s’exécute régulièrement (toutes les heures, quotidienne, hebdomadaire, etc.).   

Pour plus d’informations, voir [vue d’ensemble et Concepts clés](data-factory-introduction.md) . 

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Où puis-je trouver des informations de tarification destinée aux données Azure ?

Consultez la [page Détails de tarification usine données] [ adf-pricing-details] pour les détails de tarification pour le Factory de données Azure.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Comment puis-je commencer avec une usine de données Azure ?

- Pour une vue d’ensemble de Azure Data Factory, voir [présentation Azure Data Factory](data-factory-introduction.md).
- Pour un didacticiel sur la **copie/déplacement des données** à l’aide d’activité de copie, voir [Copier les données de stockage d’objets Blob Azure à base de données SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Pour un didacticiel sur la **transformation des données** à l’aide de HDInsight ruche activité. Afficher [les données de processus en exécutant le script Hive sur cluster Hadoop](data-factory-build-your-first-pipeline.md) 
  
### <a name="what-is-the-data-factorys-region-availability"></a>Quelle est la disponibilité dans la région d’usine données ?
Données par défaut est disponible dans **US ouest** et **Europe du Nord**. Les services informatiques et de stockage utilisés par les références de données peuvent se trouver dans d’autres régions. Voir [régions pris en charge](data-factory-introduction.md#supported-regions). 
 
### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quelles sont les limites du nombre de données références/pipelines/activités/jeux de données ?
 
Consultez la section **Azure données usine limites** de l’article [abonnement Azure et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Quelle est l’expérience de création/développeur avec service Factory de données Azure ?

Vous pouvez auteur/créer des références de données à l’aide d’une des opérations suivantes :

- **Portail Azure**  
   cartes The Data Factory dans le portail Azure fournissant interface utilisateur riche vous permettant de créer des services de données références ad lié. L' **Éditeur de Factory de données**, qui est également partie du portail, vous permet de créer aisément des services liés, des tableaux, des jeux de données et pipelines en spécifiant des définitions de JSON pour ces objets. Pour obtenir un exemple d’utilisation de l’éditeur/portail pour créer et déployer une usine de données, consultez [créer votre première pipeline de données à l’aide du portail Azure](data-factory-build-your-first-pipeline-using-editor.md) .

- **Visual Studio**  
   vous pouvez utiliser Visual Studio pour créer une usine de données Azure. Pour plus d’informations, consultez [créer votre première pipeline de données à l’aide de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) . 

- **PowerShell Azure**  
   pour un didacticiel/procédure pas à pas pour la création d’une usine de données à l’aide de PowerShell, consultez [créer et surveiller l’utilisation de PowerShell Azure Azure Data Factory](data-factory-build-your-first-pipeline-using-powershell.md) . Consultez [Données usine applet de commande référence] [ adf-powershell-reference] du contenu sur MSDN Library pour une documentation complète des applets de commande Data Factory.
   
- **Bibliothèque de classes .NET**  
   vous pouvez créer des références de données à l’aide de données usine .NET SDK. Procédure de création d’une usine de données à l’aide du Kit de développement .NET, consultez [créer, surveiller et gérer les références de données à l’aide du Kit de développement .NET](data-factory-create-data-factories-programmatically.md) . Voir [La référence de la bibliothèque de données de classe usine] [ msdn-class-library-reference] pour une documentation complète de données usine .NET SDK.

- **API REST**  
   vous pouvez également utiliser l’API REST exposés par le service Azure Data Factory pour créer et déployer des références de données. Voir [Référence de l’API REST données usine] [ msdn-rest-api-reference] pour une documentation complète de l’API REST de données par défaut.
 
- **Modèle de gestionnaire de ressources Azure** 
   voir [didacticiel : créer votre première usine données Azure à l’aide du Gestionnaire de ressources Azure modèle](data-factory-build-your-first-pipeline-using-arm.md) détails fo. 

### <a name="can-i-rename-a-data-factory"></a>Puis-je renommer une usine de données ?
Non. Tels que d’autres ressources Azure, le nom d’une usine de données Azure ne peuvent pas être modifié. 

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Puis-je déplacer une usine de données d’un abonnement Azure à l’autre ? 
Oui. Utilisez le bouton **déplacer** sur votre carte usine données comme indiqué dans le diagramme suivant. 

![Déplacer des données par défaut](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quelles sont les environnements cluster pris en charge par Data Factory ?
Le tableau suivant fournit la liste des environnements cluster pris en charge par Data Factory et activités pouvant s’exécuter sur les. 

| Environnement informatique | activités |
| ------------------- | -------- | 
| [À la demande HDInsight cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [votre propre cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [ruche](data-factory-hive-activity.md), [cochon](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop diffusion en continu](data-factory-hadoop-streaming-activity.md) | 
| [Lot Azure](data-factory-compute-linked-services.md#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |  
| [Apprentissage automatique Azure](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) | [Machine activités d’apprentissage : l’exécution du lot et des ressources de mise à jour](data-factory-azure-ml-batch-execution-activity.md) |
| [Données Azure Lake Analytique](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) | [Données Analytique Lake U-SQL](data-factory-usql-activity.md)
| [SQL azure](data-factory-compute-linked-services.md#azure-sql-linked-service), [Data Warehouse SQL Azure](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) | [Procédure stockée](data-factory-stored-proc-activity.md)

## <a name="activities---faq"></a>Activités - Forum aux questions
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quelles sont les différents types d’activités que vous pouvez utiliser dans un pipeline de données par défaut ? 

- [Activités de déplacement des données](data-factory-data-movement-activities.md) pour déplacer des données.
- [Activités de Transformation des données](data-factory-data-transformation-activities.md) à processus/transformation de données. 

### <a name="when-does-an-activity-run"></a>Lorsqu’une activité fonctionne ?
Le paramètre de configuration de **disponibilité** dans la table de données de sortie détermine quand l’activité est exécutée. Si des jeux de données d’entrée est indiqués, l’activité vérifie si toutes les dépendances de données d’entrée sont satisfaites (autrement dit, l’état **prêt** ) avant de démarrer en cours d’exécution. 

## <a name="copy-activity---faq"></a>Copier une activité - Forum aux questions
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Est-il mieux comporte un pipeline avec plusieurs activités ou des opportunités différentes pour chaque activité ? 
Pipelines censé de regrouper des activités associées. Si les jeux de données qui les relient n’est pas utilisées par toute autre activité en dehors du pipeline, vous pouvez conserver les activités dans un pipeline. Ainsi, vous n'aurait pas obligé périodes active chaîne pipeline afin qu’ils s’alignent entre eux. En outre, l’intégrité des données dans les tableaux internes au pipeline sont conservée mieux lorsque le pipeline de mise à jour. Pipeline mise à jour pour l’essentiel cesse de toutes les activités dans le pipeline, les supprime et crée les à nouveau. À partir du point de vue de création, il peut être plus facile de voir le flux de données dans les activités associées dans un même fichier JSON pour le pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quelles sont les banques de données pris en charge ?
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

### <a name="what-are-the-supported-file-formats"></a>Quels sont les formats de fichier pris en charge ? 
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Où est effectuée l’opération de copie ? 
Pour plus d’informations, reportez-vous à la section [déplacement de données globalement disponibles](data-factory-data-movement-activities.md#global) . En bref, lorsqu’une banque de données locale est impliquée, l’opération de copie est effectuée par la passerelle de gestion des données dans votre environnement local. Et, lorsque le déplacement de données est entre deux banques cloud, l’opération de copie est effectuée dans la région plus proche de l’emplacement de récepteur dans le même emplacement géographique. 


## <a name="hdinsight-activity---faq"></a>Activité HDInsight - Forum aux questions

### <a name="what-regions-are-supported-by-hdinsight"></a>Quelles régions sont pris en charge par HDInsight ?

Voir la section disponibilité géographique dans l’article suivant : ou les [Détails de tarification HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Quelle est la région est utilisée par un cluster de HDInsight à la demande ?

Le cluster de HDInsight à la demande est créé dans la même région où se trouve le stockage que vous avez spécifié pour être utilisés avec le cluster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Comment faire pour associer des comptes d’espace de stockage supplémentaire à votre cluster HDInsight ?

Si vous utilisez votre propre HDInsight Cluster (BYOC - importer votre propre Cluster), consultez les rubriques suivantes : 

- [À l’aide d’un Cluster HDInsight avec Metastores et les comptes de stockage de remplacement][hdinsight-alternate-storage]
- [Utiliser des comptes d’espace de stockage supplémentaire avec Hive HDInsight][hdinsight-alternate-storage-2]

Si vous utilisez un cluster d’à la demande est créé par le service de données par défaut, spécifiez des comptes de stockage supplémentaire pour la HDInsight liés service afin que le service de données usine peut s’inscrire à votre place. Dans la définition JSON pour le service liée à la demande, utilisez propriété **additionalLinkedServiceNames** pour spécifier les comptes d’autre emplacement de stockage comme le montre l’extrait de JSON suivant :
 
    {
        "name": "MyHDInsightOnDemandLinkedService",
        "properties":
        {
            "type": "HDInsightOnDemandLinkedService",
            "typeProperties": {
                "clusterSize": 1,
                "timeToLive": "00:01:00",
                "linkedServiceName": "LinkedService-SampleData",
                "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
            }
        }
    } 

Dans l’exemple ci-dessus, otherLinkedServiceName1 et otherLinkedServiceName2 représentent les services liées dont les définitions contiennent des informations d’identification que le cluster HDInsight a besoin pour accéder aux comptes de stockage de remplacement.

## <a name="slices---faq"></a>Secteurs - Forum aux questions

### <a name="why-are-my-input-slices-not-in-ready-state"></a>En quoi les mon secteurs d’entrée pas l’état prêt ?  
Une erreur courante n’est pas propriété **externes** du paramètre **true** sur le jeu de données d’entrée lorsque les données d’entrée en dehors de l’usine de données (ne pas généré par le factory de données). 

Dans l’exemple suivant, il vous suffit de valeur **externes** sur **dataset1**true.  

**DataFactory1** Pipeline 1 : dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2 : dataset3 -> activity3 -> dataset4

Si vous avez une autre usine de données avec un pipeline qui vous permet d’accéder dataset4 (obtenus par pipeline 2 dans usine données 1), marquer dataset4 comme un jeu de données externes, car le jeu de données est généré par une usine de données différents (DataFactory1, pas DataFactory2).  

**DataFactory2**    
Pipeline 1 : dataset4 -> activity4 -> dataset5

Si la propriété externe est correctement définie, vérifiez si les données d’entrée existent dans l’emplacement spécifié dans la définition du jeu de données d’entrée. 

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Comment faire pour exécuter un secteur ultérieurement à minuit lorsque le secteur est produit de tous les jours ?
Utilisez la propriété **décalage** pour spécifier le moment auquel vous souhaitez que le secteur à produire. Pour plus d’informations sur cette propriété, reportez-vous à la section [disponibilité du jeu de données](data-factory-create-datasets.md#Availability) . Voici un exemple rapide :

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

Secteurs quotidiennes démarrer à **6 heures** au lieu de la minuit par défaut.     

### <a name="how-can-i-rerun-a-slice"></a>Comment puis-je réexécuter une section ?
Vous pouvez réexécuter une section dans une des façons suivantes : 

- Utilisez le moniteur et gérer une application pour relancer une fenêtre d’activité ou secteur. Pour obtenir des instructions, voir [réexécuter sélectionné fenêtres activité](data-factory-monitor-manage-app.md#re-run-selected-activity-windows) .   
- Cliquez sur **exécuter** dans la barre de commandes sur la carte de **Données secteur** pour le secteur dans le portail Azure.
- Exécutez l’applet de commande **Set-AzureRmDataFactorySliceStatus** dont le statut en **attente** pour le secteur.   
    
        Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Voir [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] pour plus d’informations sur l’applet de commande. 

### <a name="how-long-did-it-take-to-process-a-slice"></a>Combien de temps faut-il pour traiter un secteur ?
Utilisez l’Explorateur fenêtre activité dans un seul moniteur et gérer une application pour connaître la durée nécessaire au traitement d’un secteur de données. Pour plus d’informations, voir [Activité fenêtre Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) . 

Vous pouvez également effectuer les opérations suivantes dans le portail Azure :  

1. Cliquez sur **jeux de données** vignette sur la carte de **Données par défaut** pour votre usine de données.
2. Cliquez sur le jeu de données spécifique sur la carte de **jeux de données** .
3. Sélectionnez le secteur qui vous intéresse dans la liste de **secteurs récentes** sur la carte **TABLE** .
4. Cliquez sur l’activité exécutée dans la liste **Activité s’exécute** sur la carte de **Données secteur** . 
5. Cliquez sur **Propriétés de la** vignette sur la carte de **Détails de la série activité** . 
6. Vous devriez voir le champ **durée** avec une valeur. Cette valeur est le temps nécessaire pour traiter le secteur.   

### <a name="how-to-stop-a-running-slice"></a>Comment arrêter un secteur en cours d’exécution ?
Si vous avez besoin d’arrêter le pipeline de l’exécution, vous pouvez utiliser l’applet de commande de [Mise en veille AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) . Pour l’instant, la suspension du pipeline n’empêche pas les exécutions secteur qui sont en cours. Une fois que les exécutions en cours terminé, aucune tranche supplémentaire n’est sélectionné.

Si vous voulez vraiment arrêter toutes les exécutions immédiatement, la seule façon de serait afin de supprimer le pipeline et créer à nouveau. Si vous choisissez de supprimer le pipeline, vous n’avez pas besoin de supprimer des tables et des services liées utilisés par le pipeline. 


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 
