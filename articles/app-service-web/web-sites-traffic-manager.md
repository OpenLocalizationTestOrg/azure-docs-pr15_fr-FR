<properties
    pageTitle="Contrôle web Azure application du trafic avec le Gestionnaire de trafic Azure"
    description="Cet article fournit des informations de synthèse pour le Gestionnaire de trafic Azure par rapport aux applications web Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Contrôle web Azure application du trafic avec le Gestionnaire de trafic Azure

> [AZURE.NOTE] Cet article fournit des informations de synthèse pour le Gestionnaire de trafic Microsoft Azure par rapport à Azure Application Service Web Apps. Vous trouverez plus d’informations sur le Gestionnaire de trafic Azure lui-même en consultant les liens à la fin de cet article.

## <a name="introduction"></a>Introduction
Vous pouvez utiliser le Gestionnaire de trafic Azure pour contrôler la répartition des demandes des clients web aux applications web dans le Service d’application Azure. Lorsque des points de terminaison de l’application web sont ajoutés à un profil Azure le trafic Manager, Azure le trafic gestionnaire le suivi conserve de l’état de vos applications web (en cours d’exécution, arrêté ou supprimés) afin que celle-ci puissiez décider lequel de ces points de terminaison devant recevoir le trafic.

## <a name="load-balancing-methods"></a>Méthodes d’équilibrage de charge
Gestionnaire de trafic Azure utilise trois méthodes équilibrage de charge différents. Celles-ci sont décrites dans la liste suivante en ce qui concerne aux applications web Azure.

* **Basculement**: Si vous avez web application clones dans différentes régions, vous pouvez utiliser cette méthode pour configurer une application web pour tout le trafic web client du service et configurer une autre application web dans une zone différente pour que le trafic du service en cas d’indisponibilité de l’application web première.

* **Fonction Round Robin**: Si vous avez web application clones dans différentes régions, vous pouvez utiliser cette méthode pour distribuer le trafic de manière égale dans les applications web dans différentes régions.

* **Performances**: méthode performances la répartit le trafic en fonction de la durée des boucles le plus court aux clients. La méthode de performances peut être utilisée pour les applications web au sein de la même région ou dans différentes régions.

##<a name="web-apps-and-traffic-manager-profiles"></a>Applications Web et les profils de trafic Manager
Pour configurer le contrôle du trafic d’application web, vous créez un profil dans Azure le trafic Manager utilise un des trois charge équilibrage de charge méthodes décrites précédemment et ajoutez les points de terminaison (dans ce cas, les applications web) pour laquelle vous souhaitez contrôler le trafic vers le profil. Votre statut de l’application web (en cours d’exécution, arrêté ou supprimés) est communiqué régulièrement au profil afin que le Gestionnaire de trafic Azure peut diriger le trafic en conséquence.

Lorsque vous utilisez le Gestionnaire de trafic Azure avec Azure, gardez à l’esprit les points suivants :

* Pour les déploiements uniquement d’application web au sein de la même région, Web Apps fournit déjà basculement et fonctionnalités cyclique sans tenir compte en mode web app.

* Pour les déploiements dans la même région qui utilisent des applications Web conjointement avec un autre service de nuage Azure, vous pouvez combiner les deux types de points de terminaison de scénarios hybride.

* Vous ne pouvez spécifier un point de terminaison de l’application web par région dans un profil. Lorsque vous sélectionnez une application web comme un point de terminaison d’une région, les applications web restant dans cette zone ne sont plus disponibles pour la sélection de ce profil.

* Les points de terminaison de l’application web que vous spécifiez dans un profil Azure le trafic Manager seront affichent sous la section **Noms de domaine** dans la page Configuration de l’application web dans le profil, mais ne pourra pas être configurés il.

* Après avoir ajouté une application web à un profil, l' **URL du Site** du tableau de bord de la page du portail de l’application web affiche l’URL de domaine personnalisé de l’application web si vous avez configuré un. Dans le cas contraire, il affiche l’URL du profil Gestionnaire de trafic (par exemple, `contoso.trafficmgr.com`). Les deux le nom de domaine directe de l’application web et l’URL du Gestionnaire de trafic sera visible sur la page de configuration de l’application web sous la section **Domain Names** .

* Les noms de votre domaine personnalisé fonctionne comme prévu, mais en plus de les ajouter à vos applications web, vous devez également configurer votre carte DNS pour qu’il pointe vers l’URL du Gestionnaire de trafic. Pour plus d’informations sur la façon de configurer un domaine personnalisé pour une application web Azure, voir [configuration d’un nom de domaine personnalisé pour un site web Azure](web-sites-custom-domain-name.md).

* Vous ne pouvez ajouter des applications web qui sont en mode standard pour un profil Azure le trafic Manager.

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation conceptuelle et technique du Gestionnaire de trafic Azure, voir [Présentation du Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md).

Pour plus d’informations sur l’utilisation du Gestionnaire de trafic avec Web Apps, consultez les que publications du blog de [l’Utilisation du Gestionnaire de trafic Azure avec les Sites Web Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) et [Azure le trafic Gestionnaire pouvez intégrer maintenant avec les Sites Web Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
