<properties
 pageTitle="Gérer les nœuds de calcul cluster HPC Pack | Microsoft Azure"
 description="Découvrez les outils de script PowerShell pour ajouter, supprimer, démarrer et arrêter HPC Pack cluster cluster nœuds dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gérer le nombre et la disponibilité des nœuds de calcul dans un cluster HPC Pack dans Azure

Si vous avez créé un cluster HPC Pack dans Azure machines virtuelles, vous souhaiterez façons d’ajouter, supprimer, démarrer (disposition) ou arrêter le nœud (annulation) un certain nombre de cluster machines virtuelles du cluster. Pour effectuer ces tâches, exécuter des scripts PowerShell Azure qui sont installés sur le nœud de tête machine virtuelle. Ces scripts vous aident à contrôler le numéro et la disponibilité des ressources de votre cluster HPC Pack afin de contrôler les coûts.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Conditions préalables

* **Cluster HPC Pack dans Azure machines virtuelles** - créer un cluster HPC Pack dans le modèle de déploiement classique en utilisant au moins HPC Pack 2012 R2 mise à jour 1. Par exemple, vous pouvez automatiser le déploiement à l’aide de l’image HPC Pack machine virtuelle actuelle dans la Azure Marketplace et un script PowerShell Azure. Pour des informations et les conditions préalables, voir [créer un Cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Après le déploiement, recherchez le nœud scripts de gestion dans le champ % CCP\_Corbeille accueil % sur le nœud principal. Vous devez exécuter chacun des scripts en tant qu’administrateur.

* **Azure publier le certificat de fichier ou de gestion des paramètres** - vous devez effectuer une des opérations suivantes sur le nœud principal :

    * **Importer le Azure publier le fichier de configuration**. Pour ce faire, exécutez les applets de commande PowerShell Azure sur le nœud principal :

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurer le certificat de gestion Azure sur le nœud principal**. Si vous avez le fichier .cer, l’importer dans le magasin de certificats CurrentUser\My, puis exécutez l’applet de commande PowerShell Azure suivant pour votre environnement Azure (AzureCloud ou AzureChinaCloud) :

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Ajouter le nœud de calcul machines virtuelles

Ajouter des nœuds de calcul avec le script **Ajouter HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Paramètres

* **ServiceName** - nom du cloud service ce nouveau nœud cluster machines virtuelles sont ajoutés à.

* **ImageName** - machine virtuelle Azure nom de l’image, qui peut être obtenu via le portail classique Azure ou l’applet de commande PowerShell Azure **Get-AzureVMImage**. L’image doit répondre aux conditions suivantes :

    1. Un système d’exploitation Windows doit être installé.

    2. HPC Pack doit être installé dans le rôle de nœud de calcul.

    3. L’image doit être une image dans la catégorie utilisateur, et non une image machine virtuelle Azure publique privée.

* **Quantité** , nombre de nœud de calcul machines virtuelles à ajouter.

* **InstanceSize** - taille du nœud cluster machines virtuelles.

* **DomainUserName** - nom d’utilisateur de domaine, qui sera utilisé pour participer aux nouvelles machines virtuelles sur le domaine.

* **DomainUserPassword** - mot de passe de l’utilisateur de domaine.

* **NodeNameSeries** (facultatif) - modèle de dénomination pour les nœuds de calcul. Le format doit être &lt; *racine\_nom*&gt;&lt;*Démarrer\_nombre*&gt;%. Par exemple, moyens de 10 % MyCN % une série du nœud de calcul noms à partir de MyCN11. Le cas contraire, le script utilise le nœud configuré dénomination série dans le cluster HPC.

### <a name="example"></a>Exemple

L’exemple suivant ajoute le nœud de cluster de grande taille 20 machines virtuelles dans le cloud service *hpcservice1*, en fonction de l' image de machine virtuelle *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Supprimer le nœud de calcul machines virtuelles

Supprimer des nœuds de calcul avec le script **Supprimer HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Paramètres

* **Nom** : nom des nœuds de cluster à supprimer. Caractères génériques sont pris en charge. Le nom du jeu de paramètre est le nom. Vous ne pouvez pas spécifier le **nom** et le **nœud** paramètres.

* **Nœud** : HpcNode l’objet pour les nœuds à supprimer, qui peuvent être obtenus via l’applet de commande PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètre est nœud. Vous ne pouvez pas spécifier le **nom** et le **nœud** paramètres.

* **DeleteVHD** (facultatif) - configuration pour supprimer les disques associés pour les ordinateurs virtuels qui sont supprimés.

* **Force** (facultatif) - configuration pour forcer nœuds HPC en mode hors connexion avant de les supprimer.

* **Confirmer** (facultatif) - demander confirmation avant d’exécuter la commande.

* **WhatIf** - en définissant pour décrire que se passe-t-il si vous avez exécuté la commande sans exécuter la commande.

### <a name="example"></a>Exemple

L’exemple suivant force nœuds en mode hors connexion dont le nom commence *HPCNode-CN -* et les supprime les nœuds et leurs disques associés.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Démarrez cluster nœud machines virtuelles

Précédez nœuds de calcul le script de **Démarrage HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Paramètres

* **Nom** : les noms des nœuds de cluster pour qu’il démarre. Caractères génériques sont pris en charge. Le nom du jeu de paramètre est le nom. Vous ne pouvez pas spécifier le **nom** et le **nœud** paramètres.

* **Nœud**: HpcNode l’objet pour les nœuds pour qu’il démarre, qui peuvent être obtenus via l’applet de commande PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètre est nœud. Vous ne pouvez pas spécifier le **nom** et le **nœud** paramètres.

### <a name="example"></a>Exemple

L’exemple suivant démarre nœuds dont le nom commence *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Arrêter le nœud de calcul machines virtuelles

Arrêter les nœuds de calcul avec le script **Stop HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Paramètres


* **Nom**: les noms des nœuds de cluster à être arrêtée. Caractères génériques sont pris en charge. Le nom du jeu de paramètre est le nom. Vous ne pouvez pas spécifier le **nom** et le **nœud** paramètres.

* **Nœud** : HpcNode l’objet pour les nœuds à être arrêtée, qui peuvent être obtenus via l’applet de commande PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètre est nœud. Vous ne pouvez pas spécifier le **nom** et le **nœud** paramètres.

* **Force** (facultatif) - configuration pour forcer nœuds HPC en mode hors connexion avant d’arrêter les.

### <a name="example"></a>Exemple

L’exemple suivant force nœuds en mode hors connexion dont le nom commence *HPCNode-CN -* , puis s’arrête les nœuds.

Stop-HPCIaaSNode.ps1 – nom HPCNodeCN-*-Force

## <a name="next-steps"></a>Étapes suivantes

* Si vous souhaitez un moyen pour agrandir ou réduire les nœuds de cluster en fonction de la charge de travail en cours de projets et des tâches sur le cluster automatiquement, voir [automatiquement agrandir et réduire les ressources de cluster HPC Pack dans Azure en fonction de la charge de travail cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
