<properties
    pageTitle="Exécuter Cassandra avec Linux sur Azure | Microsoft Azure"
    description="Comment exécuter un cluster Cassandra sous Linux dans le Machines virtuelles Azure à partir d’une application Node.js"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>En cours d’exécution Cassandra avec Linux sur Azure et y accéder à partir de Node.js

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Vue d’ensemble
Microsoft Azure est une plateforme cloud ouverte qui exécute Microsoft en tant que bien comme logiciel non-Microsoft qui inclut les systèmes d’exploitation, serveurs d’applications, logiciels intermédiaires messagerie ainsi que des bases de données SQL et NoSQL de ces deux modèles commerciaux et ouvrir la source. Création de services résistants sur nuages publics, y compris Azure requiert une planification et architecture délibérée pour les deux serveurs d’applications en tant que couches de stockage bien. Architecture de stockage distribué de Cassandra naturellement vous aide à construire des systèmes hautement disponibles qui sont tolérance pour les échecs de cluster. Cassandra est une échelle à nuage de base de données NoSQL conservée par Apache Software Foundation en cassandra.apache.org ; Cassandra écrits en Java et par conséquent exécuté sur les deux sur Windows, ainsi que Linux plateformes.

L’objectif de cet article consiste à afficher déploiement Cassandra sur Ubuntu comme un cluster unique et multi-centre de données exploiter les Machines Microsoft Azure et réseaux virtuel. Le déploiement de cluster pour charges de travail optimisé est en dehors de l’étendue de cet article car elle requiert configuration nœuds de disque multiples, conception d’une topologie simultanée appropriée et modélisation pour prendre en charge de la réplication, la cohérence des données, débit et configuration requise de disponibilité des données.

Cette prend article une approche fondamentale pour indiquer ce qu’il est impliquée dans la création d’un cluster Cassandra comparés Docker, Chef ou Marionnette qui peut faciliter le déploiement de l’infrastructure souvent.  

## <a name="the-deployment-models"></a>Les modèles de déploiement
Mise en réseau Microsoft Azure permet le déploiement d’isolé clusters privés, dont l’accès peut être limité à atteindre la sécurité du réseau optimiser à grains fins.  Étant donné que cet article concerne montrant le déploiement Cassandra à un niveau fondamentaux, nous allons étudier pas le niveau de la cohérence et la conception du stockage optimal pour débit. Voici la liste des conditions requises pour notre cluster hypothétique de mise en réseau :

- Systèmes externes ne peuvent pas accéder Cassandra de base de données à partir de dans ou en dehors d’Azure
- Cluster Cassandra doit être derrière un programme d’équilibrage de charge pour le trafic thrift
- Déployer des nœuds Cassandra dans deux groupes dans chaque centre de données pour une disponibilité améliorée
- Verrouiller le cluster ainsi que seul batterie de serveurs d’application a accès à la base de données directement
- Aucun point de terminaison réseau public autre que SSH
- Chaque nœud Cassandra a besoin d’une adresse IP interne fixe

Cassandra peut être déployé à une région Azure unique ou à plusieurs régions en fonction de la nature distribuée de la charge de travail. Modèle de déploiement de plusieurs régions peut être exploitée pour répondre à l’utilisateur final rapprocher un emplacement géographique particulier via l’infrastructure Cassandra même. Nœud intégré réplication prend en charge de Cassandra de la synchronisation entre plusieurs maîtres écrit provenant de plusieurs centres de données et présente une vue cohérente des données pour les applications. Déploiement de plusieurs région peut également aider et la limitation des risques la plus large Azure d’interruption des services. La cohérence ACCORDABLE de Cassandra et la topologie de réplication vous aidera à répondre aux besoins de RPO centralisées des applications.

### <a name="single-region-deployment"></a>Déploiement de la zone unique
Nous démarre avec un déploiement seule région et recueillir foule en créant un modèle de plusieurs région. Mise en réseau virtuelle Azure permet de créer sous-réseaux isolés afin de respecter les exigences de sécurité réseau présentés ci-dessus.  La procédure décrite dans la création du déploiement seule région utilise Ubuntu 14.04 LTS et Cassandra 2.08 ; Toutefois, le processus facilement pouvons-nous adopter aux autres variantes Linux. Voici quelques-unes des caractéristiques systémiques du déploiement région unique.  

