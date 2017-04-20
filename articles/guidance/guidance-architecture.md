
<properties
   pageTitle="Conseils Azure | modèles et pratiques | Microsoft Azure"
   description="Architectures de référence Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Architectures de référence Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Ce contenu est en cours de développement active. Il est utile aujourd'hui, afin que nous apportions rendre disponible pour l’aperçu. Nous apprécions vos commentaires._

Notre architectures de référence sont organisés par scénario, avec plusieurs architectures connexes regroupés.
Chaque architecture individuel offre pratiques recommandées et des instructions, ainsi qu’un composant exécutable représentant les recommandations.
La plupart des architectures sont progressives ; créez en haut des architectures précédents qui ont des exigences moins.

## <a name="designing-your-infrastructure-for-resiliency"></a>Conception de l’infrastructure la résilience des

Cette série commence par des pratiques recommandées pour une configuration machine virtuelle optimale et se termine par un déploiement de plusieurs région avec basculement.

- [Exécutez une machine virtuelle Windows sur Azure](guidance-compute-single-vm.md)
- [Exécuter une machine virtuelle Linux sur Azure](guidance-compute-single-vm-linux.md)
- [Plusieurs ordinateurs virtuels pour la disponibilité et extensibilité élevées en cours d’exécution](guidance-compute-multi-vm.md)
- [Machines virtuelles Windows en cours d’exécution pour une architecture multicouches](guidance-compute-n-tier-vm.md)
- [Machines virtuelles Linux en cours d’exécution pour une architecture multicouches](guidance-compute-n-tier-vm-linux.md)
- [Machines virtuelles Windows en cours d’exécution dans plusieurs zones de disponibilité](guidance-compute-multiple-datacenters.md)
- [Machines virtuelles Linux en cours d’exécution dans plusieurs zones de disponibilité](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>Connecter votre réseau local à Azure

Cette série commence par montrant les façons de se connecter votre réseau existant à Azure. Puis elle se développe pour indiquer la configuration requise pour la disponibilité et la sécurité.

- [Mise en œuvre une architecture réseau hybride avec Azure et VPN local](guidance-hybrid-network-vpn.md)
- [Mise en œuvre une architecture réseau hybride avec Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [Mise en œuvre une architecture de réseau hautement disponible hybride](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Sécurisation de votre réseau hybride

Cette série couvre pratiques sur la création de DMZ dans Azure à connexion sécurisée en provenance de votre centre de données en local et sur Internet.

- [Mise en œuvre une DMZ entre Azure et votre centre de données locale](guidance-iaas-ra-secure-vnet-hybrid.md)
- [Mise en œuvre une DMZ entre Azure et Internet](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Fournir des services d’identité

Cette série commence par montrant comment utiliser Azure Active Directory pour fournir une authentification utilisateur dans Azure. Puis elle se développe pour recouvrir des scénarios complexes étendre votre infrastructure ajoute à Azure et l’utilisation d’ADFS pour la délégation.

- [Implémentation d’Azure Active Directory](./guidance-identity-aad.md)
- [Extension de Services Active Directory (ajoute) vers Azure](./guidance-identity-adds-extend-domain.md)
- [Création d’une forêt de ressources Services : (Active Directory Directory ajoute) dans Azure](./guidance-identity-adds-resource-forest.md)
- [Mise en œuvre des Services de fédération Active Directory (AD FS) dans Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Architecture d’application web scalable avec PaaS Azure

Cette série traite des recommandations pour la construction d’applications web scalable et hautement disponible. 

- [Application web simple](guidance-web-apps-basic.md)
- [Amélioration extensibilité élevées dans une application web](guidance-web-apps-scalability.md)
- [Application Web avec haute disponibilité](guidance-web-apps-multi-region.md)
