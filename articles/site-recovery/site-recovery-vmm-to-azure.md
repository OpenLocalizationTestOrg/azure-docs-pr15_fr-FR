<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM sur Azure à l’aide de la récupération de Site du portail Azure | Microsoft Azure"
    description="Décrit comment déployer récupération de Site Azure pour organiser réplication, le basculement et récupération machines virtuelles Hyper-V en nuages VMM à Azure à l’aide du portail Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Répliquer machines virtuelles de Hyper-V dans nuages VMM sur Azure à l’aide de la récupération de Site Azure grâce au portail Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-azure.md)
- [Azure classique](site-recovery-vmm-to-azure-classic.md)
- [Gestionnaire de ressources PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell classique](site-recovery-deploy-with-powershell.md)

Bienvenue dans récupération de Site Azure ! Utilisation de cet article si vous souhaitez répliquer machines virtuelles de Hyper-V local géré dans nuages System Center Virtual Machine Manager (VMM) pour Azure à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE]Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model
> ) pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.


Azure récupération de Site dans le portail Azure fournit plusieurs nouvelles fonctionnalités :

- Dans le portail Azure les services Azure sauvegarde et restauration de Site Azure sont combinées dans un archivage sécurisé aux Services de récupération unique, afin que vous pouvez configurer et gérer continuité des activités et récupération d’urgence (BCDR) à partir d’un emplacement unique. Un tableau de bord unifiée vous permet de surveillance et de gérer les opérations sur vos sites en local et le nuage public Azure.
- Opérations de récupération de Site dans le portail Azure peuvent maintenant gérer les utilisateurs disposant d’un abonnement Azure mis en service avec le programme Cloud Solution fournisseur (fournisseur).
- Récupération de site dans le portail Azure pouvez répliquer machines aux comptes de stockage Azure le Gestionnaire de ressources. À basculement, récupération de Site crée le Gestionnaire de ressources sur des ordinateurs virtuels dans Azure.
- Récupération de site continue à prendre en charge les comptes de stockage classique de la réplication. Lors du basculement, récupération de Site crée des machines virtuelles à l’aide du modèle classique.


Après la lecture de cet article, publiez vos commentaires en bas dans les commentaires Disqus. Poser des questions techniques sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR doit conserver les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail restent disponibles en permanence cas d’urgence.

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en orchestrant ces opérations réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure), ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous basculer vers l’emplacement secondaire afin que les applications et les charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations que vous devez répliquer locaux machines virtuelles Hyper-V dans nuages VMM à Azure. Il inclut une présentation de l’architecture, planification des informations et des étapes de déploiement de configuration Azure, sur les serveurs locaux, les paramètres de réplication et planification de la capacité. Une fois que vous avez configuré l’infrastructure que vous pouvez activer la réplication sur les ordinateurs que vous voulez protéger et vérifier que le basculement fonctionne.


## <a name="business-advantages"></a>Avantages de l’entreprise

- Récupération de site offre une protection hors site pour les charges de travail métier et applications qui s’exécutent sur les ordinateurs virtuels Hyper-V.
- Le portail de Services de récupération fournit un emplacement unique pour configurer, gérer et surveiller réplication, le basculement et récupération.
- Vous pouvez facilement exécuter basculement à partir de votre infrastructure en local pour Azure et de restauration (restauration) à partir d’Azure aux serveurs hôtes Hyper-V dans votre site local.
- Vous pouvez configurer les plans de récupération avec plusieurs ordinateurs afin que les charges de travail hiérarchisé d’applications basculent entre eux.


## <a name="scenario-architecture"></a>Architecture de scénario


Voici les composants du scénario :

- **Serveur VMM**: un serveur VMM en local avec un ou plusieurs nuages.
- **Hôte Hyper-V ou cluster**: serveurs hôtes Hyper-V ou groupes géré dans nuages VMM.
- **Fournisseur de récupération de Site Azure et agent de récupération de services**: au cours du déploiement vous installez le fournisseur de récupération de Site Azure sur le serveur VMM et l’agent de Services de récupération de Microsoft Azure sur vos serveurs hôtes Hyper-V. Le fournisseur sur le serveur VMM communique avec récupération de Site via HTTPS 443 répliquer orchestration. L’agent sur le serveur hôte Hyper-V réplique les données vers le stockage Azure sur HTTPS 443 par défaut.
- **Azure**: vous devez un abonnement Azure, un compte de stockage Azure stockage répliquée des données et un réseau virtuel Azure afin que les ordinateurs virtuels Azure sont connectés à un réseau après le basculement.

