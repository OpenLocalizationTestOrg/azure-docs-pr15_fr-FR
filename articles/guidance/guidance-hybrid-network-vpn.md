<properties
   pageTitle="Mise en œuvre une Architecture réseau hybride avec VPN Azure et en local | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau de site à site sécurisé qui s’étend sur un réseau virtuel Azure et un réseau local connecté à l’aide d’un réseau privé virtuel."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Mise en œuvre une Architecture réseau hybride avec Azure et VPN en local

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article présente un ensemble de pratiques pour le développement d’un réseau local sur Azure à l’aide d’un réseau privé virtuel site-à-site (VPN). Le trafic s’écoule entre le réseau local et un réseau virtuel Azure (VNet) via un tunnel VPN IPSec. Cette architecture est adaptée aux applications hybride avec les caractéristiques suivantes :

- Parties de l’application exécutent en local, tandis que d’autres personnes exécutent dans Azure.

- Le trafic entre local matériel et le cloud est susceptible d’être clair ou il est intéressant de Commerce latence légèrement étendue pour la flexibilité et la puissance de traitement du cloud.

- Le réseau étendu constitue un système fermé. Il n’est pas directe à partir d’Internet vers le VNet Azure.

- Les utilisateurs se connecter au réseau local pour utiliser les services hébergés dans Azure. La passerelle entre le réseau local et les services en cours d’exécution dans Azure est transparente pour les utilisateurs.

Voici quelques exemples de scénarios qui correspondent à ce profil :

- Applications métier utilisées dans une organisation, où partie de la fonctionnalité a été migrée vers le cloud.

- Développement/laboratoire les charges de travail.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Azure le Gestionnaire de ressources] [ resource-manager-overview] et classique. Ce plan utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant illustre les composants de cette architecture :

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur le « hybride réseau - VPN » page.

![[0]][0]

- **Réseau local.** Il s’agit d’un réseau d’ordinateurs et appareils, connectés à un réseau local privé en cours d’exécution au sein d’une organisation.

- **Appareil VPN.** Il s’agit d’un appareil ou un service qui fournit la connectivité externe au réseau local. L’appareil VPN peut être un périphérique matériel, ou elle peut être une solution logicielle tels que le routage et le Service d’accès distant (RRAS) de Windows Server 2012.

> [AZURE.NOTE] Pour obtenir la liste des appareils VPN pris en charge et des informations sur la configuration des appareils VPN sélectionnées pour vous connecter à une passerelle VPN Azure, consultez les instructions pour le périphérique approprié dans la [liste des appareils VPN pris en charge par Azure][vpn-appliance].

- **Application en nuage multicouches.** Il s’agit de l’application hébergée dans Azure. Il peut inclure plusieurs niveaux, avec plusieurs sous réseaux connectés via des programmes d’équilibrage de charge Azure. Le trafic dans chaque sous-réseau être facturés règles définies à l’aide de [Groupes de sécurité réseau Azure (NSGs)][azure-network-security-group]. Pour plus d’informations, voir [prise en main de Microsoft Azure sécurité][getting-started-with-azure-security].

> [AZURE.NOTE] Cet article décrit l’application cloud comme une seule entité. Voir [une architecture multicouches sur Azure en cours d’exécution] [ implementing-a-multi-tier-architecture-on-Azure] pour plus d’informations.

- **[Réseau virtuel (VNet)][azure-virtual-network].** L’application cloud et les composants de la passerelle VPN Azure résident dans le même VNet.

