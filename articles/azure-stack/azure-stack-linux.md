<properties
    pageTitle="Les invités Linux sur pile Azure | Microsoft Azure"
    description="Découvrez comment créer des machines virtuelles basées sur Linux sur Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Déployer des machines virtuelles Linux Azure pile

Vous pouvez déployer des machines virtuelles Linux le contrôle du concept pile Azure en ajoutant une image basée sur Linux dans la pile Azure Marketplace. Plusieurs fournisseurs Linux ont fourni les images qui peuvent être ajoutés dans une pile de Azure du contact ou vous pouvez créer votre propre.

## <a name="download-an-image"></a>Télécharger une image

 1. Téléchargez et extrayez une image compatible avec Azure pile les liens suivants ou préparez votre propre :
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Extraire l’image disque dur virtuel si nécessaire et [Ajouter l’image à la place de marché](azure-stack-add-vm-image.md). Assurez-vous que la `OSType` paramètre est défini sur `Linux`.
 
 3. Une fois que vous avez ajouté l’image à la place de marché, un élément Marketplace est créé et vous pouvez déployer une machine virtuelle Linux.
  
## <a name="prepare-your-own-image"></a>Préparez votre propre image

1. Préparez votre propre image Linux à l’aide d’une des instructions suivantes :
 - [Distribution centOS](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Debian Linux](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Linux Oracle](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Chapeau rouge Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Téléchargez et installez l' [Agent de Linux Azure](https://github.com/Azure/WALinuxAgent/)

    L’Agent de Linux Azure version 2.1.3 ou version ultérieure est requis pour la mise en service de votre VM Linux sur Azure pile. La plupart des répartitions énumérées déjà comprennent cette version de l’agent ou plus en tant que package dans leurs référentiels (généralement appelée `WALinuxAgent` ou `walinuxagent`). Toutefois, si la version du package agent Azure est inférieure à 2.1.3 (c'est-à-dire 2.0.18 ou inférieur), puis vous devez installer l’agent manuellement. La version installée peut être déterminée à partir du nom du package ou en exécutant `/usr/sbin/waagent -version` sur l’ordinateur virtuel.

    Suivez les instructions ci-dessous pour installer manuellement - l’agent Linux Azure

 - Tout d’abord, téléchargez l’agent Azure Linux dernière depuis [Github](https://github.com/Azure/WALinuxAgent/releases), exemple :

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Décompresser l’agent Azure :

            # tar -vzxf v2.2.0.tar.gz

 - Installer python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Installer l’agent Azure :

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Systèmes avec Python 2.x et 3.x est installé Python côte à côte peuvent avoir besoin exécuter la commande suivante :

        # sudo python3 setup.py install --register-service

    Pour plus d’informations, voir l’Agent de Linux Azure [Lisezmoi](https://github.com/Azure/WALinuxAgent/blob/master/README.md).

3. [Ajouter l’image à la place de marché](azure-stack-add-vm-image.md). Assurez-vous que la `OSType` paramètre est défini sur `Linux`.

4. Une fois que vous avez ajouté l’image à la place de marché, un élément Marketplace est créé et vous pouvez déployer une machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes

[Forum aux questions sur la pile d’Azure](azure-stack-faq.md)