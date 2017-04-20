<properties
   pageTitle="Guide de démarrage rapide pour l’installation manuelle de HANA SAP sur machines virtuelles Azure | Microsoft Azure"
   description="Guide de démarrage rapide pour l’installation manuelle de HANA SAP sur machines virtuelles Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guide de démarrage rapide pour l’installation manuelle d’uniques SAP HANA sur machines virtuelles Azure

## <a name="introduction"></a>Introduction

Ce guide de démarrage rapide qui permet de définir un uniques SAP HANA prototype/démo système sur machines virtuelles Azure par une installation manuelle de SAP NetWeaver 7.5 et SAP HANA SP12.
Le guide suppose que le lecteur est familiarisé avec base de Azure IaaS, telles que le déploiement des machines virtuelles ou des réseaux virtuels via le portail Azure ou Powershell/infrastructure du langage commun, y compris la possibilité d’utiliser des modèles de json. Par ailleurs, il est prévu que le lecteur est familiarisé avec HANA SAP, SAP NetWeaver et comment l’installer en local.

Il est prévu que le lecteur connaît de la documentation SAP Azure générale mentionnée dans la section informations générales à la fin de cet article.

En raison de la restriction aux systèmes de production non ce guide porte pas thèmes comme HA, sauvegarder, DR, haute performance ou considérations de sécurité particulières.

L’exemple de configuration effectué à l’aide de deux machines virtuelles atteindre une installation SAP NetWeaver distribuée via le modèle de gestionnaire de ressources Azure SAP-Linux-Azure est uniquement prise en charge sur dans le Gestionnaire de ressources Azure et non par le modèle classique. Vous trouverez des liens vers des informations supplémentaires sur le Gestionnaire de ressources Azure dans la section informations générales à la fin de cet article.

Il s’agissait de deux test ordinateurs virtuels utilisés pour l’installation de l’exemple :

* Hana-appsrv (type DS3) pour héberger l’instance NW 7.5 ASC + PAS
* Hana-dbsrv (type GS4) à l’hôte SP12 HANA
* les deux machines virtuelles appartenant à un réseau virtuel Azure (azure-hana-test-vnet)
* Système d’exploitation dans les deux cas a été SLES 12 SP1


N’oubliez pas du fait que qu’à partir de juillet 2016 HANA SAP est uniquement entièrement pris en charge pour les systèmes de production OLAP (bande passante) sur le type de machine virtuelle Azure GS5. Dans lequel une attend pas soutien SAP à des fins de test, il est prêt utilise une plus petite comme par exemple, GS4.
Que doit toujours être utilisé pour HANA SAP sur Azure stockage Azure premium pour les fichiers de données et le journal HANA - consultez « Configuration disque » section tout en bas. En ce qui concerne les détails supplémentaires sur le SAP produits sont prises en charge sur Azure Vérifiez la section informations générales à la fin de cet article.


Le guide décrit deux façons différentes pour installer manuellement HANA SAP sur machines virtuelles Azure :

* installer SAP HANA via SAP logiciel mise en service Manager (SWPM) dans le cadre d’une installation NetWeaver distribuée dans l’étape « instance de base de données »
* Installez HANA SAP à l’aide de la hdblcm outil HANA Cycle de vie Manager, puis NetWeaver par la suite

Il est également possible d’utiliser SWPM et installer tous les composants (SAP HANA, serveur d’applications SAP, instance ASC, SAP graphique) dans une machine virtuelle unique. Cette option n’est pas décrite dans cet article, mais les éléments qui doivent être considérées comme sont identiques.

Avant de commencer une installation, la section se trouvant après les listes de contrôle ci-dessous sur la configuration des ordinateurs test Azure virtuels doit être lu pour éviter les erreurs de base plusieurs qui seront produit lorsque vous utilisez uniquement une configuration machine virtuelle Azure par défaut.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Liste de vérification des installation SAP HANA via SWPM SAP

Il s’agit d’une simple liste de vérification de la clé éléments associés à une installation SAP HANA uniques manuelle pour démo ou prototypage pursposes via SWPM SAP effectuant un 7.5 NW SAP distribué installer. Les éléments individuels sont expliqués et indiqués dans le formulaire de captures d’écran plus en détail dans l’ensemble de l’article :