- **[Passerelle VPN Azure][azure-vpn-gateway].** Le service de passerelle VPN vous permet de connecter la VNet au réseau local via un appareil VPN. Pour plus d’informations, voir [se connecter un réseau local à un réseau virtuel Microsoft Azure][connect-to-an-Azure-vnet]. La passerelle VPN comprend les éléments suivants :

  - **Passerelle réseau virtuel.** Il s’agit d’une ressource qui propose un appareil VPN virtuel pour le VNet. Il est responsable pour acheminer le trafic du réseau local pour la VNet.

  - **Passerelle réseau local.** Il s’agit d’une abstraction de l’appareil VPN en local. Le trafic réseau à partir de l’application cloud au réseau local est routé via cette passerelle.

  - **Connexion.** La connexion comporte des propriétés qui spécifient le type de connexion (IPSec) et la clé partagée avec l’appareil VPN en local pour chiffrer le trafic.

  - **Sous-réseau passerelle.** La passerelle réseau virtuel est conservée dans sa propre sous-réseau, ce qui est soumise à des exigences différentes, comme décrit dans la section recommandations ci-dessous.

- **Équilibrage de charge interne.** Le trafic réseau à partir de la passerelle VPN est routé vers l’application cloud via un programme d’équilibrage de charge interne. L’équilibrage de charge se trouve dans le sous-réseau frontal de l’application.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="vnet-and-gateway-subnet"></a>Sous-réseau VNet et la passerelle

Créer un VNet Azure permettant de maintenir les composants dans le cloud. L’espace d’adressage de la VNet Azure doit être assez grande pour contenir les adresses utilisées par les machines virtuelles et les sous-réseaux dans le VNet. Assurez-vous que l’espace d’adressage VNet dispose de suffisamment d’espace pour la croissance si machines virtuelles supplémentaires sont susceptibles d’être nécessaires à l’avenir. L’espace d’adressage de la VNet doit se chevauchent pas avec le réseau local. Par exemple, le schéma ci-dessus utilise les 10.20.0.0/16 espace adresse pour la VNet.

Créer un sous-réseau nommé _GatewaySubnet_, avec une plage d’adresses de /27. Ce sous-réseau est requis par la passerelle réseau virtuel, et allocation 32 adresses vers ce sous-réseau aidera à éviter que vous exécutez respectivement limites de taille de passerelle possibles à l’avenir. Évitez de placer ce sous-réseau au milieu de l’espace d’adressage. Une bonne pratique consiste à définir l’espace d’adressage pour le sous-réseau passerelle à l’extrémité supérieure de l’espace d’adressage VNet. L’exemple présenté dans l’illustration utilise 10.20.255.224/27.  Une procédure rapide pour calculer le CIDR est la suivante :

1. Définir les bits variables dans l’espace d’adressage de la VNet à 1, jusqu'à les bits est utilisé par le sous-réseau passerelle, puis définissez les bits restants à 0.

2. Convertissez les bits qui en résulte en nombre décimal et express comme un espace d’adressage à l’ensemble de longueur préfixe à la taille du sous-réseau passerelle.

Par exemple, pour une VNet avec une plage d’adresses IP de 10.20.0.0/16, application étape 1 # ci-dessus devient 10.20.0b11111111.0b11100000.  Qui convertir en nombre décimal et il exprimant comme un espace d’adressage donne 10.20.255.224/27

> [AZURE.WARNING] Ne pas déployer les autres machines virtuelles ou les instances de rôles au sous-réseau passerelle. En outre, n’affectez pas un NSG à ce sous-réseau, car cela provoquera la passerelle vers cesse de fonctionner.

### <a name="virtual-network-gateway"></a>Passerelle réseau virtuel

Allouer une adresse IP publique de la passerelle réseau virtuel.

Créer la passerelle réseau virtuel du sous-réseau passerelle et affectez-le à l’adresse IP publique nouvellement alloué. Utiliser le type de passerelle qui correspond le mieux à vos besoins et qui est activé par votre appareil VPN :

Créer une [passerelle basée] [ policy-based-routing] si vous avez besoin contrôler étroitement l’acheminement des demandes. en fonction de critères de stratégie tels que les préfixes d’adresse. Les passerelles basées sur une stratégie utilisent le routage statique et fonctionnent uniquement avec les connexions de site à.

