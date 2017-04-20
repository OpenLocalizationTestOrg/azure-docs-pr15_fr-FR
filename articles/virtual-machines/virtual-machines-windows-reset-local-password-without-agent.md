<properties
   pageTitle="Réinitialiser un mot de passe Windows local lorsque l’agent Azure invité n’est pas installé | Microsoft Azure"
   description="Comment réinitialiser le mot de passe d’un compte d’utilisateur Windows local lorsque l’agent Azure invité n’est pas installé ou qu’il fonctionne sur un ordinateur virtuel"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Comment réinitialiser le mot de passe Windows local pour machine virtuelle Azure
Vous pouvez réinitialiser le mot de passe Windows local d’un ordinateur virtuel dans Azure en utilisant que le [portail Azure ou PowerShell Azure](virtual-machines-windows-reset-rdp.md) fourni que l’agent Azure invité est installé. Cette méthode est le principal moyen de réinitialiser un mot de passe pour une machine virtuelle Azure. Si vous rencontrez des problèmes avec l’agent invité Azure ne répond pas, ou ignore installer après avoir téléchargé une image personnalisée, vous pouvez réinitialiser manuellement un mot de passe Windows. Cet article décrit comment réinitialiser un mot de passe du compte local en joignant le disque virtuel source du système d’exploitation à un autre ordinateur virtuel. 

> [AZURE.WARNING] N’utilisez ce processus en dernier recours. Toujours essayer de réinitialiser un mot de passe à l’aide [portail Azure ou PowerShell Azure](virtual-machines-windows-reset-rdp.md) tout d’abord.


## <a name="overview-of-the-process"></a>Vue d’ensemble du processus
Les étapes principales pour exécuter un local du mot de passe pour un ordinateur virtuel Windows Azure lorsqu’il n’y a aucun accès à l’agent Azure invité est la suivante :

- Supprimer la source de machine virtuelle. Les disques virtuels sont conservés.
- Joindre disque du système d’exploitation de l’ordinateur source à un autre ordinateur virtuel au sein de votre abonnement Azure. Cet ordinateur virtuel est appelé la machine virtuelle dépannage.
- À l’aide de la machine virtuelle dépannage, créer des fichiers de configuration de la sur du système d’exploitation disque l’ordinateur source.
- Détacher le disque du système d’exploitation de l’ordinateur à partir de la machine virtuelle dépannage.
- Utiliser un modèle de gestionnaire de ressources pour créer une machine virtuelle, à l’aide du disque virtuel d’origine.
- Lorsque la nouvelle machine virtuelle démarre, les fichiers de configuration que vous créez mettre à jour le mot de passe de l’utilisateur requis.


## <a name="detailed-steps"></a>Procédure détaillée
Toujours essayer de réinitialiser un mot de passe à l’aide [portail Azure ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) avant d’essayer les étapes suivantes. Vérifiez que vous disposez d’une sauvegarde de votre ordinateur virtuel avant de commencer. 

