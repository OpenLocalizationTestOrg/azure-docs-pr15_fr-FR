<properties
   pageTitle="Architecture de Windows Azure référence - IaaS : Extension d’Active Directory vers Azure | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau sécurisée hybride avec l’autorisation d’Active Directory dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Extension de Services Active Directory (ajoute) vers Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour étendre votre environnement Active Directory (AD) vers Azure, pour fournir des services d’authentification distribuée à l’aide de [Services de domaine Active Directory (AD DS)][active-directory-domain-services]. Cette architecture étend qui décrit dans les articles [implémentation d’une architecture réseau hybride sécurisé dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [implémentation d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Vous utilisez les services AD DS pour authentifier les identités. Ces identités peuvent appartenir à des utilisateurs, ordinateurs, applications ou d’autres ressources qui font partie d’un domaine de sécurité. Vous pouvez héberger les services AD DS en local, mais dans un scénario hybride où se trouvent les éléments d’une application dans Azure, il peut être plus efficace pour répliquer cette fonctionnalité et le référentiel AD dans le cloud. Cette approche permet de réduire la latence provoquée par l’envoi d’authentification et les demandes d’autorisation locales à partir du cloud l’à AD DS en cours d’exécution en local. 

Il existe deux façons d’héberger vos services d’annuaire dans Azure :

1. Vous pouvez utiliser [Azure Active Directory (DAS)] [ azure-active-directory] pour créer un nouveau domaine AD dans le cloud et lien vers un local domaine Active Directory. Configuration puis [Azure AD Connect] [ azure-ad-connect] promesses répliquer identités contenues dans le le référentiel local dans le cloud. Notez que le répertoire dans le nuage n’est **pas** une extension du système local, il s’agit plutôt une copie qui contient la même identité. Modifications apportées à ces identités locales seront copiées dans le nuage, mais les modifications apportées dans le nuage **ne sont pas** être répliquées sur le domaine local. Par exemple, mot de passe doit être effectuées en local et Azure AD Connect permet de copier la modification dans le cloud. Notez également que la même instance de AAD peut être liée à plusieurs instances de AD DS ; AAD contiendra les identités de chaque référentiel AD auquel il est lié.

    DAS est utile dans les situations où le réseau local et le réseau virtuel Azure qui héberge les ressources de cloud ne sont pas directement liés à l’aide d’un tunnel VPN ou circuit ExpressRoute. Bien que cette solution soit simple, est peut-être pas approprié pour les systèmes où les composants peuvent migrer au-delà de la limite de cloud/le locaux comme ceci peut demander de reconfigurer AAD. En outre, AAD gère uniquement l’authentification des utilisateurs au lieu d’authentification de l’ordinateur. Certaines applications et services, telles que SQL Server, peuvent nécessiter l’authentification des ordinateurs auquel cas cette solution n’est pas le cas.

2. Vous pouvez déployer une machine virtuelle exécutant les services AD DS comme contrôleur de domaine dans Azure, extension de votre infrastructure AD existant à partir de votre centre de données locale. Cette approche est plus courante pour les scénarios où le réseau local et le réseau virtuel Azure sont connectés par une connexion VPN et/ou ExpressRoute. Cette solution prend également en charge bidirectionnelle réplication ce qui permet de vous apporter des modifications dans le nuage et en local, à l’endroit où il convient le mieux. Selon vos besoins de sécurité, l’installation AD dans le nuage peut être :
    - partie du même domaine en tant que cette tenue en local
    - un nouveau domaine au sein d’une forêt partagé
    - une forêt distincte

<!-- we might want to add info on how to choose from the three options above -->

Cette architecture se concentre sur solution 2, en utilisant le même domaine AD DS dans Azure et en local.

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Applications hybrides où les charges de travail exécuter partiellement en local et Azure partiellement en.

