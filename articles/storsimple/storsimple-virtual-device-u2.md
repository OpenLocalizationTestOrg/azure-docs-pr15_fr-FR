<properties 
   pageTitle="Périphérique virtuel StorSimple mise à jour 2 | Microsoft Azure"
   description="Découvrez comment créer, déployer et gérer un périphérique virtuel StorSimple dans un réseau virtuel Microsoft Azure. (S’applique à la mise à jour de StorSimple 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Déployer et gérer un périphérique virtuel StorSimple dans Azure


##<a name="overview"></a>Vue d’ensemble
Le périphérique virtuel de série 8000 StorSimple est une fonctionnalité supplémentaire est fourni avec votre solution Microsoft Azure StorSimple. Le périphérique virtuel StorSimple s’exécute sur une machine virtuelle dans un réseau virtuel Microsoft Azure, et vous pouvez l’utiliser pour sauvegarder et cloner des données à partir de vos hôtes. Ce didacticiel décrit comment déployer et gérer un périphérique virtuel dans Azure et est inférieure et applicables à tous les appareils virtuels version du logiciel 2 mise à jour en cours d’exécution.


#### <a name="virtual-device-model-comparison"></a>Comparaison de modèles de périphérique virtuel

Le périphérique virtuel StorSimple est disponible en deux modèles, un 8010 standard (auparavant appelé le 1100) et une prime 8020 (introduite dans mise à jour 2). Une comparaison des deux modèles est sous forme de tableau en dessous.


