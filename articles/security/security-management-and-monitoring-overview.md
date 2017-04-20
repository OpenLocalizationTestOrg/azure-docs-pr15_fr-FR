<properties
   pageTitle="Gestion de la sécurité Azure et présentation du contrôle | Microsoft Azure"
   description=" Azure fournit des mécanismes de sécurité afin de faciliter la gestion et de surveillance des services cloud Azure et machines virtuelles.  Cet article fournit une vue d’ensemble de ces fonctionnalités de sécurité et les services. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Gestion de la sécurité Azure et présentation du contrôle

Azure fournit des mécanismes de sécurité afin de faciliter la gestion et de surveillance des services cloud Azure et machines virtuelles. Cet article fournit une vue d’ensemble de ces fonctionnalités de sécurité et les services. Des liens sont fournis vers des articles qui donnent des détails de chaque afin que vous pouvez en savoir plus.

La sécurité de vos services cloud Microsoft est un partenariat et le partage des responsabilités entre vous et Microsoft. Partage des responsabilités signifie Microsoft est responsable de la Microsoft Azure et centres de sécurité physique de ses données (au moyen de protection de sécurité tels que badge verrouillées entrée portes et protège les plages de gestion). En outre, Azure fournit des niveaux de sécurité de cloud au niveau du logiciel qui répond aux besoins de ses clients exigeants sécurité, confidentialité et conformité.

Vous êtes propriétaire de vos données et des identités, la responsabilité de la protection, la sécurité de vos ressources en local et la sécurité des composants de cloud que vous contrôlez. Microsoft vous propose des contrôles de sécurité et des fonctions pour vous aider à protéger vos données et applications. Votre responsable de la sécurité est basé sur le type de service cloud.

Le diagramme suivant récapitule le solde de responsabilité pour Microsoft et le client.

![Partage des responsabilités][1]

Pour approfondir gestion de la sécurité, voir [gestion de la sécurité dans Azure](azure-security-management.md).

Voici les principales fonctionnalités qui seront traités dans cet article :

- Contrôle d’accès basé sur un rôle
- Contre les logiciels malveillants
- Authentification multifacteur
- ExpressRoute
- Passerelles réseau virtuel
- Gestion des identités dotés de privilèges
- Protection d’identité
- Centre de sécurité

## <a name="role-based-access-control"></a>Contrôle d’accès basé sur un rôle

Contrôle d’accès basé sur un rôle (RBAC) fournit la gestion des permissions accès pour les ressources Azure. L’utilisation de RBAC, vous pouvez octroyer aux personnes que la quantité d’accès dont elles ont besoin pour effectuer leur travail.  RBAC peut également vous aider à vous assurer que lorsque les utilisateurs quittent l’organisation qu’ils perdent l’accès aux ressources dans le cloud.

Pour en savoir plus :

