<properties
    pageTitle="Vue d’ensemble des échelle dans Machines virtuelles de Microsoft Azure, Cloud Services et applications Web | Microsoft Azure"
    description="Vue d’ensemble d’échelle dans Microsoft Azure. S’applique aux Machines virtuelles, les Services en nuage et les applications Web."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Vue d’ensemble des échelle dans Machines virtuelles de Microsoft Azure, Cloud Services et applications Web

Cet article décrit quels échelle Microsoft Azure, ses avantages et comment commencer à l’utiliser.  

Azure moniteur échelle automatique s’applique uniquement aux [Jeux d’échelle Machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Services Cloud](https://azure.microsoft.com/services/cloud-services/)et [Service d’application - Web Apps](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure comporte deux méthodes d’échelle. Une version antérieure d’échelle automatique s’applique aux Machines virtuelles (jeux de disponibilité). Prise en charge de cette fonctionnalité est limitée et nous vous recommandons de migration vers machine virtuelle échelle jeux pour la prise en charge de l’échelle plus rapide et plus fiable. Un lien sur la façon d’utiliser la technologie plus ancienne est inclus dans cet article.  


## <a name="what-is-autoscale"></a>Qu’est échelle automatique ?

Échelle permet que vous ayez la bonne quantité de ressources en cours d’exécution pour gérer la charge sur votre application. Il vous permet d’ajouter des ressources pour gérer les augmentations de charge et également économiser de l’argent en supprimant des ressources qui sont assis inactif. Vous spécifiez un nombre minimal et maximal d’instances d’exécuter et ajouter ou supprimer des machines virtuelles automatiquement selon un ensemble de règles. Ayant un effectue minimale que votre application est toujours en cours d’exécution même sous pas de charge. Dont au maximum les limites de votre coût total de possible toutes les heures. Vous ajuster automatiquement entre ces deux variées à l’aide de règles que vous créez.

 ![Échelle expliqué. Ajouter et supprimer des machines virtuelles](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Lorsque les conditions de règle sont remplies, une ou plusieurs actions échelle automatique se déclenche. Vous pouvez ajouter et supprimer des machines virtuelles ou effectuer d’autres actions. Le diagramme conceptuel suivant montre ce processus.  

 ![Diagramme de flux échelle conceptuelle](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>Processus d’échelle expliqués
Les explications suivantes s’appliquent aux éléments du diagramme précédent.   

### <a name="resource-metrics"></a>Indicateurs de ressources
Ressources émettent des mesures, qui sont traités ultérieurement par les règles. Indicateurs fournis par le biais des méthodes différentes.
Machine virtuelle échelle jeux utilise des données de télémétrie d’agents diagnostics de Windows Azure alors que télémétrie pour les applications Web et les services en nuage est fourni directement à partir de l’Infrastructure Azure. Certaines statistiques fréquemment utilisés incluent de l’UC, utilisation de la mémoire, le nombre de threads, file d’attente et l’utilisation du disque. Pour une liste des données de télémétrie, vous pouvez utiliser, voir [Métriques courantes de l’échelle](insights-autoscale-common-metrics.md).

### <a name="time"></a>Heure
Règles basées sur les prévisions sont basées sur UTC. Vous devez définir votre fuseau horaire correctement lors de la configuration de vos règles.  

### <a name="rules"></a>Règles
Le diagramme ne montre qu’une seule règle échelle, mais vous pouvez avoir bon nombre d'entre eux. Vous pouvez créer des règles qui se chevauchent complexes selon vos besoins selon votre situation.  Incluent des types de règles  

 - **En fonction de métrique** - par exemple, effectuer cette action lors de l’UC est supérieure à 50 %.
 - **Temporelles** - par exemple, déclencher un webhook chaque 8 am samedi dans un fuseau horaire donné.

Règles métrique mesurent la charge de l’application et ajouter ou supprimer des machines virtuelles en fonction de cette charge. Règles basées sur le planning vous permettent à l’échelle lorsque vous voir modèles d’heure dans votre charge et que vous souhaitez mettre à l’échelle avant une augmentation de charge possibles ou diminuer se produit.  


### <a name="actions-and-automation"></a>Actions et automatisation

Règles peuvent déclencher un ou plusieurs types d’actions.

- **Échelle** : échelle machines virtuelles ou l’arrière
- **Courrier électronique** - envoyer un e-mail aux administrateurs de l’abonnement, -coadministrateurs et/ou adresse de messagerie supplémentaires que vous spécifiez
- **Automatisez via webhooks** - appel webhooks, qui peut déclencher plusieurs actions complexes à l’intérieur ou à l’extérieur Azure. À l’intérieur d’Azure, vous pouvez démarrer un runbook Azure automatisation, fonction Azure ou Azure logique d’application. Exemple 3e partie URL en dehors d’Azure inclure des services tels que la marge et Twilio.


## <a name="autoscale-settings"></a>Paramètres d’échelle
Échelle utiliser la terminologie et la structure suivante.

- Un **paramètre d’échelle** est lu par le moteur d’échelle pour déterminer s’il faut mettre à l’échelle vers le haut ou vers le bas. Il contient un ou plusieurs profils, informations sur la ressource cible et les paramètres de notification.
    - Un **profil échelle automatique** est une combinaison d’une capacité définition, un ensemble de règles concernant les déclencheurs et les actions d’échelle pour le profil et une périodicité. Vous pouvez avoir plusieurs profils, ce qui vous permettent de prendre en charge des exigences différentes qui se chevauchent.
        - Un **paramètre de capacité** indique les champs minimum, maximum et valeurs par défaut pour le nombre d’instances. [emplacement approprié pour utiliser Fig. 1]
        - Une **règle** inclut un déclencheur — un déclencheur métrique ou un déclencheur d’heure et une action échelle, indiquant si échelle doit augmentée ou diminuée lorsque cette règle est satisfaite.
        - Une **périodicité** signifie lorsque échelle doit placer ce profil en vigueur. Vous pouvez avoir échelle différents profils des moments d’ou les jours de la semaine, par exemple.
- Un **paramètre de notification** définit quelles notifications doivent se produit lorsqu’un événement échelle automatique intervient selon répondant aux critères de l’une des profils du paramètre échelle automatique. Échelle peut signaler une ou plusieurs adresses de messagerie ou passer des appels vers un ou plusieurs webhooks.

![Paramètre d’échelle Azure, profil et structure de règle](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

La liste complète des champs configurables et les descriptions est disponible dans l' [API REST échelle automatique](https://msdn.microsoft.com/library/dn931928.aspx).

Pour plus d’exemples de code, consultez

* [Configuration échelle avancée à l’aide de modèles Gestionnaire de ressources pour les jeux d’échelle machine virtuelle](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [API REST échelle automatique](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Échelle verticale et horizontale

Échelle augmente ressources dans prise en charge allant horizontalement, qui est une augmentation (« out ») ou diminuer le nombre d’instances de machine virtuelle (« in »).  Évolution horizontale, qui est plus flexible dans une situation cloud, car il vous autorise à exécuter potentiellement des milliers de machines virtuelles pour gérer la charge. Mise à l’échelle verticale est différente. Il conserve le même nombre de machines virtuelles, mais rend la machine virtuelle (« suivi ») ou inférieur (« bas ») puissants. Power est mesuré en mémoire, vitesse du processeur, espace disque, etc..  Échelle verticale comporte des limitations plus. Il est dépendant de la disponibilité de matériel plus grand, ce qui peut varier par région et accès rapidement et limite supérieure. Échelle verticale requiert également généralement un taquet machine virtuelle et un démarrage. Pour plus d’informations, voir [évolution verticale Azure machine virtuelle avec Azure Automation](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## <a name="methods-of-access"></a>Méthodes d’accès
Vous pouvez configurer échelle via

- [Portail Azure](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Disponibilité sur plusieurs plateformes Interface de ligne de commande (CLI)](insights-cli-samples.md#autoscale )
- [API REST moniteur Azure](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Services pris en charge pour l’échelle


| Service                              | Schéma et documents                                       |
|--------------------------------------|-----------------------------------------------------|
| Applications Web                             | [Mise à l’échelle des applications Web](insights-how-to-scale.md)              |
| Services en nuage                       | [Échelle un Service Cloud](../cloud-services/cloud-services-how-to-scale.md) |
| Machines virtuelles : classique           | [Mise à l’échelle des ensembles de disponibilité Machine virtuelle classique](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Machines virtuelles : Windows échelle jeux| [Mise à l’échelle échelle machine virtuelle définit dans Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Machines virtuelles : Échelle Linux définit  | [Mise à l’échelle échelle machine virtuelle définit dans Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Machines virtuelles : Exemple de Windows   | [Configuration échelle avancée à l’aide de modèles Gestionnaire de ressources pour les jeux d’échelle machine virtuelle](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur échelle, utilisez les Autoscale Walkthroughs répertoriés précédemment ou reportez-vous aux ressources suivantes :

- [Mesures courantes Azure moniteur échelle automatique](insights-autoscale-common-metrics.md)
- [Meilleures pratiques pour analyseur Azure échelle](insights-autoscale-best-practices.md)
- [Utiliser les actions d’échelle pour envoyer des messages électroniques et webhook notifications](insights-autoscale-to-webhook-email.md)
- [API REST échelle automatique](https://msdn.microsoft.com/library/dn931953.aspx)
- [Dépannage Machine virtuelle échelle jeux échelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
