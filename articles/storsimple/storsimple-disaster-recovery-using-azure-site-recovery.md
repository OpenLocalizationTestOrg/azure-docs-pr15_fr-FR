<properties 
   pageTitle="Automatiser DR pour les partages de fichiers sur StorSimple à l’aide de la récupération de Site Azure | Microsoft Azure"
   description="Décrit les étapes et les meilleures pratiques pour la création d’une solution de récupération d’urgence partages de fichiers hébergés sur StorSimple stockage."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solution de sinistre automatisée à l’aide de la récupération de Site Azure partages de fichiers hébergés sur StorSimple

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure StorSimple est une solution de stockage cloud hybride qui répond à la complexité des données non structurées associées fréquemment partages de fichiers. StorSimple utilise le stockage cloud comme une extension de la solution locale et automatiquement les données de niveaux sur local et stockage cloud. Intégré de protection des données, à local et dans le cloud instantanés, élimine la nécessité d’une infrastructure de stockage immense.

[Récupération de Site Azure](../site-recovery/site-recovery-overview.md) est un service basé sur Azure qui fournit les fonctionnalités de reprise de sinistre en organiser réplication, basculement et la restauration des machines virtuelles. Récupération de Site Azure prend en charge un certain nombre de technologies de réplication afin de manière cohérente à travers répliquer, protéger et en toute transparence basculer machines virtuelles et des applications à nuages hébergés ou public/privé.

À l’aide de récupération de Site Azure, la réplication de la machine virtuelle et fonctionnalités de capture instantanée StorSimple cloud, vous pouvez protéger l’environnement du serveur complet du fichier. En cas d’interruption, vous pouvez utiliser un simple clic pour avancer vos partages de fichiers en ligne Azure en quelques minutes.

Ce document explique en détail comment vous pouvez créer une solution de récupération d’urgence pour vos partages de fichiers hébergés sur un stockage StorSimple et effectuer planifiée, et non planifiée et tester le basculement à l’aide d’un plan de récupération d’un seul clic. En résumé, elle indique comment vous pouvez modifier le Plan de récupération dans votre l’archivage sécurisé de récupération de Site Azure pour permettre le basculement StorSimple lors de scénarios d’urgence. En outre, il décrit les configurations prises en charge et les conditions préalables. Ce document suppose que vous connaissez les concepts de base des architectures de récupération de Site Azure et StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Options de déploiement de récupération de Site Azure prises en charge

Clients pouvant déployer des serveurs de fichiers en tant que serveurs physiques ou machines virtuelles (machines virtuelles) s’exécutant sur Hyper-V ou VMware, puis créez des partages de fichiers à partir des volumes coupées StorSimple stockage. Récupération de Site Azure pouvez protéger déploiements physiques et virtuelles à soit un site secondaire ou sur Azure. Ce document traite des détails d’une solution DR avec Azure que le site de récupération pour un serveur de fichiers que machine virtuelle hébergé sur Hyper-V et les partages de fichiers de stockage StorSimple. Autres scénarios dans lesquels le serveur de fichiers machine virtuelle se trouve sur un VMware VM ou une machine physique peuvent être implémentés de la même manière.

## <a name="prerequisites"></a>Conditions préalables

Implémentation d’une solution de récupération après sinistre un seul clic qui utilise récupération de Site Azure partages de fichiers hébergés sur StorSimple stockage comporte les conditions préalables suivantes :

-   Serveur de Windows Server 2012 R2 fichier que machine virtuelle hébergé sur Hyper-V ou VMware ou une machine physique local

-   StorSimple stockage appareil local enregistré avec le Gestionnaire de StorSimple Azure

-   Équipement de Cloud StorSimple créé dans le Gestionnaire de StorSimple Azure (Cela peut être conservée dans état arrêt)

-   Partages de fichiers hébergés sur volumes configurés sur le périphérique de stockage StorSimple

-   [L’archivage sécurisé des services de récupération de Site azure](../site-recovery/site-recovery-vmm-to-vmm.md) créé dans un abonnement Microsoft Azure

En outre, si Azure est votre site de récupération, exécutez l' [outil d’évaluation de la compatibilité avec Azure Machine virtuelle](http://azure.microsoft.com/downloads/vm-readiness-assessment/) sur machines virtuelles pour vous assurer qu’elles sont compatibles avec les services machines virtuelles Azure et de récupération de Site Azure.

