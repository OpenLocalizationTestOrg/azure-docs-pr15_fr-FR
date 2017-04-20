<properties
   pageTitle="Créer et télécharger une image FreeBSD VM | Microsoft Azure"
   description="Découvrez comment créer et télécharger un disque dur virtuel (disque dur virtuel) qui contient le système d’exploitation FreeBSD pour créer une machine virtuelle Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Créer et télécharger un VHD FreeBSD vers Azure

Cet article vous explique comment créer et télécharger un disque dur virtuel (disque dur virtuel) qui contient le système d’exploitation FreeBSD. Après que l’avoir téléchargée, vous pouvez l’utiliser comme votre propre image pour créer une machine virtuelle () dans Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez les éléments suivants :

- **Abonnement un Azure**--si vous n’avez pas un compte, vous pouvez créer une en quelques minutes. Si vous avez un abonnement MSDN, voir [bancaire mensuel Azure pour les abonnés Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, découvrez comment [créer un compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).  

- **Outils de PowerShell azure**--The PowerShell Azure module doit être installé et configuré pour utiliser votre abonnement Azure. Pour télécharger le module, voir [Azure téléchargements](https://azure.microsoft.com/downloads/). Un didacticiel décrivant comment installer et configurer le module est disponible ici. Utiliser l’applet de commande [Azure téléchargements](https://azure.microsoft.com/downloads/) pour télécharger le disque dur virtuel.

- **FreeBSD d’exploitation installés dans un fichier .vhd**--un prise en charge FreeBSD système d’exploitation doit être installé sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation telles que Hyper-V pour créer le fichier .vhd et installer le système d’exploitation. Pour savoir comment installer et utiliser Hyper-V, voir [installer Hyper-V et créer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Le nouveau format de VHDX n’est pas pris en charge dans Azure. Vous pouvez convertir le disque au format de disque dur virtuel à l’aide de Gestionnaire Hyper-V ou l' applet de commande [convertir dur](https://technet.microsoft.com/library/hh848454.aspx). Par ailleurs, il existe un [didacticiel sur MSDN sur l’utilisation de FreeBSD avec Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Cette tâche inclut les cinq étapes suivantes.

## <a name="step-1-prepare-the-image-for-upload"></a>Étape 1 : Préparer l’image pour téléchargement

Sur l’ordinateur virtuel où vous avez installé le système d’exploitation FreeBSD, procédez comme suit :

1. Activer le protocole DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Activer le protocole SSH.

    SSH est activé par défaut après l’installation à partir du disque. Si elle n’est pas activée pour une raison quelconque, ou si vous utilisez FreeBSD VHD directement, tapez les informations suivantes :

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Configurer la console série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Installez sudo.

    Le compte racine est désactivé dans Azure. Cela signifie que vous avez besoin d’utiliser sudo à partir d’un utilisateur sans privilège d’exécuter des commandes avec des privilèges élevés.

        # pkg install sudo
;
5. Conditions préalables pour Agent Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Installez l’Agent Azure.

    La dernière version de l’Agent Azure toujours sont accessibles sous [github](https://github.com/Azure/WALinuxAgent/releases). La version 2.0.10 + embauche prend en charge FreeBSD 10 et 10.1, la version 2.1.4 embauche prend en charge FreeBSD 10.2 et versions ultérieures.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0, nous utiliserons 2.0.16 par exemple :

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1, nous utiliserons 2.1.4 par exemple :

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Après avoir installé l’Agent Azure, il est recommandé de vérifier qu’il s’exécute :

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Deprovision le système.

    Deprovision le système pour nettoyer et adapté à la nouvelle mise en service. La commande suivante supprime également le compte d’utilisateur généré dernière et les données associées :

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Vous pouvez maintenant arrêter votre machine virtuelle.

## <a name="step-2-create-a-storage-account-in-azure"></a>Étape 2 : Créer un compte de stockage dans Azure ##

Vous avez besoin d’un compte de stockage dans Azure pour télécharger un fichier .vhd afin qu’il puisse être utilisé pour créer une machine virtuelle. Vous pouvez utiliser le portail classique Azure pour créer un compte de stockage.

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).

2. Dans la barre de commandes, sélectionnez **Nouveau**.

3. Sélectionnez les **Services Business Data** > **stockage** > **Création rapide**.

    ![Rapide créer un compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Renseignez les champs comme suit :

    - Dans le champ **URL** , tapez un nom de sous-domaine à utiliser dans l’URL de compte de stockage. L’entrée peut contenir entre 3-24 numéros et toutes les lettres minuscules. Ce nom devient le nom d’hôte au sein de l’URL est utilisée pour adresser stockage d’objets Blob Azure, stockage Azure file d’attente ou ressources de stockage de Table Azure pour l’abonnement.

    - Dans le menu déroulant **Emplacement/affinité du groupe** , choisissez l' **emplacement ou affinité du groupe** pour le compte de stockage. Un groupe affinité permet d’insérer votre cloud services et le stockage dans le même centre de données.

    - Dans le champ **réplication** , décidez si vous souhaitez utiliser la réplication **Geo redondants** pour le compte de stockage. Geo réplication est activée par défaut. Cette option réplique vos données dans un emplacement secondaire, sans frais, afin que votre stockage bascule vers cet emplacement si une défaillance majeure se produit à l’emplacement principal. L’emplacement secondaire est affecté automatiquement et ne peuvent pas être modifié. Si vous avez besoin de davantage de contrôle sur l’emplacement de votre espace de stockage sur le nuage en raison des obligations légales ou de stratégie de l’organisation, vous pouvez désactiver geo réplication. Toutefois, n’oubliez pas que si vous activez ultérieurement geo réplication, vous devrez payer frais de transfert de données unique pour dupliquer des données existantes à l’emplacement secondaire. Services de stockage sans geo réplication sont proposés avec une remise. Obtenir plus d’informations sur la gestion des geo-réplication de comptes de stockage sont accessibles ici : [créer, gérer, ou supprimer un compte de stockage](../storage-create-storage-account/#replication-options).

    ![Entrez les détails de compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Sélectionnez **créer le compte de stockage**. Le compte apparaît maintenant sous **stockage**.

    ![Compte de stockage créé avec succès](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Ensuite, créez un conteneur pour vos fichiers .vhd téléchargé. Sélectionnez le nom de compte de stockage, puis **conteneurs**.

    ![Détails de compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Sélectionnez **créer un conteneur**.

    ![Détails de compte de stockage](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. Dans le champ **nom** , tapez un nom pour votre conteneur. Puis, dans le menu déroulant **accès** , sélectionnez le type de stratégie d’accès souhaité.

    ![Nom du conteneur](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Par défaut, le conteneur est privé et sont accessibles par le propriétaire du compte. Pour autoriser l’accès en lecture public pour les objets BLOB dans le conteneur, mais pas pour les propriétés de conteneur et les métadonnées, utilisez l’option **Blob Public** . Pour autoriser l’accès en lecture publique complète pour le conteneur et des objets BLOB, utilisez l’option **Conteneur Public** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Étape 3 : Préparer la connexion à Azure

Avant que vous pouvez télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Vous pouvez utiliser la méthode Azure Active Directory (AD Azure) ou le certificat pour effectuer cette action.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Utilisez la méthode Azure AD pour télécharger un fichier .vhd

1. Ouvrez la console PowerShell Azure.

2. Tapez la commande suivante :  
    `Add-AzureAccount`

    Cette commande ouvre une fenêtre de connexion à l’endroit où vous pouvez vous connecter avec votre compte professionnel ou scolaire.

    ![Fenêtre PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure authentifie et enregistre les informations d’identification. Puis elle ferme la fenêtre.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Utilisez la méthode certificat pour télécharger un fichier .vhd

1. Ouvrez la console PowerShell Azure.

2. Type :  `Get-AzurePublishSettingsFile`.

3. Une fenêtre de navigateur s’ouvre et vous invite à télécharger un fichier .publishsettings. Ce fichier contient des informations et un certificat pour votre abonnement Azure.

    ![Page de téléchargement de navigateur](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings.

4. Type :  `Import-AzurePublishSettingsFile <PathToFile>`, où `<PathToFile>` est le chemin d’accès complet au fichier .publishsettings.

   Pour plus d’informations, voir [prise en main Azure applets de commande](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Pour plus d’informations sur l’installation et configuration de PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Étape 4 : Télécharger le fichier .vhd

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n’importe où dans votre espace de stockage Blob. Voici certains des termes que vous utiliserez lorsque vous téléchargez le fichier :
-  **BlobStorageURL** est l’URL pour le compte de stockage que vous avez créé à l’étape 2.
-  **YourImagesFolder** est le conteneur dans stockage Blob de l’endroit où vous voulez stocker vos images.
- **VHDName** est l’étiquette qui apparaît dans le portail classique Azure pour identifier le disque dur virtuel.
- **PathToVHDFile** est le chemin d’accès complet et le nom du fichier .vhd.


Dans la fenêtre PowerShell Azure que vous avez utilisé à l’étape précédente, tapez :

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Étape 5 : Créer une machine virtuelle avec le fichier téléchargé .vhd
Une fois que vous téléchargez le fichier .vhd, vous pouvez l’ajouter en tant qu’image à la liste d’images personnalisées qui sont associés à votre abonnement et créer une machine virtuelle avec cette image personnalisée.

1. Dans la fenêtre PowerShell Azure que vous avez utilisé à l’étape précédente, tapez :

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Utilisez Linux comme type de système d’exploitation. La version actuelle de PowerShell Azure accepte uniquement « Linux » ou « Windows » en tant que paramètre.

2. Une fois que vous suivez les étapes précédentes, la nouvelle image est répertoriée lorsque vous cliquez sur l’onglet **Images** dans le portail classique Azure.  

    ![Choisir une image](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Créer une machine virtuelle à partir de la galerie. Cette nouvelle image est désormais disponible sous **Mes Images**.
4. Sélectionnez la nouvelle image. Accédez ensuite, suivez les invites pour configurer un nom d’hôte, mot de passe, code et ainsi de suite.

    ![Image personnalisée](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Après avoir effectué la mise en service, vous verrez votre VM FreeBSD en cours d’exécution dans Azure.

    ![Image de FreeBSD dans azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
