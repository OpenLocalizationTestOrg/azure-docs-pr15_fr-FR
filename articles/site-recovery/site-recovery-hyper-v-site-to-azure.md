<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V (sans VMM) vers Azure à l’aide de la récupération de Site Azure grâce au portail Azure | Microsoft Azure"
    description="Décrit comment déployer récupération de Site Azure pour organiser réplication, basculement et la restauration des ordinateurs virtuels Hyper-V locaux qui ne sont pas gérés par le Gestionnaire de mémoire virtuelle Azure à l’aide du portail Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Répliquer machines virtuelles de Hyper-V (sans VMM) vers Azure à l’aide de la récupération de Site Azure grâce au portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-hyper-v-site-to-azure.md)
- [Azure classique](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-deploy-with-powershell-resource-manager.md)



Bienvenue dans récupération de Site Azure ! Utilisation de cet article si vous souhaitez répliquer locaux Hyper-V virtual machines qui **ne sont pas** gérés dans nuages System Center Machines virtuelles Manager (VMM) pour Azure. Cet article décrit comment configurer la réplication à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE] Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.

> Récupération de Site Azure prend en charge la récupération et la migration de machines virtuelles Hyper-V Azure. Les étapes décrites dans cet article s’appliquent identique lors de la configuration de la réplication Azure récupération d’urgence ou de la migration de machines virtuelles vers Azure

Azure récupération de Site dans le portail Azure fournit un certain nombre de nouvelles fonctionnalités :

- Dans la Azure portal, sauvegarde Azure et les services de récupération de Site Azure sont combinées en un archivage sécurisé aux Services de récupération unique afin que vous pouvez configurer et gérer continuité des activités et récupération d’urgence (BCDR) à partir d’un emplacement unique. Un tableau de bord unifiée permet de contrôler et de gérer les opérations sur vos sites en local et le nuage public Azure.
- Opérations de récupération de Site dans le portail Azure peuvent maintenant gérer les utilisateurs disposant d’un abonnement Azure mis en service avec le programme Cloud Solution fournisseur (fournisseur).
- Récupération de site dans le portail Azure pouvez répliquer machines aux comptes de stockage Gestionnaire de ressources. À basculement, récupération de Site crée le Gestionnaire de ressources sur des ordinateurs virtuels dans Azure.
- Récupération de site continue à prendre en charge de la réplication de comptes de stockage classique et le basculement de machines virtuelles à l’aide du modèle de déploiement classique.


Après la lecture de vos commentaires en bas dans la section commentaires Disqus ce billet de l’article. Poser des questions techniques sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble


Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR conserve les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail sont disponibles en permanence cas d’urgence.

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en organiser réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure) ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous pouvez basculer à l’emplacement secondaire pour conserver les applications et les charges de travail disponibles. Vous pouvez restauré sur votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations que vous devez répliquer locaux Hyper-V virtual machines qui **ne sont pas** gérés dans nuages System Center Machines virtuelles Manager (VMM) pour Azure. Il inclut une présentation de l’architecture, planification des informations et des étapes de déploiement de configuration de serveurs locale Azure, une stratégie de réplication et planification de la capacité. Une fois que vous avez configuré l’infrastructure vous pouvez activer la réplication sur les ordinateurs à protéger et effectuer un basculement test pour valider la configuration correcte. Vous pouvez également migrer vos ordinateurs virtuels vers Azure en effectuant un basculement planifié d’abord, puis complétez la migration.

## <a name="business-advantages"></a>Avantages de l’entreprise

- Fournit hors basculement (Azure) pour les charges de travail métier et applications qui s’exécutent sur des ordinateurs virtuels Hyper-V.
- Fournit une console de Services de récupération unique pour le programme d’installation simple et la gestion des processus de réplication, le basculement et récupération.
- Permet de facilement exécuter basculement de votre infrastructure en local vers Azure et rétablissement (restaurer) à partir d’Azure sur le site local.
- Vous pouvez configurer les plans de récupération avec plusieurs ordinateurs, afin que les charges de travail hiérarchisé d’applications basculent entre eux.

## <a name="scenario-architecture"></a>Architecture de scénario

Voici les composants du scénario :

