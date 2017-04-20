<properties
    pageTitle="Utiliser des instantanés incrémentielles pour la sauvegarde et restauration des machines virtuelles Azure | Microsoft Azure"
    description="Créer une solution personnalisée pour la sauvegarde et restauration de vos disques Azure machine virtuelle à l’aide d’instantanés incrémentiels."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Sauvegarder disques Azure machine virtuelle avec instantanés incrémentiels

## <a name="overview"></a>Vue d’ensemble

Stockage Azure permet de prendre des instantanés des objets BLOB. Instantanés capturent l’état d’objets blob à ce stade. Dans cet article, nous décrira un scénario de comment vous pouvez gérer des sauvegardes de disques des machines virtuelles à l’aide des instantanés. Vous pouvez utiliser cette méthode lorsque vous choisissez de ne pas utiliser Azure sauvegarde et restauration Service et que vous souhaitez créer une stratégie de sauvegarde personnalisée pour vos disques machine virtuelle.

Machine virtuelle Azure disques sont stockés en tant que des objets BLOB de page dans le stockage Azure. Étant donné que nous parlons de stratégie de sauvegarde de disques machine virtuelle dans cet article, nous doit se référer à des instantanés dans le contexte des objets BLOB de page. Pour plus d’informations sur les instantanés, reportez-vous à la [Création d’un instantané d’un objet Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>Qu’est un instantané ?

Un instantané blob est une version en lecture seule d’un blob qui est capturé à un point dans le temps. Une fois un instantané a été créé, il peut être lue, copié, ou supprimé, mais pas modifiée. Instantanés permettent de sauvegarder un blob tel qu’il apparaît à un moment donné. Jusqu'à reste version 04/2015 / 05, vous avez la possibilité de copier des instantanés complètes. Avec la version 2015-07-08 reste et versions ultérieures, vous pouvez également copier des instantanés incrémentiels.

## <a name="full-snapshot-copy"></a>Copie complète

Instantanés peuvent être copiés vers un autre compte de stockage comme un objet blob à conserver des copies de sauvegarde de la base blob. Vous pouvez également copier un instantané via son blob de base, qui ressemble à restaurer le blob dans une version antérieure. Lorsqu’un instantané est copié à partir d’un compte de stockage à l’autre, elle occupe le même espace que le blob de page de base. Par conséquent, instantanés toute copies d’un compte de stockage à un autre seront lents et consomment également beaucoup d’espace dans le compte de stockage cible.

>[AZURE.NOTE] Si vous copiez le blob base vers une autre destination, les instantanés du blob ne sont pas copiés en même temps. De même, si vous remplacez un blob de base avec une copie, clichés associés à la base blob ne sont pas affectées et restent intactes sous nom de la base blob.

### <a name="back-up-disks-using-snapshots"></a>Sauvegarder des disques à l’aide des instantanés

Comme une stratégie de sauvegarde de vos disques machine virtuelle, vous pouvez effectuer des instantanés périodiques du blob disque ou page et les copier vers un autre compte de stockage à l’aide d’outils tels que l’opération [Copier Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou [AzCopy](storage-use-azcopy.md). Vous pouvez copier un instantané dans un blob page destination sous un autre nom. Le blob de page de destination qui en résulte est un blob page accessible en écriture et pas un instantané. Plus loin dans cet article, nous décrira étapes pour effectuer des sauvegardes de disques des machines virtuelles à l’aide des instantanés.

### <a name="restore-disks-using-snapshots"></a>Restaurer des disques à l’aide des instantanés

Lorsqu’il est temps pour restaurer une version précédente stable capturée dans un des instantanés sauvegarde votre disque, vous pouvez copier un instantané via le blob de page de base. Une fois l’instantané est promu à la page de base blob, le reste instantané, mais sa source est remplacée par une copie qui peut être à la fois lire et écrire. Plus loin dans cet article, nous décrira étapes pour restaurer une version antérieure de votre disque à partir de sa capture instantanée.

### <a name="implementing-full-snapshot-copy"></a>Mise en œuvre de copie complète

Vous pouvez mettre en œuvre une copie complète en procédant ainsi,

-   Tout d’abord, prendre un instantané de la base blob à l’aide de l’opération [d’Objets Blob instantané](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   Ensuite, copiez l’instantané dans un compte de stockage cible à l’aide [Blob de copie](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Répétez cette procédure pour conserver des copies de sauvegarde de votre base blob.

## <a name="incremental-snapshot-copy"></a>Copie instantané incrémentiel

La nouvelle fonctionnalité de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API fournit un bien meilleur moyen de sauvegarder des instantanés de vos objets BLOB de page ou de disques. L’API renvoie la liste des modifications entre le blob de base et les instantanés. Cela permet de réduire la quantité d’espace de stockage utilisé sur le compte de sauvegarde. L’API prend en charge des objets BLOB page sur stockage Premium ainsi que le stockage Standard. À l’aide de cette API, vous pouvez désormais créer des solutions de sauvegarde plus rapides et efficaces pour les machines virtuelles Azure. Il s’agit disponibles avec la version reste 2015-07-08 ou version ultérieure.

Copie instantané incrémentiel vous permet de copier à partir d’un compte de stockage à un autre la différence entre,

-   Base blob et son instantané ou
-   Les deux instantanés du blob base

Fourni que les conditions suivantes sont remplies,

- Le blob a été créé sous Jan-1-2016 ou version ultérieure.
- Le blob n’a pas été remplacé avec [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) ou [Copie Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) entre deux instantanés.


**Remarque**: cette fonctionnalité est disponible pour Premium et des objets BLOB de Page Azure Standard.

Lorsque vous avez une stratégie de sauvegarde personnalisée qui utilise des instantanés, copie des instantanés à partir d’un compte de stockage vers un autre peut être très lente et consomme beaucoup d’espace de stockage. Au lieu de copier la capture instantanée à un compte de stockage de sauvegarde, vous pouvez écrire la différence entre deux instantanés consécutives à un blob page sauvegarde. De cette façon, le temps de copie et d’espace pour stocker des sauvegardes est réduit sensiblement.

### <a name="implementing-incremental-snapshot-copy"></a>Mise en œuvre de copie instantané incrémentiel

Vous pouvez mettre en œuvre copie instantané incrémentiel en procédant ainsi,

-   Prendre un instantané de la base blob à l’aide [d’Objets Blob instantané](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copier la capture instantanée vers le compte de stockage de sauvegarde cible à l’aide [Blob de copie](https://msdn.microsoft.com/library/azure/dd894037.aspx). Il s’agit le blob page sauvegarde. Prendre un instantané de ce blob page sauvegarde et stocker dans compte de sauvegarde.
-   Prendre une autre instantané du blob base à l’aide d’objets Blob instantané.
-   Obtenir la différence entre la première et la deuxième des instantanés de base blob en utilisant [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Le nouveau paramètre **prevsnapshot** permet de spécifier la valeur de date/heure de l’instantané que vous souhaitez obtenir la différence avec. Lorsque ce paramètre est présent, la réponse reste inclura uniquement les pages qui ont été modifiées entre cible instantané et instantané précédent, y compris les pages effacer.
-   Utilisez [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) pour appliquer ces modifications au blob page sauvegarde.
-   Enfin, prendre un instantané du blob page sauvegarde et stockez-le dans le compte de stockage de sauvegarde.

Dans la section suivante, nous décrit plus en détail comment vous pouvez gérer des sauvegardes de disques à l’aide de copie instantané incrémentiel

## <a name="scenario"></a>Scénario

Dans cette section, nous décrira un scénario impliquant une stratégie de sauvegarde personnalisée pour disques des machines virtuelles à l’aide des instantanés.

Pensez à une machine virtuelle Azure DS série avec un disque de P30 de stockage premium connecté. Le disque P30 appelé *mypremiumdisk* est stocké dans un compte de stockage premium appelé *mypremiumaccount*. Un compte de stockage standard appelé *mybackupstdaccount* sera utilisé pour le stockage de la sauvegarde de *mypremiumdisk*. Nous souhaitons conserver un instantané de *mypremiumdisk* toutes les 12 heures.

Pour en savoir plus sur la création de disques et compte de stockage, reportez-vous à [comptes de stockage sur Azure](storage-create-storage-account.md).

Pour en savoir plus sur la sauvegarde des machines virtuelles Azure, reportez-vous à [sauvegardes de planifier des machines virtuelles Azure](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Étapes à suivre pour mettre à jour des sauvegardes d’un disque à l’aide d’instantanés incrémentiels

Les étapes décrites ci-dessous seront prendre des instantanés de *mypremiumdisk* et mettre à jour les sauvegardes dans *mybackupstdaccount*. La sauvegarde sera un blob page standard appelé *mybackupstdpageblob*. Le blob page sauvegarde refléteront toujours le même état que la dernière capture instantanée de *mypremiumdisk*.

1.  Commencez par créer le blob page sauvegarde de votre disque de stockage premium. Pour ce faire, prendre un instantané de *mypremiumdisk* appelé *mypremiumdisk_ss1*.
2.  Copiez cette capture instantanée vers mybackupstdaccount comme blob page appelé *mybackupstdpageblob*.
3.  Prendre un instantané de *mybackupstdpageblob* appelé *mybackupstdpageblob_ss1*, à l’aide [d’Objets Blob instantané](https://msdn.microsoft.com/library/azure/ee691971.aspx) et le stocker dans *mybackupstdaccount*.
4.  Au cours de la fenêtre de sauvegarde, créer un nouvel instantané de *mypremiumdisk*, dites *mypremiumdisk_ss2*et stockez-le dans *mypremiumaccount*.
5.  Obtenir les modifications incrémentielles entre les deux clichés, *mypremiumdisk_ss2* et *mypremiumdisk_ss1*, à l’aide de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) sur *mypremiumdisk_ss2* avec **prevsnapshot** paramètre défini à l’horodatage de *mypremiumdisk_ss1*. Écrire ces modifications incrémentielles dans le blob de sauvegarde page *mybackupstdpageblob* dans *mybackupstdaccount*. S’il existe des plages supprimés dans les modifications incrémentielles, ils doivent être désactivées à partir du blob page sauvegarde. Utilisez [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) pour écrire les modifications incrémentielles au blob page sauvegarde.
6.  Prendre un instantané de la page sauvegarde blob *mybackupstdpageblob*, appelé *mybackupstdpageblob_ss2*. Supprimer l' instantané précédent *mypremiumdisk_ss1* de compte de stockage premium.
7.  Répétez les étapes 4 à 6 chaque fenêtre de sauvegarde. De cette façon, vous pouvez gérer des sauvegardes de *mypremiumdisk* dans un compte de stockage standard.

![Effectuer une sauvegarde disque à l’aide d’instantanés incrémentiels](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Étapes à suivre pour restaurer un disque à partir d’instantanés

Les étapes décrites ci-dessous restaure disque premium, *mypremiumdisk* vers un instantané antérieur à partir du compte de stockage de sauvegarde *mybackupstdaccount*.

1.  Identifier le point de fois que vous souhaitez restaurer le disque premium. Supposons qu’est instantané *mybackupstdpageblob_ss2*, qui est stocké dans le compte de stockage de sauvegarde *mybackupstdaccount*.
2.  Dans mybackupstdaccount, promouvoir l' instantané *mybackupstdpageblob_ss2* comme le blob de nouvelle page de base de sauvegarde *mybackupstdpageblobrestored*.
3.  Prendre un instantané de ce blob page sauvegarde restauré, appelé *mybackupstdpageblobrestored_ss1*.
4.  Copiez les objets blob de page restauré *mybackupstdpageblobrestored* *mybackupstdaccount* à *mypremiumaccount* en tant que le disque de nouveau premium *mypremiumdiskrestored*.
5.  Prendre un instantané de *mypremiumdiskrestored*, appelé *mypremiumdiskrestored_ss1* sauvegardes incrémentielles futures.
6.  Point le service d’annuaire série machine virtuelle à la restauré disque *mypremiumdiskrestored* et détachez l' ancien *mypremiumdisk* à partir de la machine virtuelle.
7.  Commencer le processus de sauvegarde décrit dans la section précédente pour disque restauré *mypremiumdiskrestored*à l’aide de la *mybackupstdpageblobrestored* en tant que le blob page sauvegarde.

![Restaurer disque à partir d’instantanés](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la création d’instantanés d’un objet blob et planification de votre infrastructure de sauvegarde machine virtuelle en utilisant les liens ci-dessous.

- [Création d’un instantané d’un objet Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planifier votre Infrastructure de sauvegarde machine virtuelle](../backup/backup-azure-vms-introduction.md)