Pour éviter les problèmes (ce qui peuvent entraîner une hausse des coûts), assurez-vous que vous créez votre équipement de Cloud StorSimple, compte automation et le stockage de latence compte (s) dans la même région.

## <a name="enable-dr-for-storsimple-file-shares"></a>Activer DR StorSimple partages de fichiers  

Chaque composant de l’environnement local doit être protégé pour activer la restauration et la réplication complète. Cette section décrit comment :

-   Configurer la réplication Active Directory et DNS (facultatif)

-   Utiliser la récupération de Site Azure pour activer la protection de la mémoire virtuelle du serveur de fichiers

-   Activer la protection des volumes StorSimple

-   Configuration du réseau

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurer la réplication Active Directory et DNS (facultatif)

Si vous voulez protéger les ordinateurs qui exécutent Active Directory et DNS afin qu’ils soient disponibles sur le site DR, vous devez les protéger explicitement (de sorte que les serveurs de fichiers sont accessibles après basculement avec l’authentification). Il existe deux options recommandées en fonction de la complexité de l’environnement du client local.

#### <a name="option-1"></a>Option 1

Si le client possède un petit nombre d’applications, un seul contrôleur de domaine pour l’intégralité de site local et défectueux sur l’ensemble du site, nous vous recommandons d’utilisation de la réplication de récupération de Site Azure répliquer l’ordinateur contrôleur de domaine sur un site secondaire (cela s’applique aux site à et site-Azure).

#### <a name="option-2"></a>Option 2

Si le client possède un grand nombre d’applications, une forêt Active Directory est en cours d’exécution et basculement quelques applications à la fois, puis nous vous recommandons de configurer un contrôleur de domaine supplémentaires sur le site DR (soit un site secondaire ou dans Azure).

Reportez-vous à la [solution automatisée DR pour Active Directory et DNS à l’aide de la récupération de Site Azure](../site-recovery/site-recovery-active-directory.md) pour obtenir des instructions lors de la disposition d’un contrôleur de domaine sur le site DR. Pour le reste de ce document, nous supposons qu'un contrôleur de domaine n’est disponible sur le site DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Utiliser la récupération de Site Azure pour activer la protection de la mémoire virtuelle du serveur de fichiers

Cette étape nécessite que vous préparez l’environnement de serveur du fichier en local, créez et préparez un archivage sécurisé récupération de Site Azure et activez la protection des fichiers de la machine virtuelle.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Pour préparer l’environnement de serveur du fichier en local

1.  Définir le **contrôle de compte d’utilisateur** pour **ne jamais m’avertir**. Cela est nécessaire afin que les scripts d’automatisation Azure permet de connecter les cibles iSCSI après fail par récupération de Site Azure.

    1.  Appuyez sur la touche Windows + Q et recherche de **compte d’utilisateur**.

    2.  Sélectionnez **paramètres de modifier le contrôle de compte d’utilisateur**.

    3.  Faites glisser la barre vers le bas vers **Ne jamais m’avertir**.

    4.  Cliquez sur **OK** , puis sélectionnez **Oui** lorsque vous y êtes invité.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Installer l’Agent machine virtuelle sur chacune des machines virtuelles du serveur de fichiers. Cela est nécessaire pour que vous pouvez exécuter des scripts d’automatisation Azure sur l’échec sur machines virtuelles.

    1.  [Téléchargez l’agent](http://aka.ms/vmagentwin) sur `C:\\Users\\<username>\\Downloads`.

    2.  Ouvrir Windows PowerShell en mode administrateur (exécuter en tant qu’administrateur) et entrez la commande suivante pour accéder à l’emplacement de téléchargement :

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] Le nom de fichier peut changer en fonction de la version.

1.  Cliquez sur **suivant**.

2.  Acceptez les **Termes du contrat** , puis sur **suivant**.

3.  Cliquez sur **Terminer**.


