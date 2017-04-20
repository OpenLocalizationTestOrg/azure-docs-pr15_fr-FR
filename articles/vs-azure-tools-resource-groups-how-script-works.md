<properties
    pageTitle="Vue d’ensemble du script de déploiement de projet de groupe de ressources Azure | Microsoft Azure"
    description="Décrit comment fonctionne le script PowerShell dans le projet de déploiement de groupe de ressources Azure."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Vue d’ensemble du script de déploiement de projet de groupe de ressources Azure

Déploiement d’un groupe de ressources Azure des projets de l’aide de l’étape et déployer des fichiers et autres objets sur Azure. Lorsque vous créez un projet de déploiement d’Azure le Gestionnaire de ressources dans Visual Studio, un script PowerShell appelé **Déployer AzureResourceGroup.ps1** est ajouté au projet. Cette rubrique fournit des détails sur ce script et comment l’exécuter à l’intérieur et en dehors de Visual Studio.

## <a name="what-does-the-script-do"></a>Quel est le script ?

Le script de déploiement AzureResourceGroup.ps1 effectue deux opérations qui sont importantes pour le flux de travail de déploiement.

- Télécharger des fichiers ou des éléments nécessaires pour le déploiement de modèle
- Déployez le modèle

La première partie du script télécharge les fichiers et les objets pour le déploiement et l’applet de commande dernière dans le script réellement déployez le modèle. Par exemple, si une machine virtuelle doit être configuré avec un script, le script de déploiement tout d’abord en toute sécurité télécharge le script de configuration à un compte de stockage Azure. Cela rend disponible pour le Gestionnaire de ressources Azure pour configurer la machine virtuelle au cours de la mise en service.

Étant donné que pas tous les déploiements de modèle doivent avoir des objets supplémentaires qui doivent être téléchargés, un paramètre de commutateur appelé *uploadArtifacts* est évalué. Si les objets doivent être téléchargés, définissez le commutateur *uploadArtifacts* lorsque vous appelez le script. Notez que le fichier de modèle principale et le fichier de paramètres ne doivent être téléchargés. Seulement d’autres fichiers, tels que des scripts de configuration, les modèles de déploiement imbriqués et les fichiers d’application doivent être téléchargés.

## <a name="detailed-script-description"></a>Description détaillée de script

Voici une description de quels sélectionner des parties de la ne script déployer AzureResourceGroup.ps1 Azure PowerShell.

>[AZURE.NOTE] Cela décrit la version 1.0 du script déployer AzureResourceGroup.ps1.

1.  Déclarer des paramètres requises par projet de déploiement d’Azure le Gestionnaire de ressources. Certains paramètres ont des valeurs par défaut qui ont été définies lorsque le projet a été créé. Vous pouvez modifier ces valeurs par défaut dans le script ou ajouter des valeurs de paramètre différentes avant d’exécuter le script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Paramètre|Description|
  	|---|---|
  	|$ResourceGroupLocation|Emplacement centre région ou les données du groupe de ressources, tels que les **États-Unis Ouest** ou **Asie de l’est**.|
  	|$ResourceGroupName|Le nom du groupe de ressources Azure.|
  	|$UploadArtifacts|Valeur binaire qui indique si les objets devront être téléchargé vers Azure depuis votre système.|
  	|$StorageAccountName|Le nom de votre compte de stockage Azure où vos objets sont téléchargés.|
  	|$StorageAccountResourceGroupName|Le nom du groupe de ressources Azure qui contient le compte de stockage.|
  	|$StorageContainerName|Le nom du conteneur de stockage utilisé pour le téléchargement des objets.|
  	|$TemplateFile|Le chemin d’accès au fichier de déploiement (`<app name>.json`) dans votre projet de groupe de ressources Azure.|
  	|$TemplateParametersFile|Le chemin d’accès au fichier de paramètres (`<app name>.parameters.json`) dans votre projet de groupe de ressources Azure.|
  	|$ArtifactStagingDirectory|Le chemin d’accès sur votre système où objets sont localement téléchargés, y compris le dossier racine de script PowerShell. Ce chemin d’accès peut être absolue ou relative à l’emplacement de script.|
  	|$AzCopyPath|Le chemin d’accès dans lequel l’outil AzCopy.exe copie ses fichiers .zip, y compris le dossier racine de script PowerShell. Ce chemin d’accès peut être absolue ou relative à l’emplacement de script.|
  	|$DSCSourceFolder|Le chemin d’accès au dossier source DSC (Configuration de l’état vous le souhaitez), y compris le dossier racine de script PowerShell. Ce chemin d’accès peut être absolue ou relative à l’emplacement de script. Voir [Présentation de l’extension Azure PowerShell DSC (Configuration de l’état vous le souhaitez)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), le cas échéant, pour plus d’informations.|

