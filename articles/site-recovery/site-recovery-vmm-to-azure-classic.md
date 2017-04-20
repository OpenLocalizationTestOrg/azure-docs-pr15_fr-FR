<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM sur Azure | Microsoft Azure"
    description="Cet article décrit comment répliquer machines virtuelles Hyper-V hôtes Hyper-V situés dans des nuages de System Center VMM à Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Réplication machines virtuelles Hyper-V dans nuages VMM vers Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portail classique](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classique](site-recovery-deploy-with-powershell.md)



Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md).

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment déployer récupération de Site pour répliquer machines virtuelles de Hyper-V sur les serveurs hôtes Hyper-V qui sont trouvent dans nuages privés VMM à Azure.

L’article inclut les conditions préalables pour le scénario et explique comment configurer un archivage sécurisé récupération de Site, obtenir le fournisseur de récupération de Site Azure installés sur le serveur VMM source, inscrire le serveur dans l’archivage sécurisé, ajouter un compte de stockage Azure, installez l’agent de Services de récupération Azure sur vos serveurs hôtes Hyper-V, configurer les paramètres de protection pour nuages VMM qui seront appliquées à toutes les machines virtuelles protégées , puis activez la protection pour ces machines virtuelles. Terminer la configuration en testant le basculement pour vous assurer que tout fonctionne comme prévu.

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Architecture

![Architecture](./media/site-recovery-vmm-to-azure-classic/topology.png)

- Le fournisseur de récupération de Site Azure est installé sur le moniteur pendant le déploiement de récupération de Site et le serveur VMM est enregistré dans l’archivage sécurisé récupération de Site. Le fournisseur communique avec récupération de Site pour gérer orchestration réplication.
- L’agent de Services de récupération Azure est installé sur vos serveurs hôtes Hyper-V pendant le déploiement de récupération de Site. Il traite réplication des données vers le stockage Azure.


## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure.

