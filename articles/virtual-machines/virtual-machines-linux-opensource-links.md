<properties
    pageTitle="Linux et Open Source informatique sur Azure | Microsoft Azure"
    description="Répertorie les articles Linux et Open Source Computing sur Azure, y compris l’utilisation de base Linux, concepts fondamentaux à l’exécution ou télécharger les images Linux sur Azure et d’autres contenus sur les technologies spécifiques et les optimisations."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux et open source informatique sur Azure

Rechercher tous les documents que vous avez besoin pour créer et gérer des machines virtuelles Linux dans le modèle de déploiement classique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Prise en main
- [Introduction pour Linux sur Azure](virtual-machines-linux-intro-on-azure.md)
- [Forum aux questions sur les Machines virtuelles Azure créées avec le modèle de déploiement classique](virtual-machines-linux-classic-faq.md)
- [À propos des images des machines virtuelles](virtual-machines-linux-classic-about-images.md)
- [Télécharger votre propre Distro Image](virtual-machines-linux-classic-create-upload-vhd.md) (et également instructions à l’aide une [Distribution Azure-Endorsed](virtual-machines-linux-endorsed-distros.md))
- [Ouvrez une session sur une machine virtuelle Linux à l’aide du portail classique Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Configurer la

- [Installer Azure Interface de ligne (commande Azure)](../xplat-cli-install.md)


## <a name="tutorials"></a>Didacticiels

- [Installer la pile de feu sur une machine virtuelle Linux dans Azure](virtual-machines-linux-create-lamp-stack.md)
- [Ruby sur application Web Rails sur un ordinateur virtuel Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Comment : installer Apache Qpid PROTONS-c pour AMQP et Bus des services](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Bases de données
- [Optimiser les performances de MySQL sur Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [En cours d’exécution Cassandra avec Linux sur Azure et y accéder à partir de Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Créer un cluster de MariaDbs maîtres multiples](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [Prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Exécutez NAMD avec Microsoft HPC Pack sur nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurer un cluster Linux RDMA à exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [À l’aide de l’Extension de la machine virtuelle Docker à partir de la ligne de commande Azure Interface (Azure infrastructure du langage commun)](virtual-machines-linux-classic-cli-use-docker.md)
- [À l’aide de l’Extension de la machine virtuelle Docker à partir du portail Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Comment utiliser docker machine sur Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Comment : Clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Comment : Node.js et Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Comment : installer et exécuter MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Comment : utiliser CoreOS sur Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Planification
- [Instructions d’implémentation Azure infrastructure services](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Sélection de noms d’utilisateur Linux](virtual-machines-linux-usernames.md)
- [Comment configurer une disponibilité défini pour les machines virtuelles dans le modèle de déploiement classique](virtual-machines-linux-classic-configure-availability.md)
- [Comment faire pour planifier la Maintenance planifiée sur machines virtuelles Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gérer la disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md)
- [Maintenance planifiée pour les machines virtuelles Linux dans Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Déploiement
- [Créer une machine virtuelle personnalisée en cours d’exécution Linux](virtual-machines-linux-classic-createportal.md)
- [Concepts de base : capturer une VM Linux pour créer un modèle](virtual-machines-linux-classic-capture-image.md)
- [Informations pour répartitions Non approuvée](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Gestion des

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Comment réinitialiser un mot de passe ou propriétés SSH pour Linux](virtual-machines-linux-classic-reset-access.md)
- [À l’aide de la racine](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Ressources Azure

- [L’Agent Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Fonctionnalités et Extensions de machine virtuelle azure](virtual-machines-windows-extensions-features.md)
- [Injection de données personnalisée dans une machine virtuelle à utiliser avec Cloud initialisation](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Espace de stockage

- [Insertion d’un disque de données dans une machine virtuelle Linux](virtual-machines-linux-classic-attach-disk.md)
- [Détacher un disque de données à partir d’une machine virtuelle Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Mise en réseau
- [Comment configurer des points de terminaison sur une machine virtuelle classique dans Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Résolution des problèmes
- [Résoudre les problèmes de connexions SSH (Secure Shell) à une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Résoudre les problèmes de déploiement classique de création d’une nouvelle machine virtuelle Linux dans Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Résoudre les problèmes de déploiement classique de redémarrer ou en redimensionnant une Machine virtuelle Linux existante dans Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Référence

- [Commandes infrastructure du langage commun Azure en mode de gestion des services Azure (asm)](../virtual-machines-command-line-tools.md)
- [Gestion des services Azure API REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Liens général
Les liens suivants sont appliquent aux Microsoft blogs, pages Technet et sites externes plutôt que Azure.com documentation comme indiqué ci-dessus. Azure et du monde informatique open source sont mouvement rapide cibles, il est presque certain que les liens suivants sont obsolètes, *malgré* le fait que nous allons faire notre mieux pour ajouter des sujets plus récents en permanence et supprimer sont ceux qui obsolètes. Si nous avons manqué une, veuillez laissez-nous connaître dans les commentaires ou envoyer une demande d’extraction à notre [mis en pension GitHub](https://github.com/Azure/azure-content/).

- [5 ASP.NET en cours d’exécution sur Linux à l’aide de conteneurs Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Comment déployer une Image de machine virtuelle CentOS à partir de OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infrastructure de mises à jour SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server pour SAP Cloud matériel bibliothèque](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Construction hautement disponible Linux sur Azure dans 12 étapes](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatiser le processus de mise en service Linux sur Azure avec Azure infrastructure du langage commun, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS sur Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [FreeBSD en cours d’exécution dans Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Facile déployer FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Déploiement d’une Image FreeBSD personnalisée](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV pour serveur de fichiers Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [8 bases de données NoSql open source pour Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech) : Expériences avec CouchDb sur Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [CouchDB-as-a-Service avec node.js CORS et Grunt en cours d’exécution](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis sur Windows dans le Service de Cache Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Annonce de fournisseur d’état de Session ASP.NET pour Redis (version préliminaire)](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog : RavenHQ est désormais disponible sur le marché Azure](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Données volumineux
- [L’installation d’Hadoop sur machines virtuelles Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Base de données relationnelle
- [Architecture de disponibilité haute MySQL dans Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [L’installation Postgres avec corosync, pg_bouncer à l’aide de ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux haute performance computing (HPC)

- [Modèle de démarrage rapide : tourner un cluster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (et [le billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modèle de démarrage rapide : créer un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>DevOps, la gestion et l’optimisation

En tant que le monde de devops, gestion et optimisation est assez vaste et change très rapidement, vous devez prendre en compte la liste en dessous d’un point de départ.

- [Vidéo : Machines virtuelles Azure : à l’aide de Chef, Marionnette et Docker pour la gestion des machines virtuelles Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guide complet de déploiement de cluster Kubernetes automatisé avec CoreOS et tissage](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualiseur Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Esclave Jenkins plug-in pour Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [Mis en pension GitHub : stockage Jenkins plug-in pour Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Tiers : Hudson esclave plug-in pour Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Tiers : Stockage de Hudson plug-in pour Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vidéo : Qu’est Chef et comment cela fonctionne-t-il ?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vidéo : Comment utiliser Automation Azure avec machines virtuelles Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog : Comment faire DSC Powershell pour Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub : Docker Client DSC](https://github.com/anweiss/DockerClientDSC)

- [Plug-in du programme de compression pour Azure](https://github.com/msopentech/packer-azure)