1.  Vérifiez si les objets doivent être téléchargés vers Azure. Dans le cas contraire, passez à l’étape 11. Dans le cas contraire, procédez comme suit.

1.  Convertir toutes les variables avec les chemins d’accès relatifs chemins d’accès absolus. Par exemple, telles que modifier un chemin d’accès `..\Tools\AzCopy.exe` à `C:\YourFolder\Tools\AzCopy.exe`. En outre, initialisation les variables *ArtifactsLocationName* et *ArtifactsLocationSasTokenName* NULL. *ArtifactsLocation* et *SaSToken* peuvent être des paramètres pour le modèle. Si leurs valeurs sont null après lecture dans le fichier de paramètres, le script génère des valeurs pour eux.

    Les outils Azure utilisent les valeurs de paramètre *_artifactsLocation* et *_artifactsLocationSasToken* dans le modèle pour gérer les objets. Si le script PowerShell détecte paramètres avec les noms, mais les valeurs de paramètre ne sont pas fournis, le script télécharge les objets et renvoie les valeurs appropriées pour ces paramètres. Il les passe ensuite à l’applet de commande via `@OptionsParameters`.

  	|Variable|Description|
  	|---|---|
  	|ArtifactsLocationName|Le chemin d’accès à l’endroit où se trouvent les objets Azure.|
  	|ArtifactsLocationSasTokenName|Le nom de jeton associations de sécurité (partagés accès Signature) qui est utilisé par le script pour authentifier au Service Bus. Pour plus d’informations, consultez [Partagés Signature authentification de l’accès à Service](service-bus-shared-access-signature-authentication.md) .|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Cette section vérifie si la <app name>. parameters.json fichier (appelée « fichier paramètres ») a un nœud parent **paramètres** nommé (dans le `else` bloc). Dans le cas contraire, il comporte pas de nœud parent. Un seul format est acceptable.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Parcourir la collection de paramètres JSON. Si une valeur de paramètre a été affectée à *_artifactsLocation* ou *_artifactsLocationSasToken*, puis définir la variable *$OptionalParameters* avec ces valeurs. Cela empêche le script remplacer par inadvertance des valeurs de paramètre que vous avez défini.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  Obtenir la clé de compte de stockage et le contexte de la ressource de compte de stockage utilisée pour contenir les objets pour le déploiement.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Si vous utilisez DSC PowerShell pour configurer une machine virtuelle, l’extension DSC nécessite les objets se trouver dans un fichier zip unique. Par conséquent, créez un fichier d’archive .zip pour la configuration DSC. Pour ce faire, vérifiez si $DSCSourceFolder existe. Si une configuration DSC existe, supprimez-le, puis créez un nouveau fichier compressé appelé dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Si aucun chemin d’accès pour les objets Azure n’est fournie dans le fichier de paramètres, définissez un chemin d’accès pour le script PowerShell à utiliser lors du chargement des objets. Pour ce faire, créez un chemin d’accès à l’aide d’une combinaison de chemin d’accès du point de terminaison du compte de stockage et le nom de conteneur de stockage. Ensuite, mettez à jour le fichier de paramètres avec ce nouveau chemin d’accès.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Utiliser l’utilitaire **AzCopy** (inclus dans le dossier **Outils** de votre projet de déploiement de groupe de ressources Azure) pour copier les fichiers à partir de votre chemin de suppression de stockage local dans votre compte Azure stockage en ligne. Si cette étape échoue, quittez le script dans la mesure où le déploiement n’est pas susceptible de réussir sans les objets requis.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Si un jeton associations de sécurité à l’emplacement d’objets n’est pas indiquée dans le fichier de paramètres, créez-en un pour fournir un accès en lecture seule temporaire au conteneur de stockage en ligne. Puis, passez ce jeton associations de sécurité à la ligne de commande comme un « optionalParameter ». Notez que tous les paramètres transmis sur la ligne de commande seront prioritaire sur les valeurs fournies dans le fichier de paramètres.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Créer le groupe de ressources s’il ne pas déjà existe et archiver le fichier de modèle et les paramètres pour des erreurs de validation qui permet d’éviter le déploiement de réussir.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Pour finir, déployez le modèle. Ce code crée un nom unique pour le déploiement à l’aide d’un horodatage.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Déployer le groupe de ressources

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Pour déployer le groupe de ressources dans Visual Studio

