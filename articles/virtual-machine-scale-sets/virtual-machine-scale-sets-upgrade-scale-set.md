<properties
    pageTitle="Déployer une application sur les jeux d’échelle machine virtuelle | Microsoft Azure"
    description="Déployer une application sur les jeux d’échelle machine virtuelle"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>Mettre à niveau un ensemble d’échelle machine virtuelle

Cet article décrit comment vous pouvez déployer une mise à jour du système d’exploitation sur une échelle Azure machine virtuelle définie sans interruption de service. Dans ce contexte, une mise à jour du système d’exploitation entraîne la modification de la version ou la référence (SKU) de système d’exploitation ou en modifiant l’URI d’une image personnalisée. Mise à jour sans interruption moyens mise à jour des machines virtuelles celui individuellement ou en groupes (par exemple, un domaine d’erreur à la fois) plutôt que d’en une seule fois. En procédant ainsi, toutes les machines virtuelles ne sont pas mis à niveau peut conserver en cours d’exécution.

Pour éviter toute ambiguïté, nous allons distinguer les trois types de mise à jour du système d’exploitation que vous souhaiterez peut-être effectuer :

- Modification de la version ou la référence (SKU) d’une image de la plateforme. Par exemple, la modification Ubuntu version 14.04.2-LTS de 14.04.201506100 en 14.04.201507060, ou en modifiant les dernières/15.10 Ubuntu référence (SKU) en 16.04.0-LTS/latest. Ce scénario est décrit dans cet article.

- Modification de l’URI qui pointe vers une nouvelle version d’une image personnalisée vous intégré (**Propriétés** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). Ce scénario est décrit dans cet article.

- Correction du système d’exploitation à partir d’une machine virtuelle (des exemples d’installation d’un correctif de sécurité et en exécutant Windows Update). Ce scénario est pris en charge, mais pas traitée dans cet article.

Les deux premières options sont pris en charge besoins couverts par le présent article. Vous devez créer une nouvelle échelle pour exécuter la troisième option définie.

Jeux d’échelle machine virtuelle déployées dans le cadre d’un cluster [Azure Service TISSU](https://azure.microsoft.com/services/service-fabric/) n’est pas abordés ici.

La séquence de base pour modifier le système d’exploitation version/SKU d’une image de la plateforme ou l’URI d’une image personnalisée se présente comme suit :

1. Obtenir le modèle du jeu échelle machine virtuelle.

2. Modifier la version, référence (SKU) ou URI valeur dans le modèle.

3. Mettre à jour le modèle.

4. Effectuer un appel *manualUpgrade* sur les ordinateurs virtuels dans l’ensemble d’échelle. Cette étape n’est utile si *upgradePolicy* est défini sur **manuel** dans votre jeu d’échelle. Si elle est définie sur **automatique**, tous les ordinateurs virtuels sont mis à niveau en même temps, et donc à l’origine de temps d’arrêt.


Grâce à ces informations d’arrière-plan à l’esprit, voyons comment vous pouvez mettre à jour la version d’une échelle définie dans PowerShell et à l’aide de l’API REST. Ces exemples couvrent le cas d’une image platform, mais cet article fournit suffisamment d’informations pour vous permettant d’adapter cette procédure à suivre pour une image personnalisée.

## <a name="powershell"></a>PowerShell##

Cet exemple met à jour une échelle de machine virtuelle Windows définie pour la nouvelle version 4.0.20160229. Après mise à jour le modèle, elle effectue une instance machine virtuelle une mise à jour à la fois.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si vous mettez à jour l’URI pour une image personnalisée au lieu de changer d’une version de l’image plateforme, remplacez la ligne « définir la nouvelle version » par ce qui suit :

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>L’API REST

Voici quelques exemples de Python qui utilisent l’API REST Azure à déployer une mise à jour de la version du système d’exploitation. Les deux utilisent la bibliothèque légère [azurerm](https://pypi.python.org/pypi/azurerm) des fonctions d’emballage API REST Azure pour exécuter une commande GET sur le modèle de jeu échelle, suivi de vente avec un modèle mis à jour. Qu’ils ont également dans les vues d’instances de machine virtuelle pour identifier les machines virtuelles par domaine de mise à jour.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) est un script Python qui est utilisé pour déployer une mise à niveau du système d’exploitation sur une échelle de machine virtuelle en cours d’exécution définie un domaine de mise à jour à la fois.

![Script de Vmssupgrade pour le choix des machines virtuelles ou un domaine de mise à jour](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Ce script vous permet de choisir des machines virtuelles spécifiques pour mettre à jour ou spécifier un domaine de mise à jour. Il prend en charge la modification d’une version de l’image platform ou en modifiant l’URI d’une image personnalisée.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) est un éditeur à usage général pour les jeux d’échelle machine virtuelle qui affiche l’état de la machine virtuelle comme un heatmap où une seule ligne représente un domaine de mise à jour. Entre autres, vous pouvez mettre à jour le modèle pour un ensemble d’échelle avec une nouvelle version, une référence (SKU) ou une image personnalisée URI et choisissez domaines d’erreur pour mettre à niveau. Lorsque vous procédez ainsi, tous les ordinateurs virtuels dans ce domaine de mise à jour sont mis à niveau vers le nouveau modèle. Par ailleurs, vous pouvez effectuer une mise à niveau propagée en fonction de la taille du lot de votre choix.  

La capture d’écran suivante montre un modèle d’une échelle définie pour Ubuntu 14.04-2LTS version 14.04.201507060. Beaucoup plus d’options ont été ajoutés à cet outil étant donné que cette capture d’écran a été effectuée.

![Modèle de Vmsseditor d’une échelle définie pour Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Une fois que vous cliquez sur **mettre à niveau** et **Obtenir des détails**, machines virtuelles dans UD 0 commencer à mettre à jour.

![Mise à jour d’affichant Vmsseditor en cours](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
