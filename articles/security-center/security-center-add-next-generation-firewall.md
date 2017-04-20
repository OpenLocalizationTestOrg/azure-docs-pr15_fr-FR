<properties
   pageTitle="Ajouter un pare-feu génération suivant dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre les recommandations centre de sécurité Azure **Ajouter un pare-feu de génération suivante** et **utilise acheminer via Conviction uniquement**."
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

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Ajouter un pare-feu génération suivant dans le centre de sécurité Azure

Centre de sécurité Azure peut recommander que vous ajoutez un pare-feu génération suivant (Conviction) d’un partenaire Microsoft afin d’augmenter la protection de votre sécurité. Ce document vous présente un exemple de cette procédure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **Ajouter un pare-feu de génération suivante**.
![Ajouter un pare-feu de génération suivant][1]

2. Dans la carte **Ajouter un pare-feu génération suivante** , sélectionnez un point de terminaison.
![Sélectionnez un point de terminaison][2]

3. Une deuxième carte **Ajouter un pare-feu génération suivante** s’ouvre. Vous pouvez choisir d’utiliser une solution existante, le cas échéant, ou vous pouvez créer un autre. Dans cet exemple il n’existe aucune solution existante afin que nous allons créer une nouvelle Conviction.
![Créer nouveau pare-feu génération suivante][3]

4. Pour créer une nouveau Conviction, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous allons sélectionner **Point de vérification**.
![Sélectionnez la solution suivante pare-feu génération][4]

5. La carte de **Point de vérification** s’ouvre et vous fournir des informations sur la solution partenaire. Sélectionnez **créer** dans la carte d’informations.
![Carte d’informations pare-feu][5]

6. La carte **machine virtuelle créer** s’ouvre. Vous pouvez saisir des informations requises pour commencer à tourner une machine virtuelle (machine virtuelle) qui continuera la Conviction. Suivez les étapes et fournir les informations Conviction requises. Sélectionnez OK pour appliquer.
![Créer des machines virtuelles pour exécuter Conviction][6]

## <a name="route-traffic-through-ngfw-only"></a>Acheminer le trafic via Conviction uniquement

Revenir à la carte de **recommandations** . Une nouvelle entrée a été générée une fois que vous avez ajouté une Conviction via le centre de sécurité, appelé **acheminer le trafic via Conviction uniquement**. Cette recommandation est créée uniquement si vous avez installé votre Conviction via le centre de sécurité. Si vous avez des points de terminaison via Internet, centre de sécurité recommandons de configurer des règles de groupe de sécurité réseau qui forcer le trafic entrant sur votre ordinateur virtuel via votre Conviction.

1. Dans la **carte de recommandations**, sélectionnez **rediriger le trafic via Conviction uniquement**.
![Acheminer le trafic via Conviction uniquement][7]

2. Cette action ouvre la carte **acheminer le trafic via Conviction uniquement** qui répertorie les machines virtuelles qui vous pouvez d’acheminer le trafic vers. Sélectionnez une machine virtuelle dans la liste.
![Sélectionnez une machine virtuelle][8]

3. Une carte de la machine virtuelle sélectionnée s’ouvre et affiche les règles de trafic entrant connexes. Une description vous offre plus d’informations sur les étapes suivantes possibles. Sélectionnez **Modifier les règles de trafic entrant** pour poursuivre la modification d’une règle de trafic entrant. L’objectif est que **Source** n’est pas définie sur **tous** pour les points de terminaison via Internet reliées par la Conviction. Pour en savoir plus sur les propriétés de la règle de trafic entrant, voir [règles NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Configurer des règles pour limiter l’accès][9]
![modifier une règle entrante][10]

## <a name="see-also"></a>Voir aussi

Ce document vous montrer comment mettre en œuvre la recommandation centre de sécurité « Ajouter un pare-feu génération suivant ». Pour en savoir plus sur NGFWs et la solution partenaire de Point de vérification, voir les rubriques suivantes :

- [Pare-feu nouvelle génération](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Point de vérification vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --rechercher blog sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