Créer une [passerelle itinéraire] [ route-based-routing] si vous vous connectez au réseau local à l’aide de RRAS, en charge les connexions à plusieurs sites ou entre région, ou d’implémenter des connexions VNet-VNet (y compris les itinéraires qui traversent plusieurs VNets). Les passerelles basées sur itinéraire utilisent le routage dynamique pour diriger le trafic entre les réseaux. Ils peuvent faire face défaillances dans le chemin d’accès réseau supérieure à celle des itinéraires statiques, car ils peuvent essayer routes alternatives. Passerelles basées sur itinéraire peuvent également réduire les frais de gestion, car itinéraires ne doit pas nécessairement être mis à jour manuellement quand les adresses réseau changent.

Pour une liste des appareils VPN pris en charge, voir [appareils sur VPN pour les connexions Site à Site VPN passerelle][vpn-appliances].

> [AZURE.NOTE] Une fois que la passerelle a été créée, vous ne pouvez pas modifier entre les types de passerelle sans supprimer et recréer la passerelle.

Sélectionnez la référence de passerelle VPN Azure qui correspond le mieux à vos besoins de débit. Azure passerelle VPN est disponible dans trois références SKU indiqué dans le tableau suivant. Chaque référence (SKU) offre un débit différent, [frais sont perçus] [ azure-gateway-charges] en fonction de la durée de la passerelle est mis en service et disponible.

| RÉFÉRENCE (SKU) | Débit VPN | Max IPSec tunnel|
|-----|----------------|------------------|
| Base | 100 Mbps | 10 |
| Standard | 100 Mbps | 10 |
| Haute Performance | 200 Mbps | 30 |

> [AZURE.NOTE] La référence (SKU) base n’est pas compatible avec Azure ExpressRoute. Vous pouvez [Modifier la référence (SKU)] [ changing-SKUs] après la passerelle a été créée.

Créer des règles de routage pour le sous-réseau passerelle que le trafic application entrant directement à partir de la passerelle vers l’équilibrage de charge interne au lieu d’autoriser les demandes passer directement aux machines virtuelles qui implémentent l’application.

### <a name="on-premises-network-connection"></a>Connexion de réseau local

Créer une passerelle réseau local. Spécifier l’adresse IP de l’appareil VPN en local et l’espace d’adressage du réseau local. Notez que l’appareil VPN local doit avoir une adresse IP publique accessible par la passerelle réseau local dans Azure VPN passerelle. Le périphérique VPN ne peut pas être situé derrière un périphérique NAT.

Créer une connexion à un site pour la passerelle réseau virtuel et la passerelle du réseau local. Sélectionnez le type de connexion de Site à site (IPSec), puis spécifiez la clé partagée. Chiffrement de site à site grâce à la passerelle VPN Azure est basé sur le protocole IPSec, à l’aide des touches partagées pour l’authentification. Vous spécifiez la clé lorsque vous créez la passerelle VPN Azure. Vous devez configurer l’appareil VPN en cours d’exécution en local avec la même clé. Autres mécanismes d’authentification ne sont actuellement pas pris en charge.

Vous assurer que les locaux infrastructure de routage est configuré pour envoyer les demandes destinés aux adresses dans la VNet Azure à l’appareil VPN.

Ouvrez les ports requis par l’application cloud dans le réseau local.

Tester la connexion pour vérifier que :

- L’appareil VPN local achemine correctement le trafic vers l’application cloud via la passerelle VPN Azure.

- La VNet achemine correctement le trafic sur le réseau local.

- Interdit le trafic dans les deux sens est bloqué correctement.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Vous pouvez obtenir limitée extensibilité élevées verticale en le déplaçant vers la référence SKU de haute Performance VPN à partir du Basic ou références SKU de passerelle VPN Standard.

Pour VNets qu’attendez un grand volume de trafic VPN, vous pouvez distribuer les charges de travail différents dans distincte VNets inférieure et la configuration d’une passerelle VPN pour chacun d’eux.

