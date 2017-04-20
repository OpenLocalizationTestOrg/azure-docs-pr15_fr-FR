<properties
   pageTitle="Implémentation d’une architecture de réseau sécurisée hybride dans Azure | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau sécurisée hybride dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
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
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>Mise en œuvre une DMZ entre Azure et votre centre de données locale

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour implémenter un réseau hybride sécurisée qui s’étend un réseau local à Azure. Cette architecture de référence met en œuvre une DMZ entre un réseau local et un réseau virtuel Azure itinéraires définis par l’utilisateur (UDRs). La zone DMZ inclut réseau hautement disponible authentification multifacteur (NVAs) qui implémenter des fonctionnalités de sécurité tels que les pare-feu et inspection des paquets. Tout le trafic sortant de la VNet est mis en tunnel forcer à Internet via le réseau local afin qu’il etc. 

Cette architecture requiert une connexion à votre centre de données locale implémentée à l’aide d’une [passerelle VPN][ra-vpn], ou un [ExpressRoute] [ ra-expressroute] connexion.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Applications hybrides où les charges de travail exécuter partiellement en local et Azure partiellement en.

- Azure infrastructure qui achemine le trafic entrant depuis en local et sur internet.

- Applications requises à un audit le trafic sortant. Ceci est souvent une obligation réglementaire de nombreux systèmes professionnelle et peut vous aider à empêcher la divulgation d’informations privées.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture :

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est dans la page « DMZ – privé ».

[! [0]][0]

- **Réseau local.** Il s’agit d’un réseau d’ordinateurs et appareils connectés via un privé réseau local activée dans une organisation.

- **Réseau virtuel Azure (VNet).** La VNet héberge l’application et autres ressources en cours d’exécution dans le cloud.

- **Passerelle.** La passerelle assure la connectivité entre routeurs dans le réseau local et le VNet.

- **Équipement réseau virtuel (à couloirs).** À couloirs est un terme générique décrivant une machine virtuelle effectuant des tâches comme autoriser ou refuser l’accès comme un pare-feu, optimisation opérations WAN (y compris la compression réseau), routage personnalisé ou d’autres fonctionnalités réseau. 

- **Couche Web, couche d’entreprise et sous-réseaux de niveau de données.** Il s’agit des sous-réseaux hébergeant les machines virtuelles et les services de mise en œuvre d’un exemple d’application niveau 3 en cours d’exécution dans le cloud. Voir [En cours d’exécution des machines virtuelles Windows pour une architecture multicouches sur Azure] [ ra-n-tier] pour plus d’informations.

- **Itinéraires définis par l’utilisateur (UDR).** [Itinéraires définis par l’utilisateur] [ udr-overview] définir le flux du trafic IP dans Azure VNets. 

> [AZURE.NOTE] Selon les besoins de votre connexion VPN, vous pouvez configurer des itinéraires bordure BGP (Gateway Protocol) comme alternative à l’utilisation de UDRs pour mettre en œuvre les règles de transfert de diriger le trafic reculer dans le réseau local.

- **Sous-réseau gestion.** Ce sous-réseau contient machines virtuelles que la mise en œuvre la gestion et les fonctionnalités de surveillance pour les composants exécutés dans le VNet. 

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="rbac-recommendations"></a>Recommandations RBAC

Créer plusieurs rôles RBAC pour gérer les ressources dans votre application. Envisager de créer un DevOps [rôle personnalisé] [ rbac-custom-roles] avec des autorisations pour gérer l’infrastructure de l’application. Envisager de créer un d’administrateur informatique centralisée [rôle personnalisé] [ rbac-custom-roles] pour gérer les ressources réseau et une administrateur informatique [rôle personnalisé] de sécurité distinct[ rbac-custom-roles] pour gérer les ressources de réseau sécurisé tels que les NVAs. 

