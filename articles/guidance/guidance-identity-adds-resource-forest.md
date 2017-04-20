<properties
   pageTitle="Référence Architecture Azure - IaaS : Création d’une forêt ressource Active Directory dans Azure | Microsoft Azure"
   description="Comment créer un domaine Active Directory approuvé dans Azure."
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
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Création d’une forêt de ressources Services : (Active Directory Directory ajoute) dans Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit la création d’un domaine Active Directory dans Azure est approuvé par, mais distincte à partir de domaines dans votre forêt en local.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Une organisation qui s’exécute Active Directory (AD) locaux peut avoir une forêt comprenant de nombreux domaines différents. Par exemple, vous pouvez créer des domaines individuels pour divers départements ou suborganizations ou nouveaux domaines ont été ajoutés à la suite d’acquisition ou d’une fusion d’autres organisations. Vous pouvez utiliser des domaines pour fournir la séparation entre les différentes zones fonctionnelles doivent être séparés, éventuellement pour des raisons de sécurité, mais vous pouvez partager des informations entre des domaines en établissant des relations de gestion de la confidentialité.

Une organisation qui utilise des domaines distincts peut bénéficier de Azure en déplaçant vers un ou plusieurs de ces domaines dans le cloud. Par ailleurs, une organisation peut souhaiter conserver toutes les ressources de cloud logiquement distinctes des ces maintenu en local et stocker des informations sur les ressources du cloud dans leur propre répertoire, dans un domaine également contenus dans le cloud.

Vous pouvez implémenter Active Directory dans Azure de différentes manières, comme décrit dans les articles [Extension d’Active Directory pour Azure] [ extending-ad-to-azure] et [Implémentation d’Azure Active Directory][implementing-aad]. Ce document se concentre sur un scénario spécifique : création d’un domaine dans le nuage qui se distingue les domaines maintenus en local, mais qui peut avoir une relation d’approbation avec les domaines locaux. 

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- La conservation de séparation de sécurité pour les objets et des identités contenues dans le cloud.

