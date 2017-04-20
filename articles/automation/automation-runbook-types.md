<properties 
   pageTitle="Types de Runbook Automation Azure"
   description="Décrit les différents types de procédures opérationnelles que vous pouvez utiliser dans Azure Automation et considérations que vous devez prendre en compte pour déterminer le type à utiliser. "
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
   ms.author="bwren" />

# <a name="azure-automation-runbook-types"></a>Types de runbook Automation Azure

Automatisation Azure prend en charge quatre types de procédures opérationnelles brièvement décrits dans le tableau suivant.  Les sections suivantes fournissent des informations supplémentaires sur chaque type, y compris les considérations sur l’utilisation de chacun.


| Type |  Description |
|:---|:---|
| [Graphique](#graphical-runbooks) | En fonction de Windows PowerShell et éditeur graphique créé et modifié complètement dans Azure portail. | 
| [Flux de travail PowerShell graphique](#graphical-runbooks) | Basé sur le flux de travail de Windows PowerShell et créés et modifiés complètement dans l’éditeur graphique portail Azure. 
| [PowerShell](#powershell-runbooks) | Runbook de texte en fonction de script Windows PowerShell.
| [Flux de travail PowerShell](#powershell-workflow-runbooks) | Runbook de texte en fonction du flux de travail Windows PowerShell. |


## <a name="graphical-runbooks"></a>Procédures opérationnelles graphique

Graphique du flux de travail PowerShell et [graphique](automation-runbook-types.md#graphical-runbooks) procédures opérationnelles sont créés et modifiés dans l’éditeur de graphique dans le portail Azure.  Vous pouvez les exporter vers un fichier et les importer dans un autre compte automatisation, mais vous ne pouvez pas créer ou les modifier avec un autre outil.  Procédures opérationnelles graphiques génèrent un code PowerShell, mais vous ne pouvez pas directement afficher ou modifier le code. Procédures opérationnelles graphique ne peut pas être convertie à un des [formats de texte](automation-runbook-types.md), ni une runbook de texte peut être converti en format graphique. Procédures opérationnelles graphique peut être converti en graphique flux de travail PowerShell procédures opérationnelles pendant l’importation et vice versa.

### <a name="advantages"></a>Avantages

- Créer des procédures opérationnelles connaissant minimales [PowerShell](automation-powershell-workflow.md).
- Représenter visuellement des processus de gestion.
- Ajoutez d’autres procédures opérationnelles comme enfant procédures opérationnelles pour créer des flux de travail de niveau élevé.


### <a name="limitations"></a>Limitations

- Impossible de modifier runbook en dehors d’Azure portail.
- Peuvent nécessiter une activité de Code contenant le code de PowerShell pour effectuer une logique complexe.
- Impossible d’afficher ou modifier le code de PowerShell est créé par le flux de travail graphique directement. Notez que vous pouvez afficher le code que vous créez dans toutes les activités de Code.


## <a name="powershell-runbooks"></a>Procédures opérationnelles PowerShell

Procédures opérationnelles PowerShell sont basés sur Windows PowerShell.  Vous modifiez directement le code de la procédure opérationnelle à l’aide de l’éditeur de texte dans le portail Azure.  Vous pouvez également utiliser un éditeur de texte en mode hors connexion et [Importer le runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) dans Azure Automation.

### <a name="advantages"></a>Avantages

- Mise en œuvre toute la logique complexe avec code PowerShell sans la complexité supplémentaire de flux de travail PowerShell. 
- Runbook démarre plus vite que graphique ou du flux de travail PowerShell procédures opérationnelles dans la mesure où il ne doit pas être compilé avant d’exécuter.

### <a name="limitations"></a>Limitations

- Doit être familiarisé avec l’écriture de script PowerShell.
- Impossible d’utiliser [un traitement parallèle](automation-powershell-workflow.md#parallel-processing) pour effectuer plusieurs actions en parallèle.
- Impossible d’utiliser [les points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre runbook en cas d’erreur.
- Flux de travail PowerShell procédures opérationnelles et procédures opérationnelles graphique peuvent uniquement être inclus sous forme de procédures opérationnelles enfant à l’aide de l’applet de commande Démarrer AzureAutomationRunbook qui crée une nouvelle tâche.

### <a name="known-issues"></a>Problèmes connus
Voici des problèmes connus en cours avec procédures opérationnelles PowerShell.

- Procédures opérationnelles PowerShell ne peut pas Impossible d’extraire un non chiffré [bien variable](automation-variables.md) avec une valeur null.
- Procédures opérationnelles PowerShell ne peut pas récupérer d’une [variable bien](automation-variables.md) *~* dans le nom.
- Get-processus d’une boucle dans une PowerShell runbook peut se bloquer après environ 80 itérations. 
- Un runbook PowerShell peut échouer s’il tente d’écrire une grande quantité de données dans le flux de sortie à la fois.   Vous pouvez généralement contourner ce problème par conséquent uniquement les informations que nécessaires lorsque vous travaillez avec des objets de grande taille.  Par exemple, au lieu de sortie s’intitule *Get-Process*, vous pouvez exporter uniquement les champs requis avec *Get-Process | Sélectionnez ProcessName, processeur*.

## <a name="powershell-workflow-runbooks"></a>Procédures opérationnelles PowerShell le flux de travail

Flux de travail PowerShell procédures opérationnelles sont procédures opérationnelles texte basé sur [Le flux de travail de Windows PowerShell](automation-powershell-workflow.md).  Vous modifiez directement le code de la procédure opérationnelle à l’aide de l’éditeur de texte dans le portail Azure.  Vous pouvez également utiliser un éditeur de texte en mode hors connexion et [Importer le runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) dans Azure Automation.

### <a name="advantages"></a>Avantages

- Mettre en œuvre toute la logique complexe avec un code de flux de travail PowerShell.
- Utilisez [les points de contrôle](automation-powershell-workflow.md#checkpoints) pour reprendre runbook en cas d’erreur.
- [Traitement en parallèle](automation-powershell-workflow.md#parallel-processing) permet d’effectuer plusieurs actions en parallèle.
- Peut inclure d’autres procédures opérationnelles graphique et les procédures opérationnelles de flux de travail PowerShell comme enfant procédures opérationnelles pour créer des flux de travail de niveau élevé.


### <a name="limitations"></a>Limitations

- Auteur doit être familiarisé avec flux de travail PowerShell.
- Runbook doit gérer la complexité supplémentaire de flux de travail PowerShell comme [désérialisé objets](automation-powershell-workflow.md#code-changes).
- Runbook prend plus de temps de départ se PowerShell procédures opérationnelles dans la mesure où il doit être compilé avant d’exécuter.
- Procédures opérationnelles PowerShell peuvent uniquement être utilisés comme enfant procédures opérationnelles à l’aide de l’applet de commande Démarrer AzureAutomationRunbook qui crée une nouvelle tâche.


## <a name="considerations"></a>Considérations relatives à la

Vous prenez en compte les considérations supplémentaires suivantes pour déterminer le type à utiliser pour une procédure opérationnelle particulière.

- Vous ne peut pas convertir procédures opérationnelles graphique type textuel ou vice versa.
- Il existe des limitations au moyen de procédures opérationnelles de différents types comme un runbook enfant.  Pour plus d’informations, consultez [procédures opérationnelles enfant dans Azure Automation](automation-child-runbooks.md) .

  
## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de graphique runbook, reportez-vous à [la création de graphique dans Azure Automation](automation-graphical-authoring-intro.md)
- Pour mieux comprendre les différences entre PowerShell et PowerShell flux de travail pour les procédures opérationnelles, voir [Flux de travail apprentissage Windows PowerShell](automation-powershell-workflow.md)
- Pour plus d’informations sur la façon de créer ou importer un Runbook, voir [créer ou importer un Runbook](automation-creating-importing-runbook.md)



