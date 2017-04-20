<properties 
   pageTitle="Échec des machines virtuelles VMware précédent et des serveurs physiques au site local | Microsoft Azure"
   description="Découvrez comment basculer vers le site local après le basculement d’ordinateurs virtuels VMware et des serveurs physiques vers Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Machines virtuelles VMware précédent de Fail et des serveurs physiques vers le site en local

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-failback-azure-to-vmware.md)
- [Portail classique Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portail classique Azure (hérité)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



Cet article explique comment échouer revenir Azure machines virtuelles à partir d’Azure au site local. Suivez les instructions de cet article lorsque vous êtes prêt à être restauré vos machines virtuelles VMware ou serveurs physiques Windows/Linux après avez basculé le locales du site vers Azure à l’aide de ce [didacticiel](site-recovery-vmware-to-azure-classic.md).



## <a name="overview"></a>Vue d’ensemble

Ce diagramme illustre l’architecture de retour arrière pour ce scénario.

Utilisez cette architecture lorsque le serveur de processus est en local et que vous utilisez un ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Utilisez cette architecture lorsque le serveur de traitement se trouve sur Azure et que vous avez un réseau privé virtuel ou une connexion ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Pour afficher la liste complète de ports et le retour arrière Architect diagramme font référence à l’image ci-dessous

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Voici comment fonctionne la restauration automatique :

- Une fois que vous avez basculé vers Azure, vous restauré vers votre site local en quelques étapes :
    - **Étape 1**: vous restaurez protection les ordinateurs virtuels Azure afin qu’ils commencer répliquées de nouveau machines virtuelles VMware en cours d’exécution dans votre site local. L’activation reprotection déplace la machine virtuelle dans un groupe de protection retour arrière qui a été créé automatiquement lorsque vous avez créé à l’origine du groupe de protection basculement. Si vous avez ajouté à que votre groupe de protection basculement à un plan de récupération puis le groupe de protection retour arrière a été ajouté automatiquement à l’offre.  Au cours de restaurez protection que vous spécifiez comment planifier soit restauré.
    - **Étape 2**: une fois que vos ordinateurs virtuels Azure sont réplication vers votre site local, vous exécutez un fail échec revenir à partir d’Azure.
    - **Étape 3**: une fois que vos données a échoué précédent, vous restaurez protection ordinateurs locaux virtuels que vous n’avez pas à, afin qu’ils commencer réplication vers Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Retour arrière vers l’emplacement d’origine ou l’autre

Si vous n’avez pas sur une VM VMware pouvez vous ne parvenez pas à la même source machine virtuelle s’il existe toujours en local. Dans ce scénario, seules les modifications delta va échouer précédent. Notez que :

- Si vous n’avez pas sur des serveurs physiques retour arrière est toujours à une nouvelle VM VMware.
    - Avant l’échec d’une machine physique revenir Notez que
    - Machine physique protégé est transférés comme une machine virtuelle lorsque basculé revenir à partir d’Azure vers VMware
    - Vérifiez que vous découvrez au moins une cible masque Server ainsi que les hôtes ESX/ESXi nécessaires à laquelle vous devez retour arrière.
- Si vous ne parvenez pas à la machine virtuelle d’origine la suivante est nécessaire :
    - Si la machine virtuelle est gérée par un serveur vCenter hôte ESX de la cible du masque doit y accéder au magasin de données machines virtuelles.
    - Si la machine virtuelle se trouve sur un hôte ESX mais n’est pas gérée par vCenter le disque dur de la machine virtuelle doit être dans un magasin de données accessible par l’hôte de MT.
    - Si votre ordinateur virtuel se trouve sur un hôte ESX et n’utilise pas vCenter vous devez effectuer la découverte de l’hôte ESX de la MT avant vous restaurez protection. Cela s’applique si vous êtes trop ignore serveurs physiques précédent.
    - Une autre option (si la machine virtuelle locale existe) consiste à supprimer avant d’effectuer un retour arrière. Retour arrière puis créerez ensuite un nouvel ordinateur virtuel sur le même hôte que l’hôte ESX cible maître.
    
- Lorsque vous retour arrière vers un autre emplacement les données sera restaurée dans le même magasin de données et l’hôte ESX même que celui utilisé par le serveur de base cible local. 


## <a name="prerequisites"></a>Conditions préalables

- Vous devez un environnementVMWare afin d’échouer sauvegarder VMware machines virtuelles et des serveurs physiques. Basculer sur un serveur physique n’est pas pris en charge.
- Afin d’être restauré doit avoir créé un réseau Azure lors de la configuration initiale protection. Retour arrière nécessite une connexion de réseau privé virtuel ou ExpressRoute à partir du réseau Azure dans lequel les ordinateurs virtuels Azure se trouvent sur le site local.
- Si les ordinateurs virtuels que vous souhaitez basculer sur sont gérés par un serveur vCenter vous devez pour vous assurer que vous disposez des autorisations nécessaires pour la découverte des machines virtuelles sur des serveurs vCenter. [Pour en savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Si des instantanés sont présents sur un ordinateur virtuel reprotection échouera. Vous pouvez supprimer les instantanés ou les disques. 
- Avant de vous restaurez, vous devez créer un certain nombre de composants :
    - **Créer un serveur de traitement dans Azure**. Il s’agit d’une machine virtuelle Azure dont vous aurez besoin pour créer et maintenir en cours d’exécution lors du retour arrière. Vous pouvez supprimer l’ordinateur une fois la restauration terminée.
    - **Créer un serveur principal cible**: le serveur cible maître envoie et reçoit des données de retour arrière. Le serveur d’administration que vous avez créé en local dispose d’un serveur cible maître installé par défaut. Toutefois, selon le volume de trafic précédent échec vous devrez peut-être créer un serveur cible maître distincte pour le retour arrière.
    - Si vous souhaitez créer un serveur cible maître supplémentaire exécuté sous Linux, vous devez configurer la Linux VM avant d’installer le serveur maître cible, comme décrit ci-dessous.
- Configuration serveur est requis en local lorsque vous effectuez un retour arrière. Lors du retour arrière, la machine virtuelle doivent exister dans la base de données serveur Configuration, ignore lesquelles ne la restauration aboutisse. Par conséquent, garantir que vous prenez sauvegarde planifiée régulière de votre serveur. En cas de sinistre, vous devrez restaurer avec la même adresse IP afin que retour arrière est prises en charge.

## <a name="set-up-the-process-server-in-azure"></a>Configurer le serveur de traitement dans Azure

Vous devez installer un serveur de traitement dans Azure afin que les ordinateurs virtuels Azure puissent envoyer les données au serveur cible maître local. 

1.  Dans le portail de récupération de Site > **Serveurs de Configuration** permet d’ajouter un nouveau serveur de processus.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Indiquez un nom de serveur de traitement et entrez un nom et un mot de passe que vous utiliserez pour vous connecter à la machine virtuelle Azure en tant qu’administrateur. Dans **Serveur de Configuration de** sélectionner le serveur de gestion locale et spécifier le réseau Azure dans lequel le serveur de traitement doit être déployé. Il s’agit du réseau dans lequel se trouvent les ordinateurs virtuels Azure. Spécifiez une adresse IP unique à partir du sous-réseau select et commencer le déploiement.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Un travail de déploiement du serveur de traitement sera déclenché

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Une fois que le serveur de processus est déployé dans Azure, vous pouvez vous connecter à en utilisant les informations d’identification que vous avez spécifié. La première fois que vous ouvrez une session dans la boîte de dialogue processus serveur s’exécutera. Entrez l’adresse IP du serveur de gestion locale et son mot de passe. Laissez le paramètre de port 443 par défaut. Vous pouvez également laisser de la valeur par défaut port 9443 pour la réplication de données, à moins que vous spécifiquement modifié ce paramètre lorsque vous configurez le serveur de gestion locale. 

    >[AZURE.NOTE] Le serveur n’est pas visible, sous **Propriétés de la machine virtuelle**. Elle n’est visible sous l’onglet **serveurs** dans le serveur d’administration dans lequel elle est inscrite. Vous pouvez patienter environ 10 à 15 minutes pour le serveur de traitement doit apparaître.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurer la cible maître server en local

Le serveur cible maître reçoit les données de retour arrière. Un serveur principal cible est automatiquement installé sur le serveur d’administration en local, mais si vous êtes ignore un grand nombre de données vous devrez peut-être configurer un serveur cible maître supplémentaire. Procédez comme suit :

>[AZURE.NOTE] Si vous voulez installer un serveur cible maître sur Linux, suivez les instructions dans la procédure suivante.

1. Si vous installez le serveur cible maître sous Windows, ouvrez la page de démarrage rapide à partir de la machine virtuelle sur lequel vous installez le serveur cible maître et téléchargez le fichier d’installation pour l’Assistant Configuration d’unifiée Azure Site récupération.
2. Exécuter le programme d’installation, puis sélectionnez **Ajouter des serveurs supplémentaires processus à l’échelle déploiement**dans **avant de commencer** .
3. Exécuter l’Assistant de la même façon que vous l’avez fait lorsque vous [Configurez le serveur d’administration](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Dans la page **Détails de la Configuration serveur** , spécifiez l’adresse IP de ce serveur cible principal et un mot de passe pour accéder à la machine virtuelle.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurer un Linux VM en tant que le serveur maître cible
Pour configurer le serveur de gestion exécutant le serveur maître cible comme un VM Linux vous devez installer le Cent) S 6.6 minimales système d’exploitation, récupérer les ID SCSI pour chaque disque dur SCSI, installer certains packages supplémentaires et appliquer des modifications personnalisées.

#### <a name="install-centos-66"></a>Installer CentOS 6.6

1.  Installer le système d’exploitation minimal CentOS 6.6 sur le serveur de gestion des machines virtuelles. Conserver l’ISO dans un lecteur de DVD et démarrer le système. Ignorer les tests de support, sélectionnez anglais (États-Unis) à la langue, sélectionnez **Périphériques de stockage de base**, vérifier que le disque dur ne contient aucune donnée importante et cliquez sur **Oui**, ignorer toutes les données. Entrez le nom d’hôte du serveur d’administration et sélectionnez la carte réseau server.  Dans la boîte de dialogue **Modifier le système** sélectionnez** se connecter automatiquement** et ajoutez une adresse IP statique, réseau et les paramètres DNS. Spécifier un fuseau horaire et un mot de passe racine pour accéder au serveur de gestion. 
2.  Lorsque le système vous demande le type d’installation vous aimeriez sélectionnez **Créer la mise en page personnalisée** en tant que la partition. Après avoir cliqué sur **suivant** sélectionnez **libre** et cliquez sur Créer. Créer **/**, **/var/crash** et **/ partitions à domicile** avec **FS Type :** **ext4**. Créer la partition permutation comme **FS Type : permutation**.
3.  Si les périphériques préexistants se trouvent qu'un message d’avertissement s’affiche. Cliquez sur **Format** pour mettre en forme le lecteur avec les paramètres de partition. Cliquez sur **écrire des modifications sur le disque** pour appliquer les modifications de partition.
4.  Sélectionnez **chargeur de démarrage d’installation** > **suivante** pour installer le chargeur de démarrage sur la partition racine.
5.  Une fois l’installation terminée cliquez sur **redémarrer**.


#### <a name="retrieve-the-scsi-ids"></a>Récupérer les ID SCSI

1. Après l’installation récupérer les ID SCSI pour chaque disque dur SCSI de l’ordinateur virtuel. Pour effectuer cette arrêter le serveur d’administration machine virtuelle, dans les propriétés de la machine virtuelle dans VMware avec le bouton droit l’entrée machine virtuelle > **Modifier les paramètres** > **Options**.
2. Sélectionnez **Avancé** > **Général de l’article** et cliquez sur **Paramètres de Configuration**. Cette option est de-active lorsque l’ordinateur est en cours d’exécution. Pour la rendre active l’ordinateur doit être arrêté.
3. Si la ligne **disque. EnableUUID** existe Vérifiez que la valeur est définie sur **True** (respecter la casse). S’il est déjà vous pouvez annuler et tester la commande SCSI à l’intérieur d’un système d’exploitation invité une fois que ce dernier est démarré. 
4.  Si la ligne n’est pas un existant Cliquez sur **Ajouter une ligne** – et ajoutez-le avec la valeur **True** . N’utilisez des guillemets doubles.

#### <a name="install-additional-packages"></a>Installer des packages supplémentaires

Vous devez télécharger et installer des packages supplémentaires. 

1.  Vérifiez que le serveur maître cible est connecté à internet.
2.  Exécutez cette commande pour télécharger et installer 15 packages à partir du référentiel CentOS : **# yum installer – y xfsprogs perl lsscsi ont wget kexec-outils**.
3.  Si les ordinateurs source protection exécutent Linux suivi Reiser ou XFS de fichiers système pour le périphérique racine ou de démarrage, puis vous devez télécharger et installer des packages supplémentaires comme suit :

    - # <a name="cd-usrlocal"></a>CD/usr/local
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>reiserfs dans le kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm de – ivh t/mn-utilitaires-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>t/min – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Appliquer les modifications personnalisées

Procédez comme suit pour appliquer les modifications personnalisées une fois que vous avez effectuez les étapes après l’installation et installé les packages :

1.  Copier l’Agent d’unifiée RHEL 6-64 binaire de la machine virtuelle. Exécutez cette commande pour décompressez le fichier binaire : **tar – zxvf <file name> **
2.  Exécutez cette commande pour accorder des autorisations : **# chmod 755./ApplyCustomChanges.sh**
3.  Exécuter le script : **#./ApplyCustomChanges.sh**. Vous ne devez exécuter le script une seule fois. Redémarrez le serveur une fois que le script s’exécute correctement.


## <a name="run-the-failback"></a>Exécuter le retour arrière

### <a name="reprotect-the-azure-vms"></a>Restaurez protection les ordinateurs virtuels Azure

1.  Dans le portail de récupération de Site > onglet **Machines** , sélectionnez la machine virtuelle qui est basculée et cliquez sur **protéger de nouveau**.
2.  Dans **Serveur cible principal** et **Serveur Process** sélectionnez le serveur de base cible en local et le serveur de processus machine virtuelle Azure.
3.  Sélectionnez le compte que vous avez configuré pour se connecter à la machine virtuelle.
4.  Sélectionnez la version de restauration du groupe de protection. Pour exemple, si la machine virtuelle est protégée dans PG1, vous devez sélectionner PG1_Failback.
5.  Si vous souhaitez récupérer vers un autre emplacement, sélectionnez l’unité de rétention et le magasin de données configuré pour le serveur maître cible. Lorsque vous ne parvenez pas à son site local les ordinateurs virtuels VMware dans le plan de protection retour arrière utilise le même magasin de données en tant que le serveur maître cible. Si vous souhaitez récupérer le réplica Azure machine virtuelle au même local machine virtuelle, la machine virtuelle en local doit déjà être dans le même magasin de données en tant que le serveur maître cible. S’il n’existe aucune machine virtuelle locale un est créé au cours de reprotection.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Après avoir cliqué sur **OK** pour commencer le reprotection une tâche commence à répliquer la machine virtuelle d’Azure vers le site local. Vous pouvez suivre la progression de l’onglet **tâches** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Exécuter un basculement vers le site en local

Après avoir reprotection la machine virtuelle est déplacée vers la version de restauration de son groupe de protection et est automatiquement ajoutée au plan de récupération que vous avez utilisé pour le basculement vers Azure le cas échéant.

1.  Dans la page **Récupération Plans** sélectionnez le plan de récupération contenant le groupe retour arrière, puis cliquez sur **basculement** > **Basculement non planifié**.
2.  Dans le **Basculement confirmer** vérifier la direction de basculement (vers Azure), puis sélectionnez le point de récupération à utiliser pour le basculement (dernière version). Si vous avez activé **Multi-machine virtuelle** lorsque vous avez configuré les propriétés de réplication que vous pouvez les récupérer à la dernière application ou un point de récupération cohérent de blocage. Activez la case à cocher pour démarrer le basculement.
3.  Pendant le basculement récupération de Site s’arrête les ordinateurs virtuels Azure. Après avoir vérifié que la restauration terminée comme prévu que vous pouvez vous pouvez vérifier que les ordinateurs virtuels Azure ont été arrêtés comme prévu.

### <a name="reprotect-the--on-premises-site"></a>Restaurez protection du site local

Une fois terminée retour arrière vos données seront revenir sur le site local, mais ne serez pas protégées. Pour lancer la réplication vers Azure à nouveau, procédez comme suit :

1.  Dans le portail de récupération de Site > onglet **ordinateurs** , sélectionnez les ordinateurs virtuels qui n’ont pas pu précédent et cliquez sur **protéger de nouveau**. 
2.  Après avoir vérifié que la réplication vers Azure fonctionne comme prévu, dans Azure vous pouvez supprimer les ordinateurs virtuels Azure (actuellement ne pas en cours d’exécution) qui ont été rétablies.


### <a name="common-issues-in-failback"></a>Problèmes courants dans retour arrière

1. Si vous effectuez la découverte vCenter utilisateur en lecture seule et protégez les machines virtuelles, il a réussi et basculement fonctionne. Au moment de restaurez protection, il échouera dans la mesure où les magasins de données ne peut pas être découvert. Comme une manifestation vous ne verrez pas les magasins de données répertoriés tout en protégeant nouveau. Pour résoudre ce problème, vous pouvez mettre à jour les informations d’identification vCenter avec approprié compte disposant des autorisations et renouvelez l’opération. [En savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Lorsque vous retour arrière une VM Linux et exécutez-le local, vous verrez que le package du Gestionnaire de réseau est désinstallé de l’ordinateur. C’est parce que lorsque la machine virtuelle récupérée dans Azure, le package du Gestionnaire de réseau est supprimé.
3. Lorsqu’une machine virtuelle est configurée avec une adresse IP statique et basculée vers Azure, l’adresse IP est acquis via DHCP. Lorsque vous ne parvenez pas sur Précédent pour local, la machine virtuelle continue à utiliser DHCP pour obtenir l’adresse IP. Vous avez besoin pour vous connecter manuellement dans l’ordinateur et définir l’adresse IP au adresse statique si nécessaire.
4. Si vous utilisez édition gratuite ESXi 5.5 ou édition gratuite vSphere 6 hyperviseur, basculement devraient réussir, mais retour arrière n’aboutit pas. Vous allez ned mise à niveau vers une licence d’évaluation pour activer le retour arrière.

## <a name="failing-back-with-expressroute"></a>Coupures précédent avec ExpressRoute

Vous pouvez basculer renvoyé sur une connexion VPN ou Azure ExpressRoute. Si vous souhaitez utiliser ExpressRoute Remarque ce qui suit :

- ExpressRoute doit être configuré sur le réseau virtuel Azure à quelle source machines fail sur et dans quel machines virtuelles Azure se trouvent après le basculement.
- Sont répliquées vers un compte de stockage Azure sur un point de terminaison public. Vous devez définir homologue public dans ExpressRoute avec le centre de données cible de la réplication de récupération de Site à utiliser ExpressRoute.



