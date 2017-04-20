<properties
   pageTitle="Conditions préalables pour adoption ExpressRoute | Microsoft Azure"
   description="Cette page fournit la liste des exigences à respecter avant de pouvoir commander un circuit Azure ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# <a name="expressroute-prerequisites--checklist"></a>Liste de vérification et ExpressRoute conditions préalables  

Pour vous connecter aux services cloud Microsoft à l’aide de ExpressRoute, vous devez vérifier que les conditions suivantes répertoriées dans les sections ci-dessous sont remplies.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Compte Azure

- Un compte Microsoft Azure valide et actif. Cela est nécessaire pour configurer le circuit ExpressRoute. Circuits ExpressRoute sont des ressources d’abonnements Azure. Un abonnement Azure est une obligation même si la connectivité est limitée aux services cloud non Microsoft Azure, tels que les services Office 365 et CRM en ligne.
- Un abonnement Office 365 actif (si vous utilisez les services Office 365). Consultez la section [Office 365 de configuration spécifiques](#office-365-specific-requirements) de cet article pour plus d’informations.

## <a name="connectivity-provider"></a>Fournisseur de connectivité
- Vous pouvez travailler avec un [partenaire de connectivité ExpressRoute](expressroute-locations.md#partners) pour vous connecter au cloud Microsoft. Vous pouvez configurer une connexion entre votre réseau local et Microsoft de [trois manières](expressroute-introduction.md#howtoconnect). 
- Si votre fournisseur n’est pas un partenaire de connectivité ExpressRoute, vous pouvez connecter toujours dans le cloud Microsoft via un [fournisseur d’exchange cloud](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Configuration requise de réseau
- **Connectivité redondante**: il n’est pas obligatoire redondance sur la connectivité physique entre vous et votre fournisseur. Microsoft nécessite-t-il redondantes sessions BGP configurer entre les composants Router de Microsoft et les routeurs homologation, même lorsque vous avez simplement [une connexion physique à un échange de cloud](expressroute-faqs.md#onep2plink). 
- **Routage**: en fonction de la façon de vous connecter au Cloud Microsoft, vous ou votre fournisseur besoin pour configurer et gérer les sessions BGP pour [les domaines de routage](expressroute-circuit-peerings.md). Certains fournisseur de connectivité Ethernet ou cloud exchange peut proposer gestion BGP comme un service à valeur ajoutée.
- **NAT**: Microsoft n’accepte que les adresses IP publiques via Microsoft peering. Si vous utilisez des adresses IP privées dans votre réseau local, vous ou votre fournisseur doivent traduire des adresses IP privées à l’adresse IP publique des adresses [à l’aide de la NAT](expressroute-nat.md).
- **Qualité de service**: Skype pour entreprise a différents services (par exemple, vocaux et vidéo, du texte) qui nécessitent de différencier traitement de qualité de service. Vous et votre fournisseur doivent respecter les [exigences de qualité de service](expressroute-qos.md).
- **Sécurité du réseau**: vous devez prendre en compte [la sécurité du réseau](../best-practices-network-security.md) lors de la connexion dans le Cloud Microsoft via ExpressRoute.
 
## <a name="office-365"></a>Office 365

Si vous prévoyez d’activer Office 365 sur ExpressRoute, veuillez consulter les documents suivants pour plus d’informations sur la configuration requise pour Office 365.


- [Vue d’ensemble des ExpressRoute pour Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routage avec ExpressRoute pour Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Plages d’adresses IP et URL Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Planification réseau et optimisation des performances pour Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Outils et calculatrices de bande passante réseau](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Intégration d’Office 365 aux environnements locaux](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM en ligne 
Si vous prévoyez d’activer CRM Online sur ExpressRoute, veuillez consulter les documents suivants pour plus d’informations sur CRM Online

- [CRM Online URL](https://support.microsoft.com/kb/2655102) et [plages d’adresses IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
- Rechercher un fournisseur de connectivité ExpressRoute. Voir [ExpressRoute partenaires et des emplacements homologation](expressroute-locations.md).
- Reportez-vous à la configuration requise pour le [routage](expressroute-routing.md), [NAT](expressroute-nat.md) et [qualité de service](expressroute-qos.md).
- Configurer votre connexion ExpressRoute.
    - [Créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurer le routage](expressroute-howto-routing-classic.md)
    - [Lier un VNet à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

