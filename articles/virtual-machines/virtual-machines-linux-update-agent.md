<properties
    pageTitle="Mettre à jour l’Agent Linux Azure GitHub | Microsoft Azure"
    description="Découvrez comment la mise à jour Azure Linux Agent pour votre VM Linux dans Azure vers la version lateset de Github"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Comment mettre à jour l’Agent de Linux Azure sur un ordinateur virtuel vers la dernière version de GitHub

Pour mettre à jour votre [Azure Linux Agent](https://github.com/Azure/WALinuxAgent) sur un Linux VM dans Azure, vous devez déjà avoir :

1. Un VM Linux en cours d’exécution dans Azure.
2. Une connexion à ce VM Linux à l’aide de SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Si vous exécutez cette tâche à partir d’un ordinateur Windows, vous pouvez utiliser PuTTY à SSH dans l’ordinateur Linux. Pour plus d’informations, voir [comment se connecter à un Linux en cours d’exécution Machine virtuelle](virtual-machines-linux-mac-create-ssh-keys.md).

Approuvée Azure Linux distros avez placé le package Azure Linux Agent dans leurs référentiels, vérifiez et installez la version la plus récente à partir de ce référentiel Distro tout d’abord si possible.  

Pour Ubuntu, tapez :

    #sudo apt-get install walinuxagent

Puis sur CentOS, tapez :

    #sudo yum install waagent


Pour Oracle Linux, assurez-vous que la `Addons` référentiel est activé. Cliquez sur Modifier le fichier `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) et modifiez la ligne `enabled=0` à `enabled=1` sous **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.

Ensuite, pour installer la dernière version de l’Agent de Linux Azure, tapez :


    #sudo yum install WALinuxAgent

Si vous ne trouvez pas le référentiel de module complémentaire vous pouvez simplement ajouter ces lignes à la fin de votre fichier .repo selon votre version Oracle Linux :

Pour les machines virtuelles Oracle Linux 6 :

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Pour les machines virtuelles Oracle Linux 7 :

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Puis tapez :

    #sudo yum update WALinuxAgent

C’est généralement tout vous avez besoin, mais si pour une raison quelconque, vous devez installer à partir de https://github.com directement, procédez comme suit.


## <a name="install-wget"></a>Installer wget

Connectez-vous à votre ordinateur virtuel à l’aide de SSH.

Installez wget (il existe certaines distros qui ne l’installer par défaut telles que les versions Redhat et Oracle Linux CentOS 6.4 et 6.5) en tapant `#sudo yum install wget` sur la ligne de commande.


## <a name="download-the-latest-version"></a>Téléchargez la dernière version

Ouvrir [la version de l’Agent de Linux Azure dans GitHub](https://github.com/Azure/WALinuxAgent/releases) dans une page web et découvrez le numéro de version plus récente. (Vous pouvez trouver votre version actuelle en tapant `#waagent --version`.)

### <a name="for-version-20x-type"></a>Pour version 2.0.x, tapez :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   La ligne suivante utilise la version 2.0.14 par exemple :

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Pour version 2.1.x ou version ultérieure, tapez :

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   La ligne suivante utilise version2.1.0 par exemple :

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Installer l’Agent Linux Azure

### <a name="for-version-20x-use"></a>Pour version 2.0.x, utilisez :

 Rendez waagent exécutable :

    #chmod +x waagent

 Copier de nouveau l’exécutable dans/usr/sbin /.

  Pour la plupart des Linux, utilisez :

    #sudo cp waagent /usr/sbin

  Pour CoreOS, utilisez :

    #sudo cp waagent /usr/share/oem/bin/

  S’il s’agit d’une nouvelle installation de l’Agent de Linux Azure, exécutez :
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Pour version PowerPath2.1, utilisez :

Vous devrez peut-être installer le package `setuptools` --voir [ici](https://pypi.python.org/pypi/setuptools). Ensuite, exécutez :

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Redémarrez le service waagent

Pour la plupart des linux Distros :

    #sudo service waagent restart

Pour Ubuntu, utilisez :

    #sudo service walinuxagent restart

Pour CoreOS, utilisez :

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Confirmer la version de le Linux Agent Azure

    #waagent -version

Pour CoreOS, la commande ci-dessus peut ne pas fonctionne.

Vous verrez que la version de l’Agent Azure Linux a été mis à jour vers la nouvelle version.

Pour plus d’informations sur l’Agent de Linux Azure, voir [Azure Linux Agent Lisezmoi](https://github.com/Azure/WALinuxAgent).