**Disponibilité :** Les nœuds Cassandra indiqués dans la Figure 1 sont déployés à deux ensembles de disponibilité afin que les nœuds sont réparties entre plusieurs domaines d’erreur de disponibilité. Machines virtuelles annotées avec chaque jeu de disponibilité est mappé sur 2 domaines d’erreur.  Utilise Microsoft Azure le concept de domaine d’erreur pour gérer le temps d’indisponibilité (par exemple, défaillance logicielle ou matérielle) pendant le concept de la mise à niveau domaine (par exemple, hôte ou invité du système d’exploitation correctifs/mises à niveau, mises à niveau de l’application) est utilisé pour la gestion planifiée temps d’arrêt. Consultez [sinistre et disponibilité des Applications Azure](http://msdn.microsoft.com/library/dn251004.aspx) pour le rôle de panne et mettre à niveau des domaines à la réalisation des disponibilité.

![Déploiement de la zone unique](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figure 1 : Déploiement de région unique

Notez qu’au moment de la rédaction, Azure n’autorise pas le mappage explicite d’un groupe de machines virtuelles à un domaine d’erreur spécifiques ; Par conséquent, même si le modèle de déploiement illustré Figure 1, il est statistique probable que tous les ordinateurs virtuels peuvent être mappés à deux domaines d’erreur au lieu de quatre.

**Équilibrage du trafic Thrift :** Bibliothèques de client Thrift à l’intérieur du serveur web se connectent au cluster via un programme d’équilibrage de charge interne. Cela nécessite la procédure d’ajout de l’équilibrage de charge interne au sous-réseau « données » (voir Figure 1) dans le cadre du service cloud hébergeant le cluster Cassandra. Une fois que l’équilibrage de charge interne est définie, chaque nœud nécessite le point de terminaison équilibrée de charge à ajouter avec les annotations d’un ensemble d’équilibrage de charge avec nom d’équilibrage de charge précédemment définis. Pour plus d’informations, voir [Équilibrage de charge interne Azure ](../load-balancer/load-balancer-internal-overview.md).

**Cluster graines :** Il est important sélectionner les nœuds hautement disponibles la plupart des semences comme les nouveaux nœuds va communiquer avec les nœuds de valeur de départ pour la découverte de la topologie du cluster. Un nœud de chaque jeu de disponibilité est désigné comme nœuds de valeur de départ pour éviter de point de défaillance unique.

**Facteur de réplication et le niveau de cohérence :** Intégré disponibilité et données durabilité de Cassandra est caractérisée par le facteur de réplication (fr - nombre de copies de chaque ligne qui se trouve sur le cluster) et le niveau de cohérence (nombre de copies sur être en lecture/écriture avant de le transmettre à l’appelant). Facteur de réplication est spécifié lors de la création de KEYSPACE (semblable à une base de données relationnel) alors que le niveau de cohérence est spécifié lors de l’envoi de la requête CRUD. Consultez la documentation Cassandra sur [configuration pour la cohérence](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) pour plus d’informations la cohérence et la formule pour le calcul du quorum.

Cassandra prend en charge les deux types de modèles de l’intégrité des données – la cohérence et la cohérence éventuelle ; le facteur de réplication et le niveau de cohérence ensemble détermine si les données seront cohérentes dès qu’une opération d’écriture ou qu’il sera finalement cohérente. Par exemple, spécifiez QUORUM ainsi que le niveau de cohérence toujours pour garantir la cohérence des données pendant que vous n’importe quel niveau de cohérence, en dessous du nombre de copies sur apparaître nécessaire pour atteindre QUORUM (par exemple une) entraîne des données soient finalement cohérentes.

Le cluster 8 nœuds ci-dessus, avec un facteur de réplication de 3 et QUORUM (2 nœuds lire ou écrits la cohérence) en lecture/écriture niveau de cohérence, pouvez survie la perte théorique d’au maximum 1 nœud par groupe de réplication avant le démarrage de l’application constaté que l’échec. Cette procédure suppose que tous les espaces clés ont bien équilibrée en lecture/écriture demandes.  Les paramètres que nous utiliserons pour le cluster déployé sont les suivantes :

Configuration du cluster Cassandra seule région :

| Paramètre de cluster | Valeur | Remarques |
| ----------------- | ----- | ------- |
| Nombre de nœuds (N) | 8   | Nombre total de nœuds dans le cluster |
| Facteur de réplication (HF) | 3 | Nombre de copies d’une ligne donnée |
| Niveau de cohérence (écriture) | QUORUM[(RF/2) +1) = 2] le résultat de la formule est arrondi vers le bas | Écrit au maximum 2 réplicas avant d’envoyer la réponse à l’appelant ; 3e réplica écrit de manière cohérente finalement. |
| Niveau de cohérence (lecture) | QUORUM [(RF/2) + 1 = 2] le résultat de la formule est arrondi vers le bas | Lit 2 réplicas avant d’envoyer la réponse à l’appelant. |
| Stratégie de réplication | NetworkTopologyStrategy voir [Réplication des données](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) dans la documentation Cassandra pour plus d’informations | Prend en charge de la topologie de déploiement et le place réplicas sur des nœuds de sorte que tous les réplicas ne finissent dans le même rack |
| Snitch | GossipingPropertyFileSnitch voir [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) dans la documentation Cassandra pour plus d’informations | NetworkTopologyStrategy utilise un concept de snitch pour déterminer la topologie. GossipingPropertyFileSnitch vous offre une meilleure contrôle lors du mappage de chaque nœud au rack et centre de données. Le cluster utilise puis discussions de se propager ces informations. Il s’agit qui simplifie considérablement dynamique paramètre IP par rapport à PropertyFileSnitch |


**Azure considérations sur Cluster Cassandra :** Machines virtuelles de Microsoft Azure fonctionnalité utilise le stockage Blob Azure de fichier disque ; Stockage Azure enregistre 3 copies de chaque disque pour grande longévité. Cela signifie que chaque ligne de données insérées dans une table Cassandra est déjà stocké dans 3 doubles et par conséquent, la cohérence des données est déjà pris en charge même si le facteur de réplication (HF) est 1. Le problème principal avec facteur de réplication 1 est que l’application rencontrer des temps d’arrêt même si un seul nœud Cassandra échoue. Toutefois, si un nœud est vers le bas pour résoudre les problèmes (par exemple, matériel, défaillances du logiciel système) reconnus par contrôleur de tissu Azure, il met en service un nouveau nœud dans son emplacement à l’aide des même lecteurs de stockage. Mise en service un nouveau nœud pour remplacer l’ancienne peut prendre quelques minutes.  De même pour les activités de maintenance planifiée telles que les modifications de système d’exploitation invité, Cassandra met à niveau et modifications de l’application contrôleur de tissu Azure effectue propagée des nœuds dans le cluster.  Mise à niveau propagée également peut prendre quelques nœuds à la fois et, par conséquent, le cluster peut rencontrer brève indisponibilité pour plusieurs partitions. Toutefois, les données ne soient pas perdues en raison de la redondance du stockage Azure intégrée.  

Pour les systèmes déployés sur Azure qui ne nécessite pas de disponibilité (par exemple, autour 99,9 qui équivaut à 8,76 hr/année ; pour plus d’informations, consultez [Disponibilité](http://en.wikipedia.org/wiki/High_availability) ) vous pourrez peut-être exécuter avec HF = 1 et le niveau de cohérence = 1.  Pour les applications aux exigences de disponibilité, HF = 3 et le niveau de cohérence = QUORUM tolérera le temps d’arrêt d’un des nœuds un réplica. HF = 1 dans traditionnel déploiements (par exemple, localement) ne peuvent pas être utilisés en raison de la perte de données possible dues à des problèmes tels que des problèmes de disque.   

## <a name="multi-region-deployment"></a>Déploiement de plusieurs région
Modèle de réplication et la cohérence centre-bases de données de Cassandra ci-dessus permet le déploiement de plusieurs région prêts à l’emploi sans qu’il soit nécessaire pour les outils externes. Voici différent des bases de données relationnelles traditionnels où la configuration de base de données mise en miroir pour les écritures maîtres multiples peut être très complexe. Cassandra dans une zone multiples configurer utile dans les scénarios d’utilisation, y compris les éléments suivants :

**Déploiement basée sur la proximité :** Applications clients multiples, avec Effacer mappage d’utilisateurs client-à-région, peuvent être bénéficié par latence faible du cluster plusieurs régions. Par exemple une gestion de la formation systèmes pour les établissements d’enseignement peuvent déployer un cluster distribué dans les régions US Extrême-Orient et Ouest pour traiter les campus respectives pour transactions ainsi qu’analytique. Les données peuvent être localement cohérentes à la fois lecture et en écriture et peuvent être finalement cohérentes dans les deux régions. Il existe d’autres exemples, tels que distribution multimédia et rien de commerce et tout le contenu qui gère geo concentré utilisateur base est un exemple d’utilisation bonne ce modèle de déploiement.

**Disponibilité :** Redondance est un facteur clé à la réalisation des disponibilité des logiciels et le matériel. Pour plus d’informations, consultez systèmes Cloud fiables construction sur Microsoft Azure. Sur Microsoft Azure, l’uniquement fiable d’atteindre réelle redondance consiste en déployant un cluster de plusieurs région. Les applications peuvent être déployées en mode actif ou actif-passif et si l’une des régions est vers le bas, Azure le trafic Gestionnaire pouvez rediriger le trafic vers la zone active.  Grâce au déploiement de la zone unique, si la disponibilité est 99,9, un déploiement deux région peut atteindre une disponibilité de 99,9999 calculée par la formule : (1-(1-0.999) *(1-0,999))*100) ; consultez le livre ci-dessus pour plus d’informations.

**Sinistre :** Cluster Cassandra de plusieurs région, si correctement conçue, pouvez résistance défaillances centre de données grave. Si une zone géographique ne fonctionne pas, l’application déployée vers d’autres régions peut démarrer servant les utilisateurs finaux. Comme n’importe quel autres entreprise continuité mises en œuvre, l’application doit être tolérance de perte des données résultant à partir des données dans le pipeline asynchrone. Toutefois, Cassandra permet la récupération beaucoup intensifs que la durée de processus de récupération de base de données traditionnelle. Figure 2 montre le modèle de déploiement de plusieurs régions classiques avec huit nœuds dans chaque région. Les deux régions sont des images de pages en vis-à-vis de l’autre pour la même de symétrie ; conceptions réels varient selon le type de charge de travail (par exemple, transactions ou analytique) RPO, RTO, la cohérence des données et des exigences de disponibilité.

![Déploiement de la région à plusieurs](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

Figure 2 : Déploiement de plusieurs régions Cassandra

### <a name="network-integration"></a>Intégration des réseaux
Jeux de machines virtuelles déployés sur les réseaux privés situés sur deux régions communique avec eux à l’aide d’un tunnel VPN. Le tunnel VPN connecte deux passerelles logiciel sa mise en service au cours du processus de déploiement réseau. Les deux régions ont une architecture réseau similaire en termes de sous-réseaux « web » et « données » ; Mise en réseau Azure permet de créer des sous-réseaux autant selon vos besoins et appliquer des utilisateurs selon les besoins de sécurité du réseau. Lors de la conception de la topologie de cluster notamment latence de communication de centre de données et l’impact de la nécessité du trafic réseau à prendre en considération économique.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Cohérence des données pour le déploiement de centre de données à plusieurs
Distribué déploiements nécessaire de tenir compte de l’impact de topologie cluster sur débit et la disponibilité. Les HF et le niveau de cohérence doivent être sélectionnés de sorte que le quorum ne dépend pas la disponibilité de tous les centres de données.
Pour un système nécessitant la cohérence élevée, un LOCAL_QUORUM pour le niveau de cohérence (pour la lecture et en écriture) va s’assurer que la lecture locale et écriture est remplies à partir des nœuds locales tandis que les données sont répliquées asynchrone aux centres de données distante.  Le tableau 2 récapitule les détails de configuration pour le cluster plusieurs région décrites plus loin dans l’écriture vers le haut.

**Configuration de cluster Cassandra deux région**


| Paramètre de cluster | Valeur | Remarques |
| ----------------- | ----- | ------- |
| Nombre de nœuds (N) | 8 + 8 | Nombre total de nœuds dans le cluster |
| Facteur de réplication (HF) | 3 | Nombre de copies d’une ligne donnée |
| Niveau de cohérence (écriture) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] le résultat de la formule est arrondi vers le bas | 2 nœuds seront écrites dans le centre de données première synchrone ; les 2 nœuds supplémentaires nécessaires pour quorum seront écrites asynchrone dans le centre de données 2ème. |
| Niveau de cohérence (lecture) | LOCAL_QUORUM ((RF/2) + 1) = 2 le résultat de la formule est arrondi vers le bas | Requêtes de lecture sont satisfaites à partir d’une région ; 2 nœuds sont lues avant d’envoyer la réponse au client. |
| Stratégie de réplication | NetworkTopologyStrategy voir [Réplication des données](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) dans la documentation Cassandra pour plus d’informations | Prend en charge de la topologie de déploiement et le place réplicas sur des nœuds de sorte que tous les réplicas ne finissent dans le même rack |
| Snitch | GossipingPropertyFileSnitch voir [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) dans la documentation Cassandra pour plus d’informations | NetworkTopologyStrategy utilise un concept de snitch pour déterminer la topologie. GossipingPropertyFileSnitch vous offre une meilleure contrôle lors du mappage de chaque nœud au rack et centre de données. Le cluster utilise puis discussions de se propager ces informations. Il s’agit qui simplifie considérablement dynamique paramètre IP par rapport à PropertyFileSnitch |


##<a name="the-software-configuration"></a>LA CONFIGURATION LOGICIELLE
Les versions logicielles suivantes sont utilisées pendant le déploiement :

<table>
<tr><th>Logiciel</th><th>Source</th><th>Version</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Étant donné que le téléchargement de JRE requiert acceptation manuelle de licence Oracle, pour simplifier le déploiement, télécharger tous les logiciels requis pour le bureau pour le téléchargement ultérieurement dans l’image du modèle Ubuntu que nous allons créer au préalable le déploiement cluster.

Téléchargez les logiciels ci-dessus dans un dossier de téléchargement connu (par exemple, %TEMP%/downloads sur Windows ou ~/Downloads sur la plupart des versions de Linux ou Mac) sur l’ordinateur local.

### <a name="create-ubuntu-vm"></a>CRÉER UBUNTU MACHINE VIRTUELLE
Dans cette étape du processus, nous allons créer Ubuntu image avec les logiciels requis pour que l’image peut être réutilisée pour plusieurs nœuds Cassandra de mise en service.  
####<a name="step-1-generate-ssh-key-pair"></a>ÉTAPE 1 : Générer paire de clés SSH
Azure doit un X509 clé publique PEM ou DER codé au moment de la mise en service. Générer une paire de clés publique/privée en suivant les instructions situées à comment utiliser SSH avec Linux sur Azure. Si vous prévoyez d’utiliser putty.exe comme un clientSSH sous Windows ou Linux, vous devez convertir le PEM codé clé privée RSA au format PPK à l’aide de puttygen.exe ; Vous trouverez les instructions pour cela dans la page web ci-dessus.

####<a name="step-2-create-ubuntu-template-vm"></a>ÉTAPE 2 : Créer le modèle Ubuntu machine virtuelle
Pour créer le modèle machine virtuelle, connectez-vous au portail classique Azure et suivez les étapes suivantes : cliquez sur Nouveau, cluster, MACHINE virtuelle, galerie FROM, UBUNTU, Ubuntu Server 14.04 LTS, puis cliquez sur la flèche droite. Pour un didacticiel qui explique comment créer un Linux VM, voir créer un Linux en cours d’exécution Machine virtuelle.

Entrez les informations suivantes sur l’écran « configuration de la machine virtuelle » #1 :

<table>
<tr><th>NOM DE CHAMP              </td><td>       VALEUR DE CHAMP               </td><td>         REMARQUES                </td><tr>
<tr><td>DATE DE PUBLICATION DE VERSION    </td><td> Sélectionner une date à partir de la drow vers le bas</td><td></td><tr>
<tr><td>NOM DE LA MACHINE VIRTUELLE    </td><td> modèle de cas                </td><td> Il s’agit du nom d’hôte de la machine virtuelle </td><tr>
<tr><td>NIVEAU                     </td><td> STANDARD                        </td><td> Conservez la valeur par défaut              </td><tr>
<tr><td>TAILLE                     </td><td> A1                              </td><td>Sélectionnez la machine virtuelle en fonction des besoins IO ; à cet effet, conservez la valeur par défaut </td><tr>
<tr><td> NOUVEAU NOM D’UTILISATEUR           </td><td> LocalAdmin                      </td><td> « administrateur » est un nom d’utilisateur réservé Ubuntu 12.offres xx et après</td><tr>
<tr><td> AUTHENTIFICATION      </td><td> Cliquez sur case à cocher                 </td><td>Si vous voulez voir apparaître sécuriser avec un code SSH </td><tr>
<tr><td> CERTIFICAT             </td><td> nom de fichier de certificat de clé publique </td><td> Utiliser la clé publique générée précédemment</td><tr>
<tr><td> Nouveau mot de passe   </td><td> mot de passe fort </td><td> </td><tr>
<tr><td> Confirmez le mot de passe   </td><td> mot de passe fort </td><td></td><tr>
</table>

Entrez les informations suivantes sur l’écran « configuration de la machine virtuelle » #2 :

<table>
<tr><th>NOM DE CHAMP             </th><th> VALEUR DE CHAMP                       </th><th> REMARQUES                                 </th></tr>
<tr><td> SERVICE DE NUAGE  </td><td> Créer un nouveau service cloud    </td><td>Service de nuage est une ressource de cluster conteneur comme machines virtuelles</td></tr>
<tr><td> NOM DNS DU SERVICE CLOUD </td><td>Ubuntu template.cloudapp.net   </td><td>Donnez un nom d’équilibrage de charge indépendante machine</td></tr>
<tr><td> RÉGION/GROUPE AFFINITÉ/RÉSEAU VIRTUEL </td><td>    États-Unis Ouest </td><td> Sélectionnez une région à partir de laquelle vos applications web accèdent au cluster Cassandra</td></tr>
<tr><td>COMPTE DE STOCKAGE </td><td>   Utiliser par défaut </td><td>Utiliser le compte de stockage par défaut ou un compte de stockage prédéfinis dans une région particulière</td></tr>
<tr><td>JEU DE DISPONIBILITÉ </td><td>  Aucun </td><td>  Laissez ce champ vide</td></tr>
<tr><td>POINTS DE TERMINAISON   </td><td>Utiliser par défaut </td><td>  Utiliser la configuration SSH par défaut </td></tr>
</table>

Cliquez sur la flèche vers la droite, laissez les valeurs par défaut dans l’écran #3 et cliquez sur le bouton « Vérifier » pour terminer la machine virtuelle mise en service des processus. Après quelques minutes, la machine virtuelle avec le nom « ubuntu-modèle » doit être dans un état « en cours d’exécution ».

###<a name="install-the-necessary-software"></a>INSTALLER LES LOGICIELS NÉCESSAIRES
####<a name="step-1-upload-tarballs"></a>ÉTAPE 1 : Télécharger tarballs
À l’aide de scp ou pscp, copiez le logiciel précédemment téléchargé au répertoire ~/downloads en utilisant le format de commande suivantes :

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Répétez la commande ci-dessus pour JRE ainsi que pour les bits Cassandra.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>ÉTAPE 2 : Préparer la structure du répertoire et extraire les archives
Connectez-vous à la machine virtuelle et créer la structure du répertoire et extraire logiciel tant qu’utilisateur super en utilisant le script bash ci-dessous :

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Si vous collez ce script dans la fenêtre vim, veillez à supprimer le retour chariot ('\r ») à l’aide de la commande suivante :

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Étape 3 : Modifier etc./profil
Ajoutez le code suivant à la fin :

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Étape 4 : Installer JNA pour les systèmes de production
Utilisez la séquence de commande suivante : la commande suivante s’installer jna 3.2.7.jar et jna-plateforme-3.2.7.jar au répertoire /usr/share.java susceptibles sudo get-installer libjna java

Créer des liens symboliques dans le répertoire $CASS_HOME/bibliothèque afin que le script de démarrage Cassandra peut trouver ces bocaux :

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Étape 5 : Configurer cassandra.yaml
Modifier cassandra.yaml sur chaque ordinateur virtuel pour refléter la configuration requise par tous les ordinateurs virtuels [nous sera le modifier pendant la mise en service réel] :

<table>
<tr><th>Nom de champ   </th><th> Valeur  </th><th> Remarques </th></tr>
<tr><td>que le cluster </td><td>  « CustomerService »   </td><td> Utilisez le nom qui reflète votre déploiement</td></tr>
<tr><td>listen_address  </td><td>[laissez ce champ vide]   </td><td> Supprimer « hôte local » </td></tr>
<tr><td>rpc_addres   </td><td>[laissez ce champ vide]  </td><td> Supprimer « hôte local » </td></tr>
<tr><td>graines   </td><td>« 10.1.2.4, 10.1.2.6, 10.1.2.8 » </td><td>Liste de toutes les adresses IP qui sont désignés comme éléments fondamentaux.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Ceci est utilisé par le NetworkTopologyStrateg qui permettent de déduire le centre de données et le rack de la machine virtuelle</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Étape 6 : Effectuer la machine virtuelle
Connectez-vous à la machine virtuelle en utilisant le nom d’hôte (hk-autorités de certification-template.cloudapp.net) et la clé privée SSH créé précédemment. Voir comment utiliser SSH avec Linux sur Azure pour plus d’informations sur la façon de se connecter à l’aide de la commande ssh ou putty.exe.

Exécuter la séquence d’actions pour capturer l’image suivante :
#####<a name="1-deprovision"></a>1. annulation
Utilisez la commande « sudo waagent – annulation + utilisateur » pour supprimer des informations spécifiques d’instance Machine virtuelle. Voir [Comment faire pour capturer une Machine virtuelle Linux](virtual-machines-linux-classic-capture-image.md) à utiliser en tant que modèle obtenir plus d’informations sur le processus de capture.

#####<a name="2-shutdown-the-vm"></a>2 : arrêter la machine virtuelle
Vérifiez que la machine virtuelle est mis en surbrillance et cliquez sur le lien de fermeture de la barre de commandes en bas.

#####<a name="3-capture-the-image"></a>3 : capturer l’image
Vérifiez que la machine virtuelle est mis en surbrillance et cliquez sur le lien de la CAPTURE à partir de la barre de commandes en bas. Dans l’écran suivant, donnez un nom d’IMAGE (par exemple, hk-cas-2-08-ub-14-04-2014071), appropriés DESCRIPTION de l’IMAGE, puis cliquez sur « coche » pour terminer le processus de CAPTURE.

Cela prendra quelques secondes et l’image doit être disponible dans la section Mes IMAGES de la galerie d’image. La source machine virtuelle sera automatiquement delated une fois que l’image est capturé correctement.

##<a name="single-region-deployment-process"></a>Processus de déploiement de région unique
**Étape 1 : créer le réseau virtuel** Se connecter au portail classique Azure et créez un réseau virtuel avec le diaporama attributs dans le tableau. Pour plus d’informations du processus, consultez [configurer un réseau virtuel Cloud-Only dans le portail classique Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) .      

<table>
<tr><th>Nom de l’attribut machine virtuelle</th><th>Valeur</th><th>Remarques</th></tr>
<tr><td>Nom</td><td>vnet-cas-ouest-nous</td><td></td></tr>
<tr><td>Région</td><td>États-Unis Ouest</td><td></td></tr>
<tr><td>Serveurs DNS </td><td>Aucun</td><td>Ignorer ce que nous n’utilisons pas un serveur DNS</td></tr>
<tr><td>Configurer un réseau privé virtuel point-à-site</td><td>Aucun</td><td> Ignorer ce</td></tr>
<tr><td>Configurer un réseau VPN site à</td><td>Nnone</td><td> Ignorer ce</td></tr>
<tr><td>Espace d’adressage</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>Adresse IP de début</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Ajoutez les sous-réseaux suivants :

<table>
<tr><th>Nom</th><th>Adresse IP de début</th><th>CIDR</th><th>Remarques</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Sous-réseau pour la batterie de serveurs web</td></tr>
<tr><td>données</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Sous-réseau pour les nœuds de base de données</td></tr>
</table>

Données et sous-réseaux Web peuvent être protégés par le biais des groupes de sécurité réseau n’appartient pas la couverture dont l’étendue de cet article.  

**Étape 2 : configurer des ordinateurs virtuels** À l’aide de l’image créée précédemment, nous créer les ordinateurs virtuels suivants dans le serveur en nuage « hk-c-svc-ouest » et les lier aux sous-réseaux comme indiqué ci-dessous :

<table>
<tr><th>Nom de l’ordinateur    </th><th>Sous-réseau </th><th>Adresse IP </th><th>Jeu de disponibilité</th><th>Contrôleur de domaine/Rack</th><th>Valeur de départ ?</th></tr>
<tr><td>HK-c1-ouest-nous   </td><td>données   </td><td>10.1.2.4   </td><td>HK-c-une série-1    </td><td>DC = en rack WESTUS = racks 1 </td><td>Oui</td></tr>
<tr><td>HK-c2-ouest-nous   </td><td>données   </td><td>10.1.2.5   </td><td>HK-c-une série-1    </td><td>DC = en rack WESTUS = racks 1 </td><td>N° </td></tr>
<tr><td>HK-c3-ouest-nous   </td><td>données   </td><td>10.1.2.6   </td><td>HK-c-une série-1    </td><td>DC = en rack WESTUS = rack2 </td><td>Oui</td></tr>
<tr><td>HK-c4-ouest-nous   </td><td>données   </td><td>10.1.2.7   </td><td>HK-c-une série-1    </td><td>DC = en rack WESTUS = rack2 </td><td>N° </td></tr>
<tr><td>HK-c5-ouest-nous   </td><td>données   </td><td>10.1.2.8   </td><td>HK-c-une série-2    </td><td>DC = en rack WESTUS = rack3 </td><td>Oui</td></tr>
<tr><td>HK-c6-ouest-nous   </td><td>données   </td><td>10.1.2.9   </td><td>HK-c-une série-2    </td><td>DC = en rack WESTUS = rack3 </td><td>N° </td></tr>
<tr><td>HK-c7-ouest-nous   </td><td>données   </td><td>10.1.2.10  </td><td>HK-c-une série-2    </td><td>DC = en rack WESTUS = rack4 </td><td>Oui</td></tr>
<tr><td>HK-c8-ouest-nous   </td><td>données   </td><td>10.1.2.11  </td><td>HK-c-une série-2    </td><td>DC = en rack WESTUS = rack4 </td><td>N° </td></tr>
<tr><td>HK-w1-ouest-nous   </td><td>Web    </td><td>10.1.1.4   </td><td>HK-w-une série-1    </td><td>                       </td><td>N/A</td></tr>
<tr><td>HK-w2-ouest-nous   </td><td>Web    </td><td>10.1.1.5   </td><td>HK-w-une série-1    </td><td>                       </td><td>N/A</td></tr>
</table>

La création de la liste ci-dessus des machines virtuelles requiert le processus suivant :

1.  Créer un service cloud vide dans une région particulière
2.  Créer une machine virtuelle à partir de l’image capturée précédemment et joignez-le au réseau virtuel créé précédemment. Répétez cette étape pour tous les ordinateurs virtuels
3.  Ajouter un programme d’équilibrage de charge interne au service cloud et joignez-le au sous-réseau « données »
4.  Pour chaque machine virtuelle créé précédemment, ajoutez un point de terminaison équilibrée de charge pour le trafic thrift via un ensemble d’équilibrage de charge connecté à l’équilibrage de charge interne précédemment créée

La procédure ci-dessus peut être exécutée à l’aide du portail classique Azure ; Utilisez un ordinateur Windows (utilisez une machine virtuelle sur Azure si vous n’avez pas accès à un ordinateur Windows), utilisez le script PowerShell suivant pour configurer automatiquement tous les ordinateurs 8 virtuels.

**Liste 1 : les options Script PowerShell de mise en service des machines virtuelles**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Étape 3 : Configurer Cassandra sur chaque machine virtuelle**

Connectez-vous à la machine virtuelle et effectuez les opérations suivantes :

* Modifier $CASS_HOME/conf/cassandra-rackdc.properties pour spécifier les propriétés de centre et rack de données :

       dc =EASTUS, rack =rack1

* Modifier cassandra.yaml pour configurer des nœuds de valeur de départ comme indiqué ci-dessous :

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Étape 4 : Démarrer les ordinateurs virtuels et tester le cluster**

Connectez-vous à un des nœuds (par exemple, hk-c1-ouest-us) et exécutez la commande suivante pour afficher l’état du cluster :

       nodetool –h 10.1.2.4 –p 7199 status

Vous devriez voir l’affichage similaire à celui ci-dessous pour un cluster 8 nœuds :

<table>
<tr><th>État</th><th>Adresse  </th><th>Chargement   </th><th>Jetons </th><th>Propriétaire </th><th>ID de l’hôte  </th><th>Montage en rack</th></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.4   </td><td>87.81 BASE DE CONNAISSANCES   </td><td>256    </td><td>38.0 %  </td><td>GUID (supprimé)</td><td>racks 1</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.5   </td><td>41.08 KO   </td><td>256    </td><td>68.9 %  </td><td>GUID (supprimé)</td><td>racks 1</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.6   </td><td>55.29 BASE DE CONNAISSANCES   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack2</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.7   </td><td>55.29 BASE DE CONNAISSANCES   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack2</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.8   </td><td>55.29 BASE DE CONNAISSANCES   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack3</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.9   </td><td>55.29 BASE DE CONNAISSANCES   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack3</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.10  </td><td>55.29 BASE DE CONNAISSANCES   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack4</td></tr>
<tr><th>DÉSACTIVER  </td><td>10.1.2.11  </td><td>55.29 BASE DE CONNAISSANCES   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testez le Cluster région unique
Utilisez les étapes suivantes permettent de tester le cluster :

1.    En utilisant l’applet de commande Get-AzureInternalLoadbalancer de commande Powershell, obtenir l’adresse IP de l’équilibrage de charge interne (par exemple  10.1.2.101). La syntaxe de la commande est indiquée ci-dessous : Get-AzureLoadbalancer – ServiceName% « hk-c-svc-ouest-nous » [affiche les détails de l’équilibrage de charge interne ainsi que son adresse IP]
2.  Connectez-vous à la batterie de serveurs web machine virtuelle (par exemple, hk-w1-ouest-us) à l’aide de Putty ou ssh
3.  Exécuter $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4.  Utilisez les commandes CQL suivantes pour vérifier si le cluster fonctionne :

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Vous devriez voir un affichage comme celui ci-dessous :

<table>
  <tr><th> customer_id </th><th> Prénom </th><th> nom </th></tr>
  <tr><td> 1 </td><td> John </td><td> Dupont </td></tr>
  <tr><td> 2 </td><td> Jeanne </td><td> Dupont </td></tr>
</table>

Veuillez noter que la keyspace créé à l’étape 4 utilise SimpleStrategy avec un replication_factor de 3. SimpleStrategy est recommandée pour données unique centre déploiements alors que les déploiements de centres de NetworkTopologyStrategy pour les données à plusieurs. Un replication_factor de 3 donne tolérance pour les échecs de nœuds.

##<a id="tworegion"> </a>Processus de déploiement de plusieurs régions
Sera tirer parti du déploiement seule région terminé et répétez la même procédure pour l’installation de la deuxième zone. La différence entre le déploiement d’une ou plusieurs région clée est la configuration de tunnel VPN pour les communications entre région ; Nous démarre avec l’installation réseau, mise en service des ordinateurs virtuels et configurer Cassandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Étape 1 : Créer le réseau virtuel à la zone 2ème
Se connecter au portail classique Azure et créez un réseau virtuel avec le diaporama attributs dans le tableau. Pour plus d’informations du processus, consultez [configurer un réseau virtuel Cloud-Only dans le portail classique Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) .      

<table>
<tr><th>Nom de l’attribut    </th><th>Valeur    </th><th>Remarques</th></tr>
<tr><td>Nom    </td><td>vnet-cas-Moyen-Orient-nous</td><td></td></tr>
<tr><td>Région  </td><td>États-Unis Extrême-Orient</td><td></td></tr>
<tr><td>Serveurs DNS     </td><td></td><td>Ignorer ce que nous n’utilisons pas un serveur DNS</td></tr>
<tr><td>Configurer un réseau privé virtuel point-à-site</td><td></td><td>     Ignorer ce</td></tr>
<tr><td>Configurer un réseau VPN site à</td><td></td><td>      Ignorer ce</td></tr>
<tr><td>Espace d’adressage   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Adresse IP de début </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Ajoutez les sous-réseaux suivants :
<table>
<tr><th>Nom    </th><th>Adresse IP de début    </th><th>CIDR   </th><th>Remarques</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Sous-réseau pour la batterie de serveurs web</td></tr>
<tr><td>données    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Sous-réseau pour les nœuds de base de données</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Étape 2 : Créer des réseaux locaux
Un réseau Local dans un réseau virtuel Azure est un espace d’adressage proxy qui correspond à un site distant, y compris un cloud privé ou une autre région Azure. Cet espace d’adressage proxy est lié à une passerelle à distance pour le routage réseau vers les droite destinations réseau. Consultez [configurer un VNet vers VNet connexion](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) pour obtenir des instructions sur l’établissement de connexion VNET-VNET.

Créez deux réseaux locaux par les détails suivants :

| Nom du réseau | Adresse de la passerelle VPN | Espace d’adressage | Remarques |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-US | 23.1.1.1  | 10.2.0.0/16   | Lors de la création du réseau Local donner un espace réservé adresse de la passerelle. Adresse de la passerelle réel est rempli une fois que la passerelle est créée. Vérifiez que l’espace d’adressage correspond exactement à la VNET à distance respectif ; Dans ce cas le VNET créé dans la région des États-Unis Extrême-Orient. |
| HK-lnet-Map-to-West-US | 23.2.2.2  | 10.1.0.0/16   | Lors de la création du réseau Local donner un espace réservé adresse de la passerelle. Adresse de la passerelle réel est rempli une fois que la passerelle est créée. Vérifiez que l’espace d’adressage correspond exactement à la VNET à distance respectif ; Dans ce cas le VNET créé dans la région États-Unis Ouest. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Étape 3 : Carte réseau Local » » aux VNETs respectives
À partir du portail classique Azure, sélectionnez chaque vnet, cliquez sur « Configurer », cochez « Se connecter au réseau local » et sélectionnez les réseaux Local par les informations suivantes :


| Réseau virtuel | Réseau local |
| --------------- | ------------- |
| HK-vnet-ouest-nous | HK-lnet-Map-to-East-US |
| HK-vnet-Moyen-Orient-nous | HK-lnet-Map-to-West-US |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Étape 4 : Créer des passerelles sur VNET1 et VNET2
Dans le tableau de bord des deux réseaux virtuels, cliquez sur passerelle créer qui doit déclencher la passerelle VPN processus de mise en service. Après quelques minutes, le tableau de bord de chaque réseau virtuel doit afficher l’adresse de passerelle réel.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Étape 5 : Mise à jour « Local » les réseaux et les adresses correspondantes « passerelle »###
Modifier les réseaux locales pour remplacer l’adresse IP de passerelle espace réservé avec l’adresse IP réelle des passerelles simplement mis en service. Utiliser le mappage suivant :

<table>
<tr><th>Réseau local    </th><th>Passerelle réseau virtuel</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Passerelle hk-vnet-ouest-introduction</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Passerelle hk-vnet-Moyen-Orient-introduction</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Étape 6 : Mettre à jour la clé partagée
Utilisez le script Powershell suivant pour mettre à jour la clé IPSec de chaque passerelle VPN [utiliser la clé souci pour les deux passerelles] : Set-AzureVNetGatewayKey - VNetName hk-vnet-Moyen-Orient-nous - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-ouest-nous - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Étape 7 : Établir la connexion VNET-VNET
À partir du portail classique Azure, utilisez le menu « Tableau de bord » des réseaux virtuels pour établir la connexion passerelle-passerelle. Utiliser les éléments de menu « CONNECT » dans la barre d’outils en bas. Après quelques minutes le tableau de bord doit afficher les détails de connexion sous forme graphique.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Étape 8 : Créer les machines virtuelles dans la zone #2
Créez l’image Ubuntu comme décrit dans le déploiement de la zone #1 en suivant la même procédure ou copier le fichier de disque dur virtuel image sur le compte de stockage Azure situé dans la zone #2 et l’image. Utiliser cette image et créer la liste suivante des machines virtuelles dans un service en nuage hk-c-svc-Moyen-Orient-nous :


| Nom de l’ordinateur | Sous-réseau | Adresse IP | Jeu de disponibilité | Contrôleur de domaine/Rack | Valeur de départ ? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| HK-c1-Moyen-Orient-nous | données  | 10.2.2.4   | HK-c-une série-1      | DC = en rack EASTUS = racks 1 | Oui |
| HK-c2-Moyen-Orient-nous | données  | 10.2.2.5   | HK-c-une série-1      | DC = en rack EASTUS = racks 1 | N°  |
| HK-c3-Moyen-Orient-nous | données  | 10.2.2.6   | HK-c-une série-1      | DC = en rack EASTUS = rack2 | Oui |
| HK-c5-Moyen-Orient-nous | données  | 10.2.2.8   | HK-c-une série-2      | DC = en rack EASTUS = rack3 | Oui |
| HK-c6-Moyen-Orient-nous | données  | 10.2.2.9   | HK-c-une série-2      | DC = en rack EASTUS = rack3 | N°  |
| HK-c7-Moyen-Orient-nous | données  | 10.2.2.10  | HK-c-une série-2      | DC = en rack EASTUS = rack4 | Oui |
| HK-c8-Moyen-Orient-nous | données  | 10.2.2.11  | HK-c-une série-2      | DC = en rack EASTUS = rack4 | N°  |
| HK-w1-Moyen-Orient-nous | Web   | 10.2.1.4   | HK-w-une série-1      | N/A                    | N/A |
| HK-w2-Moyen-Orient-nous | Web   | 10.2.1.5   | HK-w-une série-1      | N/A                    | N/A |


Suivez les instructions mêmes en tant que zone #1, mais utilisez espace d’adressage 10.2.xxx.xxx.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Étape 9 : Configurer Cassandra sur chaque machine virtuelle
Connectez-vous à la machine virtuelle et effectuez les opérations suivantes :

1. Modifier $CASS_HOME/conf/cassandra-rackdc.properties pour spécifier les propriétés de centre et rack des données dans le format : dc = en rack EASTUS = racks 1
2. Modifier cassandra.yaml pour configurer des nœuds de valeur de départ : semences : « 10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10 »

###<a name="step-10-start-cassandra"></a>Étape 10 : Démarrer Cassandra
Connectez-vous à chaque machine virtuelle et démarrez Cassandra en arrière-plan en exécutant la commande suivante : $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testez le Cluster plusieurs région
À présent Cassandra a été déployé sur 16 nœuds avec 8 nœuds dans chaque région Azure. Ces nœuds sont dans le même cluster après le nom du cluster courantes et la configuration de nœuds de valeur de départ. La procédure suivante permet de tester le cluster :

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Étape 1 : Obtenir l’adresse IP équilibrage de charge interne pour les deux régions à l’aide de PowerShell
- Get-AzureInternalLoadbalancer - ServiceName « hk-c-svc-ouest-nous »
- Get-AzureInternalLoadbalancer - ServiceName « hk-c-svc-Moyen-Orient-nous »  

    Notez les adresses IP (par exemple, ouest - 10.1.2.101, orientale - 10.2.2.101) affiché.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Étape 2 : Exécutez la commande suivante dans la région Ouest après la connexion à hk-w1-ouest-nous
1.    Exécuter $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2.  Exécutez les commandes CQL suivantes :

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Vous devriez voir un affichage comme celui ci-dessous :

| customer_id | Prénom | Nom |
| ----------- | --------- | -------- |
| 1           | John      | Dupont      |
| 2           | Jeanne      | Dupont      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Étape 3 : Exécutez la commande suivante dans la région est après la connexion à hk-w1-Moyen-Orient-nous :
1.    Exécuter $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2.  Exécutez les commandes CQL suivantes :

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Vous devriez voir le même affichage comme suit pour la région Ouest :


| customer_id | Prénom | Nom   |
|------------ | --------- | ---------- |
| 1           | John      | Dupont        |
| 2           | Jeanne      | Dupont        |


Effectuer quelques insertions plus et voir qu’elles sont répliquées sur ouest-nous fait partie du cluster.

## <a name="test-cassandra-cluster-from-nodejs"></a>Test Cassandra Cluster de Node.js
En utilisant l’un des ordinateurs virtuels Linux créé dans le « web » hiérarchiser précédemment, nous allez exécuter un script Node.js simple pour lire les données précédemment insérées

**Étape 1 : Installer Node.js et Cassandra Client**

1. Installer Node.js et npm
2. Installer le nœud package « cassandra-client » à l’aide de npm
3. Exécutez le script suivant à l’invite shell qui affiche la chaîne json des données récupérées :

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Conclusion
Microsoft Azure est une plateforme flexible qui autorise l’exécution de Microsoft ainsi que des logiciels open source comme le montre cet exercice. Disponibilité clusters Cassandra peuvent être déployés sur un centre de données unique via la répartition des nœuds de cluster sur plusieurs domaines d’erreur. Clusters Cassandra peuvent également être déployées dans plusieurs régions Azure géographiquement distantes pour les systèmes de preuve d’urgence. Azure et Cassandra ensemble permet de créer des hautement scalable, hautement disponible et les services en nuage récupérables urgence nécessaire par internet d’aujourd'hui à l’échelle services.  

##<a name="references"></a>Références##
- [http://Cassandra.Apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