1.  Créer des partages de fichiers à l’aide des volumes coupées StorSimple stockage. Pour plus d’informations, voir [utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes.md).

    1.  Sur vos ordinateurs virtuels en local, appuyez sur la touche Windows + Q et recherche pour **iSCSI**.

    2.  Sélectionnez **initiateur iSCSI**.

    3.  Sélectionnez l’onglet **Configuration** et copiez le nom.

    4.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/).

    5.  Sélectionnez l’onglet **StorSimple** , puis sélectionnez le Manager StorSimple Service qui contient le périphérique physique.

    6.  Créez le volume conteneur (s), puis créez ou les volumes. (Ces volumes sont pour les partages de fichiers sur le serveur de fichiers machines virtuelles). Copier le nom et donnez un nom approprié pour les enregistrements de contrôle d’accès lorsque vous créez les volumes.

    7.  Sélectionnez l’onglet **configurer** et note vers le bas de l’adresse IP de l’appareil.

    8.  Sur vos ordinateurs locaux virtuels, accédez à l' **initiateur iSCSI** à nouveau et entrez l’adresse IP dans la section connexion rapide. Cliquez sur **Connexion rapide** (le périphérique doit maintenant être connecté).

    9.  Ouvrez le portail de gestion Azure et sélectionnez l’onglet **Volumes et périphériques** . Cliquez sur **configuration automatique**. Le volume que vous venez de créer doit apparaître.

    10. Dans le portail, sélectionnez l’onglet **périphériques** , puis sélectionnez **créer un nouvel appareil virtuel.** (Cet appareil virtuel sera utilisé si un basculement). Ce nouvel appareil virtuel peut être conservé dans un état en mode hors connexion afin d’éviter les frais supplémentaires. Pour mettre le périphérique virtuel hors connexion, accédez à la section **Machines virtuelles** sur le portail et fermez-le.

    11. Revenez aux machines virtuelles en local et ouvrez Gestion des disques (appuyez sur la touche Windows + X et sélectionnez **Gestion des disques**).

    12. Vous remarquerez certains disques supplémentaires (selon le nombre de volumes que vous avez créé). Avec le bouton droit de la première partie, sélectionnez **Initialisation disque**, puis sélectionnez **OK**. Avec le bouton droit de la section **non alloué** , sélectionnez **Nouveau Volume Simple**, lui attribuer une lettre de lecteur et terminer l’Assistant.

    13. Répétez l’étape l pour tous les disques. Vous pouvez maintenant voir tous les disques sur **Ce PC** dans l’Explorateur Windows.

    14. Le rôle Services de fichiers et stockage permet de créer des partages de fichiers sur ces volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Créer et préparer un archivage sécurisé récupération de Site Azure

Consultez la [documentation de récupération de Site Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) mise en route récupération de Site Azure avant de protéger le serveur de fichiers machine virtuelle.

#### <a name="to-enable-protection"></a>Pour activer la protection

1.  Déconnexion de l’ou les cibles iSCSI d’ordinateurs locaux virtuels que vous souhaitez protéger par la récupération de Site Azure :

    1.  Appuyez sur touche Windows + Q et effectuez une recherche **iSCSI**.

    2.  Sélectionnez **configurer initiateur iSCSI**.

    3.  Déconnecter le périphérique StorSimple déjà connecté. Par ailleurs, vous pouvez basculer désactiver le serveur de fichiers pour quelques minutes lors de l’activation de la protection.

    > [AZURE.NOTE] Cela entraînera les partages de fichiers temporairement indisponible

1.  [Activer la protection de la machine virtuelle](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) au serveur de fichiers machine virtuelle à partir du portail de récupération de Site Azure.

2.  Au début de la synchronisation initiale, vous pouvez reconnecter à nouveau la cible. Accédez à l’initiateur iSCSI, sélectionnez l’appareil StorSimple, puis cliquez sur **se connecter**.

3.  Lorsque la synchronisation est terminée et l’état de la machine virtuelle est **protégé**, sélectionnez la machine virtuelle, sélectionnez l’onglet **configurer** et mettre à jour le réseau de la machine virtuelle en conséquence (il s’agit du réseau qui l’a échoué sur VM(s) faire partie d’une). Si le réseau n’apparaît pas, cela signifie que la synchronisation est toujours va.

### <a name="enable-protection-of-storsimple-volumes"></a>Activer la protection des volumes StorSimple

