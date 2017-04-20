<properties
    pageTitle="Utiliser Azure DevTest ateliers formation | Microsoft Azure"
    description="Découvrez comment utiliser Azure DevTest ateliers pour les scénarios de formation."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="sdanie"/>

# <a name="use-azure-devtest-labs-for-training"></a>Utiliser Azure DevTest ateliers pour la formation

Ateliers DevTest Azure peut servir à mettre en œuvre de nombreux scénarios clés en plus de développement/test. Un de ces scénarios consiste à configurer un laboratoire formation. Ateliers DevTest Azure vous permet de créer un laboratoire où vous pouvez fournir des modèles personnalisés que chaque candidat permet de créer des environnements identiques et isolées formation. Vous pouvez vous assurer que les environnements de formation sont disponibles pour chaque candidat uniquement lorsqu’ils ont besoin et contient suffisamment de ressources - tels que des machines virtuelles - requis pour la formation. Enfin, vous pouvez facilement partager l’atelier avec personnel, qu’ils peuvent accéder en un seul clic.   

![Utiliser DevTest ateliers pour la formation](./media/devtest-lab-training-lab/devtest-lab-training.png)

Ateliers DevTest Azure remplit les conditions suivantes sont nécessaires pour assurer la formation dans un environnement virtuel : 


-   Personnel ne peuvent pas voir machines virtuelles créées par d’autres personnel
-   Chaque ordinateur formation doit être identique
-   Personnel peut rapidement mise en service de leurs environnements de formation
-   Contrôler le coût en veillant à ce que personnel ne peuvent pas accéder à davantage d’ordinateurs virtuels que nécessaire pour la formation et également l’arrêt machines virtuelles lorsqu’ils n’utilisent pas les
-   Partager facilement le laboratoire formation avec chaque candidat
-   Réutiliser indéfiniment l’atelier de formation


Dans cet article, vous en savoir plus sur les différentes fonctionnalités de Azure DevTest ateliers qui peuvent être utilisées pour répondre aux exigences de formation décrites précédemment et la procédure détaillée que vous pouvez suivre pour configurer un laboratoire formation.  


## <a name="implementing-training-with-azure-devtest-labs"></a>Mise en œuvre formation avec Azure DevTest pratiques

1. **Créer le laboratoire** 

    Ateliers sont le point de départ dans Azure DevTest ateliers. Une fois que vous créez un laboratoire, vous pouvez effectuer les tâches telles lorsque ajoutez des utilisateurs (personnel) à l’atelier, définir des stratégies pour contrôler les coûts, définissez les images de machine virtuelle qui peuvent de créer rapidement et bien plus encore.   

    Plus d’informations en cliquant sur les liens dans le tableau suivant :

  	| Tâche                                                            | Vous découvrez                                                    |
|-----------------------------------------------------------------|----------------------------------------------------------------------|
| [Création d’un atelier dans Azure DevTest ateliers](devtest-lab-create-lab.md) | Découvrez comment créer un laboratoire dans Azure DevTest ateliers dans le portail Azure. |

2. **Créer des ordinateurs virtuels de formation en minutes à l’aide de cadres marketplace et les images personnalisées** 
    
    Vous pouvez choisir des images prêts à l’emploi à partir d’un large éventail d’images dans la Azure Marketplace et les rendre disponibles pour le personnel de l’atelier. Si les images prêts à l’emploi ne répond à vos besoins, vous pouvez créer une image personnalisée en créant un laboratoire machine virtuelle à l’aide d’une image prêts à l’emploi à partir d’Azure Marketplace, installer tous les logiciels dont vous avez besoin pour la formation et l’enregistrement de la machine virtuelle en tant qu’image personnalisée dans le laboratoire. 

    Plus d’informations en cliquant sur les liens dans le tableau suivant :

  	| Tâche                                                                              | Vous découvrez                                                                                                                                  |
