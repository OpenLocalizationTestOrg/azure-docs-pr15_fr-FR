<properties 
   pageTitle="Plannings dans Azure automatisation | Microsoft Azure"
   description="Automatisation grilles sont utilisées pour planifier les procédures opérationnelles dans Azure Automation pour démarrer automatiquement. Décrit comment créer et gérer un échéancier dans de sorte que vous pouvez démarrer automatiquement un runbook à un moment donné ou selon un calendrier périodique."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="mgoedtel" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Planification d’une procédure opérationnelle dans Azure Automation

Pour planifier une procédure opérationnelle dans Azure Automation pour commencer à un moment donné, vous la liez à une ou plusieurs planifications. Un calendrier peut être configuré pour exécuter une seule fois ou sur un pose à nouveau toutes les heures ou quotidien un calendrier de procédures opérationnelles dans le portail classique Azure et des procédures opérationnelles dans le portail Azure, vous pouvez en outre planifier les hebdomadaire, mensuelle, certains jours de la semaine ou jours du mois ou un jour précis du mois.  Un runbook peut être lié à plusieurs calendriers, et une planification peut avoir plusieurs procédures opérationnelles lié.

>[AZURE.NOTE]  Planifications ne prennent pas en charge les configurations Azure Automation DSC.

## <a name="windows-powershell-cmdlets"></a>Applets de commande Windows PowerShell

Les applets de commande dans le tableau suivant sont utilisées pour créer et gérer des calendriers avec Windows PowerShell dans Azure Automation. Ils sont fournis dans le cadre du [module Azure PowerShell](../powershell-install-configure.md).

