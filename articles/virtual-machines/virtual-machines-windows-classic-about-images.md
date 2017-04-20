<properties
    pageTitle="À propos des images pour machines virtuelles Windows | Microsoft Azure"
    description="Obtenir des informations sur l’utilisation des images avec des machines virtuelles de Windows dans Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>À propos des images pour machines virtuelles Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Utiliser des images

Vous pouvez utiliser le module Azure PowerShell pour gérer les images disponibles pour votre abonnement Azure. Vous pouvez également utiliser le portail classique Azure pour certaines tâches de l’image, mais la ligne de commande vous propose des options supplémentaires.


-   **Obtenir toutes les images**:`Get-AzureVMImage`renvoie une liste de toutes les images disponibles dans votre abonnement actuel : vos images, ainsi que ceux fournis par Azure ou partenaires. Cela signifie que vous pouvez obtenir une liste de grande taille. Les exemples suivants montrent comment obtenir une liste plus courte.
-   **Obtenir des familles image**:`Get-AzureVMImage | select ImageFamily` Obtient une liste des familles image en affichant des chaînes de propriété **ImageFamily** .
-   **Obtenir toutes les images dans une famille spécifique**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Rechercher des Images de machine virtuelle**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` cela fonctionne en filtrant la propriété DataDiskConfiguration, qui s’applique uniquement aux Images machine virtuelle. Cet exemple filtre également la sortie uniquement le nom d’étiquette et image.
-   **Enregistrer une image GRG**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Enregistrer une image spécialisée**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Le paramètre OSState est obligatoire si vous voulez créer une image de machine virtuelle, qui inclut des disques de données, ainsi que le disque système d’exploitation. Si vous n’utilisez pas le paramètre, l’applet de commande crée une image du système d’exploitation. La valeur du paramètre indique si l’image est généralisée ou spécialisé, selon que le disque système d’exploitation a été préparé pour le réutiliser.
-   **Supprimer une image**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [créer un ordinateur Windows à l’aide du portail classique](virtual-machines-windows-classic-tutorial.md)

