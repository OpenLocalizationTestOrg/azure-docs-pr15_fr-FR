<properties
   pageTitle="Redimensionnement d’un Linux VM | Microsoft Azure"
   description="Comment mettre à l’échelle vers le haut ou ajuster vers le bas une machine virtuelle Linux, en modifiant la taille de la mémoire virtuelle."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Redimensionnement d’un Linux VM

## <a name="overview"></a>Vue d’ensemble 

Une fois que vous devez configurer une machine virtuelle (), vous pouvez mettre à l’échelle la machine virtuelle vers le haut ou vers le bas en modifiant la [taille de mémoire virtuelle][vm-sizes]. Dans certains cas, vous devez d’abord libérer la machine virtuelle. Cela peut arriver si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge la machine virtuelle.

Cet article vous explique comment redimensionner un VM Linux à l’aide de l' [Infrastructure du langage commun Azure][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.


## <a name="resize-a-linux-vm"></a>Redimensionner une machine virtuelle Linux 

Pour redimensionner une machine virtuelle, procédez comme suit.

1. Exécutez la commande suivante infrastructure du langage commun. Cette commande répertorie les tailles de mémoire virtuelle disponibles sur le cluster matériel où la machine virtuelle est hébergée.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Si la taille souhaitée est répertoriée, exécutez la commande suivante pour redimensionner la machine virtuelle.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    La machine virtuelle redémarre au cours du processus. Après le redémarrage, votre système d’exploitation existant et disques de données seront remappés. Tout ce que le disque temporaire sera perdue.

    Utiliser la `--enable-boot-diagnostics` option permet de [diagnostics démarrage][boot-diagnostics], pour vous connecter les erreurs liées au démarrage.

3. Dans le cas contraire, si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle, redimensionner et redémarrez la machine virtuelle.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Libérer la machine virtuelle libère également toutes les adresses IP dynamiques attribuées à la machine virtuelle. Les disques du système d’exploitation et les données ne sont pas concernés.
   
## <a name="next-steps"></a>Étapes suivantes

Pour une extensibilité supplémentaire, exécutez plusieurs instances de machine virtuelle et évoluer. Pour plus d’informations, voir [Ajuster automatiquement machines Linux dans un jeu d’échelle de Machine virtuelle][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md