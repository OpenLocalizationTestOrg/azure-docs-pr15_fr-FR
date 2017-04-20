<properties
 pageTitle="Configurer un cluster RDMA Windows pour exécuter des applications MPI | Microsoft Azure"
 description="Apprenez à créer un cluster Windows HPC Pack avec une taille de H16r, H16mr, A8 ou A9 machines virtuelles à utiliser le réseau RDMA Azure pour exécuter des applications MPI."
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
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurer un cluster RDMA Windows avec HPC Pack à exécuter des applications MPI

Configurer un cluster Windows RDMA dans Azure avec [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) et [H-série ou cluster accrue instances A série](virtual-machines-windows-a8-a9-a10-a11-specs.md) à exécuter en parallèle Message transmission des applications MPI (Interface). Lorsque vous définissez des nœuds prenant en charge RDMA, basée sur Windows Server dans un cluster HPC Pack, des applications MPI communiquent efficacement sur une latence faible, réseau haut débit dans Azure basée sur la technologie d’accès (RDMA) à distance direct à la mémoire.

Si vous souhaitez exécuter les charges de travail MPI sur machines virtuelles Linux qui accèdent au réseau Azure RDMA, voir [configurer un cluster Linux RDMA afin d’exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>Options de déploiement de cluster HPC Pack
Microsoft HPC Pack est un outil sans frais supplémentaires pour créer des HPC clusters locaux ou fourni dans Azure pour exécuter des applications Windows ou Linux HPC. HPC Pack inclut un environnement d’exécution de l’implémentation Microsoft du Message en passant Interface pour Windows (MS-MPI). Lorsqu’il est utilisé avec des instances RDMA compatibles avec exécutant un système d’exploitation Windows Server pris en charge, HPC Pack fournit une option efficace pour exécuter des applications Windows MPI qui accèdent au réseau RDMA Azure. 

Cet article présente deux scénarios et des liens vers des instructions détaillées pour configurer un cluster Winodws RDMA avec Microsoft HPC Pack. 

* Scénario 1. Déployer des instances de rôle de collaborateur cluster accrue (PaaS)

* Scénario 2. Déployer des nœuds de calcul dans cluster accrue machines virtuelles (IaaS)

Pour des conditions requises générales utiliser des instances cluster accrue avec Windows, voir [à propos de machines virtuelles A série H-série et cluster accrue](virtual-machines-windows-a8-a9-a10-a11-specs.md) .



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scénario 1. Déployer des instances de rôle de collaborateur cluster accrue (PaaS)

À partir d’un cluster HPC Pack existant, ajouter des ressources supplémentaires cluster dans les instances de rôle de collaborateur Azure (nœuds Azure) en cours d’exécution dans un service cloud (PaaS). Cette fonctionnalité, également appelée « burst vers Azure » à partir de HPC Pack, prend en charge une plage de taille pour les instances de rôle de collaborateur. Lorsque vous ajoutez les nœuds Azure, spécifiez simplement une des tailles RDMA compatibles avec.

Voici les considérations et procédures éclater à RDMA compatibles avec Azure instances d’un existant (en général en local) cluster. Procédures similaires permet d’ajouter des instances de rôle de collaborateur à un nœud de tête HPC Pack est déployé dans une machine virtuelle Azure.

>[AZURE.NOTE] Pour un didacticiel éclater vers Azure avec Pack HPC, voir [configurer un cluster hybride avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Notez les considérations les étapes suivantes qui s’appliquent spécifiquement à RDMA compatibles avec les nœuds Azure.

![Burst vers Azure][burst]

### <a name="steps"></a>Étapes

4. **Déployer et configurer un nœud principal HPC Pack 2012 R2**

    Télécharger le package d’installation HPC Pack plus récente à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Pour obtenir des instructions pour préparer un déploiement d’Azure rupture et configuration requise, voir [HPC Pack Guide de démarrage](https://technet.microsoft.com/library/jj884144.aspx) et [rupture sur des Instances de travail Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurer un certificat de gestion de l’abonnement Azure**

    Configurer un certificat pour sécuriser la connexion entre le nœud de tête et Azure. Procédures et des options, voir [scénarios pour configurer le certificat de gestion Azure pour HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Pour les déploiements de test, HPC Pack installe un par défaut HPC Azure Gestion des certificats Microsoft vous pouvez télécharger rapidement à votre abonnement Azure.

6. **Créer un nouveau service cloud et un compte de stockage**

    Utiliser le portail classique Azure pour créer un service cloud et un compte de stockage pour le déploiement dans une zone dans laquelle les instances RDMA compatibles avec sont disponibles.

7. **Créer un modèle de nœud Azure**

    Utiliser l’Assistant créer un nœud modèle dans le Gestionnaire de Cluster HPC. Pour obtenir la procédure, voir [créer un modèle d’Azure nœud](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) « Étapes pour déployer Azure nœuds avec Microsoft HPC Pack ».

    Pour les tests préliminaires, nous vous recommandons de configuration d’une stratégie de disponibilité manuel dans le modèle.

8. **Ajouter des nœuds au cluster**

    Utiliser le nœud Assistant Ajouter dans le Gestionnaire de Cluster HPC. Pour plus d’informations, voir [Ajouter des nœuds au Cluster HPC Windows Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Lorsque vous spécifiez la taille des nœuds, sélectionnez une des tailles instance RDMA compatibles avec.
    
    >[AZURE.NOTE]Dans chaque rupture de déploiement d’Azure avec les instances cluster accrue, HPC Pack déploie automatiquement un minimum de 2 instances RDMA compatibles avec (par exemple, A8) en tant que nœuds de proxy, en plus des instances de rôle de collaborateur Azure que vous spécifiez. Les nœuds proxy utilisent cœurs qui sont affectées à l’abonnement et entraînent frais ainsi que les instances de rôle de collaborateur Azure.

9. **Démarrez (disposition) les nœuds et placer en ligne à l’exécution de tâches**

    Sélectionnez les nœuds et utiliser l’action **Démarrer** dans le Gestionnaire de Cluster HPC. Lors de la mise en service est terminée, sélectionnez les nœuds et utiliser l’action **Mettre en ligne** dans le Gestionnaire de Cluster HPC. Les nœuds sont prêts à exécuter des tâches.

10. **Soumettre des tâches au cluster**

    Outils de soumission tâche HPC Pack permet d’exécuter les travaux du cluster. Voir [Microsoft HPC Pack : gestion du travail](http://technet.microsoft.com/library/jj899585.aspx).

11. **Arrêter (annulation) les nœuds**

    Lorsque vous avez terminé les tâches en cours d’exécution, effectuez les nœuds en mode hors connexion et utiliser l’action **Arrêter** dans le Gestionnaire de Cluster HPC.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scénario 2. Déployer des nœuds de calcul dans cluster accrue machines virtuelles (IaaS)

Dans ce scénario, vous déployez le nœud de tête HPC Pack et cluster nœuds sur machines virtuelles joints à un domaine Active Directory dans un réseau virtuel Azure. HPC Pack fournit un certain nombre d' [options de déploiement de machines virtuelles Azure](virtual-machines-linux-hpcpack-cluster-options.md), notamment les scripts de déploiement automatisé et les modèles de démarrage rapide Azure. Par exemple, les considérations et les étapes ci-dessous vous guident pour utiliser le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour automatiser la majeure partie de ce processus.

![Cluster dans machines virtuelles Azure][iaas]



### <a name="steps"></a>Étapes

1. **Créer un nœud de tête cluster et calculer des machines virtuelles nœud en exécutant le script de déploiement HPC Pack IaaS sur un ordinateur client**

    Télécharger le package HPC Pack IaaS déploiement Script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Pour préparer l’ordinateur client, créez le fichier de configuration de script et exécuter le script, voir [créer un Cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Pour déployer des nœuds de calcul prenant en charge RDMA, notez les remarques supplémentaires suivantes :
    
    * **Réseau virtuel** - spécifier un réseau virtuel dans une zone dans laquelle la taille d’instance RDMA compatibles avec vous voulez utiliser est disponible.

    * **Système d’exploitation Windows Server** - prise en charge connectivité RDMA, spécifiez un système d’exploitation Windows Server 2012 R2 ou Windows Server 2012 pour le nœud de calcul machines virtuelles.

    * **Les services en nuage** - nous vous recommandons de déploiement de votre nœud de tête dans le service d’un nuage et vos nœuds de calcul dans un service de nuage différent.

    * **Taille de nœud de tête** : pour ce scénario, vous pouvez une taille d’au moins A4 (très grande police) pour le nœud principal.

    * **Extension HpcVmDrivers** - le script de déploiement installe l’Agent de machine virtuelle Azure et l’extension HpcVmDrivers automatiquement lorsque vous déployez taille A8 ou A9 nœuds avec un système d’exploitation Windows Server. HpcVmDrivers installe des pilotes sur le nœud de calcul machines virtuelles afin qu’ils peuvent se connecter au réseau RDMA.

    * **Configuration du réseau** - le script de déploiement configure automatiquement le cluster HPC Pack topologie 5 (tous les nœuds sur le réseau d’entreprise). Cette topologie est nécessaire pour tous les déploiements de cluster HPC Pack dans machines virtuelles. Ne modifiez pas la topologie de réseau cluster ultérieurement.

2. **Placer les nœuds de calcul en ligne à l’exécution de tâches**

    Sélectionnez les nœuds et utiliser l’action **Mettre en ligne** dans le Gestionnaire de Cluster HPC. Les nœuds sont prêts à exécuter des tâches.

3. **Soumettre des tâches au cluster**

    Se connecter au nœud de tête à soumettre des tâches, ou configurer un ordinateur local pour effectuer cette action. Pour plus d’informations, voir [Soumettre des travaux à un cluster HPC dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Mettre les nœuds hors connexion et arrêter (libérer) les**

    Lorsque vous avez terminé les tâches en cours d’exécution, prendre les nœuds en mode hors connexion dans le Gestionnaire de Cluster HPC. Puis, utilisez les outils de gestion Azure pour les arrêter.



## <a name="run-mpi-applications-on-the-cluster"></a>Exécuter des applications MPI sur le cluster

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemple : Exécuter mpipingpong sur un cluster HPC Pack

Pour vérifier un déploiement HPC Pack des instances RDMA compatibles avec, exécutez la commande **mpipingpong** HPC Pack sur le cluster. **MPIPingPong** envoie des paquets de données entre des nœuds appariés à plusieurs reprises pour calculer les mesures de débit et de latence ainsi que les statistiques réseau activé RDMA application. Cet exemple montre un modèle par défaut pour l’exécution d’une tâche MPI (dans ce cas, **mpipingpong**) à l’aide de la commande **mpiexec** cluster.

Cet exemple suppose que vous avez ajouté des nœuds Azure dans une configuration « rupture vers Azure » ([scénario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Si vous avez déployé HPC Pack sur un cluster de machines virtuelles Azure, vous devez modifier la syntaxe de la commande pour spécifier un groupe de nœud différent et de définir des variables d’environnement supplémentaires pour diriger le trafic réseau au réseau RDMA.


Pour exécuter mpipingpong sur le cluster :


1. Sur le nœud principal ou sur un ordinateur client correctement configuré, ouvrez une invite de commande.

2. Afin d’évaluer la latence entre les paires de nœuds dans un déploiement d’Azure rupture de 4 nœuds, tapez la commande suivante pour soumettre une tâche pour exécuter mpipingpong avec une taille de petits paquets et un grand nombre d’itérations :

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    La commande renvoie l’ID de la tâche qui est soumise.

    Si vous avez déployé le cluster HPC Pack déployé sur machines virtuelles Azure, spécifiez un groupe de nœud contenant nœud machines virtuelles déployées dans un service cloud unique de calcul et modifiez la commande **mpiexec** comme suit :

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Lorsque la tâche est terminée, pour afficher les résultats (dans ce cas, le résultat de la tâche 1 du travail), tapez les informations suivantes

    ```
    task view <JobID>.1
    ```

    où &lt; *identificateur* &gt; est l’ID de la tâche qui a été envoyée.

    La sortie inclut les résultats de latence semblables au suivant.

    ![Latence de pong ping][pingpong1]

4. Pour évaluer débit entre les paires de nœuds rupture Azure, tapez la commande suivante pour soumettre une tâche pour exécuter **mpipingpong** avec une taille grand paquet et un petit nombre d’itérations :

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    La commande renvoie l’ID de la tâche qui est soumise.

    Sur un cluster HPC Pack déployé sur machines virtuelles Azure, modifiez la commande comme indiqué à l’étape 2.

5. Lorsque la tâche est terminée, pour afficher les résultats (dans ce cas, le résultat de la tâche 1 du travail), tapez les informations suivantes :

    ```
    task view <JobID>.1
    ```

  La sortie inclut les résultats de débit semblables au suivant.

  ![Débit de pong ping][pingpong2]


### <a name="mpi-application-considerations"></a>Considérations sur l’application MPI


Voici des considérations relatives à des applications MPI avec Pack HPC dans Azure. Certaines s’appliquent uniquement à des déploiements des nœuds Azure (instances de rôle de collaborateur ajoutées dans une configuration « rupture vers Azure »).

* Instances de rôle de collaborateur dans un service cloud sont régulièrement configurés sans préavis Azure (par exemple, de la maintenance du système, ou en cas d’échec d’une instance). Si une instance est configurée pendant l’exécution d’une tâche MPI, l’instance perd ses données et revient à l’état lorsqu’il a été les déployé tout d’abord, qui peut entraîner l’échec du travail MPI. Les nœuds plus que vous utilisez pour une seule tâche MPI et plus le travail s’exécute, probable qu’une des instances est sera configurée lors d’une tâche est en cours d’exécution. Vous devez également envisager si vous désignez un seul nœud dans le déploiement comme un serveur de fichiers.


* Pour exécuter des travaux MPI dans Azure, vous n’êtes pas obligé d’utiliser les instances RDMA compatibles avec. Vous pouvez utiliser n’importe quelle taille instance est pris en charge par HPC Pack. Toutefois, les instances RDMA compatibles avec sont recommandées pour les tâches MPI relativement à grande échelle sensibles à la latence et la bande passante du réseau qui connecte les nœuds en cours d’exécution. Si vous utilisez d’autres tailles à l’exécution et la bande passante-sensibles à la latence MPI tâches, nous vous recommandons de petites tâches, dans lequel une seule tâche s’exécute sur uniquement quelques nœuds en cours d’exécution.

* Applications déployées sur des instances Azure sont couvertes par les termes associés avec l’application. Contactez le fournisseur de n’importe quelle application commerciale pour les restrictions de licences ou toute autre pour en cours d’exécution dans le cloud. Pas tous les fournisseurs offrent des licences de paiement.


* Instances Azure doivent le programme d’installation supplémentaire d’accès local nœuds, partage et les serveurs de licences. Par exemple, pour activer les nœuds Azure à accéder à un serveur de licences en local, vous pouvez configurer un réseau virtuel de site à Azure.


* Pour exécuter des applications MPI sur des instances Azure, enregistrer chaque application MPI avec le pare-feu Windows sur les instances en exécutant la commande **hpcfwutil** . Cela permet de communications MPI se déroule sur un port est affecté dynamiquement par le pare-feu.

    >[AZURE.NOTE] Pour rupture aux déploiements Azure, vous pouvez également configurer une commande d’exception de pare-feu pour exécuter automatiquement sur tous les nouveaux nœuds Azure auquel sont ajoutés à votre cluster. Une fois que vous exécutez la commande **hpcfwutil** et vérifiez que votre application fonctionne, ajoutez la commande à un script de démarrage pour vos nœuds Azure. Pour plus d’informations, voir [utiliser un Script de démarrage pour les nœuds Azure](https://technet.microsoft.com/library/jj899632.aspx).



* HPC Pack utilise la variable d’environnement cluster CCP_MPI_NETMASK pour spécifier une plage d’adresses acceptables pour les communications MPI. À partir de HPC Pack 2012 R2, la variable d’environnement CCP_MPI_NETMASK cluster affecte uniquement la communication MPI entre les nœuds de calcul à un domaine cluster (localement ou en machines virtuelles Azure). La variable est ignorée par les nœuds ajoutés dans une rupture à configuration Azure.


* Impossible d’exécuter des travaux MPI pour plusieurs instances Azure déployées dans les services en nuage autre (par exemple, dans la rupture pour les déploiements Azure avec nœud différents modèles ou des nœuds de calcul Azure machine virtuelle déployées dans plusieurs services de cloud). Si vous avez plusieurs déploiements nœud Azure qui sont démarrés avec les modèles de nœud différent, la MPI doit s’exécuter sur qu’un seul ensemble de nœuds Azure.


* Lorsque vous ajoutez des nœuds Azure à votre cluster et mettre en ligne, le Service de planificateur de tâches HPC essaie immédiatement de démarrer des tâches sur les nœuds. Si seulement une partie de votre charge de travail peut s’exécuter sur Azure, vérifiez que vous mettez à jour ou créez des modèles de tâche pour définir les types de tâches peuvent s’exécutent sur Azure. Par exemple, pour vous assurer que les travaux soumis avec un modèle de tâche exécutent uniquement sur les nœuds Azure, ajoutez la propriété nœud Groups au modèle de tâche et sélectionnez AzureNodes comme la valeur souhaitée. Pour créer des groupes personnalisés pour vos nœuds Azure, utilisez l’applet de commande PowerShell de HPC ajouter HpcGroup.


## <a name="next-steps"></a>Étapes suivantes

* Comme alternative à l’utilisation de HPC Pack, développer avec le service Azure lot pour exécuter des applications MPI sur des pools gérées de nœuds de calcul dans Azure. Voir [utiliser plusieurs instances tâches pour exécuter des applications en passant Interface MPI (Message) dans Azure lot](../batch/batch-mpi.md).

* Si vous souhaitez exécuter des applications qui accèdent au réseau Azure RDMA Linux MPI, voir [configurer un cluster Linux RDMA afin d’exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png