<properties
    pageTitle="Les charges de travail vous protéger avec récupération de Site Azure ?"
    description="Récupération de Site Azure protège vos charges de travail et les applications en coordonner la réplication, le basculement et la restauration des machines virtuelles en local et des serveurs physiques vers Azure ou sur un site secondaire en local"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Les charges de travail vous protéger avec récupération de Site Azure ?


Cet article décrit les charges de travail et les applications que vous pouvez répliquer avec le service de récupération de Site Azure.

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une continuité et reprise récupération (BCDR) stratégie d’entreprise pour conserver les charges de travail et les données approuvés et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible.

Récupération de site est un service Azure qui participe à votre stratégie BCDR. Utilisation de la récupération de Site, vous pouvez déployer réplication d’application dans le cloud, ou vers un site secondaire. Si vos applications sont Windows ou basé sur Linux, en cours d’exécution sur des serveurs physiques, VMware ou Hyper-V, vous pouvez utiliser la récupération de Site pour organiser réplication, effectuer des tests de reprise et basculement exécution et de restauration.


Récupération de site s’intègre avec les applications Microsoft, y compris SharePoint, Exchange, Dynamics, SQL Server et Active Directory. Microsoft travaille également en étroite collaboration avec les fournisseurs, notamment Oracle, SAP, IBM et chapeau rouge. Vous pouvez personnaliser les solutions de réplication sur une base de l’application par application.

## <a name="why-use-site-recovery-for-application-replication"></a>Pourquoi utiliser récupération de Site pour la réplication de l’application ?

Récupération de site contribue à la protection des applications et de récupération comme suit :

- Application indépendant, fournir une réplication pour les charges de travail en cours d’exécution sur un ordinateur pris en charge.
- Réplication près synchrone, avec RPO jusqu'à 30 secondes pour répondre aux besoins des applications d’entreprise les plus importantes.
- Application cohérente des instantanés applications unique ou à plusieurs niveaux.
- Intégration à SQL Server AlwaysOn et partenariat avec d’autres technologies de réplication au niveau de l’application, y compris la réplication AD, AlwaysOn SQL, groupes de disponibilité de base de données Exchange (DAGs) et protection des données Oracle.
- Plans de récupération flexibles, qui vous permettent de récupérer une pile d’ensemble de l’application d’un simple clic et incluent pour inclure des scripts externes et actions manuelles dans le plan.
- Gestion de réseau avancée dans récupération de Site et Azure pour simplifier la configuration requise de réseau application, y compris la possibilité de réserver des adresses IP, configurer l’équilibrage de charge et l’intégration avec le Gestionnaire de trafic Azure, pour basculements de réseau faibles RTO.
-  Une bibliothèque automation enrichi qui propose des scripts opérationnelle, spécifiques à l’application qui peuvent être téléchargés et intégrées avec les plans de récupération.



## <a name="workload-summary"></a>Charge de travail Synthèse

Récupération de site pouvez répliquer n’importe quelle application en cours d’exécution sur un ordinateur pris en charge. En outre, nous avons associés à permet aux équipes de produit effectuer des essais supplémentaires spécifiques à l’application.

**Charge de travail** | **Répliquer machines virtuelles Hyper-V sur un site secondaire** | **Répliquer les machines virtuelles Hyper-V vers Azure** | **Répliquer les machines virtuelles VMware vers un site secondaire** | **Répliquer les machines virtuelles VMware vers Azure**
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
Applications Web (IIS, SQL) | Y | Y | Y | Y
System Center Operations Manager | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Site SAP répliquer Azure pour non cluster | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Exchange (non DAG) | Y | À venir | Y | Y
Bureau à distance/VDI | Y | Y | Y | N/A
Linux (système d’exploitation et applications) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | À venir | Y | À venir
Oracle | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Serveur de fichiers Windows | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Répliquer DNS et Active Directory

Une infrastructure DNS et Active Directory sont essentiels pour la plupart des applications d’entreprise. Pendant la restauration d’urgence, vous devez protéger et récupérer ces composants d’infrastructure, avant de récupérer vos charges de travail et les applications.

Vous pouvez utiliser la récupération de Site pour créer un plan de récupération automatique complète d’Active Directory et DNS. Par exemple, si vous voulez échouer sur SharePoint et SAP d’un serveur principal vers un site secondaire, vous pouvez configurer un plan de récupération bascule Active Directory tout d’abord, puis sur un plan de récupération spécifiques à l’application supplémentaire basculer sur les autres applications qui s’appuient sur Active Directory.

[En savoir plus](site-recovery-active-directory.md) sur la protection de Active Directory et DNS.

## <a name="protect-sql-server"></a>Protéger SQL Server

SQL Server fournit une base de services de données pour les services de données pour plusieurs applications métiers dans un centre de données locale.  Récupération de site peut servir avec les technologies SQL Server HA/DR, pour protéger des applications d’entreprise à plusieurs niveaux qui utilisent SQL Server. Récupération de site fournit :