Si vous n’avez pas sélectionné l’option **activer une sauvegarde par défaut pour ce volume** pour les volumes StorSimple, accédez aux **Stratégies de sauvegarde** dans le service Manager StorSimple et créer une stratégie de sauvegarde appropriée pour tous les volumes. Nous vous recommandons de définir la fréquence des sauvegardes sur la récupération point l’objectif que vous voulez voir pour l’application.

### <a name="configure-the-network"></a>Configuration du réseau

Pour le serveur de fichiers machine virtuelle, configurer les paramètres réseau dans Azure Site récupération afin que les réseaux machine virtuelle sont joints au réseau DR approprié après le basculement.

Vous pouvez sélectionner la machine virtuelle dans le **Nuage VMM** ou le **Groupe de Protection** pour configurer les paramètres réseau, comme le montre l’illustration suivante.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

Vous pouvez créer un plan de récupération de récupération automatique du système pour automatiser le processus de basculement des partages de fichiers. En cas de panne, vous pouvez mettre les partages de fichiers en quelques minutes avec un seul clic. Pour activer cette automatisation, vous avez besoin d’un compte Azure automation.

#### <a name="to-create-the-account"></a>Pour créer le compte

1.  Accédez au portail classique Azure et accédez à la section **automatisation** .

1.  Créer un nouveau compte automation. Conserver dans le même geo/la région dans lequel les comptes de stockage et l’équipement de Cloud StorSimple ont été créés.

2.  Cliquez sur **Nouveau** &gt; **Application Services** &gt; **Automation** &gt; **Runbook** &gt; **De la galerie** pour importer toutes les procédures opérationnelles requis dans le compte d’automatisation.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Ajoutez les procédures suivantes opérationnelles à partir du volet **Sinistre** dans la galerie :

    -   Basculer les conteneurs de volume StorSimple

    -   Nettoyer des volumes StorSimple après Test basculement (TFO)

    -   Montage de volumes sur appareil StorSimple après le basculement

    -   Démarrer le matériel virtuel StorSimple

    -   Désinstaller l’extension de script personnalisé dans Azure machine virtuelle

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Publier tous les scripts en sélectionnant la runbook dans le compte d’automatisation et en accédant à l’onglet **auteur** . Une fois cette étape, l’onglet **procédures opérationnelles** s’affiche comme suit :

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  Dans le compte d’automatisation accédez à l’onglet **ressources** , cliquez sur **Ajouter un paramètre** &gt; **Ajouter les informations d’identification**et ajoutez vos informations d’identification Azure – name du bien AzureCredential.

    Utilisez les informations d’identification de Windows PowerShell. Il s’agit des informations d’identification qui contient un nom d’utilisateur ID d’organisation et d’un mot de passe ayant accès à cet abonnement Azure et avec l’authentification multifacteur désactivé. Cela est nécessaire pour authentifier au nom de l’utilisateur pendant le basculement et pour faire apparaître les volumes de serveur de fichiers sur le site DR.

