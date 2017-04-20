<properties 
   pageTitle="Flux de travail apprentissage PowerShell"
   description="Cet article est destiné à une leçon rapide auteurs familiarisés avec PowerShell de comprendre les différences entre PowerShell et flux de travail PowerShell spécifiques."
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

# <a name="learning-windows-powershell-workflow"></a>Flux de travail apprentissage Windows PowerShell

Procédures opérationnelles dans Azure Automation sont implémentées en tant que flux de travail Windows PowerShell.  Un flux de travail Windows PowerShell est semblable à un script Windows PowerShell, mais elle a quelques différences significatives qui peuvent être difficile à un nouvel utilisateur.  Cet article est destiné aux utilisateurs connaissant déjà PowerShell et décrit brièvement les concepts de base que vous avez besoin si vous convertissez un script PowerShell à un flux de travail PowerShell à utiliser dans une procédure opérationnelle.  

Un flux de travail est une séquence d’étapes programmées, connectés qui effectuer des tâches longues ou requièrent coordonner plusieurs étapes sur plusieurs appareils ou des nœuds gérés. Les avantages d’un flux de travail dans un script normal incluent la possibilité simultanément effectue une action sur plusieurs appareils et la possibilité de récupérer automatiquement des défaillances. Un flux de travail Windows PowerShell est un script Windows PowerShell qui s’appuie sur Windows Workflow Foundation. Tandis que le flux de travail est écrite de syntaxe Windows PowerShell et lancé par Windows PowerShell, il est traité par Windows Workflow Foundation.

