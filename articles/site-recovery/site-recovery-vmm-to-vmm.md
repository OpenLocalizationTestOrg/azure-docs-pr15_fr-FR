<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM sur un site VMM secondaire à l’aide du portail Azure | Microsoft Azure"
    description="Décrit comment déployer récupération de Site Azure pour organiser réplication, le basculement et récupération machines virtuelles Hyper-V en nuages VMM à un site VMM secondaire à l’aide du portail Azure."
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
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Répliquer machines virtuelles de Hyper-V dans nuages VMM sur un site VMM secondaire à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-vmm.md)
- [Portail classique](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bienvenue dans récupération de Site Azure ! Utilisation de cet article si vous souhaitez répliquer machines virtuelles de Hyper-V local géré dans nuages System Center Virtual Machine Manager (VMM) vers un site secondaire. Cet article décrit comment configurer la réplication à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE] Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.


Azure récupération de Site dans le portail Azure fournit plusieurs nouvelles fonctionnalités :

- Dans la Azure portal, la sauvegarde Azure et les services de récupération de Site Azure sont combinées en un archivage sécurisé aux Services de récupération unique, afin que vous pouvez configurer et gérer continuité des activités et récupération d’urgence (BCDR) à partir d’un emplacement unique. Un tableau de bord unifiée vous permet de surveillance et de gérer les opérations sur vos sites en local et le nuage public Azure.
- Opérations de récupération de Site dans le portail Azure peuvent maintenant gérer les utilisateurs disposant d’un abonnement Azure mis en service avec le programme Cloud Solution fournisseur (fournisseur).


Après la lecture de cet article, publiez vos commentaires en bas dans les commentaires Disqus. Poser des questions techniques sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR doit conserver les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail restent disponibles en permanence cas d’urgence.

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en organiser réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure) ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous basculer vers l’emplacement secondaire afin que les applications et les charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations que vous devez répliquer locaux machines virtuelles Hyper-V dans nuages VMM à un site VMM secondaire. Il inclut une présentation de l’architecture, planification des informations et des étapes de déploiement de configuration de serveurs en local, les paramètres de réplication et planification de la capacité. Une fois que vous avez configuré l’infrastructure que vous pouvez activer la réplication sur les ordinateurs que vous voulez protéger et vérifier que le basculement fonctionne.

## <a name="business-advantages"></a>Avantages de l’entreprise

- Récupération de site protège les charges de travail métier et applications qui s’exécutent sur les ordinateurs virtuels Hyper-V par les répliquer sur un serveur Hyper-V secondaire.
- Le portail de Services de récupération fournit un emplacement unique pour configurer, gérer et surveiller réplication, le basculement et récupération.
- Vous pouvez exécuter basculement facilement exécuter à partir de votre infrastructure principal local aux sites secondaire et retour arrière (restauration) à partir du site secondaire pour le serveur principal.
- Vous pouvez configurer les plans de récupération avec plusieurs ordinateurs afin que les charges de travail hiérarchisé d’applications basculent entre eux.

## <a name="scenario-architecture"></a>Architecture de scénario

Voici les composants du scénario :

