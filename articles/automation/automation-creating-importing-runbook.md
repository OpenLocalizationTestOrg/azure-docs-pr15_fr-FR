<properties
    pageTitle="Créez ou importez une procédure opérationnelle dans Azure Automation"
    description="Cet article décrit comment créer un nouveau runbook dans Azure Automation ou importer un à partir d’un fichier."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Créez ou importez une procédure opérationnelle dans Azure Automation

Vous pouvez ajouter une procédure opérationnelle à Azure Automation en [créant un nouveau](#creating-a-new-runbook) ou en important un runbook existant à partir d’un fichier ou à partir de la [Galerie de Runbook](automation-runbook-gallery.md). Cet article fournit des informations sur la création et l’importation des procédures opérationnelles à partir d’un fichier.  Vous pouvez obtenir des informations sur l’accès aux modules dans les [galeries Runbook et module Azure automatisation](automation-runbook-gallery.md)et procédures opérationnelles Communauté.

## <a name="creating-a-new-runbook"></a>Création d’un nouveau runbook

Vous pouvez créer un nouveau runbook dans Azure Automation en utilisant l’un des portails Azure ou Windows PowerShell. Une fois que le runbook a été créé, vous pouvez modifier à l’aide des informations dans les [Flux de travail apprentissage PowerShell](automation-powershell-workflow.md) et [Création dans Azure Automation graphique](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Pour créer un nouveau runbook Automation Azure grâce au portail Azure classique

Vous pouvez fonctionnent uniquement avec les [procédures opérationnelles PowerShell le flux de travail](automation-runbook-types.md#powershell-workflow-runbooks) dans le portail Azure.

1. Dans le portail Azure classique, cliquez sur, **Nouveau**, **Services d’application**, **automatisation**, **Runbook**, **Création rapide**.
2. Entrez les informations requises, puis cliquez sur **créer**. Le nom du runbook doit commencer par une lettre et peut avoir des lettres, des nombres, des traits de soulignement et des tirets.
3. Si vous souhaitez modifier la runbook maintenant, puis cliquez sur **Modifier Runbook**. Dans le cas contraire, cliquez sur **OK**.
4. Votre nouveau runbook s’affichent sous l’onglet **procédures opérationnelles** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Pour créer un nouveau runbook Automation Azure grâce au portail Azure

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur la vignette de **procédures opérationnelles** pour ouvrir la liste des procédures opérationnelles.
3. Cliquez sur le bouton **Ajouter une procédure opérationnelle** , puis **créer un nouveau runbook**.
2. Tapez un **nom** pour la runbook et sélectionnez son [Type](automation-runbook-types.md). Le nom du runbook doit commencer par une lettre et peut avoir des lettres, des nombres, des traits de soulignement et des tirets.
3. Cliquez sur **créer** pour créer la procédure opérationnelle et ouvrez l’éditeur.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Pour créer un nouveau runbook Azure Automation avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) pour créer un [flux de travail PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks)vide. Vous pouvez spécifier le paramètre de **nom** pour créer un runbook vide que vous pouvez modifier ultérieurement, ou vous pouvez spécifier le paramètre de **chemin d’accès** pour importer un fichier runbook. Le paramètre de **Type** doit également être inclus pour indiquer l’un des quatre types de runbook.

Les exemples de commandes suivants montrent comment créer un nouveau runbook vide.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importer un runbook à partir d’un fichier dans Azure Automation

Vous pouvez créer un nouveau runbook dans Azure Automation en important un script PowerShell ou PowerShell du flux de travail (extension .ps1) ou une runbook graphique exporté (.graphrunbook).  Vous devez spécifier le [type du runbook](automation-runbook-types.md) qui seront créés à partir de l’importation en prenant en compte les considérations suivantes.

- Un fichier .graphrunbook peut uniquement être importé dans un nouveau [graphique runbook](automation-runbook-types.md#graphical-runbooks)et procédures opérationnelles graphique peut être créé uniquement à partir d’un fichier .graphrunbook.
- Un fichier .ps1 contenant un flux de travail PowerShell peut uniquement être importé dans un [flux de travail PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Si le fichier contient plusieurs flux de travail PowerShell, l’importation échoue. Vous devez enregistrer chaque flux de travail vers son propre fichier et importer chacun séparément.
- Un fichier .ps1 qui ne contient-elle pas d’un flux de travail peut être importé dans un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) ou un [flux de travail PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Si elle est importée dans un flux de travail PowerShell runbook, puis elle sera convertie en un flux de travail et commentaires sont incluses dans la procédure opérationnelle spécifiant les modifications qui ont été apportées.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Pour importer un runbook à partir d’un fichier avec le portail Azure classique
Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.  Notez que vous pouvez uniquement importer un fichier .ps1 une runbook de flux de travail PowerShell à l’aide de ce portail.  Vous devez utiliser le portail Azure pour d’autres types.

1. Dans le portail de gestion de Azure, sélectionnez **Automation** , puis sélectionnez un compte Automation.
2. Cliquez sur **Importer**.
3. Cliquez sur **Rechercher le fichier** , puis recherchez le fichier de script à importer.
4. Si vous souhaitez modifier la runbook maintenant, puis cliquez sur **Modifier Runbook**. Dans le cas contraire, cliquez sur OK.
5. La nouvelle runbook s’affichent sous l’onglet **procédures opérationnelles** pour le compte d’automatisation.
6. Vous devez [publier le runbook](#publishing-a-runbook) avant que vous pouvez l’exécuter.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Pour importer un runbook à partir d’un fichier avec le portail Azure
Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.  

>[AZURE.NOTE] Notez que vous pouvez uniquement importer un fichier .ps1 une runbook de flux de travail PowerShell à l’aide du portail.

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur la vignette de **procédures opérationnelles** pour ouvrir la liste des procédures opérationnelles.
3. Cliquez sur le bouton **Ajouter une runbook** , puis sur **Importer**.
4. Cliquez sur **fichier Runbook** pour sélectionner le fichier à importer
2. Si le champ **nom** est activé, vous avez l’option Modifier.  Le nom du runbook doit commencer par une lettre et peut avoir des lettres, des nombres, des traits de soulignement et des tirets.
3. Le [type de runbook](automation-runbook-types.md) sera sélectionnée automatiquement, mais vous pouvez modifier le type après avoir pris les restrictions applicables en considération. 
3. La nouvelle runbook s’affichent dans la liste des procédures opérationnelles pour le compte d’automatisation.
4. Vous devez [publier le runbook](#publishing-a-runbook) avant que vous pouvez l’exécuter.

>[AZURE.NOTE] Après avoir importé un runbook graphique ou un graphique runbook de flux de travail de PowerShell, vous avez la possibilité pour convertir l’autre type si voulu. Vous ne pouvez pas convertir textuelle.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Pour importer un runbook à partir d’un fichier de script avec Windows PowerShell

Vous pouvez utiliser l’applet de commande [Importer AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) pour importer un fichier de script en tant que brouillon runbook de flux de travail PowerShell. Si la runbook existe déjà, l’importation échoue, sauf si vous utilisez la *-Force* paramètre. 

Les exemples de commandes suivants montrent comment importer un fichier de script dans une procédure opérationnelle.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Un runbook de publication

Lorsque vous créez ou importez un nouveau runbook, vous devez le publier avant de pouvoir l’exécuter.  Chaque runbook dans Automation comporte un brouillon et une version publiée. Seule la version publiée n’est disponible pour être exécuté, et seule la version brouillon peut être modifiée. La version publiée n’est pas affectée par toute modification apportée à la version brouillon. Lorsque la version brouillon doit être accessibles, puis vous publiez ce qui remplace la version publiée avec la version brouillon.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Pour publier un runbook à l’aide du portail Azure classique

1. Ouvrez la runbook dans le portail Azure classique.
1. Dans la partie supérieure de l’écran, cliquez sur **auteur**.
1. Dans la partie inférieure de l’écran, cliquez sur **Publier** , puis sur **Oui** pour le message de vérification.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Pour publier un runbook à l’aide du portail Azure

1. Ouvrez la runbook dans le portail Azure.
1. Cliquez sur le bouton **Modifier** .
1. Cliquez sur le bouton **Publier** , puis **Oui** pour le message de vérification.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Pour publier un runbook à l’aide de Windows PowerShell

Vous pouvez utiliser l’applet de commande [AzureRmAutomationRunbook de publier](https://msdn.microsoft.com/library/mt603705.aspx) pour publier un runbook avec Windows PowerShell. Les exemples de commandes suivants montrent comment publier un exemple de procédure opérationnelle.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Étapes suivantes
- Pour découvrir comment vous pouvez tirer parti du Runbook et PowerShell Module galerie, afficher les [galeries Runbook et module Azure Automation](automation-runbook-gallery.md)
- Pour plus d’informations sur la modification des procédures opérationnelles PowerShell et flux de travail PowerShell avec un éditeur de texte, consultez [procédures opérationnelles textuelle d’édition dans Azure Automation](automation-edit-textual-runbook.md)
- Pour en savoir plus sur la création de graphique runbook, reportez-vous à [la création de graphique dans Azure Automation](automation-graphical-authoring-intro.md)
