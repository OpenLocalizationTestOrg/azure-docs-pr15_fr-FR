<properties
   pageTitle="Dépannage des problèmes d’extension Linux VM | Microsoft Azure"
   description="En savoir plus sur la résolution des échecs d’extension machine virtuelle Linux Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Dépannage des problèmes d’extension machine virtuelle Linux Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Affichage de l’état de l’extension
Modèles de gestionnaire de ressources Azure peuvent être exécutées depuis l’interface Azure. Une fois que le modèle est exécuté, l’état de l’extension peut être affiché à partir de l’Explorateur de ressources Azure ou les outils de ligne de commande.

Voici un exemple :

Azure infrastructure du langage commun :

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Dépannage des échecs de Extenson :

### <a name="re-running-the-extension-on-the-vm"></a>Relançant l’extension de la machine virtuelle

Si vous exécutez des scripts sur l’ordinateur virtuel à l’aide d’Extension de Script personnalisé, vous pouvez parfois rencontrer un message d’erreur qui machine virtuelle a été créé correctement, mais que le script a échoué. Dans ces conditions, la procédure recommandée pour corriger cette erreur consiste à supprimer l’extension, puis exécutez à nouveau le modèle à nouveau.
Remarque : À l’avenir, cette fonctionnalité serait renforcée pour supprimer la nécessité de désinstallation de l’extension.

#### <a name="remove-the-extension-from-azure-cli"></a>Supprimer l’extension de l’infrastructure du langage commun Azure

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Où « nom objet Publisher » correspond au type d’extension de la sortie de « get-instance-vue machine virtuelle azure » et nom est le nom de la ressource extension à partir du modèle

Une fois que l’extension a été supprimée, le modèle peut être exécuté de nouveau pour exécuter les scripts sur l’ordinateur virtuel.
