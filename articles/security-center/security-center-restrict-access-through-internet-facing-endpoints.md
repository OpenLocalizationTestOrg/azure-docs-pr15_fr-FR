<properties
   pageTitle="Limiter l’accès par le biais extrémités via Internet dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **limiter l’accès par point de terminaison qui fait face à Internet**."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Limiter l’accès par le biais extrémités via Internet dans le centre de sécurité Azure

Centre de sécurité Azure recommande de limiter l’accès via les points de terminaison via Internet si un de vos groupes de sécurité réseau (NSGs) contient une ou plusieurs règles de trafic entrant qui autorisent l’accès à partir de « toute » adresse IP source. Ouverture d’accès à « tout » peut permettre aux pirates d’accéder à vos ressources. Centre de sécurité recommande que modifier ces règles de trafic entrant pour limiter l’accès aux adresses IP source réellement besoin d’y accéder.

Cette recommandation est générée pour n’importe quel port non web qui possède « un » en tant que source.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la **carte de recommandations**, sélectionnez **restreindre l’accès par point de terminaison qui fait face à Internet**.
![Limiter l’accès à Internet les en regard de point de terminaison][1]

2. Cette action ouvre la carte **limiter l’accès par point de terminaison qui fait face à Internet**. Cette carte répertorie les machines virtuelles (machines virtuelles) avec des règles de trafic entrant qui créent un problème de sécurité potentiel. Sélectionnez une machine virtuelle.
![Sélectionnez une machine virtuelle][2]

3. La carte **NSG** affiche les informations groupe de sécurité réseau, les règles de trafic entrant connexes et la machine virtuelle associée. Sélectionnez **Modifier les règles de trafic entrant** pour poursuivre la modification d’une règle de trafic entrant.
![Carte de groupe de sécurité réseau][3]

4. Les **règles de sécurité entrant** carte activez la règle entrante à modifier. Dans cet exemple, nous allons sélectionnez **AllowWeb**.
![Règles de sécurité de trafic entrant][4]

  Remarque Vous pouvez également sélectionner **règles par défaut** pour afficher l’ensemble de règles par défaut contenus dans tous les NSGs. Les règles par défaut ne peut pas être supprimés mais, car elles sont affectées une priorité, elles peuvent être remplacées par les règles que vous créez. En savoir plus sur les [règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules).
![Règles par défaut][5]

5. Sur la carte **AllowWeb** , modifiez les propriétés de la règle de trafic entrant afin que la **Source** est une adresse IP ou un bloc d’adresses IP. Pour en savoir plus sur les propriétés de la règle de trafic entrant, voir [règles NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Modifier la règle de trafic entrant][6]

## <a name="see-also"></a>Voir aussi

Cet article vous montrer comment mettre en œuvre la recommandation centre de sécurité « Restreindre l’accès par point de terminaison qui fait face à Internet ». Pour plus d’informations sur l’activation NSGs et règles, voir les rubriques suivantes :

- [Qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Comment gérer les NSGs à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md)--Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md), découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md): Découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)--apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher Forum aux questions sur l’utilisation du service.
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/)--obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
