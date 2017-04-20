<properties 
    pageTitle="Démarrer et arrêter machines virtuelles avec Azure Automation - flux de travail PowerShell | Microsoft Azure"
    description="Version graphique du scénario Automatisation Azure, y compris les procédures opérationnelles pour démarrer et arrêter machines virtuelles classiques."
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
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Scénario d’automatisation Azure - de démarrage et d’arrêt machines virtuelles

Ce scénario Automatisation Azure inclut les procédures opérationnelles pour démarrer et arrêter machines virtuelles classiques.  Vous pouvez utiliser ce scénario pour une des opérations suivantes :  

- Utilisez les procédures opérationnelles sans modification dans votre environnement. 
- Modifier les procédures opérationnelles pour effectuer la fonctionnalité personnalisée.  
- Appelez les procédures opérationnelles à partir d’un autre runbook dans le cadre d’une solution globale. 
- Utilisez les procédures opérationnelles sous forme de didacticiels pour découvrir runbook concepts de création. 

> [AZURE.SELECTOR]
- [Graphique](automation-solution-startstopvm-graphical.md)
- [Flux de travail PowerShell](automation-solution-startstopvm-psworkflow.md)

Il s’agit de la version de runbook de flux de travail PowerShell de ce scénario. Il est également disponible à l’aide des [procédures opérationnelles graphique](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Prise le scénario

Ce scénario se compose de deux procédures opérationnelles PowerShell le flux de travail que vous pouvez télécharger les liens suivants.  Afficher la [version graphique](automation-solution-startstopvm-graphical.md) de ce scénario, les liens vers les procédures opérationnelles graphique.

| Runbook | Lien | Type | Description |
|:---|:---|:---|:---|
| Démarrage AzureVMs | [Démarrer Azure machines virtuelles classiques](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Flux de travail PowerShell | Démarre toutes les machines virtuelles classiques dans un subscriptionor Azure toutes les machines virtuelles avec un nom de service particulier. |
| Stop AzureVMs | [Arrêter Azure machines virtuelles classiques](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Flux de travail PowerShell | Arrête tous les ordinateurs virtuels dans un compte automation ou tous les ordinateurs virtuels avec un nom de service particulier.  |


## <a name="installing-and-configuring-the-scenario"></a>Installer et configurer le scénario

### <a name="1-install-the-runbooks"></a>1. installer les procédures opérationnelles

Après avoir téléchargé les procédures opérationnelles, vous pouvez les importer à l’aide de la procédure dans [l’importation une procédure opérationnelle](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. consultez la description et la configuration requise
Les procédures opérationnelles incluent du texte d’aide commentées qui inclut une description et éléments requis.  Vous pouvez également ouvrir les mêmes informations à partir de cet article. 

### <a name="3-configure-assets"></a>3. configurer les actifs
Les procédures opérationnelles nécessitent les ressources suivantes que vous devez créer et remplir avec les valeurs appropriées.

| Type d’élément | Nom de la ressource | Description |
|:---|:---|:---|:---|
| Informations d’identification | AzureCredential | Contient des informations d’identification d’un compte qui a autorité pour démarrer et arrêter machines virtuelles dans l’abonnement Azure.  Vous pouvez également spécifier un autre actif d’informations d’identification dans le paramètre **d’informations d’identification** de l’activité **Ajouter AzureAccount** . |
| Variable | AzureSubscriptionId | Contient l’ID de l’abonnement de votre abonnement Azure. |

## <a name="using-the-scenario"></a>À l’aide du scénario

### <a name="parameters"></a>Paramètres

Les procédures opérationnelles chaque présentent les paramètres suivants.  Vous devez fournir des valeurs pour tous les paramètres obligatoires et pouvez éventuellement fournir des valeurs pour d’autres paramètres selon vos besoins.

| Paramètre | Type | Obligatoire | Description |
|:---|:---|:---|:---|
| ServiceName | chaîne | N° | Si une valeur est fournie, toutes les machines virtuelles portant le même nom services sont démarrés ou arrêtés.  Si aucune valeur n’est fourni, tous les ordinateurs virtuels classiques de l’abonnement Azure sont démarrés ou arrêtés. |
| AzureSubscriptionIdAssetName | chaîne | N° | Contient le nom de la [variable de biens](#installing-and-configuring-the-scenario) qui contient l’ID de l’abonnement de votre abonnement Azure.  Si vous ne spécifiez pas une valeur, *AzureSubscriptionId* est utilisé.  |
| AzureCredentialAssetName | chaîne | N° | Indique le nom de l' [actif d’informations d’identification](#installing-and-configuring-the-scenario) qui contient les informations d’identification pour la procédure opérationnelle à utiliser.  Si vous ne spécifiez pas une valeur, *AzureCredential* est utilisé.  |

### <a name="starting-the-runbooks"></a>Démarrage les procédures opérationnelles

Vous pouvez utiliser une des méthodes lors du [démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) pour démarrer une des procédures l’opérationnelles dans ce scénario.

Les exemples de commandes suivante utilise Windows PowerShell pour exécuter **StartAzureVMs** pour démarrer tous les ordinateurs virtuels avec le nom du service *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Sortie

Les procédures opérationnelles automatiquement [un message de sortie](automation-runbook-output-and-messages.md) pour chaque machine virtuelle indiquant que le démarrer ou arrêter l’instruction a été envoyé ou non.  Vous pouvez rechercher une chaîne spécifique dans le résultat pour déterminer le résultat pour chaque runbook.  Les chaînes de sortie possibles sont répertoriées dans le tableau suivant.

| Runbook | Condition | Message |
|:---|:---|:---|
| Démarrage AzureVMs | Machine virtuelle est en cours d’exécution  | MyVM est en cours d’exécution |
| Démarrage AzureVMs | Demande de démarrage pour machine virtuelle envoyé avec succès | MyVM a été démarré |
| Démarrage AzureVMs | Échec de la demande de démarrage de machine virtuelle  | MyVM n’a pas pu démarrer |
| Stop AzureVMs | Machine virtuelle est déjà arrêté  | MyVM est déjà arrêté |
| Stop AzureVMs | Arrêter de demande de machine virtuelle envoyé avec succès | MyVM a été arrêté. |
| Stop AzureVMs | Échec de la demande d’arrêt de machine virtuelle  | Impossible d’arrêter MyVM |

Par exemple, l’extrait de code suivantes à partir d’un runbook tente de démarrer tous les ordinateurs virtuels avec le nom du service *MyServiceName*.  Si un de l’échec de demandes début, actions d’erreur peuvent être effectuées. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Description détaillée

Voici une description détaillée de la procédures opérationnelles dans ce scénario.  Vous pouvez utiliser ces informations pour personnaliser les procédures opérationnelles ou tout simplement pour apprendre à partir de ceux-ci pour la création de votre propre scénarios d’automatisation.

### <a name="parameters"></a>Paramètres

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Le flux de travail démarre en obtenant les valeurs pour les [paramètres d’entrée](#using-the-scenario).  Si les noms des éléments ne sont pas fournis noms par défaut sont utilisés.

### <a name="output"></a>Sortie

    # Returns strings with status messages
    [OutputType([String])]

Cette ligne déclare que le résultat de la runbook sera une chaîne.  Ce n’est pas obligatoire mais est une pratique recommandée pour lorsque le runbook est utilisé comme un [enfant runbook](automation-child-runbooks.md) afin qu’un parent runbook connaissent le type de sortie va-t-il se passer.

### <a name="authentication"></a>Authentification

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Les lignes suivantes définissent les [informations d’identification](automation-configuring.md#configuring-authentication-to-azure-resources) et d’abonnement Azure qui sera utilisé pour le reste de la procédure opérationnelle.
Tout d’abord, nous utilisons **Get-AutomationPSCredential** pour obtenir les biens qui contient les informations d’identification avec access pour démarrer et arrêter machines virtuelles dans l’abonnement Azure. **Ajouter AzureAccount** utilise ensuite cet actif pour définir les informations d’identification.  Le résultat est affecté à une variable factice afin qu’il n’est pas inclus dans le résultat de la procédure opérationnelle.  

La variable bien avec l’ID de l’abonnement est extraite puis avec **Get-AutomationVariable** et l’abonnement avec **Sélectionnez AzureSubscription**.

### <a name="get-vms"></a>Obtenir des machines virtuelles

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** est utilisée pour récupérer les machines virtuelles avec que du runbook fonctionnent.  Si une valeur est fournie dans la variable d’entrée **ServiceName%** , uniquement les machines virtuelles portant le même nom de service sont récupérés.  Si **NomService** est vide, toutes les machines virtuelles sont récupérés.

### <a name="startstop-virtual-machines-and-send-output"></a>Début/fin machines virtuelles et envoyer la sortie

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

La prochaine étape de lignes à chaque machine virtuelle.  Tout d’abord **PowerState** de la machine virtuelle est contrôlé pour déterminer s’il est déjà en cours d’exécution ou arrêté, selon la procédure opérationnelle.  S’il est déjà dans l’état cible, un message est envoyé à la sortie et la fin de la procédure opérationnelle.  Dans le cas contraire, puis **AzureVM de démarrer** ou **Arrêter AzureVM** est utilisé pour tenter de démarrer ou arrêter la machine virtuelle avec le résultat de la demande stocké dans une variable.  Un message est envoyé puis en sortie spécifiant si la demande pour démarrer ou arrêter a été envoyée.


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation des procédures opérationnelles enfant, consultez [procédures opérationnelles enfant dans Azure Automation](automation-child-runbooks.md) 
- Pour en savoir plus sur les messages sortants lors de l’exécution du runbook et la journalisation pour résoudre les problèmes, voir [Runbook sortie et messages dans Azure Automation](automation-runbook-output-and-messages.md)