1. Dans le menu contextuel du projet de groupe de ressources Azure, sélectionnez **déployer** > **Nouveau déploiement**.

    ![][0]

1. Dans la boîte de dialogue **déployer au groupe de ressources** , soit choisir un groupe de ressources existant dans la zone de liste déroulante pour le déploiement sur ou choisissez ** &lt;créer un nouveau... &gt;** Pour créer un nouveau groupe de ressources.

    ![][1]

1. Si vous y êtes invité, entrez un nom de groupe de ressources et un emplacement dans la boîte de dialogue **Créer un groupe de ressources** , puis sur le bouton **créer** .

    ![][2]

1. Cliquez sur le bouton **Modifier les paramètres** pour afficher la boîte de dialogue **Modifier les paramètres** , puis entrez des valeurs de paramètre manquant.

    ![][3]

    >[AZURE.NOTE] Si tous les paramètres requis avez besoin de valeurs, cette boîte de dialogue s’affiche automatiquement lorsque vous déployez.

    ![][4]

1. Lorsque vous avez terminé entrer des valeurs de paramètre, cliquez sur le bouton **Enregistrer** , puis sélectionnez le bouton **déployer** .

    L’exécution du script de déploiement (déploiement-AzureResourceGroup.ps1) et votre modèle, ainsi que les objets, déploie sur Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Déployer le groupe de ressources à l’aide de PowerShell

Si vous souhaitez exécuter le script sans utiliser la commande Visual Studio déploiement et l’interface utilisateur, dans le menu contextuel pour le script, cliquez sur **Ouvrir avec PowerShell ISE**.

![][5]


## <a name="command-deployment-examples"></a>Exemples de déploiement de commande

### <a name="deploy-using-default-values"></a>Déployer à l’aide des valeurs par défaut

Cet exemple montre comment faire pour exécuter le script en utilisant les valeurs de paramètre par défaut. (Car le paramètre d’emplacement n’a pas une valeur par défaut, vous devez fournir une.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Déployer remplacer les valeurs par défaut

Cet exemple montre comment exécuter le script pour déployer des fichiers de modèle et les paramètres qui diffèrent des valeurs par défaut.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Déployer à l’aide de UploadArtifacts pour la mise en attente

Cet exemple montre comment exécuter le script pour télécharger des objets à partir du dossier release et déployer des modèles de ceux définis par défaut.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Cet exemple montre comment exécuter le script dans une tâche PowerShell Azure dans Visual Studio en ligne.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le Gestionnaire de ressources Azure [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)de lecture.

Pour plus d’exemples d’utilisation des projets de groupe de ressources Azure, voir [déployer et gérer les ressources Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour des Démarrages rapides supplémentaires à partir de la démonstration HealthClinic.biz, consultez [Azure développeur outils Démarrages rapides](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