1.  Dans le compte d’automatisation, sélectionnez l’onglet **éléments de** , puis sur **Ajouter un paramètre** &gt; **variable ajouter** et ajoutez les variables suivantes. Vous pouvez choisir chiffrer ces ressources. Ces variables sont récupération plan spécifiques. Si votre récupération planifier (que vous allez créer dans l’étape suivante) nom est plan de test, puis vos variables doivent être StorSimRegKey de plan de test, plan de test AzureSubscriptionName et ainsi de suite.

    -   *RecoveryPlanName* **-StorSimRegKey**: la clé d’inscription pour le service Manager StorSimple.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: le nom de l’abonnement Azure.

    -   *RecoveryPlanName* **-ResourceName**: le nom de la ressource StorSimple qui comporte le périphérique StorSimple.

    -   *RecoveryPlanName* **Nom de périphérique-**: le périphérique qui doit être basculé.

    -   *RecoveryPlanName* **-TargetDeviceName**: l’équipement de Cloud StorSimple sur lesquels les conteneurs doivent être basculé.

    -   *RecoveryPlanName* **-VolumeContainers**: une chaîne séparées par des virgules des conteneurs de volume présents sur le périphérique qui doivent être basculé ; par exemple, volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: le nom du service de l’équipement cible (cela se trouve dans la section **Machine virtuelle** : le nom du service est identique à celui DNS).

    -   *RecoveryPlanName* **-StorageAccountName**: le nom du compte de stockage dans lequel le script (qui doit s’exécuter sur l’échec sur machine virtuelle) sera stocké. Cela peut être n’importe quel compte de stockage qui comporte l’espace pour stocker temporairement le script.

    -   *RecoveryPlanName* **-StorageAccountKey**: touche d’accès rapide pour le compte de stockage ci-dessus.

    -   *RecoveryPlanName* **-ScriptContainer**: le nom du conteneur dans lequel le script sera stocké dans le cloud. Si le conteneur n’existe pas, il sera créé.

    -   *RecoveryPlanName* **-VMGUIDS**: lors de la protection une machine virtuelle, récupération de Site Azure affecte chaque machine virtuelle un ID unique qui donne les détails de l’échec de la mémoire virtuelle. Pour obtenir la VMGUID, sélectionnez l’onglet **Services de récupération** et puis cliquez sur **L’élément protégé** &gt; **Groupes de Protection** &gt; **Machines** &gt; **Propriétés**. Si vous avez plusieurs ordinateurs virtuels, puis ajoutez les GUID sous forme de chaîne séparées par des virgules.

    -   *RecoveryPlanName* **-AutomationAccountName** – le nom du compte automation dans lequel vous avez ajouté les procédures opérationnelles et les biens.

    Par exemple, si le nom de la planification de récupération est fileServerpredayRP, puis vos **biens** onglet doit se présenter comme suit après avoir ajouté toutes les ressources.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Accédez à la section **Services de récupération** et sélectionnez l’archivage sécurisé de récupération de Site Azure que vous avez créée.

2.  Sélectionnez l’onglet **Récupération Plans** et créez une nouvelle offre de récupération comme suit :

    un.  Spécifiez un nom et sélectionnez le **Groupe de Protection**approprié.

    b.  Sélectionnez les ordinateurs virtuels dans le groupe de protection que vous souhaitez inclure dans le plan de récupération.

    c.  Après la récupération plan est créé, sélectionnez-la pour ouvrir le mode de personnalisation de plan de récupération.

    d.  Sélectionnez **Arrêter le système tous les groupes**, cliquez sur **Script**et cliquez sur **Ajouter un script côté primaire avant l’arrêt du groupe tous les**.

    e.  Sélectionnez le compte d’automatisation (dans lequel vous avez ajouté les procédures opérationnelles), puis sélectionnez le runbook **échouer conteneurs over-StorSimple-Volume** .

    f.  Cliquez sur **groupe 1 : Démarrer**, choisissez **Machines virtuelles**, et ajoutez les ordinateurs virtuels qui doivent être protégées dans le plan de récupération.

    g.  Cliquez sur **groupe 1 : Démarrer**, sélectionnez **le Script**et ajouter tous les scripts suivants dans l’ordre comme suit **après groupe 1** .

    - Début-StorSimple-virtuel-matériel runbook
    - Échec runbook conteneurs over-StorSimple-volume
    - Montage volumes après basculement runbook
    - Runbook désinstaller--script-extension personnalisée

1.  Ajouter une action manuelle après les 4 scripts ci-dessus dans la même **groupe 1 : étapes postérieures à la** section. Cette action est le point auquel vous pouvez vérifier que tout fonctionne correctement. Cette action doit être ajouté uniquement dans le cadre de test basculement (donc uniquement le **Basculement tester** case à cocher Sélectionner).

2.  Après l’action manuelle, ajoutez le script de nettoyage à l’aide de la même procédure que vous avez utilisé pour les autres procédures opérationnelles. Enregistrez le plan de récupération.

    > [AZURE.NOTE] Lorsque vous exécutez un basculement de test, vous devez vérifier tous les éléments à l’étape action manuelle, car les volumes StorSimple qui avaient été dupliquées sur le périphérique cible seront supprimés dans le cadre du nettoyage après que l’action manuelle est terminée.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Effectuez un test basculement