|Applets de commande|Description|
|:---|:---|
|**Applets de commande Gestionnaire de ressources Azure**||
|[Get-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603733.aspx)|Extrait une planification.|
|[Nouvelle AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx)|Crée un planning.|
|[Supprimer AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603691.aspx)|Supprime la planification.|
|[Jeu de AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx)|Définit les propriétés d’une planification existante.|
|[Get-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt619406.aspx)|Récupère planifiée procédures opérationnelles.|
|[Registre AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx)|Associe un runbook à un planning.|
|[AzureRmAutomationScheduledRunbook Unregister](https://msdn.microsoft.com/library/mt603844.aspx)|Dissociates un runbook d’un planning.|
|**Applets de commande de gestion des services Azure**||
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Extrait une planification.|
|[Nouvelle AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Crée un planning.|
|[Supprimer AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Supprime la planification.|
|[Jeu de AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Définit les propriétés d’une planification existante.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Récupère planifiée procédures opérationnelles.|
|[Registre AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Associe un runbook à un planning.|
|[AzureAutomationScheduledRunbook Unregister](http://msdn.microsoft.com/library/dn690273.aspx)|Dissociates un runbook d’un planning.|

## <a name="creating-a-schedule"></a>Création d’une planification

Vous pouvez créer un planning de procédures opérationnelles dans le portail Azure, dans le portail classique, ou avec Windows PowerShell. Vous avez également la possibilité de créer un planning lorsque vous liez un runbook à un calendrier à l’aide du portail classique ou Azure Azure.

>[AZURE.NOTE] Lorsque vous associez une planification à un runbook, Automation stocke les versions actuelles des modules dans votre compte et les lie à ce planning.  Cela signifie que si vous avez un module avec la version 1.0 dans votre compte lorsque vous avez créé une planification et puis mettez à jour le module vers la version 2.0, l’échéancier continuent à utiliser 1.0.  Pour pouvoir utiliser la version module mis à jour, vous devez créer un planning. 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Pour créer un planning dans le portail Azure

1. Dans le portail Azure, à partir de votre compte automatisation, cliquez sur la vignette de **biens** pour ouvrir la carte de **biens** .
2. Cliquez sur la vignette **planifications** pour ouvrir la carte de **calendriers** .
3. Cliquez sur **Ajouter un calendrier** en haut de la carte.
4. Dans la carte de **planning** , tapez un **nom** et éventuellement une **Description** pour le nouveau calendrier.
5. Choisissez si la planification s’exécutera une seule fois, ou selon un calendrier récurrents en sélectionnant **une seule fois** ou **périodicité**.  Si vous sélectionnez **une seule fois** spécifier une **heure de début** , puis cliquez sur **créer**.  Si vous sélectionnez **périodicité**, spécifiez une **heure de début** et la fréquence de la fréquence à laquelle la procédure opérationnelle à répéter - par **heure**, **jour**, **semaine**ou par **mois**.  Si vous sélectionnez **semaine** ou **mois** dans la liste déroulante, l' **option de périodicité** s’affichent dans la carte et lors de la sélection, **l’option de périodicité** carte s’affiche et vous pouvez sélectionner le jour de semaine si vous avez sélectionné **la semaine**.  Si vous avez sélectionné **mois**, vous pouvez choisir en **jours de la semaine** ou jours spécifiques du mois dans le calendrier et enfin, procédez comme vous souhaitez exécuter le dernier jour du mois ou non, puis cliquez sur **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Pour créer un planning dans le portail classique Azure

1. Dans le portail classique Azure, sélectionnez Automation, puis sélectionnez puis le nom d’un compte d’automatisation.
1. Sélectionnez l’onglet **ressources** .
1. Dans la partie inférieure de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter un échéancier**.
1. Tapez un **nom** et éventuellement une **Description** pour le nouvel agenda schedule.your s’exécute **Une seule fois**, **toutes les heures**, **quotidienne**, **hebdomadaire**ou **mensuel**.
1. Spécifiez une **Heure de début** et autres options en fonction du type de planification que vous avez sélectionné.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Pour créer un échéancier avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) pour créer un planning de Azure Automation de procédures opérationnelles classique ou applet de commande [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) pour les procédures opérationnelles dans le portail Azure. Vous devez spécifier l’heure de début de la planification et la fréquence d’exécution.

Les exemples de commandes suivante montre comment créer une planification pour les 15 et 30 de chaque mois à l’aide d’une applet de commande Azure le Gestionnaire de ressources.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Les exemples de commandes suivants montrent comment créer un planning qui s’exécute chaque jour à 3 h 30 commençant 20 janvier 2015 avec une applet de commande de gestion des services Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Liaison d’un calendrier à un runbook

Un runbook peut être lié à plusieurs calendriers, et une planification peut avoir plusieurs procédures opérationnelles lié. Si une procédure opérationnelle possède des paramètres, vous pouvez fournir des valeurs pour eux. Vous devez fournir des valeurs pour tous les paramètres obligatoires et pouvez fournir des valeurs pour tous les paramètres facultatifs.  Ces valeurs seront utilisées chaque fois que le runbook est démarré par ce calendrier.  Vous pouvez joindre la même procédure opérationnelle à une autre planification et spécifier des valeurs de paramètre différentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Pour lier un calendrier à un runbook grâce au portail Azure

1. Dans le portail Azure, à partir de votre compte automatisation, cliquez sur la vignette de **procédures opérationnelles** pour ouvrir la carte de **procédures opérationnelles** .
2. Cliquez sur le nom de la procédure opérationnelle planifier.
3. Si le runbook n’est pas lié à un calendrier, puis vous aurez l’option pour créer un planning ou un lien à un planning existant.  
4. Si la runbook comporte des paramètres, vous pouvez sélectionner l’option **Modifier les paramètres (par défaut : Azure) d’exécution** et la carte de **paramètres** est présentée dans laquelle vous pouvez entrer les informations en conséquence.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Pour lier un calendrier à un runbook grâce au portail classique Azure

1. Dans le portail classique Azure, sélectionnez **Automation** et puis cliquez sur le nom d’un compte d’automatisation.
2. Sélectionnez l’onglet **procédures opérationnelles** .
3. Cliquez sur le nom de la procédure opérationnelle planifier.
4. Cliquez sur l’onglet **prévisions** .
5. Si le runbook n’est pas lié à un calendrier, puis vous aurez l’option de **lien** vers un nouvel horaire soit **à un planning existant**.  Si la procédure opérationnelle est lié à un calendrier, cliquez sur le **lien** en bas de la fenêtre pour accéder à ces options.
6. Si la runbook comporte des paramètres, vous devra des valeurs.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Pour lier un calendrier à un runbook avec Windows PowerShell

Vous pouvez utiliser [Registre AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) pour lier un calendrier à une runbook classique ou le [Registre AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) cmdlet pour procédures opérationnelles dans le portail Azure.  Vous pouvez spécifier des valeurs pour les paramètres du runbook avec le paramètre de paramètres. Pour plus d’informations sur la spécification des valeurs de paramètre, voir [démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md) .

Les exemples de commandes suivants montrent comment lier un calendrier à un runbook à l’aide d’une applet de commande Gestionnaire de ressources Azure avec des paramètres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Les exemples de commandes suivants montrent comment lier un planning à l’aide d’une applet de commande de gestion des services Azure avec des paramètres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Désactiver une planification

Lorsque vous désactivez une planification, les procédures opérationnelles lié ne s’exécute plus sur ce calendrier. Vous pouvez manuellement désactiver une planification ou définir un délai d’expiration des planifications avec une fréquence lorsque vous les créez. Lorsque le délai d’expiration est atteint, la planification est désactivée.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Pour désactiver une planification à partir du portail Azure

1. Dans le portail Azure, à partir de votre compte automatisation, cliquez sur la vignette de **biens** pour ouvrir la carte de **biens** .
2. Cliquez sur la vignette **planifications** pour ouvrir la carte de **calendriers** .
2. Cliquez sur le nom d’une planification pour ouvrir la carte de détails.
3. Modifier **activé** sur **non**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Pour désactiver une planification à partir du portail classique Azure

Vous pouvez désactiver un échéancier dans le portail classique Azure à partir de la page Détails de la planification de la planification.

1. Dans le portail classique Azure, sélectionnez Automation et puis cliquez sur le nom d’un compte d’automatisation.
1. Sélectionnez l’onglet ressources.
1. Cliquez sur le nom d’une planification pour ouvrir sa page de détails.
2. Modifier **activé** sur **non**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Pour désactiver un échéancier avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) pour modifier les propriétés d’une planification existante pour une runbook classique ou l’applet de commande [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) pour procédures opérationnelles dans le portail Azure. Pour désactiver l’Échéancier, spécifiez **false** pour le paramètre **IsEnabled** .

Les exemples de commandes suivants montrent comment désactiver une planification pour un runbook à l’aide d’une applet de commande Azure le Gestionnaire de ressources.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Les exemples de commandes suivants montrent comment désactiver un planning à l’aide de l’applet de commande de gestion des services Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser les procédures opérationnelles dans Azure automatisation, voir [démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md) 