<properties 
    pageTitle="Modification des procédures opérationnelles textuelle dans Azure Automation"
    description="Cet article fournit des procédures différentes pour l’utilisation de PowerShell et flux de travail PowerShell procédures opérationnelles dans Azure Automation à l’aide de l’éditeur de texte."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Modification des procédures opérationnelles textuelle dans Azure Automation

L’éditeur de texte dans Azure Automation permet de modifier les [procédures opérationnelles PowerShell](automation-runbook-types.md#powershell-runbooks) et [procédures opérationnelles PowerShell le flux de travail](automation-runbook-types.md#powershell-workflow-runbooks). Cela a les fonctionnalités standard d’autres éditeurs de code par exemple intellisense et le codage de couleur avec des fonctionnalités spéciales supplémentaires pour vous aider à accéder aux ressources communes aux procédures opérationnelles.  Cet article fournit la procédure détaillée pour effectuer des fonctions différentes avec cet éditeur.

L’éditeur de texte inclut une fonctionnalité pour insérer du code pour les activités, les biens et les procédures opérationnelles enfant dans une procédure opérationnelle. Au lieu de taper le code vous-même, vous pouvez sélectionner dans la liste des ressources disponibles et ont le code inséré dans la procédure opérationnelle.

Chaque runbook dans Azure Automation inclut deux versions, brouillon et publié. Modifier la version brouillon de la runbook et publiez-le afin de pouvoir être exécuté. La version publiée ne peuvent pas être modifiée. Pour plus d’informations, voir [publication d’une procédure opérationnelle](automation-creating-importing-runbook.md#publishing-a-runbook) .

Pour utiliser les [Procédures opérationnelles graphiques](automation-runbook-types.md#graphical-runbooks), voir [la création de graphique dans Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Pour modifier une procédure opérationnelle grâce au portail Azure

Utilisez la procédure suivante pour ouvrir une procédure opérationnelle pour être modifié dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez votre compte automation.
2. Cliquez sur la vignette de **procédures opérationnelles** pour ouvrir la liste des procédures opérationnelles.
3. Cliquez sur le nom de la procédure opérationnelle que vous voulez modifier, puis cliquez sur le bouton **Modifier** .
6. Effectuez les modifications nécessaires.
7. Une fois vos modifications terminées, cliquez sur **Enregistrer** .
8. Si vous souhaitez que la dernière version brouillon de la procédure opérationnelle à publier, cliquez sur **Publier** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Pour insérer une applet de commande dans une procédure opérationnelle

2. Dans la zone de travail de l’éditeur de texte, placez le curseur à l’endroit où vous souhaitez placer l’applet de commande.
3. Développez le nœud **applets de commande** dans le contrôle de la bibliothèque. 
3. Développez le module qui contient l’applet de commande que vous voulez utiliser.
4. Cliquez avec le bouton droit sur l’applet de commande pour insérer, puis sélectionnez **Ajouter à la zone de dessin**.  Si l’applet de commande est défini pour plusieurs paramètres, le jeu par défaut est ajouté.  Vous pouvez également développer l’applet de commande pour sélectionner un jeu de paramètres différents.
4. Le code de l’applet de commande est inséré avec sa liste entière de paramètres.
5. Fournir une valeur appropriée à la place le type de données entourée d’accolades <> pour tous les paramètres requis.  Supprimer tous les paramètres que vous n’avez pas besoin.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Pour insérer du code pour une procédure opérationnelle enfant dans une procédure opérationnelle

2. Dans la zone de travail de l’éditeur de texte, placez le curseur à l’endroit où vous souhaitez placer le code de l' [enfant runbook](automation-child-runbooks.md).
3. Développez le nœud de **procédures opérationnelles** dans le contrôle de la bibliothèque. 
3. Cliquez avec le bouton droit sur la procédure opérationnelle pour insérer, puis sélectionnez **Ajouter à la zone de dessin**.
4. Le code de la procédure opérationnelle enfant est inséré avec des espaces réservés pour tous les paramètres runbook.
5. Remplacer les espaces réservés par les valeurs appropriées pour chaque paramètre.

### <a name="to-insert-an-asset-into-a-runbook"></a>Pour insérer un élément dans un runbook

2. Dans la zone de travail de l’éditeur de texte, placez le curseur à l’endroit où vous souhaitez placer le code de la procédure opérationnelle enfant.
3. Développez le nœud de **biens** dans le contrôle de la bibliothèque. 
4. Développez le nœud correspondant au type de biens souhaité.
3. Cliquez avec le bouton droit sur la ressource pour insérer, puis sélectionnez **Ajouter à la zone de dessin**.  Pour les [biens variables](automation-variables.md), sélectionnez **Ajouter « obtenir Variable » à la zone de dessin** ou **Ajouter « définir la Variable » à la zone de dessin** selon que vous voulez obtenir ou définir la variable.
4. Le code de l’actif est inséré dans la procédure opérationnelle.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Pour modifier une procédure opérationnelle grâce au portail Azure

Utilisez la procédure suivante pour ouvrir une procédure opérationnelle pour être modifié dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez **Automation** et puis cliquez sur le nom d’un compte d’automatisation.
2. Sélectionnez l’onglet **procédures opérationnelles** .
3. Cliquez sur le nom de la procédure opérationnelle que vous voulez modifier, puis sélectionnez l’onglet **auteur** .
5. Cliquez sur le bouton **Modifier** en bas de l’écran.
6. Effectuez les modifications nécessaires.
7. Une fois vos modifications terminées, cliquez sur **Enregistrer** .
8. Si vous souhaitez que la dernière version brouillon de la procédure opérationnelle à publier, cliquez sur **Publier** .

### <a name="to-insert-an-activity-into-a-runbook"></a>Pour insérer une activité dans un Runbook

1. Dans la zone de travail de l’éditeur de texte, placez le curseur à l’endroit où vous souhaitez placer l’activité.
1. Dans la partie inférieure de l’écran, cliquez sur **Insérer** , puis **activité**.
1. Dans la colonne **Integration Module** , sélectionnez le module qui contient l’activité.
1. Dans le volet **d’activité** , sélectionnez une activité.
1. Dans la colonne **Description** , notez la description de l’activité. Si vous le souhaitez, vous pouvez cliquer sur Afficher d’aide pour lancer l’aide de l’activité dans le navigateur.
1. Cliquez sur la flèche droite.  Si l’activité possède des paramètres, ils s’affichent pour vos informations.
1. Cliquez sur le bouton de vérification.  Exécution de l’activité de code est inséré dans la procédure opérationnelle.
1. Si l’activité nécessite des paramètres, fournissent une valeur appropriée à la place le type de données entourée d’accolades <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Pour insérer du code pour une procédure opérationnelle enfant dans une procédure opérationnelle

1. Dans la zone de travail de l’éditeur de texte, placez le curseur à l’endroit où vous souhaitez placer l' [enfant runbook](automation-child-runbooks.md).
2. Dans la partie inférieure de l’écran, cliquez sur **Insérer** , puis sur **Runbook**.
3. Sélectionnez la procédure opérationnelle à insérer dans la colonne centrale et cliquez sur la flèche droite.
4. Si la runbook comporte des paramètres, ils s’affichent pour vos informations.
5. Cliquez sur le bouton de vérification.  Code pour exécuter la procédure opérationnelle sélectionnée est inséré dans la procédure opérationnelle en cours.
7. Si la procédure opérationnelle nécessite des paramètres, fournissent une valeur appropriée à la place le type de données entourée d’accolades <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Pour insérer un élément dans un runbook

1. Dans la zone de travail de l’éditeur de texte, placez le curseur à l’endroit où vous souhaitez placer l’activité pour récupérer l’actif.
1. Dans la partie inférieure de l’écran, cliquez sur **Insérer** , puis sur **paramètres**.
1. Dans la colonne **Action du paramètre** , sélectionnez l’action souhaitée.
1. Sélectionnez les éléments disponibles dans la colonne centrale.
1. Cliquez sur le bouton de vérification.  Code pour obtenir ou définir l’actif est insérée dans la procédure opérationnelle.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Pour modifier un runbook Azure Automation à l’aide de Windows PowerShell

Pour modifier un runbook avec Windows PowerShell, vous utilisez l’éditeur de votre choix et enregistrez dans un fichier .ps1. Vous pouvez utiliser l’applet de commande [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) d’extraire le contenu du runbook puis applet de commande [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) pour remplacer le runbook brouillon existant avec l’option modifiée.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Pour extraire le contenu d’un Runbook avec Windows PowerShell

Les exemples de commandes suivants montrent comment récupérer le script pour un runbook et enregistrez-le dans un fichier de script. Dans cet exemple, la version brouillon est récupérée. Il est également possible de récupérer la version publiée de du runbook bien que cette version ne peut pas être modifiée.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Pour modifier le contenu d’un Runbook avec Windows PowerShell

Les exemples de commandes suivants montrent comment remplacer le contenu existant d’un runbook avec le contenu d’un fichier de script. Notez qu’il s’agit de la même procédure exemple obtenus [pour importer un runbook à partir d’un fichier de script avec Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Articles connexes

- [Créez ou importez une procédure opérationnelle dans Azure Automation](automation-creating-importing-runbook.md)
- [Flux de travail PowerShell d’apprentissage](automation-powershell-workflow.md)
- [Création graphique dans Azure Automation](automation-graphical-authoring-intro.md)
- [Certificats](automation-certificates.md)
- [Connexions](automation-connections.md)
- [Informations d’identification](automation-credentials.md)
- [Calendriers](automation-schedules.md)
- [Variables](automation-variables.md)