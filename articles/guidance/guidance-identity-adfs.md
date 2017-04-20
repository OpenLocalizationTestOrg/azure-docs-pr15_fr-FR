<properties
   pageTitle="Mise en œuvre des services ADFS dans Azure | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau sécurisée hybride avec l’autorisation d’Active Directory Federation Service dans Azure."
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
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Mise en œuvre des Services de fédération Active Directory (AD FS) dans Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour la mise en œuvre d’un réseau hybride sécurisée qui s’étend votre réseau local à Azure, et qui utilise [Active Directory Federation Services (ADFS)] [ active-directory-federation-services] pour effectuer fédérée authentification et autorisation pour les composants qui s’exécutent dans le cloud. Cette architecture étend la structure décrite par [Extension d’Active Directory pour Azure][implementing-active-directory].

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

ADFS peut s’exécuter en local, mais dans un scénario hybride où se trouvent les applications dans Azure, il peut être plus efficace d’implémenter cette fonctionnalité dans le cloud. Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Applications hybrides où les charges de travail exécuter partiellement en local et Azure partiellement en.

- Solutions qui utilisent fédérés d’autorisation pour exposer des applications web aux organisations partenaires.

- Systèmes prenant en charge l’accès à partir de navigateurs web en cours d’exécution en dehors du pare-feu de votre entreprise.

- Systèmes qui permettent aux utilisateurs d’accéder aux applications web en vous connectant à partir d’appareils externes autorisés tels qu’ordinateurs distants blocs-notes et autres appareils mobiles. 

Pour plus d’informations sur le fonctionnement de ADFS, voir [Vue d’ensemble de Active Directory Federation Services][active-directory-federation-services-overview]. En outre, l’article [Déploiement ADFS dans Azure] [ adfs-intro] contient une présentation détaillée des instructions pas à pas à l’implémentation ADFS dans Azure.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture (*cliquez sur pour effectuer un zoom*). Pour plus d’informations sur n’importe quel élément non liée à ADFS, lisez [implémentation d’une architecture réseau hybride sécurisé dans Azure][implementing-a-secure-hybrid-network-architecture], [mise en œuvre une architecture de réseau sécurisé hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]et [implémentation d’une architecture de réseau sécurisée hybride avec des identités dans Azure Active Directory][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] Ce diagramme présente des exemples d’utilisation :
>
>- Code d’application qui s’exécute à l’intérieur d’une organisation partenaire accède à une application web hébergée à l’intérieur de votre VNet Azure.
>
>- Un utilisateur externe, enregistré (avec les informations d’identification stockées à l’intérieur : ajoute) qui accèdent à une application web hébergée à l’intérieur de votre VNet Azure.
>
>- Un utilisateur qui se connecte à votre VNet à l’aide d’un périphérique autorisé et exécutez une application web hébergée à l’intérieur de votre VNet Azure.
>
>Par ailleurs, cette architecture se concentre sur la fédération passive, où les serveurs de fédération décident quand et comment pour authentifier un utilisateur. L’utilisateur doit fournir les informations de connexion au démarrage d’une application en cours d’exécution. C’est le mécanisme plus fréquemment utilisé par les navigateurs web et implique un protocole qui redirige le navigateur vers un site dans lequel l’utilisateur peut fournir leurs informations d’identification. ADFS prend également en charge la fédération active dans laquelle une application prend responsabilité pour fournir des informations d’identification sans interaction utilisateur supplémentaire, mais ce cas est en dehors de l’étendue de cette architecture.

- **AJOUTE sous-réseau.** Les serveurs ajoute figurent dans leur propre sous-réseau. Les règles NSG permettant de protéger les serveurs ajoute et peuvent fournir un pare-feu contre le trafic de sources inconnues.

- **Ajoute des serveurs.** Il s’agit des superflus exécutés en tant que machines virtuelles dans le cloud. Ces serveurs peuvent fournir l’authentification des identités locales au sein du domaine.

- **Sous-réseau ADFS.** Les serveurs ADFS peuvent être situés dans leur propre sous-réseau, avec les règles NSG comme un pare-feu.

