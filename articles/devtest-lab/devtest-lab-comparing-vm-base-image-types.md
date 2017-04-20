<properties
    pageTitle="Comparaison des images personnalisées et les formules dans ateliers DevTest | Microsoft Azure"
    description="Découvrez les différences entre des images personnalisées et les formules comme base la machine virtuelle afin que vous puissiez décider de celui qui correspond le mieux à votre environnement."
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
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparaison des images personnalisées et les formules dans ateliers DevTest

## <a name="overview"></a>Vue d’ensemble
[Images personnalisées](./devtest-lab-create-template.md) et [formules](./devtest-lab-manage-formulas.md) peuvent servir comme bases pour la [Création de nouvelles machines virtuelles](./devtest-lab-add-vm-with-artifacts.md). Toutefois, la distinction entre des images personnalisées et les formules clée est une image personnalisée simplement une image basée sur un disque dur virtuel, alors qu’une formule est une image selon une paramètres *en plus de* préconfiguré dur - tels que la taille de mémoire virtuelle, du réseau virtuel et sous-réseau, objets et ainsi de suite. Ces paramètres préconfigurés sont configurés avec des valeurs par défaut qui peuvent être remplacés au moment de la création de la machine virtuelle. Cet article décrit certains des avantages (professionnels de l’informatique) et des inconvénients (inconvénients) dans l’utilisation d’images personnalisés par rapport à l’aide de formules.
 
## <a name="custom-image-pros-and-cons"></a>Inconvénients et image personnalisée
Fournissent des images personnalisées un statique, immuable permettent de créer machines virtuelles à partir d’un environnement souhaité. 

**Professionnels de l’informatique**
- Machine virtuelle mise en service à partir d’une image personnalisée est rapide que rien n’est modifié après que la machine virtuelle est lancée à partir de l’image. En d’autres termes, il n’existe pas de paramètres à appliquer comme l’image personnalisée est simplement une image sans paramètres. 
- Machines virtuelles créés à partir d’une image personnalisée sont identiques.

**Inconvénients**
- Si vous devez mettre à jour un aspect de l’image personnalisée, l’image doit être recréé.  

## <a name="formula-pros-and-cons"></a>Inconvénients et formule
Formules permettent dynamique pour créer des machines virtuelles à partir de la configuration souhaitée.

**Professionnels de l’informatique**
- Modifications de l’environnement peuvent être capturées à la volée via les objets. Par exemple, si vous souhaitez une machine virtuelle installée avec les dernières bits à partir de votre pipeline de version ou inscrivez le dernier code à partir de votre référentiel, vous pouvez simplement spécifier un objet qui déploie les bits dernières ou inscrit le dernier code dans la formule avec une image de base cible. Chaque fois que cette formule est utilisée pour créer des ordinateurs virtuels, le dernier bits/code sont déployé/inscrit à la machine virtuelle. 
- Formules peuvent définir des paramètres par défaut des images personnalisées ne peuvent pas fournir - telles que les tailles de mémoire virtuelle et paramètres de réseau virtuel. 
- Les paramètres enregistrés dans une formule sont affichées sous forme de valeurs par défaut, mais peuvent être modifiés lorsque la machine virtuelle est créée. 

**Inconvénients**
- Création d’une machine virtuelle à partir d’une formule peut prendre plus de temps que la création d’une machine virtuelle à partir d’une image personnalisée.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes

- [Images personnalisées ou des formules ?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

