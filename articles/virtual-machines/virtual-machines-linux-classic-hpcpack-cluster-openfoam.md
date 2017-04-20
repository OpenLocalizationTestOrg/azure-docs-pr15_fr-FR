<properties
 pageTitle="Exécutez OpenFOAM avec HPC Pack sur machines virtuelles Linux | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et exécuter une tâche OpenFOAM sur plusieurs nœuds de calcul Linux via un réseau RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Exécutez OpenFoam avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure

Cet article vous explique une des façons d’exécuter OpenFoam dans des machines virtuelles Azure. Dans ce cas, vous déployez un cluster Microsoft HPC Pack avec des nœuds de calcul Linux sur Azure et exécuter une [OpenFoam](http://openfoam.com/) des tâches avec Intel MPI. Vous pouvez utiliser RDMA compatibles avec Azure machines virtuelles pour les nœuds de calcul, afin que les nœuds de calcul communiquent sur le réseau RDMA Azure. Autres options pour exécuter OpenFoam dans Azure incluent images professionnelle préconfigurés disponibles sur le marché, tels que [OpenFoam 2.3 sur CentOS 6 de UberCloud](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)et en exécutant sur [Lot Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (pour ouvrir une opération de champ et Manipulation) est un logiciel open source CFD fluides () qui est utilisé largement dans ingénierie et scientifiques, dans les organisations commerciale et académiques. Il inclut des outils pour le maillage, notamment snappyHexMesh, un mailleur parallélisée pour géométries CAO complexes et avant et après traitement. Presque toutes les processus s’exécutent en parallèle, permettant aux utilisateurs de tirer pleinement parti de matériel informatique à leur disposition.  

Microsoft HPC Pack fournit les fonctionnalités pour exécuter HPC à grande échelle et applications parallèles, y compris les applications MPI, sous groupes de machines virtuelles Microsoft Azure. HPC Pack prend également en charge en cours d’exécution Linux HPC applications sur Linux nœud machines virtuelles déployées dans un cluster HPC Pack de calcul. Pour obtenir une présentation à l’aide des nœuds de calcul Linux avec Pack HPC, voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

>[AZURE.NOTE] Cet article explique comment exécuter une charge de travail Linux MPI avec HPC Pack. Il suppose que vous être familiarisé avec administration du système Linux et les charges de travail MPI en cours d’exécution sur les clusters Linux. Si vous utilisez des versions de MPI et OpenFOAM différente de celles présentées dans cet article, vous devrez peut-être modifier certaines étapes d’installation et de configuration. 

## <a name="prerequisites"></a>Conditions préalables

*   **Cluster HPC Pack avec Linux RDMA compatibles avec les nœuds de calcul** - déployer un cluster HPC Pack avec taille A8, A9, H16r ou Linux H16rm nœuds de calcul à l’aide d’un [modèle de gestionnaire de ressources Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou un [script PowerShell Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour les conditions préalables et les étapes d’une option. Si vous sélectionnez l’option de déploiement de script PowerShell, voir l’exemple de fichier de configuration dans les fichiers d’exemples à la fin de cet article. Utilisez cette configuration pour déployer un cluster de basée sur Azure HPC Pack constitué d’un nœud de tête A8 Windows Server 2012 R2 taille et 2 nœuds de calcul A8 SUSE Linux Enterprise Server 12 taille. Remplacer les valeurs appropriées pour les noms de votre abonnement et le service. 

    **Autres éléments à connaître**

    *   Pour Linux RDMA réseau conditions préalables dans Azure, voir [à propos des machines virtuelles A série H-série et cluster accrue](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    *   Si vous utilisez l’option de déploiement de script Powershell, déployer tous les nœuds de cluster Linux dans le service d’un nuage à utiliser la connexion de réseau RDMA.

    *   Après avoir déployé les nœuds Linux, connectez-vous en SSH pour effectuer d’autres tâches administratives. Découvrez les détails de connexion SSH pour chaque VM Linux dans le portail Azure.  
        
*   **Intel MPI** - exécuter OpenFOAM sur SLES 12 HPC nœuds dans Azure, vous devez installer le runtime Intel MPI bibliothèque 5 à partir du [site Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 est préinstallé sur images basées sur CentOS HPC).  Dans une étape ultérieure, le cas échéant, installez MPI Intel sur les nœuds de cluster Linux. Pour préparer cette étape, une fois enregistré avec Intel, suivez le lien dans le message de confirmation à la page web associée. Ensuite, copiez le lien de téléchargement du fichier .tgz pour la version appropriée d’Intel. Cet article est basé sur Intel MPI version 5.0.3.048.

*   **Pack Source OpenFOAM** - télécharger le logiciel du Pack de Source OpenFOAM pour Linux à partir du [site OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Cet article est basé sur version Pack Source 2.3.1, disponible au téléchargement en tant que OpenFOAM 2.3.1.tgz. Suivez les instructions plus loin dans cet article pour décompresser et de compilation OpenFOAM sur les nœuds de calcul Linux.

*   **EnSight** (facultatif) - pour afficher les résultats de votre simulation OpenFOAM, téléchargez et installez le programme de visualisation et l’analyse de [EnSight](https://www.ceisoftware.com/download/) . Gestion des licences et télécharger informations disponibles sur le site EnSight.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurer la confiance réciproque entre les nœuds de calcul

Exécution d’une tâche entre nœud sur plusieurs nœuds Linux requiert les nœuds d’eux approuver (en **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement Microsoft HPC Pack IaaS, le script configure automatiquement confiance réciproque permanente pour le compte d’administrateur que vous spécifiez. Pour les utilisateurs non administrateur que vous créez dans domaine du cluster, vous devez configurer temporaire confiance réciproque entre les nœuds lorsqu’une tâche est qui leur est attribuée et destroy la relation une fois que la tâche est terminée. Pour établir l’approbation pour chaque utilisateur, fournissent une paire de clés RSA pour le cluster HPC Pack utilise pour la relation d’approbation.

### <a name="generate-an-rsa-key-pair"></a>Générer une paire de clés RSA

Il est facile de générer une paire de clés RSA, qui contient une clé publique et une clé privée, en exécutant la commande **ssh keygen** Linux.

1.  Ouvrez une session sur un ordinateur Linux.

2.  Exécutez la commande suivante :

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Appuyez sur **entrée** pour utiliser les paramètres par défaut jusqu'à ce que la commande est terminée. Ne saisissez un mot de passe ici ; Lorsque vous y êtes invité pour un mot de passe, appuyez simplement sur **entrée**.

    ![Générer une paire de clés RSA][keygen]

3.  Placez-vous dans le répertoire ~/.ssh. La clé privée est stockée dans id_rsa et la clé publique dans id_rsa.pub.

    ![Clés privées et publiques][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Ajouter la paire de clés pour le cluster HPC Pack
1.  Établir une connexion Bureau à distance à votre nœud de tête avec votre compte d’administrateur HPC Pack (compte d’administrateur configurer lorsque vous avez exécuté le script de déploiement).

2. Procédures standard de Windows Server permet de créer un compte d’utilisateur de domaine dans le domaine Active Directory du cluster. Par exemple, utilisez l’outil utilisateur Active Directory et des ordinateurs sur le nœud principal. Les exemples de cet article part du principe que vous créez un utilisateur de domaine nommé hpclab\hpcuser.

3.  Créez un fichier nommé C:\cred.xml et copiez les données de clé RSA. Un exemple de fichier cred.xml est à la fin de cet article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Ouvrez une invite de commandes et entrez la commande suivante pour définir les données d’informations d’identification du compte hpclab\hpcuser. Vous utilisez le paramètre **extendeddata** pour transmettre le nom du fichier C:\cred.xml que vous avez créé pour les données de clé.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Cette commande se termine correctement sans sortie. Après avoir défini les informations d’identification pour les comptes d’utilisateurs que requis pour exécuter des travaux, stockez le fichier cred.xml dans un emplacement sécurisé, ou supprimez-le.

5.  Si vous avez généré la paire de clés RSA sur l’un de vos nœuds Linux, n’oubliez pas de supprimer les clés une fois que vous avez fini de les utiliser. Si HPC Pack détecte un fichier id_rsa existant ou id_rsa.pub, il ne configure pas confiance réciproque.

>[AZURE.IMPORTANT] Il n’est recommandé exécution d’un travail Linux en tant qu’un administrateur de cluster sur un cluster partagé, car une tâche présentée par un administrateur s’exécute sous le compte racine sur les nœuds Linux. Toutefois, une tâche soumise par un utilisateur non administrateur s’exécute sous un compte d’utilisateur Linux local avec le même nom que l’utilisateur de la tâche. Dans ce cas, HPC Pack confiance réciproque pour cet utilisateur Linux configure entre les nœuds affectées à la tâche. Vous pouvez configurer l’utilisateur Linux manuellement dans les nœuds Linux avant d’exécuter la tâche, ou HPC Pack crée automatiquement l’utilisateur lorsque la tâche est envoyée. Si HPC Pack crée l’utilisateur, HPC Pack supprime une fois que le travail est terminé. Pour réduire les menaces de sécurité, HPC Pack supprime les clés après l’achèvement du projet.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurer un partage de fichiers pour les nœuds Linux

Définir un partage PME standard sur un dossier sur le nœud principal. Pour autoriser les nœuds Linux accéder aux fichiers d’application avec un chemin d’accès courantes, monter le dossier partagé sur les nœuds Linux. Si vous le souhaitez, vous pouvez utiliser une autre option, par exemple un partage de fichiers Azure - recommandé pour les nombreux scénarios - ou un partage NFS de partage de fichiers. Consultez le fichier à partager des informations et des instructions détaillées dans [prise en main Linux cluster nœuds dans un Cluster de Pack HPC dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Créez un dossier sur le nœud de tête et partagez-le avec tous les participants en définissant des privilèges en lecture/écriture. Par exemple, partager C:\OpenFOAM sur le nœud de tête en tant que \\ \\SUSE12RDMA HN\OpenFOAM. Ici, *SUSE12RDMA HN* est le nom d’hôte du nœud principal.

2.  Ouvrez une fenêtre de Windows PowerShell et exécutez les commandes suivantes :

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /openfoam sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande montages le dossier partagé //SUSE12RDMA-HN/OpenFOAM sur les nœuds Linux avec dir_mode et file_mode bits comme 777. Le *nom d’utilisateur* et *mot de passe* dans la commande doivent être les informations d’identification d’un utilisateur sur le nœud principal.

>[AZURE.NOTE]Le «\`» symbole dans la deuxième commande est un symbole d’échappement pour PowerShell. «\`, » signifie le «, » (caractère virgule) est un composant de la commande.

## <a name="install-mpi-and-openfoam"></a>Installer MPI et OpenFOAM

Pour exécuter OpenFOAM comme une tâche MPI sur le réseau RDMA, vous devez compiler OpenFOAM avec les bibliothèques MPI Intel. 

Exécutez tout d’abord, plusieurs commandes **clusrun** pour installer les bibliothèques de MPI Intel (le cas échéant) et OpenFOAM sur les nœuds Linux. Utiliser le partage de nœud de tête configuré précédemment pour partager les fichiers d’installation parmi les nœuds Linux.

>[AZURE.IMPORTANT]Ces installation et compilation étapes sont des exemples. Vous devez maîtriser de l’administration du système Linux pour vous assurer que les bibliothèques et compilateurs dépendantes sont installés correctement. Vous devrez peut-être modifier certaines variables d’environnement ou d’autres paramètres de vos versions de MPI Intel et OpenFOAM. Pour plus d’informations, voir [Bibliothèque de MPI Intel pour Linux Installation Guide](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) et [Installation du Pack OpenFOAM Source](http://openfoam.org/download/2-3-1-source/) pour votre environnement.


### <a name="install-intel-mpi"></a>Installer Intel MPI

Enregistrez le package d’installation téléchargé pour MPI Intel (l_mpi_p_5.0.3.048.tgz dans cet exemple) dans C:\OpenFoam sur le nœud de tête afin que les nœuds Linux peuvent accéder à ce fichier à partir de /openfoam. Ensuite, exécutez **clusrun** pour installer Bibliothèque MPI Intel sur tous les nœuds Linux.

1.  Les commandes suivantes copiez le package d’installation et extraire vers /opt/intel sur chaque nœud.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Pour installer silencieusement Bibliothèque MPI Intel, utilisez un fichier silent.cfg. Vous pouvez trouver un exemple dans les fichiers d’exemples à la fin de cet article. Placez ce fichier dans le dossier partagé /openfoam. Pour plus d’informations sur le fichier silent.cfg, voir [Bibliothèque de MPI Intel pour Linux Installation en mode silencieux - Guide d’Installation](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Assurez-vous que vous enregistrez votre fichier silent.cfg au format texte avec Linux ligne fins (saut de ligne uniquement, pas CR saut de ligne). Cette étape permet de vérifier qu’il s’exécute correctement sur les nœuds Linux.

3.  Installez Bibliothèque MPI Intel en mode silencieux.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Configurer MPI

Pour le test, vous devez ajouter les lignes suivantes à la /etc/security/limits.conf sur chacun des nœuds Linux :


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Redémarrez les nœuds Linux une fois que vous mettez à jour le fichier limits.conf. Par exemple, utilisez la commande **clusrun** suivante :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Après avoir redémarré, assurez-vous que le dossier partagé est chargé en tant que /openfoam.

### <a name="compile-and-install-openfoam"></a>Compiler et installer OpenFOAM

Enregistrez le package d’installation téléchargé pour le Pack de Source OpenFOAM (OpenFOAM 2.3.1.tgz dans cet exemple) à C:\OpenFoam sur le nœud de tête afin que les nœuds Linux peuvent accéder à ce fichier à partir de /openfoam. Réexécutez **clusrun** commandes compilation OpenFOAM sur tous les nœuds Linux.


1.  Créer un dossier /opt/OpenFOAM sur chaque nœud Linux, copiez le package source dans ce dossier et extraire il.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Compilation OpenFOAM avec la Bibliothèque MPI Intel, configurez d’abord certaines variables d’environnement pour MPI Intel et OpenFOAM. Utiliser un script bash appelé settings.sh pour définir les variables. Vous pouvez trouver un exemple dans les fichiers d’exemples à la fin de cet article. Placez ce fichier (enregistré avec les fins de ligne Linux) dans le dossier partagé /openfoam. Ce fichier contienne également les paramètres pour les exécutions MPI et OpenFOAM que vous utilisez ultérieurement pour exécuter une tâche OpenFOAM.

3. Installer des packages dépendants nécessaires à la compilation OpenFOAM. Selon votre distribution Linux, vous devrez tout d’abord ajouter un référentiel. Exécutez commandes **clusrun** semblables à ce qui suit :

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Si nécessaire, SSH à chaque nœud Linux pour exécuter les commandes pour vérifier qu’ils s’exécutent correctement.

4.  Exécutez la commande suivante pour compiler OpenFOAM. Le processus de compilation prend un certain temps pour terminer et génère une grande quantité d’informations de journal vers la sortie standard, afin d’utiliser l’option **/ AVI** pour afficher la sortie avie.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]Le «\`» symbole dans la commande est un symbole d’échappement pour PowerShell. «\`& » signifie le « & » est un composant de la commande.

## <a name="prepare-to-run-an-openfoam-job"></a>Préparation à l’exécution d’une tâche OpenFOAM

Préparez-vous maintenant exécuter une tâche MPI appelée sloshingTank3D, qui est un des exemples de OpenFoam, deux nœuds Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurer l’environnement d’exécution

Pour configurer les environnements runtime pour MPI et OpenFOAM sur les nœuds Linux, exécutez la commande suivante dans une fenêtre de Windows PowerShell sur le nœud principal. (Cette commande est valide pour SUSE Linux uniquement.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Préparer les données d’exemple

Utiliser le partage de nœud de tête que vous avez configuré précédemment pour partager des fichiers entre les nœuds Linux (montés en tant que /openfoam).

1.  SSH à un de vos Linux des nœuds de calcul.

2.  Exécutez la commande suivante pour configurer l’environnement d’exécution OpenFOAM, si vous n’avez pas encore fait.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copier l’échantillon sloshingTank3D dans le dossier partagé et y accéder.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Lorsque vous utilisez les paramètres par défaut de cet exemple, cela peut prendre dizaines de minutes pour s’exécuter, afin que vous souhaiterez peut-être modifier certains paramètres pour qu’il s’exécute plus rapidement. Un choix simple consiste à modifier le temps étape variables deltaT et writeInterval dans le fichier système/controlDict. Ce fichier stocke toutes les données entrées concernant le contrôle du temps et en train de lire et écrire des données de la solution. Par exemple, vous pouvez modifier la valeur de deltaT entre 0,05 à 0,5 et la valeur de writeInterval entre 0,05 à 0,5.

    ![Modifier les variables d’étape][step_variables]

5.  Spécifiez les valeurs souhaitées pour les variables dans le fichier système/decomposeParDict. Cet exemple utilise deux nœuds Linux chaque avec 8 cœurs, donc la valeur numberOfSubdomains 16 et n de hierarchicalCoeffs à (1 1 16), ce qui signifie qu’exécuter OpenFOAM en parallèle avec 16 processus. Pour plus d’informations, voir [Guide de l’utilisateur OpenFOAM : 3.4 applications en cours d’exécution en parallèle](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Décomposer les processus][decompose]

6.  Exécutez les commandes suivantes à partir du répertoire sloshingTank3D pour préparer les données d’exemple.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  Sur le nœud principal, vous devriez voir que les fichiers d’exemples de données sont copiés dans C:\OpenFoam\sloshingTank3D. (C:\OpenFoam est le dossier partagé sur le nœud de tête).

    ![Fichiers de données sur le nœud de tête][data_files]

### <a name="host-file-for-mpirun"></a>Fichier hôte pour mpirun

Dans cette étape, vous créez un fichier host (une liste des nœuds de calcul) qui utilise la commande **mpirun** .

1.  Sur l’un des nœuds Linux, créez un fichier nommé fichier hôte sous /openfoam, afin que ce fichier peut être contacté /openfoam/hostfile sur tous les nœuds Linux.

2.  Rédigez votre noms des nœuds Linux dans ce fichier. Dans cet exemple, le fichier contient les noms suivants :
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Vous pouvez également créer ce fichier à C:\OpenFoam\hostfile sur le nœud principal. Si vous choisissez cette option, enregistrer en tant que fichier texte avec Linux fins de ligne (saut de ligne uniquement, pas CR saut de ligne). Ainsi qu’il s’exécute correctement sur les nœuds Linux.

    **Emballage de script Bash**

    Si vous avez plusieurs nœuds Linux et que vous voulez que votre travail s’exécutent sur uniquement certaines d'entre elles, il n’est pas recommandé d’utiliser un fichier hôte fixe, car vous ne savez pas les nœuds qui sont affectés à votre travail. Dans ce cas, rédigez un emballage script bash pour **mpirun** créer automatiquement le fichier d’hôte. Vous pouvez trouver un emballage de script exemple bash appelé hpcimpirun.sh à la fin de cet article et enregistrez-la en tant que /openfoam/hpcimpirun.sh. Cet exemple de script effectue les opérations suivantes :

    1.  Configure les variables d’environnement pour **mpirun**et certains paramètres de la commande addition pour exécuter la tâche MPI via le réseau RDMA. Dans ce cas, il définit les variables suivantes :

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = superflues d’une-v2-ib0
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Crée un fichier hôte en fonction de l’environnement variable $CCP_NODES_CORES, qui sont définies par le nœud de tête HPC lorsque la tâche est activée.

        Le format de CCP_NODES_CORES $ suit ce modèle :

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        où

        * `<Number of nodes>`-le nombre de nœuds affectés à cette tâche.  
        
        * `<Name of node_n_...>`-le nom de chaque nœud affecté à cette tâche.
        
        * `<Cores of node_n_...>`-le nombre de cœurs sur le nœud affectés à cette tâche.

        Par exemple, si la tâche doit deux nœuds pour exécuter, $CCP_NODES_CORES est similaire à
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Appel à cette commande **mpirun** et ajoute deux paramètres à la ligne de commande.

        * `--hostfile <hostfilepath>: <hostfilepath>`-le chemin d’accès du fichier hôte crée le script

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-une variable d’environnement définie par le nœud de tête HPC Pack, qui stocke le nombre de cœurs total affectés à cette tâche. Dans ce cas, elle indique le nombre de processus pour **mpirun**.


## <a name="submit-an-openfoam-job"></a>Soumettre une tâche OpenFOAM

Vous pouvez maintenant soumettre une tâche dans le Gestionnaire de Cluster HPC. Vous devez passer le script hpcimpirun.sh dans les lignes de commande pour certaines tâches.

1. Connectez-vous à votre nœud de tête cluster et démarrer le Gestionnaire de Cluster HPC.

2. **Dans la gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont dans l’état **en ligne** . Si elles ne sont pas, sélectionnez-les, puis cliquez sur **Mettre en ligne**.

3.  Dans la **Gestion des tâches**, cliquez sur **Nouvelle tâche**.

4.  Entrez un nom pour la tâche comme _sloshingTank3D_.

    ![Détails de la tâche][job_details]

5.  Dans les **ressources de travail**, choisissez le type de ressource en tant que « Nœud » et définissez la valeur minimale à 2. Cette configuration s’exécute le travail sur deux nœuds Linux, chacun d’eux ayant huit cœurs dans cet exemple.

    ![Ressources de travail][job_resources]

6. Cliquez sur **Modifier les tâches** dans le volet de navigation gauche, puis cliquez sur **Ajouter** pour ajouter une tâche à la tâche. Ajouter quatre tâches à la tâche avec les lignes de commande et les paramètres suivants.

    >[AZURE.NOTE]En cours d’exécution `source /openfoam/settings.sh` configure les environnements runtime OpenFOAM et MPI, afin que chacun des tâches suivantes l’appelle avant la commande OpenFOAM.

    *   **Tâche 1**. Exécutez **decomposePar** pour générer des fichiers de données pour **interDyMFoam** en cours d’exécution en parallèle.
    
        *   Affecter un nœud à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Répertoire de travail** - / openfoam/sloshingTank3D
        
        Voir la figure suivante. Vous configurez les tâches restantes de la même manière.

        ![Détails de la tâche 1][task_details1]

    *   **Tâche 2**. Exécutez **interDyMFoam** en parallèle pour calculer l’échantillon.

        *   Attribuer des deux nœuds à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Répertoire de travail** - / openfoam/sloshingTank3D

    *   **Tâche 3**. Exécutez **reconstructPar** pour fusionner les jeux des répertoires de temps à partir de chaque répertoire processor_N_ dans un seul ensemble.

        *   Affecter un nœud à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Répertoire de travail** - / openfoam/sloshingTank3D

    *   **Étape 4**. Exécutez **foamToEnsight** en parallèle pour convertir les fichiers de résultats OpenFOAM au format EnSight et placer les fichiers EnSight dans un répertoire nommé Ensight dans le répertoire cas.

        *   Attribuer des deux nœuds à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Répertoire de travail** - / openfoam/sloshingTank3D

6.  Ajouter des dépendances à ces tâches dans l’ordre des tâches dans l’ordre croissant.

    ![Interdépendances de tâches][task_dependencies]

7.  Cliquez sur **Envoyer** pour exécuter cette tâche.

    Par défaut, HPC Pack soumet le travail en tant que votre compte d’utilisateur connecté actuel. Après avoir cliqué sur **Envoyer**, vous pouvez voir une boîte de dialogue vous invitant à entrer le nom d’utilisateur et mot de passe.

    ![Informations d’identification de la tâche][creds]

    Dans certaines conditions, HPC Pack conserve les informations utilisateur entrée avant de ne plus afficher cette boîte de dialogue. Pour rendre HPC Pack afficher de nouveau, entrez la commande suivante à l’invite, puis soumettez la tâche.

    ```
    hpccred delcreds
    ```

8.  La tâche permet d’accéder à partir des dizaines de minutes à plusieurs heures selon les paramètres que vous avez défini pour l’échantillon. Dans la carte thermique, vous voyez le travail en cours d’exécution sur les nœuds Linux. 

    ![Carte thermique][heat_map]

    Sur chaque nœud, huit processus sont démarrés.

    ![Processus Linux][linux_processes]

9.  Lorsque la tâche se termine, recherchez les résultats des travaux dans les dossiers sous C:\OpenFoam\sloshingTank3D et les fichiers journaux à C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Afficher les résultats dans EnSight

Vous pouvez également utiliser [EnSight](https://www.ceisoftware.com/) pour visualiser et analyser les résultats de la tâche OpenFOAM. Pour plus d’informations sur la visualisation et animation dans EnSight, consultez ce [guide vidéo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Après avoir installé EnSight sur le nœud principal, le démarrer.

2.  Ouvrez C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Vous voyez une essence dans la visionneuse.

    ![Essence dans EnSight][tank]

3.  Créer un **Isosurface** à partir de **internalMesh**, puis choisissez variable **alpha_water**.

    ![Créer un isosurface][isosurface]

4.  Définir la couleur pour **Isosurface_part** créé à l’étape précédente. Par exemple, attribuez-lui l’eau bleu.

    ![Modifier la couleur isosurface][isosurface_color]

5.  Créer un **volume de Iso** à partir de **murs** en sélectionnant **murs** dans le volet **composants** et cliquez sur le bouton **Isosurfaces** dans la barre d’outils.

6.  Dans la boîte de dialogue, sélectionnez **Type** de fichier **Isovolume** et définissez le nombre minimal **d’Isovolume plage** à 0,5. Pour créer l’isovolume, cliquez sur **créer avec certaines parties**.

7.  Définir la couleur pour **Iso_volume_part** créé à l’étape précédente. Par exemple, attribuez-lui l’eau approfondie bleu.

8.  Définir la couleur pour que les **murs**. Par exemple, le définir en blanc transparent.

9. Cliquez sur **lire** pour afficher les résultats de la simulation.

    ![Résultat d’essence][tank_result]

## <a name="sample-files"></a>Exemples de fichiers

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemple de fichier XML configuration pour le déploiement de cluster par script PowerShell

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Exemple de fichier cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Exemple de fichier silent.cfg installer MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Exemple de script settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Exemple de script hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
