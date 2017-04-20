<properties
   pageTitle="Dépannage des problèmes d’extension machine virtuelle Windows | Microsoft Azure"
   description="En savoir plus sur la résolution des échecs d’extension machine virtuelle Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Dépannage des problèmes d’extension machine virtuelle Windows Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Affichage de l’état de l’extension
Azure modèles Gestionnaire de ressources peuvent être exécutées à partir de PowerShell Azure. Une fois que le modèle est exécuté, l’état de l’extension peut être affiché à partir de l’Explorateur de ressources Azure ou les outils de ligne de commande.

Voici un exemple :

Azure PowerShell :

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Voici l’exemple de sortie :

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Dépannage des problèmes d’extension

### <a name="re-running-the-extension-on-the-vm"></a>Relançant l’extension de la machine virtuelle

Si vous exécutez des scripts sur l’ordinateur virtuel à l’aide d’Extension de Script personnalisé, vous pouvez parfois rencontrer un message d’erreur qui machine virtuelle a été créé correctement, mais que le script a échoué. Dans ces conditions, la procédure recommandée pour corriger cette erreur consiste à supprimer l’extension, puis exécutez à nouveau le modèle à nouveau.
Remarque : À l’avenir, cette fonctionnalité serait renforcée pour supprimer la nécessité de désinstallation de l’extension.


#### <a name="remove-the-extension-from-azure-powershell"></a>Supprimer l’extension de PowerShell Azure

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Une fois que l’extension a été supprimée, le modèle peut être exécuté de nouveau pour exécuter les scripts sur l’ordinateur virtuel.
