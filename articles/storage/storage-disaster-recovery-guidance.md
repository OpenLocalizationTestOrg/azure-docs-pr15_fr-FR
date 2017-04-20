<properties
    pageTitle="Procédure à suivre en cas de panne stockage Azure | Microsoft Azure"
    description="Procédure à suivre en cas de panne stockage Azure"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Procédure à suivre en cas d’interruption du stockage Azure

Chez Microsoft, nous nous efforçons pour vous assurer que nos services sont toujours disponibles. Parfois, force au-delà de notre impact contrôle nous d’une manière qui provoque des interruptions de service non planifiée dans une ou plusieurs zones. Pour vous aider à gérer ces rares occurrences, nous fournissons aux directives de haut niveau ci-après pour les services de stockage Azure.

## <a name="how-to-prepare"></a>Comment faire pour préparer 

Il est essentiel pour chaque client préparer leur propre plan de récupération. L’effort pour récupérer une panne de stockage généralement implique personnel des opérations et des procédures automatisées pour réactiver vos applications dans un état de fonctionnement. Reportez-vous à la documentation Azure ci-dessous pour créer votre propre plan de récupération :

-   [Reprise d’activité et disponibilité des applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Conseils techniques résilience Azure](../resiliency/resiliency-technical-guidance.md)

-   [Service de récupération de Site Azure](https://azure.microsoft.com/services/site-recovery/)

-   [Réplication du stockage Azure](storage-redundancy.md)

-   [Service de sauvegarde Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Comment faire pour détecter 

Les procédures recommandées pour déterminer l’état du service Azure consiste à s’abonner à l' [État du tableau de bord Service Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Procédure à suivre en cas de panne de stockage

Si un ou plusieurs services de stockage sont temporairement indisponibles à une ou plusieurs régions, il existe deux possibilités à prendre en compte. Si vous le souhaitez un accès immédiat à vos données, envisagez d’Option 2.

### <a name="option-1-wait-for-recovery"></a>Option 1 : Attendre la récupération

Dans ce cas, aucune action de votre part est requise. Nous travaillons assidûment pour restaurer la disponibilité des services Azure. Vous pouvez surveiller l’état du service sur le [Tableau de bord au niveau de Service Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Option 2 : Copier des données depuis le secondaire

Si vous avez choisi de [stockage geo redondants accès en lecture (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recommandé) pour vos comptes de stockage, vous aurez accès en lecture à vos données à partir de la zone secondaire. Vous pouvez utiliser les outils tels que [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), la [bibliothèque de déplacement des données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) à copier dans un autre compte de stockage dans une zone unimpacted des données à partir de la zone secondaire, puis pointez vos applications à ce compte de stockage à la fois pour lire et écrire disponibilité.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>À quoi s’attendre si un stockage basculement

Si vous avez choisi de [stockage Geo redondantes (GRS)](storage-redundancy.md#geo-redundant-storage) ou [stockage geo redondants accès en lecture (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recommandé), le stockage Azure permet de solidariser vos données résistant dans deux régions (principal et secondaire). Dans les deux régions, le stockage Azure gère constamment réplicas plusieurs de vos données.

Lorsqu’une panne régionale affecte votre région principale, nous allons tout d’abord tenter restaurer le service dans cette zone. Varie en fonction de la nature de l’incident et ses impacts, dans certaines rarement que nous pouvons de ne pas pouvoir restaurer la région principale. À ce stade, nous effectue un basculement geo. La réplication de données de la région de manière croisée est un processus asynchrone qui peut impliquer un délai, il est possible que les modifications qui n’ont pas encore été répliquées vers la zone secondaire peuvent être perdues. Vous pouvez interroger la [« heure de dernière synchronisation » de votre compte de stockage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) pour obtenir plus d’informations sur l’état de réplication.

Quelques points relatifs à l’expérience geo basculement stockage :

-   Stockage geo-basculement sont déclenché par l’équipe de stockage Azure : aucun client action n’est requise.

-   Vos points de terminaison de service stockage existants pour des objets BLOB, des tableaux, des files d’attente et des fichiers restera identique après le basculement ; l’entrée DNS devra être mise à jour pour basculer entre la région principale et la zone secondaire.

-   Avant et pendant le basculement geo, vous n’aurez accès en écriture à votre compte de stockage en raison de l’impact de l’incident, mais vous pouvez toujours lire à partir de l’image secondaire si votre compte de stockage a été configuré comme RA GRS.

-   Lorsque le basculement geo a été effectuée et les modifications DNS propagées, votre lecture et l’accès en écriture à votre compte de stockage va reprendre. Vous pouvez interroger [« dernière Geo basculement temps » de votre compte de stockage](https://msdn.microsoft.com/library/azure/ee460802.aspx) pour obtenir plus de détails.

-   Après le basculement, votre compte de stockage entièrement fonctionne, mais dans un état « dégradé », tel qu’il est hébergé en réalité dans une région autonome avec aucune possible geo réplication. Pour atténuer ce risque, nous restaure la région principale d’origine et effectuez un geo-retour arrière pour rétablir l’état d’origine. Si la zone principale d’origine est irrécupérable, nous affecte une autre zone secondaire.
Pour plus d’informations sur l’infrastructure de stockage Azure geo réplication, reportez-vous à l’article de blog de l’équipe de stockage sur les [Options de redondance et RA GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##<a name="best-practices-for-protecting-your-data"></a>Meilleures pratiques pour la protection de vos données

Il existe certaines approches recommandées pour sauvegarder vos données de stockage de façon régulière.

-   Machine virtuelle disques – utiliser le [service de sauvegarde Azure](https://azure.microsoft.com/services/backup/) pour sauvegarder les disques machine virtuelle utilisés par vos machines virtuelles Azure.

-   Bloc d’objets BLOB – créer une [capture instantanée](https://msdn.microsoft.com/library/azure/hh488361.aspx) de chaque blob bloc, ou copier les objets BLOB à un autre emplacement du compte dans une autre région à l’aide de [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)ou la [bibliothèque de déplacement des données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tables – permet d’exporter les données du tableau dans un autre compte de stockage dans une autre région [AzCopy](storage-use-azcopy.md) .

-   Fichiers – utilisez [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) pour copier vos fichiers vers un autre compte de stockage dans une autre région.
