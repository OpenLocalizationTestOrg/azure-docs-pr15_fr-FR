<properties 
    pageTitle="Créer des objets personnalisés pour votre ordinateur virtuel ateliers DevTest | Microsoft Azure"
    description="Apprenez à créer vos propres objets pour une utilisation avec ateliers DevTest"
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

#<a name="create-custom-artifacts-for-your-devtest-labs-vm"></a>Créer des objets personnalisés pour votre ordinateur virtuel ateliers DevTest

> [AZURE.VIDEO how-to-author-custom-artifacts] 

## <a name="overview"></a>Vue d’ensemble
**Objets** sont utilisés pour déployer et configurer votre application après qu’une machine virtuelle est mis en service. Un objet est constitué d’un fichier de définition d’objet et d’autres fichiers de script qui sont stockés dans un dossier dans un référentiel git. Fichiers de définition d’objet consistent de JSON et expressions que vous pouvez utiliser pour spécifier ce que vous voulez installer sur un ordinateur virtuel. Par exemple, vous pouvez définir le nom de l’objet, commande à exécuter et les paramètres qui sont mis à disposition lorsque la commande est exécutée. Vous pouvez faire référence à d’autres fichiers de script dans le fichier de définition objet par nom.

##<a name="artifact-definition-file-format"></a>Format de fichier de définition d’objet
L’exemple suivant montre les sections qui composent la structure de base d’un fichier de définition.

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nom de l’élément | Obligatoire ? | Description
| ------------ | --------- | -----------
| $schema      | N°        | Emplacement du fichier de schéma JSON qui vous permet de tester la validité du fichier de définition.
| titre        | Oui       | Nom de l’objet affiché dans le laboratoire.
| Description  | Oui       | Description de l’objet affiché dans le laboratoire.
| iconUri      | N°        | URI de l’icône affichée dans le laboratoire.
| targetOsType | Oui       | Système d’exploitation de la machine virtuelle où objet doit être installé. Options prises en charge sont : Windows et Linux.
| paramètres   | N°        | Valeurs qui sont fournies lors de la commande d’installation objet est exécuté sur un ordinateur. Ainsi, sur la personnalisation de votre objet.
| ExécuterCommande   | Oui       | Objet installer commande qui est exécuté sur un ordinateur virtuel.

###<a name="artifact-parameters"></a>Paramètres d’objet

Dans la section Paramètres du fichier de définition, vous spécifiez les valeurs d’un utilisateur peut entrer pendant l’installation d’un objet. Vous pouvez faire référence à ces valeurs dans la commande d’installation objet.

Vous définissez des paramètres sera la structure suivante.

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nom de l’élément | Obligatoire ? | Description
| ------------ | --------- | -----------
| type         | Oui       | Type de valeur de paramètre. Consultez la liste ci-dessous pour les types autorisés :
| nom complet Oui       | Nom du paramètre qui s’affiche à un utilisateur dans le laboratoire.
| Description  | Oui       | Description du paramètre qui s’affiche dans le laboratoire.

Les types autorisés sont :

- chaîne – toute chaîne JSON valide
- int : un nombre entier JSON valide
- bool – tout booléenne JSON valide
- tableau – tout tableau JSON valide

##<a name="artifact-expressions-and-functions"></a>Fonctions et expressions d’objet

Vous pouvez utiliser expression et commande d’installation de fonctions à construire l’objet.
Expressions placées entre crochets ([et]) et sont évaluées lors de l’objet est installé. Les expressions peuvent apparaître n’importe où dans une valeur de chaîne JSON et toujours retourner une autre valeur JSON. Si vous devez utiliser une chaîne littérale qui commence par un crochet [, vous devez utiliser deux entre crochets [[.
En règle générale, vous utilisez des expressions avec fonctions construire une valeur. Comme dans JavaScript, les appels de fonction sont mis en forme en tant que functionName(arg1,arg2,arg3)

La liste suivante présente les fonctions courantes.

- Parameters(parameterName) - renvoie une valeur de paramètre qui est fournie lorsque la commande objet est exécutée.
- concat (arg1, arg2, arg3,...) - combine plusieurs valeurs de chaîne. Cette fonction peut prendre un nombre quelconque d’arguments.

L’exemple suivant montre comment utiliser des fonctions et expression construire une valeur.

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

##<a name="create-a-custom-artifact"></a>Créez un objet personnalisé

Créez votre objet personnalisé en suivant les étapes ci-dessous :

1. Installer un éditeur JSON : vous devez un éditeur JSON pour travailler avec des fichiers de définition d’objet. Nous vous recommandons d’utiliser [Code Visual Studio](https://code.visualstudio.com/), qui est disponible pour Windows, Linux et OS X.

1. Obtenir un artifactfile.json exemple - voir les objets créés par l’équipe Azure DevTest ateliers auprès de notre [GitHub référentiel](https://github.com/Azure/azure-devtestlab) dans lequel nous avons créé une riche bibliothèque d’objets qui vous aidera à créer vos propres objets. Télécharger un fichier de définition d’objet et y apporter des modifications pour créer vos propres objets.

1. Appel IntelliSense - Exploitez IntelliSense pour afficher les éléments valides qui peuvent être utilisés pour créer un fichier de définition d’objet. Vous pouvez également voir les différentes options pour les valeurs d’un élément. Par exemple, IntelliSense montrent les deux choix de Windows ou Linux lors de la modification de l’élément **targetOsType** .

1. Stocker l’objet dans un référentiel git
    1. Créer un répertoire différent pour chaque objet où le nom du répertoire est le même que le nom de l’objet.
    1. Stockez le fichier de définition d’objet (artifactfile.json) dans le répertoire que vous avez créé.
    1. Stocker les scripts qui sont référencées à partir de la commande d’installation objet.

    Voici un exemple de quoi peut ressembler un dossier objet :

    ![Exemple de mis en pension git objet](./media/devtest-lab-artifact-author/git-repo.png)

1. Ajouter le référentiel d’objets à l’atelier - se réfèrent à l’article, [Ajouter un référentiel objet Git à un laboratoire](devtest-lab-add-artifact-repo.md).

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
- [Comment résoudre les problèmes ignore les objets AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Participer à une machine virtuelle à domaine Active Directory existant à l’aide de modèle de processeur dans un environnement de Test de développement Azure](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Ajouter un référentiel objet Git à un laboratoire](devtest-lab-add-artifact-repo.md).
