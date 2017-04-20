<properties
    pageTitle="Comment créer une image du modèle personnalisé pour Azure RemoteApp | Microsoft Azure"
    description="Apprenez à créer une image du modèle personnalisé pour Azure RemoteApp. Vous pouvez utiliser ce modèle avec un environnement hybride ou le cloud collection."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Comment créer une image du modèle personnalisé pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure utilise une image du modèle Windows Server 2012 R2 pour héberger tous les programmes que vous voulez partager avec d’autres utilisateurs. Pour créer une image du modèle RemoteApp personnalisé, vous pouvez commencer avec une image existante ou créez-en une. 


> [AZURE.TIP] Saviez-vous que vous pouvez créer une image à partir d’une machine virtuelle Azure ? Histoire vraie et il permet de réduire la durée pendant laquelle il prend pour importer l’image. Consultez les étapes décrites [ici](remoteapp-image-on-azurevm.md).

La configuration requise pour l’image qui peuvent être téléchargés pour une utilisation avec Azure RemoteApp est :


- La taille d’image doit être un multiple de mégaoctets. Si vous essayez de télécharger une image qui n’est pas un multiple exact, le téléchargement échoue.
- La taille d’image doit être 127 Go ou plus petit.
- Il doit se trouver sur un fichier de disque dur virtuel (fichiers VHDX [Hyper-V disques durs] ne sont pas pris en charge).
- Le disque dur virtuel ne doit pas être une machine virtuelle génération 2.
- Le disque dur virtuel peut être taille fixe ou dynamique. Un disque dur virtuel dynamique est recommandé, car il prend moins de temps pour la télécharger vers Azure qu’un fichier de disque dur virtuel de taille fixe.
- Le disque doit être initialisé à l’aide de l’enregistrement (démarrage principal) partition style. Le style de partition GUID partition GPT (table) n’est pas pris en charge.
- Le disque dur virtuel doit contenir une seule installation de Windows Server 2012 R2. Il peut contenir plusieurs volumes, mais un seul qui contient une installation de Windows.
- Le rôle Remote Desktop Session hôte (RDSH) et la fonctionnalité Experience utilisateur doivent être installés.
- Le rôle de l’agent de connexion Bureau à distance doit *pas* être installé.
- Le fichier système de cryptage doit être désactivé.
- L’image doit être préparée avec Sysprep en utilisant les paramètres **/oobe /generalize /shutdown** (ne pas utiliser le paramètre **/mode:vm** ).
- Téléchargement de votre disque dur virtuel à partir d’une chaîne d’instantanés n’est pas pris en charge.


**Avant de commencer**

Vous devez effectuer les opérations suivantes avant de créer le service :