* créer un réseau virtuel Azure incluant des ordinateurs deux test virtuels 
* déployer deux Azure machines virtuelles avec OS SLES 12 SP1 via le modèle d’Azure le Gestionnaire de ressources 
* joindre deux disques standard de stockage sur le serveur application machine virtuelle (par exemple, 75 et 500 Go)
* joindre des quatre disques sur le serveur de base de données HANA machine virtuelle - disques de stockage standard 2 comme pour le serveur application machine virtuelle + 2 disques de stockage premium (par exemple, 2x512GB)
* selon la taille et/ou débit exigences joindre plusieurs disques et créer des volumes rayées soit à l’aide de gestionnaire de volume logique ou mdadm au niveau du système d’exploitation à l’intérieur de la machine virtuelle
* créer des systèmes de fichiers XFS disques joints / logique volumes
* Monter les systèmes de fichiers XFS nouveau au niveau du système d’exploitation. Utilisez un système de fichiers pour tous les logiciels SAP et l’autre, par exemple pour le répertoire sapmnt et peut-être effectuer des sauvegardes régulières. Sur la base de données SAP HANA montage server la XFS systèmes de fichiers sur les disques de stockage premium en tant que /hana et /usr/sap il s’agit tous nécessaire pour éviter que le système de fichiers racine qui n’est pas trop volumineux sur machines virtuelles Linux Azure remplit
* Entrez l’adresse ip locale du test machines virtuelles dans/etc/hosts
* Entrez le paramètre nofail dans/etc/fstab
* définir les paramètres de noyau en fonction de la note SAP HANA-SLES-12 (voir détails plus bas dans la section Paramètres noyau)
* ajouter de l’espace de permutation
* Si vous voulez que - installer un graphique Bureau sur le test machines virtuelles. Sinon, utilisez une installation sapinst à distance
* Téléchargez le logiciel SAP SAP service marketplace
* Installez l’instance ASC SAP sur le serveur application machine virtuelle
* Partagez le répertoire sapmnt via NFS entre le test machines virtuelles (application serveur machine virtuelle est le serveur NFS)
* installer l’instance de base de données, y compris HANA via SWPM sur le serveur de base de données machine virtuelle
* Installez PAS sur le serveur application machine virtuelle
* Démarrez SAP MC et connectez-vous, par exemple via l’interface SAP / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Liste de vérification des installation SAP HANA via hdblcm

Il s’agit d’une simple liste de vérification de la clé éléments associés à une installation SAP HANA uniques manuelle pour démo ou prototypage pursposes via SWPM SAP effectuant un 7.5 NW SAP distribué installer. Les éléments individuels sont expliqués et indiqués dans le formulaire de captures d’écran plus en détail dans l’ensemble de l’article :

* créer un réseau virtuel Azure incluant des ordinateurs deux test virtuels 
* déployer deux Azure machines virtuelles avec OS SLES 12 SP1 via le modèle d’Azure le Gestionnaire de ressources 
* joindre deux disques standard de stockage sur le serveur application machine virtuelle (par exemple, 75 et 500 Go)
* joindre des quatre disques sur le serveur de base de données HANA machine virtuelle - 2 stockage standard comme pour le serveur application machine virtuelle + 2 disques de stockage premium (par exemple, 2x512GB)
* selon la taille et/ou débit exigences joindre plusieurs disques et créer des volumes rayées soit à l’aide de gestionnaire de volume logique ou mdadm au niveau du système d’exploitation à l’intérieur de la machine virtuelle
* créer des systèmes de fichiers XFS disques joints / logique volumes
* Monter les systèmes de fichiers XFS nouveau au niveau du système d’exploitation. Utilisez un système de fichiers pour tous les logiciels SAP et l’autre, par exemple pour le répertoire sapmnt et peut-être effectuer des sauvegardes régulières. Sur la base de données SAP HANA montage server la XFS systèmes de fichiers sur les disques de stockage premium en tant que /hana et /usr/sap il s’agit tous nécessaire pour éviter que le système de fichiers racine qui n’est pas trop volumineux sur machines virtuelles Linux Azure remplit
* Entrez l’adresse ip locale du test machines virtuelles dans/etc/hosts
* Entrez le paramètre nofail dans/etc/fstab
* définir les paramètres de noyau en fonction de la note SAP HANA-SLES-12 (voir détails plus bas dans la section Paramètres noyau)
* ajouter de l’espace de permutation
* Si vous voulez que - installer un graphique Bureau sur le test machines virtuelles. Sinon, utilisez une installation sapinst à distance
* Téléchargez le logiciel SAP SAP service marketplace
* créer un groupe « sapsys » avec l’id du groupe 1001 sur l’ordinateur de serveur virtuel HANA DB
* Installez HANA SAP sur le serveur de base de données machine virtuelle à l’aide de hdblcm
* Installez l’instance ASC SAP sur le serveur application machine virtuelle
* Partagez le répertoire sapmnt via NFS entre le test machines virtuelles (application serveur machine virtuelle est le serveur NFS)
* installer l’instance de base de données, y compris HANA via SWPM sur le serveur de base de données machine virtuelle
* Installez PAS sur le serveur application machine virtuelle
* Démarrez SAP MC et connectez-vous, par exemple via l’interface SAP / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Préparer l’installation manuelle de SAP HANA machines virtuelles Azure