| Modèle d’appareil          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacité maximale**      | 30 TO                                                                     | 64 TO                                                                                                                                |
| **Machine virtuelle Azure**              | Standard_A3 (4 cœurs, 7 Go de mémoire)                                                                      | Standard_DS3 (4 cœurs, 14 Go de mémoire)                                                                                                                          |
| **Compatibilité des versions** | Dans les versions exécutant jour préalable 2 ou version ultérieure                                             | Dans les versions exécutant mise à jour 2 ou version ultérieure                                                                                                  |
| **Disponibilité dans la région**   | Toutes les régions Azure                                                         | Azure régions qui prennent en charge le stockage Premium<br></br>Pour obtenir la liste des régions, voir [prise en charge des régions pour 8020](#supported-regions-for-8020) |
| **Type de stockage**          | Utilise le stockage Standard Azure pour disques locaux<br></br> Découvrez comment [créer un compte de stockage Standard]() | Utilise le stockage Azure Premium pour disques locaux<sup>2</sup> <br></br>Découvrez comment [créer un compte de stockage Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Conseils de charge de travail**     | Extraction d’éléments au niveau des fichiers à partir de sauvegardes                                              | Développement en nuage et tester les scénarios, faible latence, les charges de travail performances supérieurs <br></br>Unité secondaire de sinistre                                                                                            |
 
<sup>1</sup> *auparavant connu sous le 1100*.

<sup>2</sup> *la 8010 et 8020 utiliser Azure le stockage Standard pour la couche cloud. La différence n’existe dans le niveau local au sein de l’appareil*.

#### <a name="supported-regions-for-8020"></a>Régions pris en charge pour 8020

Les zones de stockage Premium sont pris en charge pour 8020 sont sous forme de tableau en dessous. Cette liste met à jour en permanence que stockage Premium est disponible dans les autres régions. 

| S. Aucune.                                                  | Actuellement pris en charge dans les régions |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | États-Unis centre                     |
| 2                                                       |  États-Unis Extrême-Orient                       |
| 3                                                       |  États-Unis Extrême-Orient 2                     |
| 4                                                       | États-Unis Ouest                        |
| 5                                                       | Europe du Nord                   |
| 6                                                       | Europe occidentale                    |
| 7                                                       | Pays d’Asie du Sud-est                 |
| 8                                                       | Orient Japon                     |
| 9                                                       | Japon ouest                     |
| 10                                                      | Australie est                 |
| 11                                                      | Australie sud-est *           |
| 12                                                      | * Asie de l’est                     |
| 13                                                      | Sud américain Central *              |

* Stockage premium a été lancé récemment dans ces zones géographiques.

Cet article décrit le processus étape par étape du déploiement d’un périphérique virtuel StorSimple dans Azure. Après la lecture de cet article, vous pourrez :

- Comprendre comment le périphérique virtuel diffère de l’appareil physique.

- Pouvoir créer et configurer le périphérique virtuel.

- Se connecter à l’appareil virtuel.

- Apprendre à utiliser avec le périphérique virtuel.

Ce didacticiel s’applique à tous les appareils virtuels StorSimple mise à jour en cours d’exécution 2 ou version ultérieure. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Comment le périphérique virtuel diffère de l’appareil physique

Le périphérique virtuel StorSimple est un logiciel seule StorSimple qui s’exécute sur un seul nœud dans une Machine virtuelle de Microsoft Azure. Le périphérique virtuel prend en charge les scénarios de récupération d’urgence dans lequel votre périphérique physique n’est pas disponible et est approprié pour une utilisation dans récupération au niveau des éléments à partir de sauvegardes, sinistre en local et scénarios de test et développement cloud.

#### <a name="differences-from-the-physical-device"></a>Différences de l’appareil physique

Le tableau suivant montre quelques différences clés entre le périphérique virtuel StorSimple et le périphérique physique StorSimple.

|                             | Périphérique physique                                          | Périphérique virtuel                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Emplacement**                    | Se trouve dans le centre de données.                               | S’exécute dans Azure.                                                                            |
| **Interfaces réseau**          | Dispose de six interfaces réseau : données 0 à 5 de données.                  | Possède une interface de réseau : données 0.                                        |
| **Enregistrement**                | Enregistré au cours de l’étape de configuration.                | L’enregistrement est une tâche distincte.                                                          |
| **Clé de chiffrement de données de service** | Régénérer sur le périphérique physique et puis mettre à jour le périphérique virtuel avec la nouvelle clé.           | Ne peut pas régénérer du périphérique virtuel. |


## <a name="prerequisites-for-the-virtual-device"></a>Conditions préalables pour le périphérique virtuel

Les sections suivantes décrivent les conditions préalables configuration pour votre appareil virtuel StorSimple. Avant de déployer un périphérique virtuel, passez en revue les [Considérations relatives à la sécurité pour l’utilisation d’un périphérique virtuel](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Configuration requise pour Azure

Avant de vous configurez le périphérique virtuel, vous devez effectuer les préparations dans votre environnement Azure :

- Pour le périphérique virtuel, [configurer un réseau virtuel sur Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Si l’utilisation du stockage Premium, vous devez créer un réseau virtuel dans une zone Azure qui prend en charge le stockage Premium. Plus d’informations sur les [régions qui sont pris en charge pour 8020](#supported-regions-for-8020).
- Il est recommandé d’utiliser le serveur DNS par défaut fourni par Azure au lieu de spécifier votre propre nom de serveur DNS. Si le nom de votre serveur DNS n’est pas valide ou si le serveur DNS n’est pas en mesure de résoudre les adresses IP correctement, la création du périphérique virtuel échouera.
- Point-à-site et site à sont facultatif, mais pas obligatoire. Si vous le souhaitez, vous pouvez configurer les options suivantes pour des scénarios plus avancés. 
- Vous pouvez créer des [Machines virtuelles Azure](../virtual-machines/virtual-machines-linux-about.md) (serveurs hôtes) dans le réseau virtuel utilisables par les volumes exposés par le périphérique virtuel. Ces serveurs remplissent les conditions suivantes :                            
    - Être Windows ou Linux machines virtuelles avec iSCSI logiciel initiateur installé.
    - Exécuter dans le même réseau virtuel que le périphérique virtuel.
    - Être en mesure de vous connecter à la cible iSCSI du périphérique virtuel via l’adresse IP interne du périphérique virtuel.

- Vérifiez que vous avez configuré la prise en charge pour le trafic iSCSI et cloud sur le même réseau virtuel.


#### <a name="storsimple-requirements"></a>Configuration requise StorSimple

Vérifiez les mises à jour suivants à votre service Azure StorSimple avant de créer un périphérique virtuel :


- Ajouter des [enregistrements de contrôle d’accès](storsimple-manage-acrs.md) pour les ordinateurs virtuels qui vont être serveurs hôtes pour votre périphérique virtuel.

- Utilisez un [compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account) dans la même région en tant que le périphérique virtuel. Comptes de stockage dans différentes régions, peuvent entraîner une baisse des performances. Vous pouvez utiliser un compte Standard ou stockage Premium avec le périphérique virtuel. Plus d’informations sur la création d’un [compte de stockage Standard]() ou un [compte de stockage Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Utilisez un compte de stockage différentes pour la création de périphérique virtuel de celui utilisé pour vos données. En utilisant le même compte de stockage peut entraîner une baisse des performances.

Vérifiez que vous disposez des informations suivantes avant de commencer :

- Votre Azure compte portail classique avec les informations d’accès.

- Une copie de la clé de chiffrement des données de service à partir de votre appareil physique.


## <a name="create-and-configure-the-virtual-device"></a>Créer et configurer le périphérique virtuel

Avant d’effectuer ces procédures, vérifiez que vous remplissez les [conditions préalables pour le périphérique virtuel](#prerequisites-for-the-virtual-device). 

Après avoir créé un réseau virtuel, configuré un service Manager StorSimple et inscrire votre appareil StorSimple physique auprès du service, vous pouvez utiliser les étapes suivantes pour créer et configurer un périphérique virtuel StorSimple. 

### <a name="step-1-create-a-virtual-device"></a>Étape 1 : Créer un périphérique virtuel

Procédez comme suit pour créer le périphérique virtuel StorSimple.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Si la création du périphérique virtuel échoue dans cette étape, vous devrez pas une connexion à Internet. Pour plus d’informations, accédez à [résoudre les problèmes de connectivité Internet](#troubleshoot-internet-connectivity-errors) lorsque creatig un périphérique virtuel.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Étape 2 : Configurer et inscrire le périphérique virtuel

Avant de commencer cette procédure, vérifiez que vous possédez une copie de la clé de chiffrement des données de service. La clé de chiffrement des données de service a été créée lorsque vous avez configuré votre appareil StorSimple première vous ont été invité à enregistrer dans un emplacement sécurisé. Si vous ne disposez pas d’une copie de la clé de chiffrement des données de service, vous devez contacter le Support Microsoft pour obtenir une assistance.

Effectuez les opérations suivantes pour configurer et inscrire votre appareil virtuel StorSimple.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Étape 3 : (Facultatif) modifier les paramètres de configuration du dispositif

La section suivante décrit les paramètres de configuration du dispositif nécessaire pour le StorSimple périphérique virtuel si vous souhaitez utiliser CHAP, gestionnaire d’instantanés StorSimple ou modifier le mot de passe administrateur de l’appareil.

#### <a name="configure-the-chap-initiator"></a>Configurer l’initiateur CHAP

Ce paramètre contient les informations d’identification qui attend de votre périphérique virtuel (cible) à partir des initiateurs (serveurs) et essayez d’accéder aux volumes. Les initiateurs constituent un nom d’utilisateur CHAP et un mot de passe CHAP identifient sur votre appareil pendant ce type d’authentification. Pour plus d’informations, accédez à [Configuration CHAP pour votre appareil](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurer la cible CHAP

Ce paramètre contient les informations d’identification que votre périphérique virtuel utilise lorsqu’un initiateur CHAP à extension demande d’authentification commun ou bidirectionnelle. Votre périphérique virtuel utilisera inverser CHAP nom d’utilisateur et mot de passe inverser CHAP à identifier à l’initiateur au cours du processus d’authentification. Notez que CHAP cible paramètres sont des paramètres globaux. Lorsqu’ils sont appliqués, tous les volumes connectés au périphérique virtuel stockage utilisera l’authentification CHAP. Pour plus d’informations, accédez à [Configuration CHAP pour votre appareil](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurer le mot de passe Gestionnaire d’instantanés StorSimple

Gestionnaire d’instantanés StorSimple logiciel se trouve sur votre hôte Windows et permet aux administrateurs de gérer les sauvegardes de votre appareil StorSimple sous la forme de local et en nuage instantanés.

>[AZURE.NOTE] Pour le périphérique virtuel, votre hôte Windows est une machine virtuelle Azure.

Lorsque vous configurez un périphérique dans le Gestionnaire d’instantanés StorSimple, vous devrez fournir l’adresse IP du périphérique StorSimple et le mot de passe pour l’authentification de votre périphérique de stockage. Pour plus d’informations, accédez à [configurer le Gestionnaire de capture instantanée StorSimple le mot de passe](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Modifier le mot de passe administrateur 

Lorsque vous utilisez l’interface Windows PowerShell pour accéder au périphérique virtuel, vous devrez entrer un mot de passe administrateur. Pour la sécurité de vos données, vous êtes obligé de modifier ce mot de passe avant de pouvoir utiliser le périphérique virtuel. Pour plus d’informations, accédez au [mot de passe administrateur Configure appareil](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Se connecter à distance au périphérique virtuel
Accès à distance sur votre appareil virtuel via l’interface Windows PowerShell n’est pas activée par défaut. Vous devez tout d’abord activer la gestion à distance sur le périphérique virtuel, puis activez-le sur le client qui sera utilisé pour accéder à votre périphérique virtuel.

Le processus en deux étapes pour vous connecter à distance est décrite ci-dessous.

### <a name="step-1-configure-remote-management"></a>Étape 1 : Configurer la gestion à distance

Effectuez les opérations suivantes pour configurer la gestion à distance pour votre périphérique virtuel StorSimple.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Étape 2 : Accéder à distance au périphérique virtuel

Une fois que vous avez activé la gestion à distance sur la page de configuration du dispositif StorSimple, vous pouvez utiliser Windows PowerShell distante pour vous connecter à l’appareil virtuel à partir d’un autre ordinateur virtuel au sein du même réseau virtuel ; par exemple, vous pouvez vous connecter à partir de l’hôte de machine virtuelle configuré et utilisé pour la connexion iSCSI. Dans la plupart des déploiements, vous devez avoir ouvert un point de terminaison public pour accéder à votre hôte machine virtuelle que vous pouvez utiliser pour l’accès au périphérique virtuel.

>[AZURE.WARNING] **Pour renforcer la sécurité, nous vous recommandons vivement que vous utilisez HTTPS lors de la connexion aux points de terminaison et supprimez ensuite les points de terminaison lorsque vous avez terminé votre session distante PowerShell.**

Vous devez suivre les procédures de [connexion à distance à votre appareil StorSimple](storsimple-remote-connect.md) à configurer la communication à distance pour votre périphérique virtuel.

## <a name="connect-directly-to-the-virtual-device"></a>Se connecter directement au périphérique virtuel

Vous pouvez également vous connecter directement au périphérique virtuel. Si vous voulez vous connecter directement au périphérique virtuel à partir d’un autre ordinateur en dehors du réseau virtuel ou en dehors de l’environnement de Microsoft Azure, vous devez créer des points de terminaison supplémentaires comme décrit dans la procédure suivante. 

Procédez comme suit pour créer un point de terminaison public sur le périphérique virtuel.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Nous vous recommandons de vous connecter à partir d’un autre ordinateur virtuel au sein du même réseau virtuel, car cela réduit le nombre de points de terminaison publics sur votre réseau virtuel. Lorsque vous utilisez cette méthode, vous simplement se connectez à la machine virtuelle via une session de bureau à distance, puis configurez cette machine virtuelle à utiliser comme vous le feriez pour tout autre client Windows sur un réseau local. Vous n’avez pas besoin d’ajouter le numéro de port public étant donné que le port sera déjà connu.

## <a name="work-with-the-storsimple-virtual-device"></a>Travailler avec le périphérique virtuel StorSimple

À présent que vous avez créé et configuré le périphérique virtuel StorSimple, vous êtes prêt à commencer à travailler avec lui. Vous pouvez travailler avec les conteneurs de volume, les volumes et les stratégies de sauvegarde sur un périphérique virtuel comme vous le feriez sur un appareil StorSimple physique ; la seule différence est que vous devez vous assurer que vous sélectionnez le périphérique virtuel dans votre liste des périphériques. Reportez-vous à [utiliser le service StorSimple Manager pour gérer un périphérique virtuel](storsimple-manager-service-administration.md) pour les procédures étape par étape des différentes tâches de gestion du périphérique virtuel.

Les sections suivantes décrivent quelques-unes des différences que vous rencontraient avec le périphérique virtuel.

### <a name="maintain-a-storsimple-virtual-device"></a>Mettre à jour un périphérique virtuel StorSimple

Car il s’agit d’un périphérique logiciel uniquement, mise à jour pour le périphérique virtuel est minime par rapport à la mise à jour pour le périphérique physique. Vous disposez des options suivantes :

- **Mises à jour logicielles** – vous pouvez afficher la date à laquelle le logiciel a été modifié, ainsi que les messages d’état mettre à jour. Vous pouvez utiliser le bouton **Rechercher les mises à jour** dans la partie inférieure de la page pour effectuer une analyse manuelle si vous souhaitez vérifier les nouvelles mises à jour. Si les mises à jour sont disponibles, cliquez sur **Installer les mises à jour** à installer. Étant donné seulement une interface unique sur le périphérique virtuel, cela signifie qu’il y aura une interruption de service légères lorsque des mises à jour sont appliqués. Le périphérique virtuel s’arrêter et redémarrer (si nécessaire) pour appliquer des mises à jour qui ont été publiées. Pour obtenir une procédure pas à pas, accédez à [mettre à jour votre périphérique](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Prend en charge package** – vous pouvez créer et télécharger un package de prise en charge pour Support Microsoft problèmes rencontrés avec votre appareil virtuel. Pour obtenir une procédure pas à pas, accédez à [créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Comptes de stockage pour un périphérique virtuel

Comptes de stockage sont créés pour une utilisation par le service Manager StorSimple, par le périphérique virtuel et par le périphérique physique. Lorsque vous créez vos comptes de stockage, nous vous recommandons d’utiliser un identificateur de région dans le nom convivial pour vous assurer que la zone est cohérente à travers tous les composants du système. Pour un périphérique virtuel, il est important que tous les composants se trouver dans la même région pour éviter les problèmes de performances.

Pour obtenir une procédure pas à pas, accédez à [Ajouter un compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Désactiver un périphérique virtuel StorSimple

La désactivation d’un périphérique virtuel supprime la machine virtuelle et les ressources créés lorsqu’il a été mis en service. Une fois le périphérique virtuel est désactivé, il ne peut pas être restauré à son état précédent. Avant de désactiver le périphérique virtuel, veillez à arrêter ou supprimer des clients et des hôtes qui en dépendent.

La désactivation d’un périphérique virtuel se traduit par les actions suivantes :

- Le périphérique virtuel est supprimé.

- Le disque du système d’exploitation et disques de données créés pour le périphérique virtuel sont supprimés.

- Le service hébergé et le réseau virtuel créé au cours de la mise en service sont conservés. Si vous n’utilisez pas les, vous devez les supprimer manuellement.

- Instantanés cloud créés pour le périphérique virtuel sont conservés.

Pour obtenir une procédure pas à pas, accédez à [désactiver et supprimer de votre appareil StorSimple](storsimple-deactivate-and-delete-device.md).

Dès que le périphérique virtuel est affiché comme désactivés dans la page service StorSimple Manager, vous pouvez supprimer le périphérique virtuel à partir de la liste des périphériques dans la page **appareils** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Démarrer, arrêter et redémarrer un périphérique virtuel
Contrairement à l’appareil physique StorSimple, il n’existe aucune power activé ou éteint bouton distribuer sur un périphérique virtuel StorSimple. Toutefois, il peut arriver où vous devez arrêter et redémarrer le périphérique virtuel. Par exemple, certaines mises à jour peuvent nécessiter que la machine virtuelle redémarrer pour terminer le processus de mise à jour. Le moyen le plus simple pour vous permettent de démarrer, arrêter et redémarrer un périphérique virtuel consiste à utiliser la Console de gestion des Machines virtuelles.

Lorsque vous examinez la Console de gestion, l’état du périphérique virtuel est **en cours d’exécution** , car il est démarré par défaut après sa création. Vous pouvez démarrer, arrêter et redémarrer une machine virtuelle à tout moment.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Restaurez par défaut

Si vous décidez que vous voulez simplement recommencer avec votre appareil virtuel, simplement désactiver et supprimez-le, puis créez un nouveau. Lorsque votre périphérique physique est réinitialisé, comme votre nouvel appareil virtuel n’ont pas les mises à jour installés ; Par conséquent, vérifiez les mises à jour avant de l’utiliser.


## <a name="fail-over-to-the-virtual-device"></a>Basculer vers le périphérique virtuel

Reprise (DR) est un des scénarios clés que le périphérique virtuel StorSimple a été conçu pour. Dans ce scénario, le périphérique StorSimple physique ou le centre de données entier peut ne pas être disponible. Peut être effectué en, vous pouvez utiliser un périphérique virtuel pour les opérations dans un autre emplacement de restauration. Au cours de DR, les conteneurs de volume du périphérique source modifient la propriété et sont transférés vers le périphérique virtuel. Les conditions préalables pour DR sont que le périphérique virtuel a été créé et configuré, tous les volumes dans le conteneur de volume s’est ouverte en mode hors connexion et le conteneur de volume est associé à un instantané de cloud.

>[AZURE.NOTE] 
>
> - Lorsque vous utilisez un périphérique virtuel comme périphérique secondaire pour DR, n’oubliez pas que la 8010 a 30 To de stockage Standard et 8020 a 64 To de stockage Premium. Le périphérique virtuel du plus élevé capacité 8020 peut être plus adapté à un scénario DR.
> - Vous ne pouvez pas basculement ou cloner d’un appareil 2 mise à jour en cours d’exécution sur un périphérique en cours d’exécution avant mise à jour 1 logiciel. Vous pouvez toutefois basculer vers un appareil 2 mise à jour en cours d’exécution sur un périphérique en cours d’exécution mise à jour 1 (1.1 ou 1.2)

Pour obtenir une procédure pas à pas, accédez à [basculement vers un périphérique virtuel](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Arrêter ou de supprimer le périphérique virtuel

Si vous avez précédemment configuré et que vous utilisé une StorSimple périphérique virtuel mais souhaite arrêter accumuler de frais cluster de son utilisation, vous pouvez arrêter le périphérique virtuel. Arrêter le périphérique virtuel ne supprime pas son système d’exploitation ou disques de données dans le stockage. Il s’arrête frais courus sur votre abonnement, mais les frais de stockage pour les disques du système d’exploitation et les données continuera.

Si vous supprimez ou arrêtez le périphérique virtuel, il apparaît sous forme de **mode hors connexion** dans la page appareils du service Manager StorSimple. Vous pouvez choisir désactiver ou supprimer le périphérique si vous souhaitez également supprimer les sauvegardes créés par le périphérique virtuel. Pour plus d’informations, voir [désactiver et supprimer un appareil StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Résoudre les erreurs de connectivité Internet 

Lors de la création d’un périphérique virtuel, s’il n’existe pas de connexion à Internet, l’étape de création échoue. Pour résoudre l’échec est en raison de la connectivité Internet, effectuez les opérations suivantes dans le portail classique Azure :

1. Créer une machine virtuelle Windows server 2012 dans Azure. Cette machine virtuelle doivent utiliser le même compte de stockage, VNet et chaque sous-réseau au utilisée par votre périphérique virtuel. Si vous avez déjà un hôte Windows Server existant dans Azure en utilisant le même compte de stockage, Vnet et sous-réseau, vous pouvez également l’utiliser pour résoudre les problèmes de la connectivité Internet.
2. Journaux à distance sur l’ordinateur virtuel créé à l’étape précédente. 
3. Ouvrez une fenêtre de commande à l’intérieur de la machine virtuelle (Win + R et tapez `cmd`).
4. Exécutez la commande suivante à l’invite.

    `nslookup windows.net`

5. Si `nslookup` échoue, puis Échec de la connectivité Internet empêche l’inscription au service Gestionnaire de StorSimple le périphérique virtuel. 
6. Apportez les modifications nécessaires à votre réseau virtuel pour vous assurer que le périphérique virtuel est en mesure d’accéder aux sites Azure tels que « Windows.NET ».

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le service StorSimple Manager pour gérer un périphérique virtuel](storsimple-manager-service-administration.md).
 
- Comprendre comment [restaurer un volume StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md). 

