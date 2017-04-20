<properties
   pageTitle="Exemples de configuration ExpressRoute clients routeur | Microsoft Azure"
   description="Cette page fournit des exemples de configuration de la routeur routeurs Cisco et Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Exemples de configuration de routeur pour configurer et gérer le routage

Cette page contient des exemples de configuration routage et l’interface pour routeurs série Cisco IOS-XE et MX Juniper. Ceux-ci sont destinés à être exemples pour obtenir des instructions uniquement et ne doivent pas être utilisés en l’état. Vous pouvez travailler avec votre fournisseur afin de parvenir à de façon appropriée pour votre réseau. 

>[AZURE.IMPORTANT] Exemples de cette page sont destinés à être purement pour obtenir des instructions. Vous devez travailler avec l’équipe de ventes / technique de votre fournisseur et votre équipe réseau afin de parvenir avec les configurations appropriées à vos besoins. Microsoft ne prend pas en charge les problèmes liés aux configurations répertoriées dans cette page. Vous devez contacter votre fournisseur de périphérique de problèmes de prise en charge.

Exemples de configuration du routeur ci-dessous s’appliquent à tous les peerings. Passez en revue [ExpressRoute peerings](expressroute-circuit-peerings.md) et [Configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour plus d’informations sur le routage.

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE en fonction de routeurs

Les exemples de cette section s’appliquent à n’importe quel routeur la famille IOS XE du système d’exploitation en cours d’exécution.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuration et les sous-dossiers interfaces

Vous aurez besoin d’une interface sub par peering dans chaque routeur que vous vous connectez à Microsoft. Une interface sub peut être identifiée avec un ID de réseau local virtuel ou une paire empilée d’ID de réseau local virtuel et une adresse IP.

#### <a name="dot1q-interface-definition"></a>Définition de l’interface Dot1Q

Cet exemple fournit la définition d’interface sous-adresse une interface sous-adresse avec un ID de réseau local virtuel unique. L’ID de réseau local virtuel est unique par peering. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>Définition de l’interface QinQ

Cet exemple fournit la définition d’interface sous-adresse une interface sous-adresse avec un ID de réseau local virtuel deux. L’ID de réseau local virtuel externe (s-balise), si utilisé reste identique sur toutes les peerings. L’ID de réseau local virtuel interne (balise c) est unique par peering. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. la configuration de sessions eBGP

Vous devez configurer une session BGP avec Microsoft pour chaque peering. L’exemple ci-dessous vous permet de configurer une session BGP auprès de Microsoft. Si l’adresse IPv4 que vous avez utilisé pour l’interface sub a été a.b.c.d, l’adresse IP du voisin BGP (Microsoft) seront a.b.c.d+1. Le dernier octet d’adresse IPv4 de voisin BGP sera toujours un nombre pair.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. la configuration préfixes pour être publié sur la session BGP

Vous pouvez configurer votre routeur pour annoncer select doivent être respectées à Microsoft. Vous pouvez le faire à l’aide de l’exemple ci-dessous.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. cartes itinéraire

Vous pouvez utiliser itinéraire cartes et listes de préfixe aux préfixes filtre propagées à votre réseau. Vous pouvez utiliser l’exemple ci-dessous pour accomplir la tâche. Vous assurer que le programme d’installation listes préfixe approprié.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Routeurs de série Juniper MX 

Les exemples de cette section s’appliquent à tout routeur série MX Juniper.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuration et les sous-dossiers interfaces

#### <a name="dot1q-interface-definition"></a>Définition de l’interface Dot1Q

Cet exemple fournit la définition d’interface sous-adresse une interface sous-adresse avec un ID de réseau local virtuel unique. L’ID de réseau local virtuel est unique par peering. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>Définition de l’interface QinQ

Cet exemple fournit la définition d’interface sous-adresse une interface sous-adresse avec un ID de réseau local virtuel deux. L’ID de réseau local virtuel externe (s-balise), si utilisé reste identique sur toutes les peerings. L’ID de réseau local virtuel interne (balise c) est unique par peering. Le dernier octet de votre adresse IPv4 sera toujours un nombre impair.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. la configuration de sessions eBGP

Vous devez configurer une session BGP avec Microsoft pour chaque peering. L’exemple ci-dessous vous permet de configurer une session BGP auprès de Microsoft. Si l’adresse IPv4 que vous avez utilisé pour l’interface sub a été a.b.c.d, l’adresse IP du voisin BGP (Microsoft) seront a.b.c.d+1. Le dernier octet d’adresse IPv4 de voisin BGP sera toujours un nombre pair.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. la configuration préfixes pour être publié sur la session BGP

Vous pouvez configurer votre routeur pour annoncer select doivent être respectées à Microsoft. Vous pouvez le faire à l’aide de l’exemple ci-dessous.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. cartes itinéraire

Vous pouvez utiliser itinéraire cartes et listes de préfixe aux préfixes filtre propagées à votre réseau. Vous pouvez utiliser l’exemple ci-dessous pour accomplir la tâche. Vous assurer que le programme d’installation listes préfixe approprié.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus d’informations.
