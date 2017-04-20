<properties
   pageTitle="Création de modèles avec les extensions Linux VM | Microsoft Azure"
   description="En savoir plus sur le Gestionnaire de ressources Azure modèles avec les extensions de création pour les machines virtuelles Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Gestionnaire de ressources Azure propos avec les extensions Linux VM

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

À partir d’Azure infrastructure du langage commun, exécutez la commande suivante :

      Azure VM extension list

Cette commande renvoie le nom de l’éditeur, le nom de l’extension et la version comme suit :

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés mappent « éditeur », « type » et « typeHandlerVersion » respectivement dans l’extrait de modèle ci-dessus.

>[AZURE.NOTE]Il est toujours recommandé d’utiliser la dernière version d’extension pour obtenir la fonctionnalité la plus à jour.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identification du schéma pour les paramètres de configuration d’extension

L’étape suivante avec la création d’un modèle d’extension consiste à identifier le format pour fournir des paramètres de configuration. Chaque extension prend en charge son propre jeu de paramètres.

Pour examiner les exemples de configuration Linux extensions, cliquez sur la documentation de voir [Linux eExtensions exemples](virtual-machines-linux-extensions-configuration-samples.md).

Reportez-vous à ce qui suit pour obtenir un modèle totalement terminé avec les Extensions machine virtuelle.

[Extension de script personnalisé sur un Linux VM](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Après le modèle de création, vous pouvez déployer à l’aide de l’infrastructure du langage commun Azure.