Vous pouvez diviser la VNet horizontalement ou verticalement. Répartition horizontalement, déplacez certaines instances machine virtuelle de chaque niveau en sous-réseaux de la nouvelle VNet. Le résultat est que chaque VNet possède la même structure et fonctionnalités. Répartition verticalement, recréer chaque niveau pour diviser la fonctionnalité en différentes zones logiques (par exemple, la gestion des commandes, facturation, gestion des comptes client et ainsi de suite). Chaque zone fonctionnelle peut ensuite être placé dans sa propre VNet.

Réplication d’un contrôleur de domaine Active Directory local dans la VNet et implémentation du système DNS dans VNet, peuvent vous aider à réduire la sécurité et d’administration du trafic des locales dans le cloud.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Si vous avez besoin pour vous assurer que le réseau local reste disponible à la passerelle VPN Azure, mise en œuvre d’un cluster de basculement de la passerelle VPN locale.

Si votre organisation possède plusieurs sites en local, créer des [connexions de plusieurs sites] [ vpn-gateway-multi-site] à une ou plusieurs VNets Azure. Cette approche nécessite le routage dynamique (basée sur un itinéraire), assurez-vous que la passerelle VPN locale prend en charge cette fonctionnalité.

Voir [SLA pour la passerelle VPN] [ sla-for-vpn-gateway] pour obtenir des informations sur le contrat SLA passerelle VPN.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Contrôler les informations de diagnostic à partir d’appareils VPN en local. Ce processus varie selon les fonctionnalités fournies par l’appareil VPN. Par exemple, si vous utilisez le routage et le Service d’accès distant sur Windows Server 2012, vous devez activer la [journalisation RRAS][rras-logging].

Exécuter les [diagnostics de passerelle VPN Azure] [ gateway-diagnostic-logs] pour capturer des informations sur les problèmes de connectivité. Ces journaux peuvent être utilisés pour effectuer le suivi des informations telles que la source et les destinations de connexion demandes, le protocole utilisé et comment la connexion établie (ou pourquoi la tentative a échoué).

Surveiller les journaux des opérations de la passerelle VPN Azure en utilisant les journaux d’audit disponibles dans le portail Azure. Journaux distincts sont disponibles pour la passerelle réseau local, la passerelle Azure réseau et la connexion. Ces informations peuvent servir à suivre les modifications apportées à la passerelle et peuvent être utiles si une passerelle précédemment fonctionnelle cesse de fonctionner pour une raison quelconque.

![[2]][2]

Contrôler la connectivité et effectuer le suivi des événements d’échec de connectivité. Vous pouvez utiliser un package de surveillance tels que [Nagios] [ nagios] pour capturer et signaler ces informations.

## <a name="security-considerations"></a>Considérations sur la sécurité

Générer une clé partagée différente pour chaque passerelle VPN. Utilisez une clé partagée forte pour les attaques en force de réserve.

> [AZURE.NOTE] Pour l’instant, vous ne pouvez pas utiliser l’archivage sécurisé de clé Azure passerelle VPN Azure de touches partagée.

Assurez-vous que l’appareil VPN local utilise une méthode de chiffrement est [compatible avec la passerelle VPN Azure][vpn-appliance-ipsec]. Pour le routage par stratégie, la passerelle VPN Azure prend en charge les algorithmes de chiffrement AES256 AES128 et 3DES. Les passerelles basées sur itinéraire prend en charge AES256 et 3DES.

Si votre appareil VPN local se trouve sur un réseau de périmètre qui utilise un pare-feu entre le réseau de périmètre et Internet, vous devrez peut-être configurer des [règles de pare-feu supplémentaires] [ additional-firewall-rules] pour autoriser la connexion VPN site à site.

Si l’application dans la VNet envoie des données à Internet, envisagez [d’implémenter tunnel forcé] [ forced-tunneling] route tout liées aux Internet le trafic via le réseau local. Cette approche vous permet de procéder à un audit des demandes sortantes apportées par l’application à partir de l’infrastructure en local.

> [AZURE.NOTE] Tunnel forcé peut avoir un impact sur Business connectivity services Azure (par exemple, le Service stockage) et le Gestionnaire de licences de Windows.