- Une solution de récupération après sinistre simple et économique pour SQL Server. Répliquer plusieurs versions et les éditions de SQL Server autonome serveurs et de clusters, vers Azure ou sur un site secondaire.  
- Intégration des groupes de disponibilité AlwaysOn SQL, pour gérer le basculement et restauration avec plans de récupération de Site Azure.
- Plans de récupération de bout en bout pour les tous les niveaux dans une application, y compris les bases de données SQL Server.
- Mise à l’échelle de SQL Server pour pointe charge avec récupération de Site, par « rupture » dans les tailles de machine virtuelle IaaS plus grandes dans Azure.
- Tester aisément sinistre SQL Server. Vous pouvez exécuter basculement test pour analyser des données et exécuter les tests de conformité, sans ayant un impact sur votre environnement de production.

[En savoir plus](site-recovery-sql.md) sur la protection de SQL server.

##<a name="protect-sharepoint"></a>Protéger SharePoint

Azure récupération de Site permet de protéger les déploiements SharePoint, comme suit :

- Élimine la nécessité et les coûts d’infrastructure associé pour une batterie de serveurs en veille récupération d’urgence. Récupération de Site permet de répliquer une batterie de serveurs entière (couches Web, application et base de données) vers Azure ou sur un site secondaire.
- Simplifie la gestion et déploiement de l’application. Mises à jour déployées sur le site principal sont automatiquement répliquées et sont donc disponibles après basculement et la restauration d’une batterie de serveurs dans un site secondaire. Permet également de réduire la complexité de la gestion et les coûts liés aux tout en conservant une batterie de serveurs en veille à jour.
- Simplifie le développement d’applications SharePoint et le test en créant un environnement de copie à la demande de type production copie pour tester et déboguer.
- Simplifie la transition vers le cloud à l’aide de la récupération de Site pour migrer les déploiements SharePoint vers Azure.

[En savoir plus](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sur la protection de SharePoint.


## <a name="protect-dynamics-ax"></a>Protéger Dynamics AX

Azure récupération de Site permet de protéger votre solution Dynamics AX planification, par :

- Organiser la réplication de tout votre Dynamics AX environnement (couches Web et AOS, niveaux de base de données, SharePoint) pour Azure, ou vers un site secondaire.
- Simplification de la migration des déploiements Dynamics AX dans le cloud (Azure).
- Simplification de développement d’applications Dynamics AX et le test en créant une copie de type de production à la demande, pour tester et déboguer.

[En savoir plus](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sur la protection de Dynamics AX.

## <a name="protect-rds"></a>Protéger RDS

Bureau à distance Services () permet d’infrastructure virtual desktop infrastructure (VDI), les ordinateurs de bureau basés sur session et les applications, ce qui permet aux utilisateurs de travailler en tout lieu. Avec la récupération de Site Azure, vous pouvez :

- Répliquer bureaux virtuels pool géré ou sur un site secondaire et les applications à distance et les sessions sur un site secondaire ou sur Azure.
- Voici ce que vous pouvez répliquer :

**RDS** | **Répliquer machines virtuelles Hyper-V sur un site secondaire** | **Répliquer les machines virtuelles Hyper-V vers Azure** | **Répliquer les machines virtuelles VMware vers un site secondaire** | **Répliquer les machines virtuelles VMware vers Azure** | **Répliquer serveurs physiques sur un site secondaire** | **Dupliquer des serveurs physiques vers Azure**
---|---|---|---|---|---|---
**Bureau virtuel pool (non managée)** | Oui | N° | Oui | N° | Oui | N°
**Bureau virtuel pool (gérées et sans UDP)** | Oui | N° | Oui | N° | Oui | N°
**Applications à distance et les sessions Bureau (sans UDP)** | Oui | Oui | Oui | Oui | Oui | Oui


[En savoir plus](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sur la protection de RDS.


## <a name="protect-exchange"></a>Protéger Exchange

Récupération de site permet de protéger Exchange, comme suit :

- Pour les déploiements Exchange petites, par exemple un serveurs unique ou autonome, récupération de Site pouvez répliquer et basculer vers Azure ou sur un site secondaire.
- Pour les déploiements plus importants, récupération de Site s’intègre à Exchange DAGS.
- Exchange DAGs sont la solution recommandée pour reprise d’Exchange dans une entreprise.  Plans de récupération de site peuvent inclure DAGs, pour organiser DAG basculement entre les sites.


[En savoir plus](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sur la protection de Exchange.

## <a name="protect-sap"></a>Protéger SAP

Récupération de Site permet de protéger votre déploiement SAP, comme suit :

- Activer la protection de la totalité du déploiement SAP, la réplication de couches de déploiement différentes vers Azure, ou vers un site secondaire.
- Simplifier la migration de cloud, à l’aide de la récupération de Site pour migrer votre déploiement SAP vers Azure.
- Simplifier le développement SAP et test, copiez en créant un type de production à la demande pour tester et déboguer des applications.

[En savoir plus](http://aka.ms/asr-sap) sur la protection de SAP.

## <a name="next-steps"></a>Étapes suivantes

[Préparer pour le déploiement de récupération de Site](site-recovery-best-practices.md) 
