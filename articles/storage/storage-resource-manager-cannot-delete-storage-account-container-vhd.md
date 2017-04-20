<properties
    pageTitle="Résolution des erreurs lorsque vous supprimez des comptes de stockage Azure, les conteneurs ou disques durs virtuels dans un déploiement du Gestionnaire de ressources | Microsoft Azure"
    description="Résolution des erreurs lorsque vous supprimez des comptes de stockage Azure, les conteneurs ou disques durs virtuels dans un déploiement du Gestionnaire de ressources"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="genli"/>

# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Résolution des erreurs lorsque vous supprimez des comptes de stockage Azure, les conteneurs ou disques durs virtuels dans un déploiement du Gestionnaire de ressources

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Vous pouvez recevoir des erreurs lorsque vous tentez de supprimer un compte de stockage Azure, un conteneur ou un disque dur virtuel (disque dur virtuel) dans le [portail Azure](https://portal.azure.com). Cet article fournit des conseils de dépannage pour vous aider à résoudre le problème dans un déploiement d’Azure le Gestionnaire de ressources.

Si cet article ne répond pas à votre problème Azure, consultez les forums Azure sur [MSDN et débordement de pile](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou @AzureSupport sur Twitter. En outre, vous pouvez créer une demande de support Azure en sélectionnant **obtenir une assistance technique** sur le site [Azure prend en charge](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptômes

### <a name="scenario-1"></a>Scénario 1

Lorsque vous tentez de supprimer un disque dur virtuel dans un compte de stockage dans un déploiement du Gestionnaire de ressources, le message d’erreur suivant s’affiche :

**Impossible de supprimer des objets blob 'vhds/BlobName.vhd'. Erreur : Il est actuellement une location sur le blob et sans code location a été spécifié dans la demande.**

Ce problème peut se produire car une machine virtuelle () a une location sur le disque dur virtuel que vous tentez de supprimer.

### <a name="scenario-2"></a>Scénario 2

Lorsque vous tentez de supprimer un conteneur dans un compte de stockage dans un déploiement du Gestionnaire de ressources, le message d’erreur suivant s’affiche :

**Impossible de supprimer stockage conteneur « disques durs virtuels ». Erreur : Il est actuellement un contrat de location sur le conteneur et sans code location a été spécifié dans la demande.**

Ce problème peut se produire, car le conteneur possède un disque dur virtuel est verrouillé dans un état Location.

### <a name="scenario-3"></a>Scénario 3

Lorsque vous tentez de supprimer un compte de stockage dans un déploiement du Gestionnaire de ressources, le message d’erreur suivant s’affiche :

**Impossible de supprimer le compte de stockage 'StorageAccountName'. Erreur : Le compte de stockage ne peuvent pas être supprimé en raison de ses objets en cours d’utilisation.**

Ce problème peut se produire, car le compte de stockage contient un disque dur virtuel qui se trouve dans l’état Location.

## <a name="solution"></a>Solution

Pour résoudre ce problème, vous devez identifier le disque dur virtuel à l’origine de l’erreur et la machine virtuelle associée. Ensuite, détacher le disque dur virtuel à partir de la machine virtuelle (pour disques de données) ou supprimer la machine virtuelle qui utilise le disque dur virtuel (pour disques du système d’exploitation). Cette action supprime la location à partir du disque dur virtuel et lui permet d’être supprimée.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Étape 1 : Identifier le problème disque dur virtuel et la machine virtuelle associée


1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **concentrateur** , sélectionnez **toutes les ressources**. Accédez au compte de stockage que vous voulez supprimer, puis sélectionnez puis **objets BLOB** > **disques durs virtuels**.

    ![Capture d’écran du portail, avec le compte de stockage et le conteneur « virtuels » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

3. Vérifiez les propriétés de chaque disque dur virtuel dans le conteneur. Recherchez le disque dur virtuel qui se trouve dans l’état **loué** . Puis, déterminer quel machine virtuelle utilise le disque dur virtuel. En règle générale, vous pouvez déterminer lequel machine virtuelle conserve le disque dur virtuel par vérification du nom de ce dernier :

    - Disques du système d’exploitation généralement suivent cette convention d’appellation : VMNameYYYYMMDDHHMMSS.vhd
    - Disques de données généralement suivent cette convention d’appellation : VMName-AAAAMMJJ-HHMMSS.vhd

    ![Capture d’écran des informations sur le conteneur dans le portail, avec le nom de la machine virtuelle, l’état de « Verrouillé » et l’état location de « Loué » mis en surbrillance](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Étape 2 : Supprimer la location à partir du disque dur virtuel

Pour supprimer la machine virtuelle qui utilise le disque dur virtuel (pour disques du système d’exploitation) :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le menu **concentrateur** , sélectionnez **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle qui contient une location sur le disque dur virtuel.
4.  Vérifiez que rien n’activement utilise la machine virtuelle, et que vous n’avez plus besoin de la machine virtuelle.
5.  En haut de la cuillère **Détails machine virtuelle** , sélectionnez **Supprimer**, puis cliquez sur **Oui** pour confirmer.
6.  La machine virtuelle doit être supprimée, mais le disque dur virtuel doit être conservé. Toutefois, le disque dur virtuel ne devant plus avoir une location dessus. Il peut prendre quelques minutes pour que la location d’être publiées. Pour vérifier que la location a été publiée, accédez à **toutes les ressources** > **Nom de compte de stockage** > **des objets BLOB** > **disques durs virtuels**. Dans le volet **Propriétés Blob** , la valeur **d’État** doit être **déverrouillée**.

Pour détacher le disque dur virtuel à partir de la machine virtuelle qui utilise (pour disques de données) :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le menu **concentrateur** , sélectionnez **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle qui contient une location sur le disque dur virtuel.
4.  Sélectionnez **disques** sur la carte de **Détails de la machine virtuelle** .
5.  Sélectionnez le disque de données qui contient une location sur le disque dur virtuel. Vous pouvez déterminer lequel disque dur virtuel est joint dans le disque en vérifiant l’URL de ce dernier.
6.  Déterminez avec s’assurer que rien n’activement utilise le disque de données.
7.  Cliquez sur **Détacher** sur la carte de **détails d’un disque** .
8.  Le disque doit maintenant être détaché de la machine virtuelle, et le disque dur virtuel ne devant plus avoir une location dessus. Il peut prendre quelques minutes pour que la location d’être publiées. Pour vérifier que la location a été publiée, accédez à **toutes les ressources** > **Nom de compte de stockage** > **des objets BLOB** > **disques durs virtuels**. Dans le volet **Propriétés Blob** , la valeur **d’État** doit être **déverrouillée**.

## <a name="what-is-a-lease"></a>Qu’est une location ?

Un contrat de location est un verrou pouvant être utilisées pour contrôler l’accès à un blob (par exemple, un disque dur virtuel). Lorsqu’un blob allouée, seuls les propriétaires du contrat de location peuvent accéder au blob. Un contrat de location est important pour les raisons suivantes :

-   Il empêche la corruption des données si plusieurs propriétaires essaient d’écrire sur la même partie du blob en même temps.
-   Elle empêche le blob d’être supprimé si quelque chose est activement à l’utiliser (par exemple, une machine virtuelle).
-   Il empêche le compte de stockage d’être supprimé si quelque chose est activement à l’utiliser (par exemple, une machine virtuelle).



## <a name="next-steps"></a>Étapes suivantes

- [Supprimer un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
- [Comment rompre la location verrouillée de stockage blob de Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
