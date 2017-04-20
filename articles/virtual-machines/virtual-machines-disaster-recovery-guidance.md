<properties
    pageTitle="Procédure à suivre dans le cas où une interruption de service Azure a un impact sur machines virtuelles Azure | Microsoft Azure"
    description="Découvrez comment procéder au cas où une interruption de service Azure a un impact sur Azure machines virtuelles."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>Procédure à suivre dans le cas où une interruption de service Azure a un impact sur machines virtuelles Azure

Chez Microsoft, nous nous efforçons pour vous assurer que nos services sont toujours disponibles lorsque vous en avez besoin. Force au-delà de notre contrôle parfois avoir un impact sur nous d’une manière qui entraîner des interruptions de service non planifiée.

Microsoft fournit un contrat de niveau de Service (SLA) pour ses services comme un engagement pour la connectivité et de disponibilité. Vous trouverez le SLA pour des services Azure individuels à [Contrats de niveau de Service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure déjà comporte de nombreuses fonctionnalités de plateforme intégrée qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, lisez [sinistre et disponibilité des applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article traite un scénario de récupération urgence true, lorsqu’une région entière rencontre une panne en raison de sinistre naturel majeur ou d’interruption de service étendu. Il s’agit des rares occurrences, mais vous devez préparer la possibilité d’une panne d’une région entière. Si une région entière rencontre une interruption de service, les copies de vos données redondantes localement serait temporairement indisponibles. Si vous avez activé la réplication geo, trois copies supplémentaires de votre stockage Azure BLOB et les tables sont stockées dans une zone différente. En cas de panne régionale de complète ou un incident dans lequel la région principale n’est pas récupérable, Azure reconfigure toutes les entrées DNS à la région répliquées geo.

>[AZURE.NOTE]N’oubliez pas que vous n’avez pas de n’importe quel contrôle sur ce processus, et il se produit uniquement pour les interruptions de service à l’échelle de la région. Pour cette raison, vous devez également compter sur d’autres stratégies de sauvegarde spécifiques à l’application pour atteindre le niveau le plus élevé de disponibilité. Pour plus d’informations, consultez la section des [stratégies de données pour la reprise](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Pour vous aider à gérer ces rares occurrences, nous fournissons les recommandations suivantes Azure machines virtuelles dans le cas d’une interruption de service de l’ensemble de la région dans lequel votre application Azure machine virtuelle est déployée.

##<a name="option-1-wait-for-recovery"></a>Option 1 : Attendre la récupération
Dans ce cas, aucune action de votre part est requise. Savoir que nous travaillons assidûment pour restaurer la disponibilité du service. Vous pouvez voir l’état actuel du service sur notre [Tableau de bord au niveau de Service Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Il s’agit de la meilleure option si vous n’avez pas défini récupération de Site Azure, sauvegarde des machines virtuelles, stockage geo redondants accès en lecture ou stockage geo redondantes avant l’interruption. Si vous avez configuré stockage geo redondantes ou stockage geo redondants accès en lecture pour le compte de stockage où se trouvent vos disques durs virtual machine virtuelle (disques durs virtuels), vous pouvez rechercher pour récupérer l’image de base dur et réessayer de mise en service un nouvel ordinateur virtuel à partir de celui-ci. Ce n’est pas une option par défaut, car il n’existe aucune garantie de synchronisation des données, à moins que machine virtuelle Azure sauvegarde ou récupération de Site Azure sont utilisés. Par conséquent, cette option n’est pas garantie pour l’utiliser.

Pour les clients qui souhaitent un accès immédiat aux machines virtuelles, les deux options suivantes sont disponibles.  

>[AZURE.NOTE]N’oubliez pas que les deux options suivantes ont la possibilité d’une perte de données.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Option 2 : Restaurer une machine virtuelle à partir d’une sauvegarde
Pour les clients qui ont configuré une sauvegarde machine virtuelle, vous pouvez restaurer la machine virtuelle à partir de son point de sauvegarde et de restauration.

Pour restaurer une machine virtuelle à partir d’Azure sauvegarde, voir [restaurer machines virtuelles dans Azure](../backup/backup-azure-restore-vms.md).

Pour vous aider à planifier votre infrastructure de sauvegarde Azure machines virtuelles, voir [planifier votre infrastructure de sauvegarde machine virtuelle dans Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Option 3 : Initier un basculement à l’aide de récupération de Site Azure
Si vous avez configuré récupération de Site Azure pour travailler avec vos machines virtuelles Azure concernés, vous pouvez restaurer vos ordinateurs virtuels à partir de leurs duplications. Celles-ci peut résider dans Azure ou en local. Dans ce cas, vous pouvez créer une nouvelle machine virtuelle à partir de son réplica existant. Pour restaurer vos ordinateurs virtuels à partir d’un réplica de récupération de Site Azure, voir [machines virtuelles de migrer Azure IaaS entre Azure régions dont la récupération de Site Azure](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Bien que le système d’exploitation Azure machine virtuelle et des disques de données à répliquer sur un disque dur virtuel secondaire, si elles sont dans un compte de stockage geo redondants accès en lecture ou de stockage geo redondants, chaque disque dur virtuel est répliquée séparément. Ce niveau de la réplication ne garantit la cohérence entre les disques durs virtuels répliquées. Si votre application et/ou les bases de données qui utilisent ces disques de données ont des dépendances sur les uns des autres, il n’est pas sûr de que tous les disques durs virtuels sont répliquées sous forme d’un instantané. Il n’est également pas garantie que le réplica de disque dur virtuel de stockage geo redondantes ou stockage geo redondants accès en lecture entraînera un instantané cohérent application pour démarrer la machine virtuelle.

##<a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise en œuvre d’un sinistre et stratégie de disponibilité élevée, voir [sinistre et disponibilité des applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Pour développer une compréhension technique détaillée des fonctionnalités d’une plateforme en nuage, voir [conseils techniques résilience Azure](../resiliency/resiliency-technical-guidance.md).

Pour savoir comment sauvegarder des ordinateurs virtuels, consultez [Sauvegarder Azure machines virtuelles](../backup/backup-azure-vms.md).

Apprenez à utiliser la récupération de Site Azure pour organiser et automatiser la protection de vos ordinateurs Windows et Linux physiques (et virtuelles) qui s’exécutent sur VMWare et machines virtuelles Hyper-V, consultez la section [Récupération de Site Azure](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Si les instructions ne sont ne pas clairs, ou si vous souhaitez que Microsoft pour effectuer les opérations à votre place, contactez [Le Support client](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
