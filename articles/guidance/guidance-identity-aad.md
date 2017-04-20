<properties
   pageTitle="Implémentation d’Azure Active Directory | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau sécurisée hybride avec Azure Active Directory."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Implémentation d’Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour l’intégration de forêts et les domaines d’Active Directory (AD) en local avec Azure Active Directory pour fournir une authentification de l’identité sur le nuage.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Vous pouvez utiliser les services d’annuaire et d’identité, tels que ceux fournis par les Services d’annuaire Active Directory (AD DS) pour authentifier les identités. Ces identités peuvent appartenir à des utilisateurs, ordinateurs, applications ou d’autres ressources qui font partie d’une limite de sécurité. Vous pouvez héberger annuaire et identité des services en local, mais dans un scénario hybride où se trouvent les éléments d’une application dans Azure, il peut être plus efficace d’étendre ces fonctionnalités dans le cloud. Cette approche permet de réduire la latence provoquée par l’envoi d’authentification et les demandes d’autorisation locales à partir du cloud en retour vers les services d’annuaire et identité en cours d’exécution en local.

Azure fournit deux solutions pour l’implémentation des services d’annuaire et d’identité dans le cloud :

1. Vous pouvez utiliser [Azure Active Directory (DAS)] [ azure-active-directory] pour créer un domaine AD dans le cloud et lien vers un local domaine Active Directory. AAD vous permettent de configurer authentification unique (SSO) permet aux utilisateurs exécutant des applications accédées via le cloud. AAD utilise [Azure AD Connect] [ azure-ad-connect] en cours d’exécution local pour dupliquer des objets et des identités contenus dans le référentiel local dans le cloud.

    Vous pouvez également implémenter AAD sans l’aide d’un répertoire local. Dans ce cas, AAD se comporte comme source principale de toutes les informations d’identité plutôt que contenant les données répliquées à partir d’un répertoire local.

    Notez que le répertoire dans le nuage n’est **pas** une extension d’un répertoire local. Au lieu de cela, il s’agit d’une copie qui contient les objets et les identités même. Modifications apportées à ces éléments locaux sont copiées dans le nuage, mais les modifications apportées dans le nuage **ne sont pas** être répliquées sur le domaine local.

    >[AZURE.NOTE] Les éditions Azure Active Directory Premium prend en charge l’écriture différée des mots de passe des utilisateurs, permettant à vos utilisateurs locaux effectuer le mot de passe libre-service dans le cloud. Il s’agit de la seule information AAD synchronise revenir à l’annuaire local. Pour plus d’informations sur les différentes éditions de AAD et leurs fonctionnalités, voir [Azure Active Directory éditions][aad-editions].

2. Vous pouvez déployer un ordinateur exécutant les services AD DS comme contrôleur de domaine dans Azure, extension de votre infrastructure AD existant (y compris les services AD DS et AD FS) à partir de votre centre de données locale. Cette approche est plus courante pour les scénarios où le réseau local et le réseau virtuel Azure sont connectés par une connexion VPN et/ou ExpressRoute. Cette solution prend également en charge bidirectionnelle réplication ce qui permet de vous apporter des modifications dans le nuage et en local, à l’endroit où il convient le mieux.

Cette architecture se concentre sur solution 1. Pour plus d’informations sur la deuxième solution, voir [Extension d’Active Directory pour Azure][guidance-adds].

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Fournir l’authentification unique pour les utilisateurs finaux en cours d’exécution SaaS et autres applications dans le cloud, utilisant les mêmes informations d’identification qu’ils spécifient pour les applications en local.

- Publication locale applications web via le cloud pour donner accès aux utilisateurs à distance appartenant à votre organisation.

- Mise en œuvre des fonctionnalités de libre-service pour l’utilisateur final, telles que la réinitialisation de leur mot de passe et délégation de la gestion de groupe. 

    >[AZURE.NOTE] Ces fonctionnalités peuvent être contrôlées par un administrateur via la stratégie de groupe.

- Cas où le réseau local et le réseau virtuel Azure hébergement d’applications cloud ne sont pas directement liés à l’aide d’un tunnel VPN ou circuit ExpressRoute.

Notez que AAD ne fournit pas toutes les fonctionnalités de AD. Par exemple, AAD actuellement gère uniquement l’authentification des utilisateurs au lieu d’authentification de l’ordinateur. Certaines applications et services, telles que SQL Server, peuvent nécessiter l’authentification des ordinateurs auquel cas cette solution n’est pas le cas. En outre, AAD peut-être pas approprié pour les systèmes où les composants peuvent migrer au-delà de la limite de cloud/le locaux et plutôt qu’en cours de copie.

