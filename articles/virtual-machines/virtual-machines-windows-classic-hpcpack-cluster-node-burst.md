<properties
 pageTitle="Ajouter des nœuds rupture à un cluster HPC Pack | Microsoft Azure"
 description="Apprenez à développer un cluster HPC Pack dans Azure à la demande en ajoutant des instances de rôle de travail en cours d’exécution dans un service cloud"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Ajouter des nœuds à la demande « rupture » à un cluster HPC Pack dans Azure



Si vous configurez un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) dans Azure, vous souhaiterez permet à l’échelle rapidement la capacité cluster vers le haut ou vers le bas, sans conserver un ensemble de nœud de calcul préconfigurés machines virtuelles. Cet article vous explique comment ajouter des nœuds à la demande « rupture » (instances de rôle de travail en cours d’exécution dans un service cloud) en tant que ressources cluster à un nœud de tête dans Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Rupture nœuds][burst]

Les étapes décrites dans cet article vous permettent d’ajouter rapidement des nœuds Azure à un nuage HPC Pack nœud de tête machine virtuelle pour un déploiement preuve de concept ou un test. Les étapes principales sont les mêmes que les étapes « éclater vers Azure » pour ajouter la capacité à un cluster HPC Pack locaux de calcul cloud. Pour un didacticiel, voir [configurer un déploiement calculer cluster avec Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Pour des instructions détaillées et les considérations pour les déploiements de production, voir [Burst vers Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).


## <a name="prerequisites"></a>Conditions préalables

* **Nœud de tête HPC Pack déployée dans une machine virtuelle Azure** - vous pouvez utiliser un nœud de tête autonome machine virtuelle ou celui qui fait partie d’un cluster plus grand. Pour créer un nœud de tête autonome, voir [déployer un nœud de tête HPC Pack dans une machine virtuelle Azure](virtual-machines-windows-hpcpack-cluster-headnode.md). Pour les options de déploiement cluster HPC Pack automatisées, voir [Options pour créer et gérer un cluster de Windows HPC dans Azure avec Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Si vous utilisez le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour créer le cluster dans Azure, vous pouvez inclure les nœuds de rupture Azure dans votre déploiement automatisé. Voir les exemples de cet article.

* **Abonnement azure** - pour ajouter des nœuds Azure, vous pouvez choisir le même abonnement utilisé pour déployer le nœud de tête machine virtuelle, ou un autre abonnement (ou abonnements).

* **Quota de cœurs** - vous devrez peut-être augmenter le quota de cœurs, surtout si vous choisissez de déployer plusieurs nœuds Azure avec une taille de plusieurs cœurs. Pour augmenter un quota, [Ouvrez une demande de support client en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sans frais.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Étape 1 : Créer un service cloud et un compte de stockage pour les nœuds Azure

Utiliser le portail classique Azure ou outils équivalents pour configurer les ressources suivantes sont nécessaires pour déploiement les nœuds Azure :

* Un nouveau service cloud Azure
* Un nouveau compte de stockage Azure

>[AZURE.NOTE] Ne pas réutiliser un service cloud existant dans votre abonnement. 

**Considérations relatives à la**

* Configurer un service cloud distincte pour chaque modèle nœud Azure que vous prévoyez de créer. Toutefois, vous pouvez utiliser le même compte de stockage pour plusieurs modèles de nœud.

* Nous vous recommandons de localiser le service cloud et du compte de stockage pour le déploiement dans la même région Azure.




## <a name="step-2-configure-an-azure-management-certificate"></a>Étape 2 : Configurer un certificat de gestion Azure

Pour ajouter des nœuds Azure comme des ressources de calcul, vous avez besoin d’un certificat de gestion sur le nœud de tête et le téléchargement du certificat correspondant à l’abonnement Azure utilisé pour le déploiement.

Dans ce scénario, vous pouvez choisir le **Certificat de gestion de Azure HPC par défaut** qui HPC Pack installe et configure automatiquement sur le nœud principal. Ce certificat est utile pour tester les objectifs et les déploiements de preuve de concept. Pour utiliser ce certificat, téléchargez le 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack fichier du nœud de tête machine virtuelle à l’abonnement. Pour télécharger le certificat dans le [portail classique Azure](https://manage.windowsazure.com), cliquez sur **paramètres** > **Gestion des certificats**.

Pour obtenir des options supplémentaires configurer le certificat de gestion, voir [scénarios pour configurer le certificat de gestion Azure pour les déploiements Burst Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Étape 3 : Déploiement d’Azure nœuds au cluster



La procédure pour ajouter et démarrer des nœuds Azure dans ce scénario est généralement identique à la procédure avec un nœud de tête en local. Pour plus d’informations, voir les sections suivantes dans les [étapes à suivre pour déployer des nœuds de Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Créer un modèle de nœud Azure

* Ajouter des nœuds Azure pour le cluster HPC Windows

* Démarrer des nœuds (disposition) le Azure

Après avoir ajouté et commencer les nœuds, ils sont prêtes à utiliser pour exécuter les travaux du cluster.

Si vous rencontrez des problèmes lors du déploiement d’Azure nœuds, consultez [Résoudre les problèmes des déploiements de nœuds Azure avec Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser une taille d’instance cluster accrue pour les nœuds rupture, consultez les considérations dans [sur machines virtuelles A série H-série et cluster accrue](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Si vous voulez agrandir ou réduire les ressources informatiques Azure en fonction de la charge de travail cluster automatique, voir [automatiquement agrandir et réduire les ressources Azure cluster dans un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