Reportez-vous au guide le Guide [Active Directory DR Solution](../site-recovery/site-recovery-active-directory.md) les notions spécifiques à Active Directory pendant le basculement de test. Le programme d’installation locale est dérangé pas du tout lorsque le basculement de test. Les volumes StorSimple qui ont été associées à la machine virtuelle locaux sont cloner à l’application de Cloud StorSimple sur Azure. Une machine virtuelle à des fins de test s’affiche dans Azure et les volumes clonés sont joints à la machine virtuelle.

#### <a name="to-perform-the-test-failover"></a>Pour effectuer le basculement de test

1.  Dans le portail classique Azure, sélectionnez votre l’archivage sécurisé de récupération de site.

1.  Cliquez sur le plan de récupération créé pour le serveur de fichiers machine virtuelle.

2.  Cliquez sur **basculer de Test**.

3.  Sélectionnez le réseau virtuel pour démarrer le processus de basculement test.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer votre validations.

2.  Une fois les validations terminées, cliquez sur **Validations terminée**. L’environnement de test de basculement est supprimé, et l’opération TFO sera terminée.

## <a name="perform-an-unplanned-failover"></a>Effectuer un basculement non planifié

Pendant un basculement non planifié, les volumes StorSimple sont basculés vers le périphérique virtuel, un réplica machine virtuelle seront vers le haut sur Azure et les volumes sont joints à la machine virtuelle.

#### <a name="to-perform-an-unplanned-failover"></a>Pour effectuer un basculement non planifié

1.  Dans le portail classique Azure, sélectionnez votre l’archivage sécurisé de récupération de site.

1.  Cliquez sur le plan de récupération créé pour serveur de fichiers machine virtuelle.

2.  Cliquez sur **Basculer** , puis sélectionnez **Basculement non planifié**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Sélectionnez le réseau cible, puis sur l’icône ✓ pour démarrer le processus de basculement.

## <a name="perform-a-planned-failover"></a>Effectuer un basculement planifié

Pendant un basculement planifié, les locaux serveur de fichiers que machine virtuelle est arrêté normalement et un nuage sauvegarde instantané des volumes sur appareil StorSimple. Les volumes StorSimple sont basculés vers le périphérique virtuel, un réplica machine virtuelle s’affiche sur Azure et les volumes sont joints à la machine virtuelle.

#### <a name="to-perform-a-planned-failover"></a>Pour effectuer un basculement planifié

1.  Dans le portail classique Azure, sélectionnez votre l’archivage sécurisé de récupération de site.

1.  Cliquez sur le plan de récupération créé pour le serveur de fichiers machine virtuelle.

2.  Cliquez sur **Basculer** , puis sélectionnez **Basculement planifiée**.

3.  Sélectionnez le réseau cible, puis sur l’icône ✓ pour démarrer le processus de basculement.

## <a name="perform-a-failback"></a>Réaliser un retour arrière

Pendant un retour arrière, conteneurs de volume StorSimple sont basculées revenir à l’appareil physique depuis une sauvegarde.

#### <a name="to-perform-a-failback"></a>Pour effectuer un retour arrière

1.  Dans le portail classique Azure, sélectionnez votre l’archivage sécurisé de récupération de site.

1.  Cliquez sur le plan de récupération créé pour le serveur de fichiers machine virtuelle.

2.  Cliquez sur **Basculer** et sélectionnez **basculement planifiée** ou **basculement non planifié**.

3.  Cliquez sur **Modifier l’orientation**.

4.  Sélectionnez les options de la création de machine virtuelle et la synchronisation des données appropriées.

5.  Cliquez sur l’icône ✓ pour démarrer le processus de retour arrière.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Meilleures pratiques

### <a name="capacity-planning-and-readiness-assessment"></a>Évaluation des capacités planification et préparation


#### <a name="hyper-v-site"></a>Site Hyper-V

Utiliser l' [outil de planification des capacités de l’utilisateur](http://www.microsoft.com/download/details.aspx?id=39057) pour créer le serveur, le stockage et l’infrastructure réseau pour votre environnement réplica Hyper-V.

#### <a name="azure"></a>Azure