> [AZURE.NOTE] Pour obtenir une explication détaillée du fonctionnement de Azure Active Directory, regardez [Microsoft Active Directory expliqués][aad-explained].

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture. Pour plus d’informations sur les éléments de la charge de travail dans Azure, lisez [Machines virtuelles en cours d’exécution pour une architecture multicouches sur Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Pour simplifier, ce diagramme affiche les connexions directement liées à AAD uniquement et ne représente pas la redirection d’une requête web navigateur ou autre protocole relatives au trafic pouvant se produire dans le cadre du processus de fédération d’authentification et d’identité. Par exemple, un utilisateur (locale ou distante) accède généralement à une application web via un navigateur et l’application web peut rediriger un navigateur web pour authentifier la demande par le biais AAD de manière transparente. Une fois authentifié, la requête peut être passée à l’application web ainsi que les informations d’identité approprié.

[! [0]][0]

- **Client azure Active Directory**. Il s’agit d’une instance de AAD créé par votre organisation. Il se comporte comme un service d’annuaire simple pour les applications cloud (elle conserve des objets copiées à partir des locaux AD) et fournit des services d’identité.

- **Sous-réseau de niveau web**. Ce sous-réseau conserve machines virtuelles que la mise en œuvre d’une application personnalisée sur le nuage développée par votre organisation et qui AAD peut servir un gestionnaire d’identités.

- **Locaux serveur AD DS**. Susceptible de votre organisation possède déjà un service d’annuaire existants tels que les services AD DS. Vous pouvez synchroniser tous les éléments dans votre annuaire AD DS (par exemple, l’utilisateur et les informations sur les groupes) avec DAS, pour activer AAD à utiliser ces informations pour authentifier les identités.

- **Serveur de synchronisation azure AD Connect**. Il s’agit d’un ordinateur local qui exécute le service de synchronisation Azure AD Connect. Vous installez ce service en utilisant le logiciel Azure AD Connect. Le service de synchronisation Azure AD Connect synchronise les informations (utilisateurs, groupes, Contacts, etc.) contenues dans les locaux annonce sur AAD dans le cloud. Par exemple, vous pouvez mettre en service ou deprovision utilisateurs et groupes en local, et ces modifications sont propagées à AAD. Le service de synchronisation Azure AD Connect transmet les informations sur le client AAD.

    >[AZURE.NOTE] Pour des raisons de sécurité, les mots de passe de l’utilisateur ne sont pas stockés directement dans AAD. Au lieu de cela, AAD conserve un hachage chaque mot de passe. Ceci est suffisant pour vérifier le mot de passe d’un utilisateur. Si un utilisateur requiert une réinitialisation du mot de passe, cela doit être exécutées en local et le nouveau hachage envoyées à AAD. Les éditions Premium AAD incluent des fonctionnalités permettant d’automatiser cette tâche pour permettre aux utilisateurs de réinitialiser leur mot de passe.

## <a name="recommendations"></a>Recommandations

Cette section résume les recommandations pour implémenter AAD comme suit.

- AD Connect
- Sécurité

### <a name="azure-ad-connect-sync-service-recommendations"></a>Recommandations service de synchronisation Azure AD Connect

S’assurer que les informations d’identité des utilisateurs dans le nuage sont cohérentes avec celle maintenu en local concerne la synchronisation. L’objectif du service de synchronisation Azure AD Connect est cette cohérence. Les points suivants résument les recommandations pour implémenter le service de synchronisation Azure AD Connect :

- Avant d’implémenter synchronisation Azure AD Connect, vous devez déterminer la configuration requise pour la synchronisation de votre organisation (les éléments à synchroniser, des domaines et la fréquence. L’article [Configuration requise pour déterminer directory synchronisation] [ aad-sync-requirements] décrit les points que vous devez prendre en compte.

- Vous pouvez exécuter le service de synchronisation Azure AD Connect en utilisant une machine virtuelle ou un ordinateur hébergé en local. Selon la volatilité des informations dans votre annuaire Active Directory, une fois que la synchronisation initiale avec DAS a été effectuée la charge sur le service de synchronisation Azure AD Connect n’est probablement pas haute. À l’aide d’une machine virtuelle vous permet de mettre à l’échelle plus facilement le serveur (surveiller l’activité comme décrit dans la section [Considérations relatives à la surveillance](#monitoring-considerations) pour déterminer si cela est nécessaire).

- Si vous avez plusieurs domaines locaux dans une forêt, vous pouvez stocker et synchroniser des informations pour l’ensemble de la forêt à un seul client AAD (il s’agit de l’approche recommandée). Filtrer les informations pour les identités qui se produisent dans plusieurs domaines afin que n'apparaisse qu’une seule fois à chaque identité dans AAD au lieu d’être dupliqué car cela peut provoquer des incohérences dans lorsque les données sont synchronisées. Cette approche nécessite l’implémentation de plusieurs serveurs de synchronisation Azure AD Connect. Pour plus d’informations, consultez le scénario AAD plusieurs dans la section [Considérations relatives à la topologie](#topology-considerations) . 

- Utilisez le filtrage pour limiter les données stockées dans AAD à seulement celui qui est nécessaires. Par exemple, votre organisation souhaiterez pas stocker des informations à propos des comptes inactives ou non personnelles dans AAD. Filtrage peut être groupe, domaine, unité d’organisation ou attribut, et vous pouvez combiner des filtres pour générer des règles plus complexes. Par exemple, vous pouvez sélectionner pour synchroniser uniquement les objets contenus dans un domaine qui ont une valeur spécifique dans un attribut sélectionné. Pour plus d’informations, voir [synchronisation Azure AD Connect : configuration du filtrage][aad-filtering].

- Pour renforcer la disponibilité du service de synchronisation AD Connect, exécutez un serveur intermédiaire secondaire. Pour plus d’informations, consultez la section [Considérations relatives à la topologie](#topology-considerations) .

### <a name="security-recommendations"></a>Recommandations de sécurité

Les éléments suivants synthétisent les recommandations de sécurité principal pour l’implémentation AAD, selon les besoins de votre organisation :

- Gérer les mots de passe des utilisateurs.

    Si vous utilisez une édition premium de DAS, vous pouvez activer le mot de passe en écriture différée de DAS à votre annuaire local en effectuant une installation personnalisée de Azure AD Connect :

    [! [9]][9]

    Cette fonctionnalité permet aux utilisateurs de réinitialiser leur mot de passe à partir du portail Azure, mais ne doit être activée après avoir examiné la stratégie de sécurité de mot de passe de votre organisation. Par exemple, vous pouvez limiter les utilisateurs peuvent modifier leur mot de passe, et vous pouvez personnaliser l’expérience de gestion de mot de passe. Pour plus d’informations, voir [Personnalisation de gestion de mots de passe en fonction des besoins de votre organisation][aad-password-management].

- Maintenir la protection des applications local accessible avec l’extérieur.

    Utilisez le Proxy d’Application Azure AD pour fournir un accès contrôlé aux applications web en local par les utilisateurs externes via AAD. Cette approche protège vos applications en exposant ne pas directement à Internet. Seuls les utilisateurs possédant des informations d’identification valides dans votre annuaire Azure sont en mesure d’atteindre vos applications. Pour plus d’informations, voir l’article [Activer le Proxy d’Application dans le portail Azure][aad-application-proxy].

- Surveillez AAD des signes de suspect.

    Envisagez d’utiliser AAD Premium P2 edition, qui inclut la Protection d’identité AAD. Protection d’identité utilise algorithmes d’apprentissage automatique adaptive et heuristique pour détecter des anomalies et événements indiquant qu’une identité a été déchiffrée des risques. Par exemple, il détecte potentiellement anormale activité comme irréguliers connexion à des activités, signe-ins d’origine inconnue ou à partir des adresses IP avec une activité suspecte ou signe-ins à partir d’appareils peuvent être infection. Protection d’identité à l’aide de ces données, génère des rapports et les alertes qui vous permet d’analyser ces événements risque et prendre remediation appropriée ou action d’atténuation. Pour plus d’informations, voir [Azure Active Directory identité Protection][aad-identity-protection].

    Vous pouvez également utiliser la fonctionnalité de création de rapports de AAD dans le portail Azure pour surveiller les suspectes et autres activités liés à la sécurité qui se produisent dans votre système. AAD peut générer une série de rapports de synthèse :

    [! [17]][17]

    Pour plus d’informations sur l’utilisation de ces rapports, voir le [Guide Azure Active Directory Reporting][aad-reporting-guide].

## <a name="topology-considerations"></a>Considérations relatives à la topologie

Si vous intégrez un répertoire local à AAD, configurez Azure AD Connect pour mettre en œuvre une topologie qui correspond le mieux à la configuration requise de votre organisation. Topologies Azure AD Connect prend en charge sont les suivants :

- **Une seule forêt et répertoire AAD unique**. Dans cette topologie, vous utilisez Azure AD Connect pour synchroniser des objets et des informations d’identité dans un ou plusieurs domaines dans un seul local forêt avec un seul client AAD. Il s’agit de la topologie par défaut implémentée par l’installation rapide de Azure AD Connect.

    [! [1]][1]

    Ne créez pas plusieurs serveurs de synchronisation Azure AD Connect pour vous connecter différents domaines dans la même forêt local vers le même client AAD, sauf si vous exécutez un serveur de synchronisation Azure AD Connect dans le mode de mise en attente (voir les intermédiaires Server ci-dessous).

- **Plusieurs forêts, répertoire AAD unique**. Utilisez cette topologie si vous avez plus d’une forêt en local. Vous pouvez consolider des informations d’identité afin que chaque utilisateur unique est représenté une fois dans le répertoire DAS, même si le même utilisateur existe dans plus d’une forêt. Toutes les forêts utilisent le même serveur de synchronisation Azure AD Connect. Le serveur de synchronisation Azure AD Connect n’a pas à faire partie d’un domaine, mais il doit être accessible à partir de toutes les forêts :

    [! [2]][2]

    Dans cette topologie, n’utilisez pas distincte Azure AD Connect synchroniser des serveurs à se connecter chaque forêt en local sur un seul client AAD. Cela peut entraîner des informations d’identité dupliqué dans AAD si les utilisateurs sont présents dans plus d’une forêt.

- **Plusieurs forêts, topologies distinctes**. Cette approche permet de fusionner les informations d’identité à partir de forêts distinctes dans un seul client AAD. Cette stratégie est utile si vous voulez combiner forêts provenant de différentes organisations (après une fusion ou acquisition, par exemple) et les informations d’identité pour chaque utilisateur sont trouve dans une forêt :

    Si les listes d’adresses globales de chaque forêt sont synchronisées, un utilisateur d’une forêt peut être présent dans un autre en tant que contact. Cela peut se produire si, par exemple, votre organisation a implémenté GALSync avec Forefront Identity manager 2010 ou Microsoft Identity Manager 2016. Dans ce scénario, vous pouvez spécifier que les utilisateurs doivent être identifiés par leur attribut de *messagerie* . Vous pouvez également faire correspondre les identités en utilisant les attributs *ObjectSID* et *msExchMasterAccountSID* . Ceci est utile si vous avez une ou plusieurs forêts ressource avec comptes désactivés.

- **Serveur de test**. Dans cette configuration, vous exécutez une deuxième instance du serveur de synchronisation Azure AD Connect en parallèle avec la première. Cette structure prend en charge les scénarios tels que :

    - Disponibilité.

    - Test et le déploiement d’une nouvelle configuration du serveur de synchronisation Azure AD Connect.

    - Présentation d’un nouveau serveur et désaffecter une ancienne configuration. 

    Dans ce cas, la deuxième instance s’exécute en *mode de mise en attente*. Le serveur enregistre les objets importés et données de synchronisation dans sa base de données, mais ne passe pas les données au DAS. Uniquement lorsque vous désactivez le mode intermédiaire le serveur démarre écrire des données sur DAS, ainsi que les démarrages mot de passe performantes écriture rappels dans les répertoires locaux le cas échéant :

    [! [4]][4]

    Pour plus d’informations, voir [synchronisation Azure AD Connect : tâches opérationnelles et considérations][aad-connect-sync-operational-tasks].

- **AAD plusieurs répertoires**. Il est recommandé que vous créez un répertoire AAD unique pour une organisation, mais il peut arriver que vous deviez informations de partition dans l’ensemble des répertoires AAD distincts. Dans ce cas, vous devez vous assurer que chaque objet de la forêt locale s’affiche uniquement dans un répertoire AAD, d’éviter les problèmes d’écriture différée synchronisation et mot de passe.

    Pour mettre en œuvre ce scénario, configurez distincte Azure AD Connect synchronisez serveurs pour chaque répertoire DAS et le filtrage pour chaque serveur de synchronisation Azure AD Connect fonctionne sur un jeu d’objets qui s’excluent mutuellement : 

    [! [5]][5]

Pour plus d’informations sur ces topologies, voir [Topologies pour Azure AD Connect][aad-topologies].

## <a name="user-sign-in-considerations"></a>Considérations sur la connexion utilisateur

Par défaut, le service DAS part du principe que les utilisateurs se connecter en fournissant le même mot de passe qu’elles utilisent en local et le serveur de synchronisation Azure AD Connect configure la synchronisation de mot de passe entre le domaine local et DAS. Pour de nombreuses organisations, cela est approprié, mais vous devez prendre en compte les stratégies existantes et infrastructure de votre organisation. Par exemple :

- La stratégie de sécurité de votre organisation peut interdire synchronisation hachage de mot de passe dans le cloud.

- Vous pouvez être amené que les utilisateurs connaissent l’authentification unique transparente (sans mot de passe supplémentaire invite) lors de l’accès à des ressources de cloud à partir de domaine rejoint machines du réseau d’entreprise.

- Votre organisation peut avoir déjà ADFS ou un fournisseur tiers fédération déployé. Vous pouvez configurer DAS pour utiliser cette infrastructure pour implémenter l’authentification et l’authentification unique, plutôt que d’utiliser les informations de mot de passe contenues dans le cloud.

Pour plus d’informations, voir [Azure AD connecter utilisateur s’options][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Considérations relatives à la Azure AD application proxy

Utilisez Azure AD pour donner accès aux applications en local.

- Exposer vos applications web en local à l’aide de connecteurs gérer par le composant Azure AD application proxy proxy de l’application. L’application proxy connector ouvre une connexion de réseau sortant vers le proxy d’application Azure AD. Demandes d’utilisateurs à distance sont routés à partir de AAD via cette connexion aux applications web. Ce mécanisme évite d’avoir à ouvrir les ports d’entrée dans le pare-feu local, réduire la surface d’attaque exposée par votre organisation.

Pour plus d’informations, voir [publier les applications qui utilisent le proxy d’Azure AD Application][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Considérations relatives à la synchronisation objet

La configuration par défaut de Azure AD Connect synchronise les objets à partir de votre annuaire Active Directory local en fonction de l’ensemble de règles spécifié dans l’article [synchronisation Azure AD Connect : présentation de la configuration par défaut][aad-connect-sync-default-rules]. Seuls les objets qui satisfont les règles suivantes sont synchronisés, d’autres personnes sont ignorées. Par exemple, les objets utilisateur doivent avoir un attribut unique *sourceAnchor* et l’attribut *accounEnabled* doit être renseigné. Objets utilisateur qui n’ont pas un attribut *sAMAccountName* ou qui commencent par le texte *AAD_* ou *MSOL_* ne sont pas synchronisés. Azure AD Connect s’applique à plusieurs règles aux objets utilisateur, Contact, groupe, ForeignSecurityPrincipal et de l’ordinateur. Si vous avez besoin modifier l’ensemble de règles par défaut, utilisez l’éditeur de règles de synchronisation installé avec Azure AD Connect (également présentées dans [synchronisation Azure AD Connect : présentation de la configuration par défaut][aad-connect-sync-default-rules]).

Vous pouvez définir vos propres filtres pour limiter les objets à synchroniser par unité d’organisation ou un domaine. Ou implémenter un filtrage personnalisé plus complexes, comme décrit dans [synchronisation Azure AD Connect : configuration du filtrage][aad-filtering].

## <a name="security-considerations"></a>Considérations sur la sécurité

Contrôle d’accès conditionnelle permet de refuser des demandes d’authentification provenant de sources inattendus :

- Déclencher [L’authentification multifacteur Azure Multifacteur] [ azure-multifactor-authentication] si un utilisateur tente de se connecter à partir d’un emplacement non approuvé (tel que sur Internet) plutôt que d’un réseau approuvé.

- Utiliser le type de plateforme appareil de l’utilisateur (iOS, Android, Windows Mobile, Windows) pour déterminer la stratégie d’accès aux applications et fonctionnalités.

- Enregistrer l’état désactivé/activé d’appareils utilisateurs et intégrer ces informations dans les vérifications de stratégie d’accès. Par exemple, si perte ou de vol de téléphone d’un utilisateur il doit être enregistré comme désactivé pour empêcher d’être utilisé pour accéder.

- Contrôler le niveau d’accès à un utilisateur en fonction de l’appartenance aux groupes. Utiliser des [règles d’appartenance dynamique DAS] [ aad-dynamic-membership-rules] pour simplifier l’administration de groupe. Pour un bref aperçu de comment cela fonctionne, voir [présentation appartenances dynamique pour les groupes][aad-dynamic-memberships].

- Utiliser des stratégies de risque d’accès conditionnel avec Protection d’identité AAD pour fournissent une protection avancée basée sur les activités de connexion inhabituelles ou d’autres événements.

Pour plus d’informations, voir [accéder conditionnelle Azure Active Directory][aad-conditional-access].

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Extensibilité du est adressé par le service DAS et la configuration du serveur de synchronisation Azure AD Connect :

- Pour le service DAS, vous n’êtes pas obligé de configurer les options pour implémenter l’extensibilité élevées. Le service DAS prend en charge extensibilité élevées basée sur les doubles. AAD met en œuvre un seul réplica principal qui gère les opérations d’écriture, et plusieurs réplicas secondaires en lecture seule. AAD redirige de manière transparente écriture tentative effectuée par rapport à réplicas secondaires sur réplica principal. AAD fournit la cohérence éventuelle. Toutes les modifications apportées à la réplique primaire sont propagées vers les réplicas secondaires. La plupart des opérations contre AAD sont lectures au lieu d’écriture, cette architecture évolue bien.

    Pour plus d’informations, voir [Azure AD : les paramètres avancés de notre annuaire cloud geo redondantes, distribuée, très disponible][aad-scalability].

- Pour le serveur de synchronisation Azure AD Connect, vous devez déterminer le nombre d’objets il est probable que synchroniser à partir de votre répertoire local. Si vous avez moins de 100 000 objets, vous pouvez utiliser le logiciel de SQL Server Express LocalDB par défaut fourni avec Azure AD Connect. Si vous avez un grand nombre d’objets, vous devez installer une version de production de SQL Server et effectuer une installation personnalisée de la spécification d’Azure AD Connect qu’il doit utiliser une instance existante de SQL Server.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

À l’instar de ce qui concerne extensibilité élevées, disponibilité s’étend sur le service DAS et la configuration d’Azure AD Connect :

- Le service DAS est conçu pour augmenter la disponibilité. Il n’existe aucune option disponibilité configurable par l’utilisateur. Il est distribué geo et s’exécute dans les données de plusieurs centres de propagation à travers le monde, avec basculement automatique. Si un centre de données deviendrait indisponible, AAD garantit que les données de votre annuaire soient disponibles pour l’accès instance dans au moins deux autres centres de données dispersés.

    >[AZURE.NOTE] SLA pour la disponibilité de % de garanties au moins 99,9 services AAD base et Premium. Il n’existe pas de SLA pour la couche gratuite de DAS. Pour plus d’informations, voir [SLA pour Azure Active Directory][sla-aad].

- Pour augmenter la disponibilité du serveur de synchronisation Azure AD Connect, vous pouvez exécuter une deuxième instance dans les intermédiaires mode, comme décrit dans la section [Considérations relatives à la topologie](#topology-considerations) . 

    En outre, si vous n’utilisez pas l’instance SQL Server Express LocalDB qui est fourni avec Azure AD Connect, vous devez réfléchir haute disponibilité de SQL Server. Notez que la solution de disponibilité uniquement pris en charge est SQL cluster. Solutions telles que la mise en miroir et toujours sur ne sont pas pris en charge par Azure AD Connect.

    Pour les considérations supplémentaires sur la gestion de la disponibilité du serveur de synchronisation Azure AD Connect et comment récupérer après une défaillance, voir [synchronisation Azure AD Connect : tâches opérationnelles et considérations - sinistre][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Considérations sur la gestion

Il existe deux aspects de la gestion AAD :

1. Administration AAD dans le cloud.

2. Maintenir les serveurs de synchronisation Azure AD Connect.

AAD fournit les options suivantes pour la gestion des domaines et des répertoires dans le cloud :

- [Module de PowerShell Azure Active Directory][aad-powershell]. Utilisez ce module si vous avez besoin créer un script Azure AD d’administration et des tâches courantes telles que la gestion de l’utilisateur, gestion des domaines pour la configuration de l’authentification unique.

- Carte de gestion des annonces Azure dans le portail Azure. Cette carte fournit une vue interactive gestion du répertoire et vous permet de contrôler et de configurer la plupart des aspects de AAD.

    [! [10]][10]

Azure AD Connect installe les outils suivants que vous utilisez pour mettre à jour les services de synchronisation Azure AD Connect à partir de vos ordinateurs locaux :

- La console Microsoft Azure Active Directory se connecter. Cet outil vous permet de modifier la configuration du serveur Azure AD Sync, personnaliser la façon dont la synchronisation se produit, activer ou désactiver le mode intermédiaire, et passer au mode de connexion utilisateur (vous pouvez activer AD FS se connecter à l’aide de votre infrastructure en local).

- Le Gestionnaire de services de synchronisation. Utiliser l’onglet *opérations* dans cet outil pour gérer le processus de synchronisation et détecter si toutes les parties du processus ont échoué. Vous pouvez déclencher manuellement à l’aide de cet outil de synchronisation. 

    [! [12]][12]

    L’onglet *connecteurs* permet de contrôler les connexions pour les domaines (en local et dans le cloud) à laquelle le moteur de synchronisation est associé :

    [! [13]][13]

-  L’éditeur de règles de synchronisation. Utilisez cet outil pour personnaliser le mode dans lequel les objets sont transformés lorsqu’ils sont copiés entre un répertoire local et AAD dans le cloud. Cet outil permet de spécifier des attributs supplémentaires et des objets pour la synchronisation et filtres pour déterminer les instances doivent ou ne doivent pas être synchronisées.

    Pour plus d’informations, consultez la section Éditeur de règles de synchronisation dans le document [synchronisation Azure AD Connect : présentation de la configuration par défaut][aad-connect-sync-default-rules].

La page [synchronisation Azure AD Connect : pratiques recommandées pour la modification de la configuration par défaut] [ aad-sync-best-practices] contient des informations supplémentaires et des conseils pour la gestion Azure AD Connect.

## <a name="monitoring-considerations"></a>Considérations relatives au contrôle

Contrôle d’état est effectué à l’aide d’une série d’agents installés en local :

- Azure AD Connect installe un agent qui comporte des informations concernant les synchronisations. Utiliser la carte de fonctionnement Azure Active Directory se connecter dans le portail Azure pour surveiller l’intégrité et les performances de Azure AD Connect. Pour plus d’informations, voir [AD de Azure à l’aide de se connecter au niveau pour la synchronisation][aad-health].

- Pour analyser le fonctionnement des domaines AD DS et des répertoires à partir d’Azure, installez AD connecter santé Azure pour agent AD DS sur un ordinateur dans le domaine local. Utiliser la carte Azure Active Directory se connecter au niveau du portail Azure pour surveiller les services AD DS. Pour plus d’informations, reportez-vous [à l’aide de Azure AD connecter santé avec les services AD DS][aad-health-adds]

- Installez l’état de connexion de AD Azure d’agent AD FS surveiller l’état du AD FS s’exécutant sur local et utiliser la carte de fonctionnement Azure Active Directory se connecter dans le portail Azure pour surveiller les services AD DS. Pour plus d’informations, reportez-vous [à l’aide de Azure AD connecter santé avec AD FS][aad-health-adfs]

Pour plus d’informations sur l’installation d’agents AD connecter santé et leur configuration requise, voir [Azure AD connecter santé Installation de l’Agent][aad-agent-installation].

## <a name="sample-solution"></a>Solution de l’échantillon

Cette section décrit les étapes pour la création d’un exemple de solution que vous pouvez utiliser pour tester la configuration de DAS. La solution comprend les éléments suivants :

- Une application web multicouches, après la structure décrite par [Machines virtuelles en cours d’exécution pour une architecture multicouches sur Azure][implementing-a-multi-tier-architecture-on-Azure].

- Un ordinateur client de test. Nous recommandons d’utiliser un autre ordinateur virtuel pour cet ordinateur. La configuration de cet ordinateur virtuel est sans importance, tant que vous disposez des droits d’administrateur et il peut se connecter à l’application web multicouches.

- Environnement simulé locaux contenant son propre domaine créé à l’aide de AD DS.

Les scénarios qui illustrent ces étapes sont :

- Activer l’accès à l’application web multicouches en cours d’exécution dans le cloud pour les utilisateurs externes, avec AAD fournissent une authentification mot de passe.

- Activer l’accès à l’application web multicouches en cours d’exécution dans le cloud pour les utilisateurs en cours d’exécution de l’organisation, avec AAD fournissent une authentification mot de passe.

### <a name="prerequisites"></a>Conditions préalables

La procédure suivante suppose les conditions préalables suivantes :

- Vous avez un abonnement Azure existant dans lequel vous pouvez créer des groupes de ressources.

- Vous avez installé l' [Interface de ligne Azure][azure-cli].

- Vous avez téléchargé et installé la version la plus récente. Voir [ici] [ azure-powershell-download] pour obtenir des instructions.

- Vous avez installé une copie de la [makecert] [ makecert] utilitaire sur l’ordinateur client de test.

- Vous avez accès à un ordinateur de développement avec Visual Studio est installé.

### <a name="create-the-n-tier-web-application-architecture"></a>Créer l’architecture d’application web multicouches

1. Créez un dossier nommé `Scripts` qui contient un sous-dossier nommé `Parameters`.

2. Télécharger le [Déploiement ReferenceArchitecture.ps1] [ solution-script] script PowerShell pour le dossier Scripts.

3. Dans le dossier Paramètres, créez un autre sous-dossier nommé Windows.

4. Téléchargez les fichiers suivants vers dossier Paramètres/Windows :

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Modifier le fichier **Déployer ReferenceArchitecture.ps**1 dans le dossier Scripts et modifiez la ligne suivante pour spécifier le groupe de ressources qui doit être créé ou utilisé pour stocker la machine virtuelle et ressources créées par le script :

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Modifier les fichiers suivants dans le **dossier/fenêtre Paramètres**et définir le `resourceGroup` valeurs contenues dans la `virtualNetworkSettings` section dans chacun de ces fichiers soit différente de celle que vous avez spécifié dans le fichier de script **Déployer ReferenceArchitecture.ps1** .

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Ouvrez une fenêtre PowerShell Azure, déplacer vers le dossier Scripts et exécutez la commande suivante :

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Remplacer **<subscription id>** avec votre ID d’abonnement Azure.

    Pour **<location>**, spécifiez une région Azure, par exemple *eastus* ou *westus*.

8. Une fois le script terminé, utiliser le portail Azure pour obtenir l’adresse IP de l’équilibrage de charge niveau web (*kg du web multicouches ra aad*) :

    [! [18]][18]

9. Se connecter à votre client test ordinateur (ou machine virtuelle), puis vérifiez que vous pouvez accéder à la couche web à l’aide d’Internet Explorer pour accéder à l’adresse IP de l’équilibrage de charge niveau web. La page IIS par défaut doit apparaître.

### <a name="simulate-configuration-of-a-public-web-site"></a>Simuler configuration d’un site web public

>[AZURE.NOTE] Les étapes suivantes simulent associant le niveau web du nom DNS www.contoso.com en modifiant le fichier hosts sur l’ordinateur client de test. En outre, les ordinateurs virtuels niveau web sont configurés avec un faux hébergeant autorité et certificats auto-signé. Il s’agit de test uniquement à des fins et **que vous ne devez pas utiliser ces techniques dans un environnement de production**.

1. Sur votre ordinateur client de test, modifiez le fichier **C:\Windows\System32\drivers\etc\hosts** en utilisant le bloc-notes et ajouter une entrée qui associe le nom www.contoso.com à l’adresse IP de l’équilibrage de charge niveau web :

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Vérifiez que vous pouvez désormais accéder à www.contoso.com à partir de l’ordinateur client de test. La même page IIS par défaut doit apparaître comme auparavant.

3. Sur l’ordinateur client de test, ouvrez une invite de commandes en tant qu’administrateur et utiliser l’utilitaire makecert à c
4. créer une autorité de certification racine faux :

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Vérifier que les fichiers suivants sont créés :

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Exécutez la commande suivante pour installer l’autorité de certification racine test :

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Utilisez l’autorité de certification test pour générer un certificat pour www.contoso.com :

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Exécutez la commande **mmc** et ajoutez le composant logiciel enfichable Certificats pour le compte d’ordinateur pour l’ordinateur local.

7. Dans la */Certificates (ordinateur Local) / personnel/certificat/* stocker, exportez le certificat www.contoso.com avec sa clé privée dans un fichier nommé www.contoso.com.pfx :

    [! [20]][20]

8. Revenez au portail Azure et se connecter à la couche gestion machine virtuelle (*vm1 du gestion multicouches ra aad*). Le nom d’utilisateur par défaut est *testuser* par mot de passe *AweS0me@PW*:

    [! [21]][21]
    
9. À partir du niveau de la gestion des machines virtuelles, connectez-vous à chacun de la couche web machines virtuelles à son tour avec nom d’utilisateur *testuser* et le mot de passe *AweS0me@PW*et effectuer les tâches suivantes. Notez que les ordinateurs virtuels ont privé adresses IP 10.0.1.4, 10.0.1.5 et 10.0.1.6 :

    >[AZURE.NOTE] La couche web machines virtuelles ont uniquement les adresses IP privées. Vous pouvez uniquement y connecter à l’aide de la couche de gestion des machines virtuelles.

    1. Copiez les fichiers *www.contoso.com.pfx* et *MyFakeRootCertificateAuthority.cer* à partir de l’ordinateur client de test.
    
    2. Ouvrez une invite de commandes en tant qu’administrateur, déplacer vers le dossier contenant les fichiers que vous avez copié et exécutez les commandes suivantes :
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Exécuter le `mmc` de commande, ajoutez le composant logiciel enfichable Certificats pour le compte d’ordinateur pour l’ordinateur local et vérifiez que les certificats suivants ont été installés :

        - \Certificates (ordinateur local) \Personal\Certificates\ www.contoso.com

        - Authorities\Certificates\MyFakeRootCertificateAuthority de Certification racine \Certificates (ordinateur local) \Trusted

    4. Démarrez la console Gestionnaire des Services Internet (IIS) et accédez au *Site Web par* l’ordinateur.

    5. Dans le volet *Actions* , cliquez sur *les liaisons*et ajoutez une liaison https en utilisant le certificat SSL www.contoso.com :

        [! [22]][22]

10. Revenir à l’ordinateur client de test et vérifiez que vous pouvez maintenant, accédez à https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Créez un client Azure Active Directory

1. À l’aide du portail Azure, créez un client Azure Active Directory tels que *myaadname*. onmicrosoft.com, où *myaadname* est un nom de répertoire que vous avez choisi.

2. Ajouter un utilisateur nommé *administrateur* avec le rôle GlobalAdmin à l’annuaire. Prenez note du mot de passe nouvellement créé.

3. Utilisation d’Internet Explorer, accédez à https://account.activedirectory.windowsazure.com/ et connectez-vous en tant que admin@ *myaadname*. onmicrosoft.com. Modifier votre mot de passe lorsque vous y êtes invité.

### <a name="create-and-deploy-a-test-web-application"></a>Créer et déployer une application web test

1. À l’aide de Visual Studio sur l’ordinateur de développement, créez une application Web ASP.NET nommée ContosoWebApp1 (à l’aide du .NET Framework 4.5.2) :

    [! [23]][23]

2. Sélectionnez le modèle *MVC* , modifiez l’authentification *comptes professionnel et votre établissement scolaire*et indiquez le nom de votre client AAD. Ne créez pas un Service d’application dans le cloud.

    [! [24]][24]

3. Générez et exécutez l’application pour tester l’authentification. Dans la page de connexion, entrez le nom du compte admin@ *myaadname*. onmicrosoft.com, fournissent le mot de passe, puis cliquez sur *se connecter*:

    [! [25]][25]

4. Vérifiez que AAD vous invite à indiquer d’autorisation pour vous connecter et lire votre profil et commence alors à exécuter l’application web avec l’identité de l’administrateur.

5. Fermez Internet Explorer et revenir à Visual Studio.

6. Ouvrez le fichier web.config, puis, dans le `<appSettings>` section, modifiez la valeur de la clé *ida : PostLogoutRedirectUri* à *https://www.contoso.com:443 /*. Enregistrez le fichier.

7. Dans la fenêtre *Explorateur de solutions* , cliquez sur le projet ContosoWebApp1 et cliquez sur *Publier*.

8. Dans la fenêtre *Publier le site Web* , cliquez sur *personnalisé*. Créer un profil personnalisé nommé *ContosoWebApp1*.

9. Dans la page de *connexion* , définissez la *méthode de publication* à un *Système de fichiers* et spécifiez un dossier nommé *ContosoWebApp*, situé dans un emplacement sur votre ordinateur de développement.

10. Dans la page *paramètres* , définissez la *Configuration* vers *la version*.

11. Dans la page *d’Aperçu* , cliquez sur *Publier*.

12. Connectez-vous à chaque serveur web à son tour (par le biais du niveau de la gestion des machines virtuelles) et effectuez les tâches suivantes :

    1. Copiez le dossier *ContosoWebApp* et son contenu à partir de l’ordinateur de développement vers le dossier *C:\inetpub* .

    2. À l’aide de la console Gestionnaire des Services Internet (IIS), accédez au *Site Web par* sur l’ordinateur.

    3. Dans le volet *Actions* , cliquez sur *Paramètres de base*et modifiez le chemin d’accès physique du site web à *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Publier l’application web test via AAD

1. Connectez-vous au portail Azure et naviguez jusqu'à votre répertoire AAD.

2. Sous l’onglet *Applications* , cliquez sur l’application ContosoWebApp1.

3. Vérifiez que votre application est correctement ajoutée à l’annuaire, puis cliquez sur l’onglet *configurer* .

4. Modifiez l' *URL de la session* en https://www.contoso.com:443 et définissez l' *URL de réponse* à https://www.contoso.com:443 (la même URL).

5. Enregistrer la configuration.

6. Sur l’ordinateur client de test, accédez à https://www.contoso.com. Vérifiez que AAD vous invitant à vos informations d’identification, puis puis se connecter.

>[AZURE.NOTE] Il s’agit du point de terminaison pour le premier scénario : permettant d’accéder à l’application web multicouches en cours d’exécution dans le cloud pour les utilisateurs externes, avec AAD fournissent une authentification mot de passe.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Créer un environnement simulé en local avec Active Directory

L’environnement local contient deux contrôleur de domaine pour la `contoso.com` domaine et deux serveurs pour héberger la Azure AD Connect synchroniser service. Les serveurs pour l’hébergement Azure AD Connect ne sont pas à un domaine.

1. Dans l’Explorateur de fichiers, revenir au dossier Scripts contenant le script utilisé pour créer l’application web multicouches.

2. Dans le dossier Paramètres, ajoutez un autre sous-dossier nommé `Onpremise`.

3. Téléchargez les fichiers suivants vers le dossier Paramètres/locales :

    - [Ajouter-ajoute-domaine-controller.parameters.json][add-adds-domain-controller-parameters]

    - [créer-ajoute-forêt-extension.parameters.json][create-adds-forest-extension-parameters]

    - [desmachines virtuelles adc.parameters.json][virtualMachines-adc-parameters]

    - [desmachines virtuelles-connecteur Active Directory-joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [desmachines virtuelles adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [virtualNetwork ajoute dns.parameters.json][virtualNetwork-adds-dns-parameters]

5. Modifier le fichier de déploiement ReferenceArchitecture.ps1 dans le dossier Scripts et modifiez la ligne suivante pour spécifier le groupe de ressources qui doit être créé ou utilisé pour stocker la machine virtuelle et ressources créées par le script :

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Modifiez les fichiers suivants dans le dossier Paramètres/locales et définissez la `resourceGroup` valeurs contenues dans la `virtualNetworkSettings` section dans chacun de ces fichiers soit différente de celle que vous avez spécifié dans le fichier de script déployer ReferenceArchitecture.ps1.

    - desmachines virtuelles adds.parameters.json

    - desmachines virtuelles adc.parameters.json

    - virtualNetwork.parameters.json

    - virtualNetwork ajoute dns.parameters.json

7. Ouvrez une fenêtre PowerShell Azure, déplacer vers le dossier Scripts et exécutez la commande suivante :

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Remplacer `<subscription id>` avec votre ID d’abonnement Azure.

    Pour `<location>`, spécifiez une région Azure, tel que `eastus` ou `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Installer et configurer le service de synchronisation Azure AD Connect

La configuration illustrée dans ces étapes se compose de deux instances du service de synchronisation Azure AD Connect. La première est active pendant la configuration de la seconde en mode mise en attente pour permettre le basculement rapide et disponibilité en cas d’échec du premier serveur.

1. À l’aide du portail Azure, accédez au groupe de ressources en maintenant les ordinateurs virtuels pour les services de synchronisation Azure AD Connect (*ra-aad-locales-routage* par défaut), puis se connecter à la *vm1 du connecteur Active Directory locales ra aad* machine virtuelle. Connectez-vous en tant qu' *UtilisateurTest* par mot de passe *AweS0me@PW*.

2. Télécharger Azure AD Connect [ici][aad-connect-download].

3. Exécutez le programme d’installation Azure AD Connect et effectuer une installation à l’aide de l’option *Personnaliser* .

    >[AZURE.NOTE] Vous ne pouvez pas utiliser l’option *Configuration rapide* , car la machine virtuelle qui héberge le service de synchronisation Azure AD Connect n’est pas à un domaine.

4. Dans la page *installer les composants requis* , laissez les paramètres de configuration facultatif vide pour accepter les options par défaut.

5. Dans la page de *connexion utilisateur* , sélectionnez *La synchronisation de mot de passe*.

6. Dans la page *se connecter à Active Directory Azure* , entrez admin@ *myaadname*. onmicrosoft.com, où *myaadname* est le nom de votre client AAD. Entrez le mot de passe du compte d’administrateur.

7. Dans la page *se connecter vos répertoires* , spécifiez contoso.com pour la forêt (tapez la valeur, car il ne s’affiche pas dans la liste déroulante), entrez CONTOSO\testuser pour le nom d’utilisateur, spécifiez AweS0me@PW le mot de passe, puis cliquez sur *Ajouter un répertoire*.

8. Dans la page *configuration de connexion Azure AD* , acceptez la valeur par défaut pour le nom d’utilisateur principal. Vérifier *Continuer sans les domaines vérifiés*.

    >[AZURE.NOTE] Le répertoire contoso.com sont répertoriés comme *Non vérifié*. Pour vérifier un nom de domaine, vous devez créer un enregistrement TXT pour votre bureau d’enregistrement de nom de domaine. Dans cet exemple, le domaine local n’est pas enregistré avec l’extérieur. Pour plus d’informations, voir [Ajouter un nom de domaine personnalisé à Azure Active Directory][aad-custom-directory].

9. Dans la page *filtrage de domaine et unité d’organisation* , sélectionnez *synchroniser tous les domaines et unités d’organisation* (par défaut).

10. Dans la page *identifiant de vos utilisateurs* , acceptez les valeurs par défaut.

11. Dans la page *filtrer les utilisateurs et dispositifs* , sélectionnez *synchroniser tous les utilisateurs et dispositifs* (par défaut).

12. Dans la page *fonctionnalités facultatives* , sélectionnez *mot de passe en écriture différée*.

13. Dans la page *prêt à configurer* , sélectionnez *Démarrer le processus de synchronisation lors de la configuration terminée*, mais laissez *Activer le mode intermédiaire* cette option est désactivée.

14. Vérifiez que le processus de configuration se termine sans erreur, puis quittez le programme d’installation.

15. À partir du portail Azure, se connecter à la machine virtuelle *ra-aad-locales-connecteur Active Directory-ordinateur virtuel 2* . Connectez-vous en tant qu' *UtilisateurTest* par mot de passe *AweS0me@PW*.

16. Télécharger Azure AD Connect, puis exécutez le programme d’installation.

17. Lancer l’Assistant et y répondre comme décrit dans les étapes 3 à 12.

18. Dans la page *prêt à configurer* , sélectionnez *Démarrer le processus de synchronisation à l’issue de configuration*, puis **Sélectionnez également** *Activer le mode de mise en attente*. Ainsi, le service de synchronisation Azure AD Connect récupérer les détails sur les comptes et des objets à partir du domaine contoso.com et la stocker dans sa base de données, mais elle ne transmettre ces informations à votre client AAD.

14. Vérifiez que le processus de configuration se termine sans erreur, puis quittez le programme d’installation.

### <a name="test-the-aad-configuration"></a>Testez la configuration AAD

1. À l’aide du portail Azure, basculez vers votre répertoire AAD, ouvrez la carte Azure Active Directory, cliquez sur *utilisateurs et groupes*, puis sur *tous les utilisateurs* pour afficher la liste des utilisateurs et groupes synchronisés avec l’annuaire. Vous devriez voir les utilisateurs pour les comptes suivants :

    - administrateur (admin@ *myaadname*. onmicrosoft.com)

    - Compte de Service de synchronisation de répertoire local (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Compte de Service de synchronisation de répertoire local (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. Dans le portail Azure, accédez au groupe de ressources en maintenant les ordinateurs virtuels pour le domaine AD DS (*ra-aad-locales-routage* par défaut), puis se connecter à la *vm1 du ad locales ra aad* machine virtuelle. Connectez-vous en tant qu' *UtilisateurTest* par mot de passe *AweS0me@PW*.

3. À l’aide de la console *Active Directory utilisateurs et ordinateurs* , ajouter un nouvel utilisateur de domaine nommé Diane Tibbot, avec nom de connexion dianet@contoso.com. Spécifiez un mot de passe de votre choix. Faire de l’utilisateur un membre du groupe Administrateurs local (il s’agit afin que vous pouvez ouvrir une session localement cet utilisateur plus tard - les ordinateurs uniquement dans le domaine sont DC).

4. Basculer vers la *vm1 du connecteur Active Directory locales ra aad* machine virtuelle, ouvrez une fenêtre PowerShell et exécutez les commandes suivantes :

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Vérifiez que la commande renvoie *succès*.

    >[AZURE.NOTE] Cette étape est nécessaire, car par défaut le processus de synchronisation est planifié pour exécuter à intervalles de 30 minutes. Ces commandes forcent une synchronisation immédiatement.

5. Revenez au portail Azure, basculez vers votre répertoire AAD, ouvrir la carte Azure Active Directory, cliquez sur *utilisateurs et groupes*, puis cliquez sur *tous les utilisateurs*. Vous devez maintenant voir Diane Tibbot (dianet@ *myaadname*. onmicrosoft.com) s’affichent dans la liste des utilisateurs.

6. Dans la carte Azure Active Directory, cliquez sur *Applications d’entreprise*, puis cliquez sur *toutes les applications*. Cliquez sur l’application *ContosoWebApp1* , puis cliquez sur *utilisateurs et groupes*. Dans la barre d’outils, cliquez sur *Ajouter*. Cliquez sur *utilisateurs et groupes*, puis sélectionnez *Ariane Tibbot*. Cliquez sur *attribuer*.

7. Utilisation d’Internet Explorer, accédez à la https://account.activedirectory.windowsazure.com site. Connectez-vous en tant que dianet@ *myaadname*. onmicrosoft.com et le mot de passe que vous avez spécifié précédemment.

    >[AZURE.NOTE] Ne cliquez pas sur l’icône ContosoWebApp dans la liste des applications. AAD essaie de trouver l’application web à l’adresse DNS répertorié publiquement pour www.contoso.com, qui est différent de l’adresse de votre équilibrage de charge niveau web.

8. Cliquez sur l’onglet *profil* . Les détails de l’utilisateur (si vous avez spécifié une lorsqu’il a été créé) doivent être affichés.

    >[AZURE.NOTE] Si vous cliquez sur *Modifier mot de passe*, vous n’êtes pas autorisé à effectuer cette tâche, cette opération doit être activé par un administrateur tout d’abord. Pour plus d’informations, voir [prise en main de gestion de mot de passe][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Permettre aux utilisateurs locaux exécuter l’application en utilisant l’authentification par le biais AAD

1. Revenir au contrôleur de domaine *vm1 du ad locales ra aad* machine virtuelle.

2. Ouvrez la console *Gestionnaire DNS* et ajouter un nouvel enregistrement hôte pour www.contoso.com. Spécifier l’adresse IP de l’équilibrage de charge niveau web.

3. Copiez le fichier *MyFakeRootCertificateAuthority.cer* à partir de l’ordinateur client test (vous avez créé ces fichiers dans la procédure de [configuration de simuler d’un site web public](#simulate-configuration-of-a-public-web-site)
    
4. Ouvrez une invite de commandes en tant qu’administrateur, déplacer vers le dossier contenant le fichier que vous venez de copier et exécutez la commande suivante :

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Utilisation d’Internet Explorer, accédez à https://www.contoso.com. Vérifiez que la page de connexion AAD pour l’application web ContosoWebApp1.

6. Connectez-vous en tant que dianet@ *myaadname*. onmicrosoft.com. L’application doit exécuter et vous connecter correctement.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les meilleures pratiques pour [l’extension de votre domaine : ajoute local vers Azure][adds-extend-domain]

- Découvrez les meilleures pratiques pour la [Création d’une forêt de ressources ajoute] [ adds-resource-forest] dans Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Architecture d’identité cloud à l’aide d’Azure Active Directory"
[1]: ./media/guidance-identity-aad/figure2.png "Un forêt, une topologie directory AAD"
[2]: ./media/guidance-identity-aad/figure3.png "Plusieurs forêts, seule topologie répertoire AAD"
[4]: ./media/guidance-identity-aad/figure5.png "Topologie de serveur de mise en attente"
[5]: ./media/guidance-identity-aad/figure6.png "Topologie de répertoires AAD multiples"
[6]: ./media/guidance-identity-aad/figure7.png "Sélection d’une installation personnalisée de Azure synchronisation AD se connecter à une instance spécifique de SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Spécifier la méthode d’authentification unique pour la connexion utilisateur"
[8]: ./media/guidance-identity-aad/figure9.png "Spécification de domaine et unité d’organisation options de filtrage"
[9]: ./media/guidance-identity-aad/figure10.png "L’activation de mot de passe écriture différée"
[10]: ./media/guidance-identity-aad/figure11.png "La carte de gestion Azure AD dans le portail"
[11]: ./media/guidance-identity-aad/figure12.png "La console Azure AD Connect"
[12]: ./media/guidance-identity-aad/figure13.png "L’onglet opérations dans le Gestionnaire de services de synchronisation"
[13]: ./media/guidance-identity-aad/figure14.png "L’onglet connecteurs dans le Gestionnaire de services de synchronisation"
[14]: ./media/guidance-identity-aad/figure15.png "L’éditeur de règles de synchronisation"
[15]: ./media/guidance-identity-aad/figure16.png "La carte de fonctionnement Azure Active Directory se connecter dans le portail Azure affichant l’état de synchronisation"
[16]: ./media/guidance-identity-aad/figure17.png "La carte de fonctionnement Azure Active Directory se connecter dans le portail Azure montrant l’état du service d’annuaire Active Directory"
[17]: ./media/guidance-identity-aad/figure18.png "Rapports de sécurité disponibles dans le portail Azure"
[18]: ./media/guidance-identity-aad/figure19.png "Le portail Azure mise en surbrillance de l’adresse IP de l’équilibrage de charge niveau web"
[19]: ./media/guidance-identity-aad/figure20.png "Utilisation d’Internet Explorer pour accéder à l’adresse IP de l’équilibrage de charge niveau web"
[20]: ./media/guidance-identity-aad/figure21.png "Le composant logiciel enfichable Certificats montrant le certificat www.contoso.com"
[21]: ./media/guidance-identity-aad/figure22.png "Se connecter à la couche gestion machine virtuelle"
[22]: ./media/guidance-identity-aad/figure23.png "Création de la liaison HTTPS pour le site web par défaut"
[23]: ./media/guidance-identity-aad/figure24.png "Création de l’application web ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Définition des propriétés d’authentification de l’application web ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "Se connecter à Azure AAD à partir de l’application web ContosoWebApp1"
[26]: ./media/guidance-identity-aad/figure27.png "Modifier le dossier pour le site web par défaut"