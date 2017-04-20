<properties
    pageTitle="Gérer des images personnalisées Azure DevTest ateliers pour créer des ordinateurs virtuels | Microsoft Azure"
    description="Apprenez à créer une image personnalisée à partir d’un fichier de disque dur virtuel, ou d’une machine virtuelle existante dans Azure DevTest ateliers"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="tarcher"/>

# <a name="manage-azure-devtest-labs-custom-images-to-create-vms"></a>Gérer des images personnalisées Azure DevTest ateliers pour créer des ordinateurs virtuels

Dans Azure DevTest ateliers, des images personnalisées permettent de créer rapidement des machines virtuelles sans attendre que tous les logiciels requis soit installé sur l’ordinateur cible. Images personnalisées vous permettent de préinstaller tous les logiciels dont vous avez besoin d’un fichier de disque dur virtuel, puis utilisent le fichier de disque dur virtuel pour créer une machine virtuelle. Étant donné que le logiciel est déjà installé, l’heure de création d’une machine virtuelle est beaucoup plus rapide. En outre, des images personnalisées sont utilisées pour cloner machines virtuelles en créant une image personnalisée à partir d’un ordinateur virtuel, puis créer machines virtuelles à partir de cette image personnalisée.

Dans cet article, vous découvrez comment :

- [Créer une image personnalisée à partir d’un fichier de disque dur virtuel](#create-a-custom-image-from-a-vhd-file) afin que vous pouvez ensuite créer une machine virtuelle à partir de cette image personnalisée. 
- [Créer une image personnalisée à partir d’un ordinateur virtuel](#create-a-custom-image-from-a-vm) pour clonant machine virtuelle rapide.

## <a name="create-a-custom-image-from-a-vhd-file"></a>Créer une image personnalisée à partir d’un fichier de disque dur virtuel

Dans cette section, vous allez apprendre à créer une image personnalisée à partir d’un fichier de disque dur virtuel.
Vous devez accéder à un fichier de disque dur virtuel valide pour effectuer toutes les étapes dans cette section.   


1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **plusieurs services**, puis **Ateliers DevTest** dans la liste.

1. Dans la liste des ateliers, sélectionnez le laboratoire souhaité.  

1. Dans la carte du laboratoire, sélectionnez **Configuration**. 

1. Dans la carte de **Configuration** atelier, sélectionnez **images personnalisé**.

1. Dans la carte **images personnalisé** , sélectionnez **+ image personnalisée**.

    ![Ajouter une image personnalisée](./media/devtest-lab-create-template/add-custom-image.png)

1. Entrez le nom de l’image personnalisée. Ce nom s’affiche dans la liste des images de base lors de la création d’un ordinateur virtuel.

1. Entrez la description de l’image personnalisée. Cette description est affichée dans la liste des images de base lors de la création d’un ordinateur virtuel.

1. Sélectionnez **fichier de disque dur virtuel**.

1. Si vous avez accès à un fichier de disque dur virtuel qui n’est pas répertorié, ajoutez-la en suivant les instructions dans la section [télécharger un fichier de disque dur virtuel](#upload-a-vhd-file) et revenez à cet écran lorsque vous avez terminé.

1. Sélectionnez le fichier de disque dur virtuel souhaité.

1. Sélectionnez **OK** pour fermer la carte de **Fichier du disque dur virtuel** .

1. Sélectionnez **Configuration du système d’exploitation**.

1. Sous l’onglet **Configuration du système d’exploitation** , sélectionnez **Windows** ou **Linux**.

1. Si **Windows** est sélectionné, spécifiez via la case à cocher si *Sysprep* a été exécuté sur l’ordinateur.

1. Sélectionnez **OK** pour fermer la carte de **Configuration du système d’exploitation** .

1. Cliquez sur **OK** pour créer l’image personnalisée.

1. Accédez à la section [Étapes suivantes](#next-steps) .

###<a name="upload-a-vhd-file"></a>Télécharger un fichier de disque dur virtuel

Pour ajouter une image personnalisée, vous devez avoir accès à un fichier de disque dur virtuel.

1. Dans la carte de **Fichier du disque dur virtuel** , sélectionnez **télécharger un fichier de disque dur virtuel à l’aide de PowerShell**.

    ![Téléchargement de l’image](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. La carte suivante affiche les instructions pour modifier et exécuter un script PowerShell qui transfère à votre abonnement Azure un fichier de disque dur virtuel. 
**Remarque :** Ce processus peut être long selon la taille du fichier disque dur virtuel et la vitesse de connexion.

## <a name="create-a-custom-image-from-a-vm"></a>Créer une image personnalisée à partir d’une machine virtuelle
Si vous avez un ordinateur virtuel qui est déjà configuré, vous pouvez créer une image personnalisée à partir de cet ordinateur virtuel et utiliser ensuite cette image personnalisée pour créer d’autres machines virtuelles identiques. Les étapes suivantes montrent comment créer une image personnalisée à partir d’un ordinateur virtuel :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **plusieurs services**, puis **Ateliers DevTest** dans la liste.

1. Dans la liste des ateliers, sélectionnez le laboratoire souhaité.  

1. Dans la carte du laboratoire, sélectionnez **mes machines virtuelles**.
 
1. Dans la carte de **mes machines virtuelles** , sélectionnez la machine virtuelle à partir de laquelle vous voulez créer l’image personnalisée.

1. Dans la carte de la machine virtuelle, sélectionnez **créer une image personnalisée (disque dur virtuel)**.

    ![Créer un élément de menu image personnalisée](./media/devtest-lab-create-template/create-custom-image.png)

1. Sur la carte de **créer une image** , entrez un nom et une description pour votre image personnalisée. Ces informations sont affiche dans la liste des bases lorsque vous créez une machine virtuelle.

    ![Créer la carte image personnalisée](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Indiquez si sysprep a été exécuté sur l’ordinateur virtuel. Si la sysprep n’a pas été exécutée sur l’ordinateur virtuel, indiquez si vous souhaitez sysprep exécuter lorsqu’une machine virtuelle est créée à partir de cette image personnalisée.

1. Sélectionnez **OK** lorsque vous avez terminé pour créer l’image personnalisée.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes

- [Images personnalisées ou des formules ?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copie d’Images personnalisés entre ateliers DevTest Azure](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Étapes suivantes

Une fois que vous avez ajouté une image personnalisée à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [Ajouter une machine virtuelle à votre laboratoire](./devtest-lab-add-vm-with-artifacts.md).