Le rôle DevOps doit inclure des autorisations pour déployer les composants d’application ainsi qu’à surveiller et redémarrez machines virtuelles. Le rôle d’administrateur informatique centralisé doit inclure des autorisations pour surveiller les performances du réseau. Aucune de ces rôles doivent avoir accès aux ressources à couloirs comme cela doit être limité à la sécurité rôle d’administrateur informatique.

### <a name="resource-group-recommendations"></a>Recommandations de groupe de ressources

Ressources Azure tels que machines virtuelles VNets et programmes d’équilibrage de charge pouvant être gérées facilement en les groupant en groupes de ressources. Vous pouvez ensuite affecter les rôles ci-dessus pour chaque groupe de ressources pour limiter l’accès.

Nous vous recommandons de la création d’une des opérations suivantes :

- Un groupe de ressources contenant les sous-réseaux (sauf les ordinateurs virtuels), NSGs et les ressources de passerelle pour vous connecter au réseau local. Attribuer le rôle d’administrateur informatique centralisé pour ce groupe de ressources.

- Un groupe de ressources contenant les ordinateurs virtuels pour les NVAs (y compris l’équilibrage de charge), la zone passer et autres machines virtuelles gestion et la UDR pour le sous-réseau passerelle qui force la création tout le trafic via les NVAs. Attribuer la rôle d’administrateur informatique de sécurité à ce groupe de ressources.

- Groupes de ressources distincts pour chaque niveau de l’application qui contiennent les équilibrage de charge et machines virtuelles. Notez que ce groupe de ressources ne doivent pas inclure les sous-réseaux pour chaque niveau. Attribuer le rôle DevOps à ce groupe de ressources.

### <a name="virtual-network-gateway-recommendations"></a>Recommandations de passerelle réseau virtuel

Le trafic local passe à la VNet via une passerelle réseau virtuel. Nous vous recommandons d’une [passerelle VPN Azure] [ guidance-vpn-gateway] ou une [passerelle Azure ExpressRoute][guidance-expressroute]. 

### <a name="nva-recommendations"></a>Recommandations à couloirs

NVAs offrent différents services pour la gestion et analyse du trafic réseau. La Azure Marketplace propose plusieurs NVAs un fournisseur tiers, notamment :

- [Barracuda Web Application pare-feu] [ barracuda-waf] et [Barracuda NextGen pare-feu][barracuda-nf]

- [Réseaux cohérente VNS3 pare-feu/routeur/VPN][vns3]

- [Machine virtuelle Fortinet FortiGate][fortinet]

- [Pare-feu pour applications Web SecureSphere][securesphere]

- [Pare-feu pour applications Web DenyAll][denyall]

- [Point de vérification vSEC][checkpoint]

Si aucune de ces NVAs tiers ne répond à vos besoins, vous pouvez créer un à couloirs personnalisé à l’aide de machines virtuelles. Pour obtenir un exemple de la création de NVAs personnalisés, voir la DMZ dans cette architecture de référence qui mettent en œuvre, les fonctionnalités suivantes :

- Le trafic est acheminé à l’aide de [transfert IP] [ ip-forwarding] sur les cartes réseau à couloirs.

- Le trafic est autorisé à passer à la couloirs uniquement si cela est approprié pour le faire. Chaque VM NVA dans l’architecture de référence est un routeur Linux simple avec le trafic entrant arrivant sur le réseau interface *eth0*et le trafic sortant règles correspondantes définies par des scripts personnalisés distribués via réseau interface *eth1*. 

- Le trafic acheminé vers le sous-réseau gestion ne passe pas par les NVAs et les NVAs ne peuvent être configurés à partir du sous-réseau gestion. Si le trafic vers le sous-réseau gestion est nécessaire pour être routés via les NVAs, il n’existe aucun itinéraire au sous-réseau gestion pour corriger le NVAs si elles doivent échouer.  

- Les ordinateurs virtuels pour l’à couloirs sont inclus dans une disponibilité définir derrière un équilibrage de charge. La UDR du sous-réseau passerelle dirige les requêtes à couloirs vers l’équilibrage de charge.

