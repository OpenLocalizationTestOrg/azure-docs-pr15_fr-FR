<properties
    pageTitle="Utiliser les Services mobiles, quel est l’application de Service ?"
    description="Découvrez quels sont les avantages application Service apporte à vos projets de Services mobiles existants."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Utiliser les Services mobiles, quel est l’application de Service ?

##<a name="overview"></a>Vue d’ensemble
Votre Service Mobile existant est suffisamment et restera pris en charge. Cependant, il existe différentes avantages par le *Service d’application Azure* plate-forme fournit pour votre application mobile qui ne sont pas disponibles aujourd'hui avec les Services Mobile :

- Offre plus simple, plus simple et plus économique pour les applications qui incluent web et les clients mobiles
- Nouvelles fonctionnalités d’hôte, y compris des travaux Web, enregistrements CNAME personnalisé, une meilleure surveillance
- Intégration clé en main avec le Gestionnaire de trafic
- Connectivité à vos ressources locales et VPN à l’aide de VNet en plus de connexions hybride
- Surveillance, des alertes et résolution des problèmes de votre application à l’aide de NewRelic ou AppInsights
- Modes de sous-jacent plus riche sur Calculer les ressources et les prix
- Échelle automatique intégrée, l’équilibrage de charge et analyse des performances.
- Prédéfinis mise en attente, fonctions de sauvegarde, retour arrière et test de production

## <a name="new-hosting-features"></a>Nouvelles fonctionnalités d’hébergement
Dans le *Service d’application Azure* l' *Application Mobile* code principal s’exécute dans le même conteneur que Web App et API App. En tant que tel, vous pouvez tirer parti de toutes les fonctionnalités dans ce conteneur, y compris ceux qui ne sont pas actuellement présents dans les Services mobiles :

- Ajouter une logique de serveur principal s’exécute en permanence via des travaux Web
- Vérifiez que votre code principal est toujours en cours d’exécution
- Enregistrements CNAME personnalisé permet de fournir des noms stables et conviviales pour vos points de terminaison et mobile
- Geo-échelle votre application avec le Gestionnaire de trafic
- Inclure les bibliothèques et les packages souhaité.
- (Pour .NET) Tirer parti d’une fonctionnalité de ASP.NET, y compris MVC
- (Pour Node.js) Tirez parti de n’importe quelle bibliothèque JavaScript intégral de l’écosystème nœud, y compris les bibliothèques MVC courantes.

##<a name="access-on-premises-data-using-vnet"></a>Accès local à l’aide de VNet de données
Avec les Services Mobile aujourd'hui vous pouvez déjà utiliser hybride connexions pour accéder à des ressources locales. Il existe cependant des situations dans lesquelles une solution VPN est préférée. Avec le *Service d’application Azure* , vous pouvez utiliser VNet Azure pour votre code principal de l’application Mobile.

##<a name="use-your-favorite-backend-language"></a>Utiliser votre langue favorite principal
*Service d’application Azure* prend en charge plus riche et plus large plateformes ASP.NET et Node.js, y compris l’accès aux dernières exécutions.

##<a name="set-up-automatic-scale"></a>Configuration automatique de l’échelle
Avec les Services Mobile, toutes les instances de votre code serveur principal ont été en cours d’exécution sur machines virtuelles petite. *Azure Application Service* vous permet de sélectionner la taille des ordinateurs virtuels dans un jeu d’options beaucoup plus riche. Vous pouvez également rapidement évoluer vers le haut ou arrière pour gérer les charge client entrants, en fonction de diverses métriques de performances.

##<a name="be-in-the-know"></a>Dans le « savoir »
Réagir aux problèmes en temps réel avec analyse et les alertes pour vous informer automatiquement vous et votre équipe. Intégrer analytique application avancée et fonctionnalité d’analyse à partir de nouveau Relic et AppInsights obtenir encore plus riche claires les performances de votre application Mobile. Avec le *Service d’application Azure* vous pouvez maintenant configurer des alertes basées sur foule d’indicateurs de performance, par programme et via le portail Azure.

##<a name="keep-your-assets-safe"></a>Assurer la sécurité de vos ressources
Sauvegarder automatiquement votre serveur principal et la base de données. Votre code et les données sont protégées contre urgence et facilement restauré, ce qui vous permet de gérer votre entreprise en toute sécurité.

##<a name="ready-stage-go"></a>Êtes-vous prêt, étape, atteindre !
Avec le *Service d’application Azure* , vous pouvez désormais créer plusieurs tests privés et mise en environnements pour vos applications mobiles. Utiliser pour effectuer des tests avant de déployer. Remplacez en production sans interruption de service. Applications Web sont déjà chargées, intitulé vérification du service clientèle.

Vous pouvez obtenir Profitez de *Service d’application* pour votre Service Mobile existant en suivant ce [didacticiel](app-service-mobile-migrating-from-mobile-services.md).

