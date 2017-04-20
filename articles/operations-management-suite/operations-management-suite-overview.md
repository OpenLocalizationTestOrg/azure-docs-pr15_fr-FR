<properties
   pageTitle="Vue d’ensemble de la gestion des Suite (OMS) opérations | Microsoft Azure"
   description="Suite de gestion des opérations Microsoft (OMS) est sur le nuage informatique solution de gestion qui vous permet de gérer et protéger vos localement et infrastructure en nuage. de Microsoft  Cet article identifie les différents services inclus dans OMS et fournit des liens vers leur description détaillée du contenu."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>Quelle est la Suite de gestion des opérations (OMS) ?

Suite de gestion des opérations Microsoft (OMS) est sur le nuage informatique solution de gestion qui vous permet de gérer et protéger vos localement et infrastructure en nuage. de Microsoft  Dans la mesure où OMS est implémentée comme un service basé sur le cloud, vous pouvez avoir il opérationnel rapidement avec un investissement minimal dans les services d’infrastructure.  Nouvelles fonctionnalités sont remies automatiquement, ce qui vous évite maintenance continue et les coûts de mise à niveau.

En plus de fournir des services précieux dans sa propre, OMS pouvez intégrer avec des composants de System Center tels que System Center Operations Manager pour étendre votre investissements de gestion existants dans le cloud.  System Center et OMS peuvent fonctionner ensemble afin de fournir une expérience de gestion hybride.

Les sections suivantes fournissent une description détaillée des zones valeur différente de OMS et les services qui les mettre en œuvre.  Vous pouvez faire référence à l’architecture OMS pour avoir un aperçu des différents composants OMS avant d’examiner la documentation détaillée pour chacune d’elles.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Un aperçu et Analytique](media/operations-management-suite-overview/icon-insight-analytics.png) Un aperçu et Analytique

[Journal Analytique](http://azure.microsoft.com/documentation/services/log-analytics) vous permet de collecter, corrélation, rechercher et agir sur les données de performance et de journal générées par les systèmes d’exploitation et les applications. Il vous donne des perspectives opérationnelles en temps réel à l’aide de la recherche intégrée et tableaux de bord personnalisés pour analyser aisément des millions d’enregistrements dans l’ensemble de vos charges de travail et les serveurs quel que soit leur emplacement physique.

Vous pouvez facilement ajouter solutions Analytique journal définissant les données à collecter et la logique de son analyse.  Les solutions peuvent inclure des fonctionnalités supplémentaires qui sont automatiquement remies à agents avec un minimum ou aucune configuration.  Outre l’utilisation des outils d’analyse fournies par les solutions individuelles, vous pouvez effectuer des recherches personnalisés au sein de l’ensemble du dataset pour faire correspondre des données entre des systèmes et applications.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Le contrôle et automatisation](media/operations-management-suite-overview/icon-automation-control.png) Le contrôle et automatisation

Automatisation Azure automatisation des processus d’administration avec les [procédures opérationnelles](../automation/automation-runbook-types.md) basé sur PowerShell, exécutez dans le cloud Azure.  Procédures opérationnelles peut accéder à n’importe quel produit ou service qui peut être géré avec PowerShell, y compris les ressources dans d’autres nuages tels que Amazon Web Services (AWS).  Procédures opérationnelles peut également être exécuté sur un serveur dans votre centre de données locales pour gérer les ressources locales.

Automatisation Azure fournit la gestion de la configuration avec [PowerShell DSC](../automation/automation-dsc-overview.md).  Vous pouvez créer et gérer les ressources DSC hébergés dans Azure et les appliquer aux systèmes cloud et en local pour définir et appliquer automatiquement leur configuration.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protection et restauration](media/operations-management-suite-overview/icon-protection-recovery.png) La protection et récupération d’urgence

[Sauvegarde Azure](http://azure.microsoft.com/documentation/services/backup) protège les données de vos applications et conserve des années sans un investissement de capitaux et avec les coûts d’exploitation minimales.  Sauvegarder des données à partir de serveurs Windows physiques et virtuels en plus de charges d’applications telles que SQL Server et SharePoint.  Il peut également être utilisé par System Center Data Protection Manager (DPM) pour dupliquer des données protégées à Azure de redondance et de stockage à long terme.

[Récupération de Site Azure](http://azure.microsoft.com/documentation/services/site-recovery) contribue à votre continuité des activités et une stratégie de rétablissement (BCDR) par organiser réplication, basculement et la restauration des machines virtuelles de Hyper-V en local, machines virtuelles VMware et les serveurs Windows/Linux physiques. Vous pouvez répliquer machines dans un centre de données secondaire ou étendre votre centre de données en les répliquer sur Azure. Récupération de site fournit également un basculement simple et récupération pour les charges de travail. Il s’intègre à mécanismes de récupération de sinistre telles que SQL Server AlwaysOn et fournit les plans de récupération pour le basculement facile des charges de travail qui sont hiérarchisé sur plusieurs ordinateurs.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS sécurité et conformité](media/operations-management-suite-overview/icon-security-compliance.png) Sécurité et conformité
Sécurité et conformité permet d’identifier, évaluer et réduire les risques de sécurité pour votre infrastructure.  Ces fonctionnalités de OMS sont implémentées par plusieurs solutions dans journal Analytique qui analyser les données du journal et la configuration des systèmes de l’agent pour vous aider à assurer la sécurité de votre environnement en cours.

- La [solution de sécurité et d’Audit](oms-security-getting-started.md ) collecte et analyse des événements de sécurité sur les systèmes gérés à identifier l’activité suspecte.
- La [solution contre les logiciels malveillants](log-analytics-malware.md ) des rapports sur l’état de la protection contre les logiciels malveillants sur les systèmes gérés.  
- La solution mises à jour système effectue une analyse les mises à jour de sécurité et autres mises à jour sur les systèmes gérés afin que vous identifier facilement des systèmes nécessitant une correction.


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [journal Analytique](http://azure.microsoft.com/documentation/services/log-analytics).
- En savoir plus sur [automatisation Azure](../automation/automation-intro.md).
- Obtenir des informations relatives à la [sauvegarde Azure](http://azure.microsoft.com/documentation/services/backup).
- Obtenir des informations sur la [récupération de Site Azure](http://azure.microsoft.com/documentation/services/site-recovery).
