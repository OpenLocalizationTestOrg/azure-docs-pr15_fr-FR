<properties
    pageTitle="Configurer MySQL sur un Linux VM | Microsoft Azure "
    description="Découvrez comment installer la pile MySQL sur une machine virtuelle de Linux (Ubuntu ou RedHat famille du système d’exploitation) dans Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>Comment installer MySQL sur Azure


Dans cet article, vous allez apprendre à installer et configurer MySQL sur une machine virtuelle Azure exécutant Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Installer MySQL sur votre machine virtuelle

> [AZURE.NOTE] Vous devez déjà une machine virtuelle Microsoft Azure exécutant Linux afin de pouvoir suivre ce didacticiel. Consultez le [didacticiel machine virtuelle Linux Azure](virtual-machines-linux-quick-create-cli.md) pour créer et configurer un VM Linux avec `mysqlnode` en tant que le nom de la machine virtuelle et `azureuser` en tant qu’utilisateur avant de poursuivre.

Dans ce cas, utilisez port 3306 comme port MySQL.  

Se connecter à la machine virtuelle que vous avez créé via putty Linux. Si c’est la première fois que vous utilisez machine virtuelle Linux Azure, découvrez comment utiliser putty se connecter à une VM Linux [ici](virtual-machines-linux-mac-create-ssh-keys.md).

Nous allons utiliser package référentiel pour installer MySQL5.6 comme exemple dans cet article. MySQL5.6 comporte plus d’amélioration du produit de performances que MySQL5.5.  Plus d’informations [ici](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Comment installer MySQL5.6 sur Ubuntu
Nous allons utiliser Linux VM avec Ubuntu à partir d’Azure ici.

- Étape 1 : Installer MySQL Server 5.6 basculer vers `root` utilisateur :

            #[azureuser@mysqlnode:~]sudo su -

    Installer le serveur mysql 5.6 :

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Pendant l’installation, vous verrez une poping de fenêtre de dialogue jusqu’au poser vous permettant de définir MySQL racine mot de passe ci-dessous et vous devez définir le mot de passe ici.

    ![image](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Entrez le mot de passe pour le confirmer.

    ![image](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Étape 2 : Connexion MySQL Server

    Une fois l’installation de serveur MySQL, MySQL service sera démarré automatiquement. Vous pouvez ouvrir une session serveur MySQL avec `root` utilisateur.
    Utiliser l’en dessous de la commande de connexion et entrée de mot de passe.

             #[root@mysqlnode ~]# mysql -uroot -p

- Étape 3 : Gérer le service MySQL en cours d’exécution

    (a) obtenir l’état du service MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) Démarrer le Service MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) arrêter le service MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) redémarrez le service MySQL

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Comment installer MySQL sur famille rouge chapeau OS comme CentOS, Linux Oracle
Nous allons utiliser Linux VM avec CentOS ou Linux Oracle ici.

- Étape 1 : Ajouter le référentiel MySQL Yum commutateur à `root` utilisateur :

            #[azureuser@mysqlnode:~]sudo su -

    Téléchargez et installez le package MySQL :

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Étape 2 : Modifiez en dessous de fichier pour activer le référentiel MySQL pour télécharger le package MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Mettre à jour chaque valeur de ce fichier ci-dessous :

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Étape 3 : Installer MySQL MySQL référentiel MySQL installer :

           #[root@mysqlnode ~]#yum install mysql-community-server

    Dernier MySQL et tous les packages connexes seront installés.

- Étape 4 : Gérer le service MySQL en cours d’exécution

    (a) Vérifiez l’état du service du serveur MySQL :

           #[root@mysqlnode ~]#service mysqld status

    (b) Vérifiez si le serveur de port de MySQL par défaut est en cours d’exécution :

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) démarre le serveur MySQL :

           #[root@mysqlnode ~]#service mysqld start

    (d) arrêter le serveur MySQL :

           #[root@mysqlnode ~]#service mysqld stop

    (e) ensemble MySQL démarre lorsque le système démarrage cumul :

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>Comment installer MySQL sur SUSE Linux
Nous allons utiliser Linux VM avec OpenSUSE ici.

- Étape 1 : Télécharger et installer serveur MySQL

    Basculer vers `root` utilisateur par le biais en dessous de la commande :  

           #sudo su -

    Téléchargez et installez le package MySQL :

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Étape 2 : Gérer le service MySQL en cours d’exécution

    (a) vérifier l’état du serveur MySQL :

           #[root@mysqlnode ~]# rcmysql status

    (b) à cocher si le port par défaut du serveur MySQL :

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) démarre le serveur MySQL :

           #[root@mysqlnode ~]# rcmysql start

    (d) arrêter le serveur MySQL :

           #[root@mysqlnode ~]# rcmysql stop

    (e) ensemble MySQL démarre lorsque le système démarrage cumul :

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Étape suivante
Accéder à davantage l’utilisation et les informations relatives aux MySQL [ici](https://www.mysql.com/).
