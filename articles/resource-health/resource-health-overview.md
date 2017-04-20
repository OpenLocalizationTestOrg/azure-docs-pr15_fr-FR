<properties
   pageTitle="Vue d’ensemble de santé ressource Azure | Microsoft Azure"
   description="Vue d’ensemble des santé des ressources Azure"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Vue d’ensemble de santé ressource Azure

Azure santé des ressources est un service qui expose l’état des ressources Azure et fournit des instructions pour résoudre les problèmes. Dans un environnement cloud où il n’est pas possible d’accéder directement aux serveurs ou des éléments de l’infrastructure, l’objectif d’intégrité de ressource consiste à réduire le temps clients sur la résolution des problèmes, notamment réduire le temps passé pour déterminer si la cause du problème fixe à l’intérieur de l’application ou si elle est due à un événement à l’intérieur de la plateforme Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>Ce qui est considéré comme une ressource et santé des ressources en quoi décide si la ressource est correcte ou non ? 
Une ressource est une instance créés par l’utilisateur d’un type de ressource fourni par un service, par exemple : une machine virtuelle, une application Web ou une base de données SQL. 

Santé des ressources repose sur signaux émis par la ressource et/ou le service pour déterminer si une ressource est correcte ou non. Il est important de vous pouvez remarquer qu’actuellement ressource santé seuls les comptes d’intégrité d’une ressource spécifique du type ne tient pas compte d’autres éléments qui peuvent contribuer au bon fonctionnement global. Par exemple, lors de la déclaration de l’état d’une machine virtuelle, uniquement la partie cluster de l’infrastructure est considérée comme, c'est-à-dire que problèmes dans le réseau n’apparaît pas dans santé des ressources, sauf s’il existe une interruption de service déclaré, auquel cas, il sera être exposé via la bannière en haut de la carte. Plus d’informations sur l’interruption de service sont proposés plus loin dans cet article. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Quelle est santé des ressources et tableau de bord d’intégrité du Service ?

Les informations fournies par santé des ressources sont plus de précision que ce qui est fourni par le tableau de bord intégrité du Service. Bien que SHD communique les événements qui ont une incidence sur la disponibilité d’un service dans une région, santé des ressources expose des informations pertinentes pour une ressource spécifique, par exemple, il va exposer les événements qui ont une incidence sur la disponibilité d’une machine virtuelle, une application web ou une base de données SQL. Par exemple, si un nœud redémarre inopinément, clients dont machines virtuelles ont été en cours d’exécution sur ce nœud seront en mesure d’obtenir la raison pour laquelle pourquoi leur machine virtuelle n’est pas disponible pour une période donnée.   

## <a name="how-to-access-resource-health"></a>Comment accéder aux santé des ressources
Pour les services disponibles via santé des ressources, il existe 2 façons d’accéder aux santé des ressources.

### <a name="azure-portal"></a>Portail Azure
La carte d’intégrité de ressources dans le portail Azure, fournit des informations détaillées sur l’état de la ressource ainsi que les actions qui varient en fonction de l’état actuel de la ressource recommandées. Cette carte fournit la meilleure expérience lors de l’interrogation santé des ressources, car il facilite l’accès à d’autres ressources à l’intérieur du portail. Comme mentionné précédemment, l’ensemble d’actions recommandées dans la carte de santé ressource peut varier selon l’état d’intégrité actuel :

* Ressources exact : dans la mesure où aucun problème peut avoir un impact sur l’état de la ressource n’a été détecté, les actions sont but d’aider le processus de dépannage. Par exemple, il fournit un accès direct à la carte de résolution des problèmes, qui offre des conseils sur la façon de résoudre la face de clients les problèmes courante.
* Ressource incorrecte : les problèmes créés par Azure, la carte affichera actions Microsoft exécute (ou a eu) pour récupérer la ressource. Pour les problèmes créés par l’utilisateur actions lancées, l’est carte une liste de clients actions peut prendre donc résoudre le problème et récupérer la ressource.  

Une fois que vous êtes connecté au portail Azure, il existe deux façons d’accéder à la carte d’intégrité de ressource : 

###<a name="open-the-resource-blade"></a>Ouvrir la carte de ressources
Ouvrez la carte de ressources pour une ressource donnée. Dans la carte de paramètres qui s’ouvre en regard de la carte de ressource, cliquez sur santé des ressources pour ouvrir la carte d’intégrité de ressource. 

