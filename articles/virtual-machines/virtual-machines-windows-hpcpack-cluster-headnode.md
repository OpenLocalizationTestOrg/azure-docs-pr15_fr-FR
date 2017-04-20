<properties
 pageTitle="Créer un nœud de tête HPC Pack dans une machine virtuelle Azure | Microsoft Azure"
 description="Découvrez comment utiliser le portail Azure et le modèle de déploiement du Gestionnaire de ressources pour créer un nœud de tête Microsoft HPC Pack dans une machine virtuelle Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Créer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure avec une image Marketplace


Utiliser une [image de machine virtuelle Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) à partir de la Azure Marketplace et le portail Azure pour créer le nœud principal d’un cluster HPC. Cette image machine virtuelle HPC Pack est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 mise à jour 3 préinstallés. Utilisez ce nœud principal pour une preuve de déploiement concept de HPC Pack dans Azure. Vous pouvez ensuite ajouter des nœuds de calcul sur le cluster pour exécuter les charges de travail HPC.



>[AZURE.TIP]Pour déployer un cluster HPC Pack complète dans Azure qui inclut le nœud de tête et les nœuds de calcul, nous vous recommandons d’utiliser une méthode automatisée. Options incluent le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) et le modèle de gestionnaire de ressources [cluster HPC Pack pour charges de travail de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) . Pour des modèles supplémentaires, voir [options de cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-options.md) . 


## <a name="planning-considerations"></a>Considérations sur la planification

Comme indiqué dans l’illustration suivante, vous déployez le nœud de tête HPC Pack dans un domaine Active Directory dans un réseau virtuel Azure.

![Nœud de tête HPC Pack][headnode]

* **Domaine active Directory** - le Pack HPC nœud de tête doit être joint à un domaine Active Directory dans Azure avant de commencer les services HPC sur l’ordinateur virtuel. Comme indiqué dans cet article, pour une preuve de déploiement concept, vous pouvez promouvoir la machine virtuelle créer pour le nœud de tête comme contrôleur de domaine avant de commencer les services HPC. Une autre option consiste à déployer un contrôleur de domaine distinct et forêt dans Azure auquel vous joignez le nœud de tête machine virtuelle.

* **Réseau virtuel azure** - lorsque vous utilisez le modèle de déploiement du Gestionnaire de ressources pour déployer le nœud de tête, vous indiquez ou créez un réseau virtuel Azure. Vous utilisez le réseau virtuel si vous avez besoin joindre le nœud de tête à un domaine Active Directory existant. Vous avez besoin également une version ultérieure pour ajouter le nœud de calcul machines virtuelles au cluster.

    
## <a name="steps-to-create-the-head-node"></a>Étapes à suivre pour créer le nœud de tête

Voici les étapes principales pour utiliser le portail Azure pour créer une machine virtuelle Azure pour le nœud de tête HPC Pack en utilisant le modèle de déploiement du Gestionnaire de ressources. 


1. Si vous voulez créer une nouvelle forêt Active Directory dans Azure avec contrôleur de domaine distinct machines virtuelles, une option consiste à utiliser un [modèle de gestionnaire de ressources](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Pour une preuve de déploiement concept simple, il est prêt ignorer cette étape et configurer le nœud de tête machine virtuelle elle-même comme un contrôleur de domaine. Cette option est décrite plus loin dans cet article.
    
2. Dans le [HPC Pack 2012 R2 dans Windows Server 2012 R2 page](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) sur le marché Azure, cliquez sur **créer un ordinateur virtuel**. 

3. Dans le portail, dans la page **HPC Pack 2012 R2 sur Windows Server 2012 R2** , sélectionnez le modèle de déploiement du **Gestionnaire de ressources** , puis sur **créer**.

    ![Image du Pack HPC][marketplace]

4. Utiliser le portail pour configurer les paramètres et créer la machine virtuelle. Si vous débutez avec Azure, suivez le didacticiel [créer une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-hero-tutorial.md). Pour une preuve de déploiement concept, vous pouvez généralement accepter la valeur par défaut ou les paramètres recommandés.

    >[AZURE.NOTE]Si vous voulez rejoindre le nœud de tête à un domaine Active Directory existant dans Azure, vérifiez que vous spécifiez le réseau virtuel pour ce domaine lorsque vous créez la machine virtuelle.
       
4. Après avoir créé la machine virtuelle et la machine virtuelle s’exécute, [vous connecter à la machine virtuelle](virtual-machines-windows-connect-logon.md) Bureau à distance. 

5. Participer à la machine virtuelle à une forêt de domaine existante ou créer une forêt de domaine sur l’ordinateur virtuel lui-même.

    * Si vous avez créé la machine virtuelle dans un réseau virtuel Azure avec une forêt de domaine existant, rejoindre la machine virtuelle à la forêt à l’aide des outils Gestionnaire de serveur ou Windows PowerShell standards. Redémarrez ensuite.

    * Si vous avez créé la machine virtuelle dans un nouveau réseau virtuel (sans une forêt existante de domaine), puis promouvoir la machine virtuelle comme un contrôleur de domaine. Suivez les étapes standards pour installer et configurer le rôle de Services de domaine Active Directory sur le nœud principal. Pour plus d’informations, consultez [installer un nouveau Windows Server 2012 forêt Active Directory](https://technet.microsoft.com/library/jj574166.aspx).

5. Une fois que la machine virtuelle est en cours d’exécution et fait partie d’une forêt Active Directory, démarrez les services HPC Pack comme suit :

    un. Se connecter au nœud de tête machine virtuelle à l’aide d’un compte de domaine est membre du groupe Administrateurs local. Par exemple, utilisez le compte d’administrateur que vous configurez lorsque vous avez créé le nœud de tête machine virtuelle.

    b. Pour une configuration de nœud de tête par défaut, démarrez Windows PowerShell en tant qu’administrateur et tapez les informations suivantes :

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Il peut prendre plusieurs minutes pour les services HPC Pack à démarrer.

    Pour les options de configuration de nœud de tête supplémentaires, tapez `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez maintenant travailler avec le nœud de tête de votre cluster HPC Pack. Par exemple, démarrez HPC Cluster Manager et compléter la [Liste des tâches de déploiement](https://technet.microsoft.com/library/jj884141.aspx).
* Si vous voulez augmenter le cluster de calcul capacité à la demande, ajouter des [nœuds rupture Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) dans un service cloud. 

* Essayez d’exécuter une charge de travail de test sur le cluster. Pour obtenir un exemple, voir le HPC Pack [guide de mise en route](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