Pour plus d’informations sur les rubriques de cet article, voir [Prise en main du flux de travail Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Types de runbook

Il existe trois types de procédure opérationnelle dans Azure automatisation, *Flux de travail PowerShell*, *PowerShell* et *graphiques*.  Vous définissez le type de runbook lorsque vous créez la procédure opérationnelle, et vous ne pouvez pas convertir un runbook à l’autre type après sa création.

Procédures opérationnelles PowerShell du flux de travail et les procédures opérationnelles PowerShell sont pour les utilisateurs qui préfèrent travailler directement avec le code PowerShell soit à l’aide de l’éditeur de texte dans Azure Automation ou un éditeur en mode hors connexion tels que PowerShell ISE. Vous devez comprendre les informations contenues dans cet article si vous créez un flux de travail PowerShell runbook. 

Procédures opérationnelles graphiques permettent de créer un runbook les activités et les applets de commande de même, mais une interface graphique masquer la complexité du flux de travail PowerShell sous-jacente.  Concepts de cet article tels que les points de contrôle et l’exécution en parallèle restent appliquent aux graphiques procédures opérationnelles, mais vous n’aurez pas à vous soucier de la syntaxe de la détaillées. 

## <a name="basic-structure-of-a-workflow"></a>Structure de base d’un flux de travail

La première étape à la conversion d’un script PowerShell à un flux de travail PowerShell est l’encadrement avec le mot-clé de **flux de travail** .  Un flux de travail commence par le mot-clé de **flux de travail** suivi par le corps du script placé entre accolades. Le nom du flux de travail suit le mot-clé de **flux de travail** comme indiqué dans la syntaxe suivante. 

    Workflow Test-Workflow
    {
       <Commands>
    }

Le nom du flux de travail doit correspondre au nom de la procédure d’automatisation opérationnelle. Si la procédure opérationnelle importé, le nom de fichier doit correspondre au nom du flux de travail et doit se terminer par .ps1.

Pour ajouter des paramètres pour le flux de travail, utilisez le mot-clé **paramètre** comme vous le feriez pour un script. 

## <a name="code-changes"></a>Modifications du code

Code de flux de travail PowerShell est quasiment identique au code de script PowerShell à l’exception des modifications substantielles quelques.  Les sections suivantes décrivent les modifications que vous devez apporter à un script PowerShell pour qu’elle s’exécute dans un flux de travail.

### <a name="activities"></a>Activités

Une activité est une tâche spécifique dans un flux de travail. Tout comme un script est composé d’une ou plusieurs commandes, un flux de travail est composée d’une ou plusieurs activités sont effectuées dans une séquence. Flux de travail Windows PowerShell convertit automatiquement la plupart des applets de commande Windows PowerShell activités lors de l’exécution d’un flux de travail. Lorsque vous spécifiez une de ces applets de commande dans votre runbook, l’activité correspondante est exécutée en réalité par Windows Workflow Foundation. Pour ces applets de commande sans une activité correspondante, flux de travail Windows PowerShell s’exécute automatiquement l’applet de commande dans une activité [InlineScript](#inlinescript) . Il existe un ensemble d’applets de commande qui ne figurent pas et ne peuvent pas être utilisés dans un flux de travail, sauf si vous les incluez dans un bloc InlineScript. Pour plus d’informations sur ces concepts, reportez-vous [à l’aide des activités de flux de travail de Script](http://technet.microsoft.com/library/jj574194.aspx).

Activités de flux de travail partagent un ensemble de paramètres communs à configurer leur fonctionnement. Pour plus d’informations sur les paramètres communs de flux de travail, voir [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Paramètres de positionnement

Vous ne pouvez pas utiliser des paramètres de positionnement avec les activités et les applets de commande dans un flux de travail.  Cela signifie qu’est que vous devez utiliser des noms de paramètres.

Par exemple, considérez le code suivant qui obtient tous les services en cours d’exécution.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Si vous essayez d’exécuter ce code même dans un flux de travail, vous recevrez un message comme « jeu de paramètres ne peuvent pas être résolu à l’aide de paramètres nommés spécifié. »  Pour corriger ce problème, fournissent simplement le nom du paramètre comme indiqué ci-dessous.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objets désérialisés

Les objets dans des flux de travail sont désérialisés.  Cela signifie que leurs propriétés sont toujours disponibles, mais pas leurs méthodes.  Par exemple, considérez le code PowerShell suivant qui arrête un service à l’aide de la méthode Stop de l’objet de Service.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Si vous essayez d’exécuter dans un flux de travail, vous recevrez une erreur indiquant que « appel de la méthode n’est pas pris en charge dans un flux de travail Windows PowerShell ».  

Une option consiste à renvoyer à la ligne de ces deux lignes de code dans un bloc [InlineScript](#InlineScript) auquel cas $Service serait un objet de service au sein du bloc. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

Une autre option consiste à utiliser un autre applet de commande qui effectue les mêmes fonctionnalités que la méthode, s’il est disponible.  Dans le cas de notre exemple, l’applet de commande Stop-Service fournit les mêmes fonctionnalités que la méthode Stop, et vous pouvez aussi utiliser ce qui suit pour un flux de travail.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

L’activité **InlineScript** est utile lorsque vous avez besoin exécuter une ou plusieurs commandes en tant que script PowerShell traditionnel au lieu de flux de travail PowerShell.  Tandis que les commandes dans un flux de travail sont envoyés à Windows Workflow Foundation pour le traitement, les commandes dans un bloc InlineScript sont traitées par Windows PowerShell. 

InlineScript utilise la syntaxe ci-dessous.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Vous pouvez retourner la sortie à partir d’un InlineScript en affectant la sortie à une variable. L’exemple suivant arrête un service, puis renvoie le nom du service.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Vous pouvez passer des valeurs dans un bloc InlineScript, mais vous devez utiliser la touche de modification **$Using** étendue.  L’exemple suivant est identique à l’exemple précédent, sauf que le nom du service est fourni par une variable. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Activités InlineScript peuvent être critiques dans certains flux de travail, ils ne prennent pas en charge les constructions de flux de travail et ne doivent être utilisés lorsque cela est nécessaire pour les raisons suivantes :

- Vous ne pouvez pas utiliser [les points de contrôle](#Checkpoints) à l’intérieur d’un bloc InlineScript. En cas de panne au sein du bloc, il doit être relancé à partir du début du bloc.
- Vous ne pouvez pas utiliser [l’exécution en parallèle](#parallel-execution) à l’intérieur d’un InlineScriptBlock.
- InlineScript affecte extensibilité élevées du flux de travail dans la mesure où elle contient la session Windows PowerShell pour toute la longueur du bloc InlineScript.

Pour plus d’informations sur l’utilisation de InlineScript, voir [En cours d’exécution de commandes Windows PowerShell dans un flux de travail](http://technet.microsoft.com/library/jj574197.aspx) et [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Traitement en parallèle

L’un des avantages de flux de travail Windows PowerShell sont la possibilité d’effectuer un ensemble de commandes en parallèle plutôt que dans un ordre séquentiel comme avec un script typique. 

Vous pouvez utiliser le mot-clé **parallèle** pour créer un bloc de script avec plusieurs commandes qui seront exécutée simultanément. Cette méthode utilise la syntaxe de l’illustré ci-dessous. Dans ce cas, Activity1 et Activity2 démarre en même temps. Activity3 commence uniquement une fois Activity1 et Activity2 effectuées.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Par exemple, considérez les commandes PowerShell suivantes qui copier plusieurs fichiers vers une destination réseau.  Ces commandes sont exécutées dans un ordre séquentiel afin qu’un fichier doit finir le copiant avant la prochaine est démarrée.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Le flux de travail suivante s’exécute ces commandes mêmes en parallèle afin qu’ils sont tous commencent copie en même temps.  Uniquement une fois qu’ils sont tous complètement copié est le message de saisie semi-automatique affiché.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Vous pouvez utiliser la **ForEach-parallèle** construction de traiter des commandes pour chaque élément dans une collection de sites simultanément. Les éléments de la collection de sites sont traitées en parallèle, tandis que les commandes dans le bloc de script sont exécutées dans un ordre séquentiel. Cette méthode utilise la syntaxe de l’illustré ci-dessous. Dans ce cas, Activity1 démarre en même temps pour tous les éléments de la collection de sites. Pour chaque élément, Activity2 démarre une fois Activity1 terminée. Activity3 début et de fin Activity1 et Activity2 terminées pour tous les éléments.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

L’exemple suivant est similaire à l’exemple précédent copie des fichiers en parallèle.  Dans ce cas, un message s’affiche pour chaque fichier après que l’avoir copié.  Uniquement une fois qu’ils sont tous complètement copié est le message d’achèvement final affiché.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  Nous ne recommandons pas en cours d’exécution procédures opérationnelles enfant en parallèle dans la mesure où cela a été montré peut donner des résultats non fiables.  Le résultat de la procédure opérationnelle enfant parfois s’affichera pas, et paramètres dans un seul enfant runbook peuvent affecter les autres procédures opérationnelles enfant parallèle 


## <a name="checkpoints"></a>Points de contrôle

Un *point de contrôle* est un instantané de l’état actuel du flux de travail qui inclut la valeur actuelle de variables et toute sortie générée à ce point. Si un flux de travail se termine par erreur ou est suspendue, puis la prochaine exécution elle commence à partir de son dernier point de contrôle au lieu du début de la worfklow.  Vous pouvez définir un point de contrôle dans un flux de travail avec l’activité de **Point de contrôle-flux de travail** .

Dans le code suivant, une exception se produit après Activity2 à l’origine du flux de travail se terminer. Lorsque le flux de travail s’exécute à nouveau, il démarre en exécutant Activity2 car il s’agissait juste après que le dernier point de contrôle défini.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Vous devez définir des points de contrôle dans un flux de travail après que activités pouvant être propice aux exception et ne doivent pas être répétées si le flux de travail est relancée. Par exemple, votre flux de travail peut créer une machine virtuelle. Vous pouvez définir un point de contrôle avant et après les commandes pour créer la machine virtuelle. Si la création échoue, les commandes doivent être répétés si le flux de travail est démarré à nouveau. Si le le worfklow échoue après la création a réussi, puis la machine virtuelle ne sera pas créée à nouveau lorsque le flux de travail est relancée.

L’exemple suivant copie plusieurs fichiers vers un emplacement réseau et jeux d’un point de contrôle après chaque fichier.  Si l’emplacement réseau est perdue, le flux de travail se termine par erreur.  Lorsqu’il est lancé à nouveau, il va reprendre depuis le dernier point de contrôle ce qui signifie que seuls les fichiers qui ont déjà été copiés seront ignorés.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Étant donné que les informations d’identification du nom d’utilisateur ne sont pas conservées après l’appel à l’activité de [Flux de travail de suspension](https://technet.microsoft.com/library/jj733586.aspx) ou après le dernier point de contrôle, vous devez définir les informations d’identification valeur null, puis les récupérer à nouveau à partir du magasin de biens après l’appel de **Mise en veille-flux de travail** ou point de contrôle.  Dans le cas contraire, vous pouvez recevoir le message d’erreur suivant : *la tâche de flux de travail ne peut pas être repris, soit parce que les données de persistance Impossible complètement enregistrées, ou enregistrées persistance données ont été endommagées. Vous devez redémarrer le flux de travail.*

Le même code suivant montre comment traiter ce problème dans les procédures opérationnelles PowerShell le flux de travail.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Ce n’est pas nécessaire si vous soyez authentification à l’aide d’un compte Exécuter en tant que configuré avec un service principal.  

Pour plus d’informations sur les points de contrôle, voir [Ajout de points de contrôle sur un flux de travail de Script](http://technet.microsoft.com/library/jj574114.aspx).


## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser les procédures opérationnelles de flux de travail de PowerShell, voir [Mon premier runbook de flux de travail PowerShell](automation-first-runbook-textual.md) 