## <a name="troubleshooting-considerations"></a>Considérations relatives à la résolution des problèmes

> [AZURE.NOTE] Visitez le routage et le Blog de l’accès à distance pour des informations générales sur la [résolution des erreurs courantes liées VPN][troubleshooting-vpn-errors].

Si le trafic ne parvient pas à parcourir la connexion VPN, vérifiez les points suivants :

### <a name="on-premises-vpn-appliance"></a>Appareil VPN local :

**L’appareil VPN local fonctionne correctement ?**

Les fichiers journaux générés par l’appareil VPN échecs et des erreurs de la vérification. L’emplacement de ces informations varient en fonction de votre matériel. Par exemple, si vous utilisez RRAS sur Windows Server 2012, vous pouvez utiliser la commande PowerShell suivante pour afficher des informations d’événement d’erreur pour le service RRAS :

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

La propriété _Message_ de chaque entrée fournit une description de l’erreur. Certains exemples courants sont :

- Impossibilité de se connecter, probablement en raison d’une adresse IP incorrecte spécifiée pour la passerelle VPN Azure dans la configuration de l’interface réseau RRAS VPN :

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- La clé partagée incorrecte spécifiée dans la configuration de l’interface réseau RRAS VPN :

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

Vous pouvez également obtenir des informations de journal des événements sur essaie de se connecter à travers le service RRAS à l’aide de la commande PowerShell suivante :

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

En cas d’échec de connexion, ce journal contient des erreurs qui se présenter comme suit :

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**Est le VPN application routage correctement le trafic via la passerelle VPN Azure ?**

Utiliser un outil comme [PsPing] [ psping] pour vérifier la connectivité et du routage au sein de la passerelle VPN. Par exemple, pour tester la connectivité à partir d’un ordinateur local vers un serveur web situé sur le VNet, exécutez la commande suivante (remplacez `<<web-server-address>>` avec l’adresse du serveur web) :

```
PsPing -t <<web-server-address>>:80
```

Si l’ordinateur local peut acheminer le trafic vers le serveur web, vous devriez voir sortie similaire à ce qui suit :

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Si l’ordinateur local ne peut pas communiquer avec la destination prévue, vous verrez messages comme suit :

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**Le pare-feu local autorise-t-elle passer le trafic VPN ? Les ports corrects ont été ouverts ?**

Vérifiez que l’appareil VPN local utilise une méthode de chiffrement est [compatible avec la passerelle VPN Azure][vpn-appliance].

Pour le routage par stratégie, la passerelle VPN Azure prend en charge les algorithmes de chiffrement AES256 AES128 et 3DES. Les passerelles basées sur itinéraire prend en charge AES256 et 3DES.

### <a name="azure-vnet-gateway"></a>Azure VNet passerelle :

Examiner [les journaux de diagnostic Azure VPN passerelle] [ gateway-diagnostic-logs] les problèmes éventuels.

**Sont les Azure VPN passerelle et appareil VPN locaux configurés avec la même clé d’authentification partagée ?**

Vous pouvez afficher la clé partagée stockée par la passerelle VPN Azure à l’aide de la commande Azure infrastructure du langage commun suivante :

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Utilisez la commande appropriée pour votre appareil VPN en local pour afficher la clé partagée configurée pour cette application.

Vérifiez que le sous-réseau _GatewaySubnet_ en maintenant que la passerelle VPN Azure n’est pas associée à un NSG.

Vous pouvez afficher les détails du sous-réseau à l’aide de la commande Azure infrastructure du langage commun suivante :

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Assurez-vous qu’aucun champ de données ne nommé _id du groupe de sécurité réseau_. L’exemple suivant montre les résultats par exemple de la _GatewaySubnet_ qui comporte une NSG affectée (_Groupe de passerelles VPN_). Cela peut entraîner empêcher la passerelle de fonctionner correctement si toutes les règles définies pour ce NSG sont :

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**Les machines virtuelles dans le VNet Azure sont configurés pour autoriser le trafic provenant d’en dehors de la VNet ? Consultez les règles NSG associés aux sous-réseaux contenant ces machines virtuelles.**