|-----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| [Configurer les images Azure Marketplace](devtest-lab-configure-marketplace-images.md) | Découvrez comment vous pouvez d’autorisation des images Azure Marketplace ; rendre disponibles pour une sélection uniquement les images souhaitées pour la formation.                 |
| [Créer une image personnalisée](devtest-lab-create-template.md)                           | Créer une image personnalisée par avant l’installation du logiciel que vous avez besoin pour la formation afin que personnel permet de créer rapidement une machine virtuelle à l’aide de l’image personnalisée. |

3. **Créer des modèles réutilisables pour les ordinateurs de formation** 

    Une formule dans Azure DevTest ateliers est une liste de valeurs de propriété par défaut utilisé pour créer une machine virtuelle. Vous pouvez créer une formule dans le laboratoire en choisissant une image, une taille de la mémoire virtuelle (une combinaison de processeur et RAM) et un réseau virtuel. Chaque candidat peut voir la formule dans le laboratoire et utilisez-le pour créer une machine virtuelle. 

    Plus d’informations en cliquant sur les liens dans le tableau suivant :

  	| Tâche                                                                         | Vous découvrez                                                                                                          |
|------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| [Gérer les formules DevTest ateliers pour créer des ordinateurs virtuels](devtest-lab-manage-formulas.md) | Découvrez comment vous pouvez créer une formule en choisissant l’une image, la taille de la mémoire virtuelle (combinaison du processeur et RAM) et un réseau virtuel. |

4. **Contrôler les coûts**

    Ateliers DevTest Azure vous permet de définir une stratégie dans le laboratoire pour spécifier le nombre maximal de machines virtuelles pouvant être créées par un candidat dans le laboratoire. 

    Si vous organisez formation plusieurs jour et que vous souhaitez arrêter tous les ordinateurs virtuels à un moment donné du jour et puis redémarrer automatiquement les le jour suivant, cliquer facilement accomplir qui en définissant arrêt automatique et les stratégies dans le laboratoire un démarrage automatique. 

    Enfin, une fois terminée formation vous pouvez supprimer en même temps tous les ordinateurs virtuels en exécutant un script PowerShell unique. 

    Plus d’informations en cliquant sur les liens dans le tableau suivant :

  	| Tâche                                                                                                                                    | Vous découvrez                                                      |
|-----------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [Définir les stratégies d’atelier](devtest-lab-set-lab-policy.md)                                                                                    | Contrôler les coûts en définissant des stratégies dans le laboratoire.                       |
| [Supprimer tous les atelier machines virtuelles à l’aide d’un script PowerShell](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) | Supprimer tous les exercices pratiques dans une seule opération une fois la formation terminée. |

5. **Partager le laboratoire avec chaque candidat**

    Ateliers est directement accessible via un lien que vous partagez avec votre personnel. Votre personnel n’avez pas encore avoir un compte Azure, dans la mesure où ils disposent d’un [compte Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Personnel ne peut pas voir machines virtuelles créées par d’autres personnel.  

    Plus d’informations en cliquant sur les liens dans le tableau suivant :

  	| Tâche                                                                                                                                | Vous découvrez                                                   |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [Ajouter un candidat à un laboratoire dans Azure DevTest ateliers](devtest-lab-add-devtest-user.md)                                                     | Utiliser le portail Azure ajouter personnel à votre laboratoire formation.       |
| [Ajouter personnel au laboratoire à l’aide d’un script PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) | Utiliser PowerShell pour automatiser personnel ajout à votre laboratoire formation. |
| [Obtenir un lien vers l’atelier](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)                                                  | Découvrez comment un laboratoire directement accessible via un lien hypertexte.        |

6. **Réutiliser indéfiniment l’atelier** 

    Vous pouvez automatiser la création de laboratoire, y compris les paramètres personnalisés, en créant un modèle de gestionnaire de ressources et l’utiliser pour créer des ateliers identiques indéfiniment. 

    Plus d’informations en cliquant sur les liens dans le tableau suivant :

  	| Tâche                                                                                                                               | Vous découvrez                                                      |
|------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [Créer un laboratoire à l’aide d’un modèle de gestionnaire de ressources](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) | Créer des ateliers dans Azure DevTest ateliers à l’aide de modèles de gestionnaire de ressources. |

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]  

