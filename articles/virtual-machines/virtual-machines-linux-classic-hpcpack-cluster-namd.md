<properties
 pageTitle="NAMD avec Microsoft HPC Pack sur machines virtuelles Linux | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et exécuter une simulation NAMD avec charmrun sur plusieurs nœuds de calcul Linux"
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
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Exécutez NAMD avec Microsoft HPC Pack sur nœuds de calcul Linux dans Azure

Cet article vous explique une des façons d’exécuter une charge de travail Linux High performance computing (HPC) sur des machines virtuelles Azure. Là, vous configurez un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) sur Azure avec des nœuds de calcul Linux et exécuter une simulation [NAMD](http://www.ks.uiuc.edu/Research/namd/) pour calculer et visualiser la structure d’un système de grande BIOMOLÉCULAIRE.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (pour le programme a moléculaire Dynamics) est un package de dynamics moléculaire parallèle conçu pour la simulation High performance des systèmes de grande BIOMOLÉCULAIRE contenant jusqu'à des millions d’atomes. Les exemples de ces systèmes de virus, structures de cellules et protéines de grande taille. NAMD s’adapte à des centaines de cœurs pour les simulations standard et à plus de 500 000 cœurs pour les simulations plus grandes.

* **Microsoft HPC Pack** fournit les fonctionnalités pour exécuter des applications parallèles et HPC à grande échelle dans les groupes d’ordinateurs locaux ou Azure machines virtuelles. Développé à l’origine comme une solution de charges de travail Windows HPC, HPC Pack prend désormais en charge exécution d’applications Linux HPC sur Linux calcule machines virtuelles nœud déployées dans un cluster HPC Pack. Pour plus d’informations, voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) .

