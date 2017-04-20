<properties
    pageTitle="Connectez un disque à une VM Linux | Microsoft Azure"
    description="Découvrez comment joindre un disque de données pour une machine virtuelle Azure exécutant Linux et initialisation afin qu’elle soit prêt à l’emploi."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>L’insertion d’un disque de données pour une Machine virtuelle Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Consultez [l’Insertion d’un disque de données à l’aide du modèle de déploiement Gestionnaire de ressources](virtual-machines-linux-add-disk.md).

Vous pouvez joindre à la fois vides disques et qui contiennent des données dans vos ordinateurs virtuels Azure. Les deux types de disques sont des fichiers .vhd qui résident dans un compte de stockage Azure. En tant qu’à l’ajout d’un disque pour une machine Linux, après que vous attachez le disque vous devez initialisation et mettre en forme, elle est prête à être utilisée. Cet article explique en joignant les disques vides et contenant déjà des données à vos ordinateurs virtuels, ainsi que comment puis initialisation et mettre en forme un nouveau disque.

> [AZURE.NOTE]Il est recommandé d’utiliser une ou plusieurs des disques distincts pour stocker les données d’un ordinateur virtuel. Lorsque vous créez une machine virtuelle Azure, il a un disque de système d’exploitation et un disque temporaire. **N’utilisez pas le disque temporaire pour stocker les données permanentes.** Comme le nom l’indique, il fournit un stockage temporaire uniquement. Il ne propose aucune redondance ou la sauvegarde, car il ne se trouvent dans le stockage Azure.
> Le disque temporaire est généralement géré par l’Agent de Linux Azure et montage automatique **/mnt/resource** (ou **/mnt** sur les images Ubuntu). En revanche, un disque de données peut être nommé par le noyau Linux intitule `/dev/sdc`, et vous devez partition, mettre en forme et monter cette ressource. Consultez le [Guide de l’utilisateur Azure Linux Agent] [ Agent] pour plus d’informations.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Initialisation d’un disque de données nouveau dans Linux

1. SSH à votre ordinateur virtuel. Pour plus d’informations, voir [comment se connecter à une machine virtuelle exécutant Linux][Logon].

2. Vous devez ensuite rechercher l’identificateur de périphérique pour le disque données initialisation. Il existe deux manières de procéder :

    a) Grep de périphériques SCSI dans les journaux, par exemple, dans la commande suivante :

            $sudo grep SCSI /var/log/messages

    Pour la distribution Ubuntu récente, vous devrez peut-être utiliser `sudo grep SCSI /var/log/syslog` car l’enregistrement à `/var/log/messages` susceptibles d’être désactivées par défaut.

    Vous pouvez trouver l’identificateur de la dernière disquette de données qui a été ajoutée dans les messages qui sont affichés.

    ![Obtenir les messages de disque](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OR

    b) utilisez le `lsscsi` commande pour déterminer l’id du périphérique. `lsscsi`peut être installé à des options `yum install lsscsi` (sur rouge chapeau basé répartitions) ou `apt-get install lsscsi` (sur Debian en fonction de distribution). Vous pouvez trouver le disque recherchée par son _lun_ ou le **numéro d’unité logique**. Par exemple, le _lun_ pour les disques puisse être vu facilement à partir de `azure vm disk list <virtual-machine>` en tant que :

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Comparer ces données avec la sortie de `lsscsi` pour le même exemple machine virtuelle :

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    Le dernier chiffre dans le tuple dans chaque ligne est le _lun_. Voir `man lsscsi` pour plus d’informations.

3. À l’invite, tapez la commande suivante pour créer votre appareil :

        $sudo fdisk /dev/sdc


