<properties
    pageTitle="Activer l’affinité Session à l’aide de la boîte à outils Azure pour Éclipse"
    description="Apprenez à activer l’affinité session à l’aide de la boîte à outils Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Activer l’affinité Session #

Dans la boîte à outils Azure pour Eclipse, vous pouvez activer HTTP session affinité, ou « sessions permanentes », pour vos rôles. L’image suivante montre les propriétés de **L’équilibrage de charge** boîte de dialogue utilisée pour activer la fonctionnalité affinité session :

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Pour activer utilisée session pour votre rôle ##

1. Avec le bouton droit le rôle dans l’Explorateur de projets de Eclipse et cliquez sur **Azure**, puis cliquez sur **L’équilibrage de charge**.
1. Dans la boîte de dialogue **Propriétés de l’équilibrage de charge WorkerRole1** :
    1. Vérifier **utilisée de session activer HTTP (sessions permanentes) pour ce rôle.**
    1. Pour **utiliser de point de terminaison d’entrée**, sélectionnez un point de terminaison d’entrée à utiliser, par exemple, **http (public : 80, privé : 8080)**. Votre application doit utiliser ce point de terminaison en tant que son point de terminaison HTTP. Vous pouvez activer plusieurs points de terminaison pour votre rôle, mais vous pouvez sélectionner un seul d'entre eux pour prendre en charge les sessions permanentes.
    1. Reconstruire votre application.

Une fois activé, si vous avez plusieurs instances de rôle, demandes HTTP provenant d’un client donné continuera géré par la même instance de rôle.

La boîte à outils Eclipse permet du faire l’installation d’un module IIS spécial appelé Application demander routage (ARR) dans chacune de vos instances de rôles. ARR redirige les demandes HTTP à l’instance de rôle approprié. La boîte à outils reconfigure automatiquement le point de terminaison sélectionné afin que le trafic HTTP entrant est d’abord routé vers le logiciel ARR. La boîte à outils crée également un point de terminaison interne que votre serveur Java est configuré pour écouter. C’est le point de terminaison utilisé par ARR pour rediriger le trafic HTTP à l’instance de rôle approprié. De cette façon, chaque instance de rôle dans votre déploiement plusieurs instances sert de proxy inverse pour tous les autres cas, l’activation de sessions permanentes.

## <a name="notes-about-session-affinity"></a>Remarques sur affinité session ##

* Affinité session ne fonctionne pas dans l’émulateur cluster. Les paramètres peuvent être appliqués dans l’émulateur cluster sans interférer avec votre processus de génération ou calculent l’exécution émulateur, mais la fonctionnalité proprement dite ne fonctionne pas dans l’émulateur cluster.
* L’activation de session affinité générera une augmentation de la quantité d’espace disque pris en charge par votre déploiement dans Azure, comme un logiciel supplémentaire sera être téléchargé et installé dans vos instances de rôles lorsque votre service est démarré dans le cloud Azure.
* Le temps d’initialisation chaque rôle prendra plus de temps.
* Un point de terminaison interne de fonctionner comme un rerouter le trafic comme indiqué ci-dessus, sera added.ss

Pour obtenir un exemple montrant comment mettre à jour les données de session lorsque l’affinité session est activée, Découvrez [comment mettre à jour les données de Session avec affinité Session][].

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Création d’une Application World Hello pour Azure dans Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

[Comment gérer les données de Session avec affinité Session][]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Comment gérer les données de Session avec affinité Session]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
