<properties
    pageTitle="Procédure à suivre en cas d’un Azure service interruption de service a un impact sur les Services en nuage Azure | Microsoft Azure"
    description="Découvrez la procédure à suivre en cas d’une interruption de service Azure a un impact sur les Services en nuage Azure."
    services="cloud-services"
    documentationCenter=""
    authors="kmouss"
    manager="drewm"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="cloud-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Procédure à suivre en cas d’un Azure service interruption de service a un impact sur les Services en nuage Azure

Chez Microsoft, nous nous efforçons pour vous assurer que nos services sont toujours disponibles lorsque vous en avez besoin. Force au-delà de notre contrôle parfois avoir un impact sur nous d’une manière qui entraîner des interruptions de service non planifiée.

Microsoft fournit un contrat de niveau de Service (SLA) pour ses services comme un engagement pour la connectivité et de disponibilité. Vous trouverez le SLA pour des services Azure individuels à [Contrats de niveau de Service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure déjà comporte de nombreuses fonctionnalités de plateforme intégrée qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, lisez [sinistre et disponibilité des applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article traite un scénario de récupération urgence true, lorsqu’une région entière rencontre une panne en raison de sinistre naturel majeur ou d’interruption de service étendu. Il s’agit des rares occurrences, mais vous devez préparer la possibilité d’une panne d’une région entière. Si une région entière rencontre une interruption de service, les copies de vos données redondantes localement serait temporairement indisponibles. Si vous avez activé la réplication geo, trois copies supplémentaires de votre stockage Azure BLOB et les tables sont stockées dans une zone différente. En cas de panne régionale de complète ou un incident dans lequel la région principale n’est pas récupérable, Azure reconfigure toutes les entrées DNS à la région répliquées geo.

>[AZURE.NOTE]N’oubliez pas que vous n’avez pas de n’importe quel contrôle sur ce processus, et il se produit uniquement pour les interruptions de service à l’échelle du centre de données. Pour cette raison, vous devez également compter sur d’autres stratégies de sauvegarde spécifiques à l’application pour atteindre le niveau le plus élevé de disponibilité. Pour plus d’informations, consultez la section sur les [stratégies de données de sinistre](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Si vous voulez être en mesure d’affecter votre propre basculement, vous souhaiterez peut-être envisager l’utilisation de [stockage geo redondants accès en lecture (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), ce qui crée une copie en lecture seule de vos données dans une autre région.

Pour vous aider à gérer ces rares occurrences, nous fournissons les recommandations suivantes Azure machines virtuelles (machines virtuelles) dans le cas d’une interruption de service de l’ensemble de la région dans lequel votre application Azure machine virtuelle est déployée.

##<a name="option-1-wait-for-recovery"></a>Option 1 : Attendre la récupération
Dans ce cas, aucune action de votre part est requise. Savoir que Azure équipes travaillent assidûment pour restaurer la disponibilité du service. Vous pouvez voir l’état actuel du service sur notre [Tableau de bord au niveau de Service Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Il s’agit de la meilleure option si un client n’a pas configurer la récupération de Site Azure ou dispose d’un déploiement secondaire dans une zone différente.

Pour les clients qui souhaitent un accès immédiat aux services en nuage déployée, les options suivantes sont disponibles.

>[AZURE.NOTE]N’oubliez pas que ces options ont la possibilité d’une perte de données.     

##<a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>Option 2 : Redéployez votre configuration des services cloud pour une nouvelle zone

Si vous avez votre code d’origine, vous pouvez tout simplement redéployez l’application, configuration associée et les ressources associées à un service en nuage dans une nouvelle région.  

Pour plus d’informations sur la façon de créer et déployer une application de service cloud, Découvrez [comment créer et déployer un service cloud](./cloud-services-how-to-create-deploy-portal.md).

En fonction de vos sources de données d’application, vous devrez peut-être vérifier les procédures de récupération pour votre source de données de l’application.
  * Pour les sources de données de stockage Azure, consultez [réplication du stockage Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) pour vérifier les options disponibles basé sur le modèle de réplication choisi pour votre application.
  * Pour des sources de base de données SQL, lisez [vue d’ensemble : Cloud continuité et base de données reprise avec la base de données SQL](../sql-database/sql-database-business-continuity.md) pour vérifier les options disponibles basées sur le modèle de réplication choisie pour votre application.

##<a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>Option 3 : Utiliser un déploiement sauvegarde via le Gestionnaire de trafic Azure
Cette option suppose que vous avez déjà conçu votre solution d’application avec sinistre régional à l’esprit. Vous pouvez utiliser cette option si vous disposez déjà d’un déploiement d’application services cloud secondaire qui s’exécute dans une zone différente et connecté à un canal gestionnaire le trafic. Dans ce cas, vérifiez l’état du déploiement secondaire. S’il est correct, vous pouvez rediriger le trafic vers celui-ci via le Gestionnaire de trafic Azure. Grâce à cette stratégie, vous pouvez tirer parti de la méthode de routage de trafic et les configurations de commande basculement dans le Gestionnaire de trafic Azure. Pour plus d’informations, voir [comment configurer les paramètres du Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Équilibrage de Services de Cloud Azure entre régions avec le Gestionnaire de trafic Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise en œuvre d’un sinistre et stratégie de disponibilité élevée, voir [sinistre et disponibilité des applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Pour développer une compréhension technique détaillée des fonctionnalités d’une plateforme en nuage, voir [conseils techniques résilience Azure](../resiliency/resiliency-technical-guidance.md).

Si les instructions ne sont ne pas clairs, ou si vous souhaitez que Microsoft pour effectuer les opérations à votre place, contactez le [Support client](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