- **Serveurs ADFS.** Les serveurs ADFS fournissent l’authentification et l’autorisation fédérée. Dans cette architecture, ils effectuent les tâches suivantes :

    - Ils peuvent recevoir des jetons de sécurité contenant revendications effectuées par un serveur de fédération partenaire au nom d’un utilisateur partenaire. ADFS pouvez vérifier que ces jetons sont valides avant de le transmettre les revendications à l’application web qui s’exécute dans Azure. L’application web d’entreprise (dans Azure) peut utiliser ces revendications pour autoriser les demandes. Dans ce scénario, l’application web d’entreprise est le *lieu d’utiliser des tiers*, et il est la responsabilité de serveur de fédération partenaire d’émission revendications qui sont comprises par l’application web d’entreprise. Les serveurs de fédération partenaire sont appelés *partenaires de comptes* , car ils envoyer les demandes d’accès au nom de comptes authentifiés dans l’organisation du partenaire. Les serveurs ADFS sont appelés *partenaires de ressources* car ils permettent d’accéder à des ressources (applications web, dans le cas présent).

    - Ils peuvent s’authentifier (via ajoute et le [Service d’enregistrement de périphérique Active Directory][ADDRS]) et autorisez les demandes entrantes à partir d’un navigateur web ou un appareil nécessitant un accès à vos applications web d’entreprise en cours d’exécution des utilisateurs externes. 

    Les serveurs ADFS sont configurés comme une batterie de serveurs, accédé via un programme d’équilibrage de charge Azure. Cette structure contribue à améliorer la disponibilité et extensibilité élevées. Notez également que les serveurs ADFS ne sont pas exposés directement à Internet, au lieu de cela tout le trafic Internet est filtré à travers les serveurs proxy d’applications web ADFS et une DMZ.

- **Sous-réseau du proxy AD FS.** Les serveurs proxy AD FS pouvant être contenus dans leur propre sous-réseau, avec les règles NSG assurant la protection. Les serveurs de ce sous-réseau sont présentés à Internet via un ensemble de réseau authentification multifacteur fournissant un pare-feu entre votre réseau virtuel Azure et Internet.

- **Serveurs proxy (WAP) d’applications web ADFS.** Ces ordinateurs agissent en tant que serveurs ADFS demandes entrantes de périphériques externes et les organisations partenaires. Les serveurs WAP agissent en tant que filtre, protection contre les serveurs ADFS d’accéder directement à partir de l’Internet public. Comme avec les serveurs ADFS, déploiement le WAP serveurs dans une batterie de serveurs avec l’équilibrage de charge vous donne plus grande disponibilité et extensibilité élevées que le déploiement d’un ensemble de serveurs autonomes.

    >[AZURE.NOTE] Pour plus d’informations sur l’installation de serveurs WAP, voir [installer et configurer le serveur Proxy d’Application Web][install_and_configure_the_web_application_proxy_server]

- **Organisation du partenaire.** Il s’agit d’une exemple partenaire d’entreprise, qui s’exécute une application web qui demande l’accès à l’application web en cours d’exécution dans Azure. Le serveur de fédération à l’organisation du partenaire authentifie localement les demandes et envoie des jetons de sécurité contenant des revendications à ADFS en cours d’exécution dans Azure. Services ADFS dans Azure valide les jetons de sécurité, et si elles sont valides, il peut passer les revendications à l’application web en cours d’exécution dans Azure les autoriser.

    >[AZURE.NOTE] Vous pouvez également configurer un tunnel VPN à l’aide de la passerelle Azure pour fournir un accès direct à ADFS pour les partenaires approuvés. Demandes reçues à partir de ces partenaires ne passent pas par les serveurs WAP.

## <a name="recommendations"></a>Recommandations

Cette section résume les recommandations pour implémenter ADFS dans Azure, détaillé :

- Recommandations machine virtuelle.

- Recommandations de mise en réseau.

- Recommandations de disponibilité.

- Recommandations de sécurité.

- Recommandations d’installation ADFS.

- Gestion de la confidentialité ADFS recommandations.

### <a name="vm-recommendations"></a>Recommandations machine virtuelle

Créer des ordinateurs virtuels avec les ressources nécessaires pour gérer le volume de trafic attendu. Utiliser la taille des ordinateurs existants qui héberge ADFS local comme point de départ. Surveiller l’utilisation des ressources. Vous pouvez redimensionner les ordinateurs virtuels et mettre à l’échelle vers le bas s’ils sont trop volumineux.