Ce chapitre concernant la préparation de la machines virtuelles Azure pour l’installation manuelle de SAP HANA comporte cinq sections couvrant les rubriques suivantes :

* Programme d’installation de disque
* Paramètres de noyau
* Systèmes de fichiers
* / etc/hosts
* / etc/fstab


### <a name="disk-setup"></a>Programme d’installation de disque

Le système de fichiers racine dans un VM Linux sur Azure est de taille réduite. Par conséquent, il est nécessaire joindre un espace disque supplémentaire à une machine virtuelle pour l’exécution de SAP. Dans le cas d’un serveur d’applications SAP que machine virtuelle utilisée dans un environnement pur prototype/démo il est prêt à utiliser des disques de stockage standard Azure. Que pour les fichiers journaux et de données SAP HANA DB - disques de stockage Azure Premium doivent être utilisés même dans un environnement hors production.

Afficher des détails sur la façon de joindre des disques à une VM Linux [ici](virtual-machines-linux-add-disk.md)

Lorsqu’il s’agit de la mise en cache disque Azure - une doit utiliser « Aucun » pour les disques qui seront utilisés pour stocker les journaux des transactions HANA. Pour HANA est-il OK à utiliser des fichiers de données Lisez la mise en cache. Comme HANA est une base de données en mémoire que tout dépend le modèle global d’utilisation quantité le cache de lecture au niveau de disque Azure améliore les performances (par exemple, en commençant HANA et la lecture de données à partir du disque en mémoire).

Afficher des détails sur le stockage Azure Premium [ici](../storage/storage-premium-storage.md)

