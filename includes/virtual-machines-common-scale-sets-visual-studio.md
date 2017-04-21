

Cet article vous explique comment déployer un Azure Machine virtuelle échelle définie à l’aide d’un déploiement de groupe de ressources Visual Studio.


[Azure Machine virtuelle échelle jeux](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) sont une ressource Azure calculer à déployer et gérer une collection de machines virtuelles similaires avec les options facilement intégrées pour ajuster automatiquement et l’équilibrage de charge. Vous pouvez mettre en service et déployer des jeux d’échelle de machine virtuelle à l’aide de [modèles Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates). PROCESSEUR modèles peuvent être déployés à l’aide de reste Azure infrastructure du langage commun, PowerShell et également directement à partir de Visual Studio. Visual Studio propose un ensemble d’exemple modèles qui peut être déployé dans le cadre d’un projet de déploiement de groupe de ressources Azure.

Déploiements de groupe de ressources Azure constituent un moyen pour regrouper et publier un ensemble de ressources Azure connexes dans une opération de déploiement unique. Vous pouvez en savoir plus sur les ici : [Création et déploiement de groupes de ressources Azure via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/).

## <a name="pre-requisites"></a>Conditions préalables

Pour commencer à déployer des jeux d’échelle machine virtuelle dans Visual Studio, vous devez les éléments suivants :

- Visual Studio 2013 ou 2015
- Azure SDK 2.7 ou 2,8

Remarque : Ces instructions part du principe que vous utilisez Visual Studio 2015 avec [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Création d’un projet

1. Créer un projet dans Visual Studio 2015 en choisissant **fichier | Nouveau | Projet**.

    ![Fichier nouveau][file_new]

2. Sous **Visual c# | Nuage**, choisissez **Le Gestionnaire de ressources Azure** pour créer un projet pour le déploiement d’un modèle de processeur.

    ![Créer des projets][create_project]

3.  Dans la liste des modèles, sélectionnez modèle d’ensemble de Windows Machine virtuelle échelle ou Linux.

    ![Sélectionnez le modèle][select_Template]

4. Une fois que votre projet est créé vous verrez des scripts PowerShell déploiement, un modèle de gestionnaire de ressources Azure et un fichier de paramètres pour l’ensemble de l’échelle de Machine virtuelle.

    ![Explorateur de solutions][solution_explorer]

## <a name="customize-your-project"></a>Personnaliser votre projet

Désormais, vous pouvez modifier le modèle pour personnaliser pour des besoins de votre application, telles que l’ajout des propriétés de l’extension machine virtuelle ou la modification des règles d’équilibrage de charge. Par défaut, les modèles de définir machine virtuelle échelle sont configurés pour déployer l’extension AzureDiagnostics ce qui facilite son ajouter des règles d’échelle. Il déploie également un équilibrage de charge avec une adresse IP publique, configurée avec des règles de trafic entrant NAT qui vous permettent de vous connectez aux instances machine virtuelle avec SSH (Linux) ou RDP (Windows) – la plage de port frontal démarre 50000, ce qui signifie que dans le cas de Linux, si vous SSH sur le port 50000 de l’adresse IP publique (ou le nom de domaine), vous doivent être routés vers port 22 de la première machine virtuelle dans l’ensemble d’échelle. Connexion au port 50001 doivent être routé vers la deuxième machine virtuelle port 22 et ainsi de suite.

 Une bonne façon de modifier vos modèles avec Visual Studio consiste à utiliser le plan JSON pour organiser des paramètres, des variables et des ressources. Avec une compréhension du schéma de Visual Studio peut pointer les erreurs dans votre modèle avant de déployer.

![Explorer JSON][json_explorer]

## <a name="deploy-the-project"></a>Déployer le projet

6. Déployez le modèle de processeur vers Azure pour créer la ressource machine virtuelle échelle définie. Clic droit sur le nœud de projet, choisissez **déployer | Nouveau déploiement**.

    ![Modèle de déploiement][5deploy_Template]

7. Sélectionnez votre abonnement dans la boîte de dialogue « Déployer au groupe de ressources ».

    ![Modèle de déploiement][6deploy_Template]

8. À partir de là, vous pouvez également créer un nouveau groupe de ressources Azure pour déployer votre modèle.

    ![Nouveau groupe de ressources][new_resource]

9. Cliquez ensuite sur le bouton **Modifier les paramètres** à entrer des paramètres qui seront passés à votre modèle, certaines valeurs, telles que le nom d’utilisateur et mot de passe pour le système d’exploitation sont nécessaires pour créer le déploiement.

    ![Modifier les paramètres][edit_parameters]

10. Maintenant, cliquez sur **déployer**. La fenêtre de **sortie** affiche la progression du déploiement. Notez que la l’action s’exécute le script de **Déploiement AzureResourceGroup.ps1** .

    ![Fenêtre de sortie][output_window]

## <a name="exploring-your-vm-scale-set"></a>Exploration de votre jeu d’échelle machine virtuelle

Une fois que le déploiement est terminé, vous pouvez afficher le nouvel ensemble d’échelle machine virtuelle dans Visual Studio **Cloud Explorer** (Actualiser la liste). Explorer cloud vous permet de gérer les ressources Azure dans Visual Studio lors du développement d’applications. Vous pouvez également afficher votre machine virtuelle échelle définie dans le portail Azure et l’Explorateur de ressources Azure.

![Explorer cloud][cloud_explorer]

 Le portail fournit la meilleure façon de gérer votre infrastructure Azure avec un navigateur web, tandis que l’Explorateur de ressources Azure fournit un moyen facile de l’Explorateur visuellement et déboguer Azure ressources, donnant une fenêtre dans la vue « instance », laquelle s’affiche également les commandes PowerShell pour les ressources que vous examinez. Machine virtuelle échelle jeux sont dans l’aperçu, l’Explorateur de ressources affiche plus d’informations pour les jeux d’échelle machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez correctement déployée jeux d’échelle machine virtuelle via Visual Studio, vous pouvez personnaliser davantage votre projet pour l’adapter à vos besoins d’application. Par exemple la configuration d’échelle par ajout d’une ressource de perspectives, en ajoutant infrastructure à votre modèle comme autonome machines virtuelles ou déployer des applications à l’aide de l’extension de script personnalisé. Vous trouverez une bonne source d’exemples de modèles dans le référentiel de GitHub [Modèles de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates) (recherchez « vmss »).

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png