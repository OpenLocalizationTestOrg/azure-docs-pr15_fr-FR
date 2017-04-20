<properties
    pageTitle="Forum aux questions sur pile Azure | Microsoft Azure"
    description="Pile Azure Forum aux questions."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Forum aux questions sur la pile d’Azure

## <a name="deployment"></a>Déploiement

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>Dois-je mettre en forme de mes disques de données avant de démarrer ou le redémarrage d’une installation ?

Disques doivent être au format brut. Si vous réinstallez le système d’exploitation, vous devrez peut-être vérifier si le pool de stockage ancien est toujours présent et supprimer à l’aide de la procédure suivante :

1. Ouvrez le Gestionnaire de serveur.
2. Sélectionnez Pools de stockage.
3. Voir si un pool de stockage est répertorié.
4. Menu contextuel **pool de stockage** si répertoriés et activer en lecture / écriture.
5. Avec le bouton droit de **disque dur virtuel** (coin inférieur gauche), puis sélectionnez Supprimer.
6. Droit **Pool de stockage** , puis cliquez sur Supprimer.
7. Relancez script pile Azure et vérifiez que la vérification du disque passe.

Vous pouvez également le script suivant peut être utilisé :

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>Puis-je utiliser tous les disques SSD pour le pool de stockage dans l’installation du contact ?

Cette configuration n’est pas pris en charge dans cette version.  Pour plus d’informations, voir le [guide de configuration requise](azure-stack-deploy.md) pour plus d’informations.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>Puis-je utiliser disques de données NVMe pour cette phase de pile Microsoft Azure ?

Stockage espaces Direct prend en charge NVMe disques, pile Azure prend uniquement en charge un sous-ensemble des combinaisons possibles et de types de disque possibles pour stockage espaces Direct. 

### <a name="how-can-i-reinstall-azure-stack"></a>Comment puis-je réinstaller pile Azure ?
Vous pouvez suivre les étapes décrites dans le [guide redéploiement](azure-stack-redeploy.md).  

## <a name="tenant"></a>Client

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>Puis-je déployer mes propres images comme un client ?

Oui, comme dans Azure, un client peut télécharger des images dans une pile Azure, outre les images à partir de l’administrateur de service. Pour une vue d’ensemble, voir l' [Ajout d’une Image de la machine virtuelle](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Test

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>Puis-je utiliser virtualisation imbriquées pour tester le contrôle du concept Microsoft Azure pile ?

Virtualisation imbriquée n’est pas pris en charge ou testée avec Azure pile Technical Preview 2.

## <a name="virtual-machines"></a>Machines virtuelles

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Pile Azure ne prend en charge des disques dynamiques pour les machines virtuelles ?

Pile Azure ne prend pas en charge les disques dynamiques.

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes](azure-stack-troubleshooting.md)
