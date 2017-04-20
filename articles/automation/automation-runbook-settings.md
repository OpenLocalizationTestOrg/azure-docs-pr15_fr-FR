<properties 
   pageTitle="Paramètres Runbook"
   description="Décrit les paramètres de configuration pour une procédure opérationnelle dans Azure Automation et comment modifier à l’aide du portail de gestion Azure et de Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Paramètres Runbook

Chaque runbook dans Azure Automation comporte plusieurs paramètres qui vous aider à identifier et changer son comportement de journalisation. Chacun de ces paramètres est décrite ci-dessous suivie des procédures sur la façon de les modifier.

## <a name="settings"></a>Paramètres

### <a name="name-and-description"></a>Nom et une description

Vous ne pouvez pas modifier le nom d’une procédure opérationnelle après que qu’il a été créé. La Description est facultative et peut contenir jusqu'à 512 caractères.

### <a name="tags"></a>Balises

Balises vous permettent de vous permettent d’assigner des mots distincts et phrases pour aider à identifier un runbook. Par exemple, lorsque vous envoyez une procédure opérationnelle dans la [Galerie de Runbook](https://msdn.microsoft.com/library/dn781422.aspx), vous spécifiez certaines balises pour identifier les catégories du runbook doit être répertorié dans. Vous pouvez spécifier plusieurs indicateurs pour une runbook en les séparant par des virgules.

### <a name="logging"></a>Journalisation

Par défaut, les enregistrements de commentaires et l’avancement ne sont pas écrits dans l’historique du travail. Vous pouvez modifier les paramètres pour une runbook particulier afin de vous connecter ces enregistrements. Pour plus d’informations sur ces enregistrements, voir [Runbook sortie et Messages](https://msdn.microsoft.com/library/dn879148.aspx).

## <a name="changing-runbook-settings"></a>Modification des paramètres de runbook

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Modification des paramètres de runbook avec le portail de gestion Azure

Vous pouvez modifier les paramètres pour une procédure opérationnelle dans le portail de gestion Azure à partir de la page **configurer** pour la procédure opérationnelle.

1. Dans le portail de gestion Azure, sélectionnez **Automation** et puis cliquez sur le nom d’un compte d’automatisation.
1. Sélectionnez l’onglet **procédures opérationnelles** .
1. Cliquez sur le nom d’une procédure opérationnelle.
1. Sélectionnez l’onglet **configurer** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Modification des paramètres de runbook avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) pour modifier les paramètres pour une procédure opérationnelle. Si vous souhaitez spécifier plusieurs indicateurs, vous pouvez fournir un tableau ou une chaîne avec des valeurs par des virgules pour le paramètre de balises. Vous pouvez obtenir les balises actives avec [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Les exemples de commandes suivants montrent comment définir les propriétés d’un runbook. Cet exemple ajoute trois balises pour les balises existantes et indique que les enregistrements détaillées doivent être enregistrés.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Articles connexes
- [Sortie Runbook et Messages](../automation-runbook-output-and-messages) 
- [Créez ou importez un Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 