Pour les autres options exécuter Linux HPC charges de travail dans Azure, consultez [les ressources techniques par lots et High performance computing](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Conditions préalables

* **Nœuds de calcul cluster HPC Pack avec Linux** - déployer un cluster HPC Pack avec des nœuds de calcul Linux sur Azure à l’aide d’un [modèle de gestionnaire de ressources Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou un [script PowerShell Azure](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour les conditions préalables et les étapes d’une option. Si vous sélectionnez l’option de déploiement de script PowerShell, voir l’exemple de fichier de configuration dans les fichiers d’exemples à la fin de cet article. Ce fichier configure un cluster basée sur Azure HPC Pack composée d’un nœud de tête Windows Server 2012 R2 et quatre nœuds de calcul 6.6 CentOS grande taille. Personnaliser ce fichier selon vos besoins pour votre environnement.


* **Fichiers de logiciels et didacticiel NAMD** - logiciel de télécharger NAMD pour Linux à partir du site [NAMD](http://www.ks.uiuc.edu/Research/namd/) (inscription obligatoire). Cet article est basé sur NAMD version 2.10 et utilise le fichier d’archives [Linux-x86_64 (64 bits Intel/AMD avec Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . Également télécharger les [fichiers du didacticiel NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Les téléchargements de fichiers .tar et vous avez besoin d’un outil Windows pour extraire les fichiers sur le nœud de tête cluster. Pour extraire les fichiers, suivez les instructions plus loin dans cet article. 

* **VMD** (facultatif) - pour afficher les résultats de votre travail NAMD, téléchargez et installez le programme de visualisation moléculaire [VMD](http://www.ks.uiuc.edu/Research/vmd/) sur un ordinateur de votre choix. La version actuelle est 1.9.2. Consultez le VMD site mise en route de téléchargement.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurer la confiance réciproque entre les nœuds de calcul
Exécution d’une tâche entre nœud sur plusieurs nœuds Linux requiert les nœuds d’eux approuver (en **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement Microsoft HPC Pack IaaS, le script configure automatiquement confiance réciproque permanente pour le compte d’administrateur que vous spécifiez. Pour les utilisateurs non administrateur que vous créez dans domaine du cluster, vous devez configurer temporaire confiance réciproque entre les nœuds lorsqu’une tâche est qui leur est attribuée. Ensuite, destroy la relation une fois que la tâche est terminée. Pour effectuer cette opération pour chaque utilisateur, fournissent une paire de clés RSA pour le cluster HPC Pack utilise pour établir la relation d’approbation. Instructions ci-dessous.

### <a name="generate-an-rsa-key-pair"></a>Générer une paire de clés RSA
Il est facile de générer une paire de clés RSA, qui contient une clé publique et une clé privée, en exécutant la commande **ssh keygen** Linux.

1.  Ouvrez une session sur un ordinateur Linux.

2.  Exécutez la commande suivante :

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Appuyez sur **entrée** pour utiliser les paramètres par défaut jusqu'à ce que la commande est terminée. Ne saisissez un mot de passe ici ; Lorsque vous y êtes invité pour un mot de passe, appuyez simplement sur **entrée**.

    ![Générer une paire de clés RSA][keygen]

3.  Placez-vous dans le répertoire ~/.ssh. La clé privée est stockée dans id_rsa et la clé publique dans id_rsa.pub.

    ![Clés privées et publiques][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Ajouter la paire de clés pour le cluster HPC Pack
1.  [Connexion Bureau à distance](virtual-machines-windows-connect-logon.md) pour le nœud de tête machine virtuelle utilisant le domaine informations d’identification fournies lorsque vous avez déployé le cluster (par exemple, hpc\clusteradmin). Vous gérez le cluster du nœud de tête.

2. Procédures standard de Windows Server permet de créer un compte d’utilisateur de domaine dans le domaine Active Directory du cluster. Par exemple, utilisez l’outil utilisateur Active Directory et des ordinateurs sur le nœud principal. Les exemples de cet article part du principe que vous créez un utilisateur de domaine nommé hpcuser dans le domaine hpclab (hpclab\hpcuser).

3. Ajouter l’utilisateur de domaine pour le cluster HPC Pack tant qu’utilisateur cluster. Pour plus d’informations, voir [Ajouter ou supprimer des utilisateurs de cluster](https://technet.microsoft.com/library/ff919330.aspx).

2.  Créez un fichier nommé C:\cred.xml et copiez les données de clé RSA. Vous pouvez trouver un exemple dans les fichiers d’exemples à la fin de cet article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Ouvrez une invite de commandes et entrez la commande suivante pour définir les données d’informations d’identification du compte hpclab\hpcuser. Vous utilisez le paramètre **extendeddata** pour transmettre le nom du fichier C:\cred.xml que vous avez créé pour les données de clé.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Cette commande se termine correctement sans sortie. Après avoir défini les informations d’identification pour les comptes d’utilisateurs que requis pour exécuter des travaux, stockez le fichier cred.xml dans un emplacement sécurisé, ou supprimez-le.

5.  Si vous avez généré la paire de clés RSA sur l’un de vos nœuds Linux, n’oubliez pas de supprimer les clés une fois que vous avez fini de les utiliser. HPC Pack n’a pas défini confiance réciproque s’il détecte un fichier id_rsa existant ou id_rsa.pub.

>[AZURE.IMPORTANT] Il n’est recommandé exécution d’un travail Linux en tant qu’un administrateur de cluster sur un cluster partagé, car une tâche présentée par un administrateur s’exécute sous le compte racine sur les nœuds Linux. Une tâche soumise par un utilisateur non administrateur s’exécute sous un compte d’utilisateur Linux local avec le même nom que l’utilisateur de la tâche. Dans ce cas, HPC Pack confiance réciproque pour cet utilisateur Linux configure sur tous les nœuds allouées à la tâche. Vous pouvez configurer l’utilisateur Linux manuellement dans les nœuds Linux avant d’exécuter la tâche, ou HPC Pack crée automatiquement l’utilisateur lorsque la tâche est envoyée. Si HPC Pack crée l’utilisateur, HPC Pack supprime une fois que le travail est terminé. Pour réduire les risques de sécurité, les clés sont supprimées après la fin du travail sur les nœuds.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurer un partage de fichiers pour les nœuds Linux

Ensuite configurer un partage de fichiers PME et monter le dossier partagé sur tous les nœuds Linux pour autoriser les nœuds Linux accéder aux fichiers NAMD avec un chemin d’accès courantes. Voici comment monter un dossier partagé sur le nœud principal. Un partage est recommandé pour répartitions comme CentOS 6.6 actuellement ne prend pas en charge le service de fichiers Azure. Si vos nœuds Linux prend en charge un partage de fichiers Azure, Découvrez [comment utiliser le stockage de fichiers Azure avec Linux](../storage/storage-how-to-use-files-linux.md). Fichier autres options de partage avec Pack HPC, voir [prise en main Linux cluster nœuds dans un Cluster de Pack HPC dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Créez un dossier sur le nœud de tête et partagez-le avec tous les participants en définissant des privilèges en lecture/écriture. Dans cet exemple, \\ \\CentOS66HN\Namd est le nom du dossier, où CentOS66HN est le nom d’hôte du nœud principal.

2. Créer un sous-dossier nommé namd2 dans le dossier partagé. Dans namd2, créez un autre sous-dossier nommé namdsample.

3. Extraire les fichiers NAMD dans le dossier à l’aide d’une version Windows de **tar** ou tout autre utilitaire de Windows qui fonctionne sur archives .tar. 
    * Extraire l’archive tar NAMD dans \\ \\CentOS66HN\Namd\namd2.
    
    * Extraire les fichiers du didacticiel sous \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Ouvrez une fenêtre de Windows PowerShell et exécutez les commandes suivantes pour monter le dossier partagé sur les nœuds Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /namd2 sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande montages la //CentOS66HN/Namd/namd2 dossier partagé sur le dossier avec dir_mode et file_mode bits comme 777. Le *nom d’utilisateur* et *mot de passe* dans la commande doivent être les informations d’identification d’un utilisateur sur le nœud principal.

>[AZURE.NOTE]Le «\`» symbole dans la deuxième commande est un symbole d’échappement pour PowerShell. «\`, » signifie le «, » (caractère virgule) est un composant de la commande.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Créer un script Bash pour exécuter une tâche NAMD

Votre travail NAMD requiert un fichier *nodelist* pour **charmrun** déterminer le nombre de nœuds à utiliser lors du démarrage de processus NAMD. Vous utilisez un script Bash génère le fichier nodelist et qui s’exécute **charmrun** avec ce fichier nodelist. Vous pouvez ensuite soumettre un travail NAMD dans le Gestionnaire de Cluster HPC appelant ce script.

À l’aide d’un éditeur de texte de votre choix, créez un script Bash dans le dossier /namd2 contenant les fichiers de programme NAMD et nommez-la hpccharmrun.sh. Pour une preuve de concept rapide, copiez l’exemple de script hpccharmrun.sh fourni à la fin de cet article et accédez à [Envoyer un travail NAMD](#submit-a-namd-job).

>[AZURE.TIP] Enregistrer votre script comme un fichier texte avec Linux ligne fins (saut de ligne uniquement, pas CR saut de ligne). Ainsi qu’il s’exécute correctement sur les nœuds Linux.

Voici plus d’informations sur ce script bash. 

1.  Définir des variables.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Obtenez des informations sur les nœuds des variables d’environnement. $NODESCORES stocke une liste de mots fractionnement CCP_NODES_CORES $. $COUNT est la taille de $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    Le format de la variable CCP_NODES_CORES $ est comme suit :

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Cette variable donne le nombre total de nœuds, les noms de nœud et le nombre de cœurs sur chaque nœud qui sont attribuées à la tâche. Par exemple, si la tâche doit 10 cœurs pour exécuter, la valeur de CCP_NODES_CORES $ ressemble à :

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Si la variable CCP_NODES_CORES $ n’est pas définie, démarrez **charmrun** directement. (Cela doit uniquement se produire lorsque vous exécutez ce script directement sur vos nœuds Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Ou créer un fichier nodelist pour **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Exécutez **charmrun** avec le fichier nodelist, obtenir son état retour et supprimez le fichier nodelist à la fin.

    ${CCP_NUMCPUS} est une autre variable d’environnement définie par le nœud de tête HPC Pack. Il stocke le nombre de cœurs total affectés à cette tâche. Nous l’utiliser pour spécifier le nombre de processus pour charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Quittez avec l’état de renvoi **charmrun** .

    ```
    exit ${RTNSTS}
    ```



Voici les informations contenues dans le fichier nodelist, qui génère le script :

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Par exemple :

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Soumettre une tâche NAMD

Vous êtes maintenant prêt à soumettre une tâche de NAMD dans le Gestionnaire de Cluster HPC.

1.  Connectez-vous à votre nœud de tête cluster et démarrer le Gestionnaire de Cluster HPC.

2.  Dans la **Gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont dans l’état **en ligne** . Si elles ne sont pas, sélectionnez-les, puis cliquez sur **Mettre en ligne**.

2.  Dans la **Gestion des tâches**, cliquez sur **Nouvelle tâche**.

3.  Entrez un nom pour la tâche comme *hpccharmrun*.

    ![Nouvelle tâche HPC][namd_job]

4.  Dans la page **Détails de la tâche** , sous **Ressources de travail**, sélectionnez le type de ressource comme **nœud** et définissez les **champs Minimum** à 3. , nous exécuter la tâche sur trois nœuds Linux et chaque nœud comporte quatre cœurs.

    ![Ressources de travail][job_resources]

5. Cliquez sur **Modifier les tâches** dans le volet de navigation gauche, puis cliquez sur **Ajouter** pour ajouter une tâche à la tâche.    


6. Dans la page **Détails de la tâche et la Redirection d’e/s** , définissez les valeurs suivantes :

    * **Ligne de commande** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] Ligne de commande précédente est une seule commande sans sauts de ligne. Il enveloppe qui pour doit apparaître sur plusieurs lignes sous la **ligne de commande**.

    * **Répertoire de travail** - /namd2

    * **Minimum** - 3

    ![Détails de la tâche][task_details]

    >[AZURE.NOTE] Vous définissez le répertoire de travail ici parce que **charmrun** essaie d’accéder au même répertoire de travail sur chaque nœud. Si le répertoire de travail n’est pas défini, HPC Pack démarre la commande dans un dossier nommé de manière aléatoire créé sur l’un des nœuds Linux. Cela provoque l’erreur suivante sur les autres nœuds : `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` pour éviter ce problème, spécifiez un chemin d’accès de dossier accessible par tous les nœuds en tant que le répertoire de travail.

5.  Cliquez sur **OK** , puis cliquez sur **Envoyer** pour exécuter cette tâche.

    Par défaut, HPC Pack soumet le travail en tant que votre compte d’utilisateur connecté actuel. Une boîte de dialogue peut vous demander d’entrer le nom d’utilisateur et mot de passe après avoir cliqué sur **Envoyer**.

    ![Informations d’identification de la tâche][creds]

    Dans certaines conditions, HPC Pack conserve les informations utilisateur entrée avant de ne plus afficher cette boîte de dialogue. Pour rendre HPC Pack afficher de nouveau, entrez la commande suivante à l’invite, puis soumettez la tâche.

    ```command
    hpccred delcreds
    ```

6.  La tâche prend plusieurs minutes pour terminer.

7.  Trouver le journal de travail en \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log et les fichiers de sortie dans \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Vous pouvez également démarrer VMD pour afficher les résultats de votre travail. Les étapes permettant de visualiser le NAMD sortie de fichiers (dans ce cas, une UBIQUITINE molecule de PROTEINE dans un domaine de l’eau) n’entrent pas dans le cadre de cet article. Pour plus d’informations, voir [Didacticiel NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .

    ![Résultats de la tâche][vmd_view]

## <a name="sample-files"></a>Exemples de fichiers

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemple de fichier XML configuration pour le déploiement de cluster par script PowerShell

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Exemple de script hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