Vous pouvez afficher toutes les règles NSG à l’aide de la commande Azure infrastructure du langage commun suivante :

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**La passerelle VPN Azure a été déconnectée ?**

Vous pouvez utiliser la commande Azure PowerShell suivante pour vérifier l’état actuel de la connexion VPN Azure. La `<<connection-name>>` paramètre est le nom de la connexion VPN Azure qui relie la passerelle réseau virtuel et la passerelle locale.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

Les extraits suivants mettez en surbrillance la sortie générée si la passerelle est connectée (le premier exemple) et que vous êtes déconnectée (le second exemple) :

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Configuration de machines virtuelles hôte, utilisation de la bande passante réseau et performances de l’application :

Vérifiez que le pare-feu dans le système d’exploitation invité s’exécutant sur les ordinateurs virtuels Azure dans le sous-réseau sont correctement configurés pour autoriser le trafic autorisé dans les plages d’adresses IP locale.

**Est le volume de trafic atteint la limite de la bande passante disponible à la passerelle VPN Azure ?**

Outils varie selon les installations disponibles sur votre appareil VPN en cours d’exécution en local. Par exemple, si vous utilisez RRAS sur Windows Server 2012, vous pouvez utiliser l’Analyseur de performances à suivre le volume de données soient reçus et transmises sur la connexion VPN ; utilisation de l’objet _RAS Total_ , sélectionnez les compteurs _Octets reçus/Sec_ et _Octets transmis/s_ :

![[3]][3]

Vous devez comparer les résultats avec la bande passante disponible à la passerelle VPN (100 des références SKU Standard Basic et 200 Mbps la référence (SKU) haute Performance) :

![[4]][4]

**Sont toutes les machines virtuelles dans le VNet Azure lent ? Sont qu'ils surchargées, sont suffisamment d’eux pour gérer la charge, sont tous les charge les programmes d’équilibrage correctement configurés ?**

Cela nécessite [la capture et l’analyse des informations de diagnostic][azure-vm-diagnostics]. Vous pouvez examiner les résultats à l’aide du portail Azure, mais de nombreux outils tiers sont également disponibles qui peuvent fournir une vue détaillée dans les données de performance.

**Est l’application efficace utilisation des ressources de cloud ?**

Code de l’application instrument en cours d’exécution sur chaque ordinateur virtuel pour déterminer si les applications sont tirer le meilleur parti des ressources. Vous pouvez utiliser les outils tels que des [Perspectives Application] [ application-insights] pour aider à effectuer les tâches suivantes.

## <a name="solution-deployment"></a>Déploiement de solution

Si vous avez une infrastructure locale existante déjà configurée avec un appareil VPN, vous pouvez déployer l’architecture de référence en procédant comme suit :

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-hybrid-vpn-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

## <a name="next-steps"></a>Étapes suivantes

- [Installation d’un contrôleur de domaine supplémentaire pour un domaine Active Directory local à l’aide de machines virtuelles dans un VNet Azure][installing-ad].

- [Instructions pour le déploiement d’Active Directory Windows Server sur machines virtuelles Azure][deploying-ad].

- [Création d’un serveur DNS dans un VNet][creating-dns].

- [Configuration et la gestion du système DNS dans un VNet][configuring-dns].

- [À l’aide des équipements de sécurité du réseau local Stormshield via un réseau privé virtuel][stormshield].

- [Mise en œuvre une Architecture réseau hybride avec Azure ExpressRoute][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Structure d’un déploiement réseau couvrant les locaux et infrastructure en nuage"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Partition un VNet pour améliorer extensibilité élevées"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Journaux d’audit dans le portail Azure"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Compteurs de performance pour l’analyse du trafic réseau VPN"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Exemple de graphique de performances VPN réseau"