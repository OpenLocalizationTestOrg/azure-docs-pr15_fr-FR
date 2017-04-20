<properties
 pageTitle="Quel est le planificateur Azure ? | Microsoft Azure"
 description="Planificateur Azure permet de manière déclarative décrire actions à exécuter dans le cloud. Il vous permet de planifier et exécute automatiquement ces actions."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>Quel est le planificateur Azure ?

Planificateur Azure permet de manière déclarative décrire actions à exécuter dans le cloud. Il vous permet de planifier et exécute automatiquement ces actions.  Planificateur de cela, à l’aide [du portail Azure](scheduler-get-started-portal.md), code, [API REST](https://msdn.microsoft.com/library/mt629143.aspx)ou PowerShell Azure.

Planificateur crée, met à jour et appelle le travail prévu.  Planificateur de ne pas héberger les charges de travail ou l’exécution de code. Informatique seul le code _appelle_ hébergé ailleurs — dans Azure, en local, ou avec un autre fournisseur. Il appelle via HTTP, HTTPS, une file d’attente de stockage, un service bus file d’attente ou une rubrique bus de service.

Planificateur planifie les [tâches](scheduler-concepts-terms.md), conserve un historique des résultats de l’exécution des travaux une pouvez passer en revue et de façon déterministe et fiable planifie les charges de travail à exécuter. Azure WebJobs (partie de la fonctionnalité d’applications Web dans le Service d’application Azure) et autres fonctionnalités de planification de Azure utilisent le planificateur en arrière-plan. L' [API REST Scheduler](https://msdn.microsoft.com/library/mt629143.aspx) permet de gérer la communication pour ces actions. Dès lors, planificateur prend en charge [des planifications complexes et leur périodicité avancée](scheduler-advanced-complexity.md) facilement.

Il existe plusieurs scénarios qui se prêtent à l’utilisation du planificateur. Par exemple :

+ _Actions d’application périodique :_ Régulièrement collecte des données de Twitter dans un flux.
+ _Maintenance quotidienne :_ Nettoyage quotidien des journaux, effectuer des sauvegardes et autres tâches de maintenance. Par exemple, un administrateur peut choisir sauvegarder la base de données à 1:00. tous les jours pour les neuf mois suivantes.

Planificateur permet de créer, mettre à jour, supprimer, afficher et gérer [des collections de travail](scheduler-concepts-terms.md) et les travaux par programme, à l’aide des scripts et dans le portail.

## <a name="see-also"></a>Voir aussi

 [Azure Scheduler concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Découvrez comment créer des planifications complexes et leur périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler Azure](scheduler-powershell-reference.md)

 [Azure Scheduler disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et codes d’erreur](scheduler-limits-defaults-errors.md)

 [Authentification sortante Scheduler Azure](scheduler-outbound-authentication.md)
