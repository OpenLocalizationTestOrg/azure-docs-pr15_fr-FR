<properties
    pageTitle="Déplacement de données vers et depuis le stockage Azure | Microsoft Azure"
    description="Cet article fournit une vue d’ensemble des différentes méthodes de déplacement des données vers et depuis le stockage Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Déplacement de données vers et depuis le stockage Azure

Si vous voulez déplacer les données locales au stockage Azure (ou inversement), il existe plusieurs façons pour effectuer cette action. L’approche qui vous convient le mieux dépend de votre scénario. Cet article constituent un bref aperçu des différents scénarios et les offres appropriées pour chacun d’eux.

## <a name="building-applications"></a>Création d’Applications

Si vous créez une application, le développement par rapport à l’API REST ou un de nos nombreuses bibliothèques client est un excellent moyen pour déplacer des données vers et depuis le stockage Azure.

Stockage Azure fournit des bibliothèques clientes enrichies pour .NET, iOS, Java, Android, universel Windows plateforme (UWP), Xamarin, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques client proposent des fonctionnalités avancées comme logique des nouvelles tentatives, journalisation et téléchargements en parallèle. Vous pouvez également développer directement par rapport à l’API REST, qui peut être appelé par n’importe quelle langue qui effectue des requêtes de HTTP/HTTPS.

Voir [Prise en main stockage d’objets Blob Azure](storage-dotnet-how-to-use-blobs.md) pour en savoir plus.

En outre, nous proposons également la [Bibliothèque de déplacement de données de stockage Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) qui est une bibliothèque conçue pour High performance copier des données vers ou à partir d’Azure. Veuillez consulter notre [documentation](https://github.com/Azure/azure-storage-net-data-movement) de bibliothèque de déplacement de données pour en savoir plus. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rapidement l’affichage/interaction avec vos données

Si vous souhaitez un moyen facile pour afficher vos données de stockage Azure tout en ayant la possibilité de charger et télécharger vos données, envisagez un Explorateur d’espace de stockage Azure.

Consultez notre liste [Azure stockage Explorers](storage-explorers.md) pour en savoir plus.

## <a name="system-administration"></a>Administration système

Si vous devez ou sentez plus à l’aise avec un utilitaire de ligne de commande (par exemple, les administrateurs système), voici quelques options à prendre en compte :

### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour High performance copier des données vers ou depuis le stockage Azure. Vous pouvez également copier les données au sein d’un compte de stockage ou entre des comptes de stockage différents.

Pour plus d’informations, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) .

### <a name="azure-powershell"></a>PowerShell Azure

PowerShell Azure est un module qui fournit des applets de commande de gestion des services dans Azure. Il s’agit d’un environnement de ligne de commande basée sur les tâches et le langage de script spécialement conçu pour l’administration système.

Consultez l’article [Utilisation de PowerShell Azure avec stockage Azure](storage-powershell-guide-full.md) pour en savoir plus.

### <a name="azure-cli"></a>Azure infrastructure du langage commun

Azure infrastructure du langage commun constitue un ensemble d’open source et disponibilité sur plusieurs plateformes commandes pour travailler avec les services Azure. Azure infrastructure du langage commun est disponible sous Windows, OSX et Linux.

Voir [à l’aide de l’infrastructure du langage commun Azure avec stockage Azure](storage-azure-cli.md) pour en savoir plus.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Déplacement de grandes quantités de données avec un réseau lent

Un des principaux défis associés au déplacement de grandes quantités de données est le temps de transfert. Si vous souhaitez obtenir des données vers ou à partir de stockage Azure sans soucier des coûts de réseaux ou écrire du code, Azure importer/exporter est une solution appropriée.

Voir la section [Azure importer/exporter](storage-import-export-service.md) pour en savoir plus.

## <a name="backing-up-your-data"></a>Sauvegarder vos données

Si vous voulez simplement sauvegarder vos données sur le stockage Azure, Azure sauvegarde est la solution idéale. Il s’agit d’une solution puissante pour sauvegarder les données locales et machines virtuelles Azure.

Consultez la rubrique [Sauvegarde Azure](../backup/backup-introduction-to-azure-backup.md) pour en savoir plus.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Accéder à vos données en local et à partir du cloud

Si vous avez besoin d’une solution pour accéder à vos données en local et à partir du cloud, vous devez envisager à l’aide de la solution de stockage cloud d’Azure hybride, StorSimple. Cette solution est constitué d’un périphérique StorSimple physique qu’intelligemment stores fréquemment sur SSDs données utilisées, il peut arriver que les données utilisées sur disques durs et données inactives/sauvegarde/archivage sur le stockage Azure.

Voir [StorSimple](../storsimple/storsimple-overview.md) pour en savoir plus.

## <a name="recovering-your-data"></a>Restauration de vos données

Lorsque vous avez des applications et charges de travail en local, vous devez une solution qui permet à votre entreprise continue de fonctionner en cas de sinistre. Récupération de Site Azure gère réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Les données répliquées sont stockées dans le stockage Azure, ce qui vous permet d’éliminer la nécessité d’un site Centre de données secondaire.

Consultez la section [Récupération de Site Azure](../site-recovery/site-recovery-overview.md) pour en savoir plus.