Vous pouvez exécuter l' [outil d’évaluation de la compatibilité avec Azure Machine virtuelle](http://azure.microsoft.com/downloads/vm-readiness-assessment/) sur machines virtuelles pour vous assurer qu’elles sont compatibles avec Azure machines virtuelles et des Services de récupération de Site Azure. L’outil d’évaluation de la compatibilité avec vérifie les configurations de la machine virtuelle et vous avertit lorsque les configurations ne sont pas compatibles avec Azure. Par exemple, il émet un avertissement si un lecteur C: est supérieur à 127 Go.


Planification de la capacité est composée d’au moins deux processus importants :

-   Mappage local machines virtuelles Hyper-V aux tailles machine virtuelle Azure (par exemple, A6, A7, A8 et A9).

-   Détermination de la bande passante Internet requise.

## <a name="limitations"></a>Limitations

- Pour l’instant, seule 1 StorSimple appareil peut être basculé (pour une solution de Cloud StorSimple unique). Le scénario d’un serveur de fichiers qui s’étend sur plusieurs appareils StorSimple n’est pas encore pris en charge.

- Si vous obtenez une erreur lors de l’activation de la protection d’un ordinateur virtuel, vérifiez que vous avez déconnecté les cibles iSCSI.

- Tous les conteneurs de volume qui ont été regroupés en raison des règles de sauvegarde couvrant entre les conteneurs de volume seront alors basculées ensemble.

- Tous les volumes dans les conteneurs de volume que vous avez choisi seront alors basculées.

- Volumes dont la somme correspond plus de 64 To ne peut pas être basculées, car la capacité maximale d’un appareil de Cloud StorSimple unique est 64 To.

- Si le basculement planifiés échoue et les ordinateurs virtuels créés dans Azure, puis ne pas nettoyer les ordinateurs virtuels. À la place, effectuez un retour arrière. Si vous supprimez les ordinateurs virtuels puis les ordinateurs locaux virtuels ne peut pas être activés à nouveau.

- Après un basculement, si vous n’êtes pas en mesure de voir les volumes, accédez aux machines virtuelles ouvrez Gestion des disques, analyser les disques et les mettre en ligne.

- Dans certains cas, les lettres des lecteurs du site DR peuvent être différents les lettres en local. Dans ce cas, vous devrez corriger le problème manuellement lorsque le basculement est terminé.

- L’authentification multifacteur doit être désactivée pour les informations d’identification Azure sont entrée dans le compte automation en tant qu’actif. Si ce type d’authentification n’est pas désactivé, les scripts ne pas être autorisés à exécution automatique et le plan de récupération échouera.

- Délai d’attente basculement : StorSimple le script expire si le basculement de conteneurs de volume prend plus de temps à la limite de récupération de Site Azure par script (actuellement 120 minutes).

- Délai d’expiration du travail de sauvegarde : StorSimple le script arrive à expiration si la sauvegarde des volumes prend plus de temps que la limite de récupération de Site Azure par script (actuellement 120 minutes).
 
    > [AZURE.IMPORTANT] Exécuter la sauvegarde manuellement à partir du portail Azure, puis exécutez à nouveau le plan de récupération.

- Cloner délai d’attente : StorSimple le script arrive à expiration si clonant des volumes prend plus de temps que la limite de récupération de Site Azure par script (actuellement 120 minutes).

- Erreur de synchronisation de temps : StorSimple les scripts erreurs arrière indiquant que les sauvegardes ont échoué alors que la sauvegarde a réussi dans le portail. Des causes possibles pour cette peut-être que heure de l’application StorSimple peut-être être synchronisé avec l’heure actuelle dans le fuseau horaire.
 
    > [AZURE.IMPORTANT] Synchroniser l’heure de matériel avec l’heure actuelle dans le fuseau horaire.

- Erreur de basculement matériel : StorSimple le script peut échouer s’il existe une bascule lorsque le plan de récupération est en cours d’exécution.
    
    > [AZURE.IMPORTANT] Exécutez à nouveau le plan de récupération après que le basculement d’application est terminé.

## <a name="summary"></a>Résumé

Utilisation de la récupération de Site Azure, vous pouvez créer un plan de récupération automatique après sinistre complète pour un serveur de fichiers machine virtuelle ayant des partages de fichiers hébergés sur StorSimple stockage. Vous pouvez lancer le basculement quelques secondes depuis n’importe où dans le cas d’une interruption de service et obtenir l’application opérationnel en quelques minutes.
