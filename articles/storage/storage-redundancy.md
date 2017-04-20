<properties
    pageTitle="La réplication du stockage Azure | Microsoft Azure"
    description="Données de votre compte de stockage de Microsoft Azure sont répliquées de durabilité et de disponibilité. Options de réplication incluent stockage localement redondante (LRS), stockage zone redondantes (ZRS), stockage geo redondantes (GRS) et stockage geo redondants accès en lecture (RA GRS)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Réplication du stockage Azure

Les données dans votre compte de stockage de Microsoft Azure sont toujours répliquées pour vous assurer que durabilité et la disponibilité. La réplication copie vos données, au sein du même centre de données ou à un deuxième centre de données, en fonction de quelle option de réplication que vous choisissez. Réplication protéger vos données et conserve votre application le temps en cas de panne du matériel transitoires. Si vos données sont répliquées sur un deuxième centre de données, également pour la protection de vos données contre une panne dans l’emplacement principal.

La réplication garantit que votre compte de stockage répond au [Niveau de Service du contrat de stockage](https://azure.microsoft.com/support/legal/sla/storage/) quoi qu’il arrive échecs. Consultez que le contrat SLA pour plus d’informations sur le stockage Azure garanties de durabilité et de disponibilité. 

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de réplication suivantes :  

- [Stockage localement redondante (LRS)](#locally-redundant-storage)
- [Stockage zone redondantes (ZRS)](#zone-redundant-storage)
- [Stockage geo redondantes (GRS)](#geo-redundant-storage)
- [Stockage geo redondants accès en lecture (RA GRS)](#read-access-geo-redundant-storage)

Stockage geo redondants accès en lecture (RA GRS) est l’option par défaut lorsque vous créez un nouveau compte de stockage.

Le tableau suivant fournit un bref aperçu des différences entre LRS, ZRS, GRS et RA-GRS, tandis que les sections suivantes traitent de chaque type de réplication plus en détail.

| Stratégie de réplication                                                               | LRS | ZRS | GRS | RA GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Données sont répliquées sur plusieurs centres de données.                                     | N°  | Oui | Oui | Oui    |
| Données peuvent être lus à partir de l’emplacement secondaire et d’emplacement principal. | N°  | N°  | N°  | Oui    |
| Nombre de copies des données conservées sur des nœuds distincts.                             | 3   | 3   | 6   | 6      |

Informations pour afficher les options de redondance différents de tarification, voir [Tarifs de stockage Azure](https://azure.microsoft.com/pricing/details/storage/) .

>[AZURE.NOTE] Stockage Premium prend en charge le stockage uniquement localement redondante (LRS). Pour plus d’informations sur le stockage Premium, consultez [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Stockage localement redondants

Stockage localement redondante (LRS) réplique vos données trois fois au sein d’une unité d’échelle de stockage qui est hébergée dans un centre de données dans la région dans lequel vous avez créé votre compte de stockage. Une demande d’écriture renvoie correctement uniquement une fois qu’il a été écrit dans tous les trois réplicas. Trois celles-ci chaque réside dans les domaines défaillance distincte et mise à niveau au sein de l’unité d’échelle de stockage. 

Une unité d’échelle de stockage est un ensemble de racks des nœuds de stockage. Un domaine d’erreur (FD) est un groupe de nœuds qui représentent une unité physique de panne et peuvent être considérés comme des nœuds appartenant à la même rack physique. Un domaine de mise à niveau (UD) est un groupe de nœuds qui sont mis à jour ensemble pendant le processus d’une mise à niveau de service (déploiement). Les trois réplicas sont réparties sur UDs et FDs au sein d’une unité d’échelle de stockage pour vous assurer que les données sont disponibles même si défaillance matérielle a un impact sur un seul rack ou lors de la mise à niveau des nœuds au cours d’une mise en place. 

LRS est l’option de coûts les plus faibles et offre moins durabilité par rapport à d’autres options. En cas de sinistre niveau centre de données (fire, saturation etc.) toutes les trois répliques peuvent être perdus ou irrécupérables. Pour atténuer ce risque Geo redondants stockage (GRS) est recommandée pour la plupart des applications.

Stockage localement redondants peut toujours s’avérer certains scénarios : 

- Fournit la bande passante maximale plus élevée des options de réplication de stockage Azure.

- Si votre application stocke les données qui peuvent être facilement reconstruites, vous pouvez choisir pour LRS.

- Certaines applications sont limitées à la réplication des données uniquement au sein d’un pays en raison de gouvernance de données. Une région appariée peut être dans un autre pays ; voir [régions Azure](https://azure.microsoft.com/regions/) pour plus d’informations sur les paires de région.

## <a name="zone-redundant-storage"></a>Stockage zone redondants

Stockage zone redondantes (ZRS) réplique vos données en mode asynchrone sur centres de données au sein d’une ou deux régions outre le stockage de trois répliques similaires à LRS, ce qui assure une durabilité élevée que LRS. Données stockées dans ZRS sont résistants même si le centre de données principal n’est pas disponible ou irrécupérables.
Les clients qui planifier l’utilisation ZRS doivent tenir compte qui : 

- ZRS est disponible uniquement pour les comptes de stockage objectif général des objets BLOB bloc et est pris en charge uniquement dans les versions de service de stockage 2014-02-14 et versions ultérieures. 

- Dans la mesure où la réplication asynchrone implique un retard, en cas de sinistre local, il est possible que les modifications n’ont pas encore été répliquées sur le site secondaire seront perdues si les données ne peuvent pas être récupérées du site principal.

- Le réplica peut ne pas être disponible jusqu'à ce que Microsoft lance le basculement vers le secondaire.

- Comptes ZRS ne peut pas être converties ultérieurement en LRS ou GRS. De même, un compte existant LRS ou GRS ne peut pas être converti à un compte ZRS.

- Comptes ZRS n’ont pas métriques ou fonctionnalité de journalisation. 

## <a name="geo-redundant-storage"></a>Stockage geo redondants

Stockage geo redondantes (GRS) réplique vos données sur une zone secondaire des centaines de miles en dehors de la région principale. Si votre compte de stockage comporte GRS activé, vos données sont résistants même en cas de panne régionale de complète ou un incident dans lequel la région principale n’est pas récupérable.

Pour un compte de stockage avec GRS activé, une mise à jour s’engage tout d’abord à la zone principale, où il est dupliqué trois fois. La mise à jour est ensuite répliquée asynchrone à la zone secondaire, où il est également répliquée trois fois. 

Avec GRS les zones principales et secondaires gérer réplicas plusieurs domaines d’erreur distincte et mettre à niveau des domaines au sein d’une unité d’échelle stockage comme décrit avec LRS.

Considérations relatives à :

- Dans la mesure où la réplication asynchrone implique un délai, en cas de sinistre régionaux, il est possible que les modifications n’ont pas encore été répliquées vers la zone secondaire seront perdues si les données ne peuvent pas être récupérées à partir de la région principale.

- Le réplica n’est pas disponible sauf si Microsoft lance le basculement à la zone secondaire.

- Si une application souhaite lire à partir de la zone secondaire l’utilisateur doit activer RA GRS. 

Lorsque vous créez un compte de stockage, vous sélectionnez la région principale pour le compte. La zone secondaire est déterminée en fonction de la région principale et ne peuvent pas être modifiée. Le tableau suivant montre les paires de région principal et secondaire.

| Principal             | Secondaire           |
|---------------------|---------------------|
| États-Unis centre nord    | États-Unis centre sud    |
| États-Unis centre sud    | États-Unis centre nord    |
| États-Unis Extrême-Orient             | États-Unis Ouest             |
| États-Unis Ouest             | États-Unis Extrême-Orient             |
| États-Unis est 2           | États-Unis centre          |
| États-Unis centre          | États-Unis est 2           |
| Europe du Nord        | Europe occidentale         |
| Europe occidentale         | Europe du Nord        |
| Asie du Sud-est     | Asie de l’est           |
| Asie de l’est           | Asie du Sud-est     |
| Chine Extrême-Orient          | En Chine du Nord         |
| En Chine du Nord         | Chine Extrême-Orient          |
| Orient Japon          | Japon ouest          |
| Japon ouest          | Orient Japon          |
| Brésil sud        | États-Unis centre sud    |
| Australie est      | Australie sud-est. |
| Australie sud-est. | Australie est      |
| Inde sud         | Inde centrale       |
| Inde centrale       | Inde sud         |
| États-Unis et pour le gouvernement Iowa         | États-Unis et pour le gouvernement Virginie     |
| États-Unis et pour le gouvernement Virginie     | États-Unis et pour le gouvernement Iowa         |
| Canada Central      | Canada Extrême-Orient          |
| Canada Extrême-Orient         | Canada Central      |
| Royaume-Uni ouest             | Royaume-Uni sud            |
| Royaume-Uni sud            | Royaume-Uni ouest             |
| Allemagne centrale     | Allemagne nord-est   |
| Allemagne nord-est   | Allemagne centrale     |
| États-Unis Ouest 2           | États-Unis Ouest centre     |
| États-Unis Ouest centre     | États-Unis Ouest 2           |

Pour obtenir des informations récentes sur régions pris en charge par Azure, voir [Régions Azure](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Stockage geo redondants accès en lecture

Stockage geo redondants accès en lecture (RA GRS) optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la réplication entre deux régions fournies par GRS. 

Lorsque vous activez l’accès en lecture seule à vos données dans la zone secondaire, vos données sont disponibles sur un point de terminaison secondaire, outre le point de terminaison principal pour votre compte de stockage. Le point de terminaison secondaire est semblable au point de terminaison principal, mais ajoute le suffixe `–secondary` au nom du compte. Par exemple, si votre principal point de terminaison du service Blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les touches d’accès pour votre compte de stockage sont les mêmes pour les deux points de terminaison principales et secondaires.

Considérations relatives à :

- Votre application doit gérer quel point de terminaison interagit avec lorsque vous utilisez RA GRS. 

- RA GRS est destiné à des fins de disponibilité. Pour obtenir des instructions extensibilité élevées, veuillez consulter [liste de vérification de performances](storage-performance-checklist.md).

## <a name="next-steps"></a>Étapes suivantes

- [Tarifs de stockage Azure](https://azure.microsoft.com/pricing/details/storage/)
- [À propos des comptes de stockage Azure](storage-create-storage-account.md)
- [Extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md)
- [Options de redondance de stockage de Microsoft Azure et accès en lecture Geo stockage redondants](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Papier SOSP - stockage Azure : Un nuage hautement disponible Service de stockage grâce à la cohérence fort](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
