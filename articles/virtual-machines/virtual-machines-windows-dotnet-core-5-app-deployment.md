<properties
   pageTitle="Automatisation de déploiement d’applications avec les Extensions Machine virtuelle | Microsoft Azure"
   description="Machine virtuelle Azure DotNet Core didacticiel"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Déploiement des applications avec les modèles de gestionnaire de ressources Azure

Une fois que toutes les exigences infrastructures Azure ont été identifiés et traduits dans un modèle de déploiement, le déploiement des applications réel doit être résolus. Déploiement d’une application ici fait référence à réinstaller les binaires application réelle sur les ressources d’Azure. Pour l’exemple de magasin de musique, .net Core et IIS doivent être installé et configuré sur chaque ordinateur virtuel. Les fichiers binaires du magasin de musique doivent être installés sur l’ordinateur virtuel, et la base de données de magasin de musique créés à l’avance.

Ce document en détail comment automatiser les extensions Machine virtuelle déploiement des applications et la configuration des machines virtuelles Azure. Toutes les dépendances et configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail ainsi que le modèle Azure le Gestionnaire de ressources. Le modèle complète sont accessibles ici – [Musique Store déploiement sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Script de configuration

Machine virtuelle extensions sont des programmes spécialisés qui s’exécutent sur machines virtuelles à fournir automation de configuration. Extensions sont disponibles à de nombreuses fins spécifiques tels que configuration Docker antivirus et configuration de la journalisation. L’extension de Script personnalisé peut être utilisée pour exécuter un script sur une machine virtuelle. Dans l’exemple de magasin de musique, il revient à l’extension de script personnalisé pour configurer les machines virtuelles Windows et installer l’application du magasin de musique.

Avant d’indiquant comment les extensions de machine virtuelle sont déclarées dans un modèle de gestionnaire de ressources Azure, examinez le script est exécuté. Ce script configure la machine virtuelle Windows pour héberger l’application de magasin de musique. Lorsque vous exécutez, le script installe tous nécessaires logiciel, installez l’application de magasin de musique à partir de contrôle de code source et préparer la base de données. 

> Cet exemple est pour la démonstration.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extension de Script machine virtuelle

Machine virtuelle Extensions peuvent être exécutées sur une machine virtuelle au moment de créer en incluant la ressource extension dans le modèle Azure le Gestionnaire de ressources. L’extension peut être ajoutée avec l’Assistant Visual Studio ajouter une ressource, ou en insérant JSON valide dans le modèle. La ressource de Script Extension est imbriquée à l’intérieur de la ressource Machine virtuelle ; Ceci est visible dans l’exemple suivant.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Extension de Script machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Notez que dans la ci-dessous JSON qu’il est stocké dans GitHub. Ce script peut également être stocké dans le stockage Blob Azure. En outre, le Gestionnaire de ressources Azure modèles permettre de la chaîne de l’exécution de script à construite, tels que les valeurs de paramètres de modèle peuvent être utilisées en tant que paramètres pour l’exécution de scripts. Dans ce cas les données sont fournies lorsque vous déployez les modèles, et ces valeurs puis peuvent être utilisés lorsque vous exécutez le script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé, voir [extensions de script personnalisé avec les modèles de gestionnaire de ressources](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Étape suivante

<hr>

[Explorer plus Azure modèles Gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates)
