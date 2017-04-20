<properties
    pageTitle="Clusterize MySQL avec jeux d’équilibrage de charge | Microsoft Azure"
    description="Configurer une équilibrage de charge haute disponibilité Linux MySQL cluster créé avec le modèle de déploiement classique sur Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Utilisation d’équilibrage de charge ensembles pour clusterize MySQL sur Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


L’objectif de cet article est d’Explorer et illustrer les différentes approches disponibles pour déployer les services Linux hautement disponibles sur Microsoft Azure, exploration haute disponibilité du serveur MySQL comme une introduction. Une vidéo illustrant cette approche est disponible sur [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Nous hiérarchique une solution haute disponibilité sans partage deux nœuds maître unique MySQL basée sur DRBD, Corosync et STIMULATEUR. Un seul nœud s’exécute MySQL à la fois. Lecture et l’écriture de la ressource DRBD sont également limité à un seul nœud à la fois.

Étant donné que nous utilisons ensembles d’équilibrage de charge de Microsoft Azure pour assurer la fonctionnalité cyclique et la détection de point de terminaison, suppression et la récupération ordonnée du protocole VIP n’est pas nécessaire pour une solution VIP comme les VL. L’adresse VIP est une adresse IPv4 globalement prenant affectée par Microsoft Azure lorsque nous créons tout d’abord le service cloud.

Il existe d’autres architectures possibles pour MySQL, y compris le jour ouvré suivant Cluster, Percona et Galera ainsi que plusieurs solutions de logiciels intermédiaires, y compris au moins une disponibles en tant qu’une machine virtuelle sur [Machine virtuelle dépôt](http://vmdepot.msopentech.com). Tant que ces solutions peuvent répliquer sur monodiffusion ou multidiffusion ou diffusion et ne s’appuient sur stockage partagé ou plusieurs interfaces réseau, les scénarios doivent être faciles à déployer sur Microsoft Azure.

Bien entendu ces architectures cluster peuvent être prolongées à d’autres produits tels que PostgreSQL et OpenLDAP de façon similaire. Par exemple, cette procédure équilibrage avec rien partagé a été correctement testée avec plusieurs maîtres OpenLDAP, et vous pouvez de regarder notre blog Channel 9.

## <a name="getting-ready"></a>Vous vous préparez

Vous devrez un compte Microsoft Azure avec un abonnement valide en mesure de créer des machines virtuelles au moins deux (2) (x a été utilisée dans cet exemple), définir un réseau et un sous-réseau, un groupe affinité et disponibilité, ainsi que la possibilité pour créer des disques durs virtuels nouveau dans la même région en tant que le service cloud et les attacher aux ordinateurs virtuels Linux.

### <a name="tested-environment"></a>Environnement testé

- Ubuntu 13.10
  - DRBD
  - Serveur MySQL
  - Corosync et STIMULATEUR

### <a name="affinity-group"></a>Affinité du groupe

Un groupe affinité pour la solution est créé en vous connectant dans le Azure classique portail défilement vers le bas jusqu'à paramètres et créer un nouveau groupe affinité. Ressources allouées créés ultérieurement doivent être affectées à ce groupe affinité.

### <a name="networks"></a>Réseaux

Un nouveau réseau est créé et un sous-réseau est créé au sein du réseau. Nous avons choisi un réseau 10.10.10.0/24 avec /24 qu’un seul sous-réseau à l’intérieur.

### <a name="virtual-machines"></a>Machines virtuelles

La première virtuelle de 13.10 Ubuntu est créée à l’aide d’une image de la galerie de Ubuntu Endorsed et appelée `hadb01`. Un service en nuage est créé dans le processus, appelé hadb. Nous appelez de cette façon afin d’illustrer la nature partagée, équilibrage de charge le service aura quand nous ajouter davantage de ressources. La création de `hadb01` est se déroule normalement et rempli à l’aide du portail. Un point de terminaison événementsSSH est créé automatiquement, et notre réseau créé est sélectionné. Nous avons également choisir de créer une nouveau disponibilité défini pour les ordinateurs virtuels.

Une fois que la première machine virtuelle est créée (techniquement, lorsque le service cloud est créé) nous procéder pour créer la deuxième machine virtuelle, `hadb02`. Pour la deuxième machine virtuelle nous utiliserons également Ubuntu 13.10 machine virtuelle à partir de la galerie à l’aide du portail, mais nous allons choisir d’utiliser un service cloud existant, `hadb.cloudapp.net`, au lieu de créer un nouvel identifiant. Le jeu de réseau et disponibilité doit déjà être sélectionné automatiquement pour nous. Un point de terminaison SSH est créé, trop.

Une fois les deux machines virtuelles ont été créés, nous prend note du port SSH pour `hadb01` (TCP 22) et `hadb02` (automatiquement affectée par Azure)

### <a name="attached-storage"></a>Stockage lié

Nous joindre un nouveau disque à deux machines virtuelles et créer de nouveaux disques 5 Go dans le processus. Les disques seront hébergés dans le conteneur de disque dur virtuel en cours d’utilisation pour notre disques système d’exploitation principal. Une fois que les disques sont créées et joint n’est pas nécessaire pour que nous puissions redémarrez Linux comme le noyau verrez le nouvel appareil (généralement `/dev/sdc`, vous pouvez vérifier `dmesg` pour la sortie)

Sur chaque ordinateur virtuel nous continuer pour créer une nouvelle partition à l’aide `cfdisk` (principale, partition Linux) et d’écrire la nouvelle table. **Ne créez pas un système de fichiers sur cette partition** .

## <a name="setting-up-the-cluster"></a>La configuration du cluster

Dans les deux machines virtuelles Ubuntu, nous devons utiliser APT pour installer Corosync, STIMULATEUR et DRBD. À l’aide de `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**N’installez pas MySQL pour le moment** . Debian et les scripts d’installation Ubuntu initialisera un répertoire de données MySQL sur `/var/lib/mysql`, mais étant donné que le répertoire sera remplacé par un système de fichiers DRBD, nous avons besoin de le faire ultérieurement.

À ce stade, nous devons vérifier également (à l’aide de `/sbin/ifconfig`) que les deux machines virtuelles utilisent les adresses dans le sous-réseau 10.10.10.0/24 et qu’ils peuvent eux ping par nom. Si vous le souhaitez vous pouvez également utiliser `ssh-keygen` et `ssh-copy-id` pour vous assurer que les deux machines virtuelles peuvent communiquer via SSH sans exiger un mot de passe.

### <a name="setting-up-drbd"></a>La configuration DRBD

Nous allons créer une ressource DRBD qui utilise sous-jacent `/dev/sdc1` partition pour produire un `/dev/drbd1` ressource peut être mis en forme à l’aide d’ext3 et utilisés dans les nœuds principaux et secondaires. Pour ce faire, ouvrez `/etc/drbd.d/r0.res` et copiez la définition de la ressource suivante. Procédez comme suit dans les deux machines virtuelles :

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Après cela, initialisation de la ressource à l’aide `drbdadm` dans les deux machines virtuelles :

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Et enfin, sur le serveur principal (`hadb01`) forcer la propriété (primaire) de la ressource DRBD :

    sudo drbdadm primary --force r0

Si vous examinez le contenu de/procédure/drbd (`sudo cat /proc/drbd`) sur les deux machines virtuelles, vous devriez voir `Primary/Secondary` sur `hadb01` et `Secondary/Primary` sur `hadb02`cohérente avec la solution à ce stade. Le disque 5 Go est synchronisé via le réseau 10.10.10.0/24 sans frais aux clients.

Une fois que le disque est synchronisé, vous pouvez créer le système de fichiers sur `hadb01`. Aux fins de test, nous avons utilisé ext2, mais l’instruction suivante crée un système de fichiers ext3 :

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>Montage de la ressource DRBD

Sous `hadb01` nous êtes maintenant prêts à monter les ressources DRBD. Utiliser Debian et dérivés `/var/lib/mysql` comme répertoire de données de MySQL. Étant donné que nous n’avons pas installé MySQL, nous allons créer le répertoire et monter la ressource DRBD. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>La configuration de MySQL

À présent que vous êtes prêt à installer MySQL sur `hadb01`:

    sudo apt-get install mysql-server

Pour `hadb02`, vous avez deux possibilités. Vous pouvez installer serveur mysql maintenant, qui créer /var/lib/mysql et remplissez-la avec un nouveau répertoire de données, puis procédez à la suppression du contenu. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La deuxième option consiste à basculement vers `hadb02` , puis installez serveur mysql il (scripts d’installation remarquerez l’installation existante et ne touchez)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si vous ne prévoyez basculement DRBD maintenant, la première option est plus facile bien que sans doute moins élégant. Une fois que vous configuré ces paramètres, vous pouvez commencer à travailler sur votre base de données MySQL. Sous `hadb02` (ou quel que soit celui des serveurs est actif, en fonction de DRBD) :

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Avertissement**: cette dernière instruction désactive efficacement l’authentification de l’utilisateur racine dans ce tableau. Il doit être remplacé par votre note production accorder des instructions et est fourni uniquement à titre d’exemple.

Vous devez également activer la mise en réseau pour MySQL si vous voulez effectuer des requêtes à partir de, à l’extérieur les ordinateurs virtuels, ce qui correspond à l’objectif de ce guide. Sur les deux machines virtuelles, ouvrez `/etc/mysql/my.cnf` , puis naviguez vers `bind-address`, remplaçant 127.0.0.1 0.0.0.0. Après avoir enregistré le fichier, émettre un `sudo service mysql restart` sur votre moniteur principal en cours.

### <a name="creating-the-mysql-load-balanced-set"></a>Création de la charge MySQL équilibrée ensemble

Nous revenez au portail et accédez à la `hadb01` machine virtuelle, puis points de terminaison. Nous sera créer un point de terminaison, choisissez MySQL (TCP 3306) dans les graduations dans la zone *Créer nouveau équilibrage jeu* et la liste déroulante. Nous appelons notre point de terminaison équilibrée de charge `lb-mysql`. Nous allons conserver la plupart des options uniquement à l’exception de temps ce qui nous allons réduire à 5 (secondes, minimales)

Après avoir créé le point de terminaison nous atteindre `hadb02`, points de terminaison et créer un point de terminaison, mais nous allons choisir `lb-mysql`, puis sélectionnez MySQL dans le menu déroulant. Vous pouvez également utiliser l’infrastructure du langage commun Azure pour cette étape.

Pour l’instant, nous avons tout ce dont nous avons besoin pour une opération manuelle du cluster.

### <a name="testing-the-load-balanced-set"></a>Test de la charge équilibrée ensemble

Tests peuvent être effectuées à partir d’un ordinateur externe, à l’aide de n’importe quel client MySQL, ainsi que les applications (par exemple, phpMyAdmin en cours d’exécution sous forme d’un site Web Azure) dans ce cas, nous avons utilisé outil de ligne de commande de MySQL sur une autre zone Linux :

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Basculement manuellement

Vous pouvez simuler basculement maintenant en arrêt MySQL, passer principal de DRBD et redémarrer MySQL.

Sous hadb01 :

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Puis, sous hadb02 :

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Une fois que vous basculement manuellement vous pouvez répéter votre requête à distance et il doivent fonctionne parfaitement.

## <a name="setting-up-corosync"></a>La configuration Corosync

Corosync est l’infrastructure cluster sous-jacente requis pour STIMULATEUR pour l’utiliser. Pour les utilisateurs v1 et v2 pulsation (et d’autres méthodes comme Ultramonkey) Corosync est un fractionnement des fonctionnalités CRM, tandis que STIMULATEUR reste plus proche de passer les pulsations en termes de fonctionnalité.

La contrainte principale pour Corosync sur Azure est que Corosync préfère multidiffusion diffusion sur les communications monodiffusion, mais mise en réseau Microsoft Azure prend uniquement en charge monodiffusion.

Peut être effectué en, Corosync dispose d’un mode de monodiffusion de travail et la seule véritable contrainte est que, dans la mesure où tous les nœuds ne sont pas communiquer entre eux *State*, vous devez définir les nœuds dans vos fichiers de configuration, y compris leurs adresses IP. Nous pouvons utiliser les fichiers exemple Corosync pour monodiffusion et changez simplement lier adresse, des listes de nœuds et répertoire de journalisation (Ubuntu utilise `/var/log/corosync` lors de l’exemple utiliser des fichiers `/var/log/cluster`) et en activant outils quorum.

**Note la `transport: udpu` directive ci-dessous et les adresses IP définis manuellement pour les nœuds**.

Sous `/etc/corosync/corosync.conf` pour les deux nœuds :

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Nous copier ce fichier de configuration dans les deux machines virtuelles et démarrer des Corosync dans les deux nœuds :

    sudo service start corosync

Peu de temps après le démarrage du service cluster il convient de l’anneau active et quorum doit constituer. Nous pouvons vérifier cette fonctionnalité en passant en revue les journaux ou :

    sudo corosync-quorumtool –l

Suivre une sortie similaire à l’image ci-dessous :

![corosync quorumtool - l exemple de sortie](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>La configuration de STIMULATEUR

STIMULATEUR utilise le cluster pour surveiller des ressources, définir lorsque primaires aller vers le bas et atteindre ces ressources secondaires. Ressources peuvent être définis à partir d’un ensemble de scripts disponibles ou de scripts (comme initialisation) LSB parmi les autres choix.

Nous voulons STIMULATEUR à « posséder » la ressource DRBD, le point de montage et le service MySQL. Si STIMULATEUR peut activer et désactiver DRBD, monter / umount et vous démarrer/arrêter MySQL dans l’ordre approprié lorsque quelque chose incorrecte se passe-t-il avec l’image principale, notre le programme d’installation est terminée.

Lorsque vous installez STIMULATEUR pour la première fois, votre configuration doit être opération assez simple, par exemple :

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Vérifiez en exécutant `sudo crm configure show`. À présent, créez un fichier (par exemple, `/tmp/cluster.conf`) avec les ressources suivantes :

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Et maintenant chargez-le dans la configuration correcte (uniquement pour effectuer cette opération dans un seul nœud) :

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

En outre, vérifiez que STIMULATEUR commence au démarrage dans les deux nœuds :

    sudo update-rc.d pacemaker defaults

Après quelques secondes et l’utilisation de `sudo crm_mon –L`, vérifiez qu’un de vos nœuds est devenu le masque pour le cluster et toutes les ressources est en cours d’exécution. Vous pouvez utiliser montage et ps pour vérifier que les ressources sont en cours d’exécution.

La capture d’écran suivant montre `crm_mon` avec un nœud arrêté (quitter à l’aide de CTRL + C)

![nœud crm_mon arrêté](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

Et cette capture d’écran montre les deux nœuds, avec un seul masque et un esclave :

![crm_mon opérationnelles maître/esclave](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Test

Nous sommes prêts pour une simulation basculement automatique. Il existe deux manières de procéder : souple et strict. La façon de logicielle est à l’aide de fonction d’arrêt du cluster : ``crm_standby -U `uname -n` -v on``. Utilisez cette option dans le masque du document, secondaire aura sur. N’oubliez pas ce réglage OFF (crm_mon vous indiquera un nœud est en veille dans le cas contraire)

Disque dur apparaisse est arrêter la machine virtuelle principale (hadb01) via le portail ou en modifiant le niveau d’exécution sur l’ordinateur virtuel (arrêt, arrêt) puis nous aidons Corosync et STIMULATEUR par signalisation allant de la forme vers le bas. Nous pouvons tester ceci (utiles pour les fenêtres de maintenance), mais nous pouvons également forcer le scénario en figeant simplement la machine virtuelle.

## <a name="stonith"></a>STONITH

Il doit être possible d’émettre un arrêt machine virtuelle via l’infrastructure du langage commun Azure à la place d’un script STONITH qui contrôle un périphérique physique. Vous pouvez utiliser `/usr/lib/stonith/plugins/external/ssh` sous forme de base et activer STONITH dans la configuration du cluster. Azure infrastructure du langage commun doit être installé globalement et le profil/paramètres de publication doit être chargé pour l’utilisateur du cluster.

Exemples de code pour la ressource disponible sur [GitHub](https://github.com/bureado/aztonith). Vous devez modifier la configuration du cluster en ajoutant des options suivantes pour `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Remarque :** le script n’effectue pas haut/bas tests. La ressource SSH d’origine avait 15 chèques ping mais temps de récupération pour une machine virtuelle Azure peut être plus variable.

## <a name="limitations"></a>Limitations

Les limitations suivantes s’appliquent :

- Le script de ressources DRBD linbit qui gère DRBD en tant que ressource dans des utilisations STIMULATEUR `drbdadm down` lorsque vous arrêtez un nœud, même si le nœud est sur le point en attente. Ce n’est pas idéal car secondaire sera être se synchronisent pas la ressource DRBD tandis que le masque des obtient écrit. Si la forme de base n’échoue pas simple, secondaire peut prendre un état du système de fichiers plus ancien. Il existe deux manières potentiels de résolution ceci :
  - Appliquer un `drbdadm up r0` dans tous les nœuds par le biais de surveillance (pas clusterized) local, ou,
  - Modification de la linbit DRBD script vérifiant que `down` n’est pas appelée, en `/usr/lib/ocf/resource.d/linbit/drbd`.
- Équilibrage de charge doit au moins 5 secondes pour répondre, afin que les applications doivent être cluster prenant en charge et être plus tolérance de délai d’expiration ; autres architectures peut vous aider, par exemple files d’attente dans l’application, middlewares de requête, etc..
- Optimisation des MySQL est nécessaire pour assurer écriture est effectuée à un rythme concentré et vidage de cache disque aussi souvent que possible pour limiter la perte de mémoire
- Écrire des performances seront dépendantes dans machine virtuelle interconnect dans le commutateur virtuel car il s’agit du mécanisme utilisé par DRBD pour répliquer le périphérique
