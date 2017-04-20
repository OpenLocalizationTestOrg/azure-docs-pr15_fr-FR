<properties 
   pageTitle="Procédures opérationnelles enfant dans Azure automatisation | Microsoft Azure"
   description="Décrit les différentes méthodes de démarrage d’un runbook dans Azure Automation à partir d’un autre runbook et partager des informations entre elles."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Procédures opérationnelles enfant dans Azure Automation

Il est recommandé de représenter dans Azure Automation pour écrire des procédures opérationnelles réutilisable, modulaire avec une fonction discrète qui peut être utilisée par d’autres procédures opérationnelles. Un runbook parent appellent souvent un ou plusieurs des procédures d’enfant opérationnelles pour effectuer la fonctionnalité requise. Il existe deux manières d’appeler une procédure opérationnelle enfant, et chacune possède des différences distincts que vous devez comprendre afin que vous pouvez déterminer qui vous convient le mieux pour vos différents scénarios.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Appeler un runbook enfant à l’aide de l’exécution inline

Pour appeler un inline runbook à partir d’un autre runbook, vous utilisez le nom de la procédure opérationnelle et fournissez les valeurs de ses paramètres exactement à ce que vous utiliseriez une activité ou une applet de commande.  Toutes les procédures opérationnelles dans le même compte Automation sont disponibles pour tous les autres devant être utilisé de cette manière. La procédure opérationnelle parent attend la runbook enfant terminer avant de passer à la ligne suivante, puis tout le résultat directement dans le parent.

Lorsque vous appelez un inline runbook, il s’exécute en même temps que la runbook parent. Il n’y a aucune indication dans l’historique des travaux de la procédure opérationnelle enfant qui il a été exécuté. Les exceptions et les flux de sortie à partir de la procédure opérationnelle enfant sera associés avec le parent. Cette formule produit comme résultat un nombre moins élevé de tâches et simplifie la pour suivre et résoudre les problèmes dans la mesure où les exceptions renvoyées par la runbook enfant et d’un de sa sortie de flux de données sont associées à la tâche parent.

Lorsqu’une procédure opérationnelle est publié, les procédures opérationnelles enfant qu’elle appelle doit déjà être publié. C’est parce que Azure Automation crée une association avec les procédures opérationnelles enfant lors de la compilation d’un runbook. Si elles ne sont pas, la runbook parent s’affichent à publier correctement, mais générera une exception lors de son démarrage. Dans ce cas, vous pouvez publier le runbook parent pour référencer correctement les procédures opérationnelles enfant. Vous n’avez pas besoin republier la runbook parent si un des procédures opérationnelles enfant sont modifié, car l’association aura déjà été créée.

