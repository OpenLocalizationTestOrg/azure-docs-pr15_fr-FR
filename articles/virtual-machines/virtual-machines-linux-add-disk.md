<properties
    pageTitle="Ajout d’un disque de Linux VM | Microsoft Azure"
    description="Découvrez comment ajouter un disque permanent à votre Linux VM"
    keywords="machine virtuelle Linux, ajouter disque de la ressource"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Ajout d’un disque à une VM Linux

Cet article vous explique comment joindre un disque permanent à votre ordinateur virtuel afin que vous pouvez conserver vos données - même si votre ordinateur virtuel est configuré en raison de maintenance ou en redimensionnant. Pour ajouter un disque, vous devez [L’infrastructure du langage commun Azure](../xplat-cli-install.md) configuré en mode Gestionnaire de ressources (`azure config mode arm`).  

## <a name="quick-commands"></a>Commandes rapides

Dans les exemples suivants de la commande, remplacez les valeurs comprises entre &lt; et &gt; avec les valeurs de votre propre environnement.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Connectez un disque

Attacher un nouveau disque est rapide. Type de `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` pour créer et joindre un nouveau disque Go pour votre ordinateur virtuel. Si vous ne pas explicitement identifier un compte de stockage, un disque que vous créez est placé dans le même compte de stockage où réside votre disque du système d’exploitation.  Il doit ressembler à ce qui suit :

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Sortie

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Se connecter à la VM Linux monter le nouveau disque

> [AZURE.NOTE] Cette rubrique se connecte à une machine virtuelle à l’aide de noms d’utilisateur et les mots de passe. Pour utiliser des paires de clés privées et communiquer avec votre ordinateur virtuel, voir [comment utiliser SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md). Vous pouvez modifier la connectivité **SSH** de machines virtuelles créées avec la `azure vm quick-create` commande à l’aide de la `azure vm reset-access` commande pour réinitialiser **accèsSSH** complètement, ajouter ou supprimer des utilisateurs ou ajouter des fichiers de clé publiques pour sécuriser l’accès.

Vous devez à SSH votre machine virtuelle Azure pour partition, mettre en forme et le monter votre nouveau disque afin que votre Linux VM s’en servir. Si vous n’êtes pas familiarisé avec la connexion avec **ssh**, la commande prend la forme `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`et ressemble à ce qui suit :

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Sortie

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

À présent que vous êtes connecté à votre ordinateur virtuel, vous êtes prêt à joindre un disque.  Tout d’abord rechercher le disque, à l’aide de `dmesg | grep SCSI` (la méthode vous permet de découvrir votre nouveau disque peut-être varier). Dans ce cas, il ressemble à :

```bash
dmesg | grep SCSI
```

Sortie

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

et dans le cas de cette rubrique, la `sdc` disque est celui que nous voulons. Maintenant partition du disque avec `sudo fdisk /dev/sdc` --en supposant que votre initiale a été le disque `sdc`, rendre un disque principal sur partition 1 et acceptez les paramètres par défaut.

```bash
sudo fdisk /dev/sdc
```

Sortie

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Créer la partition en tapant `p` à l’invite :

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Et écrire un système de fichiers sur la partition à l’aide de la commande **mkfs** , et en indiquant le type de votre système de fichiers et le nom de l’appareil. Dans cette rubrique, nous utilisons `ext4` et `/dev/sdc1` ci-dessus :

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Sortie

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

À présent, nous créons un répertoire pour monter le système de fichiers à l’aide `mkdir`:

```bash
sudo mkdir /datadrive
```

Et monter le répertoire à l’aide `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

Le disque de données est maintenant prêt à utiliser comme `/datadrive`.

```bash
ls
```

Sortie

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, il doit être ajouté au fichier/etc/fstab. En outre, il est vivement recommandé de que l’UUID (identificateur Unique universel) est utilisé dans/etc/fstab pour faire référence à l’unité plutôt que le nom du périphérique (par exemple, `/dev/sdc1`). Si le système d’exploitation détecte une erreur de disque lors du démarrage, à l’aide de l’UUID évite le disque incorrect montage vers un emplacement donné. Restant disques de données serait puis être affectée à ces mêmes ID de périphériques. Pour rechercher l’UUID du nouveau lecteur, utilisez l’utilitaire **blkid** :

```bash
sudo -i blkid
```

Le résultat est semblable à ce qui suit :

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] Incorrectement modification du **fichier/etc/fstab** peut entraîner un système initialisé. Cas de doute, reportez-vous à la documentation de la distribution pour plus d’informations sur la façon de modifier correctement ce fichier. Il est également recommandé qu’une sauvegarde du fichier/etc/fstab est créée avant de le modifier.

Ensuite, ouvrez le **fichier/etc/fstab** dans un éditeur de texte :

```bash
sudo vi /etc/fstab
```

Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** qui a été créé dans les étapes précédentes et le point de montage **/datadrive**. Ajoutez la ligne suivante à la fin du **fichier/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] Supprimer ensuite un disque de données sans modifier fstab peut entraîner la machine virtuelle pour ne parviennent pas à démarrer. La plupart des versions fournissent la `nofail` et/ou `nobootwait` fstab options. Ces options permettent un système pour qu’il démarre même si le disque ne parvient pas à monter lors du démarrage. Consultez la documentation de votre distribution pour plus d’informations sur ces paramètres.

>[AZURE.NOTE] L’option **nofail** garantit que la machine virtuelle démarre même si le système de fichiers est endommagé ou le disque n’existe pas lors du démarrage. Sans cette option, vous pouvez rencontrer comportement comme décrit dans [Impossible SSH de machine virtuelle Linux en raison des erreurs FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge TRIM/annuler le mappage de Linux dans Azure
Certains noyaux Linux prennent en charge les opérations d’ajustement/annuler le mappage pour annuler les bloc non utilisés sur le disque. Ceci est principalement utile dans le stockage standard pour informer Azure que pages supprimées ne sont plus valide et peut être supprimé. Cela permet de gagner coût si vous créez des fichiers volumineux et supprimez.

Il existe deux façons d’activer TRIM prend en charge dans votre VM Linux. Pour l’approche recommandée, comme d’habitude, consultez votre distribution :

- Utiliser la `discard` monter option dans `/etc/fstab`, par exemple :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Par ailleurs, vous pouvez exécuter le `fstrim` commande manuellement à partir de la ligne de commande, ou ajoutez-le à votre crontab à exécuter régulièrement :

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Résolution des problèmes
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Étapes suivantes

- N’oubliez pas que votre nouveau disque n’est pas disponible pour la machine virtuelle si elle redémarre, sauf si vous écrivez ces informations dans votre fichier [fstab](http://en.wikipedia.org/wiki/Fstab) .
- Pour vous assurer que votre Linux VM est correctement configuré, passez en revue les recommandations [optimiser les performances de votre ordinateur Linux](virtual-machines-linux-optimization.md) .
- Développer votre capacité de stockage en ajoutant des disques supplémentaires et [configurer RAID](virtual-machines-linux-configure-raid.md) pour augmenter les performances.