- Migration de domaines individuels locales vers le cloud.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture (*cliquez sur pour effectuer un zoom*). Pour plus d’informations sur les éléments grisé, voir [implémentation une architecture réseau hybride sécurisé dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [implémentation d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Réseau local.** Le réseau local contient sa propre forêt Active Directory et des domaines.

- **Serveurs Active Directory.** Il s’agit des superflus mise en œuvre des services d’annuaire (AD DS) en cours d’exécution en tant que machines virtuelles dans le cloud. Ces serveurs hébergent une forêt contenant un ou plusieurs domaines, distinctes de ces situé en local.

- **Relation d’approbation à sens unique.** Dans le diagramme montre une approbation à sens unique à partir du domaine dans le nuage pour le domaine local. Cette relation permet aux utilisateurs locaux accéder aux ressources dans le domaine dans le nuage, mais pas le sens inverse. Il s’agit d’un cas courant. Toutefois, vous pouvez créer une approbation bidirectionnelle si cloud utilisateurs doivent également accéder aux ressources en local.

- **Sous-réseau annuaire Active.** Les serveurs AD DS sont hébergés dans un sous-réseau distinct. Les règles NSG protéger les serveurs AD DS et peuvent fournir un pare-feu contre le trafic de sources inconnues.

- **Sous-réseau de niveau Web**, du **Business couche sous-réseau**et **sous-réseau couche de données**. Ces sous-réseaux héberge les serveurs et les composants qui s’exécutent les applications dans le cloud. Pour plus d’informations, voir [Machines virtuelles en cours d’exécution pour une architecture multicouches sur Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Les ressources et les machines virtuelles dans ce sous-réseau sont contenues dans le domaine de cloud.

- **Passerelle azure**. La passerelle Azure fournit une connexion entre le réseau local et le VNet Azure. Cela peut être une [connexion VPN] [ azure-vpn-gateway] ou [Azure ExpressRoute][azure-expressroute]. Pour plus d’informations, voir [mise en œuvre une architecture de réseau sécurisée hybride dans Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recommandations

Cette section fournit la liste des recommandations basées sur les composants essentiels requis pour implémenter l’architecture de base. Ces recommandations couvrent :

- Ajoute les paramètres, et

- Configuration de la relation d’approbation.

Vous devrez exigences supplémentaires ou différentes de celles décrites ici. Vous pouvez utiliser les éléments de cette section comme point de départ pour considérer comment personnaliser l’architecture de votre propre système.

### <a name="adds-recommendations"></a>Ajoute recommandations

Pour obtenir des recommandations spécifiques sur l’implémentation Active Directory dans le cloud, consultez le document [Développement d’Active Directory pour Azure][extending-ad-to-azure]. L’article [instructions de déploiement d’Active Directory Windows Server Azure machines virtuelles en fonctionnement] [ ad-azure-guidelines] contient des informations détaillées supplémentaires.

### <a name="trust-recommendations"></a>Gestion de la confidentialité recommandations

Les domaines locaux sont contenues dans un autre domaine à partir des domaines dans le cloud. Pour activer l’authentification des utilisateurs locaux dans le nuage, les domaines dans le nuage doivent faire confiance au domaine d’ouverture de session de la forêt locale. De même, si le cloud fournit un domaine de connexion pour les utilisateurs externes, il peut être nécessaire pour la forêt locale faire confiance au domaine cloud.

Vous pouvez établir des approbations au niveau de la forêt en [créant des approbations de forêt][creating-forest-trusts], ou au niveau du domaine en [créant des approbations externes][creating-external-trusts]. Une approbation de niveau forêt crée une relation entre tous les domaines de deux forêts. Une niveau de confiance du domaine externes ne crée une relation entre deux domaines spécifiés. Vous devez créer uniquement les approbations au niveau de domaine externes entre les domaines dans des forêts différentes.

Les approbations peuvent être unidirectionnelles (à sens unique) ou bidirectionnel (bidirectionnelle) :

- Une approbation à sens unique permet aux utilisateurs d’un domaine ou forêt (appelé le domaine *entrants* ou la forêt) pour accéder aux ressources contenues dans un autre (le domaine *sortant* ou la forêt). 

- Une approbation bidirectionnelle permet aux utilisateurs de domaine ou forêt accéder aux ressources contenues dans l’autre.

Le tableau suivant résume les configurations de gestion de la confidentialité pour certains scénarios simples :

| Scénario | Gestion de la confidentialité en local | Gestion de la confidentialité cloud |
|----------|-------------------|-------------|
| En local utilisateurs nécessitent un accès aux ressources dans le nuage, mais pas inversement | À sens unique, entrant | À sens unique, sortant |
| Les utilisateurs dans le cloud nécessitent un accès aux ressources situées en local, mais pas inversement | À sens unique, sortant | À sens unique, entrant |
| Les utilisateurs dans le cloud et en local doit accéder aux ressources contenues dans le cloud et en local | À double sens, entrants et sortants | À double sens, entrants et sortants |

## <a name="security-considerations"></a>Considérations sur la sécurité

Approbations au niveau de la forêt sont transitifs. Si vous établissez une approbation de niveau forêt entre une forêt en local et un dans le cloud, cette approbation est étendue à d’autres domaines nouveau créés dans une forêt. Si vous utilisez des domaines pour fournir une séparation pour des raisons de sécurité, envisagez de créer des approbations au niveau du domaine uniquement. Approbations au niveau de domaine sont non-transitives.

AD spécifiques en matière de sécurité, consultez la section *Considérations relatives à la sécurité* de [Développement d’Active Directory pour Azure][extending-ad-to-azure].

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Mise en œuvre d’au moins deux superflus pour chaque domaine. Cela permet la réplication automatique entre les serveurs. Créer une disponibilité défini pour les ordinateurs virtuels servant de serveurs AD gestion chaque domaine. Assurez-vous qu’il existe au moins deux serveurs dans le jeu. 

Envisagez également de désigner un ou plusieurs serveurs dans chaque domaine comme [maîtres d’opérations en attente] [ standby-operations-masters] en cas d’échec de la connectivité à un serveur ayant un rôle FSMO.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Active Directory est automatiquement adapte de superflus qui font partie du même domaine. Demandes sont réparties entre tous les contrôleurs dans un domaine. Vous pouvez ajouter un autre contrôleur de domaine, et il se synchronise automatiquement avec le domaine. Ne configurez pas un équilibrage de charge distincts pour acheminer le trafic vers contrôleurs au sein du domaine. Vous assurer que le domaine toutes les ressources de mémoire et de stockage suffisantes pour gérer la base de données de domaine. Uniformiser la taille de contrôleur de domaine tous les ordinateurs virtuels.

## <a name="management-and-monitoring-considerations"></a>Gestion et considérations relatives au contrôle

Pour plus d’informations sur la gestion et les considérations relatives au contrôle, consultez les sections équivalentes dans [Extension d’Active Directory pour Azure][extending-ad-to-azure]. 

Pour plus d’informations, consultez [Surveillance Active Directory][monitoring_ad]. Vous pouvez installer les outils tels que [Microsoft Systems Center] [ microsoft_systems_center] sur un serveur de surveillance dans le sous-réseau gestion pour aider à effectuer les tâches suivantes. 

## <a name="solution-components"></a>Composants de la solution

Un exemple de script solution, [Déployer ReferenceArchitecture.ps1][solution-script], n’est disponible que vous pouvez utiliser pour implémenter l’architecture qui suit les recommandations décrites dans cet article. Ce script utilise le Gestionnaire de ressources Azure modèles. Les modèles sont disponibles comme un ensemble de blocs de construction fondamentaux, chacun d'entre eux effectue une action spécifique tel que la création d’un VNet ou configurez un NSG. L’objectif du script est d’organiser le déploiement du modèle.

Les modèles sont paramétrées, avec les paramètres contenus dans les fichiers JSON distincts. Vous pouvez modifier les paramètres dans ces fichiers pour configurer le déploiement pour répondre à vos besoins. Vous n’avez pas besoin de modifier les modèles eux-mêmes. Vous ne devez pas modifier les schémas des objets dans les fichiers de paramètres.

Lorsque vous modifiez les modèles, créer des objets qui suivent les conventions d’attribution de noms décrites dans [Recommandé les Conventions d’affectation des noms des ressources Azure][naming-conventions].

L’exemple de solution crée et configure un environnement dans le nuage qui mettent en œuvre, un domaine nommé *treyresearch.com*. Cet environnement comprend la passerelle VPN sous-réseau et serveurs, DMZ, couche web, couche d’entreprise et composants de niveau d’accès aux données, ajoute et un niveau de gestion. L’exemple de solution comprend également une configuration facultatif pour la création d’un environnement simulé en local avec son propre domaine, *contoso.com*. La solution inclut des scripts qui permettent d’établir une relation d’approbation entre ces domaines qui permet aux utilisateurs locaux accéder aux objets dans le domaine *treyresearch.com* dans le cloud.

Les sections suivantes décrivent les éléments de l’en local et en nuage configurations.

### <a name="on-premises-components"></a>Composants en local

>[AZURE.NOTE] Ces composants ne sont pas l’objectif principal de l’architecture décrite dans ce document et sont fournies simplement pour vous permettre de tester l’environnement cloud en toute sécurité, plutôt que d’utiliser un environnement de production réel. Pour cette raison, cette section résume uniquement les fichiers de paramètre de clé. Vous pouvez modifier les paramètres tels que les adresses IP ou les tailles des ordinateurs virtuels, mais il est conseillé de laisser la plupart des autres paramètres inchangés.

Cet environnement comprend une forêt Active Directory du domaine *contoso.com* . Le domaine contient deux serveurs ajoute avec des adresses IP 192.168.0.4 et 192.168.0.5. Ces deux serveurs exécutant également le service DNS. Le compte d’administrateur local sur les deux machines virtuelles est appelé `testuser` par mot de passe `AweS0me@PW`. En outre, la configuration configure une passerelle VPN pour vous connecter à la VNet dans le cloud. Vous pouvez modifier la configuration en modifiant les fichiers JSON suivants situés dans les [**paramètres/locales**] [ on-premises-folder] dossier :

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Ce fichier définit l’espace d’adressage réseau pour l’environnement local.

- ** [desmachines virtuelles adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Ce fichier contient la configuration pour les ordinateurs locaux virtuels ajoute services d’hébergement. Par défaut, les deux machines virtuelles *Standard-DS3-v2* sont créés.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** et ** [connection.parameters.json][on-premises-connection-parameters]**. Ces fichiers contiennent les paramètres pour la connexion VPN à la passerelle VPN Azure dans le cloud, y compris la clé partagée pour être utilisé pour protéger le trafic traversant la passerelle.

Les autres fichiers dans le dossier contiennent les informations de configuration utilisées pour créer le domaine local (*contoso.com*) à l’aide de cette infrastructure. Vous permet de les pour installer ajouter, configurer le système DNS et créez la forêt locale.

La solution utilise également le script suivant, nommé [entrant trust.ps1][incoming-trust], qui s’exécute sur un ordinateur dans le domaine local :

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Ce script ajoute les adresses IP des serveurs AD DS dans le cloud (voir la section suivante) au service DNS local, puis utilise [netdom] [ netdom] commande pour créer une approbation à sens unique entrante à partir du domaine dans le cloud (*treyresearch.com*).

### <a name="cloud-components"></a>Composants de nuage

Ces composants constituent le cœur de cette architecture. Ils l’infrastructure pour le domaine *treyresearch.com* de configuration et créer les relations d’approbation avec le domaine *contoso.com* en local. Les [**paramètres/azure**] [ azure-folder] dossier contient les fichiers de paramètres suivantes pour configurer ces composants :

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Ce fichier définit la structure de la VNet pour les ordinateurs virtuels et d’autres composants dans le cloud. Il comprend des paramètres, tels que le nom, un espace d’adressage, sous-réseaux et les adresses de tous les serveurs DNS requis. Les adresses DNS indiquées dans cette référence d’exemple les adresses IP de serveurs DNS en local, ainsi que le serveur DNS Azure par défaut. Modifier ces adresses pour faire référence à la configuration de votre propre DNS si vous n’utilisez pas l’exemple d’environnement local :
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
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
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [desmachines virtuelles adds.parameters.json ] [ virtualmachines-adds-parameters] **. Ce fichier configure les ordinateurs virtuels en cours d’exécution : ajoute dans le cloud. La configuration se compose de deux machines virtuelles. Modifier le nom d’utilisateur et mot de passe dans la `virtualMachineSettings` section et vous pouvez éventuellement modifier la taille de la mémoire virtuelle pour correspondre à la configuration requise du domaine :

    Pour plus d’informations, voir [Extension d’Active Directory pour Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
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
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [Ajouter-ajoute-domaine-controller.parameters.json][add-adds-domain-controller-parameters]**. Ce fichier contient les paramètres de création du domaine *treyresearch.com* couvrant les serveurs ajoute. Il utilise les extensions personnalisées que le domaine et lui ajouter les serveurs ajoute. Sauf si vous créez des serveurs supplémentaires : ajoute (auquel cas vous devez les ajouter à la `vms` tableau), modifier leurs noms à partir de la valeur par défaut ou pour créer un domaine avec un autre nom que vous n’avez pas besoin de modifier ce fichier.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Ce fichier contient les paramètres utilisés pour créer la passerelle VPN Azure dans le cloud utilisé pour se connecter au réseau local. Vous devez modifier le `sharedKey` valeurs contenues dans les `connectionsSettings` section corresponde à celle de l’appareil VPN en local. Pour plus d’informations, voir [mise en œuvre une Architecture réseau hybride avec Azure et en local VPN][hybrid-azure-on-prem-vpn].

- ** [dmz private.parameters.json] [ dmz-private-parameters] ** et ** [dmz public.parameters.json ] [ dmz-public-parameters] **. Ces fichiers configurent entrant (public) et sur les côtés (privées) sortantes des ordinateurs virtuels qui composent la DMZ, protégeant les serveurs dans le cloud. Pour plus d’informations sur ces éléments et leur configuration, consultez [implémentation d’une DMZ entre Azure et Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer web.parameters.json][loadBalancer-web-parameters]**, ** [loadBalancer biz.parameters.json][loadBalancer-biz-parameters]**, et ** [loadBalancer data.parameters.json][loadBalancer-data-parameters]**. Ces fichiers paramètres contiennent les spécifications machine virtuelle pour les niveaux d’accès web, entreprise et de données et configurer les programmes d’équilibrage de charge pour chaque niveau. Il s’agit des ordinateurs virtuels qui hébergent les applications web et les bases de données et effectuer les charges de travail d’entreprise de l’organisation. Les ordinateurs virtuels dans la couche web sont ajoutés au domaine dans le cloud à l’aide de paramètres spécifiés dans la ** [web-machine virtuelle-domaine-join.parameters.json] [ web-vm-domain-join-parameters] ** fichier.

    Chaque fichier contient deux ensembles de paramètres de configuration. La `virtualMachineSettings` section définit les ordinateurs virtuels hébergeant le service ADFS dans le cloud. Par défaut, le script crée deux de ces ordinateurs virtuels dans le même jeu de disponibilité. Les fragments suivants illustrent les parties pertinentes du fichier *loadBalancer web.parameters.json* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
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
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    Vous pouvez modifier la taille et le nombre de machines virtuelles dans chaque niveau selon vos besoins.

    La `loadBalancerSettings` section fournit une description de l’équilibrage de charge pour ces machines virtuelles. L’équilibrage de charge passe le trafic qui s’affiche sur le port 80 (HTTP) et le port 443 (HTTPS) à une ou l’autre des ordinateurs virtuels. 

    >[AZURE.NOTE] La règle pour le port 80 utilise une connexion TCP au lieu de HTTP. C’est parce que l’installation d’IIS sur le niveau web est configurée pour prendre en charge l’authentification Windows uniquement. Authentification anonyme est désactivée. La tentative de *test ping* port 80 via une connexion HTTP échoue avec une erreur 401 (non autorisé), alors qu’à l’aide d’une connexion TCP simplement détecte si le port est actif :

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [desmachines virtuelles mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Ce fichier contient la configuration de la zone saut/gestion des machines virtuelles. Il n’est possible d’obtenir d’ouverture et de l’accès administratif aux ordinateurs virtuels dans le web, entreprise et les niveaux de données à partir de la zone passer. Par défaut, le script crée un seul *Standard_DS1_v2* machine virtuelle, mais vous pouvez modifier ce fichier pour créer des machines virtuelles agrandir ou supplémentaires si la charge de travail Gestion des est susceptible d’être important.

La configuration utilise également le [trust.ps1 sortant] [ outgoing-trust] script ci-dessous pour créer une approbation sortante avec le domaine *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Ce script est similaire au script *entrant trust.ps1* décrit plus haut. Elle ajoute les adresses IP de la local serveurs AD DS au service DNS local, puis utilise [netdom] [ netdom] commande pour créer la relation d’approbation sortants.

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
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Modifier les fichiers de paramètre dans les Scripts/paramètres/locales et les dossiers de Scripts/paramètres/Azure. Mettre à jour les références de groupe de ressources dans ces fichiers pour faire correspondre les noms des groupes de ressources affectées aux variables dans le fichier de déploiement ReferenceArchitecture.ps1. Le tableau suivant répertorie les fichiers de paramètre font référence à quel groupe de ressources. Les groupes de ressources *ra-adfs-la charge de travail-routage*, *ra-adfs-sécurité-routage*, *ra-adfs-ajoute-routage*, *ra-adfs-adfs-routage*et *ra-adfs-proxy-routage* sont uniquement utilisées dans le script PowerShell et ne figurent pas dans les fichiers de paramètres.

  	|Groupe de ressources|Fichiers de paramètres|
  	|--------------|--------------|
  	|RA-adtrust-locales-routage|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adtrust-réseau-routage|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*deux occurrences*)

    En outre, définir la configuration de l’en local et en nuage composants, comme décrit dans les [Composants d’une Solution] [ solution-components] section.

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

    - `WebTier`, qui crée le site web de niveau machines virtuelles et l’équilibrage de charge.

    - `Prepare`, qui effectue toutes les tâches précédents. **Il s’agit de l’option recommandée si vous créez un déploiement entièrement nouvel et vous n’avez pas une infrastructure locale existante.**

    - `Workload`Pour créer la couche vos données professionnelles et machines virtuelles et programmes d’équilibrage de charge. Ces ordinateurs virtuels ne sont pas inclus dans le cadre de la `Prepare` option.

    >[AZURE.NOTE] Si vous utilisez la `Prepare` option, le script prend plusieurs heures.

8.  Si vous utilisez l’exemple de configuration local :

    1. Se connecter à la zone passer (*ra-adtrust-gestion-vm1* dans le groupe de ressources *ra-adtrust-sécurité-routage* ). Connectez-vous en tant qu' *UtilisateurTest* par mot de passe *AweS0me@PW*.

    2.  Dans la zone passer ouvrir une session RDP sur la première machine virtuelle dans le domaine *contoso.com* (le domaine local). Cette machine virtuelle comporte l’adresse IP 192.168.0.4. Le nom d’utilisateur est *contoso\testuser* par mot de passe *AweS0me@PW*.

    3. Télécharger le [entrant trust.ps1] [ incoming-trust] script et exécutez-le pour créer l’approbation entrante à partir du domaine *treyresearch.com* .

9. Si vous utilisez votre propre infrastructure local :

    1. Télécharger le [entrant trust.ps1] [ incoming-trust] script.

    2. Modifier le script et remplacez la valeur de la `$TrustedDomainName` variable avec le nom de votre propre domaine.

    3. Exécuter le script.

10. Dans la zone passer, connectez-vous à la première machine virtuelle dans le domaine *treyresearch.com* (le domaine dans le cloud). Cette machine virtuelle comporte l’adresse IP 10.0.4.4. Le nom d’utilisateur est *treyresearch\testuser* par mot de passe *AweS0me@PW*.

11. Télécharger le [trust.ps1 sortant] [ outgoing-trust] script et exécutez-le pour créer l’approbation entrante à partir du domaine *treyresearch.com* . Si vous utilisez votre propre machines en local, modifiez le script tout d’abord. Définir la `$TrustedDomainName` variable le nom de votre domaine local et spécifiez les adresses IP des serveurs AD DS pour ce domaine dans le `$TrustedDomainDnsIpAddresses` variable.

12. Sur un ordinateur local, exécutez les étapes décrites dans l’article [vérifier une approbation] [ verify-a-trust] pour déterminer si la relation d’approbation a été configurée correctement entre les domaines *contoso.com* et *treyresearch.com* . Vous devrez peut-être patienter pendant quelques minutes après les étapes précédentes avant l’approbation peut être validée.

Les étapes restantes facultatifs montrent comment déterminer si l’approbation du domaine fonctionne comme prévu. Cette procédure requiert que vous avez accès à un ordinateur de développement avec Visual Studio installé.

1.  Dans la fenêtre Azure PowerShell, exécutez la commande suivante pour créer la couche web si vous le n'avez pas configuré précédemment (à l’aide de la `Prepare` option) :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Cette commande crée la couche web et ajoute les ordinateurs virtuels au domaine *treyresearch.com* .

2. À l’aide de Visual Studio sur l’ordinateur de développement, créer une application Web ASP.NET nommée *TreyResearchWebApp*. Utiliser le .NET Framework 4.5.2.

3. Sélectionnez le modèle *MVC* et modifiez l’authentification *Windows*. Ne créez pas un Service d’application dans le cloud.

3. Générez et exécutez l’application pour tester l’authentification. Vérifiez que votre nom d’utilisateur Windows actuel s’affiche dans la barre de menus en haut de la page vers la droite.

4. Fermez Internet Explorer.

5. Dans la fenêtre *Explorateur de solutions* , cliquez sur le projet TreyResearchWebApp et cliquez sur *Publier*.

6. Dans la fenêtre *Publier le site Web* , cliquez sur *personnalisé*. Créer un profil personnalisé nommé *TreyResearchWebApp*.

7. Dans la page de *connexion* , définissez la *méthode de publication* à un *Système de fichiers* et spécifiez un dossier nommé *TreyResearchWebApp*, situé dans un emplacement sur votre ordinateur de développement.

8. Dans la page *paramètres* , définissez la *Configuration* vers *la version*.

9. Dans la page *d’Aperçu* , cliquez sur *Publier*.

10. Connectez-vous à chaque machine virtuelle dans la couche web à son tour (via la zone passer) et effectuer les tâches suivantes. Les adresses IP du site web couche machines virtuelles sont 10.0.1.4 et 10.0.1.5. Le nom d’utilisateur pour les deux machines virtuelles est *treyresearch\testuser* par mot de passe *AweS0me@PW*:

    1. Copiez le dossier *TreyResearchWebApp* et son contenu à partir de l’ordinateur de développement vers le dossier *C:\inetpub* .

    2. À l’aide de la console Gestionnaire des Services Internet (IIS), accédez au *Site Web par* sur l’ordinateur.

    3. Dans le volet *Actions* , cliquez sur *Paramètres de base*et modifiez le chemin d’accès physique du site web à *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. Dans le volet *d’Affichage des fonctionnalités* , double-cliquez sur *l’authentification*. Vérifiez que *L’authentification Windows* est activée et *L’authentification anonyme* est désactivée.

11. à partir d’un ordinateur local, ouvrez Internet Explorer et accédez au site web en http://10.0.1.254 (il s’agit de l’adresse de l’équilibrage de charge couche web).

12. Dans la boîte de dialogue *Sécurité de Windows* , entrez les informations d’identification d’un utilisateur dans le domaine local. Spécifiez un nom d’utilisateur qui n’existe pas dans le domaine *treyresearch* . Si vous utilisez l’environnement simulé locaux tout d’abord créer un utilisateur dans le domaine *contoso* et spécifiez des informations d’identification de cet utilisateur.

13. Lorsque la page d’accueil s’affiche, vérifiez que le bon domaine et un nom d’utilisateur s’affichent dans la barre de menu en haut de la page vers la droite.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les meilleures pratiques pour [l’extension de votre domaine : ajoute local vers Azure][adds-extend-domain]

- Découvrez les meilleures pratiques pour la [Création d’une infrastructure ADFS] [ adfs] dans Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Architecture de réseau hybride avec des domaines AD différents sécurisée"