Les paramètres d’une procédure opérationnelle enfant appelé inline peuvent être n’importe quel type de données, y compris les objets complexes, et il existe aucune [sérialisation JSON](automation-starting-a-runbook.md#runbook-parameters) comme lorsque vous démarrez le runbook à l’aide du portail de gestion Azure ou avec l’applet de commande Démarrer AzureRmAutomationRunbook.


### <a name="runbook-types"></a>Types de Runbook

Quels types peuvent appeler eux :

- Un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) et les [procédures opérationnelles graphique](automation-runbook-types.md#graphical-runbooks) peuvent appeler chaque en ligne (les deux sont en fonction de PowerShell).
- Un procédures opérationnelles [runbook de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) et flux de travail PowerShell graphique peuvent appeler chaque en ligne (sont tous deux des flux de travail PowerShell basé)
- Les types de PowerShell et les types de flux de travail PowerShell ne peut pas appeler inline uns des autres et doivent utiliser démarrer AzureRmAutomationRunbook.
    
Quand publie importance à ordre :

- La commande Publier des procédures opérationnelles est important uniquement pour les procédures opérationnelles PowerShell le flux de travail et flux de travail PowerShell graphique.


Lorsque vous appelez un graphique ou du flux de travail PowerShell enfant procédure opérationnelle à l’aide de l’exécution inline, vous utilisez simplement le nom de la procédure opérationnelle.  Lorsque vous appelez une procédure opérationnelle enfant PowerShell, vous devez précéder son nom avec *.\\ * Pour spécifier que le script se trouve dans le répertoire local. 

### <a name="example"></a>Exemple

L’exemple suivant appelle une procédure opérationnelle enfant test qui accepte trois paramètres, un objet complexe, un nombre entier et une valeur booléenne. Le résultat de la procédure opérationnelle enfant est affecté à une variable.  Dans ce cas, la procédure opérationnelle enfant est un runbook de flux de travail PowerShell

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Voici le même exemple à l’aide d’un runbook PowerShell comme enfant.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Démarrage d’un runbook enfant à l’aide de la cmdlet

Vous pouvez utiliser l’applet de commande [Démarrer AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) pour démarrer une runbook comme décrit dans [pour démarrer une runbook avec Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Il existe deux modes d’utilisation pour cette cmdlet.  Dans un mode, l’applet de commande renvoie l’id du travail dès que le travail enfant est créé pour la runbook enfant.  Dans l’autre mode, vous activez en spécifiant la **-attendre** paramètre, l’applet de commande attend de l’enfant se termine la tâche et renvoie le résultat de la runbook enfant.

La tâche à partir d’un runbook enfant une applet de commande en main est exécutée dans une tâche distincte à partir de la runbook parent. Cela se traduit dans les tâches plus que d’appeler l’inline runbook et les rend plus difficile à suivre. Le parent peut commencer plusieurs procédures opérationnelles enfant asynchrone sans en attente pour chacune d’elles terminer. Pour ce même type de l’exécution en parallèle appelant l’inline procédures opérationnelles enfant, du runbook parent devront utiliser le [mot clé en parallèle](automation-powershell-workflow.md#parallel-processing).

Paramètres pour une runbook enfant main une applet de commande sont fournis comme table de hachage comme décrit dans les [Paramètres de procédure opérationnelle](automation-starting-a-runbook.md#runbook-parameters). Uniquement les types de données simples peuvent être utilisés. Si la runbook a un paramètre à un type de données complexes, puis elle doit être appelée en ligne.

### <a name="example"></a>Exemple

L’exemple suivant démarre un runbook enfant avec paramètres, puis attend qu’elle se termine à l’aide de la AzureRmAutomationRunbook début-attendre paramètre. Une fois terminé, le résultat est collecté à partir de la procédure opérationnelle enfant.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparaison des méthodes permettant d’appeler une procédure opérationnelle enfant

Le tableau suivant récapitule les différences entre les deux méthodes pour appeler une procédure opérationnelle à partir d’un autre runbook.

| | Inline| Applet de commande|
|:---|:---|:---|
|Tâche|Procédures opérationnelles enfant s’exécutent dans la même tâche en tant que le parent.|Une tâche distincte est créée pour le runbook enfant.|
|Erreur d’exécution|Runbook parent attend la runbook enfant se termine avant de poursuivre.|Runbook parent continue immédiatement après que l’enfant runbook est démarré *ou* runbook parent attend pour le travail enfant terminer.|
|Sortie|Runbook parent accessible directement sortie de runbook enfant.|Runbook parent doit récupérer sortie d’enfant runbook travail *ou* parent runbook pouvez directement obtenir le résultat d’enfant runbook.|
|Paramètres|Valeurs pour les paramètres de runbook enfant sont spécifiés séparément et peuvent utiliser n’importe quel type de données.|Valeurs pour les paramètres de runbook enfant doivent être combinées dans une table de hachage unique et ne peuvent comporter simples, de tableau et qui sérialisation JSON exploiter les types de données de l’objet.|
|Compte d’Automation|Runbook parent ne peut utiliser runbook enfant dans le même compte automation.|Parent runbook pouvez utiliser runbook enfant à partir de n’importe quel compte automation à partir de la même abonnement Azure et même un autre abonnement si vous avez une connexion à ce dernier.|
|Publication|Enfant runbook doit être publiée avant runbook parent est publié.|Runbook enfant doit être publiée à tout moment avant le démarrage de runbook parent.|

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md)
- [Sortie Runbook et les messages dans Azure Automation](automation-runbook-output-and-messages.md)
