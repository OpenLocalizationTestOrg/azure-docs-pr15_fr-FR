<properties
   pageTitle="Activer les groupes de sécurité réseau dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **Activer les groupes de sécurité réseau**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-network-security-groups-in-azure-security-center"></a>Activer les groupes de sécurité réseau dans le centre de sécurité Azure

Centre de sécurité Azure recommande activer un groupe de sécurité réseau (NSG) si une n’est pas déjà activée. NSGs contenir une liste des règles de liste de contrôle (accès) autoriser ou refuser le trafic réseau vers vos instances machine virtuelle dans un réseau virtuel. NSGs peuvent être associés aux sous-réseaux ou instances machine virtuelle individuelles dans ce sous-réseau. Lorsqu’un NSG est associé à un sous-réseau, les règles et s’appliquent à toutes les instances de ce sous-réseau machine virtuelle. En outre, le trafic vers une machine virtuelle individuel peut être restreint davantage en associant un NSG directement à cet ordinateur virtuel. Pour en savoir plus, voir [qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)

Si vous n’avez pas NSGs activés, centre de sécurité présentera deux recommandations à vous : activer des groupes de sécurité réseau sur sous-réseaux et activer des groupes de sécurité réseau sur des machines virtuelles. Vous choisissez quel niveau sous-réseau ou machine virtuelle, appliquer NSGs.


> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **Activer les groupes de sécurité réseau** sous-réseaux ou sur des machines virtuelles.
![Activer les groupes de sécurité réseau][1]

2. Cette action ouvre la carte **Configurer des groupes de sécurité réseau manquants** pour sous-réseaux ou pour machines virtuelles, selon la recommandation que vous avez sélectionné. Sélectionnez un sous-réseau ou une machine virtuelle pour configurer un NSG sur.

  ![Configurer NSG pour sous-réseau][2]

  ![Configurer NSG machine virtuelle][3]
3. Sur la carte du **groupe de sécurité réseau choisir** sélectionner un NSG existant ou sélectionnez cette option pour créer un nouveau NSG.

  ![Sélectionnez le groupe de sécurité réseau][4]

Si vous créez une nouvelle NSG, suivez les étapes de [la gestion de NSGs à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) créer un NSG et définir des règles de sécurité.

## <a name="see-also"></a>Voir aussi

Cet article vous montrer comment mettre en œuvre la recommandation centre de sécurité « Activer des groupes de sécurité réseau » pour sous-réseaux ou machines virtuelles. Pour plus d’informations sur l’activation NSGs, voir les rubriques suivantes :

- [Qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Comment gérer les NSGs à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