Un autre recommandation à prendre en compte se connecte plusieurs NVAs de série avec chaque à couloirs exécuter une tâche de sécurité spécialisés. Cela permet à chaque fonction sécurité être gérés sur une base par à couloirs. Par exemple, un à couloirs mise en œuvre un pare-feu peut être placé dans une série avec un à couloirs exécutant les services d’identité. Le compromis pour faciliter la gestion des est l’ajout de sauts de réseau supplémentaires qui peuvent augmenter la latence, assurez-vous que cela n’affecte pas les performances de votre application.

### <a name="nsg-recommendations"></a>Recommandations NSG

La passerelle VPN expose une adresse IP publique pour la connexion au réseau local. Nous vous recommandons de création d’un groupe de sécurité réseau (NSG) pour les entrants à couloirs sous-réseau modalités d’application bloquer le trafic ne proviennent ne pas le réseau local.

Nous vous conseillons également de mise en œuvre de NSGs de chaque sous réseau pour offrir un deuxième niveau de protection contre le trafic entrant ignorer un à couloirs incorrectement configuré ou désactivé. Par exemple, le sous-réseau couche web dans l’architecture de référence met en œuvre un NSG avec une règle pour ignorer toutes les demandes autre que celles provenant du réseau local (192.168.0.0/16) ou la VNet et une autre règle qui ignore toutes les demandes ne créées pas sur le port 80. 

### <a name="internet-access-recommendations"></a>Recommandations d’accès Internet

[Forcer tunnel] [ azure-forced-tunneling] tout le trafic internet sortant via votre réseau local à l’aide du tunnel VPN site à et itinéraire à l’internet à l’aide du réseau adresse tranlation (NAT). Cela à la fois empêcher les fuites accidentelles de toute information confidentielle stockée dans votre couche de données et permettent également d’inspection et audit de tout le trafic sortant.

> [AZURE.NOTE] Ne bloquer complètement le trafic Internet à partir du web, entreprise et des couches application. Si ces niveaux utilisent les services PaaS Azure qu’ils s’appuient sur les adresses IP publiques pour l’enregistrement des diagnostics machine virtuelle, le téléchargement d’extensions de machine virtuelle et d’autres fonctionnalités. Diagnostics de Windows Azure requiert également que les composants puissent lire et écrire à un compte de stockage Azure dépendant internet.