Suivez les recommandations répertoriées dans [l’exécution d’une machine virtuelle Windows sur Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Recommandations de mise en réseau

Configurer l’interface réseau pour chacun des ordinateurs virtuels hébergeant des serveurs AD FS et WAP avec des adresses IP privées statiques.

Ne donnent pas les adresses IP publiques machines virtuelles ADFS. Pour plus d’informations, voir [Considérations relatives à la sécurité][security-considerations].

Définissez l’adresse IP des serveurs DNS secondaires et préférés pour les interfaces réseau pour chaque AD FS et WAP VM référencer les ordinateurs virtuels ajoute (qui doit exécuter DNS). Cette étape est nécessaire pour activer chaque machine virtuelle joindre le domaine.

### <a name="availability-recommendations"></a>Recommandations de disponibilité

Créer une batterie de serveurs ADFS avec au moins deux serveurs pour accroître la disponibilité du service ADFS.

Utiliser des comptes de stockage différents pour chaque VM ADFS dans la batterie de serveurs. Cette approche contribue à assurer qu’une défaillance dans un compte de stockage unique ne fait pas la batterie entière inaccessible.

Créer des jeux de disponibilité Azure distincts pour les services ADFS et machines virtuelles WAP. Assurez-vous qu’il existe au moins deux machines virtuelles dans chaque ensemble. Chaque jeu de disponibilité doit avoir au moins deux domaines de mise à jour et deux domaines d’erreur.

Configurer les programmes d’équilibrage de charge des chaînes ADFS machines virtuelles machines virtuelles WAP comme suit :

- Utilisez un équilibrage de charge Azure à fournir un accès externe pour les ordinateurs virtuels WAP et un équilibrage de charge interne pour répartir la charge sur les serveurs ADFS dans la batterie de serveurs ADFS.

- Uniquement achemine le trafic affichés sur le port 443 (HTTPS) vers les serveurs ADFS/WAP.

- Donnez à l’équilibrage de charge une adresse IP statique.

- Créer une sonde santé utilise le protocole TCP plutôt que HTTPS. Vous pouvez tester le port 443 pour vérifier qu’un serveur ADFS fonctionne.

    >[AZURE.NOTE] Serveurs ADFS utilisent le protocole Indication de nom de serveur (SNI), si vous tentez de sonde à l’aide d’un point de terminaison HTTPS à partir de l’échec d’équilibrage de charge.

- Ajouter un enregistrement DNS *A* pour le domaine pour l’équilibrage de charge ADFS. 

    Indiquez l’adresse IP de l’équilibrage de charge et lui donner un nom du domaine (par exemple, adfs.contoso.com). C’est le nom par lequel les clients et les serveurs WAP accéder à la batterie de serveurs ADFS.

### <a name="security-recommendations"></a>Recommandations de sécurité

Empêcher l’exposition directe des serveurs ADFS à Internet. Serveurs ADFS sont des ordinateurs à un domaine qui ont l’autorisation complète d’octroyer des jetons de sécurité. Si un serveur ADFS est compromis, un utilisateur malveillant peut émettre jetons d’accès complet à toutes les applications web et aux serveurs de fédération qui sont protégés par AD FS. Si votre système doit gérer les requêtes d’utilisateurs externes n’est pas nécessairement qui se connectent à partir des sites de confiance partenaire, utilisez les serveurs WAP pour gérer ces demandes. Pour plus d’informations, voir [où placer un serveur Proxy de fédération][where-to-place-an-fs-proxy].

Serveurs ADFS lieu et WAP dans sous réseaux distincts avec leur propre pare-feu. Vous pouvez utiliser des règles NSG pour définir des règles de pare-feu. Si vous avez besoin d’une protection plus complète que vous pouvez implémenter un périmètre de sécurité supplémentaires autour des serveurs en utilisant une paire de sous-réseaux et NVAs, comme décrit dans le document [mise en œuvre une architecture de réseau sécurisé hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Notez que tous les pare-feu doivent autoriser le trafic sur le port 443 (HTTPS).

Limiter l’accès direct connexion vers les serveurs ADFS et WAP. Seul le personnel DevOps doit être en mesure de vous connecter.

Ne pas participer les serveurs WAP sur le domaine.

### <a name="adfs-installation-recommendations"></a>Recommandations d’installation ADFS

L’article [déploiement d’une batterie de serveurs de fédération] [ Deploying_a_federation_server_farm] fournit des instructions détaillées pour installer et configurer des services ADFS. Avant de configurer le premier serveur ADFS dans la batterie de serveurs, effectuez les tâches suivantes :

1. Obtenir un certificat de confiance publique pour effectuer l’authentification de serveur. Le *nom de sujet* doit contenir le nom par lequel les clients accèdent au service de fédération. Cela peut être le nom DNS enregistré pour l’équilibrage de charge, par exemple, *adfs.contoso.com* (évitez d’utiliser des noms génériques tels que **. contoso.com*, pour des raisons de sécurité). Utiliser le même certificat sur tous les ordinateurs virtuels de serveur AD FS. Vous pouvez acheter un certificat auprès d’une autorité de certification approuvée, mais si votre organisation utilise Active Directory Certificate Services que vous pouvez créer votre propre. 

    L' *autre nom du sujet* est utilisé par le service DRS pour activer l’accès à partir d’appareils externes. Il s’agit de l' écran *enterpriseregistration.contoso.com*.

    Pour plus d’informations, consultez [obtenir et configurer un certificat SSL pour ADFS][adfs_certificates].

2. Sur le contrôleur de domaine, générer une nouvelle clé racine pour le Service de Distribution de clés. Définir le temps effectif à l’heure actuelle moins 10 heures (cette configuration réduit le délai pouvant se produire dans la distribution et la synchronisation des touches dans le domaine). Cette étape est nécessaire pour prendre en charge la création du compte de service de groupe qui sont utilisés pour exécuter le service ADFS. La commande Powershell suivante montre un exemple de la procédure à suivre :

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Ajoutez chaque serveur ADFS machine virtuelle sur le domaine.

>[AZURE.NOTE] Pour installer ADFS, le contrôleur de domaine en cours d’exécution Cet émulateur rôle FSMO pour le domaine doit être en cours d’exécution et accessibles à partir d’ordinateurs virtuels ADFS.

### <a name="adfs-trust-recommendations"></a>Gestion de la confidentialité ADFS recommandations

Établir une approbation de fédération entre votre installation ADFS et les serveurs de fédération de n’importe quel organisations partenaires. Configurez toutes les revendications filtrage et mappage requis. Ce processus requiert :

- DevOps personnel de **chaque organisation partenaire** pour ajouter une approbation tiers de confiance pour les applications web accessibles via vos serveurs ADFS.

- DevOps personnel **de votre organisation** pour configurer l’approbation de fournisseur de réclamations pour permettre à vos serveurs ADFS d’approuver les revendications qui fournissent des organisations partenaires.

- DevOps personnel **de votre organisation** pour configurer ADFS pour passer des revendications à des applications web de votre organisation.

    Pour plus d’informations, consultez [Établissement d’une confiance fédération][establishing-federation-trust].

Publier des applications web de votre organisation et les rendre disponibles pour les partenaires externes à l’aide de la pré-authentification via les serveurs WAP. Pour plus d’informations, voir [publier des Applications à l’aide de la pré-authentification ADFS][publish_applications_using_AD_FS_preauthentication]

Notez qu’ADFS prend en charge augmentation et à leur transformation jeton. Azure Active Directory ne fournit pas cette fonctionnalité. Avec les services ADFS, lorsque vous configurez les relations d’approbation, vous pouvez :

- Configurez des transformations réclamer pour les règles d’autorisation. Par exemple, vous pouvez mapper sécurité de groupe à partir d’une représentation utilisée par une organisation non Microsoft partenaire à un élément qui ajoute peut autoriser de votre organisation.

- Transformer des revendications d’un format à un autre. Par exemple, vous pouvez mapper à partir de SAML 2.0 SAML 1.1 si votre application prend uniquement en charge les revendications SAML 1.1. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vous pouvez utiliser SQL Server ou la base de données interne Windows (travail) pour conserver les informations de configuration AD FS. Travail fournit redondantes base. Modifications sont enregistrées directement à un seul des bases de données ADFS dans le cluster ADFS, tandis que les autres serveurs utilisent la réplication par réception à maintenir leurs bases de données à jour. À l’aide de SQL Server peut fournir redondance complète de la base de données et la disponibilité à l’aide de basculement cluster ou la mise en miroir.

## <a name="security-considerations"></a>Considérations sur la sécurité

ADFS utilise le protocole HTTPS, assurez-vous que les règles NSG pour le sous-réseau contenant le site web couche machines virtuelles permis HTTPS demandes. Ces requêtes peuvent provenir du réseau local, les sous-réseaux contenant la couche web, couche d’entreprise, couche données, DMZ privé, DMZ publique et le sous-réseau contenant les serveurs ADFS.

Envisagez d’utiliser un ensemble d’authentification multifacteur réseau qui se connecte des informations détaillées sur le trafic parcourir le bord de votre réseau virtuel pour les audits.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Les considérations suivantes, résumées du document [planifier votre déploiement ADFS][plan-your-adfs-deployment], envoyer un point de départ pour batteries de serveurs ADFS de redimensionnement :

- Si vous avez moins de 1 000 utilisateurs, ne créez pas de serveurs ADFS dédiés, mais plutôt installer ADFS sur chacun des serveurs : ajoute dans le cloud (Assurez-vous que vous disposez au moins deux serveurs ajoute, pour assurer la disponibilité). Créer un serveur WAP unique.

- Si vous avez entre 1000 et 15000 utilisateurs, créez deux serveurs ADFS dédiés et deux serveurs WAP dédiés.

- Si vous avez entre 15000 et 60000 utilisateurs, créez entre trois et cinq serveurs ADFS dédiés et au moins deux serveurs WAP dédiés.

Ces chiffres part du principe que vous utilisez deux cœurs machines virtuelles (D4_v2 Standard, ou mieux) pour héberger les serveurs dans Azure.

Notez que si vous utilisez la base de données interne Windows pour stocker les données de configuration ADFS, vous êtes limité à huit serveurs ADFS dans la batterie de serveurs. Si vous pensez avoir besoin de plus, puis utiliser SQL Server. Pour plus d’informations, voir [Le rôle de la base de données de Configuration ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Considérations sur la gestion

DevOps personnel doit être prêt à effectuer les tâches suivantes :

- Gérer les serveurs de fédération (gestion de la batterie de serveurs ADFS, gestion de la stratégie de gestion de la confidentialité sur les serveurs de fédération et gérer les certificats utilisés par les services de fédération).

- Gérer les serveurs WAP (gestion de la batterie de serveurs WAP, gérer les certificats WAP).

- Gestion des applications web (configuration des parties de confiance, les méthodes d’authentification et les mappages de revendications).

- Sauvegarde des composants ADFS.

## <a name="monitoring-considerations"></a>Considérations relatives au contrôle

Le [Management Pack Microsoft System Center pour Active Directory Federation Services 2012 R2] [ oms-adfs-pack] fournit une surveillance proactive et réactive de votre déploiement ADFS pour le serveur de fédération. Ce management pack surveille :

- Événements que les ADFS service d’enregistrements dans les journaux d’événements ADFS.

- Les données de performance collectent les compteurs de performance ADFS. 

- Le fonctionnement global des applications web et système ADFS (parties de confiance) et fournit des alertes pour des problèmes critiques et les avertissements.

## <a name="solution-components"></a>Composants de la solution

Un exemple de script solution, [Déployer ReferenceArchitecture.ps1][solution-script], n’est disponible que vous pouvez utiliser pour implémenter l’architecture qui suit les recommandations décrites dans cet article. Ce script utilise le Gestionnaire de ressources Azure modèles. Les modèles sont disponibles comme un ensemble de blocs de construction fondamentaux, chacun d'entre eux effectue une action spécifique tel que la création d’un VNet ou configurez un NSG. L’objectif du script est d’organiser le déploiement du modèle.

Les modèles sont paramétrées, avec les paramètres contenus dans les fichiers JSON distincts. Vous pouvez modifier les paramètres dans ces fichiers pour configurer le déploiement pour répondre à vos besoins. Vous n’avez pas besoin de modifier les modèles eux-mêmes. Notez que vous ne devez pas modifier les schémas des objets dans les fichiers de paramètres.

Lorsque vous modifiez les modèles, créer des objets qui suivent les conventions d’attribution de noms décrites dans [Recommandé les Conventions d’affectation des noms des ressources Azure][naming-conventions].

L’exemple de solution crée et configure l’environnement dans le cloud comprenant ajoute sous-réseau et serveurs, le sous-réseau ADFS et serveurs, sous-réseau de proxy AD FS et, DMZ, couche web, couche d’entreprise et composants de niveau d’accès aux données, passerelle VPN et niveau de gestion. L’exemple de solution comprend également une configuration facultatif pour la création d’un environnement simulé en local.

Les sections suivantes décrivent les éléments de l’en local et en nuage configurations.

### <a name="on-premises-components"></a>Composants en local

>[AZURE.NOTE] Ces composants ne sont pas l’objectif principal de l’architecture décrite dans ce document et sont fournies simplement pour vous permettre de tester l’environnement cloud en toute sécurité, plutôt que d’utiliser un environnement de production réel. Pour cette raison, cette section résume uniquement les fichiers de paramètre de clé. Vous pouvez modifier les paramètres tels que les adresses IP ou les tailles des ordinateurs virtuels, mais il est conseillé de laisser la plupart des autres paramètres inchangés.

Cet environnement comprend une forêt Active Directory pour un domaine nommé contoso.com. Le domaine contient deux serveurs ajoute avec des adresses IP 192.168.0.4 et 192.168.0.5. Ces deux serveurs exécutant également le service DNS. Le compte d’administrateur local sur les deux machines virtuelles est appelé `testuser` par mot de passe `AweS0me@PW`. En outre, la configuration configure une passerelle VPN pour vous connecter à la VNet dans le cloud. Vous pouvez modifier la configuration en modifiant les fichiers JSON suivants situés dans les [**paramètres/locales**] [ on-premises-folder] dossier :

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Ce fichier définit l’espace d’adressage réseau pour l’environnement local.

- ** [desmachines virtuelles adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Ce fichier contient la configuration pour les ordinateurs locaux virtuels ajoute services d’hébergement. Par défaut, les deux machines virtuelles *Standard-DS3-v2* sont créés.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** et ** [connection.parameters.json][on-premises-connection-parameters]**. Ces fichiers contiennent les paramètres pour la connexion VPN à la passerelle VPN Azure dans le cloud, y compris la clé partagée pour être utilisé pour protéger le trafic traversant la passerelle.

Les autres fichiers dans le dossier contiennent les informations de configuration utilisées pour créer le domaine local à l’aide de cette infrastructure. Elles permettent d’installer : ajoute, configuration de DNS, créez une forêt et configurer les sites de réplication de la forêt.

### <a name="cloud-components"></a>Composants de nuage

Ces composants constituent le cœur de cette architecture. Les [**paramètres/azure**] [ azure-folder] dossier contient les fichiers de paramètres suivantes pour configurer ces composants :

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Ce fichier définit la structure de la VNet pour les ordinateurs virtuels et d’autres composants dans le cloud. Il comprend des paramètres, tels que le nom, un espace d’adressage, sous-réseaux et les adresses de tous les serveurs DNS requis. Notez que les adresses DNS indiquées dans cet exemple référencent les adresses IP de serveurs DNS en local, ainsi que le serveur DNS Azure par défaut. Modifier ces adresses pour faire référence à la configuration de votre propre DNS si vous n’utilisez pas l’exemple d’environnement local :

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [desmachines virtuelles adds.parameters.json ] [ virtualmachines-adds-parameters] **. Ce fichier configure les ordinateurs virtuels en cours d’exécution : ajoute dans le cloud. La configuration se compose de deux machines virtuelles. Vous devez modifier le nom d’utilisateur et mot de passe dans la `virtualMachineSettings` section et vous pouvez éventuellement modifier la taille de la mémoire virtuelle pour correspondre à la configuration requise du domaine :

    Pour plus d’informations, voir [Extension d’Active Directory pour Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [Ajouter-ajoute-domaine-controller.parameters.json][add-adds-domain-controller-parameters]**. Ce fichier contient les paramètres de création du domaine CONTOSO couvrant les serveurs ajoute. Il utilise les extensions personnalisées que le domaine et lui ajouter les serveurs ajoute. Sauf si vous créez des serveurs supplémentaires : ajoute (auquel cas vous devez les ajouter à la `vms` tableau), modifier leurs noms à partir de la valeur par défaut ou pour créer un domaine avec un autre nom que vous n’avez pas besoin de modifier ce fichier.

- ** [loadBalancer adfs.parameters.json] [loadbalancer-adfs-parameters] ** Le fichier contient deux ensembles de paramètres de configuration. La `virtualMachineSettings` section définit les ordinateurs virtuels hébergeant le service ADFS dans le cloud. Par défaut, le script crée deux de ces ordinateurs virtuels dans le même jeu de disponibilité :

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    La `loadBalancerSettings` section fournit une description de l’équilibrage de charge pour ces machines virtuelles. L’équilibrage de charge transmet le trafic qui s’affiche sur le port 443 (HTTPS) à un ou l’autre des ordinateurs virtuels :

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-batterie-domaine-join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Ce fichier contient les paramètres utilisés pour ajouter les serveurs ADFS au domaine CONTOSO. Vous ne devez modifier ce fichier si vous avez créé des serveurs ADFS supplémentaires (mettre à jour la `vms` dans ce cas de tableau), ou vous avez modifié le nom de domaine.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [adfs-batterie-first.parameters.json][adfs-farm-first-parameters]**, et ** [adfs-batterie-rest.parameters.json][adfs-farm-rest-parameters]**. Le script utilise les paramètres dans ces fichiers pour créer la batterie de serveurs ADFS. 

    Le fichier *gmsa.parameters.json* contient les paramètres du compte de service groupe gérée utilisées par le service ADFS. Vous pouvez modifier ce fichier si vous souhaitez modifier le nom du compte ou le domaine.

    Le fichier *adfs-batterie-first.parameters.json* conserve les informations requises pour créer la batterie de serveurs ADFS et ajouter le premier serveur. Si vous avez modifié le domaine ou le nom du compte de service groupe géré, vous devez mettre à jour ce fichier.

    Le fichier *adfs-batterie-rest.parameters.json* est utilisé pour ajouter les serveurs ADFS restants à la batterie de serveurs. Là encore, si vous avez modifié le domaine ou le nom du compte de service groupe géré, vous devez mettre à jour ce fichier. Mise à jour la `vms` tableau si vous avez créé des serveurs ADFS supplémentaires.

- ** [loadBalancer adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Ce fichier est similaire dans la structure et le contenu vers le fichier *loadBalancer adfs.parameters.json* . Il contient les données pour créer les serveurs proxy AD FS et équilibrage de charge.

- ** [adfsproxy-batterie-first.parameters.json][adfsproxy-farm-first-parameters]**, et ** [adfsproxy-batterie-rest.parameters.json][adfsproxy-farm-rest-parameters]**. Le script utilise les paramètres dans ces fichiers pour créer la batterie de serveurs proxy AD FS. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Ce fichier contient les paramètres utilisés pour créer la passerelle VPN Azure dans le cloud utilisé pour se connecter au réseau local. Vous devez modifier le `sharedKey` valeurs contenues dans les `connectionsSettings` section corresponde à celle de l’appareil VPN en local. Pour plus d’informations, voir [mise en œuvre une Architecture réseau hybride avec Azure et en local VPN][hybrid-azure-on-prem-vpn].

- ** [dmz private.parameters.json] [ dmz-private-parameters] ** et ** [dmz public.parameters.json ] [ dmz-public-parameters] **. Ces fichiers configurent entrant (public) et sur les côtés (privées) sortantes des ordinateurs virtuels qui composent la DMZ, protégeant les serveurs dans le cloud. Pour plus d’informations sur ces éléments et leur configuration, consultez [implémentation d’une DMZ entre Azure et Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters-json][loadBalancer-web-parameters]**, ** [loadBalancer-biz.parameters-json][loadBalancer-biz-parameters]**, et ** [loadBalancer-data.parameters-json][loadBalancer-data-parameters]**. Ces fichiers paramètres contiennent les spécifications machine virtuelle pour les niveaux d’accès web, entreprise et de données et configurer les programmes d’équilibrage de charge pour chaque niveau. Il s’agit des ordinateurs virtuels qui hébergent les applications web et les bases de données et effectuer les charges de travail d’entreprise de l’organisation. Vous pouvez modifier la taille et le nombre de machines virtuelles dans chaque niveau selon vos besoins.

- ** [desmachines virtuelles mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Ce fichier contient la configuration de la zone saut/gestion des machines virtuelles. Il n’est possible d’obtenir d’ouverture et de l’accès administratif aux ordinateurs virtuels dans le web, entreprise et les niveaux de données à partir de la zone passer. Par défaut, le script crée un seul *Standard_DS1_v2* machine virtuelle, mais vous pouvez modifier ce fichier pour créer des machines virtuelles agrandir ou supplémentaires si la charge de travail Gestion des est susceptible d’être important.

## <a name="solution-deployment"></a>Déploiement de solution

La solution suppose les conditions préalables suivantes :

- Vous avez un abonnement Azure existant dans lequel vous pouvez créer des groupes de ressources.

- Vous avez téléchargé et installé la version plus récente de Powershell Azure. Voir [ici] [ azure-powershell-download] pour obtenir des instructions.

Pour exécuter le script qui déploie la solution :

1. Déplacer vers un dossier sur votre ordinateur local et créez les sous-dossiers suivants :

    - Scripts

    - Les scripts/paramètres

    - Paramètres/scripts/locales

    - Paramètres/scripts/Azure

2. Télécharger le [Déploiement ReferenceArchitecture.ps1] [ solution-script] fichier dans le dossier Scripts

3. Télécharger le contenu de la [paramètres/locales] [ on-premises-folder] dossier vers le dossier de Scripts/paramètres/locales :

4. Télécharger le contenu de [paramètres/azure] [ azure-folder] dossier vers le dossier de Scripts/paramètres/Azure.

5. Modifier le fichier de déploiement ReferenceArchitecture.ps1 dans le dossier Scripts et modifiez les lignes suivantes pour spécifier les groupes de ressources qui doivent être créés ou utilisées pour stocker les ressources créées par le script :

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Modifier les fichiers de paramètre dans les Scripts/paramètres/locales et les dossiers de Scripts/paramètres/Azure. Mettre à jour les références de groupe de ressources dans ces fichiers pour faire correspondre les noms des groupes de ressources affectées aux variables dans le fichier de déploiement ReferenceArchitecture.ps1. Le tableau suivant répertorie les fichiers de paramètre font référence à quel groupe de ressources. Notez que les groupes *ra-adfs-la charge de travail-routage*, *ra-adfs-sécurité-routage*, *ra-adfs-ajoute-routage*, *ra-adfs-adfs-routage*et *ra-adfs-proxy-routage* sont uniquement utilisées dans le script PowerShell et ne figurent pas dans les fichiers de paramètres.

  	|Groupe de ressources|Fichiers de paramètre|
  	|--------------|--------------|
  	|RA-adfs-locales-routage|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adfs-réseau-routage|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-with-onpremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*deux occurrences*)

    En outre, définir la configuration de l’en local et en nuage composants, comme décrit dans la section [composants de la Solution] [composants de la solution].

7. Ouvrez une fenêtre PowerShell Azure, déplacer vers le dossier Scripts et exécutez la commande suivante :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Remplacer `<subscription id>` avec votre ID d’abonnement Azure.

    Pour `<location>`, spécifiez une région Azure, tel que `eastus` ou `westus`.

    La `<mode>` paramètre peut avoir une des valeurs suivantes :

    - `Onpremise`, pour créer l’environnement simulé en local.

    - `Infrastructure`, pour créer l’infrastructure VNet et accéder zone dans le cloud.

    - `CreateVpn`, pour générer passerelle Azure réseau virtuel et vous connecter au réseau local.

    - `AzureADDS`, pour créer les ordinateurs virtuels servant de serveurs ajoute, déployer Active Directory sur ces ordinateurs virtuels et créer le domaine dans le cloud.

    - `AdfsVm`Pour créer les ordinateurs virtuels ADFS et les intégrer au domaine dans le cloud.

    - `ProxyVm`Pour créer le proxy AD FS machines virtuelles et les intégrer au domaine dans le cloud.

    - `Prepare`, qui effectue toutes les tâches ci-dessus. **Il s’agit de l’option recommandée si vous créez un déploiement entièrement nouvel et vous n’avez pas une infrastructure locale existante.**

    >[AZURE.NOTE] Vous pouvez également exécuter le script avec un `<mode>` paramètre de `Workload` pour créer le web, business et couche données machines virtuelles et réseau. Ce programme d’installation n’est pas inclus dans le cadre de la `Prepare` mode.

    Si vous utilisez la `Prepare` option, le script prend plusieurs heures, et se termine par le message *préparation est terminée. Installez le certificat à tous les AD FS et proxy machines virtuelles.*

8.  Redémarrez la zone passer (*ra-adfs-gestion-vm1* dans le groupe *ra-adfs-sécurité-routage* ) pour permettre à ses paramètres DNS prenne effet.

9.  [Obtenir un certificat SSL pour ADFS] [ adfs_certificates] et installer ce certificat sur les ordinateurs virtuels ADFS. Notez que vous pouvez vous connecter aux ordinateurs virtuels ADFS via la zone passer. Les adresses IP sont *10.0.5.4* et *10.0.5.5*. Le nom d’utilisateur par défaut est *contoso\testuser* par mot de passe *AweSome@PW*.

    >[AZURE.NOTE] Les commentaires dans le script de déploiement ReferenceArchitecture.ps1 à ce stade fournissent des instructions détaillées pour la création d’un certificat auto-signé test et à l’aide de l’autorité la `makecert` commande. Toutefois, n’utilisez pas les certificats générés par makecert dans un environnement de production.

10. Exécutez la commande Powershell suivante pour configurer la batterie de serveurs ADFS :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. Dans la zone lien, accédez à *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* pour tester l’installation ADFS (vous pouvez recevoir un avertissement de certificat, vous pouvez ignorer pour ce test). Vérifiez que la page de connexion à Contoso Corporation s’affiche. Connectez-vous en tant qu' *contoso\testuser* par mot de passe *AweS0me@PW*.

12. Installer le certificat SSL sur le serveur proxy AD FS machines virtuelles. Les adresses IP sont *10.0.6.4* et *10.0.6.5*.

13. Exécutez la commande Powershell suivante pour configurer le premier serveur de proxy AD FS :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Suivez les instructions affichées par le script pour tester l’installation du premier serveur proxy AD FS.

15. Exécutez la commande Powershell suivante pour configurer le premier serveur de proxy AD FS deuxième :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Suivez les instructions affichées par le script pour tester la configuration du proxy AD FS complet.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Architecture de réseau hybride avec Active Directory sécurisée"