**Conditions préalables** | **Plus d’informations**
--- | ---
**Compte Azure**| Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**Stockage Azure** | Vous devez posséder un compte de stockage Azure pour stocker les données dupliquées. Données répliquées sont stockées dans le stockage Azure et machines virtuelles Azure sont empilés des cas de basculement. <br/><br/>Vous avez besoin d’un [compte de stockage geo redondants standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit dans la même région en tant que le service de récupération de Site et être associés à la même abonnement. Notez que la réplication aux comptes de stockage premium n’est pas actuellement pris en charge et ne doit pas être utilisée.<br/><br/>[En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous avez besoin d’un réseau virtuel Azure qui machines virtuelles Azure se connecteront à basculement. Le réseau virtuel Azure doit être placé dans la même région en tant que l’archivage sécurisé récupération de Site.

## <a name="on-premises-prerequisites"></a>Conditions préalables pour les locaux

Voici ce que vous devez en local.

**Conditions préalables** | **Plus d’informations**
--- | ---
**VMM** | Vous devez au moins un serveur VMM déployé sous la forme d’un serveur physique ou virtuel autonome ou un cluster virtuel. <br/><br/>Le serveur VMM doit s’exécuter System Center 2012 R2 avec les dernières mises à jour cumulatives.<br/><br/>Vous devez au moins un nuage configuré sur le serveur VMM.<br/><br/>Le cloud source que vous voulez protéger doit contenir un ou plusieurs groupes hôtes VMM.<br/><br/>En savoir plus sur la configuration nuages VMM dans [procédure pas à pas : création de nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) sur le blog de Keith Mayer.
**Hyper-V** | Vous avez besoin d’un ou plusieurs des serveurs hôtes Hyper-V ou des groupes dans le nuage VMM. Le serveur hôte doit avoir un ou plusieurs ordinateurs virtuels et. <br/><br/>Le serveur Hyper-V doit être en cours d’exécution au moins **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2** et ont installé les dernières mises à jour.<br/><br/>N’importe quel serveur Hyper-V contenant des machines virtuelles à protéger doit se trouver dans un nuage VMM.<br/><br/>Si vous exécutez Hyper-V dans une note cluster ce intermédiaire cluster n’est pas créée automatiquement si vous avez un cluster de basée sur l’adresse IP statique. Vous devez configurer manuellement l’intermédiaire cluster. [En savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) dans une entrée de blog de Marie Finn.
**Machines protégées** | Machines virtuelles à protéger doivent être conformes aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Conditions préalables mappage réseau
Lorsque vous protégez les machines virtuelles dans les cartes de mappage réseau Azure entre les réseaux machine virtuelle sur le serveur VMM source et ciblez Azure réseaux pour activer ce qui suit :

- Tous les ordinateurs où le basculement sur le même réseau peut se connecter à d’autres, quel que soit le plan de récupération elles se trouvent dans.
- Si une passerelle réseau est configuré sur la réseau Azure cible, machines virtuelles peuvent se connecter aux autres machines virtuelles en local.
- Si vous ne configurez pas réseau mappage uniquement machines virtuelles basculer dans le même plan de récupération sera peuvent se connecter à l’autre après le basculement vers Azure.

Si vous voulez déployer mappage réseau, vous devez les éléments suivants :

- Machines virtuelles à protéger sur le serveur VMM source doit être connectés à un réseau machine virtuelle. Ce réseau doit être lié à un réseau logique est associé avec le cloud.
- Un réseau Azure auquel les machines virtuelles dupliquées peuvent se connecter après le basculement. Vous pouvez de sélectionner ce réseau au moment de basculement. Le réseau doit être dans la même région en tant que votre abonnement Azure Site récupération.

Préparer pour le réseau mappage comme suit :

1. [Découvrez les](site-recovery-network-mapping.md) exigences de mappage réseau.
2. Préparer les réseaux machine virtuelle dans VMM :

    - [Configurer des réseaux logiques](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurer des réseaux machine virtuelle](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Étape 1 : Créer un archivage sécurisé récupération de Site

1. Connectez-vous au [Portail de gestion](https://portal.azure.com) du serveur VMM que vous souhaitez enregistrer.
2. Cliquez sur **Data Services** > **Services de récupération** > **l’archivage sécurisé récupération de Site**.
3. Cliquez sur **créer une nouvelle** > **Création rapide**.
4. Dans la zone **nom**, entrez un nom convivial pour identifier l’archivage sécurisé.
5. Dans **la région**, sélectionnez la région géographique pour l’archivage sécurisé. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Cliquez sur **créer l’archivage sécurisé**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Vérifiez la barre d’état pour confirmer que l’archivage sécurisé a été créée. L’archivage sécurisé est indiquée comme étant **actif** sur la page principale dans les Services de récupération.

## <a name="step-2-generate-a-vault-registration-key"></a>Étape 2 : Générer une clé d’inscription de l’archivage sécurisé

Générer une clé d’enregistrement dans l’archivage sécurisé. Après avoir téléchargé le fournisseur de récupération de Site Azure et installez-le sur le serveur VMM, vous allez utiliser cette clé pour inscrire le serveur VMM dans l’archivage sécurisé.

1. Dans la page **Services de récupération** , cliquez sur l’archivage sécurisé pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. Dans la liste déroulante, sélectionnez **entre un site VMM en local et Microsoft Azure**.
3. Dans les **Serveurs de VMM préparer**, cliquez sur fichier de **Générer une clé d’inscription** . Le fichier de clé est généré automatiquement et est valable 5 jours après sa création. Si vous n’êtes pas accéder au portail Azure à partir du serveur VMM, vous devez copier ce fichier sur le serveur.

    ![Clé d’enregistrement](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Étape 3 : Installer le fournisseur de récupération de Site Azure

1. Dans la zone **Démarrage rapide** > **serveurs VMM préparer**, cliquez sur **Télécharger Microsoft Azure récupération fournisseur de Site pour l’installation sur des serveurs VMM** pour obtenir la dernière version du fichier d’installation fournisseur.
2. Exécuter ce fichier sur le serveur VMM source.

    >[AZURE.NOTE] Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois l’installer sur un nœud actif et terminer l’installation pour enregistrer le serveur VMM dans l’archivage sécurisé. Installez ensuite le fournisseur sur les autres nœuds. Notez que si vous mettez à niveau le fournisseur que vous devez mettre à niveau sur tous les nœuds car ils doivent tous exécuter la même version du fournisseur.

3. Le programme d’installation effectue une vérification prerequirements et les demandes d’autorisation pour arrêter le service VMM pour commencer la configuration du fournisseur. Le Service VMM sera redémarré automatiquement lors de la fin de l’installation. Si vous installez sur un cluster VMM que vous serez invité à arrêter le rôle Cluster.

4. Dans **Microsoft Update,** vous pouvez choisir dans les mises à jour. Lorsque ce paramètre est activé fournisseur mises à jour seront installés en fonction de votre stratégie de Microsoft Update.

    ![Mises à jour Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  L’emplacement d’installation pour le fournisseur est défini sur ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin**. Cliquez sur **installer**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Après avoir installé le fournisseur de cliquer sur **Enregistrer** pour enregistrer le serveur dans l’archivage sécurisé.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. Dans la zone **nom de l’archivage sécurisé**, vérifiez le nom de l’archivage sécurisé dans lequel le serveur sera enregistré. Cliquez sur *suivant*.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. Dans **Connexion Internet** spécifiez comment le fournisseur en cours d’exécution sur le serveur VMM se connecte à Internet. Sélectionnez **se connecter avec les paramètres de proxy existants** d’utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres d’Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur. Lorsque vous configurez des paramètres de proxy personnalisés un test s’exécutera pour vérifier la connexion de proxy.
    - Si vous n’utilisez pas un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez entrer les détails de proxy, y compris l’adresse proxy et le port.
    - URL suivantes doivent être accessibles à partir du serveur VMM et les hôtes Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Autoriser les adresses IP décrits dans les [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/confirmation.aspx?id=41653) et HTTPS protocol (443). Vous devez plages d’adresses IP liste blanche de la région Azure que vous envisagez d’utiliser et celle des États-Unis Ouest.
    - Si vous utilisez un proxy personnalisé qu'un compte RunAs VMM (DRAProxyAccount) est créé automatiquement en utilisant les informations d’identification du proxy spécifié. Configurer le serveur proxy de sorte que ce compte peut s’authentifier avec succès. Les paramètres de compte RunAs VMM peuvent être modifiés dans la console. Pour ce faire, ouvrir l’espace de travail **paramètres** développez **sécurité**et cliquez sur **Exécuter en tant que comptes**, puis modifiez le mot de passe pour DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.


8. Sous **Clé d’inscription**, sélectionnez la clé que vous avez téléchargé à partir de récupération de Site Azure copiés sur le serveur VMM.


10.  Le paramètre de chiffrement est utilisé uniquement lorsque vous êtes réplication machines virtuelles Hyper-V dans nuages VMM vers Azure. Si vous êtes réplication vers un site secondaire n’est pas utilisé.

11.  Dans la zone **nom du serveur**, spécifiez un nom convivial pour identifier le serveur VMM dans l’archivage sécurisé. Dans une configuration cluster spécifiez le nom du rôle cluster VMM.
12.  Dans Sélectionner **synchroniser cloud métadonnées** si vous souhaitez synchroniser les métadonnées pour tous les nuages sur le serveur VMM avec l’archivage sécurisé. Cette action ne doit se produire une fois sur chaque serveur. Si vous ne voulez pas synchroniser tous les nuages, vous pouvez laisser ce paramètre désactivé et synchroniser chaque cloud individuellement dans les propriétés du cloud dans la console.

13.  Cliquez sur **suivant** pour terminer le processus. Après l’enregistrement, métadonnées à partir du serveur VMM récupérées lors de la récupération de Site Azure. Le serveur s’affiche sous l’onglet **Serveurs VMM** dans la page de **serveurs** dans l’archivage sécurisé.

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Après l’enregistrement, métadonnées à partir du serveur VMM récupérées lors de la récupération de Site Azure. Le serveur s’affiche sous l’onglet **Serveurs VMM** dans la page de **serveurs** dans l’archivage sécurisé.

### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure peut également être installé à l’aide de la ligne de commande suivante. Cette méthode peut être utilisée pour installer le fournisseur sur un serveur Core pour Windows Server 2012 R2.

1. Téléchargez la clé de fichier et d’enregistrement de l’installation fournisseur dans un dossier. Par exemple : C:\ASR.
2. Arrêter le service System Center Machine virtuelle Manager
3. À partir d’une invite de commandes avec élévation de privilèges, extrayez le programme d’installation de fournisseur avec ces commandes :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installez le fournisseur comme suit :

        C:\ASR> setupdr.exe /i

5. Enregistrer le fournisseur comme suit :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Où les paramètres sont les suivants :

 - **/Credentials** : paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
 - **/FriendlyName** : paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
 - **/EncryptionEnabled** : paramètre facultatif pour spécifier si vous voulez le chiffrement vos machines virtuelles dans Azure (en chiffrement reste). Le nom du fichier doit avoir une extension **.pfx** .
 - **/ProxyAddress** : paramètre facultatif qui spécifie l’adresse du serveur proxy.
 - **/ProxyPort** : paramètre facultatif qui spécifie le port du serveur proxy.
 - **/proxyUsername** : paramètre facultatif qui indique le nom d’utilisateur proxy.
 - **/proxyPassword** : paramètre facultatif qui spécifie le mot de passe.  


## <a name="step-4-create-an-azure-storage-account"></a>Étape 4 : Créer un compte de stockage Azure

1. Si vous n’avez pas un compte de stockage Azure, cliquez sur **Ajouter un compte de stockage Azure** pour créer un compte.
2. Créez un compte avec geo-réplication est activée. Il doit dans la même région en tant que le service de récupération de Site Azure et être associés à la même abonnement.

    ![Compte de stockage](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Étape 5 : Installer l’Agent de Services de récupération Azure

Installez l’agent de Services de récupération Azure sur chaque serveur hôte Hyper-V dans le nuage VMM.

1. Cliquez sur **Quick Start** > **Agent des Services de récupération télécharger Azure Site et installez sur hôtes** pour obtenir la dernière version de fichier d’installation de l’agent.

    ![Installer l’Agent de Services de récupération](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Exécutez le programme d’installation sur chaque serveur hôte Hyper-V.
3. Dans la page **Prerequisites Check** cliquez sur **suivant**. Les composants requis manquants seront automatiquement installés.

    ![Agent de Services de récupération de conditions préalables](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. Dans la page **Paramètres de l’Installation** , spécifiez l’endroit où vous voulez installer l’agent et sélectionnez l’emplacement du cache dans lequel les métadonnées de sauvegarde va être installée. Cliquez sur **installer**.
5. Une fois l’installation terminée, cliquez sur **Fermer** pour fermer l’Assistant.

    ![Inscrire Agent de MARS](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Installation de ligne de commande

Vous pouvez également installer l’Agent de Services Microsoft Azure récupération à partir de la ligne de commande à l’aide de cette commande :

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Étape 6 : Configurer cloud paramètres de protection

Une fois que le serveur VMM est enregistré, vous pouvez configurer les paramètres de protection cloud. Vous avez activé l’option **synchroniser les données de cloud avec l’archivage sécurisé** lorsque vous avez installé le fournisseur de sorte que tous les nuages sur le serveur VMM apparaisse dans l’onglet <b>Éléments protégée</b> dans l’archivage sécurisé.

![Cloud publié](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Dans la page de démarrage rapide, cliquez sur **définir la protection pour les nuages VMM**.
2. Sous l’onglet **Éléments protégé** , cliquez sur le cloud que vous souhaitez configurer et accédez à l’onglet **Configuration** .
3. Dans **cible** sélectionnez **Azure**.
4. Dans un **Compte de stockage** , sélectionnez le compte de stockage Azure que vous utilisez pour la réplication.
5. Définissez **chiffrer les données stockées** sur **désactiver**. Ce paramètre spécifie que les données doivent être chiffrées répliquées entre le site local et Azure.
6. Dans la zone **Copier fréquence** laissez le paramètre par défaut. Cette valeur spécifie la fréquence à laquelle les données doivent être synchronisées entre les sites sources et cibles.
7. **Conserver les points de récupération**, laissez le paramètre par défaut. Avec une valeur par défaut de zéro, seul le dernier point de récupération pour une machine virtuelle principal est stocké sur un serveur hôte réplica.
8. Dans la **fréquence des instantanés cohérents avec les applications**, laissez le paramètre par défaut. Cette valeur spécifie la fréquence de création des instantanés. Instantanés permet de s’assurer que les applications sont dans un état cohérent lorsque l’instantané Volume Shadow Copy Service (VSS).  Si vous ne définissez pas une valeur, assurez-vous qu’il provient inférieur au nombre de points de récupération supplémentaires que vous configurez.
9. Dans la zone **heure de début de la réplication**, spécifiez lors de la réplication initiale des données à Azure doit commencer. Le fuseau horaire sur le serveur hôte Hyper-V est utilisé. Nous vous conseillons de prévoir la réplication initiale en dehors des heures.

    ![Paramètres de réplication cloud](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Une fois que vous enregistrez les paramètres d’une tâche est créée et peut être contrôlée sous l’onglet **tâches** . Tous les serveurs hôtes Hyper-V dans le cloud source VMM seront configurés pour la réplication.

Après l’avoir enregistré, cloud paramètres peuvent être modifiés dans l’onglet **configurer** . Pour modifier l’emplacement de destination ou le compte de stockage cible, vous devez supprimer la configuration cloud, et puis reconfigurer le cloud. Notez que si vous modifiez le compte de stockage la modification est appliquée uniquement pour les machines virtuelles sont activés pour la protection une fois le compte de stockage a été modifié. Machines virtuelles existantes ne sont pas migrés vers le nouveau compte de stockage.

## <a name="step-7-configure-network-mapping"></a>Étape 7 : Configurer le mappage de réseau
Avant de commencer mappage réseau Vérifiez que machines virtuelles sur le serveur VMM source sont connectés à un réseau machine virtuelle. En outre créer un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux machine virtuelle peuvent être mappées à un seul réseau Azure.

1. Dans la page de démarrage rapide, cliquez sur **mapper réseaux**.
2. Sous l’onglet **réseaux** , dans un **emplacement Source**, sélectionnez le serveur VMM source. Dans **un emplacement cible** , sélectionnez Azure.
3. Une liste de réseaux machine virtuelle associé au serveur VMM s’affichent dans les réseaux de **Source** . Les réseaux Azure associés à l’abonnement sont affichent dans les réseaux **cible** .
4. Sélectionnez le réseau machine virtuelle source, puis cliquez sur **carte**.
5. Dans la page **Sélectionner un réseau cible** , sélectionnez la cible Azure réseau que vous voulez utiliser.
6. Activez la case à cocher pour terminer le processus de mappage.

    ![Paramètres de réplication cloud](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Une fois que vous enregistrez les paramètres une tâche commence à suivre la progression de mappage et il peut être analysé dans l’onglet tâches. N’importe quel réplica des machines virtuelles existantes qui correspondent au réseau machine virtuelle source sera connectés à la cible Azure réseaux. Nouvelles machines virtuelles qui sont connectés au réseau machine virtuelle source sera connectés au réseau Azure mappé après la réplication. Si vous modifiez un mappage avec un réseau existant, vous serez connectés machines virtuelles réplica avec les nouveaux paramètres.

Notez que si le réseau cible a plusieurs sous réseaux et un de ces sous-réseaux a le même nom que sous-réseau sur lequel se trouve l’ordinateur virtuel source, puis la machine virtuelle réplica sera connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible ayant un nom correspondant, la machine virtuelle est connectée au premier sous-réseau dans le réseau.

> [AZURE.NOTE] [Migration des réseaux](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Étape 8 : Activer la protection des machines virtuelles

Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection des machines virtuelles dans le cloud. Notez les points suivants :

- Machines virtuelles doit répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Pour activer la protection du système d’exploitation et le système d’exploitation les propriétés d’un disque doivent être définies pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l’aide d’un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et la **Configuration matérielle** des propriétés machine virtuelle. Si vous ne définissez ces propriétés dans VMM vous pourrez les configurer dans le portail de récupération de Site Azure.

    ![Créer la machine virtuelle](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modifier les propriétés de la machine virtuelle](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Pour activer la protection, sous l’onglet **Machines virtuelles** dans le cloud dans lequel se trouve la machine virtuelle, cliquez sur **Activer la protection** > **machines virtuelles ajouter**.
2. Dans la liste des machines virtuelles dans le cloud, sélectionnez celle que vous voulez protéger.

    ![Activer la protection de la machine virtuelle](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Suivre la progression de l’action **Activer la Protection** dans l’onglet **tâches** , y compris la réplication initiale. Après l’exécution de la tâche de **Protection finaliser** la machine virtuelle est prête pour le basculement. Une fois la protection est activée et machines virtuelles sont répliquées, vous pourrez les afficher dans Azure.


    ![Machine virtuelle une tâche de protection](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Vérifier les propriétés de la machine virtuelle et modifiez-le si nécessaire.

    ![Vérifier les machines virtuelles](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. Sous l’onglet **configurer** les propriétés de la machine virtuelle propriétés réseau suivantes peuvent être modifiées.





- **Nombre de cartes réseau sur l’ordinateur virtuel cible** - le nombre de cartes réseau dépend de la taille que vous spécifiez pour la machine virtuelle cible. Vérifier les [caractéristiques de taille de la machine virtuelle](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) pour le nombre de cartes prises en charge par la taille de la machine virtuelle. Si vous modifiez la taille pour une machine virtuelle et enregistrez les paramètres, le numéro de carte réseau changent lorsque la prochaine ouverture de page **configurer** . Le nombre de cartes réseau des machines virtuelles cible est le nombre minimal de cartes réseau sur un ordinateur virtuel source et le nombre maximal de cartes réseau pris en charge par la taille de la machine virtuelle choisie, comme suit :

    - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
    - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé pour la taille de la cible avant la taille maximale des cible sera utilisée.
    - Par exemple, si une machine source possède deux cartes réseau et la taille de l’ordinateur cible prend en charge quatre, l’ordinateur cible aura deux cartes. Si l’ordinateur source possède deux cartes, mais la taille cible pris en charge n’accepte qu’une l’ordinateur cible aura qu’une carte.    

- **Réseau de la machine virtuelle cible** - le réseau auquel la machine virtuelle se connecte est déterminé par mappage réseau du réseau de l’ordinateur virtuel source. Si l’ordinateur virtuel source contient plus d’une carte réseau et réseaux source sont mappés à différents réseaux cible, vous devez choisir entre un des réseaux cible.
- **Sous réseau de chaque carte réseau** - pour chaque carte que vous pouvez sélectionner le sous-réseau auquel réseau l’échec sur machine virtuelle connecte à.
- **Adresse IP cible** - si la carte réseau de machine virtuelle source est configurée pour utiliser une adresse IP statique, vous pouvez fournir l’adresse IP de la machine virtuelle cible. Utilisez cette fonctionnalité conserver l’adresse IP d’un ordinateur virtuel source après un basculement. Si aucune adresse IP n’est fourni n’importe quelle adresse IP disponible est donné à la carte réseau au moment de basculement. Si l’adresse IP cible est spécifié mais est déjà utilisé par un autre ordinateur virtuel en cours d’exécution dans Azure basculement échouera.  

    ![Modifier les propriétés de réseau](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] Machines virtuelles de Linux avec adresse IP statique ne sont pas pris en charge.

## <a name="test-the-deployment"></a>Tester le déploiement

Pour tester votre déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle, ou créer un plan de récupération constituée de plusieurs machines virtuelles et exécuter un basculement de test pour le plan.  

Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez que :

- Si vous voulez vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance après le basculement, activer connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le basculement de test.
- Après le basculement vous allez utiliser une adresse IP publique pour vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance. Si vous voulez faire, assurez-vous que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.

>[AZURE.NOTE] Pour optimiser les performances lorsque vous effectuez un basculement vers Azure, vérifiez que vous avez installé l’Agent Azure sur l’ordinateur protégé. Cela permet de démarrage plus rapide et permet également de diagnostic en cas de problèmes. Agent de Linux peut être trouvé [ici](https://github.com/Azure/WALinuxAgent) - et Windows agent sont accessibles [ici](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Sous l’onglet **Récupération Plans** , ajoutez une nouvelle offre. Spécifiez un nom, **VMM** dans **type de Source**, et le serveur VMM source dans la **Source**, la cible sera Azure.

    ![Créer le plan de récupération](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. Dans la page **Sélectionner des Machines virtuelles** , sélectionnez machines virtuelles à ajouter au plan de récupération. Ces machines virtuelles sont ajoutés au groupe par défaut de plan de récupération, groupe 1. Un maximum de 100 machines virtuelles dans un plan de récupération unique ont été testés.

- Si vous souhaitez vérifier les propriétés de la machine virtuelle avant de les ajouter à l’offre, cliquez sur l’ordinateur virtuel dans la page Propriétés du cloud dans lequel il se trouve. Vous pouvez également configurer les propriétés de la machine virtuelle dans la console.
- Toutes les machines virtuelles qui s’affichent ont été activés pour la protection. La liste comprend les deux machines virtuelles qui sont activés pour la protection et la réplication initiale est terminée, et ceux qui sont activés pour la protection avec initiale réplication en attente. Uniquement les machines virtuelles avec réplication initiale terminée peut basculer dans le cadre d’un plan de récupération.

    ![Créer le plan de récupération](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Une fois un plan de récupération a été créé, il apparaît dans l’onglet **Plans de récupération** . Vous pouvez également ajouter des [procédures opérationnelles automation Azure](site-recovery-runbook-automation.md) pour le plan de récupération pour automatiser des actions pendant le basculement.

### <a name="run-a-test-failover"></a>Exécuter un basculement de test

Il existe deux manières d’exécuter un basculement test vers Azure.

- **Tester le basculement sans un réseau Azure**— ce type de basculement test vérifie que la machine virtuelle s’affiche correctement dans Azure. La machine virtuelle ne va pas connectée à un réseau Azure après le basculement.
- **Tester le basculement avec un réseau Azure**— ce type de basculement vérifie que l’environnement de réplication dans son intégralité est fourni comme prévu et qui ont basculé machines virtuelles sera connecté à la cible spécifiée Azure réseau. Pour la gestion de sous-réseau, pour test basculement le sous-réseau de la machine virtuelle test sera être maîtrisez en fonction du sous-réseau de la machine virtuelle réplica. Ceci est différent de réplication régulières lorsque le sous-réseau d’une machine virtuelle réplica est basé sur le sous-réseau de l’ordinateur virtuel source.

Si vous souhaitez exécuter un basculement de test pour une machine virtuelle activé pour une protection à Azure sans spécifier un réseau cible Azure vous n’avez pas besoin préparer rien. Pour exécuter un basculement de test avec une réseau Azure cible, vous devez créer un nouveau réseau Azure a isolé à partir de votre réseau de production Azure (le comportement par défaut lorsque vous créez un nouveau réseau dans Azure). Observez comment [exécuter un basculement test](site-recovery-failover.md#run-a-test-failover) pour plus d’informations.


Vous devez également configurer l’infrastructure de la machine virtuelle répliquée fonctionne comme prévu. Par exemple, une machine virtuelle avec contrôleur de domaine et DNS peuvent être répliquée vers Azure à l’aide de la récupération de Site Azure et peuvent être créée dans le réseau de test à l’aide de tester le basculement. Observez la section [tester les considérations relatives à basculement pour active directory](site-recovery-active-directory.md#considerations-for-test-failover) pour plus d’informations.

Pour exécuter un test, procédez basculement ce qui suit :

1. Sous l’onglet **Plans de** , sélectionnez le plan, puis cliquez sur **Test basculement**.
2. Dans la page **Confirmer le basculement Test** sélectionnez **Aucune** ou sur un réseau Azure spécifique.  Notez que si vous sélectionnez aucun le basculement test doit vérifier que la machine virtuelle répliquées correctement sur Azure mais ne vérifie pas la configuration de votre réseau réplication.

    ![Aucun réseau](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Si le chiffrement des données est activé pour le cloud, dans la **Clé de chiffrement** , sélectionnez le certificat qui a été émis lors de l’installation du fournisseur sur le serveur VMM, lorsque vous avez activé l’option Activer le chiffrement des données pour un nuage.
4. Sous l’onglet **tâches** , vous pouvez suivre l’avancement de basculement. Vous devez également être en mesure de voir le réplica de test machine virtuelle dans le portail Azure. Si vous êtes configuré pour machines virtuelles access à partir de votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel.
5. Lorsque le basculement atteint la phase de **test terminé** , cliquez sur **Tester terminé** pour terminer le basculement de test. Vous pouvez extraire vers le bas jusqu'à l’onglet **tâche** pour suivre l’état et l’avancement de basculement et effectuer toutes les actions qui sont nécessaires.
6. Après le basculement vous pourrez voir du réplica de test machine virtuelle dans le portail Azure. Si vous êtes configuré pour machines virtuelles access à partir de votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel. Procédez comme suit :

    1. Vérifiez que les ordinateurs virtuels démarrer correctement.
    2. Si vous voulez vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance après le basculement, activer connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le basculement de test. Vous devez également ajouter un point de terminaison RDP sur l’ordinateur virtuel. Vous pouvez exploiter un [Azure Automation procédures opérationnelles](site-recovery-runbook-automation.md) pour ce faire.
    3. Après basculement si vous utilisez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance, assurez-vous que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.

7.  Une fois le test terminée, procédez comme suit :
    - Cliquez sur **le basculement de test est terminé**. Nettoyer l’environnement de test pour power désactiver et supprimer les machines virtuelles test automatiquement.
    - Cliquez sur **Notes** pour enregistrer et enregistrer des observations associées le basculement de test.

>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [la configuration de plans de récupération](site-recovery-create-recovery-plans.md) et le [basculement](site-recovery-failover.md).
