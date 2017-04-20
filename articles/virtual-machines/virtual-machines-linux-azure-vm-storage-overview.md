<properties
  pageTitle="Stockage de machine virtuelle Linux et Azure | Microsoft Azure"
  description="Décrit Azure Standard et le stockage Premium avec des machines virtuelles Linux."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Stockage Azure et Linux VM

Stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur durabilité, la disponibilité et extensibilité élevées en fonction des besoins de leurs clients.  En plus de ce qui permet aux développeurs de créer des applications à grande échelle pour prendre en charge de nouveaux scénarios, le stockage Azure fournit également la base de stockage pour le Machines virtuelles Azure.

## <a name="azure-storage-standard-and-premium"></a>Stockage Azure : Standard et Premium

Machines virtuelles Azure peut être basé sur disques de stockage standard ou disques de stockage premium.  Lors de l’utilisation du portail pour choisir votre machine virtuelle, vous devez activer/désactiver une liste déroulante à l’écran concepts de base pour afficher les disques standards et premium.  La capture d’écran ci-dessous met en évidence ce menu bascule.  Lorsque activée ou désactivée pour SSD, seul le stockage premium machines virtuelles activés seront affichés, toutes assorties SSD lecteurs.  Lorsque activée ou désactivée sur le disque dur, le stockage standard activé machines virtuelles disques qui tournent sauvegardés seront affichés, ainsi que de stockage premium machines virtuelles bénéficient SSD.

  ![Écran1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Lorsque vous créez une machine virtuelle à partir de la `azure-cli` vous pouvez choisir entre standard et premium lors du choix de la taille de la mémoire virtuelle via le `-z` ou `--vm-size` indicateur infrastructure du langage commun.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Créer une machine virtuelle avec stockage mémoire virtuelle standard sur l’infrastructure du langage commun

L’indicateur de l’infrastructure du langage commun `-z` choisit Standard_A1 avec A1 étant un stockage standard basé Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Créer une machine virtuelle avec stockage premium sur l’infrastructure du langage commun

L’indicateur de l’infrastructure du langage commun `-z` choisit Standard_DS1 avec DS1 étant un stockage premium basé Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Stockage standard

Stockage Standard Azure est le type par défaut de stockage.  Stockage standard est rentable tout en conservant la performant.  

## <a name="premium-storage"></a>Stockage Premium

Le stockage Premium Azure offre prise en charge de disque hautes performances et à faible latence des machines virtuelles je/O-accrue les charges de travail en cours d’exécution. Machine virtuelle () disques qui utilisent le stockage Premium stockent des données sur disques (SSDs). Vous pouvez déplacer des disques machine virtuelle de votre application pour le stockage Azure Premium pour tirer parti de la vitesse et de performances de ces disques.

Fonctionnalités de stockage Premium :

- Disques de stockage Premium : Stockage Premium Azure prend en charge disques machine virtuelle qui peuvent être attachés série DS, DSv2 ou GS machines virtuelles Azure.

- Premium Page Blob : Stockage Premium prend en charge les objets BLOB Azure de Page, qui sont utilisés pour contenir disques permanentes pour Machines virtuelles Azure (machines virtuelles).

- Stockage localement redondants Premium : Un compte de stockage Premium uniquement prend en charge localement redondants stockage (LRS) en tant que l’option de réplication et conserve les trois copies des données dans une région unique.

- [Stockage Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Stockage de Premium pris en charge machines virtuelles

Stockage Premium prend en charge la série DS, DSv2 série, GS série et Azure Fs série virtuels (machines virtuelles). Vous pouvez utiliser des disques de stockage Standard et Premium avec stockage Premium pris en charge de machines virtuelles. Mais vous ne pouvez pas utiliser disques de stockage Premium avec série machine virtuelle, qui n’est pas Premium stockage compatibles.

Voici les éditions Linux qui nous validées avec stockage Premium.

| Distribution | Version                 | Noyau pris en charge    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7.x, 8.x                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SERVICE PACK 4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| CentOS       | 6.5, 6.6, 6,7, 7.0, 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6.8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Stockage de fichiers

Stockage de fichiers Azure propose des partages de fichiers dans le nuage en utilisant le protocole PME standard. Fichiers Azure, vous pouvez migrer des applications d’entreprise qui s’appuient sur les serveurs de fichiers vers Azure. Applications qui s’exécutent dans Azure peuvent monter facilement des partages de fichiers à partir d’Azure machines virtuelles exécutant Linux. Et avec la dernière version de stockage de fichiers, vous pouvez également monter un partage de fichiers à partir d’une application locale qui prend en charge les PME 3.0.  Partages de fichiers étant PME partages, vous pouvez y accéder via API du système de fichiers standard.

Stockage de fichiers repose sur la même technologie de stockage Blob, la Table et file d’attente, afin que le stockage de fichiers offre la disponibilité, durabilité, extensibilité élevées et redondance geo intégré à la plate-forme de stockage Azure. Pour plus d’informations sur les objectifs de performance de stockage de fichier et les limites, voir extensibilité du stockage Azure et des objectifs de Performance.

- [Comment utiliser le stockage de fichiers Azure avec Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Stockage d’accès rapide

Le niveau de stockage hot Azure est optimisé pour stocker les données fréquemment utilisées.  Stockage actif est le type de stockage par défaut pour les objets blob magasins.

## <a name="cool-storage"></a>Stockage pouvant être ajoutée

Le niveau de stockage pouvant être ajoutées Azure est optimisé pour stocker les données rarement consultées et durée de vie longue. Exemples d’utilisation exemple pour le stockage pouvant être ajoutée incluent des sauvegardes, du contenu multimédia, des données scientifiques, conformité et données d’archivage. En règle générale, toutes les données auxquelles vous accédez rarement sont une candidate idéale pour le stockage pouvant être ajoutée.

|                             | Niveau de stockage actif      | Niveau de stockage pouvant être ajoutées     |
|:----------------------------|:---------------------:|:---------------------:|
| Disponibilité                | 99,9 %                 | 99 %                   |
| Disponibilité (lectures RA GRS) | 99,99 %                | 99,9 %                 |
| Frais d’utilisation               | Coûts de stockage supérieures  | Réduire les coûts de stockage   |
|                             | Accès inférieur          | Accès plus élevé         |
|                             | et les coûts de transaction | et les coûts de transaction |


## <a name="redundancy"></a>Redondance

Les données dans votre compte de stockage de Microsoft Azure sont toujours répliquées pour vous assurer que durabilité et la disponibilité, les contrats du stockage Azure SLA quoi qu’il arrive défaillances matérielles transitoires.

Lorsque vous créez un compte de stockage, vous devez sélectionner une des options de réplication suivantes :

- Stockage localement redondante (LRS)
- Stockage zone redondantes (ZRS)
- Stockage geo redondantes (GRS)
- Stockage geo redondants accès en lecture (RA GRS)

### <a name="locally-redundant-storage"></a>Stockage localement redondants

Stockage localement redondante (LRS) réplique vos données dans la zone dans laquelle vous avez créé votre compte de stockage. Pour optimiser la durabilité, toutes les requêtes adressées par rapport aux données dans votre compte de stockage sont répliquée trois fois. Trois celles-ci chaque réside dans les domaines défaillance distincte et mise à niveau.  Renvoi d’une requête avec succès uniquement une fois qu’il a été écrit dans tous les trois réplicas.

### <a name="zone-redundant-storage"></a>Stockage zone redondants

Stockage zone redondantes (ZRS) réplique vos données entre deux ou trois installations, dans une région unique ou entre deux régions, fournir une durabilité élevée que LRS. Si votre compte de stockage comporte ZRS activé, vos données sont résistants même en cas de panne sur une des installations.

### <a name="geo-redundant-storage"></a>Stockage geo redondants

Stockage geo redondantes (GRS) réplique vos données sur une zone secondaire des centaines de miles en dehors de la région principale. Si votre compte de stockage comporte GRS activé, vos données sont résistants même en cas de panne régionale de complète ou un incident dans lequel la région principale n’est pas récupérable.

### <a name="read-access-geo-redundant-storage"></a>Stockage geo redondants accès en lecture

Stockage geo redondants accès en lecture (RA GRS) optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la réplication entre deux régions fournies par GRS. Dans le cas où les données sont plus disponibles dans la région principale, votre application peut lire des données à partir de la zone secondaire.

Pour approfondie de stockage Azure redondance voir :

- [Réplication du stockage Azure](../storage/storage-redundancy.md)

## <a name="scalability"></a>Extensibilité élevées

Stockage Azure étant considérablement format SVG, vous pouvez stocker et processus des centaines de To de données pour prendre en charge les scénarios de données Long requis par analyse scientifique, financière et les applications multimédia. Ou vous pouvez stocker les petites quantités de données requises pour un site Web de petite entreprise. À l’endroit où se trouvent de vos besoins, vous payez uniquement pour les données que sont stockés. Stockage Azure actuellement stocke les dizaines de codage d’objets customer uniques et gère des millions de requêtes par seconde en moyenne.

Pour les comptes de stockage standard : un compte de stockage standard a un taux total maximale d’une demande de 20 000 sorties par. Le total sorties par dans l’ensemble de vos disques machine virtuelle dans un compte de stockage standard ne doit pas dépasser cette limite.

Pour les comptes de stockage premium : un compte de stockage premium a un débit total maximale de 50 Go/s. Le débit total dans l’ensemble de vos disques machine virtuelle ne doit pas dépasser cette limite.

## <a name="availability"></a>Disponibilité

Nous garantit qu’au moins 99,99 % (99,9 % pour pouvant être ajoutées niveau d’accès) de l’heure, nous correctement traitera demandes pour lire les données à partir de comptes de stockage redondants accès en lecture Geo (RA GRS), à condition qu’échecs de lire les données à partir de la zone primaire sont retentées sur la zone secondaire.

Nous garantit qu’au moins 99,9 % (99 % pour le niveau d’accès pouvant être ajoutées) de l’heure, nous correctement traitera les requêtes de lecture des données à partir de stockage localement redondantes (LRS), Zone redondants stockage (ZRS) et les comptes de stockage redondants Geo (GRS).

Nous garantit qu’au moins 99,9 % (99 % pour le niveau d’accès pouvant être ajoutées) de l’heure, nous correctement traitera demandes pour écrire des données aux stockage localement redondantes (LRS), Zone redondants stockage (ZRS) et comptes Geo redondants stockage (GRS) et accès en lecture Geo stockage redondants (RA GRS).

- [Azure SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Régions

Azure est généralement disponible dans les 30 régions du monde et a annoncé de 4 régions supplémentaires. Développement géographique est une priorité pour Azure, car elle permet à nos clients à obtiendront de meilleures performances et il prend en charge leurs besoins et préférences en ce qui concerne l’emplacement des données.  Azures dernière région pour lancer est en Allemagne.

L’Allemagne Cloud Microsoft fournit une option différenciée aux services Cloud Microsoft déjà disponibles en Europe, création de meilleures opportunités de l’innovation et croissance économique pour les partenaires réglementation et vos clients en Allemagne, l’Union européenne (UE) et l’Association européenne de libre échange (AELE).

Données client dans ces nouveaux centres de données, dans Magdeburg et Francfort, sont gérées sous le contrôle d’un tiers de confiance de données, T-Systems International, une société allemande indépendante et filiale de Deutsche Telekom. Services en nuage professionnelle de Microsoft dans ces centres de données sont conformes aux données allemandes gestion des réglementations et fournir aux clients des options supplémentaires d’où et comment les données sont traitées.


- [Mapper les régions Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Sécurité

Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Le compte de stockage peut être sécurisé à l’aide de contrôle d’accès basé sur un rôle et Azure Active Directory. Les données peuvent être sécurisées lors des transferts entre une application et Azure en utilisant le chiffrement côté Client, HTTPS ou PME 3.0. Données peuvent être définies à chiffrer automatiquement lors de l’écriture au stockage Azure à l’aide de chiffrement de Service de stockage (SSE). Système d’exploitation et données disques utilisés par les machines virtuelles peuvent être définies pour être chiffrés à l’aide de chiffrement des disques Azure. Accès délégué aux objets de données dans le stockage Azure peuvent être accordées à l’aide de Signatures d’accès partagé.

### <a name="management-plane-security"></a>Sécurité de plan de gestion

Le plan de gestion des se compose des ressources utilisées pour gérer votre compte de stockage. Dans cette section, nous aborderons le modèle de déploiement d’Azure le Gestionnaire de ressources et comment utiliser le contrôle d’accès basé sur un rôle (RBAC) pour contrôler l’accès à vos comptes de stockage. Nous allons aborder également gérer vos clés de compte de stockage et comment les régénérer.

### <a name="data-plane-security"></a>Sécurité de plan de données

Dans cette section, nous allons aborder permettant d’accéder aux objets de données réelles dans votre compte de stockage, tels que des objets BLOB, les fichiers, les files d’attente et les tables, à l’aide de partagés accès Signatures et stockées les stratégies d’accès. Cet article aborde les associations de sécurité au niveau de service et les associations de sécurité au niveau des comptes. Nous verrons également comment limiter l’accès à une adresse IP spécifique (ou plage d’adresses IP), comment vous pouvez limiter le protocole utilisé pour HTTPS et comment révoquer une Signature accès partagé sans attendre sa date d’expiration.

## <a name="encryption-in-transit"></a>Chiffrement lors des transferts

Cette section explique comment faire pour sécuriser les données lorsque vous le transférez ou disparition stockage Azure. Nous aborderons ensuite l’utilisation de HTTPS et le chiffrement utilisé par PME 3.0 Azure partages de fichiers recommandée. Nous allons également au chiffrement côté Client, qui permet de chiffrer les données avant leur transfert dans le stockage dans une application cliente et déchiffrer les données après transfert se déconnecter de stockage.

## <a name="encryption-at-rest"></a>Chiffrement inactives

Nous allons aborder chiffrement de Service de stockage (SSE), et comment vous pouvez activer pour un compte de stockage, ce qui crée de vos objets BLOB bloc, des objets BLOB de la page et ajoutez des objets BLOB en cours de chiffrement automatiquement lors de l’écriture au stockage Azure. Nous allons étudier également comment vous pouvez utiliser le chiffrement des disques Azure et découvrez les différences de base et les cas de chiffrement de disque et SSE et chiffrement côté Client. Nous allons étudier brièvement la conformité FIPS pour les ordinateurs gouvernement des États-Unis.

- [Guide de sécurité de stockage Azure](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Réduction des coûts

- [Coût de stockage](https://azure.microsoft.com/pricing/details/storage/)

- [Calculateur de coût](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de stockage

- [Limites de stockage de Service](../azure-subscription-service-limits.md#storage-limits)