![Carte d’intégrité de ressource](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Aide et carte de prise en charge
Ouvrez la carte aide et Support en cliquant sur le point d’interrogation dans le coin supérieur droit puis en sélectionnant aide + prise en charge. 

**Dans la barre de navigation supérieure**

![Aide + prise en charge](./media/resource-health-overview/HelpAndSupport.png)

Cliquez sur la vignette pour ouvrir la carte d’abonnement au niveau ressource qui permet de répertorier toutes les ressources dans votre abonnement. En regard de chaque ressource, il existe une icône indiquant son état. Cliquez sur chaque ressource, ouvrira la carte d’intégrité de ressource.

**Vignette d’intégrité de ressource**

![Vignette d’intégrité de ressource](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>Que signifie mon état d’intégrité des ressources ?
Il existe 4 statuts santé différents qui peut s’afficher pour la ressource.

### <a name="available"></a>Disponible
Le service n’a pas pu détecté des problèmes dans la plateforme qui pourrait être ayant un impact sur la disponibilité de la ressource. Cela est indiqué par une icône de coche verte. 

![Ressource est disponible](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non disponible

Dans ce cas le service a détecté un problème en cours dans la plateforme qui affecte la disponibilité de cette ressource, par exemple, le nœud exécutant la machine virtuelle redémarré de manière inattendue. Cela est indiqué par une icône d’avertissement rouge. Informations supplémentaires sur le problème sont fournies dans la section du milieu de la cuillère, y compris : 

1.  Quelles actions Microsoft est en train de récupérer la ressource 
2.  Une chronologie détaillée du problème, y compris l’heure de résolution prévue
3.  Une liste des actions pour les utilisateurs recommandée 

![Ressources ne sont pas disponible](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Non disponible – client initié
La ressource est indisponible en raison d’une demande de client telles que l’arrêt d’une ressource ou demander un redémarrage. Cela est indiqué par une icône d’information bleue. 

![Ressources ne sont pas disponible en raison d’une action créé par utilisateur](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Inconnu
Le service n’a pas reçu d’informations sur cette ressource pendant plus de 5 minutes. Cela est indiqué par une icône de point d’interrogation gris. 

Il est important de noter qu’il ne s’agit pas d’une indication définitive qu’il existe un problème au niveau d’une ressource, afin que les clients doivent suivre ces recommandations :

* Si la ressource fonctionne comme prévu, mais son état est défini sur inconnu dans santé des ressources, il n’y a aucun problème et vous pouvez attendre l’état de la ressource à mettre à jour vers exact après quelques minutes.
* Si il existe des problèmes d’accès à la ressource et son état sont définis sur inconnu dans santé des ressources, cela peut être une indication au plus tôt, il peut y avoir un problème et les investigations supplémentaires doivent être faites jusqu'à ce que l’état est mis à jour pour correcte ou incorrecte

![Santé des ressources est inconnue](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Service ayant un impact sur les événements
Si la ressource peut-être être affectée par un événement d’ayant un impact sur Service en cours, une bannière s’affichera en haut de la cuillère santé ressource. En cliquant sur la bannière s’ouvre la carte d’événements d’Audit, qui affiche plus d’informations sur l’interruption.

![Santé des ressources peut-être être affectée par un arrêt](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>Que dois-je dois-je connaître santé des ressources ?

### <a name="signal-latency"></a>Latence signal
Les signaux qui flux santé des ressources, peut être jusqu'à 15 min retardée, qui peuvent entraîner des différences entre l’état actuel de la ressource et sa disponibilité réelle. Il est important de maintenir cette esprit car elle permet d’éliminer le temps consacré inutilement chargées éventuels problèmes. 

### <a name="special-case-for-sql"></a>Cas spécial pour SQL 
Santé des ressources indique l’état de la base de données SQL, et non sur le serveur SQL. En accédant cet itinéraire fournit une image d’intégrité plus plausible, il nécessite que plusieurs composants et services prendre en considération pour déterminer l’état de la base de données. Le signal en cours s’appuie sur les connexions à la base de données, ce qui signifie que des bases de données qui reçoivent des connexions régulières (y compris entre autres, reçoit des demandes d’exécution de requête) l’état état régulièrement affichera. Si la base de données n’ont pas été utilisée pendant une période de 10 minutes ou plus, il est déplacé vers l’état inconnu. Cela ne signifie pas que la base de données est indisponible, seulement qu’aucun signal n’a été émis, car aucune connexion n’ont été effectuées. Se connecter à la base de données et l’exécution d’une requête émet les signaux nécessaires pour déterminer et mettre à jour de l’état d’intégrité de la base de données.

## <a name="feedback"></a>Commentaires
Nous sont toujours ouverts aux commentaires et suggestions ! Envoyez-nous vos [suggestions](https://feedback.azure.com/forums/266794-support-feedback). En outre, vous pouvez commencer avec contactez-nous via [Twitter](https://twitter.com/azuresupport) ou les [forums MSDN](https://social.msdn.microsoft.com/Forums/azure).