- **Site principal**: dans le site principal, il existe une ou plusieurs Hyper-V héberger des serveurs exécutant machines virtuelles source que vous souhaitez répliquer. Ces serveurs hôte principal se trouvent dans un cloud privé VMM.
- **Site secondaire**: dans le site secondaire, il existe une ou plusieurs Hyper-V héberger des serveurs exécutant machines virtuelles cible à laquelle vous répliquez les ordinateurs virtuels principales. Ces serveurs hôtes sont situés dans un nuage privé VMM. Le cloud peut être sur le serveur principal (si vous avez uniquement un seul serveur VMM) ou sur un serveur VMM secondaire.
- **Fournisseur**: au cours du déploiement de récupération de Site, vous installez le fournisseur de récupération de Site Azure sur les serveurs VMM et d’enregistrer ces serveurs dans un archivage sécurisé aux Services de récupération. Le fournisseur en cours d’exécution sur le serveur VMM communique avec récupération de Site via HTTPS 443 répliquer orchestration. La réplication de données se produit entre les principaux et secondaires serveurs hébergés de Hyper-V. Données répliquées restent dans les réseaux et des sites en local et ne sont pas envoyées à Azure. En savoir plus sur la [confidentialité](#privacy-information-for-site-recovery).

![Topologie E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Vue d’ensemble de la confidentialité des données

Le tableau suivant résume la façon dont les données sont stockées dans ce scénario :
****
Action | **Plus d’informations** | **Données collectées** | **Utilisation** | **Obligatoire**
--- | --- | --- | --- | ---
**Enregistrement** | Vous inscrire un serveur VMM dans un archivage sécurisé aux Services de récupération. Si vous voulez annuler l’enregistrement d’un serveur, vous pouvez le faire en supprimant les informations du serveur à partir du portail Azure. | Après avoir enregistré un serveur VMM récupération de Site collecte, processus et transfère les métadonnées sur le serveur VMM et les noms des nuages VMM détectés par récupération de Site. | Les données sont utilisées pour identifier et communiquer avec le serveur VMM approprié et configurer les paramètres pour les nuages VMM appropriés. | Cette fonctionnalité est requise. Si vous ne voulez pas envoyer ces informations à récupération de Site vous ne devez pas utiliser le service de récupération de Site.
**Activer la réplication** | Le fournisseur de récupération de Site Azure est installé sur le serveur VMM et est le support de communication avec le service de récupération de Site. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Une fois que le fournisseur est installé, la fonctionnalité « Récupération de centre de données » est activée dans la console administrateur. Machines virtuelles nouveaux et existants peuvent activer cette fonctionnalité Activer la protection d’un ordinateur virtuel. | Avec cette propriété est définie, le fournisseur envoie le nom et l’ID de la machine virtuelle récupération de Site.  La réplication est activée par Windows Server 2012 ou Windows Server 2012 R2 Hyper-V réplica. Les données de machine virtuelle répliquées à partir d’un hôte Hyper-V à l’autre (généralement situé dans un centre de données différents « récupération »). | Récupération de site utilise les métadonnées pour remplir les informations de mémoire virtuelle dans le portail Azure. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne voulez pas envoyer ces informations, n’activez pas la protection de récupération de Site pour les machines virtuelles. Notez que toutes les données envoyées par le fournisseur de récupération de Site sont envoyées sur HTTPS.
**Plan de récupération** | Plans de vous aident à créer un plan d’orchestration pour le centre de récupération des données. Vous pouvez définir l’ordre dans lequel les machines virtuelles ou un groupe de machines virtuelles doit être démarré sur le site de récupération. Vous pouvez également spécifier les scripts automatisés pour être s’exécute ou tout manuelle action à exécuter, au moment de la récupération pour chaque machine virtuelle. Basculement est généralement déclenché au niveau de plan de récupération pour récupération coordonné. | Récupération de site collecte, traite et transmet les métadonnées pour le plan de récupération, y compris la machine virtuelle et les métadonnées des scripts d’automatisation et notes action manuelle. | Les métadonnées sont utilisées pour créer le plan de récupération dans le portail Azure. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne voulez pas envoyer ces informations à récupération de Site, ne créez pas les plans de récupération.
**Mappage réseau** | Cartes réseau des informations à partir du centre de données principal vers le centre de récupération des données. Quand machines virtuelles sont récupérés sur le site de récupération, mappage réseau est utile pour établir la connectivité réseau. | Récupération de site collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (primaire et centre de données). | Les métadonnées sont utilisée pour renseigner les paramètres du réseau afin que vous pouvez mapper les informations du réseau. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne voulez pas envoyer ces informations à récupération de Site, ne pas utiliser le mappage réseau.
**Basculement (planifiée/non planifiée/test)** | Basculement bascule machines virtuelles à partir d’un centre de données géré par VMM à l’autre. L’action de basculement se déclenche manuellement dans le portail Azure. | Le fournisseur sur le serveur VMM est informé de l’événement basculement par récupération de Site et exécute une action basculement sur l’hôte Hyper-V via des interfaces VMM. Le basculement réel d’un ordinateur virtuel est d’un hôte Hyper-V à un autre et gérées par Windows Server 2012 ou Windows Server 2012 R2 Hyper-V réplica. Une fois le basculement terminé, le fournisseur sur le serveur VMM dans le centre de récupération des données envoie les informations de réussite à récupération de Site. | Récupération de site utilise les informations envoyées à l’état des informations dans le portail Azure action basculement de remplissage. | Cette fonctionnalité est une partie essentielle du service et ne peut pas être désactivée. Si vous ne voulez pas envoyer ces informations à récupération de Site, n’utilisez pas basculement.


## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure pour déployer ce scénario :

**Conditions préalables** | **Plus d’informations**
--- | ---
**Azure**| Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.


## <a name="on-premises-prerequisites"></a>Conditions préalables pour les locaux

Voici ce que vous devez dans les sites principaux et secondaires en local pour déployer ce scénario :

**Conditions préalables** | **Plus d’informations**
--- | ---
**VMM** | Nous vous recommandons de que vous déployez un serveur VMM dans le site principal et un serveur VMM dans le site secondaire.<br/><br/> Vous pouvez également [répliquer entre nuages sur un seul serveur VMM](site-recovery-single-vmm.md). Pour ce faire, vous devez au moins deux nuages configurés sur le serveur VMM.<br/><br/> Serveurs VMM doivent s’exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit avoir un ou plusieurs nuages configurés et tous les nuages doivent avoir le profil de capacité Hyper-V. <br/><br/>Nuages doivent contenir un ou plusieurs groupes hôtes VMM.<br/><br/>En savoir plus sur la configuration nuages VMM lors de la [configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), et [procédure pas à pas : création de nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Serveurs VMM besoin d’accès à internet.
**Hyper-V** | Serveurs Hyper-V doivent exécuter au minimum Windows Server 2012 avec le rôle Hyper-V et ont installé les dernières mises à jour.<br/><br/> Un serveur Hyper-V doit contenir un ou plusieurs ordinateurs virtuels.<br/><br/>  Serveurs hôte Hyper-V doivent être placés dans des groupes hôtes dans les nuages VMM principales et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster sur Windows Server 2012 R2 vous devez installer la [mise à jour 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si vous exécutez Hyper-V dans un cluster sur Windows Server 2012, notez qu’intermédiaire cluster n’est pas créée automatiquement si vous avez un cluster de basée sur l’adresse IP statique. Vous devez configurer manuellement l’intermédiaire cluster. [Pour en savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Fournisseur** | Pendant le déploiement de récupération de Site, vous installez le fournisseur de récupération de Site Azure sur des serveurs VMM. Le fournisseur communique avec récupération de Site via HTTPS 443 pour harmoniser réplication. Réplication de données se produise entre les serveurs Hyper-V principales et secondaires sur le réseau local ou une connexion VPN.<br/><br/> Le fournisseur en cours d’exécution sur le serveur VMM a besoin d’accéder à ces URL : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net.<br/><br/> En outre autoriser communications pare-feu à partir des serveurs VMM vers les [plages d’adresses IP centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autoriser le protocole HTTPS (443).

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour préparer pour le déploiement, vous devez :

1. [Préparer le serveur VMM](#prepare-the-vmm-server) pour le déploiement de récupération de Site.
2. [Préparer pour le mappage réseau](#prepare-for-network-mapping). Configurer des réseaux afin que vous pouvez configurer le mappage réseau.

### <a name="prepare-the-vmm-server"></a>Préparer le serveur VMM

Assurez-vous que le serveur VMM soit conforme à la [Configuration requise](#on-premises-prerequisites) et peut accéder aux URL répertoriées.


### <a name="prepare-for-network-mapping"></a>Préparer pour le mappage de réseau

Cartes de mappage réseau entre les réseaux VMM VM sur les serveurs VMM principales et secondaires pour :

- Placez optimale machines virtuelles réplica sur secondaires hôtes Hyper-V après le basculement.
- Connecter réplica machines virtuelles aux réseaux de machine virtuelle appropriés.
- Si vous ne configurez réplica de mappage réseau que machines virtuelles ne sont pas connectés à un réseau après le basculement.
- Si vous voulez configurer réseau mappage lors de la récupération de Site déploiement ici constitue la démarche :

    - Assurez-vous que machines virtuelles sur le serveur source Hyper-V hôte sont connectés à un réseau VMM VM. Ce réseau doit être lié à un réseau logique est associé avec le cloud.
    - Vérifiez qu’un réseau machine virtuelle correspondant configuré sur le cloud secondaire que vous utilisez pour la récupération. Ce réseau machine virtuelle doit être lié à un réseau logique associée le cloud secondaire.

- [En savoir plus](site-recovery-network-mapping.md) sur le fonctionne de mappage réseau.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Préparer pour le déploiement avec un seul serveur VMM

Si vous n’avez qu’un seul serveur VMM vous pouvez répliquer machines virtuelles dans hôtes Hyper-V dans le nuage VMM à [Azure](site-recovery-vmm-to-azure.md) ou à un nuage VMM secondaire. Nous vous recommandons de que la première option, car la réplication entre nuages n’est pas transparente, mais si vous devez précises de cette procédure est ce que vous devez faire :

1. **Configurer VMM sur un ordinateur virtuel Hyper-V**. Lorsque cela, nous nous propose que peut-être vous co-implanter l’instance SQL Server utilisée par VMM sur le même ordinateur virtuel. Cela gagner du temps comme une machine virtuelle doit être créé. Si vous souhaitez utiliser à distance instance de SQL Server et une panne se produise, vous devez récupérer cette instance avant de pouvoir récupérer VMM.
2. **Vérifiez que le serveur VMM comporte au moins deux nuages configurés**. Un nuage contiendra les ordinateurs virtuels vous souhaitez répliquer et l’autres cloud servira de l’emplacement secondaire. Le nuage qui contient les ordinateurs virtuels à protéger doit être conformes aux [conditions préalables](#on-premises-prerequisites).
3. Configurer la récupération de Site comme décrit dans cet article. Créer et enregistrer le serveur VMM dans l’archivage sécurisé, définir une stratégie de réplication et activer la réplication. Vous devez indiquer que la réplication initiale a lieu sur le réseau.
4. Lorsque vous configurez le mappage réseau vous mappez le réseau machine virtuelle pour le nuage primaire au réseau machine virtuelle pour le nuage secondaire.
5. Dans la console Gestionnaire Hyper-V, activer réplica Hyper-V sur l’hôte Hyper-V qui contient le VMM VM et activer la réplication sur l’ordinateur virtuel. Vérifiez que vous n’ajoutez pas la machine virtuelle VMM à nuages qui sont protégés par récupération de Site, pour vous assurer que les paramètres de Hyper-V réplica ne sont pas remplacés par la récupération de Site.
6. Si vous créez des plans de récupération pour le basculement vous utilisez le même serveur VMM pour source et cible.
7. Vous basculer et récupérer comme suit :

    - Basculer manuellement le VMM VM sur le site secondaire à l’aide du Gestionnaire Hyper-V avec un basculement planifié.
    - Basculer vers les ordinateurs virtuels.
    - Une fois que le serveur principal VMM VM a été récupéré, connectez-vous au portail Azure -> Services de récupération vault et exécuter un basculement non planifié des ordinateurs virtuels à partir du site secondaire sur le site principal.
    - Une fois le basculement non planifié terminée, toutes les ressources sont accessibles à partir du site principal à nouveau.


### <a name="create-a-recovery-services-vault"></a>Créer un archivage sécurisé aux Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **Services de récupération**. Vous pouvez également cliquer sur **Parcourir** > **Services de récupération de** chambres fortes > **Ajouter**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Dans la zone **nom** , spécifiez un nom convivial pour identifier l’archivage sécurisé. Si vous avez plusieurs abonnements, sélectionnez un d’eux.
4. [Créer un nouveau groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en une existante et spécifiez une région Azure. Machines seront répliquées à cette zone. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à l’archivage sécurisé du tableau de bord, cliquez sur **Ajouter au tableau de bord** > **créer l’archivage sécurisé**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

L’archivage sécurisé nouveau s’affichent dans le **tableau de bord** > **toutes les ressources**et sur le principaux **Services de récupération de chambres fortes** carte.




## <a name="getting-started"></a>Prise en main

Récupération de site fournit une mise en route expérience qui vous aident à déployer aussi rapidement que possible. Mise en route vérifie les conditions préalables et vous guide tout au long de récupération de Site déploiement les étapes dans l’ordre approprié.

Dans mise en route vous sélectionnez le type d’ordinateurs que vous souhaitez répliquer et l’endroit où vous souhaitez répliquer à. Vous configurez serveurs locale, créez des stratégies de réplication et planifier la capacité. Une fois que vous avez configuré votre infrastructure vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter basculement pour les ordinateurs spécifiques, ou créer des plans de récupération à basculer sur plusieurs ordinateurs.

Commencer mise en route en choisissant la manière dont vous souhaitez déployer récupération de Site. Le flux de mise en route change légèrement selon vos besoins en matière de réplication.

## <a name="step-1-choose-your-protection-goal"></a>Étape 1 : Choisir votre objectif de protection

Sélectionnez ce que vous voulez répliquer et l’endroit où vous souhaitez répliquer à.

1. Dans la carte de **Services de récupération de chambres fortes** Sélectionnez votre l’archivage sécurisé et cliquez sur **paramètres**.
2. Dans **paramètres** > **Prise en main** cliquez sur **Récupération de Site** > **étape 1 : préparer une Infrastructure** > **objectif de Protection**.
3. Dans **l’objectif de Protection** sélectionnez **sur le site de**, puis sélectionnez **Oui, avec Hyper-V**.
4. Sélectionnez **Oui** pour indiquer que vous utilisez VMM pour gérer les hôtes Hyper-V, puis sélectionnez **Oui** si vous avez un serveur VMM secondaire. Si vous déployez réplication entre nuages sur un seul serveur VMM vous cliquez sur **non**. Cliquez ensuite sur **OK**.

    ![Sélectionnez des objectifs](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Installez le fournisseur de récupération de Site Azure sur des serveurs VMM et enregistrer des serveurs dans l’archivage sécurisé.

1. Cliquez sur **étape 2 : préparer Infrastructure** > **Source**.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. Dans **préparer source** cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. Dans la vérification de carte **Ajouter un serveur** qui **System Center VMM serveur** apparaît dans le **type de serveur** et que le serveur VMM répond aux [conditions préalables et configuration de l’URL](#on-premises-prerequisites).
4. Téléchargez le fichier d’installation fournisseur de récupération de Site Azure.
5. Téléchargez la clé d’inscription. Vous devez ceci lorsque vous exécutez le programme d’installation. La clé est valide pour 5 jours après la générer.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Installer le fournisseur de récupération de Site Azure sur le serveur VMM.

> [AZURE.NOTE] Vous ne devez rien installer explicitement sur vos serveurs hôtes Hyper-V.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurer le fournisseur de récupération de Site Azure

1. Exécuter le fournisseur de fichier d’installation sur chaque serveur VMM. Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois l’installer sur un nœud actif et terminer l’installation pour enregistrer le serveur VMM dans l’archivage sécurisé. Installez ensuite le fournisseur sur les autres nœuds. Nœuds de cluster doivent exécuter la même version du fournisseur.
2. Le programme d’installation s’exécute quelques vérifications prerequirement et les demandes d’autorisation pour arrêter le service VMM. Le Service VMM sera redémarré automatiquement lors de la fin de l’installation. Si vous installez sur un cluster VMM que vous serez invité à arrêter le rôle Cluster.

2.  Dans **Microsoft Update,** vous pouvez choisir dans les mises à jour afin que les mises à jour du fournisseur sont installés conformément à votre stratégie Microsoft Update.
3. Dans **Installation** accepter ou modifier l’emplacement d’installation de fournisseur par défaut et cliquez sur **installer**.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Une fois l’installation terminée cliquer sur **Enregistrer** pour enregistrer le serveur dans l’archivage sécurisé.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. Dans la zone **nom de l’archivage sécurisé**, vérifiez le nom de l’archivage sécurisé dans lequel le serveur sera enregistré. Cliquez sur *suivant*.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Dans **Connexion Internet** spécifiez comment le fournisseur en cours d’exécution sur le serveur VMM se connecte à Internet. Sélectionnez **se connecter avec les paramètres de proxy existants** d’utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres d’Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

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

    ![Serveur](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Une fois que le serveur est disponible dans la console de récupération de Site, dans la **Source** > **préparer source** sélectionnez le serveur VMM, puis sélectionnez le cloud dans lequel se trouve l’hôte Hyper-V. Cliquez ensuite sur **OK**.

#### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure peuvent être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur Server Core de Windows Server 2012 R2.

1. Téléchargez la clé de fichier et d’enregistrement de l’installation fournisseur dans un dossier. Par exemple C:\ASR.
2. Arrêter le Service de gestionnaire de Machine virtuelle System Center.
3. À partir d’une invite de commandes avec élévation de privilèges, exécutez ces commandes pour extraire le programme d’installation de fournisseur :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Exécutez la commande suivante pour installer le fournisseur :

        C:\ASR> setupdr.exe /i

5. Puis exécutez ces commandes pour enregistrer le serveur dans l’archivage sécurisé :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Où les paramètres sont :

 - **/Credentials**: paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
 - **/FriendlyName**: paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
 - **/EncryptionEnabled**: paramètre facultatives que vous utilisez uniquement lors de la réplication à partir de VMM à Azure.
 - **/ProxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy.
 - **/ProxyPort**: paramètre facultatif qui spécifie le port du serveur proxy.
 - **/proxyUsername**: paramètre facultatif qui indique le nom d’utilisateur Proxy (si proxy requiert une authentification).
 - **/proxyPassword**: paramètre facultatif qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si proxy requiert une authentification).  

## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Configurer l’environnement cible

Sélectionnez le serveur VMM cible et le cloud.

1. Cliquez sur **préparer infrastructure** > **cible** et sélectionnez le serveur VMM cible que vous voulez utiliser.
2.  Nuages sur le serveur qui sont synchronisés avec récupération de Site seront affiche. Sélectionnez le cloud cible.

    ![Cible](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Configurer les paramètres de réplication

1. Pour créer une nouvelle réplication stratégie, cliquez sur **préparer infrastructure** > **Paramètres de réplication** > **+ créer et associer**.

    ![Réseau](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. Dans **créer et stratégie associer** spécifiez un nom de la stratégie. Le type de source et cible doit être **Hyper-V**.
3. Dans la **version hôte Hyper-V** sélectionnez système d’exploitation est en cours d’exécution sur l’hôte.

    > [AZURE.NOTE] Le nuage VMM peut contenir des hôtes Hyper-V exécutant différentes versions (pris en charge) de Windows Server, mais une stratégie de réplication est appliqués hôtes exécutant le système d’exploitation même. Si vous avez hôtes plus de version d’un système d’exploitation en cours d’exécution puis créer des stratégies de réplication séparée.

4. Dans **type d’authentification** et de **port d’authentification** , spécifiez comment le trafic est authentifié entre les serveurs de l’hôte Hyper-V principaux et de restauration. Sélectionnez le **certificat** à moins d’avoir un environnement Kerberos qui fonctionne. Azure récupération de Site configure automatiquement les certificats pour l’authentification HTTPS. Vous ne devez rien faire manuellement. Par défaut, port 8083 et 8084 (pour les certificats) s’ouvre dans le pare-feu Windows sur les serveurs hôtes Hyper-V. Si vous sélectionnez **Kerberos**, un tickets Kerberos seront utilisées pour l’authentification commun des serveurs hôte. Notez que ce paramètre n’est pertinent pour les serveurs hôtes Hyper-V en cours d’exécution sur Windows Server 2012 R2.
3. Dans la zone **Copier fréquence** spécifier la fréquence à laquelle vous souhaitez répliquer données delta après la réplication initiale (toutes les 30 secondes, 5 ou 15 minutes).
4. Dans la **rétention des points de récupération**, spécifiez heures combien de temps la fenêtre de rétention seront pour chaque point de récupération. Machines protégées peuvent être récupérés à tout moment dans une fenêtre.
6. **Cohérente à l’échelle de l’application** de la fréquence instantané spécifier la fréquence (1 à 12 heures) contenant des instantanés cohérents avec les applications de points de récupération sera créé. Hyper-V utilise deux types d’instantanés — une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérentes avec les applications qui prend un instantané de données de l’application à l’intérieur de la machine virtuelle. Instantanés cohérents avec les applications permet de s’assurer que les applications sont dans un état cohérent lorsque l’instantané Volume Shadow Copy Service (VSS). Notez que si vous activez les instantanés cohérents avec les applications, il affectent les performances des applications qui s’exécutent sur des machines virtuelles source. Vérifiez que la valeur est inférieure à celle du nombre de points de récupération supplémentaires que vous configurez.
7. Dans **la compression de transfert de données** , spécifiez si les données répliquées transférées doivent être compressées.
8. Sélectionnez **Supprimer réplica machine virtuelle** pour indiquer que la machine virtuelle réplica doit être supprimée si vous désactivez la protection de la source de machine virtuelle. Si vous activez ce paramètre, lorsque vous désactivez la protection de la source de machine virtuelle il est supprimé de la console de récupération de Site, les paramètres de récupération de Site pour le moniteur sont supprimés à partir de la console et la réplique est supprimée.
3. Dans **la méthode de réplication Initial** si vous êtes réplication via le réseau spécifiez si vous souhaitez démarrer la réplication initiale ou planifiez quand il doit. Pour enregistrer la bande passante réseau, vous souhaiterez peut-être planifier en dehors de vos heures occupé (e). Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Lorsque vous créez une nouvelle stratégie il a automatiquement associé le nuage VMM. Dans **la stratégie de réplication** cliquez sur **OK**. Vous pouvez associer supplémentaires nuages VMM (et les ordinateurs virtuels qu’ils) à cette stratégie de réplication dans **les paramètres** > **réplication** > nom de la stratégie > **Associer VMM Cloud**.

    ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Préparer pour la réplication initiale en mode hors connexion

Vous pouvez le faire réplication hors connexion pour la copie de données initiales. Vous pouvez le préparer comme suit :

- Sur le serveur source, vous devez spécifier un chemin d’accès à partir de laquelle l’exportation de données exécutée. Affecter un contrôle total des autorisations NTFS et de partage au service VMM sur le chemin d’exportation. Sur le serveur cible, vous devez spécifier un chemin d’accès à partir duquel l’importation de données a lieu. Affecter les mêmes autorisations sur ce chemin d’accès de l’importation.
- Si le chemin d’accès de l’importation ou exportation est partagée, attribuer administrateur, utilisateur Power, opérateur d’impression ou un opérateur de serveur l’appartenance aux groupes pour le compte de service VMM sur l’ordinateur distant sur lequel le partagé se trouve.
- Si vous utilisez tous les comptes d’exécuter en tant que pour ajouter des hôtes, sur les chemins d’accès d’importation et d’exportation, attribuer en lecture et en écriture aux comptes d’exécuter en tant que dans VMM.
- Les actions d’importation et d’exportation ne doivent pas être trouver sur n’importe quel ordinateur utilisé comme serveur hôte Hyper-V, car configuration en boucle n’est pas pris en charge par Hyper-V.
- Dans Active Directory, sur chaque Hyper-V serveur hôte qui contient des machines virtuelles que vous voulez protéger, activer et configurer la délégation contrainte d’approuver les ordinateurs distants sur lesquels les chemins d’accès d’importation et d’exportation sont installées, comme suit :
    1. Sur le contrôleur de domaine, ouvrez **utilisateurs Active Directory et ordinateurs**.
    2. Dans l’arborescence de la console cliquez sur **nom_domaine** > **ordinateurs**.
    3. Cliquez sur le nom du serveur hôte Hyper-V > **Propriétés**.
    4. Sous l’onglet **délégation** , cliquez sur **Approuver cet ordinateur pour la délégation aux services spécifiés**.
    5. Cliquez sur **utiliser n’importe quel protocole d’authentification**.
    6. Cliquez sur **Add** > **utilisateurs et ordinateurs**.
    7. Tapez le nom de l’ordinateur qui héberge le chemin d’exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l’ordinateur qui héberge le chemin d’accès de l’importation. Répétez cette procédure pour les autres serveurs d’hôtes Hyper-V.


### <a name="configure-network-mapping"></a>Configurer le mappage de réseau

Configurer le mappage de réseau entre les réseaux source et cible.

- Le fait de [lecture](#prepare-for-network-mapping) pour un bref aperçu des quels mappage réseau. Dans l’addition de [lecture](site-recovery-network-mapping.md) pour une explication plus approfondie.
- Vérifiez que les machines virtuelles sur des serveurs VMM sont connectés à un réseau machine virtuelle.

Configurer le mappage comme suit :

1. **Paramètres** > **Infrastructure de récupération de Site** > **Mappage du réseau** > **mappages réseau** cliquez sur **+ mappage du réseau**.

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Dans **Ajouter un mappage réseau** onglet Sélectionner la source et cible serveurs VMM. Les réseaux machine virtuelle associés avec les serveurs VMM sont récupérés.
3. Dans le **réseau Source**, sélectionnez le réseau que vous voulez utiliser dans la liste de réseaux machine virtuelle associé au serveur VMM principal.
6. Dans le **réseau cible** , sélectionnez le réseau que vous voulez utiliser sur le serveur VMM secondaire. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Voici que se passe-t-il lorsque commence le mappage réseau :

- N’importe quel réplica des machines virtuelles existantes qui correspondent au réseau machine virtuelle source sera connectés au réseau machine virtuelle cible.
- Nouvelles machines virtuelles qui sont connectés au réseau machine virtuelle source sera connectés au réseau cible mappé après la réplication.
- Si vous modifiez un mappage avec un réseau existant, vous serez connectés machines virtuelles réplica avec les nouveaux paramètres.
- Si le réseau cible comporte plusieurs sous réseaux de ces sous-réseaux ait le même nom que sous-réseau sur lequel se trouve l’ordinateur virtuel source, la machine virtuelle réplica est connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible ayant un nom correspondant, la machine virtuelle est connectée au premier sous-réseau dans le réseau.

### <a name="configure-storage-mapping"></a>Configurer le mappage de stockage
Par défaut lors de la réplication une machine virtuelle sur un serveur source Hyper-V hôte sur un serveur de hôte cible Hyper-V, données répliquées sont stockées dans l’emplacement par défaut qui est indiqué pour l’hôte Hyper-V cible dans le Gestionnaire Hyper-V. Pour contrôler davantage l’emplacement de stockage des données dupliquées, vous pouvez configurer le mappage de stockage<br/><br/> Pour configurer le mappage de stockage, vous devez configurer les classifications de stockage sur la source et cible serveurs VMM avant de commencer le déploiement. Actuellement mappage de stockage à nouveau portail Azure n’est pas pris en charge. Toutefois, elle peut être activée via Powershell. [En savoir plus](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configurer vous pouvez envisager de planification de la capacité et déterminer si vous avez besoin des ressources supplémentaires.

Récupération de site fournit un planificateur de capacité basé sur Excel pour vous aider à allouer les ressources de votre environnement source, composants de récupération de site, mise en réseau et de stockage. Vous pouvez exécuter le planificateur en mode rapide pour estimations selon un nombre moyen de machines virtuelles, des disques et stockage ou en mode détaillé dans laquelle vous allez saisir les chiffres au niveau de la charge de travail. Avant de commencer, vous devez à :

- Collecter les informations relatives à votre environnement de réplication, y compris les machines virtuelles, disques par machines virtuelles et stockage par disque.
- Estimer le taux de modification (évolution du) quotidien qu'avoir des données répliquée delta. Vous pouvez utiliser le [Planificateur de capacité réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour vous aider à procédez comme suit.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lisez l’article intitulé](site-recovery-capacity-planner.md) qui accompagne l’outil.
2.  Lorsque vous avez terminé sélectionnez **Oui** dans **que vous avez exécuté le Planificateur de capacités**?

    ![Planification de la capacité](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Contrôle la bande passante

Une fois que vous avez recueilli des informations de réplication delta en temps réel à l’aide du réplica de Hyper-V de planificateur de capacité, l’outil de planificateur de capacité basé sur Excel vous aide à calculer la bande passante que vous avez besoin de la réplication (initiale et delta). Pour contrôler la quantité de bande passante utilisée pour la réplication, vous pouvez configurer la stratégie de NetQos à l’aide de la stratégie de groupe ou Windows PowerShell. Il existe plusieurs manières vous pouvez le faire :

**PowerShell** | **Plus d’informations**
--- | ---
**Nouvelle - NetQosPolicy « Qualité de service au sous-réseau de destination »** | Limiter le trafic à partir d’un hôte Hyper-V exécutant Windows Server 2012 R2 à un sous-réseau secondaire. Utilisez si vos sous-réseaux principaux et secondaires est différentes.
**Nouvelle - NetQosPolicy « Qualité de service sur le port de destination »** | Limiter le trafic à partir d’un hôte Hyper-V exécutant Windows Server 2012 R2 à un port de destination.
**Nouvelle - NetQosPolicy « Limitation le trafic à partir de VMM »** | Limiter le trafic de vmms.exe. Cela sera limiter le trafic Hyper-V et Live le trafic de Migration. Vous pouvez faire correspondre les ports pour affiner et les protocoles IP.

Vous pouvez utiliser les paramètres d’épaisseur de la bande passante ou limiter le trafic par bits / secondaire. Si vous utilisez un cluster, vous devez effectuer cette opération dans tous les nœuds de cluster. Pour plus d’informations, voir :


- Blog de Thomas Maurer à [La limitation pour le trafic réplica Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Informations supplémentaires sur l' [applet de commande New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Étape 6 : Réplication d’activer

Maintenant, activez la réplication comme suit :

1. Cliquez sur **étape 2 : répliquer application** > **Source**. Une fois que vous avez activé la réplication pour la première fois, vous cliquez sur **+ répliquer** dans l’archivage sécurisé pour activer la réplication pour d’autres ordinateurs.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. Dans la carte **Source** > sélectionnez serveur VMM et le cloud dans lequel se trouvent les hôtes Hyper-V que vous souhaitez répliquer. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Dans la carte **cible** Vérifiez le serveur VMM secondaire et le cloud.
4. Machines **virtuelles** sélectionnez les ordinateurs virtuels à protéger dans la liste.

    ![Activer la protection de la machine virtuelle](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Vous pouvez suivre la progression de l’action **Activer la Protection** dans Paramètres > **travaux** > **tâches de récupération de Site**. Après l’exécution de la tâche de **Protection finaliser** la machine virtuelle est prête pour le basculement.


>[AZURE.NOTE] Vous pouvez également activer la protection des machines virtuelles dans la console. Cliquez sur **Activer la Protection** dans la barre d’outils dans les propriétés de la machine virtuelle > onglet **Récupération de Site Azure** .

Une fois que vous avez activé réplication vous pouvez afficher les propriétés de la machine virtuelle dans **les paramètres** > **Éléments répliquées** > nom de la machine virtuelle. Dans le tableau de bord **Essentials** vous trouverez des informations sur la stratégie de réplication pour la machine virtuelle et son statut. Pour plus d’informations, cliquez sur **Propriétés** .

### <a name="onboard-existing-virtual-machines"></a>Machines virtuelles existantes intégrées

Si vous avez des machines virtuelles existantes dans VMM qui réplication avec Hyper-V réplica vous pouvez intégrée leur protection Azure Site récupération comme suit :

1. Assurez-vous que le serveur Hyper-V qui héberge la machine virtuelle existante se trouve dans le cloud principal et que le serveur Hyper-V qui héberge la machine virtuelle réplica se trouve dans le cloud secondaire.
2. Vérifiez qu’une stratégie de réplication est configurée pour le nuage VMM principal.
2. Activer la réplication de la machine virtuelle principale. Azure récupération de Site et VMM garantit que la machine virtuelle et même hôte réplica est détecté, et Azure Site reprise réutiliser et rétablir la réplication à l’aide de paramètres spécifiés.


## <a name="step-7-test-your-deployment"></a>Étape 7 : Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle, ou créer un plan de récupération qui contient une ou plusieurs machines virtuelles.

### <a name="prepare-for-failover"></a>Préparer pour le basculement

- Pour tester entièrement votre déploiement, vous avez besoin d’une infrastructure pour la machine répliquée fonctionne comme prévu. Si vous souhaitez tester Active Directory et DNS vous pouvez créer une machine virtuelle comme contrôleur de domaine avec le système DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lisez en autres [Considérations relatives à basculement de test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Les instructions fournies dans cet article décrivent comment exécuter un basculement de test avec aucun réseau. Cette option va tester que la machine virtuelle bascule mais ne tester les paramètres de réseau de la machine virtuelle. [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur les autres options.
- Si vous souhaitez exécuter un basculement non planifié au lieu de basculement test Notez les points suivants :

    - Si possible, vous devez fermer vers le bas machines principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas la source et la copie machines en cours d’exécution en même temps.
    - Lorsque vous exécutez un basculement non planifié l’arrêt réplication des données à partir d’ordinateurs principales afin de n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence. En outre si vous exécutez un basculement non planifié sur un plan de récupération il s’exécutera jusqu'à la fin, même si une erreur se produit.




### <a name="run-a-test-failover"></a>Exécuter un basculement de test

1. Basculer sur un ordinateur virtuel unique dans **les paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > **+ basculement de Test**.

    ![Basculement de test](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Basculer sur un plan de récupération, dans **les paramètres** > de**Récupération Plans**, avec le bouton droit de l’offre > **Basculement de Test**. Pour créer une récupération planifier [Suivez ces instructions](site-recovery-create-recovery-plans.md).
2. Dans le **Basculement de Test**, sélectionnez **Aucun**. Avec cette option vous testez la machine virtuelle bascule comme prévu, mais la machine virtuelle répliquée n’est pas connectée à un réseau.

    ![Basculement de test](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Cliquez sur **OK** pour lancer le basculement. Vous pouvez suivre la progression en cliquant sur l’ordinateur virtuel pour ouvrir ses propriétés ou sur le travail de **Test basculement** dans **paramètres** > **travaux** > **tâches de récupération de Site**.
4. Lorsque la tâche basculement atteint la phase de **test achevé** , procédez comme suit :

    -  Afficher le réplica machine virtuelle dans le nuage VMM secondaire.
    -  Cliquez sur **Terminer le test** pour terminer le basculement de test.
    -  Cliquez sur **Notes** pour enregistrer et enregistrer des observations associées le basculement de test.

5. La machine virtuelle test sont créée sur le même hôte que l’hôte sur lequel se trouve la machine virtuelle réplica. Il est ajouté dans le cloud même dans lequel se trouve la machine virtuelle réplica.
6. Après avoir vérifié qui commencent machines virtuelles correctement cliquez sur **le basculement de test est terminé**. À ce stade tous les éléments créés automatiquement par récupération de Site pendant le basculement de test sont supprimés.  

    > [AZURE.NOTE] Si un basculement test persiste plus de deux semaines qu’elle est terminée en vigueur.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Mettre à jour de DNS avec l’adresse IP VM réplica

Après le basculement le réplica machine virtuelle n’ait pas la même adresse IP que la machine virtuelle principale.

- Machines virtuelles met à jour le serveur DNS qu’ils utilisent mise en route.
- Vous pouvez également mettre à jour manuellement DNS comme suit :

#### <a name="retrieve-the-ip-address"></a>Récupérer l’adresse IP

Exécuter cet exemple de script pour récupérer l’adresse IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Mettre à jour DNS

Exécuter cet exemple de script pour mettre à jour DNS, spécifiez l’adresse IP récupérées à l’aide de l’exemple de script précédent.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Étapes suivantes

Une fois votre déploiement est configuré et en cours d’exécution, [pour en savoir plus](site-recovery-failover.md) sur les différents types de basculement.
