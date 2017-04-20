
<properties
   pageTitle="Conseils Azure | modèles et pratiques | Microsoft Azure"
   description="Meilleures pratiques et conseils pour Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Conseils Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

L’équipe Microsoft modèles et pratiques fait partie de l’équipe de conseil clientèle Azure. Notre objectif est d’aider les développeurs, architectes et professionnels de l’informatique aboutisse sur la plateforme Microsoft Azure. Nous développons conseils qui présente les pratiques recommandées pour créer des solutions de cloud sur Azure.

## <a name="checklists"></a>Aide-mémoire

Ces listes sont référence rapide pour l’examen des principaux aspects de la disponibilité et extensibilité élevées. 

- [Liste de vérification de disponibilité][AvailabilityChecklist] 

    Un résumé des pratiques recommandées pour garantir la disponibilité.

- [Liste de vérification extensibilité élevées][ScalabilityChecklist]

    Un résumé des pratiques recommandées pour la conception et implémentation des services de fichiers et gestion de gestion des données.

## <a name="best-practices-articles"></a>Meilleures pratiques articles

Les articles suivants fournissent une présentation détaillée des concepts importants généralement associé au cloud computing. 

- [Conception de l’API][APIDesign] 

    Explication des problèmes de conception à prendre en considération lorsque vous créez un site web API.

- [Implémentation de l’API][APIImplementation] 

    Un ensemble de pratiques recommandées pour la mise en œuvre et publier un site web API.

- [Conseils de sécurité de l’API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    Explication des problèmes d’authentification et d’autorisation (par exemple, types de jetons, protocoles d’autorisation, flux d’autorisation et atténuation des menaces).

- [Conseils AutoScaling][AutoscalingGuidance] 

    Un résumé des considérations pour suivre l’évolution des solutions sans la nécessité d’intervention manuelle.

- [Conseils de travaux d’arrière-plan][BackgroundJobsGuidance] 

    Une description des options disponibles et des pratiques recommandées pour l’implémentation des tâches qui doivent être effectuées à l’arrière-plan, indépendamment de tout premier plan ou d’opérations interactives.

- [Contenu conseils de remise réseau (CDN)][CDNGuidance] 

    Instructions générales fournies pour et pratique recommandée pour l’utilisation du CDN pour réduire la charge sur vos applications et optimiser la disponibilité et les performances.

- [Guide de mise en cache][CachingGuidance] 

    Un résumé de la façon d’utiliser la mise en cache pour améliorer les performances et extensibilité élevées d’un système.

- [Conseils de partitionnement de données][DataPartitioningGuidance]

    Stratégies que vous pouvez utiliser pour les données partition pour améliorer extensibilité élevées, réduire les conflits et optimiser les performances.

- [Guide de surveillance et de diagnostic][MonitoringandDiagnosticsGuidance] 

    Conseils sur l’utilisant de vos utilisateurs sur votre système, du suivi repérer l’utilisation des ressources et généralement surveiller l’intégrité et les performances de votre système.

- [Conventions d’appellation recommandées][naming-conventions] 

    Conventions d’appellation recommandées pour les ressources Azure.

- [Réessayer conseils généraux][RetryGeneralGuidance] 

    Discussion concepts généraux pour la gestion des défaillances temporaires.

- [Réessayer recommandations spécifiques au Service][RetryServiceSpecificGuidance]

    Un résumé des fonctionnalités de nouvelle tentative pour la plupart des services Azure, y compris des informations pour vous aider à utiliser, adapter ou étendre le mécanisme de nouvelle tentative pour ce service.

## <a name="scenario-guides"></a>Guides de scénario

- [Elasticsearch en cours d’exécution sur Azure][elasticsearch] 
    
    Elasticsearch est un moteur de recherche open source hautement scalaire et la base de données. Il est adapté aux situations qui nécessitent une analyse rapide et découverte des informations contenues dans les grands ensembles de données. Ce guide examine certains aspects essentiels à prendre en considération lorsque vous créez un cluster Elasticsearch.

- [Gestion des identités pour les applications partagées][identity-multitenant] 
    
    Donc le partage est une architecture qui plusieurs clients partagent une application, mais ne soient isolées les unes des autres. Ce guide vous explique comment gérer les identités utilisateur dans une application partagée, à l’aide [d’Azure Active Directory] [ AzureAD] pour gérer la connexion et l’authentification.
    
- [Développez des solutions de données volumineux](https://msdn.microsoft.com/library/dn749874.aspx)

    Ce guide présente l’utilisation de HDInsight pour des scénarios tels qu’exploration itérative, comme un data warehouse, de processus ETL et l’intégration des systèmes BI existants. Il inclut également des conseils pour comprendre les concepts de données volumineux, de planification et de conception de solutions de données volumineux et implémenter ces solutions.
    
## <a name="patterns"></a>Modèles

- [Modèles de conception cloud : Architecture des conseils pour les Applications Cloud](https://msdn.microsoft.com/library/dn568099.aspx)

    Modèles de conception cloud est une bibliothèque de modèles de conception et rubriques connexes. Il expose l’avantage de l’application de modèles en montrant comment chaque élément pouvant être affiché dans architectures d’applications cloud.
    
- [Optimisation des performances des Applications en nuage](https://github.com/mspnp/performance-optimization)

    Ce guide est une exploration de modèles anti-courrier courants qui empêchent des applications à partir de la mise à l’échelle en charge. Il inclut des exemples qui illustrent des motifs contre huit et [primer d’analyse de performances](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) et un guide pour [évaluer les performances par rapport aux mesures clés](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Architectures de référence

Notre architectures de référence sont organisés par scénario.
Chaque architecture individuel propose des pratiques recommandées et des instructions et un composant exécutable représentant les recommandations.

La bibliothèque actuelle des architectures de référence est disponible sur [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Conseils supplémentaires sur la résilience

Les rubriques suivantes expliquent comment concevoir des applications qui sont résistants aux défaillances dans un environnement cloud distribué.   

- [Vue d’ensemble de la résilience][ResiliencyOvervew]

     Découvrez comment créer des applications sur la plateforme Azure qui peuvent récupérer les échecs et continue à fonctionner. Décrit une approche structurée pour obtenir la résilience, de la conception à l’implémentation, le déploiement et opérations.

- [Liste de vérification de la résilience][resiliency-checklist]

    Une liste de vérification des recommandations qui vous aideront à planifier une variété de modes de panne qui peut se produire.

- [Analyse de mode de défaillance][resiliency-fma] 

    Échec du mode analyse (FMA) est un processus d’intégration de la résistance à un système, en identifiant les points de défaillance possibles. Comme point de départ pour votre processus FMA, cet article contient un catalogue des modes échec potentiels et leur minimisation. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