1. Supprimer la machine virtuelle concernée portail Azure. La suppression de la machine virtuelle supprime uniquement les métadonnées, la référence de la machine virtuelle dans Azure. Les disques virtuels sont conservées lors de la suppression de la machine virtuelle :

    - Sélectionnez la machine virtuelle dans le portail Azure, cliquez sur *Supprimer*:

    ![Supprimer la machine virtuelle existant](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Joindre disque du système d’exploitation de l’ordinateur source à la machine virtuelle dépannage. La machine virtuelle dépannage doit se trouver dans la même région en tant que disque du système d’exploitation de l’ordinateur source (par exemple `West US`) :

    - Sélectionnez la machine virtuelle résolution des problèmes dans le portail Azure. Cliquez sur *disques* | *joindre existant*:

    ![Joindre disque existant](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Sélectionnez *Fichier de disque dur virtuel* , puis sélectionnez le compte de stockage qui contient votre source machine virtuelle :

    ![Sélectionnez le compte de stockage](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Sélectionnez le conteneur source. Le conteneur source est généralement *disques durs virtuels*:

    ![Sélectionnez le conteneur de stockage](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Sélectionnez le disque dur virtuel du système d’exploitation pour joindre. Cliquez sur *Sélectionner* pour terminer le processus :

    ![Sélectionnez le disque virtuel source](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Se connecter à la machine virtuelle dépannage à l’aide de bureau à distance et vérifiez que du système d’exploitation disque l’ordinateur source est visible :

    - Sélectionnez la machine virtuelle résolution des problèmes dans le portail Azure et cliquez sur *se connecter*.
    - Ouvrez le fichier RDP qui télécharge. Entrez le nom d’utilisateur et mot de passe de la machine virtuelle dépannage.
    - Dans l’Explorateur de fichiers, recherchez le disque de données que vous avez joint. Si la source dur de machine virtuelle est le disque uniquement les données lié à la résolution des problèmes machine virtuelle, il doit être le lecteur f : :

    ![Afficher les données joints disque](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Créer `gpt.ini` dans `\Windows\System32\GroupPolicy` sur le lecteur de l’ordinateur source (si gpt.ini existe, attribuez-lui gpt.ini.bak) :

    > [AZURE.WARNING] Assurez-vous que vous ne créez pas accidentellement de fichiers suivants dans C:\Windows, le lecteur système d’exploitation pour la machine virtuelle dépannage. Créer les fichiers suivants dans le lecteur du système d’exploitation pour votre ordinateur virtuel qui est joint sous forme d’un disque de données source.

    - Ajoutez les lignes suivantes dans le `gpt.ini` fichier que vous avez créé :

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Créer gpt.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Créer `scripts.ini` dans `\Windows\System32\GroupPolicy\Machine\Scripts`. Vérifiez que les dossiers masqués sont affichés. Si nécessaire, créez le `Machine` ou `Scripts` dossiers.

    - Ajoutez les lignes suivantes du `scripts.ini` fichier que vous avez créé :

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Créer scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Créer `FixAzureVM.cmd` dans `\Windows\System32` avec le contenu suivant, en remplaçant `<username>` et `<newpassword>` avec vos propres valeurs :

    ```
    NET USER <username> <newpassword>
    ```

    ![Créer FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Vous devez respecter les exigences de complexité de mot de passe configuré pour votre ordinateur virtuel lorsque vous définissez le nouveau mot de passe.

7. Dans le portail Azure, détacher le disque à partir de la machine virtuelle résolution des problèmes :

    - Sélectionnez la machine virtuelle résolution des problèmes dans le portail Azure, cliquez sur *disques*.
    - Sélectionnez le disque de données pièce joint à l’étape 2, cliquez sur *Détacher*:

    ![Détacher disque](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Avant de créer une machine virtuelle, obtenir l’URI sur votre disque OS source :

    - Sélectionnez le compte de stockage dans le portail Azure, cliquez sur *des objets BLOB*.
    - Sélectionnez le conteneur. Le conteneur source est généralement *disques durs virtuels*:

    ![Sélectionnez blob de compte de stockage](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Sélectionnez votre source de disque dur virtuel du système d’exploitation machine virtuelle et cliquez sur le bouton *Copier* en regard du nom *d’URL* :

    ![Copier une disquette URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Créer une machine virtuelle à partir du disque du système d’exploitation de l’ordinateur source :

    - Utilisez [ce modèle Azure le Gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) pour créer une machine virtuelle à partir d’un disque dur virtuel spécialisé. Cliquez sur le `Deploy to Azure` bouton pour ouvrir le portail Azure avec les détails basé sur un modèle remplies pour vous.
    - Si vous voulez conserver tous les paramètres de la machine virtuelle, sélectionnez *Modifier le modèle* à fournir votre VNet existant, sous-réseau, carte réseau ou adresse IP publique.
    - Dans la `OSDISKVHDURI` zone de texte paramètre, coller obtenir l’URI de votre disque dur virtuel source à l’étape précédente :

    ![Créer une machine virtuelle à partir de modèle](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Une fois que la nouvelle machine virtuelle est en cours d’exécution, se connecter à la machine virtuelle à l’aide de bureau à distance avec le nouveau mot de passe que vous avez spécifié dans le `FixAzureVM.cmd` script.

11. À partir de votre session distante à la nouvelle machine virtuelle, supprimez les fichiers suivants pour nettoyer l’environnement :

    - À partir de %windir%\System32
        - supprimer FixAzureVM.cmd
    - À partir de %windir%\System32\GroupPolicy\Machine\
        - supprimer scripts.ini
    - À partir de %windir%\System32\GroupPolicy
        - supprimer gpt.ini (si gpt.ini précèdent, et que vous avez renommé gpt.ini.bak, renommer le fichier .bak revient à gpt.ini)

## <a name="next-steps"></a>Étapes suivantes
Si vous ne pouvez pas toujours vous connecter à l’aide de bureau à distance, consultez le [guide de dépannage RDP](virtual-machines-windows-troubleshoot-rdp-connection.md). Le [guide de dépannage détaillé RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md) examine méthodes plutôt que des étapes de dépannage. Vous pouvez également [Ouvrir une demande de support Azure](https://azure.microsoft.com/support/options/) pour obtenir une assistance pratique.