![Topologie E2A](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure pour déployer ce scénario.

**Conditions préalables** | **Plus d’informations**
--- | ---
**Compte Azure**| Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**Stockage Azure** | Vous avez besoin d’un compte de stockage Azure standard pour stocker les données dupliquées. Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résistants si une panne régionale se produit ou si la région primaire ne peuvent pas être récupérée. [En savoir plus](../storage/storage-redundancy.md). Le compte doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.<br/><br/>Stockage Premium n’est pas pris en charge.<br/><br/> Données répliquées sont stockées dans le stockage Azure et Azure machines virtuelles sont créées lors du basculement. <br/><br/> [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous avez besoin d’un réseau virtuel Azure Azure machines virtuelles connectés à basculement. Le réseau doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.

## <a name="on-premises-prerequisites"></a>Conditions préalables pour les locaux

Voici ce que vous devez en local

**Conditions préalables** | **Plus d’informations**
--- | ---
**VMM**| Un ou plusieurs serveurs VMM s’exécutant sur System Center 2012 R2. Chaque serveur VMM doit avoir un ou plusieurs nuages configurés. Un nuage doit contenir :<br/><br/> Un ou plusieurs groupes hôtes VMM.<br/><br/> Un ou plusieurs serveurs hôtes Hyper-V ou groupes dans chaque groupe hôte.<br/><br/>[En savoir plus](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sur la configuration de nuages VMM.
**Hyper-V** | Serveurs hôte Hyper-V doivent exécuter au minimum **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2** et ont installé les dernières mises à jour.<br/><br/> Un serveur Hyper-V doit contenir un ou plusieurs ordinateurs virtuels.<br/><br/> Un serveur hôte Hyper-V ou un cluster incluant des machines virtuelles que vous souhaitez répliquer doit être géré dans un nuage VMM.<br/><br/>Serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br/><br/>Serveurs Hyper-V doivent avoir correctifs mentionnées dans l’article [2961977](https://support.microsoft.com/kb/2961977) installé.<br/><br/>Serveurs hôte Hyper-V doivent accès à internet pour la réplication de données à Azure.
**Fournisseur et agent** | Pendant le déploiement d’Azure Site récupération vous devez installer le fournisseur de récupération de Site Azure sur le serveur VMM et l’agent de Services de récupération sur hôtes Hyper-V. Le fournisseur et l’agent vous devez vous connecter à Azure via internet directement ou via un proxy. Notez qu’un proxy basée sur HTTPS n’est pas pris en charge. Le serveur proxy sur le serveur VMM et hôtes Hyper-V doit autoriser l’accès à : <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur VMM, vérifiez que les règles permettent communications vers Azure. Vous devez autoriser les [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/>Autoriser les plages d’adresses IP pour la région Azure de votre abonnement et pour les États-Unis Ouest.<br/><br/>De plus,. le serveur proxy sur le serveur VMM a besoin d’accéder à https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Ordinateur protégé conditions préalables


**Conditions préalables** | **Plus d’informations**
--- | ---
**Machines virtuelles protégées** | Avant de basculer sur un ordinateur virtuel, vérifiez que le nom qui est affecté à la machine virtuelle Azure est conforme aux [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Vous pouvez modifier le nom après avoir activé la réplication de la machine virtuelle. <br/><br/> Capacité disque individuel sur machines protégées ne doivent pas être plus de 1023 go. Une machine virtuelle peut avoir jusqu'à 16 disques (et donc 16 To maximum).<br/><br/> Partagé invité disque clusters ne sont pas pris en charge.<br/><br/> Unifiée microprogramme UEFI (Extensible Interface) / démarrage Extensible Interface(EFI) microprogramme n’est pas pris en charge.<br/><br/> Si la source machine virtuelle a cartes, il est converti en une seule carte réseau après le basculement vers Azure.<br/><br/>Protection des machines virtuelles exécutant Linux avec une adresse IP statique n’est pas pris en charge.

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour préparer pour le déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) dans laquelle machines virtuelles Azure se trouve après le basculement.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données dupliquées.
4. [Préparer le serveur VMM](#prepare-the-vmm-server) pour le déploiement de récupération de Site.
5. [Préparer pour le mappage réseau](#prepare-for-network-mapping). Configurer des réseaux afin que vous pouvez configurer le mappage réseau lors du déploiement de récupération de Site.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Vous avez besoin d’un réseau Azure afin que les ordinateurs virtuels Azure créées après que basculement doit se connecter.

- Le réseau doit être dans la même région que celui dans lequel vous déployez l’archivage sécurisé aux Services de récupération.
- Selon le modèle de ressources que vous souhaitez utiliser pour basculé machines virtuelles Azure, vous devez configurer le réseau Azure en [mode directeur des ressources](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [en mode classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Nous vous recommandons de que configurer une connexion réseau avant de commencer. Si vous n’avez pas, vous devez effectuer au cours du déploiement de récupération de Site.

> [AZURE.NOTE] [Migration des réseaux](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de récupération de Site.


### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous avez besoin d’un compte de stockage Azure standard pour stocker les données répliquées vers Azure. Le compte doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.
- Selon le modèle de ressources que vous souhaitez utiliser pour basculé machines virtuelles Azure, vous configurez un compte en [mode directeur des ressources](../storage/storage-create-storage-account.md) ou [en mode classique](../storage/storage-create-storage-account-classic-portal.md).
- Nous vous recommandons de configurer un compte avant de commencer. Si vous n’avez pas, vous devez effectuer au cours du déploiement de récupération de Site.

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

### <a name="prepare-the-vmm-server"></a>Préparer le serveur VMM

- Vérifiez que le serveur VMM est conforme à la [Configuration requise](#on-premises-prerequisites).
- Pendant le déploiement de récupération de Site, vous pouvez spécifier que tous les nuages sur un serveur VMM doivent être disponibles dans le portail Azure. Si vous voulez uniquement nuages spécifiques apparaisse dans le portail, vous pouvez activer ce paramètre sur le cloud dans la console d’administration.


### <a name="prepare-for-network-mapping"></a>Préparer pour le mappage de réseau

Vous devez configurer le mappage réseau lors du déploiement de récupération de Site. Le mappage réseau mappe entre source VMM VM réseaux et cibler Azure réseaux pour activer ce qui suit :

- Machines basculent sur le même réseau peuvent se connecter à d’autres, même si elles ne vous pas basculés de la même façon ou dans le même plan de récupération.
- Si une passerelle réseau a été configurée sur la cible du réseau Azure, Azure machines virtuelles pouvez vous connecter à des machines virtuelles en local.
- Pour configurer réseau mappage ici est ce que vous devez préparer :

    - Assurez-vous que machines virtuelles sur le serveur source Hyper-V hôte sont connectés à un réseau VMM VM. Ce réseau doit être lié à un réseau logique est associé avec le cloud.
    - Un réseau Azure comme décrit [ci-dessus.](#set-up-an-azure-network)

- [En savoir plus](site-recovery-network-mapping.md) sur le fonctionne de mappage réseau.


## <a name="create-a-recovery-services-vault"></a>Créer un archivage sécurisé aux Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **Services de récupération**. Vous pouvez également cliquer sur **Parcourir** > **Services de récupération de** chambres fortes > **Ajouter**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. Dans la zone **nom**, attribuez un nom convivial pour identifier l’archivage sécurisé. Si vous avez plusieurs abonnements, sélectionnez un d’eux.
4. [Créer un groupe de ressources](../resource-group-template-deploy-portal.md), ou sélectionnez-en un. Spécifier une région Azure. Machines seront répliquées à cette zone. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à l’archivage sécurisé du tableau de bord, cliquez sur **Ajouter au tableau de bord** > **créer l’archivage sécurisé**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

L’archivage sécurisé nouveau apparaît dans le **tableau de bord** > **toutes les ressources**et sur le principaux **Services de récupération de chambres fortes** carte.

## <a name="getting-started"></a>Prise en main

Récupération de site fournit une mise en route expérience qui vous aident à déployer aussi rapidement que possible. Mise en route vérifie les conditions préalables et vous guide tout au long de récupération de Site déploiement les étapes dans l’ordre approprié.

Dans mise en route vous sélectionnez le type d’ordinateurs que vous souhaitez répliquer et l’endroit où vous souhaitez répliquer à. Vous configurez serveurs locaux, les comptes de stockage Azure et les réseaux. Vous créez des règles de réplication et planifier la capacité. Une fois que vous avez configuré votre infrastructure vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter basculement pour les ordinateurs spécifiques, ou créer des plans de récupération à basculer sur plusieurs ordinateurs.

Commencer mise en route en choisissant la manière dont vous souhaitez déployer récupération de Site. Le flux de mise en route change légèrement selon vos besoins en matière de réplication.



## <a name="step-1-choose-your-protection-goals"></a>Étape 1 : Choisir vos objectifs de protection

Sélectionnez ce que vous voulez répliquer et l’endroit où vous souhaitez répliquer à.

1. Dans la carte de **Services de récupération de chambres fortes** , sélectionnez votre l’archivage sécurisé et cliquez sur **paramètres**.
2. Dans **Mise en route** , cliquez sur **Récupération de Site** > **étape 1 : préparer une Infrastructure** > **objectif de Protection**.

    ![Sélectionnez des objectifs](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. Dans **l’objectif de Protection** sélectionnez **Sur Azure**, puis sélectionnez **Oui, avec Hyper-V**. Sélectionnez **Oui** pour confirmer que vous utilisez VMM pour gérer les hôtes Hyper-V et le site de récupération. Cliquez ensuite sur **OK**.

    ![Sélectionnez des objectifs](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Installez le fournisseur de récupération Azure Site sur le serveur VMM et inscrire le serveur dans l’archivage sécurisé. Installer l’agent de Services de récupération Azure sur hôtes Hyper-V.

1. Cliquez sur **étape 2 : préparer Infrastructure** > **Source**.

    ![Configurer la source](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Dans **préparer source** cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Dans la vérification de carte **Ajouter un serveur** qui **System Center VMM serveur** apparaît dans le **type de serveur** et que le serveur VMM répond aux [conditions préalables et configuration de l’URL](#on-premises-prerequisites).
4. Téléchargez le fichier d’installation fournisseur de récupération de Site Azure.
5. Téléchargez la clé d’inscription. Vous devez ceci lorsque vous exécutez le programme d’installation. La clé est valide pour cinq jours après que vous générez.

    ![Configurer la source](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Installer le fournisseur de récupération de Site Azure sur le serveur VMM.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurer le fournisseur de récupération de Site Azure

1.  Exécutez le fournisseur de fichier d’installation.
2. Dans **Microsoft Update,** vous pouvez choisir dans les mises à jour afin que les mises à jour du fournisseur sont installés conformément à votre stratégie Microsoft Update.
3. Dans **Installation**, acceptez ou modifier l’emplacement d’installation de fournisseur par défaut et cliquez sur **installer**.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-azure/provider2.png)

4. Une fois l’installation cliquez sur **Enregistrer** pour enregistrer le serveur VMM dans l’archivage sécurisé.
5. Dans la page **Paramètres de l’archivage sécurisé** , cliquez sur **Parcourir** pour sélectionner le fichier de clé de l’archivage sécurisé. Spécifier l’abonnement Azure Site récupération et le nom de l’archivage sécurisé.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. Dans **Connexion Internet**, spécifiez comment le fournisseur en cours d’exécution sur le serveur VMM se connecteront aux récupération de Site via internet.

    - Si vous souhaitez que le fournisseur de se connecter directement, sélectionnez **se connecter directement à la récupération de Site Azure sans proxy**.
    - Si votre proxy existant requiert une authentification, ou que vous souhaitez utiliser une instruction select proxy personnalisé **se connecter à la récupération de Site Azure en utilisant un serveur proxy**.
    - Si vous utilisez un proxy personnalisé, spécifiez l’adresse, les ports et les informations d’identification.
    - Si vous utilisez un proxy vous avez devez déjà autorisé les URL décrites dans les [conditions préalables](#on-premises-prerequisites).
    - Si vous utilisez un proxy personnalisé qu'un compte RunAs VMM (DRAProxyAccount) est créé automatiquement en utilisant les informations d’identification du proxy spécifié. Configurer le serveur proxy de sorte que ce compte peut s’authentifier avec succès. Les paramètres de compte RunAs VMM peuvent être modifiés dans la console. Dans **paramètres**, développez **sécurité** > **Exécuter en tant que comptes**, puis modifiez le mot de passe pour DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Accepter ou modifier l’emplacement d’un certificat SSL qui est généré automatiquement pour le chiffrement des données. Ce certificat est utilisé si vous activez le chiffrement des données pour un nuage protégé par Azure dans le portail de récupération de Site Azure. Protégez ce certificat. Lorsque vous exécutez un basculement Azure vous en aurez besoin pour déchiffrer, si le chiffrement des données est activé.


8. Dans la zone **nom du serveur**, spécifiez un nom convivial pour identifier le serveur VMM dans l’archivage sécurisé. Dans une configuration cluster, spécifiez le nom du rôle cluster VMM.
9. Activer la **synchronisation cloud métadonnées** si vous voulez synchroniser les métadonnées pour tous les nuages sur le serveur VMM avec l’archivage sécurisé. Cette action ne doit se produire une fois sur chaque serveur. Si vous ne voulez pas synchroniser tous les nuages, vous pouvez laisser ce paramètre désactivé et synchroniser chaque cloud individuellement dans les propriétés du cloud dans la console. Cliquez sur **Enregistrer** pour terminer le processus.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Enregistrement au début. Une fois l’enregistrement terminé, le serveur est affiché dans les **paramètres** > **serveurs** carte dans l’archivage sécurisé.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Installation de ligne de commande pour le fournisseur de récupération de Site Azure

Le fournisseur de récupération de Site Azure peuvent être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur Server Core de Windows Server 2012 R2.

1. Téléchargez la clé de fichier et d’enregistrement de l’installation fournisseur dans un dossier. Par exemple, C:\ASR.
2. À partir d’une invite de commandes avec élévation de privilèges, exécutez ces commandes pour extraire le programme d’installation de fournisseur :

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Exécutez la commande suivante pour installer les composants :

            C:\ASR> setupdr.exe /i

4. Puis exécutez ces commandes pour enregistrer le serveur dans l’archivage sécurisé :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Dans cet exemple :

- **/Credentials**: paramètre obligatoire qui spécifie où se trouve le fichier de clé d’inscription.  
- **/FriendlyName**: paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
- - **/EncryptionEnabled**: paramètre facultatif lorsque vous êtes réplication machines virtuelles Hyper-V dans VMM nuages à Azure. Spécifiez si vous voulez chiffrer machines virtuelles dans Azure (en chiffrement reste). Assurez-vous que le nom du fichier a une extension **.pfx** . Chiffrement est désactivée par défaut.
- **/ProxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy.
- **/ProxyPort**: paramètre facultatif qui spécifie le port du serveur proxy.
- **/proxyUsername**: paramètre facultatif qui indique le nom d’utilisateur proxy (si proxy requiert une authentification).
- **/proxyPassword**: paramètre facultatif qui spécifie le mot de passe pour vous authentifier avec le serveur proxy (si le serveur proxy requiert une authentification).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installer l’agent de Services de récupération Azure sur hôtes Hyper-V

1. Une fois que vous avez configuré le fournisseur, vous devez télécharger le fichier d’installation de l’agent de Services de récupération Azure. Exécutez le programme d’installation sur chaque serveur Hyper-V dans le nuage VMM.

    ![Sites Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Dans la page **Prerequisites Check** , cliquez sur **suivant**. Les composants requis manquants seront automatiquement installés.

    ![Agent de Services de récupération de conditions préalables](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. Dans la page **Paramètres de l’Installation** , accepter ou modifier l’emplacement d’installation et l’emplacement du cache. Vous pouvez configurer le cache sur un lecteur qui comporte au moins 5 Go de stockage disponible, mais nous vous recommandons d’un lecteur de cache au moins 600 Go d’espace libre. Cliquez sur **installer**.
4. Une fois l’installation terminée, cliquez sur **Fermer** pour terminer.

    ![Inscrire Agent de MARS](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Installation de ligne de commande pour l’agent de Services de récupération de Site Azure

Vous pouvez installer l’Agent de Services Microsoft Azure récupération à partir de la ligne de commande à l’aide de la commande suivante :

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configurer l’accès proxy internet à la récupération de Site à partir des hôtes Hyper-V

L’agent de Services de récupération exécuté sur hôtes Hyper-V nécessite un accès internet vers Azure pour la réplication de la machine virtuelle. Si vous avez accès à internet via un proxy, configurez-la comme suit :

1. Ouvrez le composant logiciel enfichable Microsoft Azure sauvegarde MMC sur l’hôte Hyper-V. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sous l’onglet **Configuration du serveur Proxy** , spécifiez les informations du serveur proxy.

    ![Inscrire Agent de MARS](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Assurez-vous que l’agent de communiquer avec les URL décrites dans les [conditions préalables](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Configurer l’environnement cible

Spécifier le compte de stockage Azure à utiliser pour la réplication et le réseau Azure auquel machines virtuelles Azure va se connecter après le basculement.

1.  Cliquez sur **préparer infrastructure** > **cible** et sélectionnez l’abonnement Azure que vous voulez utiliser.
2.  Spécifiez le modèle de déploiement que vous voulez utiliser pour les machines virtuelles après le basculement.
3.  Récupération de site vérifie que vous avez un ou plusieurs comptes de stockage Azure compatible et réseaux.

    ![Espace de stockage](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Si vous n’avez pas créé un compte de stockage et que vous voulez créer un à l’aide du Gestionnaire de ressources, cliquez sur **+ compte de stockage** pour effectuer cette inline.  Sur la carte de **créer un compte de stockage** , spécifiez un nom de compte, type, abonnement et emplacement. Le compte doit être dans le même emplacement que l’archivage sécurisé aux Services de récupération.

    ![Espace de stockage](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Notez que :

    - Si vous voulez créer un compte de stockage à l’aide du modèle classique, vous faire dans le portail Azure. [Pour en savoir plus](../storage/storage-create-storage-account-classic-portal.md)
    - Si vous utilisez un compte de stockage premium pour les données répliquées, vous devez configurer un compte espace de stockage supplémentaire standard pour stocker les journaux de réplication capturer les modifications en cours apportées aux données locales.

4.  Si vous n’avez pas créé un réseau Azure et que vous voulez créer un à l’aide du Gestionnaire de ressources cliquez sur **+ réseau** pour effectuer cette inline. Sur la carte **réseau virtuel créer** spécifiez un nom de réseau, plage d’adresses, détails du sous-réseau, abonnement et un emplacement. Le réseau doit être dans le même emplacement que l’archivage sécurisé aux Services de récupération.

    ![Réseau](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Si vous voulez créer un réseau à l’aide du modèle classique vous ferez que dans le portail Azure. [En savoir plus](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurer le mappage de réseau

- [Lire](#prepare-for-network-mapping) un bref aperçu des quels mappage réseau effectue. [Lisez ce](site-recovery-network-mapping.md) pour une explication plus approfondie.
- Vérifiez que les machines virtuelles sur le serveur VMM sont connectés à un réseau machine virtuelle et que vous avez créé au moins un réseau virtuel Azure. Plusieurs réseaux machine virtuelle peuvent être mappées à un seul réseau Azure.

Configurer le mappage comme suit :

1. Dans **paramètres** > **Infrastructure de récupération de Site** > **mappages réseau** > **Mappage du réseau**, cliquez sur l’icône **+ mappage du réseau** .

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Sur le **mappage réseau ajouter** sélectionnez le serveur VMM source et **Azure** comme cible.
3. Vérifiez que l’abonnement et le modèle de déploiement après le basculement.
4. Dans le **réseau Source**, sélectionnez le réseau de machine virtuelle locale source que vous voulez mapper à partir de la liste associée au serveur VMM.
5. Dans le **réseau cible**, sélectionnez le réseau Azure dans quel réplica machines virtuelles Azure seront situés lorsqu’elles sont créées. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Voici que se passe-t-il lorsque commence le mappage réseau :

- Machines virtuelles existantes sur le réseau de machine virtuelle source sont connectés au réseau cible lors de la mise en correspondance commence. Nouvelles machines virtuelles connectés au réseau machine virtuelle source sont connectés au réseau Azure mappé lors de la réplication se produit.
- Si vous modifiez un mappage réseau existant, vous serez connectés machines virtuelles réplica avec les nouveaux paramètres.
- Si le réseau cible comporte plusieurs sous réseaux de ces sous-réseaux ait le même nom que sous-réseau sur lequel se trouve l’ordinateur virtuel source, la machine virtuelle réplica est connectée à ce sous-réseau cible après le basculement.
- S’il n’existe aucun sous-réseau cible ayant un nom correspondant, la machine virtuelle est connectée au premier sous-réseau dans le réseau.



## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Configurer les paramètres de réplication


1. Pour créer une nouvelle stratégie de réplication, cliquez sur **préparer infrastructure** > **Paramètres de réplication** > **+ créer et associer**.

    ![Réseau](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. Dans **créer et stratégie associer**, spécifiez un nom de la stratégie.
3. Dans la zone **fréquence de copie**, spécifiez la fréquence à laquelle vous souhaitez répliquer données delta après la réplication initiale (toutes les 30 secondes, 5 ou 15 minutes).
4. Dans la **rétention des points de récupération**, spécifiez heures combien de temps la fenêtre de rétention seront pour chaque point de récupération. Machines protégées peuvent être récupérés à tout moment dans une fenêtre.
6. Dans **fréquence instantané cohérente à l’échelle de l’application**, indiquez la fréquence (1 à 12 heures) contenant des instantanés cohérents avec les applications de points de récupération sera créé. Hyper-V utilise deux types d’instantanés — une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérentes avec les applications qui prend un instantané de données de l’application à l’intérieur de la machine virtuelle. Instantanés cohérents avec les applications permet de s’assurer que les applications sont dans un état cohérent lorsque l’instantané Volume Shadow Copy Service (VSS). Notez que si vous activez les instantanés cohérents avec les applications, il affectent les performances des applications qui s’exécutent sur des machines virtuelles source. Vérifiez que la valeur est inférieure à celle du nombre de points de récupération supplémentaires que vous configurez.
3. **Heure de début de la réplication initiale**, indiquer quand lancer la réplication initiale. La réplication a lieu sur votre bande passante internet afin que vous souhaiterez peut-être planifier en dehors de vos heures occupé (e).
5. Dans **chiffrer les données que qui se trouve sur Azure**, spécifiez si vous voulez chiffrer en données reste dans le stockage Azure. Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Lorsque vous créez une nouvelle stratégie il a automatiquement associé le nuage VMM. Cliquez sur **OK**. Vous pouvez associer supplémentaires nuages VMM (et les ordinateurs virtuels qu’ils) à cette stratégie de réplication dans **les paramètres** > **réplication** > nom de la stratégie > **Associer VMM Cloud**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configurer vous pouvez envisager de planification de la capacité et déterminer si vous avez besoin des ressources supplémentaires.

Récupération de site fournit un planificateur de capacité pour vous aider à allouer les ressources de votre environnement source, composants de récupération de site, mise en réseau et de stockage. Vous pouvez exécuter le planificateur en mode rapide pour estimations selon un nombre moyen de machines virtuelles, des disques et stockage ou en mode détaillé dans laquelle vous allez saisir les chiffres au niveau de la charge de travail. Avant de commencer, vous devez à :

- Collecter les informations relatives à votre environnement de réplication, y compris les machines virtuelles, disques par machines virtuelles et stockage par disque.
- Estimer le taux de modification (évolution du) quotidien qu'avoir pour les données répliquées. Vous pouvez utiliser le [Planificateur de capacité réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour vous aider à procédez comme suit.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lisez l’article intitulé](site-recovery-capacity-planner.md) qui accompagne l’outil.
2.  Lorsque vous avez terminé sélectionnez **Oui** dans **que vous avez exécuté le Planificateur de capacités**?

    ![Planification de la capacité](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considérations relatives à la bande passante réseau

Vous pouvez utiliser l’outil de planificateur de capacité pour calculer la bande passante que vous avez besoin de la réplication (réplication initiale, puis delta). Pour contrôler la quantité d’utilisation de la bande passante de la réplication plusieurs options s’offrent à vous :

- **Limiter la bande passante**: le trafic Hyper-V qui réplique sur un site secondaire parcourt un hôte Hyper-V spécifique. Vous pouvez limiter la bande passante sur le serveur hôte.
- **Modifier la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre.

#### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable Microsoft Azure sauvegarde MMC sur le serveur hôte Hyper-V. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sous l’onglet **régulation** sélectionnez **Activer l’utilisation de la bande passante internet la limitation pour les opérations de sauvegarde**et définir les limites de travail et non liés au travail heures. Les plages valides sont compris 512 Kb/s et 102 Mo/s par seconde.

    ![Limiter la bande passante](./media/site-recovery-vmm-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Jeu-OBMachineSetting NoThrottle** indique qu’aucune limitation n’est requise.


#### <a name="influence-network-bandwidth"></a>Influencer la bande passante réseau

La valeur de Registre **UploadThreadsPerVM** détermine le nombre de threads qui sont utilisés pour le transfert de données (réplication initiale ou delta) d’un disque. Une valeur plus élevée augmente la bande passante réseau utilisée pour la réplication. La valeur de Registre **DownloadThreadsPerVM** Spécifie le nombre de threads utilisés pour le transfert de données lors du retour arrière.

1. Dans le Registre atteindre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

    - Modifiez la valeur **UploadThreadsPerVM** (ou créez la clé s’il n’existe) pour contrôler les threads utilisés pour la réplication de disque.
    - Modifiez la valeur **DownloadThreadsPerVM** (ou créez la clé s’il n’existe) pour contrôler les threads utilisés pour le trafic de retour arrière à partir d’Azure.
2. La valeur par défaut est 4. Dans un réseau « overprovisioned », ces clés de Registre par les valeurs par défaut doivent être modifiées. La valeur maximale est 32. Surveiller le trafic afin d’optimiser la valeur.

## <a name="step-6-enable-replication"></a>Étape 6 : Réplication d’activer

Maintenant, activez la réplication comme suit :

1. Cliquez sur **étape 2 : répliquer application** > **Source**. Après avoir activé la réplication pour la première fois que vous cliquez sur **+ répliquer** dans l’archivage sécurisé pour activer la réplication pour d’autres ordinateurs.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. Dans la carte **Source** > Sélectionner le serveur VMM et le cloud dans lequel se trouvent les hôtes Hyper-V. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. Dans **cible** , sélectionnez l’abonnement, modèle de déploiement post-basculement et du compte de stockage que vous utilisez pour les données répliquées.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous souhaitez utiliser un compte de stockage autre que celles vous devez vous pouvez [en créer un](#set-up-an-azure-storage-account). Pour créer un espace de stockage compte à l’aide du modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous voulez créer un compte de stockage à l’aide du modèle classique, vous effectuez cette [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md). Cliquez ensuite sur **OK**.
5. Sélectionnez le réseau Azure et sous-réseau auquel machines virtuelles Azure va se connecter lorsqu’ils êtes lancés après le basculement. Cliquez sur **configurer maintenant pour les ordinateurs sélectionnés** pour appliquer le paramètre de réseau à tous les ordinateurs que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau Azure par ordinateur. Si vous voulez utiliser un réseau différent de ceux vous avez vous pouvez [en créer un](#set-up-an-azure-network). Pour créer une connexion réseau à l’aide du modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous voulez créer un réseau à l’aide du modèle classique vous ferez que [dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Sélectionner un sous-réseau le cas échéant. Cliquez ensuite sur **OK**.
6. Machines **virtuelles** > **Sélectionnez machines virtuelles** cliquez puis sélectionnez chaque ordinateur que vous voulez dupliquer. Vous ne pouvez sélectionner machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. Dans les **Propriétés** > **configurer les propriétés**, sélectionnez le système d’exploitation pour les ordinateurs virtuels sélectionnés et le disque du système d’exploitation. Cliquez ensuite sur **OK**. Vous pouvez définir des propriétés supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. Dans **les paramètres de réplication** > **configurer des paramètres de réplication**, sélectionnez la stratégie de réplication que vous voulez appliquer pour les ordinateurs virtuels protégées. Cliquez ensuite sur **OK**. Vous pouvez modifier la stratégie de réplication dans **les paramètres** > **stratégies de réplication** > nom de la stratégie > **Modifier les paramètres**. Vous appliquez les modifications sont utilisées pour machines sont déjà réplication et nouvelles machines.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Vous pouvez suivre la progression de la tâche **d’Activation de la Protection** dans **les paramètres** > **travaux** > **tâches de récupération de Site**. Après l’exécution de la tâche de **Protection finaliser** l’ordinateur est prêt pour le basculement.

### <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés de la machine virtuelle

Nous vous recommandons de vérifier les propriétés de l’ordinateur source. N’oubliez pas que le nom de la machine virtuelle Azure doit être conformes aux [exigences de machine virtuelle Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Cliquez sur **paramètres** > **Éléments protégé** > **Éléments répliquées** > et sélectionnez l’ordinateur pour afficher les détails.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Dans les **Propriétés** vous pouvez afficher les informations de réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. Dans le **cluster et réseau** > **Calculer propriétés** que vous pouvez spécifier la taille de nom et cibles machine virtuelle Azure. Modifier le nom conformes aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) si vous avez besoin. Vous pouvez également afficher et modifier des informations sur le réseau cible, le sous-réseau et l’adresse IP est affectée à la machine virtuelle Azure. Notez les points suivants :

    - Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse de l’échec de la sur ordinateur utilise DHCP. Si vous définissez une adresse qui n’est pas disponible lors du basculement, le basculement échouera. La même adresse IP cible peut servir pour le basculement de test si l’adresse est disponible dans le réseau de basculement test.
    - Le nombre de cartes réseau est dépend de la taille que vous spécifiez pour la machine virtuelle cible, comme suit :

        - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
        - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé pour la taille cible, la taille maximale des cible est utilisée.
        - Par exemple, si une machine source a deux cartes réseau et la taille de l’ordinateur cible prend en charge quatre, l’ordinateur cible comporte deux cartes. Si l’ordinateur source possède deux cartes, mais la taille cible pris en charge n’accepte qu’une l’ordinateur cible aura qu’une carte.     
        - Si la machine virtuelle possède plusieurs cartes réseau qu’ils seront tous se connecter au même réseau.

    ![Activer la réplication](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  Dans **disques** , vous pouvez voir le système d’exploitation et les données disques sur l’ordinateur virtuel qui sera répliquée.



## <a name="step-7-test-your-deployment"></a>Étape 7 : Tester votre déploiement

Pour tester le déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle ou un plan de récupération qui contient une ou plusieurs machines virtuelles.


### <a name="prepare-for-failover"></a>Préparer pour le basculement

- Pour exécuter un basculement de test, nous vous recommandons de créer un nouveau réseau Azure a isolé à partir de votre réseau de production Azure (c’est le comportement par défaut lorsque vous créez un nouveau réseau dans Azure). [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur l’exécution de basculement test.
- Pour obtenir les meilleures performances lorsque vous basculer vers Azure, installez l’Agent Azure sur l’ordinateur protégé. Il crée démarrage plus rapides et aide à la résolution des problèmes. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Pour tester entièrement votre déploiement, vous avez besoin d’une infrastructure pour la machine répliquée fonctionne comme prévu. Si vous souhaitez tester Active Directory et DNS vous pouvez créer une machine virtuelle comme contrôleur de domaine avec le système DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lisez en autres [Considérations relatives à basculement de test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Si vous souhaitez exécuter un basculement non planifié au lieu de basculement test Notez les points suivants :

    - Si possible, vous devez fermer vers le bas machines principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas la source et la copie machines en cours d’exécution en même temps.
    - Lorsque vous exécutez un basculement non planifié l’arrêt réplication des données à partir d’ordinateurs principales afin de n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence. En outre si vous exécutez un basculement non planifié sur un plan de récupération il s’exécutera jusqu'à la fin, même si une erreur se produit.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer pour vous connecter aux machines virtuelles Azure après le basculement

Si vous voulez vous connecter aux machines virtuelles Azure à l’aide de RDP après le basculement, vérifiez que vous effectuez les opérations suivantes :

**Sur l’ordinateur local avant le basculement**:

- Pour l’accès par le biais d’internet activer RDP, vous assurer que les règles TCP et UDP sont ajoutés pour le **Public**et faire en sorte que RDP est autorisé dans le **Pare-feu Windows** -> **autorisés applications et fonctionnalités** pour tous les profils.
- Pour l’accès via une connexion de site à activer RDP sur l’ordinateur et faire en sorte que RDP est autorisé dans le **Pare-feu Windows** -> **autorisés applications et fonctionnalités** pour les réseaux de **domaine** et **privées** .
- Installez l' [agent Azure machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) sur l’ordinateur local.
- Assurez-vous que la stratégie SAN du système d’exploitation est définie sur OnlineAll. [Pour en savoir plus]( https://support.microsoft.com/kb/3031135)
- Désactiver le service IPSec avant d’exécuter le basculement.

**Sur le Azure machine virtuelle après le basculement**:

- Ajouter un point de terminaison public pour le protocole RDP (Port3389) et spécifiez des informations d’identification pour la connexion.
- Vérifiez que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution. Pour obtenir d’autres conseils de dépannage Lisez cet [article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Si vous voulez accéder à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un Secure Shell client (ssh), procédez comme suit :

**Sur l’ordinateur local avant le basculement**:

- Assurez-vous que le service banque d’informations sécurisé Shell sur l’ordinateur virtuel Azure est défini pour démarrer automatiquement au démarrage du système.
- Vérifiez que les règles de pare-feu permettent une connexion SSH à ce dernier.

**Sur le Azure machine virtuelle après le basculement**:

- Les règles de groupe de sécurité réseau de l’échec de la machine virtuelle et le sous-réseau Azure auquel il est connecté devront autoriser les connexions entrantes à la voie SSH.
- Un point de terminaison public doit être créée pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).
- Si la machine virtuelle est accessible via une connexion VPN (itinéraire Express ou un site à VPN) le client peut être utilisé pour se connecter directement à la machine virtuelle sur SSH.


### <a name="run-a-test-failover"></a>Exécuter un basculement de test

Pour exécuter la ne basculement test ce qui suit :

1. Basculer sur un ordinateur virtuel unique dans **les paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > **+ basculement de Test**.
2. Basculer sur un plan de récupération, dans **les paramètres** > de**Récupération Plans**, avec le bouton droit de l’offre > **Basculement de Test**. Pour créer une récupération planifier [Suivez ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le **Test de basculement** sélectionnez le réseau Azure auquel se connecter machines virtuelles Azure après basculement.
4. Cliquez sur **OK** pour lancer le basculement. Vous pouvez suivre la progression en cliquant sur l’ordinateur virtuel pour ouvrir ses propriétés ou sur le travail de **Test basculement** dans **paramètres** > **tâches de récupération de Site**.
5. Lorsque le basculement atteint la phase de **test achevé** , procédez comme suit :

    1. Afficher la machine virtuelle réplica dans le portail Azure. Vérifiez que la machine virtuelle démarre correctement.
    2. Si vous êtes configuré pour machines virtuelles access à partir de votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel.
    3. Cliquez sur **Terminer le test** pour terminer.
    4. Cliquez sur **Notes** pour enregistrer et enregistrer des observations associées le basculement de test.
    5. Cliquez sur **le basculement de test est terminé**. Nettoyer l’environnement de test pour power désactiver et supprimer la machine virtuelle test automatiquement.
    6. À ce stade tous les éléments ou machines virtuelles créées automatiquement par la récupération de Site pendant le basculement de test sont supprimés. Tous les éléments que vous avez créé pour le basculement de test ne sont pas supprimées.

    > [AZURE.NOTE] Si un basculement test persiste plus de deux semaines qu’elle est terminée en vigueur.

6. Une fois le basculement terminée, vous devriez pouvoir également afficher le réplica Azure machine s’affichent dans le portail Azure > **Machines virtuelles**. Vous devez vous assurer que la machine virtuelle est la taille appropriée, qui s’il est connecté au réseau approprié, et qu’il s’exécute.
7. Si vous [préparé pour les connexions après le basculement](#prepare-to-connect-to-Azure-VMs-after-failover) vous devriez pouvoir se connecter à la machine virtuelle Azure.


## <a name="monitor-your-deployment"></a>Surveiller votre déploiement

Voici comment vous pouvez contrôler les paramètres de configuration, état et la santé pour votre déploiement de récupération de Site :

1. Cliquez sur le nom de l’archivage sécurisé pour accéder au tableau de bord **Essentials** . Dans ce tableau de bord, vous pouvez travaux, état de la réplication, les plans de récupération, l’intégrité du serveur et événements récupération de Site.  Vous pouvez personnaliser Essentials pour afficher les vignettes et les dispositions qui sont plus utiles, y compris l’état d’autres chambres fortes sauvegarde et restauration de Site.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. Dans la vignette de **l’état** , vous pouvez surveiller les serveurs de site (serveurs VMM ou configuration) qui en cas de problème et les événements déclenchés par récupération de Site dans les dernières 24 heures.
3. Vous pouvez gérer et analyser la réplication dans les **Éléments répliquées**, **Plans de récupération**, et **Les tâches de récupération de Site** vignettes. Vous pouvez explorer les travaux dans **les paramètres** -> **travaux** -> **Tâches de récupération de Site**.


## <a name="next-steps"></a>Étapes suivantes

Une fois votre déploiement est configuré et en cours d’exécution, [pour en savoir plus](site-recovery-failover.md) sur les différents types de basculement.
