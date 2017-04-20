<properties
    pageTitle="Résoudre les problèmes de suppression des comptes de stockage Azure, conteneurs ou disques durs virtuels dans un déploiement classique | Microsoft Azure"
    description="Résoudre les problèmes de suppression des comptes de stockage Azure, conteneurs ou disques durs virtuels dans un déploiement classique"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Résoudre les problèmes de suppression des comptes de stockage Azure, conteneurs ou disques durs virtuels dans un déploiement classique

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Vous pouvez recevoir des erreurs lorsque vous tentez de supprimer le compte de stockage Azure, conteneur ou disque dur virtuel dans le [portail Azure](https://portal.azure.com/) ou du [portail classique Azure](https://manage.windowsazure.com/). Les problèmes peuvent être dû aux cas suivants :

-   Si vous supprimez une machine virtuelle, le disque et le disque dur virtuel ne sont pas supprimé automatiquement. Qui peut être la raison de l’échec de suppression du compte de stockage. Nous ne pas supprimer le disque afin que vous pouvez utiliser le disque pour monter un autre ordinateur virtuel.

-   Il est toujours une location sur un disque ou blob associée le disque.

Si votre problème Azure n’est pas traité dans cet article, consultez les forums Azure sur [MSDN et le débordement de pile](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou @AzureSupport sur Twitter. En outre, vous pouvez créer une demande de support Azure en sélectionnant **obtenir une assistance technique** sur le site [Azure prend en charge](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptômes

La section suivante répertorie les erreurs courantes que vous pouvez recevoir lorsque vous tentez de supprimer les comptes de stockage Azure, les conteneurs ou disques durs virtuels.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scénario 1 : Impossible de supprimer un compte de stockage

Lorsque vous naviguez sur le compte de stockage dans le [portail Azure](https://portal.azure.com/) ou [Azure portal classique](https://manage.windowsazure.com/) et sélectionnez **Supprimer**, vous peut s’afficher le message d’erreur suivant :

*Compte de stockage StorageAccountName contient des Images de machine virtuelle. Vérifiez que ces Images machine virtuelle sont supprimées avant de supprimer ce compte de stockage.*

Vous pouvez également voir cette erreur :

**Sur le Azure portail**:

*Impossible de supprimer le compte de stockage < machine virtuelle stockage-nom de compte >. Impossible de supprimer le compte de stockage < machine virtuelle stockage-nom de compte > : « le compte de stockage < machine virtuelle stockage-nom de compte > comporte certains actives ou les images et/ou disques. Vous assurer ces images et/ou disques sont supprimés avant de supprimer ce compte de stockage. ».*

**Sur le Azure classique portail**:

*Compte de stockage < machine virtuelle stockage-nom de compte > comporte certains actives ou les images et/ou disques, par exemple, xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Vérifiez que ces images et/ou disques sont supprimés avant de supprimer ce compte de stockage.*

Ou

**Sur le Azure portail**:

Compte de stockage *< machine virtuelle stockage-nom de compte > comporte 1 conteneur (s) ayant une image active et/ou les objets de disque. Vous assurer ces objets sont supprimés à partir du référentiel image avant de supprimer ce compte de stockage*.

**Sur le Azure classique portail**:

Compte soumettre le stockage a échoué *< machine virtuelle stockage-nom de compte > a 1 conteneur (s) ayant une image active et/ou les objets de disque. Vérifiez que ces objets sont supprimés du référentiel image avant de supprimer ce compte de stockage. Lorsque vous tentez de supprimer un compte de stockage et il y a toujours actives disques associés, vous voyez un message vous invitant à disques active doivent être supprimées*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scénario 2 : Impossible de supprimer un conteneur

Lorsque vous tentez de supprimer le conteneur de stockage, l’erreur suivante peut s’afficher :

*N’a pas pu supprimer le conteneur de stockage <container name>. Erreur : « une location est actuellement sur le conteneur et sans code location a été spécifié dans la demande*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scénario 3 : Impossible de supprimer un disque dur virtuel

Après avoir supprimé une machine virtuelle, puis tentez de supprimer les objets BLOB disques durs virtuels associés, vous pouvez recevoir le message suivant :

*Impossible de supprimer des objets blob ' chemin d’accès/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erreur : « une location est actuellement sur le blob et sans code location a été spécifié dans la demande.*

## <a name="solution"></a>Solution
Pour résoudre les problèmes courants, essayez la méthode suivante :

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Étape 1 : Supprimer les disques du système d’exploitation qui empêchent la suppression du compte de stockage, du conteneur ou du disque dur virtuel

1. Basculer vers le [portail classique Azure](https://manage.windowsazure.com/).
2. Sélectionnez **MACHINE virtuelle** > **disques**.

    ![Image de disques sur machines virtuelles sur Azure portal classique.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Recherchez les disques qui sont associés au compte de stockage, conteneur ou disque dur virtuel que vous voulez supprimer. Lorsque vous vérifiez l’emplacement du disque, vous trouverez du compte de stockage associé, le conteneur ou le disque dur virtuel.

    ![Image qui affiche des informations sur l’emplacement des disques sur Azure portal classique](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Vérifiez qu’aucune machine virtuelle n’est répertorié dans le champ **joint à** des disques, puis supprimez les disques.

    > [AZURE.NOTE] Si un disque est associé à une machine virtuelle, vous ne serez pas en mesure de le supprimer. Disques sont détachés asynchrone à partir d’un ordinateur virtuel supprimé. Il peut prendre quelques minutes après la suppression de ce champ libérer de la machine virtuelle.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Étape 2 : Supprimer toutes les Images de machine virtuelle qui empêchent la suppression du compte de stockage ou du conteneur

1. Basculer vers le [portail classique Azure](https://manage.windowsazure.com/).
2. Sélectionnez **MACHINE virtuelle** > **IMAGES**, puis supprimez les images qui sont associés au compte de stockage, conteneur ou disque dur virtuel.

    Ensuite, essayez de supprimer le compte de stockage, le conteneur ou le disque dur virtuel à nouveau.

> [AZURE.WARNING] N’oubliez pas de sauvegarder des éléments de que votre choix à enregistrer avant de supprimer le compte. Il n’est pas possible de restaurer un compte de stockage supprimés ou récupérer un des contenus qu’elle contenait avant la suppression. Ceci est également vrai pour toutes les ressources dans le compte : lorsque vous supprimez un disque dur virtuel, blob, table, file d’attente ou un fichier, il est définitivement supprimé. Assurez-vous que la ressource n’est pas en cours d’utilisation.

## <a name="about-the-stopped-deallocated-status"></a>À propos de l’état arrêt (libéré)

Machines virtuelles qui ont été créées dans le modèle de déploiement classique et qui ont été conservés a l’état **arrêt (libéré)** sur le [portail Azure](https://portal.azure.com/) ou [Azure portal classique](https://manage.windowsazure.com/).

**Portail classique azure**:

![Arrêté (Deallocated) état pour les machines virtuelles sur le portail Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Portail azure**:

![Arrêté (désalloué) état pour les machines virtuelles sur Azure portal classique.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Un état « Arrêt (libéré) » libère les ressources informatiques, tels que le processeur, la mémoire et le réseau. Les disques, cependant, sont toujours conservés afin que vous pouvez rapidement recréer la machine virtuelle si nécessaire. Ces disques sont créés en haut des disques durs virtuels, qui sont sauvegardées par le stockage Azure. Le compte de stockage comporte ces disques durs virtuels, et les disques ont location sur ces disques durs virtuels.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
- [Comment rompre la location verrouillée de stockage blob de Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
