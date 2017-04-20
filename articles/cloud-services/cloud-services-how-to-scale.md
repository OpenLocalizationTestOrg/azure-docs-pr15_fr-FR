<properties
    pageTitle="Automatique adapter un service cloud dans le portail | Microsoft Azure"
    description="(standard) Découvrez comment utiliser le portail classique pour configurer des règles d’échelle automatique pour un rôle de cloud service web ou collaborateur dans Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Comment un service cloud à l’échelle automatique

> [AZURE.SELECTOR]
- [Portail Azure](cloud-services-how-to-scale-portal.md)
- [Portail classique Azure](cloud-services-how-to-scale.md)

Dans la page d’échelle du portail Azure classique, vous pouvez manuellement mettre à l’échelle votre rôle web ou travail, ou vous pouvez activer la mise à l’échelle automatique en fonction de la charge du processeur ou une file d’attente.

>[AZURE.NOTE] Cet article se concentre sur les rôles web et collaborateur Service Cloud. Lorsque vous créez une machine virtuelle (classique) directement, s’il est hébergé dans un service cloud. Certaines de ces informations s’appliquent à ces types de machines virtuelles. Redimensionnement d’un jeu de disponibilité des machines virtuelles est simplement en train de les et désactiver les règles d’échelle que vous configurez. Pour plus d’informations sur les Machines virtuelles et des jeux de disponibilité, voir [Gérer la disponibilité des ordinateurs virtuels](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Vous devez prendre en compte les informations suivantes avant de configurer mise à l’échelle de votre application :

- Mise à l’échelle n’est affecté par l’utilisation de base. Plus grande instances de rôle utilisent plus de cœurs. Vous pouvez adapter une application uniquement dans la limite de cœurs pour votre abonnement. Par exemple, si votre abonnement a une limite de vingt cœurs et que vous exécutez une application avec deux support Tabloïd cloud services (un total de quatre cœurs), vous pouvez uniquement évoluer vers d’autres déploiements de service cloud dans votre abonnement en 16 cœurs. Pour plus d’informations sur les tailles, consultez [Tailles de Service Cloud](cloud-services-sizes-specs.md) .

- Vous devez créer une file d’attente et associer à un rôle avant que vous pouvez mettre à l’échelle une application basée sur un seuil de message. Pour plus d’informations, voir [comment utiliser le Service de stockage file d’attente](../storage/storage-dotnet-how-to-use-queues.md).

- Vous pouvez ajuster les ressources qui sont liées à votre service cloud. Pour plus d’informations sur la liaison des ressources, voir [Comment : lier une ressource à un service cloud](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Pour activer la disponibilité de votre application, vous devez vous assurer qu’elle est déployée avec deux ou plusieurs instances de rôle. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Planifier la mise à l’échelle

Par défaut, tous les rôles ne suivent pas un calendrier spécifique. Par conséquent, les paramètres modifiés s’appliquent à tout moment et tous les jours tout au long de l’année. Si vous le souhaitez, vous pouvez configurer la mise à l’échelle manuelle ou automatique pour :

- Jours de la semaine
- Week-ends
- Nuit
- Matin semaine
- Dates spécifiques
- Plages de dates spécifique

Il s’agit de conigured dans le [portail classique Azure](https://manage.windowsazure.com/) sur la  
**Services en nuage** > **\[votre service cloud\]** > **échelle** > **\[Production ou intermédiaire\] ** page.

Cliquez sur le bouton **configurer les heures de planification** pour chaque rôle que vous souhaitez modifier.

![Échelle d’un service cloud automatique en fonction d’une planification][scale_schedules]



## <a name="manual-scale"></a>Échelle manuelle

Dans la page **d’échelle** , vous pouvez manuellement augmenter ou diminuer le nombre d’instances en cours d’exécution dans un service cloud. Il est configuré pour chaque planification que vous avez créée ou à tout le temps si vous n’avez pas créé un planning.

1. Dans le [portail classique Azure](https://manage.windowsazure.com/), cliquez sur **Les Services en nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier de **Production** mis en **œuvre** ou vice versa.

2. Cliquez sur **échelle**.

3. Sélectionnez le calendrier que vous voulez modifier les options de mise à l’échelle pour. Valeur par défaut est *N° heures* si vous n’avez aucune planification définie.

4. Recherchez la section **échelle par métrique** et sélectionnez **NONE**. C’est le paramètre par défaut pour tous les rôles.

5. Chaque rôle dans le service cloud comporte un curseur permettant de modifier le nombre d’instances à utiliser.

    ![Échelle manuellement un rôle de service cloud][manual_scale]

    Si vous avez besoin de plusieurs instances, vous devrez modifier la [taille du cloud service machine virtuelle](cloud-services-sizes-specs.md).

6. Cliquez sur **Enregistrer**.  
Instances de rôle seront ajoutés ou supprimés en fonction de vos sélections.

>[AZURE.TIP] Chaque fois que vous voyez ![][tip_icon] atteindre votre souris et vous pouvez obtenir de l’aide sur quels un paramètre spécifique.


## <a name="automatic-scale---cpu"></a>Échelle automatique - processeur

Cela permet de dimensionner si le pourcentage moyen de l’UC tombe au-dessus ou en dessous des seuils spécifiés ; instances de rôle sont créées ou supprimées.

1. Dans le [portail classique Azure](https://manage.windowsazure.com/), cliquez sur **Les Services en nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier de **Production** mis en **œuvre** ou vice versa.

2. Cliquez sur **échelle**.

3. Sélectionnez le calendrier que vous voulez modifier les options de mise à l’échelle pour. Valeur par défaut est *N° heures* si vous n’avez aucune planification définie.

4. Recherchez la section **échelle par métrique** et sélectionnez **processeur**.

5. Désormais, vous pouvez configurer une plage minimale et maximale des instances de rôles, la cible de l’UC (pour déclencher une échelle vers le haut) et le nombre d’occurrences à l’échelle monter et descendre en.

![Ajuster un rôle service cloud en charge du processeur][cpu_scale]

>[AZURE.TIP] Chaque fois que vous voyez ![][tip_icon] atteindre votre souris et vous pouvez obtenir de l’aide sur quels un paramètre spécifique.





## <a name="automatic-scale---queue"></a>Échelle automatique - file d’attente

Cela s’ajuste automatiquement si le nombre de messages dans une file d’attente accède inférieures ou supérieures à un seuil spécifié ; instances de rôle sont créées ou supprimées.

1. Dans le [portail classique Azure](https://manage.windowsazure.com/), cliquez sur **Les Services en nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier de **Production** mis en **œuvre** ou vice versa.

2. Cliquez sur **échelle**.

3. Recherchez la section **échelle par métrique** et sélectionnez **processeur**.

4. Désormais, vous pouvez configurer une plage minimale et maximale d’instances de rôles, la file d’attente et la quantité de messages file d’attente à traiter pour chaque instance et le nombre d’occurrences à l’échelle monter et descendre en.

![Réduire un rôle de service de nuage à une file d’attente][queue_scale]

>[AZURE.TIP] Chaque fois que vous voyez ![][tip_icon] atteindre votre souris et vous pouvez obtenir de l’aide sur quels un paramètre spécifique.


## <a name="scale-linked-resources"></a>Mettre les ressources liées à l’échelle

Souvent lorsque vous redimensionnez un rôle, il est intéressant à l’échelle de la base de données qui utilise également l’application. Si vous liez la base de données au service cloud, vous pouvez accéder aux paramètres mise à l’échelle pour cette ressource en cliquant sur le lien approprié.

1. Dans le [portail classique Azure](https://manage.windowsazure.com/), cliquez sur **Les Services en nuage**, puis cliquez sur le nom du service cloud pour ouvrir le tableau de bord.

    > [AZURE.TIP] Si vous ne voyez pas votre service cloud, vous devrez peut-être modifier de **Production** mis en **œuvre** ou vice versa.

2. Cliquez sur **échelle**.

3. Recherchez la section **ressources liées** et cliqué sur **échelle gérer pour cette base de données**.

    > [AZURE.NOTE] Si vous ne voyez pas de section **ressources liées** , vous probablement pas dois-je toutes les ressources liées.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
