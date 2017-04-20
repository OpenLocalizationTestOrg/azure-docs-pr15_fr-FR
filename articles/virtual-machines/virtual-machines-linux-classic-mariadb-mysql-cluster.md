<properties
    pageTitle="Exécuter un cluster MariaDB (MySQL) sur Azure"
    description="Créer un MariaDB + Galera MySQL cluster Azure machines virtuelles en fonctionnement"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>Cluster MariaDB (MySQL) - didacticiel Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  Cluster MariaDB entreprise est désormais disponible sur le marché Azure.  La nouvelle offre déployez automatiquement un cluster MariaDB Galera sur le cloud. Vous devez utiliser la nouvelle offre à partir de https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Nous créons un cluster [Galera](http://galeracluster.com/products/) maîtres multiples de [MariaDBs](https://mariadb.org/en/about/), remplace dans le désordre robuste, scalable et fiable MySQL, pour l’utiliser dans un environnement hautement disponible Azure machines virtuelles en fonctionnement.

## <a name="architecture-overview"></a>Présentation de l’architecture

Cette rubrique effectue les opérations suivantes :

1. Créer un cluster 3 nœuds
2. Séparez les disques de données à partir du disque du système d’exploitation
3. Créer les données disques RAID-0/réparties paramètre pour augmenter sorties par
4. Utiliser l’équilibrage de charge Azure pour équilibrer la charge pour les 3 nœuds
5. Pour réduire le travail répétitive, créez une image de machine virtuelle contenant MariaDB + Galera et utilisez-le pour créer le cluster autres machines virtuelles.

![Architecture](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  Cette rubrique utilise les outils [Azure infrastructure du langage commun](../xplat-cli-install.md) , vérifiez que les télécharger et de les contacter à votre abonnement Azure conformément aux instructions. Si vous avez besoin d’une référence aux commandes disponibles dans l’infrastructure du langage commun Azure, consultez ce lien pour afficher la [référence des commandes Azure infrastructure du langage commun](../virtual-machines-command-line-tools.md). Vous sera également nécessaires pour [créer un code SSH pour l’authentification] et prenez note de l' **emplacement du fichier .pem**.


## <a name="creating-the-template"></a>Création du modèle

### <a name="infrastructure"></a>Infrastructure

1. Créer un groupe affinité pour mettre en attente les ressources ensemble

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Créer un réseau virtuel

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Créer un compte de stockage pour héberger tous nos disques. Notez que vous ne devez pas placer plus de 40 intensément utilisé disques sur le même compte de stockage pour éviter en appuyant sur la limite de compte de stockage sorties par 20 000. Dans ce cas, nous sommes éloignées à partir de ce numéro pour que nous allons stocker toutes les informations sur le même compte pour simplifier

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Recherchez le nom de l’image CentOS 7 Virtual Machine

        azure vm image list | findstr CentOS
Vous obtiendrez s’intitule `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Utilisez le nom de l’étape suivante.

4. Créer le modèle de machine virtuelle remplaçant **/path/to/key.pem** par le chemin d’accès où vous avez enregistré le SSH .pem générée

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Joindre des disques de données de 500 x 4 Go de la machine virtuelle à utiliser dans la configuration RAID

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH dans le modèle machine virtuelle que vous avez créé en **mariadbhatemplate.cloudapp.net:22** et se connectez à l’aide de votre clé privée.

### <a name="software"></a>Logiciel

1. Obtenir la racine

        sudo su

2. Installer la prise en charge RAID :

     - Installer mdadm

                yum install mdadm

     - Créer la configuration RAID0/répartition avec un système de fichiers EXT4

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Créer le répertoire de point de montage

                mkdir /mnt/data

     - Récupérer l’UUID du périphérique RAID nouvellement créé

                blkid | grep /dev/md0

     - Modifier/etc/fstab

                vi /etc/fstab

     - Ajouter le périphérique dans cet emplacement pour activer mouting automatique au redémarrage remplaçant l’UUID par la valeur obtenue à partir de la commande **blkid** avant

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Monter la nouvelle partition

                mount /mnt/data

3. Installer MariaDB :

     - Créez le fichier MariaDB.repo :

                vi /etc/yum.repos.d/MariaDB.repo

     - Remplissez-la avec l’en dessous de contenu

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Supprimer le suffixe existant et mariadb-et les bibliothèques pour éviter les conflits

            yum remove postfix mariadb-libs-*

     - Installer MariaDB avec Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Déplacer le répertoire de données MySQL à l’appareil de bloc RAID

     - Copiez le répertoire MySQL actif dans son nouvel emplacement et supprimez l’ancien répertoire

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Définir les autorisations sur Nouveau répertoire en conséquence

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Créer un type pointant vers le répertoire ancien vers le nouvel emplacement sur la partition RAID

            ln -s /mnt/data/mysql /var/lib/mysql

5. Étant donné [que SELinux interfère avec les opérations de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), il est nécessaire de désactiver pour la session active (jusqu'à ce qu’une version compatible apparaisse). Modifier `/etc/selinux/config` pour désactiver pour redémarrage suivant :

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Valider s’exécute MySQL

    - Démarrer MySQL

            service mysql start

    - Sécuriser l’installation de MySQL, définir le mot de passe racine, supprimer les utilisateurs anonymes, désactivation de la connexion à distance racine et suppression de la base de données de test

            mysql_secure_installation

    - Créer un utilisateur dans la base de données pour les opérations de cluster et vous pouvez également vos applications

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Arrêter MySQL

            service mysql stop

7. Créer l’espace réservé de configuration

    - Modifier la configuration de MySQL pour créer un espace réservé pour les paramètres de cluster. Ne pas remplacer le **`<Vairables>`** ou annulez le commentaire maintenant. Qui se produit une fois que nous avons créé une machine virtuelle à partir de ce modèle.

            vi /etc/my.cnf.d/server.cnf

    - Modifier la section **[galera]** et effacer les

    - Modifiez la section **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Ouvrez les ports nécessaires sur le pare-feu (à l’aide de FirewallD sur CentOS 7)

    - MySQL :`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA :`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - TSI GALERA :`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - ONT :`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Recharger le pare-feu :`firewall-cmd --reload`

9.  Optimiser le système pour les performances. Reportez-vous à cet article sur la [stratégie d’optimisation des performances](virtual-machines-linux-classic-optimize-mysql.md) pour plus d’informations

    - Modifier le fichier de configuration MySQL à nouveau

            vi /etc/my.cnf.d/server.cnf

    - Modifiez la section **[mariadb]** et ajoutez la ci-dessous

    > [AZURE.NOTE] Il est recommandé **innodb\_tampon\_pool_size** être est de 70 % de la mémoire de votre ordinateur virtuel. Il a été défini en Go 2,45 ici pour la machine virtuelle Azure support avec 3,5 Go de RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Arrêter MySQL, désactiver le service MySQL d’exécuter au démarrage pour éviter altérer le cluster lors de l’ajout d’un nouveau nœud et deprovision l’ordinateur.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Capturer la machine virtuelle via le portail. (Pour l’instant, outils [problème 1268 # dans l’infrastructure du langage commun Azure] décrit le fait que les images capturées par les outils Azure infrastructure du langage commun ne capturent pas les disques données attachées.)

    - Arrêtez l’ordinateur à partir du portail
    - Cliquez sur Capture et spécifiez le nom de l’image en tant que **mariadb-galera-image** et fournir une description et consultez « J’ai exécute waagent ».
    ![Capture de la Machine virtuelle](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![capturer la Machine virtuelle](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Créer le cluster

Créer 3 machines virtuelles déconnecter le modèle que vous venez de créer et ensuite configurez et démarrez le cluster.

1. Créer la première machine virtuelle des 7 CentOS à partir de l’image **mariadb-galera-image** que vous avez créé, fournir le nom de réseau virtuel **mariadbvnet** et le sous-réseau **mariadb**, machine de taille **moyenne**, en passant dans le Service Cloud nommez être **mariadbha** (ou un autre nom que vous voulez être accessible via mariadbha.cloudapp.net), définition du nom de cette machine pour être **mariadb1** et le nom d’utilisateur pour être **azureuser**et activation de l’accèsSSH et en passant le SSH certificat .pem fichier et le remplacement **/path/to/key.pem** avec le chemin d’accès dans lequel vous stocké le SSH .pem générée.

    > [AZURE.NOTE] Les commandes ci-dessous sont fractionnées sur plusieurs lignes pour plus de clarté, mais vous devez entrer chacun d’eux comme une seule ligne.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Créer des 2 Machines virtuelles plus en _vous connectant_ à actuellement créées **mariadbha** Service Cloud, modifier le **nom de la machine virtuelle** , ainsi que le **port SSH** à un port unique pas en conflit avec d’autres ordinateurs virtuels dans le même Service Cloud.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
et pour MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Vous devez obtenir l’adresse IP interne de chacun des ordinateurs 3 virtuels pour l’étape suivante :

    ![Obtention de l’adresse IP](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH dans les 3 machines virtuelles et et modifier le fichier de configuration sur chaque

        sudo vi /etc/my.cnf.d/server.cnf

    commentaire de **`wsrep_cluster_name`** et **`wsrep_cluster_address`** en supprimant la **#** au début et à la validation qu’ils sont en effet vous le souhaitez.
    Par ailleurs, remplacer **`<ServerIP>`** dans **`wsrep_node_address`** et **`<NodeName>`** dans **`wsrep_node_name`** d’adresses IP de l’ordinateur et nom respectivement et ne commentez pas ces lignes également.

5. Démarrer le cluster sur MariaDB1 et laissez-le s’exécuter au démarrage

        sudo service mysql bootstrap
        chkconfig mysql on

6. Démarrer MySQL sur MariaDB2 et MariaDB3 et laissez-le s’exécuter au démarrage

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>Le cluster d’équilibrage de charge
Lorsque vous avez créé les ordinateurs virtuels groupés, vous avez ajouté les dans une disponibilité jeu nommé **clusteravset** pour vous assurer qu’ils sont placés sur différents pannes et mettre à jour des domaines et que Azure jamais ne maintenance sur tous les ordinateurs en même temps. Cette configuration répond à la configuration requise pour la prise en charge par cette Azure Service (contrat de niveau).

Désormais, vous utilisez l’équilibrage de charge Azure pour équilibrer les demandes entre nos 3 nœuds.

Exécuter le sous commandes sur votre ordinateur à l’aide de l’infrastructure du langage commun Azure.
La structure de paramètres de commande est la suivante :`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Enfin, étant donné que l’infrastructure du langage commun définit l’intervalle de sonde équilibrage de charge à 15 secondes (qui peut être légèrement trop longs), modifiez-la dans le portail sous **points de terminaison** pour une des ordinateurs virtuels

![Modifier le point de terminaison](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

Cliquez ensuite sur Reconfigure The Load-Balanced définir et aller

![RECONFIGURE charger ensemble équilibrée](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

modifier l’intervalle sonde à 5 secondes, puis enregistrer

![Modification de l’intervalle sonde](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Valider le cluster

Le travail est terminé. Le cluster doit maintenant être accessible au `mariadbha.cloudapp.net:3306` qui atteint l’équilibrage de charge et acheminer des requêtes entre les ordinateurs 3 virtuels fluide et plus efficace.

Utilisez votre client MySQL favori pour vous connecter ou se connecter uniquement à partir d’un des ordinateurs virtuels pour vérifier que ce cluster fonctionne.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Puis créer une nouvelle base de données et remplissez-la avec des données

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Entraînera dans le tableau ci-dessous

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un 3 nœud MariaDB + Galera-available cluster sur Machines virtuelles Azure CentOS 7 en cours d’exécution. Les ordinateurs virtuels sont réparties avec l’équilibrage de charge Azure.

Vous souhaiterez peut-être effectuer examiner [une autre façon de cluster MySQL sur Linux](virtual-machines-linux-classic-mysql-cluster.md) et comment [optimiser et tester les performances de MySQL sur machines virtuelles Azure Linux](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[créer un code SSH pour l’authentification]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problème #1268 dans l’infrastructure du langage commun Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268
