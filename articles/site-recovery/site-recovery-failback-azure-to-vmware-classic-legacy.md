<properties 
   pageTitle="Échec des machines virtuelles VMware précédent et des serveurs physiques à partir d’Azure à VMware (hérité) | Microsoft Azure" 
   description="Cet article décrit comment basculer une machine virtuelle VMware répliquées vers Azure avec récupération de Site Azure." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Échec des machines virtuelles VMware précédent et des serveurs physiques à partir d’Azure à VMware avec récupération de Site Azure (hérité)

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-failback-azure-to-vmware.md)
- [Portail classique Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portail classique Azure (hérité)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment échouer les machines virtuelles VMware précédent et serveurs physiques Linux/Windows Azure à votre site local une fois que vous avez répliquées à partir de votre site local vers Azure.

>[AZURE.NOTE] Cet article décrit un scénario hérité. Vous devez uniquement utiliser les instructions de cet article si vous répliquées vers Azure à l’aide de [ces instructions héritées](site-recovery-vmware-to-azure-classic-legacy.md). Si vous définissez la réplication à l’aide du [déploiement amélioré](site-recovery-vmware-to-azure-classic-legacy.md) , puis suivez les instructions de [cet article](site-recovery-failback-azure-to-vmware-classic.md) soit restauré. 


## <a name="architecture"></a>Architecture

Ce diagramme représente le scénario de basculement et de restauration. Les lignes sont les connexions utilisées pendant le basculement. Les lignes rouges sont les connexions utilisées lors du retour arrière. Les lignes avec des flèches accédez via internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Avant de commencer 

- Vous devez ait basculé votre machines virtuelles VMware ou les serveurs physiques et qu’ils doivent être en cours d’exécution dans Azure.
- Notez que vous pouvez uniquement échouer les machines virtuelles VMware précédent et serveurs physiques Linux/Windows Azure à des machines virtuelles VMware dans le site principal local.  Si vous êtes ignore revenir une machine physique, basculement vers Azure, il sera converti pour une machine virtuelle Azure et retour arrière vers VMware, il sera converti à une VMware VM.

Voici comment configurer la restauration automatique :

1. **Configurer des composants de retour arrière**: vous devez configurer un serveur vContinuum local et qu’il pointe vers le serveur de configuration machine virtuelle dans Azure. Vous devez également configurer un serveur de traitement comme une machine virtuelle Azure pour envoyer des données sur le serveur cible maître en local. Vous enregistrez le serveur de processus avec le serveur de configuration gérée le basculement. Vous installez un serveur de base cible local. Si vous avez besoin d’un serveur de base cible Windows elle est configurée automatiquement lorsque vous installez vContinuum. Si vous avez besoin de Linux, vous devez configurer manuellement sur un serveur différent.
2. **Activer la protection et restauration**: une fois que vous avez configuré les composants, dans vContinuum vous devez activer la protection pour basculé machines virtuelles Azure. Vous devez exécuter une vérification de la disponibilité sur les ordinateurs virtuels et exécuter un basculement à partir d’Azure à votre site local. Une fois terminée la restauration automatique vous restaurez protection ordinateurs locaux afin qu’ils commencer réplication vers Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Étape 1 : Installer vContinuum locaux

Vous devez installer un serveur vContinuum en local et qu’il pointe vers le serveur de configuration.

1.  [Télécharger vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Télécharger la version [vContinuum mettre à jour](http://go.microsoft.com/fwlink/?LinkID=533813) .
3. Installer la dernière version de vContinuum. Dans la page **Bienvenue** , cliquez sur **suivant**.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Sur la première page de l’Assistant, spécifiez l’adresse IP du serveur CX et le port du serveur CX. Sélectionnez **utiliser HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Trouvez le serveur de configuration de l’adresse IP sous l’onglet **tableau de bord** du serveur de configuration machine virtuelle dans Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Trouver le serveur de configuration port public HTTPS sous l’onglet **points de terminaison** du serveur de configuration machine virtuelle dans Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Dans la page **Détails de mot de passe SC** spécifier le mot de passe que vous avez noté vers le bas lorsque vous avez enregistré le serveur de configuration. Si vous ne vous souvenez pas il archiver **C:\\Program Files (x86)\\InMage systèmes\\privé\\connection.passphrase** sur le serveur de configuration machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Dans la page **Sélectionner un emplacement de Destination** , spécifiez l’endroit où vous voulez installer le serveur vContinuum et cliquez sur **installer**.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Une fois l’installation terminée, vous pouvez lancer vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Étape 2 : Installer un serveur de traitement dans Azure 

Vous devez installer un serveur de traitement dans Azure afin que les ordinateurs virtuels dans Azure peuvent renvoyer les données à un serveur principal cible local. 

1.  Dans la page **Configuration de serveurs** dans Azure, sélectionnez Ajouter un nouveau serveur de processus.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Spécifier un serveur de traitement nom et un nom et un mot de passe pour vous connecter à la machine virtuelle en tant qu’administrateur. Sélectionnez le serveur de configuration à laquelle vous souhaitez enregistrer le serveur de traitement. Il doit être du même serveur que vous utilisez pour protéger et échouer sur vos ordinateurs virtuels.
3.  Spécifiez le réseau Azure dans lequel le serveur de traitement doit être déployé. Il doit être le même réseau que le serveur de configuration. Spécifie un sous uniques IP adresse sélectionnée réseau et commencer le déploiement.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Une tâche se déclenche pour déployer le serveur de traitement.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Une fois que le serveur de processus est déployé dans Azure, vous pouvez vous connecter au serveur en utilisant les informations d’identification que vous avez spécifié. Enregistrez le serveur de processus de la même façon que vous avez enregistré le serveur de processus en local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Les serveurs enregistrés lors du retour arrière ne seront pas visibles, sous propriétés de la machine virtuelle dans récupération de Site. Ils sont uniquement visibles sous l’onglet **serveurs** du serveur de configuration auquel lequel ils sont enregistrés. Il peut prendre environ 10 à 15 minutes avant d’être le serveur de traitement s’affiche sous l’onglet.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Étape 3 : Installer un serveur principal cible en local

Selon votre système d’exploitation de machines virtuelles source, vous devez installer une Linux ou un serveur de base cible Windows locaux.

### <a name="deploy-a-windows-master-target-server"></a>Déployer un cible maître Windows server

Une cible maître Windows est déjà fournie avec le programme d’installation vContinuum. Lorsque vous installez la vContinuum, un serveur principal est également déployé sur le même ordinateur et inscrit sur le serveur de configuration.

1.  Pour commencer le déploiement, créez un vide de l’ordinateur local sur l’hôte ESX sur lequel vous souhaitez récupérer les ordinateurs virtuels à partir d’Azure.

2.  Vous assurer qu’il existe au moins deux disques attachés à la machine virtuelle : un est utilisé pour le système d’exploitation et l’autre est utilisé pour le lecteur de rétention.

3.  Installez le système d’exploitation.

4.  Installer le vContinuum sur le serveur. Installation du serveur maître cible est également terminée.

### <a name="deploy-a-linux-master-target-server"></a>Déployer un serveur de base cible Linux

1.  Pour commencer le déploiement, créez un vide de l’ordinateur local sur l’hôte ESX sur lequel vous souhaitez récupérer les ordinateurs virtuels à partir d’Azure.

2.  Vous assurer qu’il existe au moins deux disques attachés à la machine virtuelle : un est utilisé pour le système d’exploitation et l’autre est utilisé pour le lecteur de rétention.

3.  Installez le système d’exploitation Linux. Le système cible maître Linux ne devez pas utiliser le Gestionnaire de volume logique pour les espaces de stockage racine ou de rétention. Un serveur de base cible Linux est configuré pour éviter la découverte partitions/disques Gestionnaire de volume logique par défaut.
4.  Partitions que vous pouvez créer :

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Procéder à la publier ci-dessous les étapes d’installation avant de commencer l’installation maître cible.


#### <a name="post-os-installation-steps"></a>Publier des étapes d’Installation du système d’exploitation

Pour obtenir les ID SCSI pour chacun de disque SCSI dans une machine virtuelle Linux, activez le paramètre « disque. EnableUUID = TRUE » comme suit :

1. Arrêter votre machine virtuelle.
2. Avec le bouton droit de l’entrée de machine virtuelle dans le volet gauche > **Modifier les paramètres**.
3. Cliquez sur l’onglet **Options** . Sélectionnez **Avancé\>général de l’article** > **Paramètres de Configuration**. L’option **Paramètres de Configuration** est uniquement disponible lorsque l’ordinateur est arrêté.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Vérifie si une ligne avec **disque. EnableUUID** existe. Si elle et est définie sur **False** puis définissez-la sur **True** (pas respecter la casse). Si existe et est défini sur true, cliquez sur **Annuler** et testez la commande SCSI à l’intérieur du système d’exploitation invité une fois qu’il démarre. Si n’existe pas cliquez sur **Ajouter une ligne**.
5. Ajouter un disque. EnableUUID dans la colonne **nom** . Définissez sa valeur avec la valeur TRUE. Ne pas ajouter les valeurs ci-dessus ainsi que des guillemets doubles.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Télécharger et installer les packages supplémentaires

Remarque : Vérifiez que le système possède une connexion internet avant de télécharger et installer les packages supplémentaires.

\#YUM installer -y xfsprogs perl lsscsi ont wget kexec-outils

Cette commande télécharge ces 15 packages CentOS 6.6 référentiel et ils sont installés :

BC-1.06.95-1.el6.x86\_64. t/min

busybox-1.15.1-20.el6.x86\_64. t/min

elfutils-et les bibliothèques-0.158-3.2.el6.x86\_64. t/min

kexec-outils-2.0.0-280.el6.x86\_64. t/min

lsscsi-0,23-2.el6.x86\_64. t/min

lzo-2.03-3.1.el6\_5.1.x86\_64. t/min

Perl-5.10.1-136.el6\_6.1.x86\_64. t/min

Perl-Module-enfichable-3.90-136.el6\_6.1.x86\_64. t/min

Perl-bloc-d’échappement-1,04-136.el6\_6.1.x86\_64. t/min
    
Perl-bloc-Simple-3.13-136.el6\_6.1.x86\_64. t/min

Perl-et les bibliothèques-5.10.1-136.el6\_6.1.x86\_64. t/min

Perl-version-0.77-136.el6\_6.1.x86\_64. t/min

ont-3.0.6-12.el6.x86\_64. t/min

rapide 1.1.0-1.el6.x86\_64. t/min

wget-1.12-5.el6\_6.1.x86\_64. t/min

Remarque : Si la machine source utilise un système de fichiers Reiser ou XFS pour le périphérique racine ou de démarrage, puis packages logiciels suivants doivent être téléchargés et installés sur Linux maître cible avant la protection.

\#CD/usr/local

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#t/mn - ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64. t/min reiserfs-utilitaires-3.6.21-1.el6.elrepo.x86\_64. t/min

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#t/mn - ivh xfsprogs-3.1.1-16.el6.x86\_64. t/min

#### <a name="apply-custom-configuration-changes"></a>Appliquer les modifications de configuration personnalisée

Avant d’appliquer ces modifications Vérifiez que vous avez terminé la section précédente, puis procédez comme suit :


1. Copier l’Agent d’unifiée RHEL 6-64 binaire dans le système d’exploitation nouvellement créé.

2. Exécutez cette commande pour décompressez le fichier binaire : **tar - zxvf \<nom de fichier\> **

3. Exécutez cette commande pour accorder des autorisations : \# **./ApplyCustomChanges.sh 755 chmod**

4. Exécuter le script : ** \# ./ApplyCustomChanges.sh**. Exécuter le script qu’une seule fois sur le serveur. Redémarrez le serveur une fois que le script s’exécute.



### <a name="install-the-linux-server"></a>Installer le serveur Linux


1. [Télécharger](http://go.microsoft.com/fwlink/?LinkID=529757) le fichier d’installation.
2. Copiez le fichier à la machine virtuelle Linux Masque cible à l’aide d’un utilitaire client sftp de votre choix. Vous pouvez également vous pouvez ouvrir une session sur l’ordinateur Linux cible maître virtuel et wget permet de télécharger le package d’installation à partir du lien fourni
3. Connectez-vous à Linux cible maître serveur machine virtuelle utilise un ssh client de votre choix
4. Si vous êtes connecté au réseau Azure sur lequel vous avez déployé votre serveur cible maître Linux via une connexion VPN utilisez l’adresse IP interne du serveur que vous pouvez trouver dans l’onglet **tableau de bord** machine virtuelle et le port 22 pour vous connecter à la cible maître Linux Server à l’aide de Secure Shell.
5. Si vous vous connectez au serveur Linux cible maître via une connexion internet public utiliser la Linux cible maître public virtuel adresse IP du serveur (à partir de l’onglet de **tableau de bord** machines virtuelles) et le point de terminaison public créé pour ssh pour se connecter à la servder Linux.
6. Extraire les fichiers à partir de l’archive tar compressés Linux cible maître Server installer en exécutant : *« tar – xvzf Microsoft-récupération automatique du système\_CU\_8.2.0.0\_RHEL6 64\ «* à partir du répertoire qui contient le fichier d’installation.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Si vous avez extrait le programme d’installation modifier les fichiers vers un autre répertoire à l’annuaire pour lequel le contenu du paquetage archive ont été extraits. À partir de ce chemin d’accès du répertoire exécuter « sudo. / install.sh ».

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Lorsque vous êtes invité à choisir un rôle principal Sélectionnez **2 (masque cible)**. Conservez les autres options d’installation interactive de valeurs par défaut.
9. Attendez d’installation pour continuer et l’Interface de configuration hôte doit apparaître. L’utilitaire de Configuration de l’hôte de la sarget maître Linux Server est un utilitaire de ligne de commande. Ne pouvez pas redimensionner le ssh fenêtre de l’utilitaire client. Utilisez les touches de direction pour sélectionner l’option **Global** et appuyez sur entrée sur votre clavier.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Dans champ **IP** , entrez l’adresse IP interne du serveur de configuration (vous pouvez le trouver dans l’onglet **tableau de bord** du serveur de configuration machine virtuelle), puis appuyez sur ENTRÉE. Dans la zone **Port** , entrez **22** et appuyez sur ENTRÉE. 
11.  Laissez **HTTPS utiliser** en tant que **Oui**et appuyez sur ENTRÉE.
12.  Entrez le mot de passe qui a été généré sur le serveur de configuration. Si vous utilisez un client mastic à partir d’un ordinateur Windows pour ssh à la machine virtuelle Linux, vous pouvez utiliser Maj + Inser pour coller le contenu du Presse-papiers. Copier le mot de passe dans le Presse-papiers local à l’aide de Ctrl + C et utilisez MAJ + INSER pour coller. Appuyez sur ENTRÉE.
13.  Utilisez la touche flèche droite pour parcourir pour quitter et appuyez sur ENTRÉE. Attendez que l’installation à effectuer.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si pour une raison quelconque vous n’avez pas enregistrer votre serveur cible maître Linux sur le serveur de configuration vous pouvez faire encore une fois en exécutant l’hôte utilitaire de configuration à partir de /usr/local/ASR/Vx/bin/hostconfigcli (vous êtes d’abord nécessaire pour définir des autorisations d’accès à ce répertoire en exécutant chmod tant qu’utilisateur super).


#### <a name="validate-master-target-registration"></a>Valider l’inscription cible maître

Vous pouvez valider que le serveur cible maître a été enregistré correctement sur le serveur de configuration de l’archivage sécurisé récupération de Site Azure > **Serveur de Configuration** > **Détails du serveur**.

>[AZURE.NOTE] Après avoir enregistré le serveur maître cible, si vous recevez des erreurs de configuration que la machine virtuelle ont été supprimée à partir d’Azure ou points de terminaison ne sont pas correctement configurées, il s’agit, car bien que la configuration cible maître est détectée par le dndpoints Azure lorsque la cible maître est déployée dans Azure, ce n’est pas true pour un local cible maître server local. Cela n’affecte pas le retour arrière et vous pouvez ignorer ces erreurs. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Étape 4 : Protéger les machines virtuelles au site local

Vous devez protéger machines virtuelles au site local avant de vous restaurez.

### <a name="before-you-begin"></a>Avant de commencer

Lorsqu’une machine virtuelle est basculée vers Azure, il ajoute un lecteur temp supplémentaires pour le fichier de la page. Il s’agit d’un disque supplémentaire est généralement pas requis par votre Échec sur machine virtuelle car il peut possède déjà un disque dédié fichier de la page. Avant de commencer inverse la protection des machines virtuelles, vous devez vous assurer que ce lecteur est considérée en mode hors connexion pour qu’elles ne pas obtenir protégées. Procédez comme suit :

1.  Ouvrez Gestion de l’ordinateur et sélectionnez gestion du stockage afin qu’elle répertorie les disques en ligne et relié à l’ordinateur.
2.  Sélectionnez le disque temporaire lié à la machine, puis mettre en mode hors connexion. 

### <a name="protect-the-vms"></a>Protéger les ordinateurs virtuels

1. Dans le portail Azure, vérifiez l’état de la machine virtuelle pour vous assurer qu’ils vous ont basculé.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Lancez vContinuum sur votre ordinateur.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Cliquez sur **Nouvelle Protection** et sélectionnez le type de système opération, la 

4.  Dans la nouvelle fenêtre qu’ouvrir sélectionner le **type de système d’exploitation** > **Obtenir plus d’informations** pour les ordinateurs virtuels que vous voulez basculer. **Détails du serveur principal**, identifiez et sélectionnez les machines virtuelles que vous voulez protéger. Machines virtuelles sont répertoriés sous le nom d’hôte vCenter qu’ils ont été sur avant le basculement.
5.  Lorsque vous sélectionnez une machine virtuelle pour protéger (et il a déjà basculé vers Azure) une fenêtre contextuelle propose deux entrées pour la machine virtuelle. C’est parce que le serveur de configuration détecte deux instances des machines virtuelles enregistrés à. Vous devez supprimer l’entrée pour la machine virtuelle locaux afin que vous puissiez protéger la machine virtuelle correcte. Pour identifier la machine virtuelle Azure correction ici, vous pouvez vous connecter à la machine virtuelle Azure et accédez à C:\Program Files (x86) \Microsoft Azure Site Recovery\Application Data\etc. Dans le fichier drscout.conf, identifier l’ID d’hôte. Dans la boîte de dialogue vContinuum, conserver l’entrée pour laquelle vous avez trouvé l’ID de l’hôte sur l’ordinateur virtuel. Supprimer toutes les autres entrées. Pour sélectionner la machine virtuelle correcte, vous pouvez faire référence à son adresse IP. Les adresses IP adresse plage locale sera la machine virtuelle en local.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Sur le serveur vCenter arrêter la machine virtuelle. Vous pouvez également supprimer les machines virtuelles en local.
7. Puis spécifiez le serveur de MT local auquel vous voulez protéger les ordinateurs virtuels. Pour ce faire, la connexion au serveur vCenter auquel vous souhaitez être restauré.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Sélectionnez le serveur cible maître basé sur l’hôte auquel vous souhaitez récupérer la machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Donne la possibilité de réplication pour chacun des machines virtuelles. Pour ce faire, vous devez sélectionner le magasin de données du côté récupération auquel les ordinateurs virtuels seront récupérés. Le tableau récapitule les différentes options que vous devez fournir pour chaque machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Option** | **Option valeur recommandée**
    ---|---
    Adresse IP du serveur de processus | Sélectionnez le serveur de processus déployé dans Azure
    Taille en Mo de rétention| 
    Valeur de rétention | 1
    Jours/heures | Jours
    Intervalle de cohérence | 1
    Sélectionnez la cible de magasin de données | Le magasin de données disponible sur le site de récupération. Le magasin de données doit avoir suffisamment d’espace et soit disponible à l’hôte ESX sur lequel vous voulez restaurer la machine virtuelle.


10. Configurer les propriétés de la machine virtuelle cherche après basculement vers le site local. Les propriétés sont répertoriées dans le tableau suivant.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **Propriété** | **Plus d’informations**
    ---|---
    Configuration du réseau| Pour chaque carte réseau est détecté, sélectionnez-le, puis cliquez sur **Modifier** pour configurer l’adresse IP retour arrière pour la machine virtuelle. 
    Configuration matérielle| Spécifiez la charge du processeur et la mémoire pour la machine virtuelle. Paramètres peuvent être appliqués à tous les ordinateurs virtuels vous cherchez à protéger. Pour identifier les valeurs correctes pour le processeur et la mémoire, vous pouvez faire référence à la taille de rôle machines virtuelles IAAS et afficher le nombre de cœurs et la mémoire affectée.
    Nom d’affichage | Après restauration en local, vous pouvez renommer les machines virtuelles tels qu’ils apparaîtront dans le stock vCenter. Le nom par défaut est le nom d’hôte ordinateur machine virtuelle. Pour identifier le nom de la machine virtuelle, vous pouvez faire référence à la liste de mémoire virtuelle dans le groupe de Protection.
    Configuration NAT | Décrites en détail ci-dessous

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Configurer les paramètres de NAT

1. Pour activer la protection des machines virtuelles, deux canaux de communication doivent être établies. Le premier canal est comprise entre la machine virtuelle et le serveur de traitement. Ce canal collecte les données à partir de la machine virtuelle et envoie au serveur de processus qui envoie ensuite les données sur le serveur maître cible. Si le serveur de traitement et la machine virtuelle à protéger se trouvent sur le même réseau virtuel Azure vous n’avez pas besoin d’utiliser des paramètres de NAT. Sinon, spécifiez les paramètres de NAT. Afficher l’adresse IP du serveur de processus dans Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Le second canal est comprise entre le serveur de traitement et le serveur maître cible. La possibilité d’utiliser NAT ou non dépend d’à l’aide d’une connexion VPN en fonction ou de la communication via internet. Ne sélectionnez pas NAt si vous utilisez VPN, mais uniquement si vous utilisez une connexion internet.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Si vous n’avez pas supprimé les machines virtuelles local comme spécifié et si le magasin de données que vous ne pouvez pas revenir à toujours contient le VMDK ancien vous devez vous assurer que le rétablissement que machine virtuelle est créée dans un nouvel emplacement. Pour sélectionner les paramètres **avancés** et spécifier un autre dossier pour restaurer **Les paramètres de noms de dossier**. Laisser les autres options avec leurs paramètres par défaut. Appliquer les paramètres de nom de dossier à tous les serveurs.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Exécuter une vérification de la disponibilité pour vous assurer que les ordinateurs virtuels sont prêtes à être protégées en local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Attendez qu’elle se termine. S’il est correctement sur tous les ordinateurs virtuels vous pouvez spécifier un nom pour le plan de protection. Puis cliquez sur **protéger** pour commencer.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Vous pouvez surveiller l’avancement dans vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Après l’étape de **L’activation d’un Plan de Protection** terminée, vous pouvez surveiller protection machine virtuelle dans le portail de récupération de Site.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Vous pouvez afficher le statut exact en cliquant sur la machine virtuelle et de surveillance de sa progression.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Préparer le plan de restauration

Vous pouvez préparer un plan de retour arrière à l’aide de vContinuum afin que l’application peut être basculée vers le site local à tout moment. Ces offres récupération sont très semblables aux plans de récupération de récupération de Site.

1.  Ouvrez vContinuum, puis sélectionnez **Gérer les plans** > **récupérer.** Vous pouvez voir de liste de toutes les offres qui ont été utilisés pour basculer sur machines virtuelles. Vous pouvez utiliser la même offre à récupérer.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Sélectionnez le plan de protection et tous les ordinateurs virtuels que vous souhaitez récupérer qu’il contient. Lorsque vous sélectionnez chaque machine virtuelle vous pouvez voir d’autres détails tels que le serveur ESX cible et le disque de machine virtuelle source. Cliquez sur **suivant** pour lancer l’Assistant récupérer et sélectionnez les ordinateurs virtuels que vous souhaitez récupérer.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Vous pouvez les récupérer en fonction de plusieurs options, mais nous vous recommandons d’utiliser la **Dernière balise** et sélectionnez **Appliquer pour tous les ordinateurs virtuels** pour vous assurer que les données les plus récentes à partir de la machine virtuelle seront utilisées.
4. Exécuter le **vérification de la disponibilité.** Cela doit vérifier si les paramètres de droite sont configurés pour activer la récupération de machine virtuelle. Si tous les tests ont réussi, cliquez sur **suivant** . Dans le cas contraire, consultez le journal et résoudre les erreurs.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Dans la **Configuration de machines virtuelles** vous assurer que les paramètres de récupération sont définis correctement. Vous pouvez modifier les paramètres de la machine virtuelle si vous avez besoin.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Passez en revue la liste des machines virtuelles qui sera récupérée et spécifiez un ordre de récupération. Notez que les ordinateurs virtuels sont répertoriés en utilisant le nom d’hôte ordinateur. Il peut être difficile à établir une correspondance entre le nom d’hôte ordinateur la machine virtuelle.
Pour faire correspondre les noms, accédez aux machines virtuelles **tableau de bord** dans Azure et vérifier le nom d’hôte machine virtuelle.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Spécifiez un nom de plan, puis sélectionnez **récupérer ultérieurement**. Nous vous recommandons de récupérer plus tard dans la mesure où la protection initiale ne soient pas complet. 
11. Cliquez sur **récupérer** pour enregistrer le plan ou d’un déclencheur la récupération si vous avez sélectionné pour récupérer maintenant et au plus tard. Vous pouvez vérifier l’état de récupération pour voir si le plan est été enregistré.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Récupérer des machines virtuelles

Une fois le plan est créé, vous pouvez récupérer les machines virtuelles. Vous contrôlez avant que les ordinateurs virtuels ont terminé la synchronisation. Si l’état de la réplication affiche OK puis la protection se termine et le seuil RPO a été satisfait. Vous pouvez vérifier le fonctionnement dans les propriétés de la machine virtuelle.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Désactiver les machines virtuelles Azure avant de lancer la récupération. Ainsi, il n’existe aucun fractionnement et que les utilisateurs n’accèdent uniquement une copie de l’application. 


1.  Démarrez le plan enregistré. Dans vContinuum sélectionnez **Moniteur** les offres. Répertorie toutes les offres qui ont été exécutés.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Sélectionnez le plan de **restauration** , cliquez sur **Démarrer**. Vous pouvez contrôler la récupération. Après machines virtuelles ont été désactivés sur vous pouvez vous connecter à leur dans vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Protéger à Azure après restauration

Une fois la restauration automatique terminée, vous souhaiterez probablement protéger les machines virtuelles à nouveau. Procédez comme suit :

1.  Vérifiez que les ordinateurs virtuels locaux sont exécutées et que les applications sont accessibles.
2.  Dans le portail de récupération de Site Azure, sélectionnez les machines virtuelles et supprimez-les. Sélectionnez pour désactiver la protection des machines virtuelles. Cela garantit que les ordinateurs virtuels ne sont pas plus protégés.
3.  Dans Azure supprimer l’échec de machines virtuelles Azure
4.  Supprimer l’ancienne machine virtuelle sur vSpehere. Il s’agit des ordinateurs virtuels que vous avez précédemment basculé vers Azure.
5.  Dans le portail de récupération de Site protéger les ordinateurs virtuels qui récemment basculaient. Une fois qu’ils sont protégés vous pouvez les ajouter à un plan de récupération.
 
## <a name="next-steps"></a>Étapes suivantes



- [En savoir plus sur](site-recovery-vmware-to-azure-classic.md) réplication machines virtuelles VMware et des serveurs physiques vers Azure à l’aide du déploiement amélioré.

 
