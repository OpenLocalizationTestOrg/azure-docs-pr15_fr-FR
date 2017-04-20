<properties
   pageTitle="Index de conseils techniques résilience | Microsoft Azure"
   description="Index d’articles techniques sur comprendre et conception résistante, hautement disponible, tolérance applications, ainsi que planification d’urgence récupération et continuité des activités"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Conseils techniques résilience Azure

##<a name="introduction"></a>Introduction

Disponibilité et en matière de restauration sinistre réunion requiert deux types de connaissances :

- Comprendre les fonctionnalités de la plate-forme cloud technique
- Savoir comment concevoir correctement un service distribué

Cette série d’articles couvre l’ancienne : les fonctionnalités et limitations de la plateforme Azure en ce qui concerne la résilience (parfois appelée continuité). Si vous êtes intéressé par ces derniers, voir la série d’articles axée sur [sinistre et disponibilité des applications Azure](https://aka.ms/drtechguide). Bien que cette série d’articles touche sur les modèles de conception et l’architecture, qui n’est pas le focus de la série. Pour des instructions de conception, vous pouvez consulter le document dans la section [des ressources supplémentaires](#additional-resources) .

Les informations sont organisées dans les articles suivants :

- [La récupération des défaillances locales](resiliency-technical-guidance-recovery-local-failures.md).
Matériel physique (par exemple, les lecteurs, serveurs et périphériques réseau) peut échouer. Ressources peuvent être atteint lors de la charge dépasse. Cet article décrit les fonctionnalités Azure fournit pour conserver la disponibilité dans ces conditions.

- [Récupération d’une interruption de service à l’échelle de la région Azure](resiliency-technical-guidance-recovery-loss-azure-region.md).
Échecs d’étendue sont rares, mais ils sont théoriquement possibles. Régions entières peuvent devenir isolées en raison des échecs de réseau ou qu’ils peuvent être physiquement endommagés d’urgence Natural Keyboard. Cet article explique comment utiliser Azure pour créer des applications qui s’étalent régions géographiquement.

- [Récupération locales à Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
Le nuage modifie considérablement la rentabilité de sinistre, permettant aux organisations de Azure permet d’établir un deuxième site pour la récupération. Vous pouvez le faire à une fraction du coût de création et maintenance d’un centre de données secondaire. Cet article décrit les fonctionnalités Azure fournit pour étendre un centre de données locales au cloud.

- [Récupération d’altération des données ou d’une suppression accidentelle](resiliency-technical-guidance-recovery-data-corruption.md).
Applications peuvent avoir des bogues ces données endommagées. Opérateurs peuvent incorrectement supprimer des données importantes. Cet article explique Azure fournit pour sauvegarder des données et la restauration à un point antérieur, il est temps.

##<a name="additional-resources"></a>Ressources supplémentaires

- [Reprise d’activité et la disponibilité des applications basées sur Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
Cet article est une présentation détaillée de la disponibilité et récupération d’urgence. Il recouvre le défi de la réplication manuelle référence et les données. Les sections finales fournissent des résumés des différents types de topologies de récupération d’urgence qui s’étalent sur Azure régions le plus haut niveau de disponibilité.

- [Liste de vérification de disponibilité](resiliency-high-availability-checklist.md).
Cet article fait partie d’une liste des fonctionnalités, services, et conceptions qui peuvent vous aider à augmentent la résilience et la disponibilité de votre application.

- [Aide à la résilience des services Microsoft Azure](resiliency-service-guidance-index.md).
Cet article est un index de services Azure et fournit des liens vers des instructions de récupération d’urgence et conseils de conception.

- [Vue d’ensemble : Cloud continuité et base de données reprise avec la base de données SQL](../sql-database/sql-database-business-continuity.md).
Cet article fournit des techniques de base de données SQL Azure pour la disponibilité. Elle centre principalement sur les stratégies de sauvegarde et de restauration. Si vous utilisez la base de données SQL Azure dans votre service cloud, vous devez examiner ce document et ses ressources connexes.

- [Disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
Cet article décrit les options de disponibilité que vous pouvez Explorer lorsque vous utilisez infrastructure en tant que service (IaaS) pour héberger vos services de base de données. Il traite des groupes de disponibilité AlwaysOn, la mise en miroir de base de données, d’envoi des journaux et sauvegarde et de restauration. Plusieurs didacticiels montrent comment utiliser ces techniques.

- [Pratiques recommandées pour concevoir des services à grande échelle sur Azure Cloud Services](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
Cet article se concentre sur le développement des architectures de cloud hautement scalable. La plupart des techniques que vous employez pour améliorer extensibilité élevées également améliorent la disponibilité. En outre, si votre application ne peut pas mettre à l’échelle sous charge accrue, extensibilité élevées devient un problème de disponibilité.

- [Sauvegarder et restaurer pour SQL Server dans le Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
Cet article fournit des conseils techniques sur la façon de sauvegarder et restaurer des ordinateurs sur Azure virtuels Microsoft SQL Server.

- [Sécurité intégrée : conseils pour des architectures de cloud résistant](https://channel9.msdn.com/Series/FailSafe).
Cet article fournit des conseils pour la création des architectures de cloud résistant, conseils pour l’implémentation ces architectures sur les technologies Microsoft et recettes pour l’implémentation de ces architectures pour des scénarios spécifiques.

- [Étude de cas technique : à l’aide des technologies de cloud pour améliorer le sinistre](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
Cette étude de cas montre comment Microsoft IT Azure pour améliorer la récupération d’urgence.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de technique aussi les instructions pour la résilience Azure. Si vous êtes intéressé par des autres articles dans cette série, vous pouvez commencer avec [la récupération des défaillances locales](resiliency-technical-guidance-recovery-local-failures.md).
