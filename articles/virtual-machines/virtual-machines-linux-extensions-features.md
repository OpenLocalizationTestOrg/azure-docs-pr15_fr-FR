<properties
 pageTitle="Machine virtuelle extensions et fonctionnalités | Microsoft Azure"
 description="Découvrez les extensions sont disponibles pour les machines virtuelles Azure, regroupés par qu’ils fournissent ou améliorent."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Les fonctionnalités et extensions de machine virtuelle

## <a name="azure-vm-extensions"></a>Extensions de machine virtuelle Azure

Azure Machine virtuelle extensions sont petites applications qui fournissent des tâches de configuration et automatisation de déploiement billet Azure machines virtuelles en fonctionnement. Par exemple, si une Machine virtuelle requiert un logiciel de protection antiviruse installée ou configuration Docker, une extension machine virtuelle peut être utilisée pour effectuer ces tâches. Extensions de machine virtuelle Azure peuvent être exécutées à l’aide de la Azure infrastructure du langage commun, PowerShell, gérer les ressources de modèles et le portail Azure. Extensions pouvant être fournies avec un nouveau déploiement machine virtuelle ou exécutées par rapport à n’importe quel système existant.

Ce document fournit des conditions préalables pour Machine virtuelle Azure extension et des conseils sur la détection des extensions de mémoire virtuelle disponibles. 

## <a name="azure-vm-agent"></a>Machine virtuelle Azure Agent

L’Agent de machine virtuelle Azure gère l’interaction entre une Machine virtuelle Azure et contrôleur de tissu Azure. L’agent de machine virtuelle est responsable de nombreux aspects fonctionnels de déploiement et la gestion des Machines virtuelles Azure, y compris les Extensions de machine virtuelle en cours d’exécution. L’Agent de machine virtuelle Azure est préinstallé sur les Images de la galerie Azure et peut être installé sur des systèmes d’exploitation pris en charge. 

Pour plus d’informations sur les systèmes d’exploitation pris en charge et des instructions d’installation, voir [Guide de l’utilisateur Agent Azure Linux](./virtual-machines-linux-agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Découvrir les Extensions de machine virtuelle

De nombreuses extensions machine virtuelle différentes sont disponibles pour une utilisation avec le Machines virtuelles Azure. Pour obtenir une liste complète, exécutez la commande suivante avec la CLI Azure, remplaçant l’emplacement par l’emplacement de choix.

```none
azure vm extension-image list westus
```

<br />

## <a name="common-vm-extensions"></a>Extensions de machine virtuelle courantes

|Nom de l’extension   |Description   |Plus d’informations   |
|---|---|---|
|Extension de Script personnalisé pour Linux  | Exécuter des scripts par rapport à une Machine virtuelle Azure  |[Extension de Script personnalisé pour Linux](./virtual-machines-linux-extensions-customscript.md)   |
|Extension docker |Installe le processus Docker pour prendre en charge les commandes Docker à distance.  | [Extension de machine virtuelle docker](./virtual-machines-linux-dockerextension.md)  |
|Extension d’accès aux machine virtuelle | Accéder à l’autre virtuelle Azure  |[Extension d’accès aux machine virtuelle](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Extension de Diagnostics de Windows Azure |Gérer les Diagnostics de Windows Azure |[Extension de Diagnostics de Windows Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

