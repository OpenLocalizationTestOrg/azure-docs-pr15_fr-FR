<properties 
    pageTitle="Redéployez Machines virtuelles Linux | Microsoft Azure" 
    description="Décrit comment redéployez machines virtuelles de Linux pour atténuer les problèmes de connexion SSH." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Redéployez machine virtuelle au nouveau nœud Azure

Si vous avez été en difficulté dépannage SSH ou application à accéder à une Azure machine virtuelle (), redéployez la machine virtuelle peut-être vous aider. Lorsque vous redéployez une machine virtuelle, il déplace la machine virtuelle vers un nouveau nœud au sein de l’infrastructure Azure et puis sous tension il précédent, en conservant tous vos options de configuration et les ressources associées. Cet article vous explique comment redéployez une machine virtuelle à l’aide de Azure infrastructure du langage commun ou le portail Azure.

> [AZURE.NOTE] Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associés aux interface réseau virtuelle sont mis à jour. 


## <a name="using-azure-cli"></a>À l’aide d’Azure infrastructure du langage commun

Vérifiez que vous avez la [Dernière Azure infrastructure du langage commun installé](../xplat-cli-install.md) sur votre ordinateur et que vous êtes en mode Gestionnaire de ressources (`azure config mode arm`).

Utiliser la commande suivante Azure infrastructure du langage commun pour redéployez votre machine virtuelle :

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Vous pouvez afficher l’état de la modification de la mémoire virtuelle comme elle effectue le processus de redéploiement. La `PowerState` de la machine virtuelle accède 'Exécution' à 'Mise à jour », puis 'à' et enfin 'Running' comme il accède à travers le processus de redéployez vers un nouvel hôte. Vérifier l’état des ordinateurs virtuels au sein d’un groupe de ressources avec :

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes de connexion à votre ordinateur virtuel, vous pouvez trouver de l’aide spécifique sur la [résolution des problèmes de connexions SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) ou [SSH détaillée des étapes de dépannage](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Si vous ne pouvez pas accéder à une application s’exécutant sur votre ordinateur virtuel, vous pouvez également lire des [problèmes de dépannage des applications](virtual-machines-linux-troubleshoot-app-connection.md).