<properties
    pageTitle="Réinitialiser l’accès sur machines virtuelles du Linux Azure à l’aide de le VMAccess Extension | Microsoft Azure"
    description="Réinitialiser l’accès sur machines virtuelles du Linux Azure à l’aide de le VMAccess Extension."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Gérer les utilisateurs, SSH et orthographe ou réparer des disques sur machines virtuelles du Linux Azure à l’aide de le VMAccess Extension

Cet article vous explique comment utiliser le VMAcesss Extension Azure pour vérifier ou réparer un disque, réinitialiser l’accès des utilisateurs, gérer les comptes d’utilisateur ou réinitialiser la configuration SSHD sous Linux. L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté à l’aide `azure login`.

- le mode de gestionnaire de ressources Azure Azure infrastructure du langage commun _doit être placé dans_ `azure config mode arm`.

## <a name="quick-commands"></a>Commandes rapides

Il existe deux façons d’utiliser VMAccess sur vos ordinateurs virtuels Linux :

- À l’aide de l’infrastructure du langage commun Azure et les paramètres requis.
- Utilisation de fichiers JSON bruts qui VMAccess traite et effectuer des opérations sur.

Pour la section commande rapide, nous allons utiliser l’infrastructure du langage commun Azure `azure vm reset-access` méthode. Dans les exemples suivants de la commande, remplacez les valeurs qui contiennent « exemple » avec les valeurs de votre propre environnement.

## <a name="create-a-resource-group-and-linux-vm"></a>Créer un groupe de ressources et de la machine virtuelle Linux

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Créer une machine virtuelle Debian

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Réinitialiser le mot de passe racine

Pour réinitialiser le mot de passe racine :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>Réinitialiser clé SSH

Pour réinitialiser le code SSH d’un utilisateur non racine :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Créer un utilisateur

Pour créer un utilisateur :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Supprimer un utilisateur

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Réinitialiser SSHD

Pour rétablir la configuration SSHD :

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

### <a name="vmaccess-defined"></a>VMAccess défini :

Le disque sur votre Linux VM présente les erreurs. Vous d’une certaine manière réinitialisez le mot de passe racine pour votre Linux VM ou supprimé par mégarde votre clé privée SSH. Dans ce cas dans les jours du centre de données, vous devrez lecteur il, puis ouvrez le KVM pour accéder à la console du serveur. Considérez l’extension Azure VMAccess comme ce commutateur qui vous permet d’accéder à la console pour rétablir l’accès Linux ou effectuer la maintenance de niveau de disque.

Pour la procédure pas à pas détaillées, nous allons utiliser la forme longue de VMAccess, qui utilise des fichiers JSON bruts.  Ces fichiers VMAccess JSON peuvent également être appelés à partir de modèles Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Utilisation de VMAccess pour vérifier ou réparer le disque d’un Linux VM

À l’aide de VMAccess, vous pouvez effectuer un fsck s’exécutent sur le disque sous votre VM Linux.  Vous pouvez également effectuer une vérification de disque et une réparation de disque à l’aide d’un VMAccess.

Pour vérifier, puis réparez le disque Utilisez ce script VMAccess :

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>À l’aide de VMAccess pour rétablir l’accès utilisateur Linux

Si vous avez perdu accès à la racine de votre Linux VM, vous pouvez démarrer un script VMAccess pour réinitialiser le mot de passe racine.

Pour réinitialiser le mot de passe racine, utilisez ce script VMAccess :

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Pour réinitialiser le code SSH d’un utilisateur non racine, utilisez ce script VMAccess :

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>L’utilisation de VMAccess pour gérer les comptes d’utilisateur sur Linux

VMAccess est un script Python qui peut servir à gérer des utilisateurs dans votre Linux VM sans se connecter et à l’aide de sudo ou le compte racine.

Pour créer un utilisateur, utilisez ce script VMAccess :

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Pour supprimer un utilisateur, utilisez ce script VMAccess :

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Utilisation de VMAccess pour réinitialiser la configuration SSHD

Si vous apportez des modifications à la configuration Linux machines virtuelles SSHD et fermez la connexion SSH avant de vérifier les modifications, vous ne puissent pas SSH'ing dans.  VMAccess peut être utilisé pour rétablir la configuration SSHD une configuration correcte connue sans être connecté en sur SSH.

Pour rétablir la configuration SSHD Utilisez ce script VMAccess :

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Étapes suivantes

Mise à jour Linux en utilisant les Extensions VMAccess Azure est une méthode pour apporter des modifications dans un VM Linux en cours d’exécution.  Vous pouvez également utiliser les outils tels que cloud initialisation et Azure modèles pour modifier votre VM Linux au démarrage.

[Les fonctionnalités et extensions de machine virtuelle](virtual-machines-linux-extensions-features.md)

[Gestionnaire de ressources Azure propos avec les extensions Linux VM](virtual-machines-linux-extensions-authoring-templates.md)

[Utilisation de cloud initialisation pour personnaliser une Linux VM lors de la création](virtual-machines-linux-using-cloud-init.md)
