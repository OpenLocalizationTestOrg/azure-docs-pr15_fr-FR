<properties
   pageTitle="MATLAB clusters sur machines virtuelles | Microsoft Azure"
   description="Machines virtuelles Microsoft Azure permet de créer des clusters MATLAB Distributed Computing Server pour exécuter votre cluster accrue parallèles MATLAB les charges de travail"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Créer des clusters MATLAB Distributed Computing Server sur machines virtuelles Azure 

Machines virtuelles Microsoft Azure permet de créer un ou plusieurs clusters MATLAB Distributed Computing Server pour exécuter votre cluster accrue parallèles MATLAB les charges de travail. Installer le logiciel MATLAB Distributed Computing Server sur un ordinateur virtuel à utiliser comme une image de base et utiliser un modèle de démarrage rapide Azure ou un script PowerShell Azure (disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) pour déployer et gérer le cluster. Après le déploiement, connectez-vous au cluster pour exécuter vos charges de travail. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>À propos de MATLAB et MATLAB distribué Computing Server 

La plateforme [MATLAB](http://www.mathworks.com/products/matlab/) est optimisée pour résoudre les problèmes d’ingénierie et scientifiques. Utilisateurs MATLAB avec simulations à grande échelle et les tâches de traitement des données peuvent utiliser parallèle MathWorks computing produits pour accélérer leurs charges de travail cluster accrue en tirant parti des services de grille et clusters de calcul. [Boîte à outils informatique parallèle](http://www.mathworks.com/products/parallel-computing/) permet aux utilisateurs MATLAB paralléliser applications et tirer parti de plusieurs processeurs, cartes graphiques et clusters de calcul. [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) permet aux utilisateurs MATLAB d’utiliser plusieurs ordinateurs dans un cluster de calcul. 


À l’aide de machines virtuelles Azure, vous pouvez créer des clusters MATLAB Distributed Computing serveur qui ont les mêmes mécanismes disponibles à soumettre un travail en parallèle en tant que clusters en local, tels que des travaux interactifs, lots, tâches indépendantes et la communication tâches. À l’aide d’Azure conjointement avec la plateforme MATLAB comporte de nombreux avantages par rapport à la mise en service et à l’aide de traditionnel locaux matériel : une plage de machine virtuelle la taille, la création de clusters à la demande et de payer uniquement pour les ressources de calcul que vous utilisez et la possibilité de tester les modèles à l’échelle.  

## <a name="prerequisites"></a>Conditions préalables

* **Ordinateur client** - vous aurez besoin d’un ordinateur fonctionnant sous Windows client pour communiquer avec Azure et le cluster MATLAB Distributed Computing serveur après le déploiement. 

* **Azure PowerShell** - voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour l’installer sur votre ordinateur client. 

* **Abonnement azure** - si vous n’avez pas un abonnement, vous pouvez créer un [compte de libérer](https://azure.microsoft.com/free/) en quelques minutes. Pour clusters de grande taille, envisagez un abonnement paiement ou autres options d’achat. 

* **Quota de cœurs** - vous devrez peut-être le quota de base pour déployer un grand cluster plusieurs MATLAB Distributed Computing cluster ou Server. Pour augmenter un quota, [Ouvrez une demande de support client en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sans frais. 

* **MATLAB, Parallel Computing boîte à outils et des licences MATLAB Distributed Computing Server** - les scripts part du principe que le [Gestionnaire de licences hébergé MathWorks](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) est utilisé pour toutes les licences.  

* **Logiciel MATLAB Distributed Computing serveur** - sera installé sur un ordinateur virtuel qui sera utilisé comme l’image de machine virtuelle base pour le cluster machines virtuelles. 


## <a name="high-level-steps"></a>Étapes de niveau élevés

Pour utiliser des machines virtuelles Azure pour vos MATLAB Distributed Computing clusters, les étapes suivantes sont nécessaires. Obtenir des instructions détaillées sont dans la documentation fournie avec le modèle de démarrage rapide et les scripts sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Créer une image de machine virtuelle base**  
    * Téléchargez et installez le logiciel MATLAB Distributed Computing Server sur cet ordinateur virtuel. 

    >[AZURE.NOTE]Ce processus peut prendre quelques heures, mais vous ne devez le faire une fois à chaque version de MATLAB vous utilisez.   
    
2. **Créer un ou plusieurs clusters**  
    * Utilisez le script PowerShell fourni ou le modèle de démarrage rapide pour créer un cluster à partir de l’image de machine virtuelle base.   
    * Gérer les groupes en utilisant le script PowerShell fourni qui permet de répertorier, suspendre, reprendre et supprimer des clusters. 
 
## <a name="cluster-configurations"></a>Configurations de cluster 

Pour l’instant, le modèle et le script de création de cluster permettent de créer une seule topologie MATLAB Distributed Computing Server. Si vous le souhaitez, créez un ou plusieurs clusters supplémentaires, avec chaque cluster comportant un nombre différent de travail machines virtuelles, à l’aide de différentes tailles de machine virtuelle et ainsi de suite. 

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB client et cluster dans Azure 

Les nœuds de client MATLAB, MATLAB planificateur et nœuds MATLAB Distributed Computing Server « employé consultant » sont tous configurés comme machines virtuelles Azure dans un réseau virtuel, comme indiqué dans l’illustration suivante. 

![Topologie de cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Pour utiliser le cluster, connexion Bureau à distance vers le nœud client. Le nœud client s’exécute le client MATLAB. 

* Le nœud client a un partage de fichiers accessible par tous les travailleurs.

* Gestionnaire de licences hébergé MathWorks est utilisé pour les tests de licence pour tous les logiciels MATLAB. 

* Par défaut, un collaborateur MATLAB Distributed Computing serveur par cœur est créée sur le collaborateur machines virtuelles, mais vous pouvez spécifier un nombre quelconque. 


## <a name="use-an-azure-based-cluster"></a>Utiliser un Cluster basée sur Azure 

Comme avec d’autres types de clusters MATLAB Distributed Computing Server, vous devez utiliser le Gestionnaire de profil Cluster dans le client MATLAB (sur le client machine virtuelle) pour créer un profil de cluster planificateur MATLAB.

![Gestionnaire de profil cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des instructions détaillées déployer et gérer des groupes de serveur de l’informatique MATLAB distribué dans un Azure, voir le référentiel [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contenant les modèles et les scripts. 

* Accédez au [site MathWorks](http://www.mathworks.com/) pour des informations détaillées sur MATLAB et MATLAB Distributed Computing Server.