- [S’inscrire](https://azure.microsoft.com/services/remoteapp/) pour RemoteApp.
- Créer un compte d’utilisateur dans Active Directory à utiliser comme compte de service RemoteApp. Restreindre les autorisations pour ce compte afin qu’il peut uniquement joindre des ordinateurs au domaine. Pour plus d’informations, consultez [Configurer Azure Active Directory pour RemoteApp](remoteapp-ad.md) .
- Recueillir des informations sur votre réseau local : adresse IP d’informations et des détails de l’appareil VPN.
- Installez le module [Azure PowerShell](../powershell-install-configure.md) .
- Collecter les informations sur les utilisateurs que vous voulez accorder l’accès à. Cela peut être soit des informations de compte de travail Active Directory pour les utilisateurs ou des informations de compte Microsoft.



## <a name="create-a-template-image"></a>Créer une image du modèle ##

Il s’agit des étapes de haut niveau pour créer une nouvelle image du modèle à partir de zéro :

1.  Recherchez une image DVD mise à jour de Windows Server 2012 R2 ou ISO.
2.  Créer un fichier de disque dur virtuel.
4.  Installer Windows Server 2012 R2.
5.  Installez le rôle Remote Desktop Session hôte (RDSH) et la fonctionnalité Experience utilisateur.
6.  Installer des fonctionnalités supplémentaires requises par vos applications.
7.  Installer et configurer vos applications. Pour faciliter le partage d’applications, ajoutez les applications ou les programmes que vous voulez partager dans le menu **Démarrer** de l’image, en particulier en **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.
8.  Effectuer toutes les configurations Windows supplémentaires requises par vos applications.
9.  Désactiver le chiffrement système de fichiers ().
10. **REQUIRED :** Accédez à Windows Update et installez toutes les mises à jour importantes.
9.  SYSPREP l’image.

Les étapes détaillées pour la création d’une nouvelle image sont :

1.  Recherchez une image DVD mise à jour de Windows Server 2012 R2 ou ISO.
2.  Créer un fichier de disque dur virtuel à l’aide de gestion des disques.
    1.  Lancez gestion des disques (diskmgmt.msc).
    2.  Créer un disque dur virtuel dynamique de 40 Go ou plus taille. (Évaluer la quantité d’espace nécessaire pour Windows, vos applications et les personnalisations. Windows Server avec le rôle RDSH et la fonctionnalité Experience utilisateur installé nécessite environ 10 Go d’espace).
        1.  Cliquez sur **Action > Créer disque dur virtuel**.
        2.  Spécifiez l’emplacement, la taille et le format de disque dur virtuel. Sélectionnez **à extension dynamique**, puis cliquez sur **OK**.

            Il s’exécute pendant quelques secondes. Lors de la création de disque dur virtuel est terminée, vous devez voir un nouveau disque sans aucune lettre de lecteur et dans l’état « Non initialisé » dans la console de gestion de disque.

        - Cliquez sur le disque (pas l’espace non alloué), puis cliquez sur **Disque initialisation**. Sélectionnez le style de partition **MBR** (enregistrement de démarrage principal), puis cliquez sur **OK**.
        - Créer un nouveau volume : avec le bouton droit de l’espace non alloué, puis cliquez sur **Nouveau Volume Simple**. Vous pouvez accepter les paramètres par défaut dans l’Assistant, mais vérifiez que vous affectez une lettre de lecteur pour éviter d’éventuels problèmes lorsque vous téléchargez l’image du modèle.
        - Cliquez sur le disque, puis cliquez sur **Détacher le disque dur virtuel**.





1. Installer Windows Server 2012 R2 :
    1. Créer un nouvel ordinateur virtuel. Suivez l’Assistant Nouvel ordinateur virtuel Hyper-V gestionnaire ou Client Hyper-V.
        1. Dans la page spécifier une génération, sélectionnez **génération 1**.
        2. Dans la page connecter un disque dur virtuel, sélectionnez **utiliser un disque dur virtuel existant**, puis naviguez vers le disque dur virtuel que vous avez créé à l’étape précédente.
        2. Dans la page Options d’Installation, sélectionnez **installer un système d’exploitation à partir d’un démarrage CD/ce dernier**, puis l’emplacement de votre support d’installation de Windows Server 2012 R2.
        3. Sélectionnez autres options de l’Assistant permettant d’installer Windows et vos applications. Fin de l’Assistant.
    2.  Une fois l’Assistant terminé, modifiez les paramètres de la machine virtuelle et apportez les modifications nécessaires à l’installation de Windows et vos programmes, telles que le nombre de processeurs virtuels, puis cliquez sur **OK**.
    4.  Se connecter à la machine virtuelle et installer Windows Server 2012 R2.
1. Installer le rôle Remote Desktop Session hôte (RDSH) et la fonctionnalité Experience utilisateur :
    1. Lancez le Gestionnaire de serveur.
    2. Cliquez sur **Ajouter des rôles et les fonctionnalités** dans l’écran d’accueil ou dans le menu **Gérer** .
    3. Dans la page avant de commencer, cliquez sur **suivant** .
    4. Sélectionnez **l’installation basée sur une fonctionnalité ou rôle**, puis cliquez sur **suivant**.
    5. Sélectionnez l’ordinateur local dans la liste, puis cliquez sur **suivant**.
    6. Sélectionnez les **Services Bureau à distance**, puis cliquez sur **suivant**.
    7. Développez les **Interfaces utilisateur et l’Infrastructure** et sélectionnez **Experience utilisateur**.
    8. Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **suivant**.
    9. Dans la page Services Bureau à distance, cliquez sur **suivant**.
    10. Cliquez sur **hôte de Session Bureau à distance**.
    11. Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **suivant**.
    12. Dans la page de sélections confirmer l’installation, sélectionnez **redémarrer le serveur de destination automatiquement si nécessaire**, puis cliquez sur **Oui** au message d’avertissement redémarrer.
    13. Cliquez sur **installer**. L’ordinateur va redémarrer.
1.  Installer des fonctionnalités supplémentaires requises par vos applications, telles que .NET Framework 3.5. Pour installer les fonctionnalités, exécutez l’Assistant de fonctionnalités et ajouter des rôles.
7.  Installer et configurer les programmes et les applications que vous voulez publier via RemoteApp.

>[AZURE.IMPORTANT]
>
>Installez le rôle RDSH avant d’installer des applications pour vous assurer que des problèmes de compatibilité des applications sont mis en évidence avant l’image est téléchargé dans RemoteApp.
>
>Assurez-vous qu’un raccourci vers votre application (fichier**.lnk** ) apparaît dans le menu **Démarrer** pour tous les utilisateurs (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs). Assurez-vous également que l’icône que s’affiche dans le menu **Démarrer** est ce que vous souhaitez afficher aux utilisateurs. Dans le cas contraire, modifiez-le. (Vous *n’avez* pour ajouter l’application jusqu’au début menu, mais il facilite publier l’application dans RemoteApp. Dans le cas contraire, vous devez indiquer le chemin d’installation pour l’application lors de la publication de l’application.)


8.  Effectuer toutes les configurations Windows supplémentaires requises par vos applications.
9.  Désactiver le chiffrement système de fichiers (). Dans une fenêtre de commande avec élévation de privilèges, exécutez la commande suivante :

        Fsutil behavior set disableencryption 1

    Par ailleurs, vous pouvez définir ou ajouter la valeur DWORD suivante dans le Registre :

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Si vous créez votre image à l’intérieur d’une machine virtuelle Azure, renommer la ** \%windir%\Panther\Unattend.xml** de fichiers, comme cela se bloque le script de téléchargement utilisé ultérieurement de fonctionner. Modifier le nom de ce fichier à Unattend.old afin que vous aurez toujours le fichier au cas où vous souhaitez récupérer votre déploiement.
10. Accédez à Windows Update et installez toutes les mises à jour importantes. Vous devrez peut-être exécuter Windows Update à plusieurs reprises pour obtenir les mises à jour. (Parfois vous installez une mise à jour, et cette mise à jour lui-même requiert une mise à jour).
10. SYSPREP l’image. À une invite de commandes avec élévation de privilèges, exécutez la commande suivante :

    **C:\Windows\System32\sysprep\sysprep.exe /generalize /shutdown /oobe**

    **Remarque :** N’utilisez pas le commutateur **/mode:vm** de la commande SYSPREP même s’il s’agit d’une machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes ##
Maintenant que vous avez votre image du modèle personnalisé, vous devez télécharger cette image dans votre collection de sites RemoteApp. Utiliser les informations dans les articles suivants pour créer votre collection de sites :


- [Comment créer une collection hybride de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Comment créer une collection de cloud de RemoteApp](remoteapp-create-cloud-deployment.md)
 