[Ici](https://github.com/Azure/azure-quickstart-templates) vous trouvez des exemples de modèles json pour créer des machines virtuelles.
Le « 101-machine virtuelle-simple-linux » montre comment un modèle de base ressemble, y compris la section de stockage qui ajoute un disque de données de 100 Go.

[Cet article](virtual-machines-linux-sap-on-suse-quickstart.md) inclut des informations sur la façon de trouver une image SUSE via Powershell ou infrastructure du langage commun et l’importance pour joindre un disque via UUID.


Selon la taille de la configuration système et débit requise, il peut être nécessaire joindre plusieurs disques au lieu d’un et version ultérieure sur Créer un agrégat sur ces disques au niveau du système d’exploitation. Voici les deux aspects pourquoi une créez un agrégat sur plusieurs disques Azure :

* augmenter le débit
* vous avez besoin d’un seul système de fichiers > 1 to comme la limite de taille disque Azure est de 1 To (à partir de juillet 2016)


Plus d’informations sur les deux principaux outils pour configurer la répartition sont accessibles ici :

[Article sur l’utilisation de mdadm pour configurer le Linux logiciel raid sur un ordinateur virtuel Azure](virtual-machines-linux-configure-raid.md)

[Article sur la configuration du Gestionnaire de volumes logiques sur un ordinateur virtuel Azure Linux](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Dans le test de deux disques de stockage standard Azure environnement ont été associés au serveur d’application SAP machine virtuelle. Un a été utilisé pour stocker tous les logiciels SAP pour l’installation (par exemple, NetWeaver 7.5, interface utilisateur SAP, SAP HANA... ) et l’autre disposer de suffisamment d’espace pour tout ce qui peut s’avérer nécessaire (par exemple, sauvegarder, données de test) ainsi que le répertoire sapmnt (par exemple, des profils SAP) à partager entre tous les ordinateurs virtuels appartenant à la même paysage SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Contrairement au serveur application disques machine virtuelle quatre ont été connectés au serveur SAP HANA machine virtuelle. Comme avant de deux disques ont été utilisés pour tout en conservant le logiciel SAP (une peut également partager le disque logiciel SAP via NFS) et disposer de suffisamment d’espace, par exemple pour la sauvegarde. Les deux disques supplémentaires ont été disques de stockage Azure Premium pour maintenir SAP HANA données et les fichiers journaux, ainsi que le répertoire /usr/sap.


### <a name="kernel-parameters"></a>Paramètres de noyau


SAP HANA nécessite des réglages de Linux qui ne font pas partie des images Galerie Azure standard et vous devez définir manuellement. Il existe une note spécifique SAP qui décrit les paramètres. 


Remarque SAP SAP HANA DB : OS paramètres recommandés pour SLES 12 / SLES pour SAP Applications 12 : [SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Une rubrique supplémentaire reagrding page-cache lié à l’exécution SAP HANA SLES sont accessibles [ici](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) dans chapitre 6.1 noyau : limite du Cache de la Page

Il existe également une Note SAP en ce qui concerne la limite de cache de page [SAP Note 1557506](https://service.sap.com/sap/support/notes/1557506)

SLES 12 contient un nouvel outil qui remplace l’ancien utilitaire sapconf. Il est « réglé membres responsables autorisés » et un profil SAP HANA spécial est disponible. Une trouverez plus d’informations sur cet outil suivant les deux liens ci-dessous.

Documentation SLES sur membres responsables autorisés réglé profil sap-hana sont accessibles [ici](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

Documentation SLES sur membres responsables autorisés réglé du profil sap-hana - chapitre 6.2 Optimisation des systèmes de charges de travail SAP avec réglé membres responsables autorisés - sont accessibles [ici](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Ici une pouvez voir comment « réglé membres responsables autorisés » modifié le transparent_hugepage ainsi que les valeurs numa_balancing selon les paramètres du SAP HANA requis.


Pour conserver les réglages SAP HANA ait à utiliser grub2 sur 12 SLES. Vous pouvez trouver des informations complémentaires sur grub2 [ici](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Cette capture d’écran montre comment les réglages ont été modifiées dans le fichier de configuration et ensuite « compilés « via grub2 mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Une autre option serait de modifier les paramètres via Yast et les réglages de paramètre chargeur de démarrage.


### <a name="filesystems"></a>Systèmes de fichiers 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Ici, un peut voir deux systèmes de fichiers qui ont été créés sur le serveur d’application SAP machine virtuelle sur les deux disques de stockage Azure standard. Les deux systèmes de fichiers sont de type XFS et monter /sapdata et /sapsoftware.

Il n’est pas obligatoire pour faire de cette façon. Il existe différentes options de la structure de l’espace disque.
L’aspect le plus important est d’éviter que le système de fichiers racine manque d’espace. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

En ce qui concerne la base de données de HANA SAP machine virtuelle, il est important de savoir que lors d’une installation de base de données via sapinst (swpm) et uniquement à l’aide de l’option d’installation « standard » simple il installe contenus par défaut sous /hana et /usr/sap. Le paramètre par défaut pour la sauvegarde de journal HANA SAP est par exemple, sous /usr/sap.
J’aime avant qu’il soit clé afin d’éviter que le système de fichiers racine manque d’espace. Par conséquent une devez vous assurer qu’il existe suffisamment d’espace libre sous /hana et /usr/sap avant d’installer HANA SAP via swpm.

[Cet article](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) à partir de SAP décrit la disposition du système de fichiers standard de HANA SAP 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Lorsque vous installez SAP NetWeaver sur une photo de la galerie SLES 12 Azure standard il y ait un message qu’il n’existe aucun espace de remplacement. Pour supprimer ce message une peut par exemple ajouter manuellement un fichier d’échange comme décrit dans ce document via jj, mkswap et swapon. Rechercher uniquement des « Ajouter un intervertir fichier manuellement » dans [cet article](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

Une autre option consiste à configurer espace d’échange via l’agent Linux VM. Plus d’informations sont accessibles [ici](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>/ etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Un autre aspect important avant de commencer à installer SAP consiste à inclure les noms d’hôtes et adresses IP des ordinateurs virtuels SAP dans le fichier/etc/hosts. Une doit déployer tous les ordinateurs SAP virtuels au sein d’un réseau virtuel Azure, puis utilisez les adresses IP internes.

### <a name="etcfstab"></a>/ etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Pendant la phase de test celle-ci activée pour s’avérer utile pour ajouter le paramètre nofail à fstab. Si un problème survient avec les disques la machine virtuelle serait toujours poser et se bloque pas dans le processus de démarrage. Mais ait à attention comme dans ce cas l’espace disque supplémentaire ne sont ne peut-être pas disponible et processus peuvent compléter le système de fichiers racine. En cas de /hana serait manquant HANA SAP ne démarre que tout.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Installer Bureau Gnome graphique sur SLES 12

Ce chapitre se compose de deux secitions couvrant les rubriques suivantes :

* Installation de bureau Gnome et xrdp sur SLES 12
* En cours d’exécution basé sur Java SAP MC à l’aide de Firefox sur SLES 12

Vous pouvez également utiliser alternatives comme Xterminal, VNC mais Sep 2016 à partir de cet article décrit uniquement xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Installation de bureau Gnome et xrdp sur SLES 12

En particulier pour ceux qui disposent d’arrière-plan de Microsoft Windows et souhaitez utiliser un graphique bureau directement dans les ordinateurs virtuels Linux SAP d’exécution Firefox, Sapinst, graphique SAP, SAP MC ou HANA Studio et peut-être vous connecter à la machine virtuelle via RDP depuis un ordinateur Microsoft Windows, il existe un moyen simple pour y parvenir. Cela peut ne pas convenir, par exemple pour un serveur de base de données de production mais il est OK pour un environnement prototype/démo intégral. Voici la procédure pour installer le bureau Gnome sur un ordinateur Azure SLES 12 virtuel :

installer le bureau gnome par la commande suivante (par exemple, dans une fenêtre mastic) :

   zypper dans motif -t gnome basic

puis installez xrdp pour autoriser la connexion à la machine virtuelle via RDP :

   zypper dans xrdp

Modifiez /etc/sysconfig/windowmanager et affectez le Gestionnaire de windows par défaut Gnome :

   DEFAULT_WM = « gnome »

Exécutez la commande chkconfig pour vous assurer que ce xrdp démarre automatiquement après un redémarrage : 

  commande chkconfig-xrdp niveau 3 sur

dans le cas où il doit être un problème avec la connexion RDP essayez de redémarrer (peut-être hors de la fenêtre mastic) :

  redémarrage /etc/xrdp/xrdp.sh

dans le cas où le redémarrage xrdp comme indiqué ci-dessus ne fonctionnent à cocher s’il existe un fichier .pid et supprimez-la :

  Vérifiez /var/run et recherchez les xrdp.pid   
  supprimer, puis réessayez le redémarrage



### <a name="sap-mc"></a>MC SAP


Pour démarrer le graphique MC SAP basé sur Java déconnecter Firefox en cours d’exécution dans un ordinateur Azure SLES 12 virtuel après avoir installé la Gnome dekstop comme décrit dans la section précédente une obtenez une erreur en raison de manque de plug-in Java navigateur.

L’URL pour démarrer le MC SAP est <server>: 5 < numéro_instance > 13

Vous pouvez trouver plus de détails [ici](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Sur la capture d’écran ci-dessus une pouvez voir comment le message d’erreur peut ressembler à lorsque le plug-in Java navigateur est manquant. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Une option pour résoudre le problème consiste à installer le plug-in manquant via Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Répéter l’URL SAP MC fait apparaître un peu de temps dialogue la première demandant d’activer le plug-in.


Un problème supplémentaire qui peut-être apparaître est un message d’erreur concernant un fichier manquant : javafx.properties cela est très probablement liés à l’installation de Java 1.8 qui est requis pour SAP graphique 7.4

La version IBM Java visible via Yast n’inclut pas ce fichier. La solution est un téléchargement Java d’Oracle.
Vous pouvez trouver un article qui parle ce problème spécifique [ici](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Installation SAP HANA manuelle via SWPM dans le cadre d’une installation NetWeaver 7.5


La liste des captures d’écran suivante montre les principales étapes d’installation SAP NetWeaver 7.5 et SAP HANA SP12 via SWPM (sapinst). Dans le cadre d’un 7.5 NW installation SWPM possède les capacités d’installer également la base de données HANA comme une instance unique.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Environnement seul ABAP application serveur a été installé le test. L’option « Système distribué » a été utilisée pour installer l’instance ASC et l’instance du serveur principal application dans une machine virtuelle Azure et SAP HANA que le système de base de données dans un autre Azure.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Une fois que l’instance ASC est installé sur le serveur application machine virtuelle et est défini sur « vert » dans SAP MC le répertoire sapmnt qui inclut, par exemple le répertoire de profil SAP doit être partagé avec le serveur de base de données SAP HANA machine virtuelle.
L’étape de l’installation de base de données a besoin d’accéder à ces informations. Le meilleur moyen consiste à utiliser NFS qui peuvent être configurés à l’aide de Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

Dans l’application serveur machine virtuelle le répertoire sapmnt doit être partagé via NFS à l’aide des options « rw » ainsi que « no_root_squash ». Valeur par défaut est « ligne » et « root_squash » peut entraîner des problèmes lors de l’installation de l’instance de base de données.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

Sur le serveur de base de données SAP HANA machine virtuelle la sapmnt partager à partir du serveur application machine virtuelle doit être configurée via « client NFS » (par exemple, à l’aide de Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Puis ait à ouvrir une session sur le serveur de base de données SAP HANA machine virtuelle et commencer à SWPM pour atteindre l’étape suivante d’une installation NW 7.5 distribuée - « Instance de base de données ».


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Liés à l’installation SAP HANA il n’est pas en fait trop d’entrer après avoir sélectionné une installation « standard ». En outre le chemin d’accès au support installatiom une doit entrer un identificateur de sécurité de base de données, le nom d’hôte, le numéro d’instance et le mot de passe administrateur système de base de données.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Étape suivante consiste à entrer le mot de passe pour le schéma DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Puis vient la question pour le mot de passe de schéma SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

À la fin doivent être uniquement verts coches en face de chaque phase du processus d’installation de base de données et la boîte de message peu qui s’affiche vous devriez voir « erreur d’exécution de... Instance de base de données a terminé ».

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Après l’installation réussie le MC SAP doivent également afficher l’instance de base de données en tant que « verte » et la liste complète des processus SAP HANA (par exemple, hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Cette capture d’écran montre des parties de la structure de fichier sous /hana/shared qui SWPM créé pendant l’installation HANA. Aucune option pour spécifier un autre chemin d’accès s’est produite. Par conséquent, il est donc important de monter l’espace disque supplémentaire sous /hana avant l’installation SAP HANA via SWPM afin d’éviter que le système de fichiers racine vient à manquer d’espace libre.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Une pouvez visualiser ici la même chose décrite avant dans le répertoire /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

La dernière étape de l’installation ABAP distribuée est le « Application Server Instance principale »


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Une fois le PAS ainsi SAP graphique vous avez installé une vérifie-t-il via la transaction « dbacockpit « aspect droite avec l’installation HANA SAP.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

En dernier étape 1 pu installer SAP HANA Studio sur le serveur de l’application SAP machine virtuelle et connectez-vous à l’instance SAP HANA en cours d’exécution sur le serveur de base de données machine virtuelle.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Installation manuelle de SAP HANA via HANA cycle de vie Gestionnaire outil hdblcm


Outre l’installation HANA SAP dans le cadre d’une installation distribuée via SWPM, il est également réaliste à tout d’abord installer HANA autonome à l’aide de hdblcm, puis installer par exemple, SAP NetWeaver 7.5 par la suite. La liste des captures d’écran ci-dessous montre comment cela fonctionne.

Voici trois sources d’informations sur l’outil hdblcm HANA :

[Choix de la HDBLCM HANA SAP correcte pour votre tâche](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Outils de gestion du cycle de vie SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Guide d’Installation et mise à jour](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Pour éviter de rencontrer des problèmes plus tard avec un paramètre d’id de groupe par défaut pour les \<HANA SID\>utilisateur adm (créé par l’outil hdblcm) il doit définir un nouveau groupe appelé « sapsys » avec l’id du groupe 1001 avant d’installer HANA SAP à l’aide de hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Démarrage hdblcm la première fois sera un où un a pour sélectionner l’élément du menu Démarrer simple 1 « nouveau système de l’installation »



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Dans cette capture d’écran une peut afficher toutes les options clées qui ont été entrées avant. Important - répertoires qui ont été nommé pour les volumes de données et de journal HANA ainsi que le chemin d’installation (/ hana/partagés dans cet exemple) et/usr/sap ne doit pas faire partie du système de fichiers racine mais qui appartiennent aux disques données Azure qui ont été attachées à la machine virtuelle comme décrit dans la section configuration machine virtuelle Azure. Cela permet d’éviter le risque que le système de fichiers racine peut manquer d’espace.
Une constatez également que l’utilisateur d’administration HANA comporte des id d’utilisateur 1005 et fait partie du groupe sapsys (id 1001) qui a été défini avant l’installation.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Une vérifie-t-il la HANA \<HANA SID\>détails de l’utilisateur adm (azdadm dans cet exemple) dans/etc/mot de passe



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Après avoir installé HANA SAP à l’aide de hdblcm qu’il puisse être vu dans SAP HANA Studio. Le schéma SAPABAP1 qui contient par exemple, toutes les SAP NetWeaver tables n’est pas disponible encore.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Après avoir installé HANA SAP une peut installer SAP NetWeaver par-dessus. Dans cet exemple qu’il soit effectué à l’aide de « une installation distribuée » via SWPM comme décrit dans la section correspondante ci-dessus.
Si l’installation de l’instance de base de données via SWPM une simplement a entrer les mêmes données en tant qu’avant avec hdblcm (par exemple, nom d’hôte, HANA SID, numéro de l’instance). SWPM ensuite utiliser l’installation HANA existante et ajouter des schémas supplémentaires.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Il s’agit de l’image de l’étape de l’installation SWPM dans laquelle ait à entrer des données concernant le schéma DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Puis SWPM attend d’entrer des données à propos du schéma SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Une fois que vous avez terminée l’installation d’instance de base de données SWPM une peut voir le schéma SAPABAP1 dans HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

Et enfin après l’installation du serveur d’applications SAP et SAP graphique une doit être en mesure de vérifier l’instance HANA DB avec transaction « dbacockpit ».




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Informations générales liées à certification Azure SAP, SAP HANA en cours d’exécution sur le téléchargement du logiciel Azure et SAP

* docu SAP Azure général sur l’exécution de SAP sur Azure avec le système d’exploitation Windows en mode classique : [À l’aide de SAP sur machines virtuelles Windows Azure](virtual-machines-windows-classic-sap-get-started.md)

* informations sur les modèles SAP existants pour l’utilisation par les clients : [Azure des modèles de démarrage rapide pour SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* docu SAP Azure général sur l’exécution de SAP sur Azure avec le système d’exploitation Linux dans le Gestionnaire de ressources Azure modèle : [SAP à l’aide sur les machines virtuelles Linux (machines virtuelles)](virtual-machines-linux-sap-get-started.md)

* certifié annuaire matériels HANA SAP qui répertorie les types de machine virtuelle Azure sont prises en charge de production : [Certifié SAP HANA® matériel Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* informations sur les tailles de machine virtuelle en particulier pour les charges de travail Linux : [tailles pour les machines virtuelles dans Azure](virtual-machines-linux-sizes.md)

* Remarque SAP qui répertorie tous les prises en charge des produits SAP sur Azure et types de machine virtuelle Azure pris en charge pour SAP : [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Note SAP relative à SAP « Amélioration de la surveillance » avec machines virtuelles Linux sur Azure : [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* SAP HANA offrant sur Azure « Instances de grande taille ». Il est important de savoir que ce n’est pas sur l’exécution HANA SAP sur machines virtuelles Azure mais dans un déploiement environnement où les serveurs d’application SAP s’exécutent dans Azure machines virtuelles mais HANA SAP s’exécute sur des serveurs vierge : [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* Remarque SAP des informations sur SAPOSCOL sous Linux : [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Touche surveillance des indicateurs pour SAP sur Microsoft Azure : [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informations sur le Gestionnaire de ressources Azure : [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)

* Informations sur le déploiement Linux machines virtuelles via des modèles : [déployer et gérer des machines virtuelles à l’aide de modèles Azure le Gestionnaire de ressources et l’infrastructure du langage commun Azure](virtual-machines-linux-cli-deploy-templates.md)

* Comparaison de déploiement de modèles entre le Gestionnaire de ressources Azure et classique : [Azure le Gestionnaire de ressources et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../resource-manager-deployment-model.md)

* Téléchargez NetWeaver 7.5 pour Linux/HANA à partir de SAP Service Marketplace :![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Téléchargez HANA SP12 plateforme Edition à partir du SAP Service Marketplace :![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