- **Hôte Hyper-V ou cluster**: serveurs hôtes locaux Hyper-V ou groupes. Les hôtes Hyper-V en cours d’exécution machines virtuelles à protéger sont réunis dans des sites Hyper-V logiques pendant le déploiement de récupération de Site.
- **Fournisseur de récupération de Site Azure et agent de récupération de services**: au cours du déploiement vous installez le fournisseur de récupération de Site Azure et de l’agent de Services de récupération de Microsoft Azure sur vos serveurs hôtes Hyper-V. Le fournisseur communique avec récupération de Site Azure via HTTPS 443 répliquer orchestration. L’agent sur le serveur hôte Hyper-V réplique les données vers le stockage Azure sur HTTPS 443 par défaut.
- **Azure**: vous devez un abonnement Azure, un compte de stockage Azure stockage répliquée des données et un réseau virtuel Azure afin que les ordinateurs virtuels Azure sont connectés à un réseau après le basculement.

![Architecture de site Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure déployer ce scénario.

**Conditions préalables** | **Plus d’informations**
--- | ---
**Compte Azure**| Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**Stockage Azure** | Vous avez besoin d’un compte de stockage standard. Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résistants si une panne régionale se produit ou si la région primaire ne peuvent pas être récupérée. [En savoir plus](../storage/storage-redundancy.md). Le compte doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.<br/><br/> Stockage Premium n’est pas pris en charge.<br/><br/> Données répliquées sont stockées dans le stockage Azure et Azure machines virtuelles sont créées lors du basculement.<br/><br/> [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous avez besoin d’un réseau virtuel Azure qui machines virtuelles Azure se connecteront à basculement. Le réseau virtuel Azure doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.

## <a name="on-premises-prerequisites"></a>Conditions préalables pour les locaux

Voici ce que vous devez en local.

**Conditions préalables** | **Plus d’informations**
--- | ---
**Hyper-V** | Un ou plusieurs locaux serveurs exécutant **Windows Server 2012 R2** avec les dernières mises à jour et le rôle Hyper-V est activé ou **Microsoft Hyper-V Server 2012 R2**.<br/><br/>Le serveur Hyper-V doit contenir un ou plusieurs machines virtuelles.<br/><br/>Serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br/><br/>Serveurs Hyper-V doivent avoir correctifs mentionnées dans [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") installé.
**Fournisseur et agent** | Pendant le déploiement d’Azure Site récupération vous devez installer le fournisseur de récupération de Site Azure. L’installation de fournisseur installe également l’Agent de Services de récupération Azure sur chaque serveur Hyper-V machines virtuelles à protéger. Tous les serveurs Hyper-V dans un archivage sécurisé récupération de Site doivent avoir la même version du fournisseur et agent.<br/><br/>Le fournisseur devront se connecter à la récupération de Site Azure via Internet. Le trafic peut être envoyé directement ou via un proxy. Notez que proxy HTTPS en fonction n’est pas pris en charge. Le serveur proxy doit autoriser l’accès à : <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blog.core.windows.net <br/><br/> *Store.Core.Windows.NET <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur, vérifiez que les règles permettent communications vers Azure. Vous devez autoriser les [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/>Autoriser les plages d’adresses IP pour la région Azure de votre abonnement et pour les États-Unis Ouest.

## <a name="protected-machine-prerequisites"></a>Ordinateur protégé conditions préalables


**Conditions préalables** | **Plus d’informations**
--- | ---
**Machines virtuelles protégées** | Avant de basculer sur un ordinateur virtuel, vous devez vous assurer que le nom qui doivent être affecté à la machine virtuelle Azure est conforme aux [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Vous pouvez modifier le nom après avoir activé la réplication de la machine virtuelle.<br/><br/> Capacité disque individuel sur machines protégées ne doivent pas être plus de 1023 go. Une machine virtuelle peut avoir jusqu'à 16 disques (et donc 16 To maximum).<br/><br/> Partagé invité disque clusters ne sont pas pris en charge.<br/><br/> Si la source machine virtuelle a cartes, il est converti en une seule carte réseau après le basculement vers Azure.<br/><br/>Protection des machines virtuelles exécutant Linux avec une adresse IP statique n’est pas pris en charge.

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour vous préparer pour le déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) dans lequel machines virtuelles Azure seront situés lorsqu’elles sont créées après le basculement.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données dupliquées.
3. [Préparer la Hyper-V hosts](#prepare-the-hyper-v-hosts) pour vous assurer qu’ils peuvent accéder aux URL requis.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Configurer un réseau Azure. Vous devez cela afin que les ordinateurs virtuels Azure créées après basculement sont connectés à un réseau.

- Le réseau doit être dans la même région que celui dans lequel vous allez déployer l’archivage sécurisé aux Services de récupération.
- Selon le modèle de ressources que vous souhaitez utiliser pour basculé machines virtuelles Azure, vous devez configurer le réseau Azure en [mode directeur des ressources](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [en mode classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Nous vous recommandons de que configurer une connexion réseau avant de commencer. Si vous ne vous devez effectuer au cours du déploiement de récupération de Site.

> [AZURE.NOTE] [Migration des réseaux](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous avez besoin d’un compte de stockage Azure standard pour stocker les données répliquées vers Azure.
- Selon le modèle de ressources que vous souhaitez utiliser pour basculé machines virtuelles Azure, vous devez configurer un compte en [mode directeur des ressources](../storage/storage-create-storage-account.md) ou [en mode classique](../storage/storage-create-storage-account-classic-portal.md).
- Nous vous recommandons de configurer un compte de stockage avant de commencer. Si vous ne vous devez effectuer au cours du déploiement de récupération de Site. Les comptes doivent être dans la même région en tant que l’archivage sécurisé aux Services de récupération.

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

### <a name="prepare-the-hyper-v-hosts"></a>Préparer les hôtes Hyper-V

- Assurez-vous que les hôtes Hyper-V respectent les [conditions préalables](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Créer un archivage sécurisé aux Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **sauvegarde et restauration de Site (OMS)**. Vous pouvez également cliquer sur **Parcourir** > **Services de récupération de** chambres fortes > **Ajouter**.

    ![Archivage sécurisé nouveau](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Dans la zone **nom** , spécifiez un nom convivial pour identifier l’archivage sécurisé. Si vous avez plusieurs abonnements, sélectionnez un d’eux.
4. [Créer un nouveau groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en une existante et spécifiez une région Azure. Machines seront répliquées à cette zone. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à l’archivage sécurisé du tableau de bord cliquez sur **Ajouter au tableau de bord** , puis sur **créer l’archivage sécurisé**.

    ![Archivage sécurisé nouveau](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

L’archivage sécurisé nouveau s’affichent dans le **tableau de bord** > **toutes les ressources**et sur le principaux **Services de récupération de chambres fortes** carte.

## <a name="getting-started"></a>Prise en main

Récupération de site fournit une mise en route expérience qui vous aident à déployer aussi rapidement que possible. Mise en route vérifie les conditions préalables et vous guide tout au long de récupération de Site déploiement les étapes dans l’ordre approprié.

Dans mise en route vous sélectionnez le type d’ordinateurs que vous souhaitez répliquer et l’endroit où vous souhaitez répliquer à. Vous configurez serveurs locaux, les comptes de stockage Azure et les réseaux. Vous créez des règles de réplication et planifier la capacité. Une fois que vous avez configuré votre infrastructure vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter basculement pour les ordinateurs spécifiques, ou créer des plans de récupération à basculer sur plusieurs ordinateurs.

Commencer mise en route en choisissant la manière dont vous souhaitez déployer récupération de Site. Le flux de mise en route change légèrement selon vos besoins en matière de réplication.



## <a name="step-1-choose-your-protection-goals"></a>Étape 1 : Choisir vos objectifs de protection

Sélectionnez ce que vous voulez répliquer et l’endroit où vous souhaitez répliquer à.

1. Dans la carte de **Services de récupération de chambres fortes** Sélectionnez votre l’archivage sécurisé et cliquez sur **paramètres**.
2. Dans **paramètres** > **Prise en main** cliquez sur **Récupération de Site** > **étape 1 : préparer une Infrastructure** > **objectif de Protection**.

    ![Sélectionnez des objectifs](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. Dans **l’objectif de Protection** sélectionnez **Sur Azure**, puis sélectionnez **Oui, avec Hyper-V**. Sélectionnez **non** pour confirmer que vous n’utilisez pas VMM. Cliquez ensuite sur **OK**.

    ![Sélectionnez des objectifs](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Configurer le site Hyper-V, installez le fournisseur de récupération de Site Azure et de l’agent de Services de récupération Azure sur hôtes Hyper-V et enregistrer les hôtes dans l’archivage sécurisé.


1. Cliquez sur **étape 2 : préparer Infrastructure** > **Source**. Pour ajouter un nouveau site Hyper-V comme un conteneur pour vos hôtes Hyper-V ou groupes, cliquez sur **+ Site Hyper-V**.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. Dans la carte de **site créer Hyper-V** spécifiez un nom pour le site. Cliquez ensuite sur **OK**. Sélectionnez le site que vous venez de créer.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Cliquez sur **+ Hyper-V Server** pour ajouter un serveur au site.
4. Dans **Ajouter un serveur** > **type de serveur de** vérifier que le **serveur Hyper-V** est affichée. Assurez-vous que le serveur Hyper-V que vous voulez ajouter soit conforme à la [Configuration requise](#on-premises-prerequisites) et est en mesure d’accéder à l’URL spécifiées.
4. Téléchargez le fichier d’installation fournisseur de récupération de Site Azure. Vous devez exécuter ce fichier pour installer le fournisseur et l’agent de Services de récupération sur chaque hôte Hyper-V.
5. Téléchargez la clé d’inscription. Vous devez ceci lorsque vous exécutez le programme d’installation. La clé est valide pour 5 jours après la générer.

    ![Configurer la source](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Exécuter le fournisseur de fichier d’installation sur chaque hôte que vous avez ajouté au site Hyper-V. Si vous installez sur un cluster Hyper-V, exécutez le programme d’installation pour chaque nœud. Installation et enregistrement de chaque nœuds Cluster Hyper-V garantissent que machines virtuelles restent protégées même si elles migrent entre les nœuds.

### <a name="install-the-provider-and-agent"></a>Installer le fournisseur et l’agent

1. Exécutez le fournisseur de fichier d’installation.
2. Dans **Microsoft Update,** vous pouvez choisir dans les mises à jour afin que les mises à jour du fournisseur sont installés conformément à votre stratégie Microsoft Update.
3. Dans **Installation** accepter ou modifier l’emplacement d’installation de fournisseur par défaut et cliquez sur **installer**.
4. Dans la page **Paramètres de l’archivage sécurisé** , cliquez sur **Parcourir** pour sélectionner le fichier de clé de l’archivage sécurisé que vous avez téléchargé. Spécifier l’abonnement de récupération de Site Azure, le nom de l’archivage sécurisé et le site Hyper-V auquel appartient le serveur Hyper-V.

    ![Enregistrement du serveur](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5 dans **Les paramètres de Proxy** , spécifiez comment le fournisseur qui est installé sur le serveur se connecteront aux récupération de Site Azure via internet.

- Si vous souhaitez que le fournisseur de se connecter directement sélectionnez **se connecter directement sans proxy**.
- Si vous voulez vous connecter avec le proxy est configuré sur le serveur sélectionnez **se connecter avec les paramètres de proxy existants**.
- Si votre proxy existant requiert une authentification, ou vous souhaitez utiliser un proxy personnalisé pour l’instruction select connexion fournisseur **se connecter avec les paramètres de proxy personnalisés**.
- Si vous utilisez un proxy personnalisé, que vous devez spécifier l’adresse, les ports et les informations d’identification
- Si vous utilisez un proxy, vérifiez que les URL décrites dans les [conditions préalables](#on-premises-prerequisites) sont autorisés traversé.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6 une fois l’installation terminée cliquer sur **Enregistrer** pour enregistrer le serveur dans l’archivage sécurisé.

![Emplacement d’installation](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7 après l’inscription métadonnées Hyper-V server est extraites lors de la récupération de Site Azure et le serveur est affiché dans les **paramètres** > **Infrastructure de récupération de Site** > carte**Hôtes Hyper-V** .


### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure et l’agent peuvent également être installés à l’aide de la ligne de commande suivante. Cette méthode peut être utilisée pour installer le fournisseur sur un serveur Core pour Windows Server 2012 R2.

1. Téléchargez la clé de fichier et d’enregistrement de l’installation fournisseur dans un dossier. Par exemple C:\ASR.
2. À partir d’une invite de commandes avec élévation de privilèges, exécutez ces commandes pour extraire le programme d’installation de fournisseur :

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Exécutez la commande suivante pour installer les composants :

            C:\ASR> setupdr.exe /i

4. Puis exécutez ces commandes pour enregistrer le serveur dans l’archivage sécurisé : CD C:\Program Files\Microsoft Azure Site récupération Provider\
            Fournisseur de récupération de Site Azure C:\Program Files\Microsoft\> DRConfigurator.exe /r /Friendlyname <friendly name of the server> /les informations d’identification<path of the credentials file>

<br/>
Dans cet exemple :

- **/Credentials** : paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
- **/FriendlyName** : paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
- **/ProxyAddress** : paramètre facultatif qui spécifie l’adresse du serveur proxy.
- **/ProxyPort** : paramètre facultatif qui spécifie le port du serveur proxy.
- **/proxyUsername** : paramètre facultatif qui indique le nom d’utilisateur Proxy (si proxy requiert une authentification).
- **/proxyPassword** : paramètre facultatif qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si proxy requiert une authentification).


## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Configurer l’environnement cible

Spécifier le compte de stockage Azure à utiliser pour la réplication et le réseau Azure auquel machines virtuelles Azure va se connecter après le basculement.

1.  Cliquez sur **préparer infrastructure** > **cible** et sélectionnez l’abonnement Azure que vous voulez utiliser.
2.  Spécifiez le modèle de déploiement que vous voulez utiliser pour les machines virtuelles après le basculement.
3.  Récupération de site vérifie que vous avez un ou plusieurs comptes de stockage Azure compatible et réseaux.

    ![Espace de stockage](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  Si vous n’avez pas créé un compte de stockage et que vous voulez créer un gestionnaire de ressources cliquez sur **+ stockage** compte pour effectuer cette inline. Sur la carte de **créer un compte de stockage** , spécifiez un nom de compte, type, abonnement et emplacement. Le compte doit être dans le même emplacement que l’archivage sécurisé aux Services de récupération.

    ![Espace de stockage](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    Si vous voulez créer un compte de stockage à l’aide du modèle classique vous ferez que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md).

5.  Si vous n’avez pas créé un réseau Azure et que vous voulez créer un à l’aide du Gestionnaire de ressources cliquez sur **+ réseau** pour effectuer cette inline. Sur la carte **réseau virtuel créer** spécifiez un nom de réseau, plage d’adresses, détails du sous-réseau, abonnement et un emplacement. Le réseau doit être dans le même emplacement que l’archivage sécurisé aux Services de récupération.

    ![Réseau](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    Si vous voulez créer un réseau à l’aide du modèle classique vous ferez que [dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).


## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Configurer les paramètres de réplication

1. Pour créer une nouvelle réplication stratégie, cliquez sur **préparer infrastructure** > **Paramètres de réplication** > **+ créer et associer**.

    ![Réseau](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. Dans **créer et stratégie associer** spécifiez un nom de la stratégie.
3. Dans la zone **Copier fréquence** spécifier la fréquence à laquelle vous souhaitez répliquer données delta après la réplication initiale (toutes les 30 secondes, 5 ou 15 minutes).
4. Dans la **rétention des points de récupération**, spécifiez heures combien de temps la fenêtre de rétention seront pour chaque point de récupération. Machines protégées peuvent être récupérés à tout moment dans une fenêtre.
6. **Cohérente à l’échelle de l’application** de la fréquence instantané spécifier la fréquence (1 à 12 heures) contenant des instantanés cohérents avec les applications de points de récupération sera créé. Hyper-V utilise deux types d’instantanés — une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérentes avec les applications qui prend un instantané de données de l’application à l’intérieur de la machine virtuelle. Instantanés cohérents avec les applications permet de s’assurer que les applications sont dans un état cohérent lorsque l’instantané Volume Shadow Copy Service (VSS). Notez que si vous activez les instantanés cohérents avec les applications, il affectent les performances des applications qui s’exécutent sur des machines virtuelles source. Vérifiez que la valeur est inférieure à celle du nombre de points de récupération supplémentaires que vous configurez.
3. Dans **l’heure de début de la réplication initiale** spécifier le moment auquel démarrer la réplication initiale. La réplication a lieu sur votre bande passante internet afin que vous souhaiterez peut-être planifier en dehors de vos heures occupé (e). Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Lorsque vous créez une nouvelle stratégie il a automatiquement associés au site Hyper-V. Cliquez sur **OK**. Vous pouvez associer un site Hyper-V (et les ordinateurs virtuels qu’il contient) à plusieurs règles de réplication dans **les paramètres** > **réplication** > nom de la stratégie > **Associer le Site Hyper-V**.

## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configurer vous pouvez envisager de planification de la capacité et déterminer si vous avez besoin des ressources supplémentaires.

Récupération de site fournit un planificateur de capacité pour vous aider à allouer les ressources de votre environnement source, composants de récupération de site, mise en réseau et de stockage. Vous pouvez exécuter le planificateur en mode rapide pour estimations selon un nombre moyen de machines virtuelles, des disques et stockage ou en mode détaillé dans laquelle vous allez saisir les chiffres au niveau de la charge de travail. Avant de commencer, vous devez à :

- Collecter les informations relatives à votre environnement de réplication, y compris les machines virtuelles, disques par machines virtuelles et stockage par disque.
- Estimer le taux de modification (évolution du) quotidien qu'avoir pour les données répliquées. Vous pouvez utiliser le [Planificateur de capacité réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour vous aider à procédez comme suit.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lisez l’article intitulé](site-recovery-capacity-planner.md) qui accompagne l’outil.
2.  Lorsque vous avez terminé sélectionnez **Oui** dans **que vous avez exécuté le Planificateur de capacités**?

    ![Planification de la capacité](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considérations relatives à la bande passante réseau

Vous pouvez utiliser l’outil de planificateur de capacité pour calculer la bande passante que vous avez besoin de la réplication (réplication initiale, puis delta). Pour contrôler la quantité d’utilisation de la bande passante de la réplication plusieurs options s’offrent à vous :

- **Limiter la bande passante**: le trafic Hyper-V qui réplique vers Azure parcourt un hôte Hyper-V spécifique. Vous pouvez limiter la bande passante sur le serveur hôte.
- **Modifier la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre.

#### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable Microsoft Azure sauvegarde MMC sur le serveur hôte Hyper-V. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sous l’onglet **régulation** sélectionnez **Activer l’utilisation de la bande passante internet la limitation pour les opérations de sauvegarde**et définir les limites de travail et non liés au travail heures. Les plages valides sont compris 512 Kb/s et 102 Mo/s par seconde.

    ![Limiter la bande passante](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Jeu-OBMachineSetting NoThrottle** indique qu’aucune limitation n’est requise.


#### <a name="influence-network-bandwidth"></a>Influencer la bande passante réseau

1. Dans le Registre atteindre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Pour influencer le trafic de bande passante sur un disque réplication, modifiez la valeur de **UploadThreadsPerVM**, ou créez la clé s’il n’existe pas.
    - Pour influencer la bande passante pour le trafic de retour arrière à partir d’Azure, modifiez la valeur **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « overprovisioned », ces clés de Registre par les valeurs par défaut doivent être modifiées. La valeur maximale est 32. Surveiller le trafic afin d’optimiser la valeur.

## <a name="step-6-enable-replication"></a>Étape 6 : Réplication d’activer

Maintenant, activez la réplication comme suit :

1. Cliquez sur **étape 2 : répliquer application** > **Source**. Après avoir activé la réplication pour la première fois que vous cliquerez sur **+ répliquer** dans l’archivage sécurisé pour activer la réplication pour d’autres ordinateurs.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. Dans la carte **Source** > sélectionnez le site Hyper-V. Cliquez ensuite sur **OK**.
3. Dans la **cible** , sélectionnez l’abonnement de l’archivage sécurisé et le modèle de basculement que vous souhaitez utiliser dans Azure (gestion classique ou une ressource) après le basculement.
4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous souhaitez utiliser un compte de stockage autre que celles vous devez vous pouvez [en créer un](#set-up-an-azure-storage-account). Pour créer un espace de stockage compte à l’aide du modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous voulez créer un compte de stockage à l’aide du modèle classique vous ferez que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md). Cliquez ensuite sur **OK**.
5.  Sélectionnez le réseau Azure et sous-réseau auquel machines virtuelles Azure va se connecter lorsqu’ils êtes lancés après le basculement. Cliquez sur **configurer maintenant pour les ordinateurs sélectionnés** pour appliquer le paramètre de réseau à tous les ordinateurs que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau Azure par ordinateur. Si vous voulez utiliser un réseau différent de ceux vous avez vous pouvez [en créer un](#set-up-an-azure-network). Pour créer une connexion réseau à l’aide du modèle de gestionnaire de ressources, cliquez sur **Créer nouveau**. Si vous voulez créer un réseau à l’aide du modèle classique vous ferez que [dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Sélectionner un sous-réseau le cas échéant. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Machines **virtuelles** > **Sélectionnez machines virtuelles** cliquez puis sélectionnez chaque ordinateur que vous voulez dupliquer. Vous ne pouvez sélectionner machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. Dans les **Propriétés** > **configurer les propriétés**, sélectionnez le système d’exploitation pour les ordinateurs virtuels sélectionnés et le disque du système d’exploitation. Vérifiez que la mémoire virtuelle Azure nom (cible) est conforme aux [exigences de machine virtuelle Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) et modifiez-le si vous avez besoin. Cliquez ensuite sur **OK**. Vous pouvez définir des propriétés supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. Dans **les paramètres de réplication** > **configurer des paramètres de réplication**, sélectionnez la stratégie de réplication que vous voulez appliquer pour les ordinateurs virtuels protégées. Cliquez ensuite sur **OK**. Vous pouvez modifier la stratégie de réplication dans **les paramètres** > **stratégies de réplication** > nom de la stratégie > **Modifier les paramètres**. Vous appliquez les modifications seront utilisées pour machines sont déjà réplication et nouvelles machines.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Vous pouvez suivre la progression de la tâche **d’Activation de la Protection** dans **les paramètres** > **travaux** > **tâches de récupération de Site**. Après l’exécution de la tâche de **Protection finaliser** l’ordinateur est prêt pour le basculement.

### <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés de la machine virtuelle

Nous vous recommandons de vérifier les propriétés de l’ordinateur source.

1. Cliquez sur **paramètres** > **Éléments protégé** > **Éléments répliquées** > et sélectionnez l’ordinateur.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Dans les **Propriétés** vous pouvez afficher les informations de réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. Dans le **cluster et réseau** > **Calculer propriétés** que vous pouvez spécifier la taille de nom et cibles machine virtuelle Azure. Modifier le nom conformes aux exigences Azure si vous avez besoin. Vous pouvez également afficher et modifier des informations sur le réseau cible, le sous-réseau et l’adresse IP qui doivent être affectée à la machine virtuelle Azure. Notez les points suivants :

    - Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse de l’échec de la sur ordinateur utilise DHCP. Si vous définissez une adresse qui n’est pas disponible lors du basculement, le basculement échouera. La même adresse IP cible peut servir pour le basculement de test si l’adresse est disponible dans le réseau de basculement test.
    - Le nombre de cartes réseau est dépend de la taille que vous spécifiez pour la machine virtuelle cible, comme suit :

        - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
        - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé pour la taille de la cible avant la taille maximale des cible sera utilisée.
        - Par exemple, si une machine source a deux cartes réseau et la taille de l’ordinateur cible prend en charge quatre, l’ordinateur cible comporte deux cartes. Si l’ordinateur source possède deux cartes, mais la taille cible pris en charge n’accepte qu’une l’ordinateur cible aura qu’une carte.     
        - Si la machine virtuelle possède plusieurs cartes réseau qu’ils seront tous se connecter au même réseau.

    ![Activer la réplication](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  Dans **disques** , vous pouvez voir le système d’exploitation et les données disques sur l’ordinateur virtuel qui sera répliquée.


## <a name="step-7-test-the-deployment"></a>Étape 7 : Tester le déploiement

Pour tester le déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle ou un plan de récupération qui contient une ou plusieurs machines virtuelles.


### <a name="prepare-for-test-failover"></a>Préparer pour le basculement de test

- Pour exécuter un basculement de test, nous vous recommandons de créer un nouveau réseau Azure a isolé à partir de votre réseau de production Azure (c’est le comportement par défaut lorsque vous créez un nouveau réseau dans Azure). [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur l’exécution de basculement test.
- Pour obtenir les meilleures performances lorsque vous basculer vers Azure, installez l’Agent Azure sur l’ordinateur protégé. Il crée démarrage plus rapides et aide à la résolution des problèmes. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Pour tester entièrement votre déploiement, vous devez une infrastructure pour la machine répliquée fonctionne comme prévu. Si vous souhaitez tester Active Directory et DNS vous pouvez créer une machine virtuelle comme contrôleur de domaine avec le système DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lisez en autres [Considérations relatives à basculement de test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
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

- Ajouter une adresse IP publique à la carte réseau associée à la machine virtuelle Azure pour permettre à RDP.
- Vérifiez que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter Vérifiez que la machine virtuelle s’exécute. Pour obtenir d’autres conseils de dépannage Lisez cet [article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Si vous voulez accéder à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un Secure Shell client (ssh), procédez comme suit :

**Sur l’ordinateur local avant le basculement**:

- Assurez-vous que le service banque d’informations sécurisé Shell sur l’ordinateur virtuel Azure est défini pour démarrer automatiquement au démarrage du système.
- Vérifiez que les règles de pare-feu permettent une connexion SSH à ce dernier.

**Sur le Azure machine virtuelle après le basculement**:

- Les règles de groupe de sécurité réseau de l’échec de la machine virtuelle et le sous-réseau Azure auquel il est connecté devront autoriser les connexions entrantes à la voie SSH.
- Un point de terminaison public doit être créée pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).
- Si la machine virtuelle est accessible via une connexion VPN (itinéraire Express ou VPN de site à) le client peut être utilisé pour se connecter directement à la machine virtuelle sur SSH.

### <a name="run-a-test-failover"></a>Exécuter un basculement de test

Pour exécuter la ne basculement test ce qui suit :

1. Basculer sur un ordinateur virtuel unique dans **les paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > **+ basculement de Test**.

    ![Basculement de test](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. Basculer sur un plan de récupération, dans **les paramètres** > de**Récupération Plans**, avec le bouton droit de l’offre > **Basculement de Test**. Pour créer une récupération planifier [Suivez ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le **Test de basculement** sélectionnez le réseau Azure auquel machines virtuelles Azure sera connecté après basculement.

    ![Basculement de test](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

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


## <a name="failover"></a>Basculement

Une fois la réplication initiale terminée pour vos ordinateurs, vous pouvez appeler basculement en cas de besoin. Récupération de site prend en charge différents types de basculement - basculement Test, basculement planifiée et non planifiée.
[En savoir plus](site-recovery-failover.md) sur les différents types de basculement et une description détaillée des quand et comment effectuer chacun d’eux.

> [AZURE.NOTE] Si votre intention est de migrer des machines virtuelles vers Azure, nous vous recommandons vivement de que vous utilisez une [opération de basculement planifiée](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary) pour migrer les machines virtuelles vers Azure. Une fois l’application migrée est validée dans Azure à l’aide de basculement test, suivez les étapes mentionnées sous [Migration complète](#Complete-migration-of-your-virtual-machines-to-Azure) pour effectuer la migration de vos machines virtuelles. Vous n’avez pas besoin d’effectuer une validation ou les supprimer. Migration complète termine la migration, supprime la protection de la machine virtuelle et arrête facturation Azure Site récupération de l’ordinateur.


###<a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

Cela doit être choisie quand un site principal devient inaccessible en raison d’un incident inattendu, par exemple une attaque de virus ou une panne power. Cette procédure explique comment exécuter un basculement non planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une seule machine virtuelle sous l’onglet Machines virtuelles. Avant de commencer, vérifiez que tous les ordinateurs virtuels que vous souhaitez basculer ont terminé la réplication initiale.

1. Sélectionnez **Plans de récupération > recoveryplan_name**.
2. Dans la carte de plan de restauration, cliquez sur **Basculement non planifié**.
3. Dans la page **basculement non planifié** , choisissez les emplacements sources et cibles.
4. Sélectionnez **Arrêter machines virtuelles et synchroniser des données les plus récentes** pour indiquer que doit essayer de récupération de Site arrêter les machines virtuelles protégés et synchroniser les données afin que la dernière version des données est alors basculée.
5. Après le basculement, les machines virtuelles sont dans une validation état d’attente.  Cliquez sur **Valider** pour valider le basculement.

[Pour en savoir plus](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Réalisez la migration de vos machines virtuelles vers Azure


>[AZURE.NOTE] Les étapes suivantes s’appliquent uniquement si vous migrez machines virtuelles vers Azure

1. Effectuer le basculement planifié comme mentionné [ici](site-recovery-failover.md)
2. Dans **Paramètres > répliquées éléments**, avec le bouton droit de la machine virtuelle et sélectionnez **Une Migration complète**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. Cliquez sur **OK** pour terminer la migration. Vous pouvez suivre la progression en cliquant sur l’ordinateur virtuel pour ouvrir ses propriétés ou à l’aide de la tâche de Migration complète dans **Paramètres > tâches de récupération de Site**.


## <a name="monitor-your-deployment"></a>Surveiller votre déploiement

Voici comment vous pouvez contrôler les paramètres de configuration, état et la santé pour votre déploiement de récupération de Site :

1. Cliquez sur le nom de l’archivage sécurisé pour accéder au tableau de bord **Essentials** . Dans ce tableau de bord, vous pouvez travaux, état de la réplication, les plans de récupération, l’intégrité du serveur et événements récupération de Site.  Vous pouvez personnaliser Essentials pour afficher les vignettes et les dispositions qui sont plus utiles, y compris l’état d’autres chambres fortes sauvegarde et restauration de Site.

    ![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. Dans la vignette de **l’état** , vous pouvez surveiller les serveurs de site qui en cas de problème et les événements déclenchés par récupération de Site dans les dernières 24 heures.
3. Vous pouvez gérer et analyser la réplication dans les **Éléments répliquées**, **Plans de récupération**, et **Les tâches de récupération de Site** vignettes. Vous pouvez explorer les travaux dans **les paramètres** -> **travaux** -> **Tâches de récupération de Site**.
