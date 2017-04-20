<properties
    pageTitle="Ateliers DevTest Azure FAQ | Microsoft Azure"
    description="Trouvez des réponses aux questions les plus fréquentes Azure DevTest ateliers"
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
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Ateliers DevTest Azure Forum aux questions

Cet article répond aux questions les plus fréquemment posées sur Azure DevTest ateliers.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Général
- [Que se passe-t-il si ma question n’est pas une réponse ici ?](#what-if-my-question-isnt-answered-here)
- [Pourquoi dois-je utiliser Azure DevTest ateliers ?](#why-should-i-use-azure-devtest-labs) 
- [Que signifie « sérénité, libre-service » ?](#what-does-quotworry-free-self-servicequot-mean)
- [Comment puis-je utiliser Azure DevTest ateliers ?](#how-can-i-use-azure-devtest-labs) 
- [Comment de facturation pour Azure DevTest ateliers ?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Sécurité 
- [Quels sont les niveaux de sécurité différent dans Azure DevTest ateliers ?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Comment créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>Automatisation et intégration CI/CD 
 
- [Azure DevTest ateliers s’intègre avec mon toolchain CI/CD ?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Machines virtuelles 
 
- [Pourquoi ne puis-je pas voir certains ordinateurs virtuels dans la carte de Machines virtuelles Azure se dans Azure DevTest ateliers ?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [Quelle est la différence entre des images personnalisées et les formules ?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Comment créer plusieurs machines virtuelles à partir du modèle même à la fois ?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Comment déplacer mes machines virtuelles Azure existantes dans mon laboratoire Azure DevTest ateliers ?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [Puis-je joindre plusieurs disques à mes machines virtuelles ?](#can-i-attach-multiple-disks-to-my-vms) 
- [Comment automatiser le processus de téléchargement de fichiers de disque dur virtuel pour créer des images personnalisées ?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Comment puis-je automatiser le processus de suppression de tous les ordinateurs virtuels dans mon laboratoire ?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Objets 
 
- [Que sont les objets ?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Configuration de l’atelier 
 
- [Comment créer un laboratoire à partir d’un modèle de gestionnaire de ressources Azure ?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Pourquoi mes ordinateurs virtuels créés dans différents groupes de ressources dont les noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Ateliers combien puis-je créer sous le même abonnement ?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Machines virtuelles combien puis-je créer par atelier ?](#how-many-vms-can-i-create-per-lab)
- [Comment partager un lien direct vers mon laboratoire ?](#how-do-i-share-a-direct-link-to-my-lab)
- [Qu’est un compte Microsoft ?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Résolution des problèmes 
 
- [Mon objet a échoué lors de la création de la machine virtuelle. Comment résoudre les il ?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Pourquoi n’est pas mon réseau existant, virtuel l’enregistrement correctement ?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>Que se passe-t-il si ma question n’est pas une réponse ici ?
Si votre question n’est pas répertoriée ici, nous le faire savoir et nous allons vous aider à trouver une réponse.

- Publier une question dans le [thread Disqus](#comments) à la fin de ce forum aux questions et faites appel à l’équipe de Cache Azure et d’autres membres de la Communauté de cet article.
- Pour atteindre un public plus large, publiez une question sur le [forum MSDN d’ateliers DevTest Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs), puis faites appel à l’équipe Azure DevTest ateliers et d’autres membres de la Communauté.
- Pour effectuer une demande de fonctionnalité, soumettez vos demandes et idées [Azure DevTest ateliers utilisateur vocale](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Pourquoi dois-je utiliser Azure DevTest ateliers ? 
Ateliers DevTest Azure peuvent enregistrer votre temps d’équipe et l’argent. Les développeurs peuvent créer leurs propres environnements à l’aide de plusieurs bases différentes et utiliser des objets pour rapidement déployer et configurer les applications. À l’aide de formules et les images personnalisées, machines virtuelles peut être enregistrés en tant que modèles et reproduire facilement. En outre, ateliers offrent plusieurs stratégies configurables qui permettent aux administrateurs de laboratoire réduire le gaspillage et gérer des environnements d’une équipe. Ces politiques incluent arrêt automatique, seuil de coût, machines virtuelles maximum par utilisateur et les tailles de mémoire virtuelle maximales. Pour une explication plus approfondie d’Azure DevTest ateliers, lisez la [vue d’ensemble](devtest-lab-overview.md) ou regardez la [vidéo d’introduction](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>Que signifie « sérénité, libre-service » ?
« Sérénité, libre-service » signifie que les développeurs et les testeurs créent leurs propres environnements selon vos besoins, et les administrateurs ont la sécurité de savoir que Azure DevTest ateliers permet de minimiser perdent et contrôler les coûts. Les administrateurs peuvent spécifier les formats de machine virtuelle sont autorisés, le nombre maximal de machines virtuelles, et lorsque les machines virtuelles sont démarrés et arrêtés. Ateliers DevTest Azure facilite également surveiller les coûts et définir des alertes pour rester informé de l’utilisation des ressources du laboratoire. 

### <a name="how-can-i-use-azure-devtest-labs"></a>Comment puis-je utiliser Azure DevTest ateliers ? 
Ateliers DevTest Azure est utile à tout moment nécessitent développement ou environnements de test, et que vous voulez les reproduire rapidement et/ou les gérer avec des stratégies de réduction des coûts. 

Voici quelques scénarios qui nos clients utilisent Azure DevTest ateliers pour : 

- Gestion des environnements de développement et de test au même endroit, utilisant les stratégies pour réduire les coûts et des images personnalisées pour partager des générations au sein de l’équipe.
- Développement d’une application à l’aide des images personnalisées pour enregistrer l’état de disque dans toutes les étapes de développement.
- Suivi des coûts par rapport à l’avancement. 
- Création d’environnements de test masse pour test d’assurance qualité.
- À l’aide des objets et des formules pour configurer facilement et reproduire une application sur différents environnements. 
- Distribuer des machines virtuelles pour hackathons (travail collaboratif développement ou test), puis puis facilement désactivation des comptes les lors de l’événement. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Comment de facturation pour Azure DevTest ateliers ? 
Ateliers DevTest Azure est un service gratuit, ce qui signifie que création ateliers et la configuration des stratégies, des modèles et des objets sont gratuit. Vous payez uniquement pour les ressources Azure utilisés au sein de votre ateliers, tels que machines virtuelles comptes de stockage et réseaux virtuels. Pour plus d’informations sur le coût des ressources de laboratoire, lisez [Azure DevTest ateliers tarifs](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quels sont les niveaux de sécurité différent dans Azure DevTest ateliers ?  
Accès de sécurité est déterminé par le [Contrôle d’accès Azure Role-Based (RBAC)](../active-directory/role-based-access-built-in-roles.md). Pour mieux comprendre le fonctionnement de l’accès, il est utile de comprendre les différences entre une autorisation et un rôle d’une étendue telle que définie par RBAC.

- **Autorisation** - une autorisation est un accès définie pour une action spécifique. Par exemple, une autorisation peut être accès en lecture à toutes virtuelles machines. 
- **Rôle** - un rôle est un ensemble d’autorisations qui peuvent être groupés et affectée à un utilisateur. Par exemple, un « propriétaire de l’abonnement » a accès à toutes les ressources au sein d’un abonnement. 
- **Étendue** - une étendue est un niveau dans la hiérarchie des ressources Azure. Par exemple, une étendue peut être un groupe de ressources ou un seul laboratoire ou tout l’abonnement. 
 
Dans le cadre d’Azure DevTest ateliers, il existe deux types de rôles pour définir des autorisations utilisateur : laboratoire propriétaire et utilisateur atelier.

- **Propriétaire de laboratoire** - un propriétaire atelier dispose de l’accès à toutes les ressources dans le laboratoire. Par conséquent, ils peuvent modifier les stratégies, lire et écrire les ordinateurs virtuels, modifier le réseau virtuel et ainsi de suite. 
- **Utilisateur atelier** - un utilisateur atelier peut afficher toutes les ressources de laboratoire, tels que machines virtuelles et des stratégies de réseaux virtuels, mais ils ne peut pas modifier les stratégies ou les machines virtuelles créées par d’autres utilisateurs. Il est également possible de créer des rôles personnalisés dans Azure DevTest ateliers, et apprenez à effectuer dans l’article, [accorder des autorisations utilisateur aux stratégies de laboratoire spécifique](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Dans la mesure où les étendues sont hiérarchiques, lorsqu’un utilisateur dispose des autorisations à une certaine étendue, ils reçoivent automatiquement les autorisations au niveau de chaque portée de niveau inférieur comprises. Par exemple, si un utilisateur reçoit le rôle de propriétaire de l’abonnement, puis ils ont accès à toutes les ressources dans un abonnement. Ces ressources comprennent toutes les machines virtuelles, tous les réseaux virtuels et tous les ateliers. Par conséquent, un propriétaire de l’abonnement hérite automatiquement le rôle du propriétaire du laboratoire. Toutefois, l’inverse n’est pas vrai. Propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure que le niveau d’abonnement. Par conséquent, un propriétaire atelier n’est pas en mesure de voir machines virtuelles ou des réseaux virtuels ou toutes les ressources qui sont trouvent en dehors du laboratoire. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Comment créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?
Un article exhaustif sur la façon de créer des rôles personnalisés et attribuez des autorisations à ce rôle sont accessibles ici. Voici un exemple d’un script qui crée le rôle DevTest ateliers avancés « utilisateurs », qui dispose des autorisations nécessaires pour démarrer et arrêter tous les ordinateurs virtuels dans le laboratoire :
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest ateliers s’intègre avec mon toolchain CI/CD ? 
Si vous utilisez VSTS, il est une [extension de Azure DevTest ateliers tâches](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) qui permet de vous permettent d’automatiser votre pipeline de version dans Azure DevTest ateliers. Voici quelques-unes des utilisations de cette extension :

- Créer et déployer automatiquement une machine virtuelle et configurez-le avec la dernière version à l’aide de la copie du fichier Azure ou PowerShell VSTS tâches. 
- Saisie automatique de l’état d’un ordinateur virtuel après le test pour reproduire un bogue sur la même machine virtuelle des recherches supplémentaires. 
- Suppression de la machine virtuelle à la fin du pipeline release lorsqu’elle n’est plus nécessaire. 

Les billets de blog suivants fournissent des instructions et des informations sur l’utilisation de l’extension VSTS :
 
- [Ateliers DevTest Azure – extension VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Déploiement d’une nouvelle machine virtuelle dans un AzureDevTestLab existant à partir de VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [À l’aide de la gestion des versions VSTS pour les déploiements continues à AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Pour d’autres toolchains CI/CD, tous les scénarios mentionnés précédemment grâce à l’extension de tâches VSTS peuvent être réalisés de la même façon au long du déploiement de [modèles Azure le Gestionnaire de ressources](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) à l’aide des [applets de commande PowerShell Azure](../resource-group-template-deploy.md) et [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Vous pouvez également utiliser [API REST pour DevTest ateliers](http://aka.ms/dtlrestapis) pour intégrer votre toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Pourquoi ne puis-je pas voir certains ordinateurs virtuels dans la carte de Machines virtuelles Azure se dans Azure DevTest ateliers ?
Lorsqu’une machine virtuelle est créée dans Azure DevTest ateliers, autorisation est attribué à accéder à cette dernière. Vous ne pouvez pour l’afficher dans la carte ateliers et la carte de **Machines virtuelles** . Les utilisateurs du rôle ateliers DevTest peuvent voir toutes les machines virtuelles créées dans l’atelier via carte de **toutes les Machines virtuelles** du laboratoire. Toutefois, les utilisateurs du rôle ateliers DevTest ne sont pas automatiquement accordées accès en lecture aux ressources machine virtuelle qu’ils ont créés. Par conséquent, ces machines virtuelles ne sont pas affichés dans la carte de **Machines virtuelles** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Quelle est la différence entre des images personnalisées et les formules ? 
Une image personnalisée est un disque dur virtuel (disque dur virtuel), tandis qu’une formule est une image que vous pouvez configurer avec des paramètres supplémentaires que vous pouvez enregistrer et reproduire. Une image personnalisée peut être préférable si vous souhaitez rapidement créer des environnements plusieurs avec la même image de base, immuable. Une formule peut être préférable si vous voulez reproduire la configuration de votre machine virtuelle avec les dernières bits, un sous-réseau virtuel ou une taille spécifique. Pour une explication plus approfondie, voir l’article de la [comparaison des images personnalisées et les formules dans DevTest ateliers](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Comment créer plusieurs machines virtuelles à partir du modèle même à la fois ? 
Vous pouvez utiliser [l’extension de tâches VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [Générer un modèle de gestionnaire de ressources Azure](devtest-lab-add-vm-with-artifacts.md#save-arm-template) lors de la création d’une machine virtuelle et [Déployez le modèle de gestionnaire de ressources Azure à partir de Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Comment déplacer mes machines virtuelles Azure existantes dans mon laboratoire Azure DevTest ateliers ? 
Nous allons conception d’une solution déplacer directement des machines virtuelles au centre de recherche DevTest Azure, mais actuellement vous pouvez copier vos machines virtuelles existantes au centre de recherche Azure DevTest comme suit : 

1. Copiez le fichier de disque dur virtuel de votre machine virtuelle existant à l’aide de ce [script Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Créer l’image personnalisée](devtest-lab-create-template.md) au sein de votre laboratoire Azure DevTest ateliers. 
1. Créer une machine virtuelle dans le laboratoire à partir de votre image personnalisée 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Puis-je joindre plusieurs disques à mes machines virtuelles ? 
Connexion de plusieurs disques aux machines virtuelles est pris en charge.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Comment automatiser le processus de téléchargement de fichiers de disque dur virtuel pour créer des images personnalisées ? 
Il existe deux possibilités :

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) peut servir à copier ou télécharger des fichiers de disque dur virtuel sur le compte de stockage associé à l’atelier.
- [Explorateur de stockage de Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui s’exécute sur Windows, OSX et Linux.   
 
Pour trouver le compte de stockage de destination associé à votre laboratoire, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Sélectionnez **Groupes de ressources** dans le volet gauche. 
1. Recherchez et sélectionnez le groupe de ressources associé à votre laboratoire. 
1. Sur la carte de la **vue d’ensemble** , sélectionnez un des comptes de stockage. 
1. Sélectionner **des objets BLOB**.
1. Recherchez les téléchargements dans la liste. Le cas contraire, revenez à l’étape 4 et essayer un autre compte de stockage.
1. Utiliser l' **URL** en tant que votre destination dans votre commande AzCopy.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Comment puis-je automatiser le processus de suppression de tous les ordinateurs virtuels dans mon laboratoire ?

Outre la suppression des machines virtuelles à partir de votre dans le portail Azure, vous pouvez supprimer tous les ordinateurs virtuels dans votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, simplement modifier les valeurs de paramètre sous le commentaire **à modifier les valeurs** . Vous pouvez récupérer la `subscriptionId`, `labResourceGroup`, et `labName` valeurs à partir de la carte d’atelier dans le portail Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>Que sont les objets ? 
Objets sont les éléments personnalisables qui peuvent être utilisés pour déployer votre dernières bits ou vos outils de développement sur un ordinateur virtuel. Ils sont joints à votre machine virtuelle lors de la création en quelques clics simples et une fois que la machine virtuelle est mis en service, les objets déploiement et configurer votre machine virtuelle. Il existe plusieurs objets préexistants dans notre [référentiel Github public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mais vous pouvez facilement [créer vos propres objets](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Comment créer un laboratoire à partir d’un modèle de gestionnaire de ressources Azure ? 
Nous vous fournissons un [référentiel Github des modèles de gestionnaire de ressources Azure laboratoire](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) que vous pouvez déployer en tant que-est ou modifiez pour créer des modèles personnalisés pour votre ateliers. Chacun de ces modèles comporte un lien sur lequel vous pouvez cliquer pour déployer pratiques-se trouve sous votre propre abonnement Azure, ou vous pouvez personnaliser le modèle et [déployer à l’aide de PowerShell ou Azure infrastructure du langage commun](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Pourquoi mes ordinateurs virtuels créés dans différents groupes de ressources dont les noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ? 
Groupes de ressources créées de cette manière dans l’ordre pour Azure DevTest ateliers gérer les autorisations des utilisateurs et l’accès aux machines virtuelles. Vous pouvez déplacer la machine virtuelle à un autre groupe de ressources avec votre nom de votre choix, cela est donc pas recommandé. Nous travaillons sur cette amélioration pour autoriser plus de flexibilité.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Ateliers combien puis-je créer sous le même abonnement ? 
Il n’existe aucune limite spécifique sur le nombre d’ateliers pouvant être créées par abonnement. Toutefois, les ressources utilisées sont limitées par abonnement. Vous pouvez lire sur les [quotas et les limites imposées sur les abonnements Azure](../azure-subscription-service-limits.md) et [Comment faire pour augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>Machines virtuelles combien puis-je créer par atelier ? 
Il n’est pas limité spécifique sur le nombre de machines virtuelles pouvant être créées par atelier. Toutefois, le laboratoire prend en charge uniquement environ 40 machines virtuelles en cours d’exécution en même temps dans le stockage standard et 25 machines virtuelles qui s’exécutent simultanément dans le stockage premium. Nous travaillons actuellement sur l’augmentation de ces limites. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Comment partager un lien direct vers mon laboratoire ?

Pour partager un lien direct vers vos utilisateurs atelier, vous pouvez effectuer la procédure suivante.

1. Accédez à l’atelier dans le portail Azure.
2. Copiez l’URL atelier dans votre navigateur et partagez-le avec d’autres utilisateurs atelier. 

>[AZURE.NOTE] Si vos utilisateurs atelier sont des utilisateurs externes avec un [compte MSA](#what-is-a-microsoft-account) et qu’ils n’appartenant pas à Active directory de votre entreprise, ils peuvent recevoir un message d’erreur lorsque vous naviguez vers le lien fourni. S’ils reçoivent un message d’erreur, demandez-leur de cliquez sur son nom dans le coin supérieur droit du portail Azure et sélectionnez le répertoire où se trouve le laboratoire à partir de la section **annuaire** du menu.

### <a name="what-is-a-microsoft-account"></a>Qu’est un compte Microsoft ?

Un compte Microsoft est ce que vous utilisez pour presque toutes vos qu'actions avec les services et périphériques de Microsoft. Il s’agit d’une adresse de messagerie et le mot de passe que vous utilisez pour vous connecter à Skype, Outlook.com, OneDrive, Windows Phone et Xbox LIVE – et cela signifie que vos fichiers, les photos, les contacts et les paramètres peuvent suivre vous pour n’importe quel appareil. 

>[AZURE.NOTE] Compte Microsoft utilisé pour être appelé « Windows Live ID ».
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mon objet a échoué lors de la création de la machine virtuelle. Comment résoudre les il ? 
Consultez le billet de blog [comment résoudre les problèmes ignore les objets AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - rédigé par un de nos MVP - pour savoir comment obtenir les journaux concernant votre objet a échoué. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Pourquoi n’est pas mon réseau existant, virtuel l’enregistrement correctement ?  
Il est possible que votre nom de réseau virtuel contient des périodes. Si c’est le cas, essayez de supprimer les périodes ou les remplacer par des traits d’union et puis réessayez d’enregistrer le réseau virtuel.
