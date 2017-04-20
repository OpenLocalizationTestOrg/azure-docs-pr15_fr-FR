<properties
   pageTitle="Modèles de création avec les extensions machine virtuelle Windows | Microsoft Azure"
   description="En savoir plus sur le Gestionnaire de ressources Azure modèles avec les extensions de création pour les machines virtuelles Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Propos avec les extensions machine virtuelle Windows Azure le Gestionnaire de ressources

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

À partir d’Azure PowerShell, exécutez l’applet de commande PowerShell Azure suivant :

      Get-AzureVMAvailableExtension


Cette applet de commande renvoie le nom de l’éditeur, le nom de l’extension et la version comme suit :

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés mappent « éditeur », « type » et « typeHandlerVersion » respectivement dans l’extrait de modèle ci-dessus.

>[AZURE.NOTE]Il est toujours recommandé d’utiliser la dernière version d’extension pour obtenir la fonctionnalité la plus à jour.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identification du schéma pour les paramètres de configuration d’extension

L’étape suivante avec la création d’un modèle d’extension consiste à identifier le format pour fournir des paramètres de configuration. Chaque extension prend en charge son propre jeu de paramètres.

Pour consulter des exemples pour les extensions Windows, voir les [exemples d’extensions Windows](virtual-machines-windows-extensions-configuration-samples.md).


Reportez-vous à ce qui suit pour obtenir un modèle totalement terminé avec les extensions machine virtuelle.

[Extension de Script personnalisé sur un Windows machine virtuelle](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Après le modèle de création, vous pouvez déployer à l’aide de PowerShell Azure.