- Applications et services qui effectuer l’authentification en utilisant Active Directory.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture (*cliquez sur pour effectuer un zoom*). Pour plus d’informations sur les éléments grisé, voir [implémentation une architecture réseau hybride sécurisé dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [implémentation d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Réseau local.** Le réseau local inclut locales serveurs AD qui peuvent effectuer l’authentification et autorisation pour les composants situés en local.

- **Serveurs Active Directory.** Il s’agit des superflus mise en œuvre des services d’annuaire (AD DS) en cours d’exécution en tant que machines virtuelles dans le cloud. Ces serveurs peuvent fournir l’authentification des composants qui s’exécutent dans votre réseau virtuel Azure.

- **Sous-réseau annuaire Active.** Les serveurs AD DS sont hébergés dans un sous-réseau distinct. Les règles NSG protéger les serveurs AD DS et peuvent fournir un pare-feu contre le trafic de sources inconnues.

- **La synchronisation de passerelle azure et AD.**. La passerelle Azure fournit une connexion entre le réseau local et le VNet Azure. Cela peut être une [connexion VPN] [ azure-vpn-gateway] ou [Azure ExpressRoute][azure-expressroute]. Toutes les demandes de synchronisation entre les serveurs AD dans le cloud et en local passent via la passerelle. Itinéraires définis par l’utilisateur (UDRs) gérer le routage pour le trafic de synchronisation qui passe directement sur le serveur AD dans le cloud et ne passe pas dans le réseau virtuel matériels existants (NVAs) utilisé dans ce scénario.

    Pour plus d’informations sur la configuration UDRs et les NVAs, voir [mise en œuvre une architecture de réseau sécurisée hybride dans Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recommandations

Cette section résume les recommandations pour implémenter AD DS dans Azure, détaillé :

- Recommandations machine virtuelle.

- Recommandations de mise en réseau.

- Recommandations de sécurité. 

- Recommandations actives Directory Site.

- Recommandations de placement actives Directory FSMO.

>[AZURE.NOTE] Le document [instructions de déploiement d’Active Directory Windows Server Azure machines virtuelles en fonctionnement] [ ad-azure-guidelines] contient des informations plus détaillées sur la plupart de ces points.

### <a name="vm-recommendations"></a>Recommandations machine virtuelle

Créer des ordinateurs virtuels avec les ressources nécessaires pour gérer le volume de trafic attendu. Utiliser la taille des ordinateurs hébergeant AD DS local comme point de départ. Surveiller l’utilisation des ressources ; Vous pouvez redimensionner les ordinateurs virtuels et mettre à l’échelle vers le bas s’ils sont trop volumineux. Pour plus d’informations sur le dimensionnement domaine AD DS, voir [Planification de la capacité pour les Services de domaine Active Directory][capacity-planning-for-adds].

Créer un disque de données virtuel distincts pour le stockage de la base de données, les journaux et SYSVOL pour AD. Ne pas stocker ces éléments sur le même disque que le système d’exploitation. Notez que par défaut, disques de données qui sont joints à un ordinateur virtuel utilisent écriture via la mise en cache. Toutefois, cette forme de la mise en cache peut entrer en conflit avec les exigences des services AD DS. Pour cette raison, définissez le paramètre de *Préférence de Cache hôte* sur le disque de données sur *Aucun*. Pour plus d’informations, voir [Placement de la base de données Windows Server AD DS et SYSVOL][adds-data-disks].

Déployer au moins deux machines virtuelles exécutant les services AD DS comme domaine à votre réseau virtuel Azure pour des raisons de [disponibilité](#Availability-considerations) .

### <a name="networking-recommendations"></a>Recommandations de mise en réseau

Configurer l’interface réseau pour chacun des ordinateurs virtuels qui héberge les services AD DS avec des adresses IP privées statiques. Cette configuration prend en charge mieux DNS sur chacun des ordinateurs virtuels AD. Pour plus d’informations, voir [comment définir une adresse IP statique privée dans le portail Azure][set-a-static-ip-address].

> [AZURE.NOTE] Ne donnent pas les adresses IP publiques machines virtuelles AD DS. Consultez les sections [sécurité] [ security-considerations] pour plus d’informations.

Vous devez vous assurer que le trafic peut circuler librement entre les serveurs AD dans le cloud et en local :

- Ajouter des règles NSG à l’annonce dont l’autoriser le trafic entrant locales. Pour plus d’informations sur les ports qui utilisent les services AD DS, voir [Active Directory et Active Directory Domain Services Port requis][ad-ds-ports].

- Vérifiez que les tables UDR n’achemine pas le trafic AD DS via les NVAs utilisées dans ce scénario. Pour vos propres déploiements, selon les NVAs que vous utilisez, vous souhaiterez rediriger le trafic correspondant.

### <a name="security-recommendations"></a>Recommandations de sécurité

Des serveurs AD DS gérer l’authentification et sont donc des éléments très sensibles. Empêcher l’exposition directe des serveurs AD DS à Internet. Placer des serveurs AD DS dans un sous-réseau séparé, avec sa propre pare-feu. Vérifiez que les ports nécessaires pour utiliser les services AD DS pour l’authentification et d’autorisation et des serveurs partiels restent ouvertes, mais ferment tous les autres ports. Pour plus d’informations, voir [Active Directory et Active Directory Domain Services Port requis][ad-ds-ports]. Les [composants d’une Solution] [ solution-components] section plus loin dans ce document indique la NSG règles que l’exemple de solution utilise pour ouvrir les ports.

Vous pouvez utiliser des règles NSG pour créer un pare-feu simple. Si vous avez besoin d’une protection plus complète que vous pouvez implémenter un périmètre de sécurité supplémentaires autour des serveurs en utilisant une paire de sous-réseaux et NVAs, comme décrit dans le document [mise en œuvre une architecture de réseau sécurisé hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Utilisation du chiffrement disque BitLocker ou Azure pour chiffrer le disque qui héberge la base de données AD DS.

### <a name="active-directory-site-recommendations"></a>Recommandations Site Directory actives

Vous pouvez utiliser des sites dans AD DS groupe gagnante risquez qui sont connectés par une liaison rapide. Contrôleurs de domaine dans le même site AD DS répliquent leurs modifications répertoire automatiquement, et une configuration minimale est nécessaire de gérer la réplication.

POUR contrôler le trafic de réplication entre Azure et votre centre de données locale, il est recommandé d’ajouter un site AD DS distinct pour représenter l’espace d’adressage utilisé par Azure. Vous pouvez configurer un lien de site entre vos localement AD DS sites et contrôler la réplication intersites plus efficacement.

Vous pouvez également utiliser la séparation de site pour appliquer différents objets stratégie de groupe (stratégie de groupe) à joindre des ordinateurs à Azure et de tirer parti des applications qui sont « site prenant en charge », tels que le Gestionnaire de Configuration de System Center.

### <a name="active-directory-fsmo-placement-recommendations"></a>Recommandations de placement actives Directory FSMO

Les serveurs opération FSMO (Single Master) flexibles sont superflus spécialisés, reposnsible la cohérence des données dans l’ensemble des paramètres différents dans AD DS, présentées ci-après.

- **Contrôleur de schéma**. Il s’agit d’un rôle forêt qui conserve la structure du schéma utilisé par les services AD DS.

- **Masque des noms de domaine**. Il s’agit d’un rôle forêt qui gère les informations sur les noms de domaine au sein de la forêt.

- **Contrôleur de domaine ()**. Il s’agit d’un rôle au niveau du domaine. Contrôleur de domaine principal gère les mises à jour de mot de passe et les verrouillages de compte. Il est consulté par les autres DC quand les demandes de service d’authentification des mots de passe qui ne correspondent pas. Contrôleur de domaine principal gère les mises à jour de la stratégie de groupe et est la cible du contrôleur de domaine pour les anciennes applications et certains outils d’administration à effectuer quelques opérations accessible en écriture.

- **Masque des ID relatifs (RID)**. Identificateurs sont utilisées pour aider à identifier des objets au sein d’un répertoire. Ce serveur est chargé de générer un pool de domaine à l’échelle d’identificateurs relatifs pour une utilisation par tous les serveurs AD au sein du domaine.

- **Rôle d’infrastructure**. Un objet d’un domaine peut faire référence à un objet dans un autre domaine. Ce rôle au niveau du domaine est responsable de la mise à jour d’un objet identificateur de sécurité et un nom unique dans une référence d’objet de domaines. Notez qu’un serveur implémentation de ce rôle ne peut pas servir également un serveur (catalogue Global).

Pour plus d’informations, voir [rôles Active Directory FSMO dans Windows][AD-FSMO-roles-in-windows].

Dans ce scénario, nous vous recommandons de que vous évitez déployer des rôles FSMO Contrôleur d’Azure. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Créer une disponibilité défini pour les serveurs AD. Assurez-vous qu’il existe au moins deux serveurs dans le jeu. Les serveurs AD dans le cloud doivent être contrôleurs de domaine dans le même domaine. Vous activez la réplication automatique entre les serveurs.

Envisagez également de désigner des serveurs en tant que [maîtres d’opérations en attente] [ standby-operations-masters] en cas d’échec de la connectivité à un serveur ayant un rôle FSMO.

## <a name="security-considerations"></a>Considérations sur la sécurité

Si toutes les tâches d’administration de domaine sont susceptibles d’être effectuée à l’aide de DC en local, envisagez d’effectuer DC dans le nuage en lecture seule. Un contrôleur de domaine en lecture seule uniquement gère un sous-ensemble d’informations d’identification des utilisateurs (suffisant pour effectuer l’authentification en local) et peut être configuré en cache les informations uniquement pour des utilisateurs spécifiques. Par conséquent, si un contrôleur de domaine en lecture seule est compromis, seules les informations pour les utilisateurs mis en cache sont affectées, plutôt que les détails de chaque compte dans le domaine. Pour plus d’informations, voir [Superflus en lecture seule][read-only-dc].

Pour réduire la vulnérabilité des comptes d’utilisateurs individuels et prévenir les tentatives d’intrusion, procédez comme nous vous recommandons d’et gérer les mots de passe des utilisateurs dans Active Directory. Pour plus d’informations, voir [Meilleures pratiques pour appliquer les stratégies de mot de passe][best_practices_ad_password_policy]. En outre, en veillant à ne les groupes auxquels vous attribuez aux utilisateurs. Par exemple, ne faites pas les utilisateurs ordinaires membres du groupe Administrateurs d’entreprise, les administrateurs de schéma regrouper et groupe Domain Admins.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Active Directory est automatiquement adapte de superflus qui font partie du même domaine. Demandes sont réparties entre tous les contrôleurs dans un domaine. Vous pouvez ajouter un autre contrôleur de domaine, et il sera automatiquement synchronisée avec le domaine. Ne configurez pas un équilibrage de charge distincts pour acheminer le trafic vers contrôleurs au sein du domaine. Veiller à ce que toutes les superflus suffisamment de mémoire et ressources de stockage pour gérer la base de données du domaine ; dans l’idéal, que tous les contrôleur de domaine machines virtuelles la même taille.

## <a name="management-considerations"></a>Considérations sur la gestion

Ne pas copier ces fichiers de domaine au lieu d’effectuer des sauvegardes régulières, car restauration peut entraîner des incohérences dans l’état entre superflus.

Arrêter et redémarrer une machine virtuelle qui s’exécute le rôle de contrôleur de domaine dans Azure au sein du système d’exploitation invité au lieu d’utiliser l’option Arrêter dans le portail Azure. Utiliser le portail Azure pour arrêter une machine virtuelle entraîne la machine virtuelle pour être libéré. Cette action réinitialise la mémoire virtuelle-GenerationID, qui est indésirable pour un contrôleur de domaine. Lorsque la mémoire virtuelle-GenerationID est réinitialisé, l’invocationID du référentiel AD est également réinitialisé, le pool RID est ignoré et SYSVOL est marquée comme non autorité.

## <a name="monitoring-considerations"></a>Considérations relatives au contrôle

Ne parvient pas à surveiller et tenir à jour un réseau de serveurs AD peut entraîner des problèmes tels que :

- **Échec de connexion**. Échec de connexion peut se produire dans le domaine ou forêt si une résolution de relation ou le nom de la confidentialité échoue, ou si un serveur de catalogue global ne peut pas déterminer l’appartenance au groupe universel.

- **Verrouillage du compte**. Comptes d’utilisateur et le service peuvent devenir verrouillés si le contrôleur de domaine principal n’est pas disponible ou Échec de la réplication entre plusieurs superflus.

- **Échec du contrôleur de domaine**. Si le lecteur contenant le fichier Ntds.dit manque d’espace disque, le contrôleur de domaine peut cesser de fonctionner.

- **Échec de l’application**. Applications critiques pour votre entreprise, tels que Microsoft Exchange ou une autre application de messagerie, peuvent échouer si les requêtes de livre adresse dans le répertoire échouer.

- **Données de l’annuaire n’est pas cohérente**. Si la réplication échoue pour une période prolongée, objets en double peuvent être créés dans l’annuaire et peuvent nécessiter approfondies de diagnostic et d’heure pour éliminer.

- **Échec de création de compte**. Un contrôleur de domaine ne peut pas créer des comptes d’utilisateur ou un ordinateur si elle a effectué le son alimentation d’ID relatifs et le maître RID n’est pas disponible.

- **Échec de stratégie de sécurité**. Si le dossier partagé SYSVOL ne réplique pas correctement, objets de stratégie de groupe et les stratégies de sécurité ne sont pas correctement appliquées aux clients.

Surveiller les serveurs AD avec soin et préparez-vous à remédier rapidement. Créer une liste de vérification de surveillance des tâches à effectuer à un intervalle approprié. Par exemple vous pouvez planifier quotidiennement les tâches critiques suivantes :

- Examiner et résoudre des alertes signalées contrôleur de domaine 

- Vérifiez que toutes les superflus peuvent communiquer et que la réplication fonctionne.

- S’assurer que SYSVOL reste partagé.

- Corriger les problèmes de synchronisation de l’heure.

- Vérifier la présence de l’espace disque sur les lecteurs physiques utilisés par Active Directory.

Vous pouvez effectuer d’autres tâches de routine moins fréquemment. Par exemple, faire passez en revue les relations d’approbation et vérifier les approbations obsolètes ou incorrectes hebdomadaire et vérifiez que toutes les superflus sont exécutent à l’aide de la même service packs et correctifs correctif tous les mois.

Pour plus d’informations, consultez [Surveillance Active Directory][monitoring_ad]. Vous pouvez installer les outils tels que [Microsoft Systems Center] [ microsoft_systems_center] sur le serveur de surveillance (voir le [diagramme de l’architecture][architecture]) pour aider à effectuer les tâches suivantes. 

## <a name="solution-components"></a>Composants de la solution

La solution fournie pour cette architecture crée un réseau hybride sécurisé comme décrit dans les documents [implémentation d’une architecture réseau hybride sécurisé dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [implémentation d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], mais avec l’ajout des éléments suivants :

- Un groupe de ressources Azure nommé *nom de base*- dns-routage, où le *nom de base* est un préfixe que vous spécifiez lors du déploiement de la solution.

- Deux Azure machines virtuelles appelé *nom de base*-ad1-machine virtuelle et le *nom de base*-ad2-machine virtuelle, créé dans le groupe de ressources de *nom de base*- dns routage. Ces machines virtuelles sont configurés comme serveurs AD avec les Services d’annuaire et DNS installé et configuré.

- Un NSG nommé *nom de base*-ad-nsg dans le groupe de ressources Azure - ntwk routage *nom de base*. Ce groupe de ressources fait partie de l’infrastructure qui constituent le réseau hybride sécurisé, mais le nouveau NSG a été ajouté qui définit les règles de sécurité entrant pour les serveurs de publicités comme indiqué dans le tableau suivant :


    Priorité|Nom|Source|Destination|Service|Action|
    --------|----|------|-----------|-------|------|
    170|vnet-port53|10.0.0.0/16|Tout|Custom(Any/53)|Autoriser|
    180|vnet-port88|10.0.0.0/16|Tout|Custom(Any/88)|Autoriser|
    190|vnet-port135|10.0.0.0/16|Tout|Custom(Any/135)|Autoriser|
    200|vnet-à-port137-9|10.0.0.0/16|Tout|Custom(Any/137-139)|Autoriser|
    210|vnet-port389|10.0.0.0/16|Tout|Custom(Any/389)|Autoriser|
    220|vnet-port464|10.0.0.0/16|Tout|Custom(Any/464)|Autoriser|
    230|vnet à rpc dynamiques|10.0.0.0/16|Tout|Custom(Any/49152-65535)|Autoriser|
    240|onprem-ad-port53|192.168.0.0/24|Tout|Custom(Any/53)|Autoriser|
    250|ad onprem à port88|192.168.0.0/24|Tout|Custom(Any/88)|Autoriser|
    260|ad onprem à port135|192.168.0.0/24|Tout|Custom(Any/135)|Autoriser|
    270|ad onprem à port389|192.168.0.0/24|Tout|Custom(Any/389)|Autoriser|
    280|ad onprem à port464|192.168.0.0/24|Tout|Custom(Any/464)|Autoriser|
    290|Autoriser la rdp-gestion|10.0.0.128/25|Tout|Custom(Any/3389)|Autoriser|
    300|Autoriser passerelle|10.0.255.224/27|Tout|Custom(Any/Any)|Autoriser|
    310|Autoriser automatique|10.0.255.192/27|Tout|Custom(Any/Any)|Autoriser|
    320|vnet-refuser|Tout|Tout|Custom(Any/Any)|Autoriser|

    Les services AD DS utilise les ports 53, 89, 135, 389 et 464 pour accepter la réplication entrante et le trafic d’authentification. Dans cette table, le contrôleur de domaine local est dans la 192.168.0.0/24 espace adresse (votre espace d’adressage peut-être varier : vous indiquez ces informations en tant que paramètre et un modèle déployé par la solution.

    La NSG définit également les règles de sécurité sortante suivantes qui permettent la synchronisation et l’autorisation le flux du trafic sur le réseau local :

    Priorité|Nom|Source|Destination|Service|Action|
    --------|----|------|-----------|-------|------|
    100|absence port53|Tout|192.168.0.0/24|Custom(Any/53)|Autoriser|
    110|hors port88|Tout|192.168.0.0/24|Custom(Any/88)|Autoriser|
    120|hors port135|Tout|192.168.0.0/24|Custom(Any/135)|Autoriser|
    130|hors port389|Tout|192.168.0.0/24|Custom(Any/389)|Autoriser|
    140|hors port445|Tout|192.168.0.0/24|Custom(Any/445)|Autoriser|
    150|hors port464|Tout|192.168.0.0/24|Custom(Any/464)|Autoriser|
    160|absence-rpc-dynamiques|Tout|192.168.0.0/24|Custom(Any/49152-65535)|Autoriser|

Le script fourni avec la solution également effectuer les tâches suivantes :

- Il ajoute le *nom de base*-ad1-machine virtuelle et le *nom de base*-ad2-serveurs machine virtuelle comme domaine sur le domaine. Vous pouvez afficher ces serveurs dans la console *ordinateurs et utilisateurs Active Directory* dans le contrôleur de domaine local :

![[1]][1]

- Il crée un nouveau sous-réseau (10.0.0.0/16) pour un site AD nommé Azure-VNet-Ad-Site pour le domaine. Ce site contient le *nom de base*-ad1-machine virtuelle et le *nom de base*-ad2-serveurs machine virtuelle. 

- Il ajoute des paramètres de communication entre les sites IP que configurer l’intervalle de réplication entre le site local et le domaine dans le cloud. Vous pouvez consulter les paramètres de sous-réseau, des sites et des paramètres de transport dans la console *Sites Active Directory et les serveurs* dans le contrôleur de domaine local :

![[2]][2]

## <a name="deployment"></a>Déploiement

L’exemple de solution comporte les prerequsites suivants :

- Vous avez déjà configuré votre domaine local, et que vous avez configuré DNS et installé services Routage et accès distant pour prendre en charge un réseau VPN se connecter à la passerelle VPN Azure.


- Vous avez installé la dernière version de l’infrastructure du langage commun Azure. [Suivez les instructions ci-après pour plus d’informations][cli-install].

- Si vous déployez la solution à partir de Windows, vous devez installer un outil qui fournit une interface de bash, tel que [Bureau GitHub][github-desktop].

>[AZURE.NOTE] Si vous n’avez pas accès à un domaine existant en local, vous pouvez créer un environnement de test à l’aide de [onpremdeploy.sh] [ onpremdeploy] bash script. Ce script crée un réseau et la mémoire virtuelle dans le nuage qui simule une configuration très simple en local. Modifiez ce script avant d’exécuter et définir les variables suivantes définis au début du fichier :
>
> - **BASE_NAME**. Un préfixe définies par l’utilisateur pour le groupe de ressources et machine virtuelle créée par le script. Cet article doit **pas comporter plus de 5 caractères** sinon que le script tentera de générer une machine virtuelle avec un nom non valide et échouer.
> 
> - **Abonnement**. Votre ID d’abonnement Azure. Le groupe de ressources est créé dans cet abonnement.
> 
> - **Emplacement**. L’emplacement dans lequel vous souhaitez créer le groupe de ressources, par exemple *eastus* ou *westus*Azure.
> 
> - **ADMIN_USER_NAME**. Le nom à utiliser pour le compte d’administrateur de l’ordinateur virtuel.
> 
> - **ADMIN_PASSWORD**. Le mot de passe du compte d’administrateur.

Procédez comme suit pour créer l’exemple de solution :

1. télécharger et modifier [azuredeploy.sh] [ azuredeploy] script et définir les paramètres suivants au début du fichier :

    - **BASE_NAME**. Un préfixe définies par l’utilisateur pour les groupes de ressources et machines virtuelles créés par le script. Comme auparavant, cet élément doit être **pas comporter plus de 5 caractères**.

    - **Abonnement**. Votre ID d’abonnement Azure.

    - **Type_système_exploitation**. Le système d’exploitation (*Windows* ou *Linux*) à utiliser pour le niveau d’accès web, entreprise et données machines virtuelles. Notez que tous les serveurs AD créés par le script exécutent Windows Server 2012, quel que soit ce paramètre.

    - **Nom_domaine**. Le nom du domaine local. Notez que si vous utilisez l’environnement créé par le script onpremdeploy.sh, cela doit être *contoso.com*.

    - **Emplacement**. L’emplacement dans lequel vous souhaitez créer les groupes de ressources Azure.

    - **ADMIN_USER_NAME**. Le nom à utiliser pour les comptes administrateur sur les ordinateurs virtuels différents.

    - **ADMIN_PASSWORD**. Le mot de passe du compte d’administrateur.

    - **ON_PREMISES_PUBLIC_IP**. L’adresse IP de l’ordinateur VPN en local.

    - **ON_PREMISES_ADDRESS_SPACE**. L’espace d’adressage interne du réseau local. Si vous utilisez l’environnement créé par le script onpremdeploy.sh, ce doit être 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. La clé partagée IPSec utilisée pour établir la connexion VPN entre le réseau local et la passerelle VPN Azure.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. L’adresse IP du serveur DNS local. Si vous utilisez l’environnement créé par le script onpremdeploy.sh, cela doit être 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** Le préfixe d’adresse du sous-réseau local. Si vous utilisez l’environnement créé par le script onpremdeploy.sh, ce doit être 192.168.0.0/24.

    >[AZURE.NOTE] Pour gagner du temps et ressources, le script ne crée pas les niveaux d’accès métier ou de données. Si vous avez besoin de ces éléments, vous pouvez supprimer la section suivante dans le script azuredeploy.sh :
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Ouvrez une invite de shell bash et déplacer vers le dossier contenant le script azuredeploy.sh.

3. Connectez-vous à votre compte Azure. Dans le bash shell, entrez la commande suivante :

    ```
    azure login
    ```

    Suivez les instructions pour vous connecter à Azure.

4. Exécutez la commande `./azuredeploy.sh`, puis patientez pendant que le script crée l’infrastructure réseau.

5. À l’invite, *Vérifiez que la VNet a été créé*, utilisez le portail Azure pour vérifier qu’un groupe de ressources nommé *nom de base*- ntwk-routage a été créé, et qu’il contient des éléments similaires à ceux indiqués dans l’image suivante :

    ![[3]][3]

    >[AZURE.NOTE] Dans les exemples présentés, *nom de base* a été définie sur *cloud* lorsque le script a été exécuté.

    Cliquez sur le *nom de base*- vnet VNet, cliquez sur *sous-réseaux*et vérifiez que vous ont créés les sous-réseaux illustrés ci-dessous :

    ![[4]][4]

6. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patientez pendant le niveau web et équilibrage de charge sont créés.

7. À l’invite, *Vérifiez que la couche Web a été créée correctement*, utiliser le portail Azure pour vérifier qu’un groupe de ressources appelé *nom de base*web-couche-routage a été créé, et qu’il contient des éléments similaires à ceux indiqués ci-dessous :

    ![[5]][5]

8. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patientez pendant les NVAs sont créés.

9. À l’invite, *Vérifiez que l’à couloirs a été créé correctement*, utilisez le portail Azure pour vérifier qu’un groupe de ressources appelé *nom de base*- gestion-routage a été créé avec le contenu suivant :

    ![[6]][6]

10. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patientez pendant le jumpbox est créé.

11. À l’invite, *Vérifiez que la jumpbox a été créé correctement*, utiliser le portail Azure pour vérifier que les éléments suivants ont été ajoutés au groupe de ressources - gestion du routage *nom de base*:

    - Un ensemble de disponibilité appelé *nom de base*- jb-en tant que.

    - Un ordinateur virtuel nommé *nom de base*- jb-machine virtuelle.

    - Une interface réseau appelée *nom de base*- jb-carte réseau.

12. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patientez pendant la passerelle VPN Azure et la connexion sont créés. Notez que cette étape peut prendre jusqu'à 30 minutes.

13. À l’invite, *Vérifiez que la passerelle VPN a été créée correctement*, utiliser le portail Azure pour vérifier que les éléments suivants ont été ajoutés au groupe de ressources - ntwk routage *nom de base*:

    - Une passerelle réseau local appelé lgw local.
    
    - Une passerelle réseau virtuel appelé vpngw - *nom de base*.

    - Une connexion de passerelle nommée *nom de base*- vnet-vpnconn. Notez que l’état de cette connexion peut être *non connecté* si vous n’avez pas encore configuré la fin en local de la connexion ; vous traitera cela plus tard.

14. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patienter pendant que les ordinateurs virtuels et autres ressources pour la zone DMZ sont créés.

15. À l’invite, *Vérifiez que la zone DMZ a été créée correctement*, utilisez le portail Azure pour vérifier qu’un groupe de ressources appelé *nom de base*-dmz-routage a été créé avec le contenu suivant :

    ![[7]][7]

16. À l’invite dans la fenêtre de shell bash, appuyez sur une touche. Les messages suivants doivent apparaître :

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Connectez-vous à votre ordinateur local qui se connecte à la passerelle Azure et configurer la connexion de manière appropriée. Ajouter des itinéraires à la passerelle locale qui achemine le requestsfor la plage d’adresses 10.0.0.0/16 via la passerelle vers la VNet. Les étapes de cette procédure varient en fonction de la façon de vous connecter. Consultez [implémentation d’une Architecture réseau hybride avec Azure et en local VPN] [ implementing-a-hybrid-network-architecture-with-vpn] pour plus d’informations.

    Notez que vous pouvez trouver l’adresse IP de la passerelle VPN Azure à l’aide du portail Azure pour examiner la passerelle vpngw - *nom de base*dans le groupe de ressources de routage de ntwk - *nom de base*:

    ![[8]][8]

    Vous pouvez déterminer si la connexion a été correctement établie en recherchant dans l’état de la connexion - vnet vpnconn *nom de base*. Il doit être défini à *connecté*.

    Pour tester la connexion, ouvrez une connexion Bureau à distance à la jumpbox (10.0.0.254) à partir d’un ordinateur dans votre réseau local.

17. À l’invite dans la fenêtre de shell bash, appuyez sur une touche. À la prochaine invite, *Appuyez sur une touche quelconque pour mettre à jour le paramètre VNet pour la VNet vers DNS en local*, appuyez sur une touche et patienter pendant la mise à jour les paramètres DNS pour la VNet à la valeur que vous avez spécifié comme paramètre **ON_PREMISES_DNS_SERVER_ADDRESS** dans le script azuredeploy.sh.

18. À l’invite, *Vérifiez que le paramètre du serveur DNS sur le VNet a été mis à jour*, utilisez le portail Azure pour examiner le paramètre *serveurs DNS* par le *nom de base*- vnet VNet dans le groupe de ressources de routage de ntwk - *nom de base*. Il doit être définie sur *Custom DNS*et le *serveur DNS principal* doit être l’adresse de votre serveur DNS local :

    ![[9]][9]

19. À l’invite *Appuyez sur une touche quelconque pour créer le groupe de ressources pour les serveurs AD* dans la fenêtre de shell bash, appuyez sur une touche et patienter pendant que le groupe de ressources pour conserver les serveurs AD dans le nuage est créé.

20. À l’invite *Appuyez sur une touche quelconque pour créer les ordinateurs virtuels pour les serveurs AD* dans la fenêtre de shell bash, appuyez sur une touche et attendre que les ordinateurs virtuels à être créés et ajoutés au groupe de ressources.

21. Lorsque la *Appuyez sur une touche quelconque pour joindre les ordinateurs virtuels au domaine local* s’affiche, accédez au portail Azure et vérifiez qu’un groupe appelé *nom de base*- dns-routage a été créé, et qu’il contient deux machines virtuelles (*nom de base*-ad1-machine virtuelle et le *nom de base*-ad2-machine virtuelle) :

    ![[10]][10]

22. Dans le groupe de ressources - ntwk routage de *nom de base*, vérifier qu’un NSG a été créé appelé *nom de base*-ad-nsg. Examinez les règles de sécurité entrant et sortant pour cette NSG. Ils doivent correspondre à ceux indiqués dans les tables dans les [composants d’une Solution] [ solution-components] section.

23. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et attendez que les ordinateurs virtuels soient ajoutés au domaine local.

24. À la *reportez-vous à la locale serveur AD pour vérifier que les ordinateurs ont été ajoutés aux domaines* invite, connectez-vous à votre ordinateur local et utiliser la console *utilisateurs Active Directory et ordinateurs* pour vérifier que les deux machines virtuelles ont été ajoutés au domaine :

    ![[11]][11]

25. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patientez pendant le site de réplication AD est créé dans le domaine.

26. À la *reportez-vous à la locale serveur AD pour vérifier que le site de réplication a été créé* invite, utilisez la console *Sites Active Directory et Services* pour vérifier qu’un site de réplication nommé *Azure-Vnet-Ad-Site* a été créé correctement, avec un lien de communication entre les sites IP intitulé *AzureToOnpremLink*et un sous-réseau qui fait référence à la VNet :

    ![[12]][12]

27. À l’invite dans la fenêtre de shell bash, appuyez sur une touche et patientez pendant le script installe des Services d’annuaire et DNS sur chacun des ordinateurs virtuels AD.

28. Lorsque l’invite, *Connectez-vous à chaque serveur Azure AD pour vérifier que les Services d’annuaire a été configuré correctement* s’affiche, ouvrez une connexion Bureau à distance à partir d’un ordinateur local à la jumpbox (*nom de base*- jb-machine virtuelle), puis un autre connexion Bureau à distance à partir de la jumpbox au premier serveur AD (*nom de base*-ad1-machine virtuelle). Connectez-vous en utilisant les **nom_domaine**, **ADMIN_USER_NAME**et **ADMIN_PASSWORD** que vous avez spécifié dans le script azuredeploy.sh. À l’aide du Gestionnaire de serveur, vérifiez que les rôles AD DS et DNS ont tous deux été ajoutées. Répétez cette procédure pour le deuxième serveur AD (*nom de base*-ad2-machine virtuelle).

29. À l’invite dans la fenêtre de shell bash, appuyez sur une touche. Lorsque l’invite *Appuyez sur une touche quelconque pour définir les paramètres Azure VNet DNS pour pointer vers le DNS dans Azure* s’affiche, appuyez sur une touche et autoriser le script mettre à jour les paramètres DNS pour la VNet.

30. Lorsque l’invite *Vérifiez que le DNS VNet paramètre a été mis à jour le DNS de machine virtuelle Azure référence serveurs* s’affiche, à l’aide de la case à cocher portail Azure le paramètre *DNS Servers* par le *nom de base*- vnet VNet dans le groupe de ressources de routage de ntwk - *nom de base*. Les serveurs DNS principales et secondaires doivent maintenant référencer les ordinateurs virtuels AD deux :

    ![[13]][13]

31. Redémarrer chacun des ordinateurs virtuels AD avant de poursuivre. Cette étape est nécessaire pour vous assurer que chaque ils décrochez les paramètres DNS appropriés à partir d’Azure. Attendez que les deux machines virtuelles en cours d’exécution avant de poursuivre.

32. À l’invite dans la fenêtre de shell bash, appuyez sur une touche. À l’invite suivante, *Appuyez sur une touche quelconque pour appliquer la passerelle UDR au sous-réseau passerelle (il a peut-être été supprimé)*, appuyez sur une touche et autoriser le script actualiser la passerelle UDR.

33. Vérifiez que le script est terminée avec succès.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les meilleures pratiques pour la [Création d’une forêt de ressources ajoute] [ adds-resource-forest] dans Azure.

- Découvrez les meilleures pratiques pour la [Création d’une infrastructure ADFS] [ adfs] dans Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Architecture de réseau hybride avec Active Directory sécurisée"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "La console utilisateurs Active Directory et ordinateurs répertoriant les ordinateurs virtuels Azure deux en tant que serveurs"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "La console Sites Active Directory et Services affichant les paramètres de réplication pour le site dans le cloud"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "Le contenu du groupe de ressources de nom de base-ntwk-routage"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "Les sous-réseaux dans le nom de base vnet VNet"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Les éléments de la couche web"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "Les NVAs dans le groupe de ressources de nom de base-gestion-Routage"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Les ressources dans le groupe de ressources de nom de base-dmz-routage"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Recherche de l’adresse IP de la passerelle VPN Azure"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "Les paramètres du serveur DNS pour la * nom de base *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "Le * nom de base *-dns-groupe de ressources de routage contenant le serveur AD machines virtuelles"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "La console utilisateurs Active Directory et ordinateurs répertoriant le serveur AD machines virtuelles en tant que membres du domaine"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "La console Sites Active Directory et Services montrant le site de réplication pour les serveurs Azure AD"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "Les paramètres du serveur DNS pour la VNet nom de base vnet référençant les serveurs AD dans le cloud"