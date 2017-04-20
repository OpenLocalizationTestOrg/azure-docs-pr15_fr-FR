<properties
   pageTitle="Gérer vos machines virtuelles à l’aide de PowerShell Azure | Microsoft Azure"
   description="Apprendre à utiliser les commandes que vous pouvez utiliser pour automatiser des tâches dans la gestion de vos machines virtuelles."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gérer vos machines virtuelles à l’aide de PowerShell Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Plusieurs tâches que vous effectuez chaque jour pour gérer vos ordinateurs virtuels peuvent être automatisées à l’aide des applets de commande PowerShell Azure. Cet article donne d’exemples de commandes pour les tâches plus simples et des liens vers des articles qui indiquent les commandes pour les tâches plus complexes.

>[AZURE.NOTE] Si vous n’avez pas installé et configuré Azure PowerShell encore, vous pouvez obtenir des instructions dans l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Comment utiliser les commandes de l’exemple
Vous devez remplacer une partie du texte dans les commandes dont le texte est approprié pour votre environnement. Le < et > symboles indiquent le texte à remplacer. Lorsque vous remplacez le texte, supprimer les symboles, mais laissez les guillemets en place.

## <a name="get-a-vm"></a>Obtenir une machine virtuelle
Il s’agit d’une tâche de base que vous utilisez souvent. Utilisez-le pour obtenir des informations sur une machine virtuelle, effectuer des tâches sur un ordinateur virtuel ou un résultat à stocker dans une variable.

Pour obtenir des informations sur la machine virtuelle, exécutez cette commande, en remplaçant tout les guillemets, y compris les caractères < et > :

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Pour stocker les résultats dans une variable $vm, exécutez :

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Ouvrez une session sur un ordinateur fonctionnant sous Windows virtuel

Exécutez ces commandes :

>[AZURE.NOTE] Vous pouvez obtenir le nom du service machine virtuelle et nuage de l’affichage de la commande **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Arrêter une machine virtuelle

Exécutez la commande suivante :

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilisez ce paramètre pour conserver les adresses IP virtuelle du service cloud s’il s’agit de la dernière machine virtuelle dans ce service cloud. <br><br> Si vous utilisez le paramètre StayProvisioned, vous serez toujours facturé pour la machine virtuelle.

## <a name="start-a-vm"></a>Démarrer une machine virtuelle

Exécutez la commande suivante :

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Joindre un disque de données
Cette tâche requiert quelques étapes sont nécessaires. Tout d’abord, vous utilisez la *** Add-AzureDataDisk *** applet de commande pour ajouter le disque à l’objet $vm. Ensuite, vous utilisez applet de commande de **Mise à jour AzureVM** pour mettre à jour la configuration de la machine virtuelle.

Vous devez également décider si vous souhaitez joindre un nouveau disque ou qui contient des données. Pour un nouveau disque, la commande crée le fichier .vhd et l’attache.

Pour joindre un nouveau disque, exécutez la commande suivante :

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Pour joindre un disque de données existant, exécutez la commande suivante :

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Pour attacher des disques de données à partir d’un fichier .vhd existant dans le stockage blob, exécutez la commande suivante :

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Créer un ordinateur fonctionnant sous Windows virtuel

Pour créer une nouvelle machine virtuelle Windows dans Azure, suivez les instructions dans [Utiliser Azure PowerShell pour créer et préconfigurer machines virtuelles fonctionnant sous Windows](virtual-machines-windows-classic-create-powershell.md). Étapes de cette rubrique vous guide dans la création d’un jeu de commandes Azure PowerShell crée un ordinateur fonctionnant sous Windows virtuel qui peut être préconfiguré :

- Avec l’appartenance de domaine Active Directory.
- Avec des disques supplémentaires.
- Définie un membre d’un équilibrage de charge existant.
- Avec une adresse IP statique.