- [Blog de l’équipe Active Directory sur RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Contrôle d’accès basé sur un rôle Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Contre les logiciels malveillants

Avec Azure logiciel contre les logiciels malveillants des fournisseurs de sécurité majeures tels que Microsoft Symantec, tendance Micro, McAfee et Kaspersky permet de protéger vos machines virtuelles à partir de fichiers malveillants, publicitaires et d’autres menaces.

Microsoft Antimalware vous offre la possibilité d’installer un agent contre les logiciels malveillants pour les rôles PaaS et machines virtuelles. En fonction de System Center Endpoint Protection, cette fonctionnalité rend disponibles les fonctions éprouvées locaux technologie de sécurité dans le cloud.

Nous proposons également d’intégration en profondeur pour la de tendance [Sécurité approfondie](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ et [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produits dans la plateforme Azure. DeepSecurity est une solution Antivirus et SecureCloud est une solution de chiffrement. Machines virtuelles à l’aide d’un modèle d’extension sera déployé sur DeepSecurity. Grâce au portail de l’interface utilisateur et PowerShell, vous pouvez choisir d’utiliser DeepSecurity à l’intérieur de nouvelles machines virtuelles qui sont en cours empilés vers le haut ou machines virtuelles existantes qui ont déjà été déployés.

Protection de Point de terminaison Symantec (SEP) est également prise en charge sur Azure. Via l’intégration de portail, clients ont la possibilité de spécifier qu’ils souhaitent SEP au sein d’une machine virtuelle. Indicateur de continuation peut être installée sur une nouvelle machine virtuelle via le portail Azure ou peut être installée sur un ordinateur virtuel existant à l’aide de PowerShell.

Pour en savoir plus :

- [Déployer des Solutions contre les logiciels malveillants sur Machines virtuelles Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Microsoft contre les logiciels malveillants pour les Services de Cloud Azure et Machines virtuelles](../security/azure-security-antimalware.md)
- [Découvrez comment installer et configurer la sécurité complète du Micro tendance en tant que Service sur un ordinateur Windows virtuel](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Comment installer et configurer Symantec Endpoint Protection sur un ordinateur Windows virtuel](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nouvelles Options contre les logiciels malveillants pour la protection des Machines virtuelles Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Authentification multifacteur

L’authentification multifacteur Azure (MFA) est une méthode d’authentification qui nécessite l’utilisation de plusieurs méthodes de vérification et ajoute un second niveau de sécurité critique à l’utilisateur se-ins et les transactions. L’authentification Multifacteur contribue à garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il propose une authentification renforcée via une plage d’options de vérification, appel téléphonique, un message texte ou l’application mobile notification ou la vérification de code et 3e partie formule jetons.

Pour en savoir plus :

- [Authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md)
- [Fonctionnement de l’authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux dans le cloud Microsoft via une connexion privée dédiée facilitée par un fournisseur de connectivité. ExpressRoute, vous pouvez établir des connexions aux services cloud Microsoft, tels que Microsoft Azure, Office 365 et CRM Online. Connectivité peut être à partir d’un réseau (IP VPN) pour tout, un réseau Ethernet point à point ou une connexion cross virtuelle via un fournisseur de connectivité chez un autre site. ExpressRoute connexions ne passent pas sur l’Internet public. Cela permet de connexions ExpressRoute à offrent davantage de fiabilité, vitesses plus rapides, latence inférieur et sécurité plus élevée que connexions classiques via Internet.

Pour en savoir plus :

- [Présentation technique ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Passerelles réseau virtuel

Passerelles VPN, également appelées Azure virtuel réseau passerelles, servent à acheminer le trafic réseau entre les réseaux virtuels et emplacements locaux. Ils permettent également à acheminer le trafic entre plusieurs réseaux virtuels dans Azure (VNet à VNet).  Passerelles VPN offrent une connectivité entre locaux sécurisé entre Azure et votre infrastructure.

Pour en savoir plus :

- [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Présentation de la sécurité réseau Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Gestion des identités dotés de privilèges

Parfois, les utilisateurs ont besoin d’effectuer les opérations dotés de privilèges dans Azure ressources ou d’autres applications SaaS. Cela signifie souvent des organisations ont pour leur donner un accès privilégié permanent dans Azure Active Directory (AD Azure). Il s’agit d’un problème de sécurité croissant pour les ressources hébergé sur le nuage, car les organisations ne pourra pas suffisamment contrôler ce que font les utilisateurs avec l’accès privilégié.
En outre, si un compte d’utilisateur avec un accès privilégié est compromis, cette une violation pourrait avoir un impact sur votre sécurité cloud globale. Gestion des identités Azure AD privilégié vous aide à résoudre ce risque en réduisant le temps d’exposition des privilèges et en augmentant visibilité sur l’utilisation.  

Gestion des identités dotés de privilèges introduit le concept d’un administrateur temporaire pour un rôle ou « juste à temps » droits d’administrateur, ce qui correspond à un utilisateur ayant besoin d’effectuer un processus d’activation pour ce rôle. Le processus d’activation remplace l’affectation de l’utilisateur à un rôle dans Azure AD période inactive actif, pour une certaine date ; par exemple 8 heures.

Pour en savoir plus :

- [Gestion des identités Azure AD privilégié](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Prise en main la gestion des identités Azure AD dotés de privilèges](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Protection d’identité

Protection d’identité Azure Active Directory (AD) offre une vue globale des activités de connexion suspectes vulnérabilités potentielles pour protéger votre entreprise. Protection d’identité détecte activités suspectes pour les utilisateurs et les identités privilégié (administrateur), basées sur des signaux telles que les attaques en force, divulguées des informations d’identification et connexions à partir d’emplacements expliquerons et infection appareils.

En fournissant les notifications et mise à jour recommandée, Protection d’identité vous permet de limiter les risques en temps réel. Il calcule la gravité du risque utilisateur, et vous pouvez configurer des règles basées sur le risque pour aider automatiquement divulgation d’informations application accéder à partir de menaces futures.

Pour en savoir plus :

- [Protection d’identité Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canal 9 : Azure AD et identité diaporama : aperçu de la Protection d’identité](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centre de sécurité

Centre de sécurité Azure vous aide à prévenir, détecter et répondre aux menaces et fournit augmentée de visibilité et contrôler, la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

Centre de sécurité vous permet d’optimiser et contrôler la sécurité de vos ressources Azure par :

- Ce qui vous permet de définir les stratégies de ressources de votre abonnement Azure en fonction des besoins de sécurité de votre société et le type d’applications ou critère de diffusion des données dans chaque abonnement.
- Contrôle de l’état de vos machines virtuelles Azure, mise en réseau et les applications.
- Qui fournit une liste hiérarchisée d’alertes de sécurité, notamment les alertes de solutions des partenaires intégrée, ainsi que les informations que nécessaires pour effectuer des recherches rapides et les recommandations sur la façon de mettre à jour une attaque.

Pour en savoir plus :

- [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
