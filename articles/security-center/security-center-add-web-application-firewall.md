<properties
   pageTitle="Ajouter un pare-feu d’application web dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre les recommandations centre de sécurité Azure **Ajouter un pare-feu d’application web** et **protection des applications Finalize**."
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

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Ajouter un pare-feu d’application web dans le centre de sécurité Azure

Centre de sécurité Azure peut recommander ajouter un pare-feu d’application web (WAF) d’un partenaire Microsoft pour assurer la sécurité de vos applications web. Ce document vous présente un exemple de cette procédure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **sécuriser application web à l’aide de pare-feu pour applications web**.
![Sécuriser des applications web][1]

2. Dans la carte **Sécurisez vos applications web à l’aide de pare-feu pour applications web** , sélectionnez une application web. La carte **Ajouter un pare-feu d’Application Web** s’ouvre.
![Ajouter un pare-feu d’application web][2]
3. Vous pouvez choisir d’utiliser un pare-feu d’application web existant, le cas échéant, ou vous pouvez créer un autre. Dans cet exemple ne sont aucun WAFs existants disponibles afin que nous allons créer une nouvelle WAF.

4. Pour créer un nouveau WAF, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous allons sélectionner **Barracuda Web Application pare-feu**.
5. La carte de **Pare-feu pour applications Web Barracuda** s’ouvre et vous fournir des informations sur la solution partenaire. Sélectionnez **créer** dans la carte d’informations.
![Carte d’informations pare-feu][3]

6. La carte de **Nouveau pare-feu Application Web** s’ouvre, où vous pouvez effectuer les étapes de **Configuration de la machine virtuelle** et fournissent des **Informations WAF**. Sélectionnez **Configuration machine virtuelle**.

7. Dans la carte de **Configuration de machines virtuelles** que vous entrez les informations requises pour commencer à tourner la machine virtuelle qui continuera la WAF.
![Configuration de machines virtuelles][4]
8. Revenir à la carte de **Pare-feu pour applications Web nouveau** et sélectionnez **Informations WAF**. Dans la carte **WAF informations** vous configurez le WAF lui-même. Étape 7 permet de configurer l’ordinateur virtuel sur lequel le WAF s’exécutera et étape 8 vous permet de mettre en service la WAF lui-même.

## <a name="finalize-application-protection"></a>Finalisation de la protection d’application

1. Revenir à la carte de **recommandations** . Une nouvelle entrée a été générée une fois que vous avez créé la WAF appelée **Finalize application protection**. Cette entrée vous avertit que vous devez terminer le processus de réellement mettre en place la WAF au sein du réseau virtuel Azure afin qu’il peut protéger l’application.
![Finalisation de la protection d’application][5]

2. Sélectionner une **protection par application Finalize**. Une nouvelle carte s’ouvre. Vous pouvez voir qu’il existe une application web qui doit comporter le trafic redirigé.
3. Sélectionnez l’application web. Une carte s’ouvre et vous propose des étapes pour finaliser la configuration de pare-feu d’application web. Suivez les étapes, puis sélectionnez **limiter le trafic**. Centre de sécurité effectuera ensuite la câblage à distance pour vous.
![Limiter le trafic][6]

> [AZURE.NOTE] Vous pouvez protéger plusieurs applications web dans le centre de sécurité en ajoutant ces applications à votre des déploiements WAF existants. Appareils WAF (créés avec le modèle de déploiement du Gestionnaire de ressources) doivent être déployés à un réseau virtuel distinct. Appareils WAF (créés avec le modèle de déploiement classique) sont limités à l’aide d’un groupe de sécurité réseau. Cette prise en charge sera étendu à un déploiement entièrement personnalisé d’un appareil WAF (classique) à l’avenir. En savoir plus sur [classique et modèles de déploiement Gestionnaire de ressources](../azure-classic-rm.md) pour les ressources Azure.

Les journaux de cette WAF sont désormais entièrement intégrées. Centre de sécurité peut démarrer automatiquement collecte et analyse les journaux afin qu’il peut faire apparaître les alertes de sécurité importantes pour vous.

## <a name="see-also"></a>Voir aussi

Ce document vous montrer comment mettre en œuvre la recommandation centre de sécurité « Ajouter une application web ». Pour plus d’informations sur la configuration d’un pare-feu pour applications web, voir les rubriques suivantes :

- [Configuration d’un pare-feu d’Application Web (WAF) pour un environnement de Service d’application](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --rechercher blog sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
