<properties
    pageTitle="Optimiser les performances de MySQL sur machines virtuelles Linux | Microsoft Azure"
    description="Découvrez comment optimiser MySQL s’exécutant sur une Azure machine virtuelle () exécutant Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Optimisation des performances MySQL sur machines virtuelles Linux Azure

De nombreux facteurs qui ont une incidence sur les performances de MySQL sur Azure, à la fois dans la configuration de sélection et logicielle matériel virtuel. Cet article se concentre sur l’optimisation des performances grâce au stockage, système et configurations de la base de données.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Utilisation de RAID sur une machine virtuelle Azure
Stockage est le facteur clé qui affecte les performances de base de données dans les environnements de cloud.  Par rapport à un disque, RAID peut fournir un accès plus rapide via la concurrence.  Pour plus d’informations, reportez-vous à [Niveaux RAID Standard](http://en.wikipedia.org/wiki/Standard_RAID_levels) .   

Débit e/s disque et l’heure de réponse d’e/s dans Azure peuvent être considérablement améliorés grâce aux RAID. Tests de notre laboratoire afficher e/s disque débit peut être double et le temps de réponse d’e/s peut être réduit de moitié en moyenne lorsque le nombre de disques RAID est double (de 2 à 4, 4 à 8, etc.). Pour plus d’informations, consultez [l’annexe A](#AppendixA) .  

Outre les e/s disque, MySQL performances améliorent lorsque vous augmentez le niveau.  Pour plus d’informations, voir [L’annexe B](#AppendixB) .  

Vous pouvez également à prendre en considération la taille de segment. En règle générale lorsque vous avez une plus grande taille de segment, vous obtiendrez inférieur aérienne, en particulier pour les grandes écrit. Toutefois, lorsque la taille de segment est trop volumineux, il peut ajouter une charge supplémentaire et vous ne pouvez pas tirer parti du RAID. La taille par défaut actuel est 512 Ko, ce qui est révélé optimal pour les environnements de production plus générales. Pour plus d’informations, consultez [L’annexe C](#AppendixC) .   

Notez qu’il existe certaines limites sur disques combien vous pouvez ajouter des types de machine virtuelle différente. Ces limites sont détaillées dans [Machine virtuelle et les tailles de Service Cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Vous devrez 4 disques de données attachées pour suivre l’exemple RAID dans cet article, bien que vous pouvez choisir de configurer RAID avec moins de disques.  

Cet article suppose que vous avez déjà créé une machine virtuelle Linux et MYSQL installé et configuré. Pour plus d’informations sur la mise en route reportez-vous à comment installer MySQL sur Azure.  

###<a name="setting-up-raid-on-azure"></a>La configuration RAID sur Azure
Les étapes suivantes montrent comment créer RAID sur Azure à l’aide du portail classique Azure. Vous pouvez également configurer RAID à l’aide de scripts Windows PowerShell.
Dans cet exemple, nous allons configurer RAID 0 avec 4 disques.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Étape 1 : Ajouter un disque de données à votre Machine virtuelle  

Dans la page Machines virtuelles du portail Azure classique, cliquez sur la machine virtuelle à laquelle vous voulez ajouter un disque de données. Dans cet exemple, la machine virtuelle est mysqlnode1.  

![][1]

Dans la page de la machine virtuelle, cliquez sur **tableau de bord**.  

![][2]


Dans la barre des tâches, cliquez sur **joindre**.

![][3]

Puis sur **disque vide joindre**.  

![][4]

Pour les disques de données, la **Préférence de Cache hôte** doit être définie sur **Aucune**.  

Cette opération ajoute un disque vide dans votre machine virtuelle. Répétez cette étape trois reprises afin que vous ayez 4 disques de données RAID.  

Vous pouvez visualiser les lecteurs ajoutés dans la machine virtuelle en consultant le journal des messages du noyau. Par exemple, pour afficher ce Ubuntu, utilisez la commande suivante :  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Étape 2 : Créer RAID avec les autres disques
Suivez cet article pour plus d’informations d’installation RAID :  

[Configurer les logiciels RAID sur Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Si vous utilisez le système de fichiers XFS, suivez les étapes ci-dessous après avoir créé RAID.

Pour installer XFS sur Debian, Ubuntu ou Linux menthe, utilisez la commande suivante :  

    apt-get -y install xfsprogs  

Pour installer XFS sur Fedora, CentOS ou RHEL, utilisez la commande suivante :  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Étape 3 : Configurer un nouveau chemin d’accès de stockage
Utilisez la commande suivante :  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Étape 4 : Copier les données d’origine dans le nouveau chemin d’accès de stockage
Utilisez la commande suivante :  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Étape 5 : Modifier les autorisations pour MySQL puisse accéder (lire et écrire) le disque de données
Utilisez la commande suivante :  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajuster l’algorithme de planification e/s disque
Linux met en œuvre quatre types d’e/s algorithmes de planification :  

-   Algorithme de NOOP (aucune opération)
-   Algorithme de date d’échéance (échéance)
-   Algorithme de files d’attente complètement juste (CFQ)
-   Algorithme de période budgétaire (Anticipatory)  

Vous pouvez sélectionner différentes planificateurs e/s sous différents scénarios pour optimiser les performances. Dans un environnement complètement vive, il n’est pas une grande différence entre les algorithmes CFQ et date d’échéance des performances. Il est généralement recommandé pour définir l’environnement de base de données MySQL à échéance de stabilité. S’il existe un grand nombre d’e/s séquentielles, CFQ peut réduire les performances disque.   

Pour SSD et autres équipements, à l’aide de NOOP ou échéance pouvez obtenir de meilleures performances que le planificateur par défaut.   

À partir du noyau 2,5, l’algorithme de planification e/s par défaut est date d’échéance. Au début du noyau 2.6.18, CFQ est devenu l’algorithme de planification e/s par défaut.  Vous pouvez spécifier ce paramètre lors du démarrage du noyau ou modifier ce paramètre de façon dynamique lorsque le système est en cours d’exécution.  

L’exemple suivant montre comment vérifier et définir le planificateur par défaut à l’algorithme NOOP.  

Pour la famille Debian distribution :

###<a name="step-1view-the-current-io-scheduler"></a>Planificateur de mode e/s en cours étape 1.
Utilisez la commande suivante :  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Vous verrez la suite de sortie, qui indique le planificateur actuel.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Étape 2. Modifier le périphérique actuel (/ développement/sda) des e/s algorithme de planification
Utilisez les commandes suivantes :  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Ce paramètre pour/dev/sda seul n’est pas utile. Il doit être définie sur tous les disques de données où réside la base de données.  

Vous devriez voir le résultat suivant, indiquant que grub.cfg a été reconstruit avec succès et que le planificateur par défaut a été mis à jour pour NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Pour la famille de distribution Redhat, vous devez uniquement la commande suivante :   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Configurer les paramètres des opérations de fichier système
Une meilleure solution consiste à désactiver la fonctionnalité de journalisation atime sur le système de fichiers. Atime est la dernière fois d’accès de fichier. Chaque fois qu’un fichier est consultée, le système de fichiers enregistre l’horodatage dans le journal. Toutefois, cette information est rarement utilisée. Vous pouvez le désactiver si vous n’avez pas besoin, qui permet de réduire le temps d’accès global disque.  

Pour désactiver atime journalisation, vous devez modifier le fstab fichier système configuration fichier trouver et ajouter l’option **noatime** .  

Par exemple, modifiez le fichier/etc/fstab vim, en ajoutant le noatime comme illustré ci-dessous.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Remontez le système de fichiers avec la commande suivante :  

    mount -o remount /RAID0

Tester le résultat modifié. Notez que lorsque vous modifiez le fichier de test, l’heure d’accès n'est pas mis à jour.  

Avant d’exemple :     

![][5]

Après l’exemple :

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Augmenter le nombre maximal de système poignées des accès concurrentiels au haute
MySQL est base de données de la concurrence élevé. Le nombre de poignées simultanées par défaut est 1024 pour Linux, qui n’est pas toujours suffisant. **Procédez comme suit pour augmenter les poignées simultanées maximales du système pour prendre en charge de la concurrence élevé de MySQL**.

###<a name="step-1-modify-the-limitsconf-file"></a>Étape 1 : Modifier le fichier limits.conf
Ajouter les quatre lignes suivantes dans le fichier /etc/security/limits.conf pour augmenter les poignées de simultanées maximales autorisées. Notez que 65536 est le nombre maximal que le système peut prendre en charge.   

    * bordures nofile 65536
    * nofile dur 65536
    * bordures nproc 65536
    * nproc dur 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Étape 2 : Mettre à jour le système pour les nouvelles limites
Exécutez les commandes suivantes :  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Étape 3 : Vérifier que les limites sont mises à jour au démarrage
Placer les commandes de démarrage suivantes dans le fichier /etc/rc.local afin qu’il prend effet chaque lors du démarrage.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Optimisation de base de données MySQL
Vous pouvez utiliser la stratégie d’optimisation performances même configurer MySQL sur Azure en tant que sur un ordinateur local.  

Les règles d’optimisation e/s principales sont :   

-   Augmenter la taille du cache.
-   Réduisez le temps de réponse d’e/s.  

Pour optimiser les paramètres du serveur MySQL, vous pouvez mettre à jour le fichier my.cnf, qui est le fichier de configuration par défaut pour la copie du serveur et les ordinateurs client.  

Les éléments de configuration suivants sont les principaux facteurs qui affectent les performances de MySQL :  

-   **innodb_buffer_pool_size**: le pool de tampons contient des données mises en mémoire tampon et l’index. Ceci est généralement définie à 70 % de mémoire physique.
-   **innodb_log_file_size**: il s’agit de la taille du journal rétablir. Journaux de rétablissement vous permet de vous assurer que les opérations d’écriture sont rapides, fiables et récupérables après un blocage. Ceci est défini sur 512 Mo, ce qui vous donne suffisamment d’espace pour la journalisation des opérations d’écriture.
-   **max_connections**: parfois applications ne ferment connexions correctement. Une plus grande valeur vous accordez au serveur davantage de temps pour Corbeille connexions inactif. Le nombre maximal de connexions est 10000, mais la valeur maximale recommandée est 5 000.
-   **Innodb_file_per_table**: ce paramètre Activer ou désactiver la possibilité de InnoDB pour stocker les tables dans des fichiers distincts. Activer l’option assuré que plusieurs opérations d’administration avancées peuvent être appliquées efficacement. Performance point de vue, il peut accélérer la transmission espace table et optimiser les performances de gestion des débris. Si le paramètre recommandé pour cela est activée.</br>
    5.6 MySQL, le paramètre par défaut est activé. Par conséquent, aucune action n’est requise. Pour les autres versions, qui est antérieure à 5.6, les paramètres par défaut est désactivé. Activation de cette suite est requis. Et doit être appliqué il avant le chargement de données, étant donné que seules les tables nouvellement créé sont affectées.
-   **innodb_flush_log_at_trx_commit**: valeur par défaut est 1, avec l’étendue de la valeur 0 ~ 2. La valeur par défaut est l’option la plus appropriée pour la base de données MySQL autonome. Le paramètre de 2 Active l’intégrité des données la plupart des et est adapté aux masque cluster MySQL. Le paramètre de 0 permet de perte de données, qui peut affecter la fiabilité, dans certains cas, avec les meilleures performances et est adapté aux esclave cluster MySQL.
-   **Innodb_log_buffer_size**: la mémoire tampon journal permet aux transactions d’exécuter sans avoir à vider le journal sur le disque avant de valider les transactions. Cependant, s’il existe des objets binaires volumineux ou un champ de texte, le cache est consommé très rapidement et e/s disque fréquents sera déclenchée. Il est mieux augmenter la taille de la mémoire tampon si Innodb_log_waits variable d’état n’est pas 0.
-   **query_cache_size**: la meilleure option consiste à désactiver dès le début. La valeur 0 (c’est maintenant le paramètre par défaut dans MySQL 5.6) query_cache_size et utiliser d’autres méthodes pour accélérer les requêtes.  

Consultez [L’annexe D](#AppendixD) de comparaison des performances suite à l’optimisation.


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Activer le journal des requêtes lente MySQL pour analyser la critique performances
Le journal des requêtes lente MySQL peut vous aider à identifier les requêtes lentes pour MySQL. Après avoir activé le journal des requêtes lente MySQL, vous pouvez utiliser des outils MySQL comme **mysqldumpslow** pour identifier le critique performances.  

Veuillez noter que par défaut cela n’est pas activée. Activer le journal des requêtes lentes peut-être utiliser certaines ressources de l’UC. Par conséquent, il est recommandé d’activer cette temporairement pour la résolution des problèmes de dégradation des performances.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Étape 1 : Modifier le fichier my.cnf en ajoutant les lignes suivantes à la fin   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Étape 2 : Redémarrer serveur mysql
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Étape 3 : Vérifier si le paramètre prend effet à l’aide de la commande « show »

![][7]   

![][8]

Dans cet exemple, vous pouvez voir que la fonctionnalité de requête lente a été activée. Vous pouvez ensuite utiliser l’outil **mysqldumpslow** pour déterminer dégradation des performances et optimiser les performances, par exemple en ajoutant des index.





##<a name="appendix"></a>Appendice

Voici des exemples de données test performances produites sur laboratoire ciblées, ils fournissent des informations générales sur la tendance de données de performance approches, d’optimisation des performances différents alors que les résultats peuvent varier sous différentes versions d’environnement ou produit.

<a name="AppendixA"></a>Annexe a :  
**Performances du disque (sorties par) qualité différents**


![][9]

**Commandes de test :**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Remarque : La charge de travail de ce test utilise 64 threads, essayez d’atteindre la limite supérieure de RAID.

<a name="AppendixB"></a>Annexe b :  
**Comparaison des performances (débit) MySQL qualité différents**   
(Système de fichiers XFS)


![][10]  
![][11]

**Commandes de test :**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparaison des performances (OLTP) MySQL qualité différents**  
![][12]

**Commandes de test :**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Annexe c :   
**Comparaison des performances (sorties par) disque pour les tailles de segment différent**  
(Système de fichiers XFS)


![][13]

**Commandes de test :**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Remarque la taille de fichier utilisée pour ce test est respectivement 30 et 1 Go, avec RAID XFS 0(4 disks) fichier système.


<a name="AppendixD"></a>Annexe d :  
**Comparaison des performances (débit) MySQL avant et après l’optimisation**  
(Système de fichiers XFS)


![][14]

**Commandes de test :**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Paramètre de configuration pour optmization et par défaut est la suivante :**

|Paramètres |Par défaut    |optmization
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Aucun   |7G
|**innodb_log_file_size**   |5M |512 MO
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size**   |16 MO    |0


Paramètres de configuration de l’optimisation plus détaillées, consultez les instructions officiel mysql.  

[http://dev.MySQL.com/doc/refman/5.6/en/InnoDB-configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.MySQL.com/doc/refman/5.6/en/InnoDB-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Environnement de test**  

|Matériel   |Plus d’informations
|-----------|-------
|Processeur    |Processeur 4171 HE AMD Opteron(tm) / 4 cœurs
|Mémoire |14G
|disque   |10G/disque
|système d’exploitation |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