Nous vous conseillons également de vérifier le trafic internet sortant est mis en tunnel forcer correctement. Si vous utilisez une connexion VPN avec le [service d’accès distant et de routage] [ routing-and-remote-access-service] sur un serveur local, utilisez un outil comme [WireShark] [ wireshark] ou de [l’Analyseur de Message de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Recommandations sur la gestion sous-réseau

Le sous-réseau gestion contient une zone de renvoi qui exécute gestion et fonctionnalité de surveillance. Mettre en œuvre les recommandations suivantes pour la zone passer :
- Ne créez pas une adresse IP publique de la zone de renvoi. 
- Créer un itinéraire pour accéder à la zone passer via la passerelle entrante et implémenter un NSG dans le sous-réseau gestion uniquement répondre aux demandes de l’itinéraire autorisé.
- Restreindre l’exécution de toutes les tâches de gestion sécurisée vers la zone de renvoi.

### <a name="nva-recommendations"></a>Recommandations à couloirs

Inclure un calque 7 à couloirs pour arrêter les connexions d’application au niveau à couloirs et conserve les affinités avec les couches principales. Cela garantit la connectivité symétrique dans lequel le trafic de réponse dans les couches principales retourne par le biais à couloirs.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

L’architecture de référence met en œuvre un équilibrage de charge diriger le trafic réseau local vers un pool de périphériques à couloirs. Comme indiqué précédemment, les appareils à couloirs sont machines virtuelles exécuter les règles de routage le trafic réseau et déployées dans une [disponibilité définie][availability-set]. Ce modèle vous permet de surveiller le débit des NVAs au fil du temps et ajouter des périphériques à couloirs en réponse aux augmentations de charge.

La passerelle SKU VPN standard prend en charge un débit jusqu'à 100 Mbps. La référence (SKU) haute Performance fournit jusqu'à 200 Mbps. Pour la bande passante supérieure, vous pouvez la mise à niveau vers une passerelle ExpressRoute. ExpressRoute fournit jusqu'à 10 Go/s bande passante avec une connexion VPN latence inférieure.

> [AZURE.NOTE] Les articles [d’implémentation d’une Architecture réseau hybride avec Azure et en local VPN] [ guidance-vpn-gateway] et [implémentation d’une architecture réseau hybride avec Azure ExpressRoute] [ guidance-expressroute] décrivent les problèmes qui entoure l’extensibilité élevées des passerelles Azure.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

L’architecture de référence met en œuvre un équilibrage de charge distribuer des demandes locales à un pool de périphériques à couloirs dans Azure. Les appareils à couloirs sont machines virtuelles exécuter les règles de routage le trafic réseau et déployées dans une [disponibilité définie][availability-set]. Régulièrement, l’équilibrage de charge une sonde santé implémentée sur chaque à couloirs des requêtes et supprime toute NVAs ne répond pas à partir du pool. 

Si vous utilisez Azure ExpressRoute pour fournir une connectivité entre le réseau VNet et en local, [configurez une passerelle VPN pour assurer le basculement] [ guidance-vpn-failover] si la connexion ExpressRoute n’est plus disponible.

Pour plus d’informations sur la gestion de disponibilité pour les connexions VPN et ExpressRoute, consultez les articles [implémentation d’une Architecture réseau hybride avec Azure et en local VPN] [ guidance-vpn-gateway] et [implémentation d’une architecture réseau hybride avec Azure ExpressRoute][guidance-expressroute].

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Toutes les applications et l’analyse de ressource doivent être effectuées par la zone passer du sous-réseau gestion. En fonction des besoins de votre application, vous devrez peut-être ajouter des ressources de surveillance supplémentaires dans le sous-réseau gestion, mais à nouveau un de ces ressources supplémentaires doivent être accessibles via la zone passer.

Si la connectivité de passerelle à partir de votre réseau local vers Azure est vers le bas, vous pouvez toujours accéder à la boîte de déviation en déployant un PIP, en ajoutant à la zone passer et l’accès distant dans à partir d’internet.

Sous-réseau de chaque niveau de l’architecture de référence est protégé par la réglementation NSG, et il peut être nécessaire créer une règle pour ouvrir le port 3389 pour l’accès RDP sur machines virtuelles Windows ou 22 pour l’accès SSH sur machines virtuelles Linux. Autres gestion et les outils d’analyse peuvent nécessiter des règles pour ouvrir des ports supplémentaires.

Si vous utilisez ExpressRoute pour fournir la connectivité entre votre centre de données en local et Azure, utiliser le [Kit de ressources de connectivité Azure (AzureCT)] [ azurect] pour surveiller et résoudre les problèmes de connexion.

> [AZURE.NOTE] Vous pouvez trouver des informations supplémentaires visant spécifiquement surveillance et la gestion des connexions VPN et ExpressRoute dans les articles [d’implémentation d’une Architecture réseau hybride avec Azure et en local VPN] [ guidance-vpn-gateway] et [implémentation d’une architecture réseau hybride avec Azure ExpressRoute][guidance-expressroute].

## <a name="security-considerations"></a>Considérations sur la sécurité

Cette architecture de référence met en œuvre plusieurs niveaux de sécurité : 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Routage de toutes les demandes d’utilisateurs locaux par le biais à couloirs

La UDR du sous-réseau passerelle bloque toutes les demandes d’utilisateur autre que celles provenant des locaux. Les étapes de UDR de requêtes pour les NVAs autorisées dans le sous-réseau DMZ privé, et ces demandes sont passés à l’application s’ils sont autorisés par les règles à couloirs. Autres itinéraires peuvent être ajoutés à la UDR, mais veiller à ce qu’ils ne par inadvertance ignore la NVAs ou bloquer le trafic d’administration destiné le sous-réseau gestion.

L’équilibrage de charge devant les NVAs joue également un dispositif de sécurité en ignorant le trafic sur les ports qui ne sont pas ouverts dans la règles d’équilibrage de charge. Les programmes d’équilibrage de charge dans l’architecture de référence écoutent uniquement les demandes de HTTP sur le port 80 et HTTPS sur le port 443. Les règles supplémentaires pour les programmes d’équilibrage de charge doivent être présentées et le trafic doit être contrôlé pour vous assurer qu’aucun problème de sécurité.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>À l’aide de NSGs pour bloquer/test le trafic entre les niveaux de l’application

Chacune des couches web, business et données de restreindre le trafic entre eux à l’aide de NSGs. En d’autres termes, la couche d’entreprise utilise un NSG pour bloquer le trafic ne provenant de la couche web et la couche données utilise un NSG pour bloquer le trafic ne provenant de la couche d’entreprise. Si vous avez besoin pour développer les règles NSG pour autoriser l’accès plus large à ces niveaux, comparez ces exigences contre les risques de sécurité. Chaque nouvelle allée entrante représente une opportunité données accidentelle ou délibérée fuites ou à l’application n’est pas endommagé.

### <a name="devops-access"></a>DevOps access

Restreindre les opérations DevOps peut effectuer sur chaque niveau de l’utilisation de [RBAC] [ rbac] pour gérer les autorisations. Lorsque vous accordez des autorisations, utilisez le [principe du moindre privilège][security-principle-of-least-privilege]. Ouvrez une session toutes les opérations d’administration et effectuez des audits réguliers pour vous assurer que les modifications de configuration ont été planifiées.

> [AZURE.NOTE] Pour des informations plus complète, des exemples et des scénarios sur la gestion de la sécurité du réseau avec Azure, consultez [services cloud Microsoft et la sécurité du réseau][cloud-services-network-security]. Pour plus d’informations sur la protection de ressources dans le cloud, voir [prise en main de Microsoft Azure sécurité][getting-started-with-azure-security]. Pour plus d’informations sur les questions de sécurité via une connexion de passerelle Azure, consultez [implémentation d’une Architecture réseau hybride avec Azure et en local VPN] [ guidance-vpn-gateway] et [implémentation d’une architecture réseau hybride avec Azure ExpressRoute][guidance-expressroute].

## <a name="solution-deployment"></a>Déploiement de solution

Un déploiement pour une architecture de référence qui mettent en œuvre, ces recommandations est disponible sur Github. Cette architecture de référence inclut un réseau virtuel (VNet), groupe de sécurité réseau (NSG), équilibrage de charge et deux machines virtuelles (machines virtuelles).

L’architecture de référence peut être déployé avec Windows ou Linux machines virtuelles en suivant les instructions ci-dessous : 

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Une fois que le lien a ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-private-dmz-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

4. Les fichiers de paramètres incluent nom codé en dur administrateur d’utilisateur et mot de passe pour tous les ordinateurs virtuels, et il est fortement recommandé que vous modifiez immédiatement les deux. Pour chaque machine virtuelle dans le déploiement, sélectionnez-le dans le portail Azure et puis cliquez sur **Réinitialiser votre mot de passe** dans la carte de **Support + résolution des problèmes** . Sélectionnez **Réinitialiser votre mot de passe** dans la zone de liste déroulante **Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et **mot de passe**. Cliquez sur le bouton de **mise à jour** pour conserver.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment mettre en œuvre une [DMZ entre Azure et Internet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Découvrez comment mettre en œuvre une [architecture réseau hybride hautement disponible](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Architecture de réseau hybride sécurisée"