4. Lorsque vous y êtes invité, tapez **n** pour créer une nouvelle partition.


    ![Créer des appareils](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Lorsque vous y êtes invité, tapez **p** pour rendre la partition la partition principale. Tapez **1** pour faciliter la première partition, puis appuyez sur ENTRÉE pour accepter la valeur par défaut pour les graphiques à cylindres. Sur certains systèmes, il peut afficher les valeurs par défaut de la première et les dernières secteurs, au lieu du cylindre. Vous pouvez choisir d’accepter ces paramètres par défaut.


    ![Créer partition](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Tapez **p** pour voir les détails sur le disque est en cours partition.


    ![Informations sur la liste disque](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Tapez **w** pour écrire les paramètres pour le disque.


    ![Enregistrer les modifications de disque](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. Vous pouvez maintenant créer le système de fichiers sur la nouvelle partition. Ajouter le numéro de partition à l’ID du périphérique (dans l’exemple suivant `/dev/sdc1`). L’exemple suivant crée une partition ext4 sur /dev/sdc1 :

        # sudo mkfs -t ext4 /dev/sdc1

    ![Créer le système de fichiers](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] Systèmes SuSE Linux Enterprise 11 uniquement en charge l’accès en lecture seule pour les systèmes de fichiers ext4. Pour ces systèmes, il est recommandé pour mettre en forme le nouveau système de fichiers en tant qu’ext3 plutôt qu’ext4.


9. Créez un répertoire monter le nouveau système de fichiers, comme suit :

        # sudo mkdir /datadrive


10. Vous pouvez enfin monter le lecteur, comme suit :

        # sudo mount /dev/sdc1 /datadrive

    Le disque de données est maintenant prêt à utiliser comme **/datadrive**.
    
    ![Créer le répertoire et monter le disque](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Ajouter le nouveau lecteur à/etc/fstab :

    Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, il doit être ajouté au fichier/etc/fstab. En outre, il est vivement recommandé que l’UUID (identificateur Unique universel) est utilisé dans/etc/fstab pour faire référence au lecteur au lieu de simplement le nom du périphérique (c'est-à-dire /dev/sdc1). À l’aide de l’UUID évite le disque incorrect montage vers un emplacement donné si le système d’exploitation détecte une erreur de disque lors du démarrage et aucun disque données restant puis assignée ces ID de l’équipement. Pour rechercher l’UUID du nouveau lecteur, vous pouvez utiliser l’utilitaire **blkid** :

        # sudo -i blkid

    Le résultat est semblable à ce qui suit :

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] Incorrectement modification du **fichier/etc/fstab** peut entraîner un système initialisé. Cas de doute, reportez-vous à la documentation de la distribution pour plus d’informations sur la façon de modifier correctement ce fichier. Il est également recommandé qu’une sauvegarde du fichier/etc/fstab est créée avant de le modifier.

    Ensuite, ouvrez le **fichier/etc/fstab** dans un éditeur de texte :

        # sudo vi /etc/fstab

    Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** qui a été créé dans les étapes précédentes et le point de montage **/datadrive**. Ajoutez la ligne suivante à la fin du **fichier/etc/fstab** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    Ou, sur les systèmes basés sur SuSE Linux vous devrez peut-être utiliser un format légèrement différent :

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] La `nofail` option garantit que la machine virtuelle démarre même si le système de fichiers est endommagé ou le disque n’existe pas lors du démarrage. Sans cette option, vous pouvez rencontrer comportement comme décrit dans [Impossible SSH de machine virtuelle Linux en raison des erreurs FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Vous pouvez à présent tester que le système de fichiers est correctement chargé par démontage et de réinstaller le système de fichiers, c'est-à-dire point de montage à l’aide de l’exemple `/datadrive` créé dans les étapes précédentes :

        # sudo umount /datadrive
        # sudo mount /datadrive

    Si la `mount` commande génère une erreur, vérifiez le fichier/etc/fstab pour la syntaxe correcte. Si des lecteurs de données ou des partitions sont créées, entrez-les dans/etc/fstab séparément également.

    Rendre le lecteur accessible en écriture à l’aide de cette commande :

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Suppression par la suite d’un disque de données sans modifier fstab peut entraîner la machine virtuelle pour ne parviennent pas à démarrer. S’il s’agit d’une occurrence courantes, la plupart des versions fournissent la `nofail` et/ou `nobootwait` options fstab qui permettent à un système pour qu’il démarre même si le disque ne parvient pas à monter au démarrage. Consultez la documentation de votre distribution pour plus d’informations sur ces paramètres.

### <a name="trimunmap-support-for-linux-in-azure"></a>Prise en charge TRIM/annuler le mappage de Linux dans Azure
Certains noyaux Linux prennent en charge les opérations d’ajustement/annuler le mappage pour annuler les bloc non utilisés sur le disque. Ces opérations sont principalement dans le stockage standard pour informer Azure que pages supprimées ne sont plus valide et peut être supprimé. En supprimant pages peut enregistrer coût si vous créez des fichiers volumineux et supprimez.

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
Vous pouvez en savoir plus sur l’utilisation de votre Linux VM dans les articles suivants :

- [Comment se connecter à une machine virtuelle Linux en cours d’exécution][Logon]

- [La dissociation d’un disque à partir d’une machine virtuelle Linux](virtual-machines-linux-classic-detach-disk.md)

- [À l’aide de l’infrastructure du langage commun Azure avec le modèle de déploiement classique](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
