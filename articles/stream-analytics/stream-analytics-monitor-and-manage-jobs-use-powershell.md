<properties 
    pageTitle="Surveiller et gérer les travaux de flux Analytique avec PowerShell | Microsoft Azure" 
    description="Découvrez comment utiliser PowerShell Azure et les applets de commande pour surveiller et gérer les travaux de flux Analytique." 
    keywords="Azure powershell, les applets de commande powershell azure, commande powershell, l’écriture de script powershell" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Surveiller et gérer les travaux de flux Analytique avec les applets de commande PowerShell Azure

Apprenez à surveiller et gérer les ressources de flux Analytique avec les applets de commande PowerShell Azure et l’écriture de script powershell qui exécutent les tâches de flux de données Analytique base.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Conditions requises pour exécuter les applets de commande PowerShell Azure pour flux Analytique

 - Créer un groupe de ressources Azure dans votre abonnement. Voici un exemple de script PowerShell Azure. Pour plus d’informations Azure PowerShell, voir [installer et configurer Azure PowerShell](../powershell-install-configure.md);  

PowerShell Azure 0.9.8 :  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0 :  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Tâches Analytique de flux de données créés par programme n’ont pas de surveillance activé par défaut.  Vous pouvez activer manuellement la surveillance dans le portail Azure en accédant à la page du Moniteur du travail, en cliquant sur le bouton Activer ou vous pouvez le faire par programme en suivant les étapes situés à [Analytique de flux de données Azure - travaux des Analytique flux moniteur par programme](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Applets de commande PowerShell Azure pour flux Analytique
Les applets de commande PowerShell Azure peut servir à surveiller et gérer les travaux Azure flux Analytique. Notez que PowerShell Azure a différentes versions. 
**Dans les exemples répertoriés la première commande concerne Azure PowerShell 0.9.8, la seconde commande concerne Azure PowerShell 1.0.** Les commandes Azure PowerShell 1.0 aura toujours « AzureRM » dans la commande.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Répertorie toutes les tâches de flux de données Analytique définis dans l’abonnement Azure ou le groupe de ressources spécifié, ou obtient des informations de travail sur une tâche spécifique au sein d’un groupe de ressources.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsJob

Cette commande PowerShell renvoie des informations sur toutes les tâches de flux de données Analytique dans l’abonnement Azure.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Cette commande PowerShell renvoie des informations sur toutes les tâches de flux de données Analytique dans le groupe de ressources StreamAnalytics-par défaut-centrale-US.

**Exemple 3**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Cette commande PowerShell renvoie des informations sur la tâche de flux de données Analytique StreamingJob dans le groupe de ressources StreamAnalytics-par défaut-centrale-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Répertorie toutes les entrées qui sont définies dans une tâche de flux Analytique spécifiée ou obtient des informations sur une entrée spécifique.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Cette commande PowerShell renvoie des informations sur toutes les entrées défini dans la tâche StreamingJob.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Cette commande PowerShell renvoie des informations sur l’entrée nommée EntryStream définie dans le travail StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Répertorie toutes les sorties qui sont définis dans une tâche de flux Analytique spécifiée ou obtient des informations sur une sortie spécifique.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Cette commande PowerShell renvoie des informations sur les sorties défini dans la tâche StreamingJob.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Cette commande PowerShell renvoie des informations sur la sortie nommée sortie définie dans le travail StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtient des informations sur le quota de diffusion en continu les unités dans une zone spécifiée.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Cette commande PowerShell renvoie des informations sur le quota et l’utilisation de la diffusion en continu unités dans la région États-Unis centre.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtient des informations sur une transformation spécifique définie dans une tâche de flux de données Analytique.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0 :  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Cette commande PowerShell renvoie des informations sur la transformation appelée StreamingJob dans la tâche StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Nouvelle AzureStreamAnalyticsInput | Nouvelle AzureRMStreamAnalyticsInput
Crée une nouvelle entrée au sein d’une tâche de flux de données Analytique, ou met à jour une entrée existante spécifiée.
  
Le nom de l’entrée peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux arguments sont spécifiés, le nom de la ligne de commande doit être identique à celle du fichier.

Si vous spécifiez une entrée qui existe déjà et que vous ne spécifiez pas le – paramètre Force, l’applet de commande demandera remplacer l’entrée existante ou non.

Si vous spécifiez – forcer paramètre et spécifiez une existante entrée nom, l’entrée sera remplacée sans confirmation.

Pour plus d’informations sur la structure de fichier JSON et le contenu, reportez-vous à l' [Entrée créer (Azure flux Analytique)] [ msdn-rest-api-create-stream-analytics-input] section de la [bibliothèque de référence API REST gestion des Analytique flux][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Cette commande PowerShell crée une nouvelle entrée à partir du fichier Input.json. Si une entrée existante avec le nom spécifié dans le fichier de définition d’entrée est déjà définie, l’applet de commande demandera remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Cette commande PowerShell crée une nouvelle entrée dans le projet nommé EntryStream. Si une entrée existante portant ce nom est déjà définie, l’applet de commande demandera remplacer ou non.

**Exemple 3**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Cette commande PowerShell remplace la définition de la source d’entrée existante appelée EntryStream avec la définition à partir du fichier.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Nouvelle AzureStreamAnalyticsJob | Nouvelle AzureRMStreamAnalyticsJob
Crée une nouvelle tâche de flux de données Analytique dans Microsoft Azure, ou met à jour la définition d’une tâche existante spécifiée.

Le nom de la tâche peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux arguments sont spécifiés, le nom de la ligne de commande doit être identique à celle du fichier.

Si vous spécifiez un nom de travail qui existe déjà et que vous ne spécifiez pas le – paramètre Force, l’applet de commande demandera de remplacement du travail existant ou non.

Si vous spécifiez – forcer paramètre et spécifiez le nom d’une tâche existante, la définition de travail sera remplacée sans confirmation.

Pour plus d’informations sur la structure de fichier JSON et le contenu, consultez [Créer un flux de travail Analytique] [ msdn-rest-api-create-stream-analytics-job] section de la [bibliothèque de référence API REST gestion des Analytique flux][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Cette commande PowerShell crée une nouvelle tâche à partir de la définition dans JobDefinition.json. Si un projet existant avec le nom spécifié dans le fichier de définition de travail est déjà défini, l’applet de commande demandera remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Cette commande PowerShell remplace la définition de tâche StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Nouvelle AzureStreamAnalyticsOutput | Nouvelle AzureRMStreamAnalyticsOutput
Crée une nouvelle sortie au sein d’une tâche de flux de données Analytique, ou met à jour une sortie existante.  

Le nom de la sortie peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux arguments sont spécifiés, le nom de la ligne de commande doit être identique à celle du fichier.

Si vous spécifiez une sortie qui existe déjà et que vous ne spécifiez pas le – paramètre Force, l’applet de commande demandera remplacer la sortie existante ou non.

Si vous spécifiez – forcer paramètre et spécifiez le nom de sortie existant, le résultat sera remplacé sans confirmation.

Pour plus d’informations sur la structure de fichier JSON et le contenu, reportez-vous à la [Sortie créer (Azure flux Analytique)] [ msdn-rest-api-create-stream-analytics-output] section de la [bibliothèque de référence API REST gestion des Analytique flux][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Cette commande PowerShell crée une nouvelle sortie appelée « sortie » dans la tâche StreamingJob. Si une sortie existante portant ce nom est déjà définie, l’applet de commande demandera remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Cette commande PowerShell remplace la définition de « sortie » dans la tâche StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Nouvelle AzureStreamAnalyticsTransformation | Nouvelle AzureRMStreamAnalyticsTransformation
Crée une nouvelle transformation au sein d’une tâche de flux de données Analytique, ou met à jour de la transformation existante.
  
Le nom de la transformation peut être spécifié dans le fichier .json ou sur la ligne de commande. Si les deux arguments sont spécifiés, le nom de la ligne de commande doit être identique à celle du fichier.

Si vous spécifiez une transformation qui existe déjà et que vous ne spécifiez pas le – paramètre Force, l’applet de commande demandera remplacer la transformation existante ou non.

Si vous spécifiez – forcer paramètre et spécifiez un nom de transformation existant, la transformation sera remplacée sans confirmation.

Pour plus d’informations sur la structure de fichier JSON et le contenu, reportez-vous à la [Créer une Transformation (Azure flux Analytique)] [ msdn-rest-api-create-stream-analytics-transformation] section de la [bibliothèque de référence API REST gestion des Analytique flux][stream.analytics.rest.api.reference].

**Exemple 1**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Cette commande PowerShell crée une nouvelle transformation appelée StreamingJobTransform dans la tâche StreamingJob. Si une transformation existante est déjà définie avec ce nom, l’applet de commande demandera remplacer ou non.

**Exemple 2**

PowerShell Azure 0.9.8 :  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0 :  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Cette commande PowerShell remplace la définition de StreamingJobTransform à la tâche StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Supprimer AzureStreamAnalyticsInput | Supprimer AzureRMStreamAnalyticsInput
En mode asynchrone supprime une entrée spécifique à partir d’une tâche de flux de données Analytique dans Microsoft Azure.  
Si vous spécifiez – paramètre Force, l’entrée sera supprimée sans confirmation.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0 :  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Cette commande PowerShell supprime le EventStream d’entrée de la tâche StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Supprimer AzureStreamAnalyticsJob | Supprimer AzureRMStreamAnalyticsJob
En mode asynchrone supprime une tâche de flux de données Analytique spécifique dans Microsoft Azure.  
Si vous spécifiez – paramètre Force, le travail sera supprimé sans confirmation.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0 :  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Cette commande PowerShell supprime le travail StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Supprimer AzureStreamAnalyticsOutput | Supprimer AzureRMStreamAnalyticsOutput
En mode asynchrone supprime une sortie spécifique à partir d’une tâche de flux de données Analytique dans Microsoft Azure.  
Si vous spécifiez – paramètre Force, le résultat sera supprimé sans confirmation.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0 :  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Cette commande PowerShell supprime le résultat de la sortie de la tâche StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Démarrage AzureStreamAnalyticsJob | Démarrage AzureRMStreamAnalyticsJob
En mode asynchrone est déployée et démarre une tâche de flux de données Analytique dans Microsoft Azure.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0 :  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Cette commande PowerShell démarre le travail StreamingJob avec une heure de début de sortie personnalisée la valeur 12 décembre 2012, 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop AzureStreamAnalyticsJob | Stop AzureRMStreamAnalyticsJob
En mode asynchrone arrête une tâche de flux Analytique de s’exécuter dans Microsoft Azure et annule l’allocation des ressources qui ont été qui ont été utilisé. La définition de travail et les métadonnées reste disponibles au sein de votre abonnement via le portail Azure et la gestion API, telles que le travail peut être modifié et redémarré. Vous ne devrez pas payer pour une tâche à l’état arrêté.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0 :  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Cette commande PowerShell arrête la tâche StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Tester la capacité de flux Analytique pour vous connecter à une entrée spécifiée.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0 :  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Cette commande PowerShell teste l’état de connexion de la EntryStream d’entrée dans StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Tester la capacité de flux Analytique pour vous connecter à une sortie spécifié.

**Exemple 1**

PowerShell Azure 0.9.8 :  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0 :  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Cette tests de commande PowerShell l’état de connexion de la sortie de sortie dans StreamingJob.  

## <a name="get-support"></a>Obtenir une assistance technique
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
