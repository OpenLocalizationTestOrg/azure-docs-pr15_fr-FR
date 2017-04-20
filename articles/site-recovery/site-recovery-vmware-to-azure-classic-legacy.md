<properties
    pageTitle="Répliquer machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure (hérité) | Microsoft Azure" 
    description="Décrit comment répliquer les machines virtuelles en local et serveurs physiques Linux/Windows Azure à l’aide de récupération de Site Azure dans un déploiement hérité dans le portail classique." 
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Répliquer machines virtuelles VMware et des serveurs physiques vers Azure avec Azure récupération de Site à l’aide du portail classique (hérité)

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmware-to-azure.md)
- [Portail classique](site-recovery-vmware-to-azure-classic.md)
- [Portail classique (hérité)](site-recovery-vmware-to-azure-classic-legacy.md)


Bienvenue dans récupération de Site Azure ! Cet article décrit un déploiement hérité pour la réplication locale des machines virtuelles VMware Windows/serveurs ou Linux physiques vers Azure à l’aide de la récupération de Site Azure dans le portail classique.

## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR doit conserver les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail restent disponibles en permanence cas d’urgence.

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en organiser réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure) ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous basculer vers l’emplacement secondaire afin que les applications et les charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)


>[AZURE.WARNING] Cet article contient des **instructions héritées**. Ne pas l’utiliser pour les nouveaux déploiements. Au lieu de cela, [Suivez ces instructions](site-recovery-vmware-to-azure.md) pour déployer récupération de Site dans le portail Azure, ou [Utilisez les instructions suivantes](site-recovery-vmware-to-azure-classic.md) pour configurer le déploiement amélioré dans le portail classique. Si vous avez déjà déployé à l’aide de la méthode décrite dans cet article, nous vous recommandons de migrer vers le déploiement amélioré dans le portail classique.


## <a name="migrate-to-the-enhanced-deployment"></a>Migrer vers le déploiement amélioré

Cette section est pertinente uniquement si vous avez déjà déployé récupération de Site en suivant les instructions de cet article.

Pour migrer votre déploiement existant, que vous devez :

1. Déployer les nouveaux composants de récupération de Site dans votre site local.
2. Configurer les informations d’identification pour la découverte automatique des machines virtuelles VMware sur le nouveau serveur de configuration.
3. Découvrez les serveurs VMware avec le nouveau serveur de configuration.
3. Créer un nouveau groupe de protection avec le nouveau serveur de configuration.


Avant de commencer :

- Nous vous recommandons de configurer une période de maintenance pour la migration.
- L’option de **Migrer des Machines** est disponible uniquement si vous avez des groupes de protection existants qui ont été créées au cours d’un déploiement hérité.
- Une fois que vous avez terminé les étapes de migration peut prendre 15 minutes ou plus pour actualiser les informations d’identification et pour découvrir et actualiser machines virtuelles de sorte que vous pouvez les ajouter à un groupe de protection. Vous pouvez actualiser manuellement au lieu d’attendre. 

Migrer comme suit :

1. En savoir plus sur [améliorée du déploiement dans le portail classique](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Passez en revue l' amélioré [architecture](site-recovery-vmware-to-azure-classic.md#scenario-architecture)et les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Désinstallez le service mobilité à partir d’ordinateurs que vous êtes réplication actuellement. Une nouvelle version du service sera être installée sur les ordinateurs lorsque vous l’ajoutez au nouveau groupe de protection.
3. Téléchargez une [clé d’inscription de l’archivage sécurisé](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) et [exécutez l’Assistant configuration unifiée](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) pour installer le serveur de configuration, serveur de traitement et composants du serveur maître cible. En savoir plus sur [la planification des capacités](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Configurer les informations d’identification](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) que la récupération Site peuvent utiliser pour accéder VMware server pour détecter automatiquement les machines virtuelles VMware. Découvrez les [autorisations requises](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Ajouter des [Serveurs vCenter ou vSphere hosts](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Il peut prendre 15 minutes pour plus d’informations pour les serveurs apparaisse dans le portail de récupération de Site.
6. Créer un [Nouveau groupe de protection](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Il peut prendre jusqu'à 15 minutes pour que le portail pour actualiser de sorte que les ordinateurs virtuels sont découvertes et s’affichent. Si vous ne voulez pas attendre que vous pouvez mettre en surbrillance le nom du serveur gestionnaire (ne cliquez pas dessus) > **Actualiser**.
7. Sous le nouveau groupe de protection, cliquez sur **Migrer Machines**.

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. Dans **Sélectionner des ordinateurs** , sélectionnez le groupe de protection que vous voulez migrer à partir de, puis les ordinateurs que vous voulez migrer.

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. Dans **Configurer les paramètres cible** Indiquez si vous souhaitez utiliser les mêmes paramètres pour tous les ordinateurs et sélectionner le serveur de traitement et un compte de stockage Azure. Si vous n’avez pas un serveur de processus distinct il s’agit de l’adresse IP du serveur de serveur de configuration.


    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

10. **Spécifier les comptes**, sélectionnez le compte que vous avez créé pour le serveur de processus pour accéder à l’ordinateur pour la nouvelle version du service mobilité de transmission.

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Récupération de site doit migrer vos données répliquées au compte de stockage Azure que vous avez fourni. Si vous le souhaitez, vous pouvez réutiliser le compte de stockage utilisé dans le déploiement hérité.
12. Une fois que la tâche se termine machines virtuelles se synchronise automatiquement. Une fois que la synchronisation se termine, vous pouvez supprimer les machines virtuelles du groupe de protection héritées.
13. Une fois tous les ordinateurs ont migrés, vous pouvez supprimer le groupe de protection héritées.
14. N’oubliez pas de spécifier les propriétés de basculement pour les ordinateurs et les paramètres réseau Azure une fois la synchronisation terminée.
15. Si vous avez des plans existants récupération, vous pouvez les migrer vers le déploiement amélioré avec l’option **Migrer un Plan de récupération** . Vous devez uniquement procéder après la migration des tous les ordinateurs protégées. 

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Une fois que vous avez terminé de migration poursuivre l' [article amélioré](site-recovery-vmware-to-azure-classic.md). Le reste de cet article hérité ne seront plus pertinent et vous n’avez pas besoin de suivre plus de la procédure décrite dans informatiques **.




## <a name="what-do-i-need"></a>Que dois-je ?

Ce diagramme montre les composants de déploiement.

![Archivage sécurisé nouveau](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Voici ce que vous devez :

**Composant** | **Déploiement** | **Plus d’informations**
--- | --- | ---
**Serveur de configuration** | Une Azure standard A3 machine virtuelle dans le même abonnement comme récupération de Site. | La communication de coordonnées configuration serveur entre machines protégées, le serveur de traitement et des serveurs cible maître dans Azure. Il définit la réplication et la restauration des coordonnées dans Azure lors du basculement.
**Serveur cible maître** | Une machine virtuelle Azure — soit Windows server basé sur une image de la galerie Windows Server 2012 R2 (pour protéger des ordinateurs Windows) ou en tant qu’un serveur Linux basée sur une image de la galerie OpenLogic CentOS 6.6 (pour protéger des ordinateurs Linux).<br/><br/> Redimensionnement trois options sont disponibles – A4 Standard, D14 Standard et DS4 Standard.<br/><br/> Le serveur est connecté au réseau Azure même en tant que le serveur de configuration.<br/><br/> Vous avez configuré dans le portail de récupération de Site | Il reçoit et conserve les données répliquées à partir de vos machines protégées à l’aide de disques durs virtuels joints créés sur le stockage blob dans votre compte de stockage Azure.<br/><br/> Sélectionnez DS4 Standard spécialement pour la configuration de la protection des charges de travail nécessitant des performances élevées et une faible latence à l’aide du compte de stockage Premium.
**Serveur de traitement** | Un serveur virtuel ou physique local exécutant Windows Server 2012 R2<br/><br/> Nous vous recommandons il est placé sur le même réseau et le segment de réseau local que les ordinateurs que vous voulez protéger, mais il peut s’exécuter sur un autre réseau tant que machines protégées ont visibilité de réseau niveau 3.<br/><br/> Vous configurez-la et enregistrez sur le serveur de configuration dans le portail de récupération de Site. | Machines protégées envoyer des données de réplication sur le serveur de processus en local. Contient une réplication de cache de mettre en cache sur le disque de données qu’il reçoit. Elle effectue un certain nombre d’actions sur ces données.<br/><br/> Il optimise les données par mise en cache, compression et le chiffrement avant d’envoyer une session sur le serveur maître cible.<br/><br/> Gestion de l’installation push du Service mobilité.<br/><br/> Elle effectue la détection automatique des machines virtuelles VMware.
**Ordinateurs locaux** | Machines virtuelles de VMware en local, ou serveurs physiques exécutant Windows ou Linux. | Vous configurez les paramètres de réplication qui s’appliquent à un ou plusieurs ordinateurs. Vous pouvez échouer sur un ordinateur individuel ou plus fréquemment, plusieurs ordinateurs rassembler dans un plan de récupération. 
**Service de mobilité** | Installé sur chaque ordinateur virtuel ou le serveur physique à protéger<br/><br/> Peut être installée manuellement ou poussée et installé automatiquement par le serveur de traitement lorsque vous activez la réplication pour une machine. | Le service mobilité envoie des données au serveur de processus lors de la réplication initiale (resync). Une fois que l’ordinateur est dans un état protégé (une fois resync terminée) le service mobilité capture écritures sur disque en mémoire et les envoie au serveur de processus. Cohérencede l’application concernée pour les serveurs Windows est effectuée à l’aide de VSS.
**Archivage sécurisé récupération de Site Azure** | Vous créez un archivage sécurisé récupération de Site avec un abonnement Azure et enregistrez des serveurs dans l’archivage sécurisé. | L’archivage sécurisé coordonnées et orchestre réplication des données, le basculement et récupération entre votre site local et Azure.
**Mécanisme de réplication** | **Sur Internet**: données communique et réplique des serveurs de protégée local vers Azure à l’aide de sécurisé SSL/TLS canal via internet. Il s’agit de l’option par défaut.<br/><br/> **VPN/ExpressRoute**— communique et réplique des données entre serveurs en local et Azure via une connexion VPN. Vous devez configurer un réseau privé virtuel à un site ou une connexion ExpressRoute entre votre site local et le réseau Azure.<br/><br/> Vous pouvez de sélectionner comment vous souhaitez répliquer pendant le déploiement de récupération de Site. Vous ne pouvez pas modifier le mécanisme après que qu’il est configuré sans ayant un impact sur la réplication des ordinateurs existants. | Aucune de ces options, vous devez ouvrir les ports réseau entrant sur machines protégées. Toutes les communications réseau sont lancée à partir du site local. 

## <a name="capacity-planning"></a>Planification de la capacité

Zones principales que vous devez prendre en compte :

- **Environnement source**— VMware l’infrastructure, paramètres de la machine source et configuration requise.
- **Serveurs de composants**: le serveur de traitement, le serveur de configuration et le serveur de cible maître 

### <a name="considerations-for-the-source-environment"></a>Considérations sur l’environnement source

- **Taille maximale du disque**, la taille maximale actuelle du disque qui peut être associé à une machine virtuelle est de 1 To. Par conséquent la taille maximale d’un disque source qui peut être répliquée est également limitée à 1 To.
- **Taille maximale par source**, la taille maximale d’une machine source unique est 31 To (avec 31 disques) et avec une instance D14 mis en service pour le serveur maître cible. 
- **Nombre de sources par serveur cible maître**— plusieurs ordinateurs source peuvent être protégés par un serveur cible maître unique. Toutefois, une machine unique source ne peut pas être protégée sur plusieurs serveurs maître cible, car comme disques répliquent, un disque dur virtuel qui reflète la taille du disque est créé sur le stockage blob Azure et joints sous forme d’un disque de données sur le serveur maître cible.  
- **Tous les jours au maximum modifier taux par source**: il existe trois facteurs qui doivent être prises en compte quand vous déterminez la fréquence de modification recommandée par source. Pour les considérations cible basée sorties deux par sont nécessaires sur le disque cible pour chaque opération sur la source. C’est parce que la lecture de données anciennes et une écriture des nouvelles données les conséquences sur le disque cible. 
    - **Rythme pris en charge par le serveur de traitement quotidien de modification**, une machine source ne peut pas s’étalent sur plusieurs serveurs de traitement. Un serveur de processus unique peut prendre en charge jusqu'à 1 To de taux de modification quotidien. 1 To est donc que les données quotidiennes maximales modifier taux pris en charge pour une machine source. 
    - **Débit maximum pris en charge par le disque cible**: évolution du Maximum par disque source ne peut pas être plus de 144 Go/jour (avec une taille d’écriture 8 Ko). Voir le tableau dans la section cible maître pour le débit et sorties par de la cible pour différents écrire tailles. Ce nombre doit être divisé par deux car chaque source IOP génère des sorties 2 par sur le disque cible. En savoir plus sur [les cibles performances et extensibilité élevées Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) lors de la configuration de la cible pour les comptes de stockage premium.
    - **Débit maximum pris en charge par le compte de stockage**, une source ne peut pas s’étalent sur plusieurs comptes de stockage. Étant donné qu’un compte de stockage prend un maximum de 20 000 requêtes par seconde et que chaque source IOP génère des sorties 2 par au niveau du serveur de base cible, nous vous recommandons de que vous réduisez le nombre de sorties par au sein de la source à 10 000. En savoir plus sur [les cibles performances et extensibilité élevées Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) lors de la configuration de la source pour les comptes de stockage premium.

### <a name="considerations-for-component-servers"></a>Considérations sur des serveurs de composants

Tableau 1 récapitule les tailles machine virtuelle pour la configuration et les serveurs cibles maître.

**Composant** | **Instances Azure déployés** | **Cœurs** | **Mémoire** | **Disques max** | **Taille du disque**
--- | --- | --- | --- | --- | ---
Serveur de configuration | A3 standard | 4 | 7 GO | 8 | GO 1023
Serveur cible maître | A4 standard | 8 | 14 GO | 16 | GO 1023
 | D14 standard | 16 | 112 GO | 32 | GO 1023
 | DS4 standard | 8 | 28 GO | 16 | GO 1023

**Tableau 1**

#### <a name="process-server-considerations"></a>Remarques sur le serveur de traitement

Redimensionnement de serveur de processus dépend généralement le taux de modification quotidien sur toutes les charges de travail protégées.


- Vous devez cluster suffisante pour effectuer des tâches telles que la compression en ligne et de chiffrement.
- Le serveur de processus utilise cache disque. Vérifiez que l’espace de cache recommandée et débit de disque est disponible pour faciliter les modifications des données stockées dans le cas de critique réseau ou une panne. 
- Vérifiez que suffisamment de bande passante afin que le serveur de traitement peut télécharger les données sur le serveur cible maître pour fournir une protection continue des données. 

Tableau 2 fournit un résumé des instructions sur le serveur le processus.

**Taux de modification de données** | **PROCESSEUR** | **Mémoire** | **Taille du cache de disque**| **Débit du disque cache** | **La bande passante entrée/sortie**
--- | --- | --- | --- | --- | ---
< 300 Go | 4 vCPUs (2 sockets * 2 cœurs @ 2,5 GHz) | 4 GO | 600 GO | 7 à 10 Mo par seconde | 30/21 Mbps Mbps
300 à 600 Go | 8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz) | 6 GO | 600 GO | 11 à 15 Mo par seconde | 60 Mbps/42 Mbps
600 Go à 1 to | 12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz) | 8 GO | 600 GO | 16 à 20 Mo par seconde | 100 Mbps/70 Mbps
1 to > | Déployer un autre serveur de processus | | | | 

**Tableau 2**

Dans cet exemple : 

- Pénétration est la bande passante de téléchargement (intranet entre le serveur source et processus).
- Sortie est la bande passante de téléchargement (internet entre le serveur de traitement et serveur principal cible). Numéros de sortie supposent moyenne compression de serveur de processus de 30 %.
- Pour le cache un disque distinct du système d’exploitation minimale 128 Go est recommandé pour tous les serveurs de processus.
- Pour débit de disque cache le stockage suivant a été utilisé pour comparative : 8 disques associations de sécurité de 10/min avec une configuration RAID 10.

#### <a name="configuration-server-considerations"></a>Considérations sur la configuration serveur

Chaque serveur de configuration peut prendre en charge jusqu'à 100 machines d’origine avec les volumes de 3 et 4. Si votre déploiement est plus grande, nous vous recommandons de que déploiement d’un autre serveur de configuration. Reportez-vous au tableau 1 pour les propriétés de la machine virtuelle par défaut du serveur de configuration. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Considérations relatives au compte cible maître serveur et de stockage

L’espace de stockage pour chaque serveur principal cible inclut un disque du système d’exploitation, un volume de rétention et disques de données. Le lecteur de rétention gère le journal des modifications de disque pour la durée de la période de rétention définie dans le portail de récupération de Site.  Reportez-vous au tableau 1 pour les propriétés de la machine virtuelle du serveur maître cible. Le tableau 3 montre comment les disques de A4 sont utilisés.

**Instance** | **Disque du système d’exploitation** | **Rétention** | **Disques de données**
--- | --- | --- | ---
 | | **Rétention** | **Disques de données**
A4 standard | 1 disque (1 * 1023 Go) | 1 disque (1 * 1023 Go) | 15 disques (1023 * 15 Go)
D14 standard |  1 disque (1 * 1023 Go) | 1 disque (1 * 1023 Go) | 31 disques (1023 * 15 Go)
DS4 standard |  1 disque (1 * 1023 Go) | 1 disque (1 * 1023 Go) | 15 disques (1023 * 15 Go)

**Tableau 3**

Dépendant de la planification des capacités pour le serveur cible maître :

- Limitations et les performances de stockage azure
    - Le nombre maximal de hautement utilisé disques pour une machine virtuelle couche Standard, environ 40 (500/20 000 par seconde disque) dans un compte de stockage unique. En savoir sur [cibles extensibilité élevées de stockage standard sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) et pour [sccounts de stockage premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Taux de modification quotidien 
-   Stockage de volume de rétention.

Notez que :

- Une seule source ne peut pas s’étalent sur plusieurs comptes de stockage. Cela s’applique sur le disque de données atteindre les comptes de stockage sélectionnés lorsque vous configurez la protection. Le système d’exploitation et les disques rétention atteindre généralement le compte de stockage automatiquement déployé.
- Le volume de stockage de rétention nécessaire dépend de la fréquence de modification quotidien et le nombre de jours de rétention. Le stockage de rétention requis par serveur cible maître = évolution totale du code à partir de la source par jour * nombre de jours de rétention. 
- Chaque serveur cible maître n'a qu’un volume de rétention. Le volume de rétention est partagé par les disques associés au serveur maître cible. Par exemple :
    - S’il existe une machine source avec 5 disques et chaque disque génère des sorties 120 par (taille 8 Ko) sur la source, cela se traduit par 240 seconde de disque (2 opérations sur le disque cible par source IO). Sorties 240 par est comprise dans la Azure par limite de sorties par disque de 500.
    - Sur le volume de rétention, il se transforme en 120 * 5 = 600 sorties par et cela peuvent devenir un goulet. Dans ce scénario, une bonne stratégie serait d’ajouter des disques au volume de rétention et s’étalent il vers la droite, comme une configuration répartition RAID. Cela améliore les performances, car les sorties par sont réparties sur plusieurs lecteurs. Le nombre de lecteurs à ajouter au volume de rétention sera comme suit :
        - Total des sorties par d’environnement source / 500
        - Évolution totale du code par jour à partir d’environnement source (sans compression) / 287 go. Go 287 est le débit maximal pris en charge par un disque cible par jour. Cette métrique varie en fonction de la taille d’écriture avec un facteur de 8K, étant donné que dans ce cas 8K est le supposé taille d’écriture. Par exemple, si la taille d’écriture est 4 Ko débit sera 287/2. Et si la taille d’écriture est de 16 Ko débit sera 287 * 2.
- Le nombre de comptes de stockage requis = source total sorties par/10000.


## <a name="before-you-start"></a>Avant de commencer

**Composant** | **Configuration requise** | **Plus d’informations**
--- | --- | --- 
**Compte Azure** | Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
**Stockage Azure** | Vous devez posséder un compte de stockage Azure pour stocker les données dupliquées<br/><br/> Le compte doit être un [Compte de stockage Geo redondants Standard](../storage/storage-redundancy.md#geo-redundant-storage) ou un [Compte de stockage Premium](../storage/storage-premium-storage.md).<br/><br/> Il doit dans la même région en tant que le service de récupération de Site Azure et être associés à la même abonnement. Nous ne prennent pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) au sein de groupes de ressources.<br/><br/> Pour en savoir plus, lisez [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md)
**Réseau virtuel Azure** | Vous avez besoin d’un réseau virtuel Azure sur lequel le serveur de configuration et le serveur de base cible seront déployées. Il doit être dans le même abonnement et région en tant que l’archivage sécurisé récupération de Site Azure. Si vous souhaitez répliquer des données via une connexion VPN ou ExpressRoute le réseau virtuel Azure doit est relié à votre réseau local via une connexion ExpressRoute ou un réseau privé virtuel de Site à.
**Ressources Azure** | Vérifiez que vous disposez de suffisamment de ressources Azure pour déployer tous les composants. En savoir plus dans [La limite d’abonnements Azure](../azure-subscription-service-limits.md).
**Machines virtuelles Azure** | Machines virtuelles à protéger doivent être conformes avec les [conditions préalables Azure](site-recovery-best-practices.md).<br/><br/> **Nombre de disques**: 31 disques au maximum peuvent être prises en charge sur un seul serveur protégé<br/><br/> **Taille des disques**— capacité de disque individuel ne doivent pas être plus de 1023 Go<br/><br/> **Cluster**— serveurs groupées ne sont pas pris en charge<br/><br/> **Démarrage**— Extensible microprogramme UEFI (Unified Interface) / démarrage Extensible Interface(EFI) microprogramme n’est pas pris en charge<br/><br/> **Volumes**— Bitlocker volumes chiffrés ne sont pas pris en charge<br/><br/> **Noms des serveurs**, les noms doivent contenir de 1 à 63 caractères (lettres, chiffres et des traits d’union). Le nom doit commencer par une lettre ou un chiffre et se terminent par une lettre ou un chiffre. Après qu’une machine est protégée, vous pouvez modifier le nom Azure.
**Serveur de configuration** | Standard machine virtuelle de A3 basé sur une image de la galerie Azure Site récupération Windows Server 2012 R2 seront créé dans votre abonnement pour le serveur de configuration. Il est créé en tant que la première instance d’un service en nuage. Si vous sélectionnez Internet Public comme type de connexion pour le serveur de configuration du service cloud est créé avec une adresse IP publique réservée.<br/><br/> Le chemin d’installation doit être en caractères anglais uniquement.
**Serveur cible maître** | Machine virtuelle Azure, standard A4, D14 ou DS4.<br/><br/> Le chemin d’installation doit être en caractères anglais uniquement. Par exemple, le chemin d’accès doit être **/usr/local/ASR** pour un serveur principal cible Linux.
**Serveur de traitement** | Vous pouvez déployer le serveur de traitement sur physiques ou machine virtuelle exécutant Windows Server 2012 R2 avec les dernières mises à jour. Installer sur C: /.<br/><br/> Nous vous recommandons de que vous placez le serveur sur le même réseau et sous-réseau que les ordinateurs à protéger.<br/><br/> Installez VMware vSphere infrastructure du langage commun 5.5.0 sur le serveur de traitement. Le composant VMware vSphere infrastructure du langage commun est nécessaire sur le serveur de processus pour découvrir machines virtuelles gérées par un serveur vCenter ou machines virtuelles s’exécutant sur un hôte ESXi.<br/><br/> Le chemin d’installation doit être en caractères anglais uniquement.<br/><br/> Système de fichiers Réf n’est pas pris en charge.
**VMware** | Un serveur de vCenter VMware gestion votre hyperviseurs vSphere VMware. Il doit s’exécuter vCenter version 5.1 ou 5.5 avec les dernières mises à jour.<br/><br/> Un ou plusieurs hyperviseurs vSphere contenant les machines virtuelles VMware que vous voulez protéger. L’hyperviseur doit s’exécuter ESX/ESXi version 5.1 ou 5.5 avec les dernières mises à jour.<br/><br/> Machines virtuelles VMware doit avoir installé et exécuté les outils VMware. 
**Machines Windows** | Serveurs physiques protégés ou machines virtuelles VMware exécutant Windows ont un nombre d’exigences.<br/><br/> A pris en charge le système d’exploitation 64 bits : **Windows Server 2012 R2**, **Windows Server 2012**, ou **Windows Server 2008 R2 avec au moins SP1**.<br/><br/> Le nom d’hôte, les points de montage, les noms de périphérique, chemin d’accès du système de Windows (par exemple : C:\Windows) doivent être en anglais uniquement.<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\.<br/><br/> Seuls les disques de base sont prises en charge. Disques dynamiques ne sont pas pris en charge.<br/><br/> Règles de pare-feu sur les ordinateurs protégés doivent lui permettre d’accéder aux serveurs cible configuration et le masque dans Azure.p ><p>Vous devez fournir un compte d’administrateur (doit être un administrateur local sur l’ordinateur Windows) pour l’installation push le Service mobilité sur des serveurs Windows. Si le compte fourni est un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur à distance sur l’ordinateur local. Pour faire cela ajouter l’entrée de Registre LocalAccountTokenFilterPolicy DWORD avec une valeur de 1 sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Pour ajouter l’entrée de Registre à partir d’une infrastructure du langage commun ouvrir cmd ou powershell et entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [En savoir plus](https://msdn.microsoft.com/library/aa826699.aspx) sur le contrôle d’accès.<br/><br/> Après le basculement, si vous voulez vous connecter à Windows machines virtuelles dans Azure avec Bureau à distance vous assurer que Bureau à distance est activée pour l’ordinateur local. Si vous vous connectez pas sur réseau privé virtuel, les règles de pare-feu doivent autoriser les connexions de bureau à distance via internet.
**Ordinateurs Linux** | Un système d’exploitation 64 bits pris en charge : **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible chapeau rouge ou un Unbreakable entreprise noyau version 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Règles de pare-feu sur les ordinateurs protégés doivent lui permettre d’atteindre la configuration et les serveurs cibles maître dans Azure.<br/><br/> fichiers/etc/hosts sur machines protégées doivent contenir des entrées correspondant au nom d’hôte local aux adresses IP associées à toutes les cartes réseau <br/><br/> Si vous voulez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un Secure Shell client (ssh), vérifiez que le service banque d’informations sécurisé Shell sur l’ordinateur protégé est défini pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent un ssh connexion à ce dernier.<br/><br/> Nom d’hôte, les points de montage, noms de périphériques et chemins d’accès du système Linux et les noms de fichier (par exemple/etc / ; /usr) doivent être en anglais uniquement.<br/><br/> Protection peut être activée pour les ordinateurs en local avec le stockage suivant :-<br>Système de fichiers : EXT3, ETX4, ReiserFS, XFS<br>Périphérique logiciel chemins multiples Mapper (chemins multiples)<br>Gestionnaire de volume : LVM2<br>Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge.
**Tiers** | Certains composants de déploiement dans ce scénario dépendent des logiciels tiers pour fonctionner correctement. Pour obtenir une liste complète, voir [informations et des informations relatives aux logiciels de tiers](#third-party)


### <a name="network-connectivity"></a>Connectivité réseau

Vous avez deux possibilités lors de la configuration de la connectivité réseau entre votre site local et le réseau virtuel Azure déployés sur laquelle les composants d’infrastructure (serveur de configuration, serveurs cibles maître). Vous devez choisir des options de connectivité réseau à utiliser avant de déployer votre serveur de configuration. Vous devez sélectionnez ce paramètre au moment de déploiement. Il ne peut pas être modifié ultérieurement.

**Internet :** Communication et la réplication des données entre les serveurs locaux (serveur de traitement, machines protégées) et les serveurs de composants Azure infrastructure (serveur de configuration, serveur cible maître) se produit via une connexion SSL/TLS sécurisée locales aux points de terminaison publics sur les serveurs cible configuration et le masque. (La seule exception est la connexion entre le serveur de traitement et le serveur cible maître sur le port TCP 9080 n’est pas chiffré. Seules les informations de contrôle relatives au protocole de réplication pour la configuration de réplication sont sur cette connexion.)

![Déploiement de diagramme internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: Communication et la réplication des données entre les serveurs locaux (serveur de traitement, machines protégées) et les serveurs de composants Azure infrastructure (serveur de configuration, serveur cible maître) qui se passe sur une connexion VPN entre votre réseau local et le réseau virtuel Azure sur lequel le serveur de configuration et les serveurs cibles maître sont déployés. Vérifiez que votre réseau local est connecté au réseau virtuel Azure par un ExpressRoute ou une connexion VPN de site à.

![Diagramme de déploiement VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Étape 1 : Créer un archivage sécurisé

1. Connectez-vous au [portail de gestion](https://portal.azure.com).


2. Développez **Data Services** > **Services de récupération** et cliquez sur **L’archivage sécurisé récupération de Site**.


3. Cliquez sur **créer une nouvelle** > **Création rapide**.

4. Dans la zone **nom**, entrez un nom convivial pour identifier l’archivage sécurisé.

5. Dans **la région**, sélectionnez la région géographique pour l’archivage sécurisé. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Cliquez sur **créer l’archivage sécurisé**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Vérifiez la barre d’état pour confirmer que l’archivage sécurisé a été créée. L’archivage sécurisé est indiquée comme étant **actif** sur la page principale dans **Les Services de récupération** .

## <a name="step-2-deploy-a-configuration-server"></a>Étape 2 : Déployer un serveur de configuration

### <a name="configure-server-settings"></a>Configurer les paramètres de serveur

1. Dans la page **Services de récupération** , cliquez sur l’archivage sécurisé pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Dans la liste déroulante, sélectionnez **entre un site en local avec des serveurs VMware/physique et Azure**.
3. **Préparer** les ressources Target(Azure) cliquez sur **Déployer un serveur de Configuration**.

    ![Déploiement de serveur de configuration](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. Dans les **Nouveaux détails de la Configuration serveur** spécifiez :

    - Un nom pour le serveur de configuration et les informations d’identification pour vous connecter à celle-ci.
    - Dans le type de connectivité réseau, faites glisser vers le bas sélectionnez **VPN**ou **Internet Public** . Notez que vous ne pouvez pas modifier ce paramètre lorsqu’il est appliqué.
    - Sélectionnez le réseau Azure sur lequel le serveur doit se trouver. Si vous utilisez marque VPN que le réseau Azure est connecté à votre réseau local comme prévu. 
    - Spécifiez l’adresse IP interne et le sous-réseau doivent être affectée sur le serveur. Notez que les quatre premières adresses IP dans n’importe quel sous-réseau sont réservées pour un usage interne Azure. Utilisez une autre adresse IP disponible.
    
    ![Déploiement de serveur de configuration](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Lorsque vous cliquez sur **OK** une machine de virtuelle A3 standard basée sur une image de la galerie Azure Site récupération Windows Server 2012 R2 est créés dans votre abonnement pour le serveur de configuration. Il est créé en tant que la première instance d’un service en nuage. Si vous avez sélectionné pour vous connecter via internet le service en nuage est créé avec une adresse IP publique réservée. Vous pouvez surveiller l’avancement dans l’onglet **tâches** .

    ![Surveiller l’avancement](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Si vous vous connectez via internet, après le serveur de configuration est déployé note l’adresse IP affectées à celui-ci dans la page **Machines virtuelles** dans le portail Azure. Puis sur les **points de terminaison de** note onglet le port HTTPS public mappées privé port 443. Vous avez besoin de ces informations ultérieurement lorsque vous enregistrez la cible maître et les serveurs de traitement avec le serveur de configuration. Le serveur de configuration est déployé avec ces points de terminaison :

    - HTTPS : Un port public est utilisé pour coordonner la communication entre les serveurs de composants et Azure via internet. Le port 443 privé est utilisé pour coordonner la communication entre les serveurs de composants et Azure sur réseau privé virtuel.
    - Personnalisé : Un port public est utilisé pour la communication d’outil retour arrière via internet. Port privé 9443 est utilisé pour la communication d’outil retour arrière sur réseau privé virtuel.
    - PowerShell : Port privé 5986
    - Bureau à distance : privé Port3389
    
    ![Points de terminaison de machine virtuelle](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Ne pas supprimer ou modifier le numéro de port public ou privé des points de terminaison créé au cours du déploiement de serveur de configuration.

Le serveur de configuration est déployé dans un service cloud Azure créée automatiquement avec une adresse IP réservée. L’adresse réservée est nécessaire pour s’assurer que l’adresse IP de configuration serveur cloud service reste la même réinitialisations des machines virtuelles (y compris le serveur de configuration) sur le service cloud. L’adresse IP réservée il faudra manuellement non réservée lorsque le serveur de configuration est désactivé ou elle allez rester réservé. Il existe une limite par défaut de 20 adresses IP publiques réservées par abonnement. [En savoir plus](../virtual-network/virtual-networks-reserved-private-ip.md) sur les adresses IP réservées. 

### <a name="register-the-configuration-server-in-the-vault"></a>Enregistrer le serveur de configuration dans l’archivage sécurisé

1. Dans la page de **Démarrage rapide** , cliquez sur **Préparer les ressources cible** > **télécharger une clé d’enregistrement**. Le fichier de clé est généré automatiquement. Il est valable pendant 5 jours après sa création. Copier dans le serveur de configuration.
2. Machines **virtuelles** sélectionnez le serveur de configuration dans la liste de machines virtuelles. Ouvrez l’onglet **tableau de bord** , puis cliquez sur **se connecter**. **Ouvrir** le fichier téléchargé RDP à se connecter au serveur de configuration à l’aide de bureau à distance. Si vous utilisez VPN, utilisez l’adresse IP interne (l’adresse que vous avez spécifié lorsque vous avez déployé le serveur de configuration) pour une connexion Bureau à distance à partir du site local. L’Assistant Configuration de serveur de Configuration Azure Site récupération s’exécute automatiquement lorsque vous vous connectez pour la première fois.

    ![Enregistrement](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. Dans **Installation de logiciels tiers** , cliquez sur **J’accepte** pour télécharger et installer MySQL.

    ![Installation de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. Dans **Détails du serveur MySQL** créer des informations d’identification pour vous connecter à l’instance du serveur MySQL.

    ![Informations d’identification MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Dans **Les paramètres d’Internet** spécifiez comment le serveur de configuration sera-t-il connecté à internet. Notez que :

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur.
    - Lorsque vous cliquez sur **suivant** un test est exécuté pour vérifier la connexion de proxy.
    - Si vous n’utilisez pas un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez entrer les détails de proxy, y compris l’adresse, port et informations d’identification.
    - Les URL suivantes doivent être accessibles via le serveur proxy :
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Si vous avez basé sur l’adresse IP règles de pare-feu vous assurer que les règles sont définis pour autoriser la communication à partir du serveur de configuration aux adresses IP décrit dans les [Plages d’adresses IP Azure centre de données](https://msdn.microsoft.com/library/azure/dn175718.aspx) et HTTPS protocol (443). Vous devez plages d’adresses IP liste blanche de la région Azure que vous envisagez d’utiliser et que des États-Unis Ouest.

    ![Inscription du proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. Dans les **Paramètres de localisation du Message d’erreur fournisseur** spécifiez langue dans laquelle vous souhaitez que les messages d’erreur s’affichent.

    ![Enregistrement de message d’erreur](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. Dans **Enregistrement de récupération de Site Azure** Parcourir et sélectionnez le fichier de clé que vous avez copié sur le serveur.

    ![Enregistrement du fichier de clé](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Sur la dernière page de l’Assistant, sélectionnez les options suivantes :

    - Sélectionnez le **Menu de lancement de boîte de dialogue compte gestion** pour indiquer que la boîte de dialogue Gérer les comptes doit ouvrir une fois que vous avez terminé l’Assistant.
    - Sélectionnez **créer une icône de bureau pour Cspsconfigtool** pour ajouter un raccourci sur le bureau sur le serveur de configuration afin que vous puissiez ouvrir la boîte de dialogue **Gérer les comptes** à tout moment sans devoir réexécutez l’Assistant.

    ![Terminer votre inscription](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Cliquez sur **Terminer** pour fermer l’Assistant. Un mot de passe est générée. Copier dans un emplacement sécurisé. Vous en aurez besoin pour authentifier et enregistrer le processus et les serveurs cibles maître avec le serveur de configuration. Il est également utilisé pour garantir l’intégrité des canaux dans les communications de serveur de configuration. Vous pouvez recréer le mot de passe, mais vous devrez puis réenregistrer la cible maître et traiter les serveurs qui utilisent le nouveau mot de passe.

    ![Mot de passe](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Après l’enregistrement du serveur de configuration apparaîtront dans la page **Configuration de serveurs** dans l’archivage sécurisé.

### <a name="set-up-and-manage-accounts"></a>Configurer et gérer les comptes

Au cours du déploiement récupération de Site demande des informations d’identification pour les actions suivantes :

- Un compte VMware thatSite récupération pouvez automatiquement découverte machines virtuelles sur serveurs vCenter ou vSphere hosts. 
- Lorsque vous ajoutez des machines pour la protection, récupération de Site permettant d’installer le service mobilité dessus.

Une fois que vous avez enregistré le serveur de configuration, vous pouvez ouvrir la boîte de dialogue **Gérer les comptes** pour ajouter et gérer les comptes doivent être utilisés pour ces actions. Il existe deux manières de procéder :

- Ouvrez le raccourci que vous avez choisi de créé pour la boîte de dialogue dans la dernière page du programme d’installation pour le serveur de configuration (cspsconfigtool).
- Ouvrir la boîte de dialogue fin de l’installation de serveur de configuration.

1. Dans **Gérer les comptes** , cliquez sur **Ajouter un compte**. Vous pouvez également modifier et supprimer des comptes existants.

    ![Gérer les comptes](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. Dans **Détails du compte** , spécifiez un nom de compte à utiliser dans Azure et informations d’identification (nom d’utilisateur/de domaine). 

    ![Gérer les comptes](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Se connecter au serveur de configuration 

Il existe deux moyens de se connecter au serveur de configuration :

- Sur un site à site VPN ou connexion ExpressRoute
- Via internet 

Notez que :

- Une connexion internet utilise les points de terminaison de la machine virtuelle conjointement avec l’adresse IP virtuelle du serveur.
- Une connexion VPN utilise l’adresse IP interne du serveur ainsi que les ports privé de point de terminaison.
- Il est une décision unique à décider si vous souhaitez vous connecter (contrôle et la réplication de données) à partir de vos serveurs en local pour différents serveurs composant (serveur de configuration, serveur maître cible) en cours d’exécution dans Azure sur une connexion VPN ou sur internet. Vous ne pouvez pas modifier ce paramètre par la suite. Si vous ne vous devrez redéployez le scénario et de restaurer vos ordinateurs.  


## <a name="step-3-deploy-the-master-target-server"></a>Étape 3 : Déployer le serveur cible maître

1. Cliquez sur **Préparer les ressources Target(Azure)** > **serveur de cible maître déployer**.
2. Spécifier les détails du serveur cible maître et les informations d’identification. Le serveur sera déployé dans le même réseau Azure que le serveur de configuration. Lorsque vous cliquez sur pour effectuer une machine virtuelle Azure sera créée avec une image de la galerie Windows ou Linux.

    ![Paramètres du serveur cible](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Notez que les quatre premières adresses IP dans n’importe quel sous-réseau sont réservées pour un usage interne Azure. Spécifier une autre adresse IP disponible.

>[AZURE.NOTE] Sélectionnez DS4 Standard lors de la configuration de la protection par pour les charges de travail qui nécessitent des performances e/s élevées et faible latence pour héberger les charges de travail intensives e/s à l’aide du [Compte de stockage Premium](../storage/storage-premium-storage.md).


3. Un Windows maître serveur cible machine virtuelle est créée avec ces points de terminaison. Notez que les points de terminaison publics sont créées uniquement si votre connexion via internet.

    - Personnalisé : Port Public utilisé par le serveur de traitement d’envoyer des données de la réplication via internet. Port privé 9443 est utilisé par le serveur de traitement pour envoyer des données de réplication sur le serveur cible maître sur réseau privé virtuel.
    - Custom1 : Port Public utilisé par le serveur de traitement pour envoyer des métadonnées via internet. Port privé 9080 est utilisé par le serveur de traitement d’envoyer des métadonnées sur le serveur cible maître via VPN.
    - PowerShell : Port privé 5986
    - Bureau à distance : privé Port3389

4. Création d’un serveur de base cible Linux machine virtuelle avec ces points de terminaison. Notez que les points de terminaison publics sont créées uniquement si vous vous connectez via internet.

    - Personnalisé : Port Public utilisé par le serveur processus d’envoyer des données de la réplication via internet. Port privé 9443 est utilisé par le serveur de traitement pour envoyer des données de réplication sur le serveur cible maître sur réseau privé virtuel.
    - Custom1 : Port Public est utilisé par le serveur de traitement d’envoyer des métadonnées via internet. Port privé 9080 est utilisé par le serveur de traitement d’envoyer des métadonnées sur le serveur cible maître via VPN
    - SSH : Port privé 22

    >[AZURE.WARNING] Ne pas supprimer ou modifier le numéro de port public ou privé d’un points de terminaison créé au cours du déploiement de serveur maître cible.

5. **Machines virtuelles** attente pour la machine virtuelle Démarrer.

    - S’il s’agit d’une note de serveur Windows vers le bas les détails de bureau à distance.
    - S’il est un serveur Linux et vous vous connectez sur réseau privé virtuel Notez l’adresse IP interne de la machine virtuelle. Si vous vous connectez via internet Notez l’adresse IP.

6.  Se connecter au serveur pour terminer l’installation et l’enregistrer avec le serveur de configuration. 
7.  Si vous exécutez Windows :

    1. Établir une connexion Bureau à distance sur l’ordinateur virtuel. La première fois que vous ouvrez une session sur un script s’exécutera dans une fenêtre de PowerShell. Ne fermez-le. Une fois l’outil de configuration de l’Agent hôte s’ouvre automatiquement pour enregistrer le serveur.
    2. Dans la **Configuration de l’Agent hôte** spécifier l’adresse IP interne du serveur de configuration et le port 443. Vous pouvez utiliser l’adresse interne et le port 443 de privé même si vous ne vous connectez pas sur réseau privé virtuel, car la machine virtuelle est connectée au réseau Azure même en tant que le serveur de configuration. Laisser **Utiliser HTTPS** activé. Entrez le mot de passe pour le serveur de configuration que vous avez noté précédemment. Cliquez sur **OK** pour enregistrer le serveur. Vous pouvez ignorer les options NAT. Elles ne sont pas utilisées.
    3. Si votre demande de lecteur rétention estimée a plus de 1 To, vous pouvez configurer le volume de rétention (r) à l’aide d’un disque virtuel et les [espaces de stockage](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Serveur de base cible de Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Si vous exécutez Linux :
    1. Vérifiez que vous avez installé la dernière Linux Integration Services (liste de verticale) installé avant d’installer le serveur maître cible. Vous pouvez trouver la dernière version de liste de verticale ainsi que des instructions sur l’installation [ici](https://www.microsoft.com/download/details.aspx?id=46842). Redémarrez l’ordinateur une fois que la liste de verticale installé.
    2. Dans **les ressources préparer cible (Azure)** cliquez sur **télécharger et installer des logiciels supplémentaires (uniquement pour Linux Masque cible Server)**. Copiez le fichier tar téléchargé sur l’ordinateur virtuel à l’aide d’un client sftp. Vous pouvez également vous pouvez ouvrir une session sur le serveur de base cible linux déployée et *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* permet de télécharger le fichier.
    2. Ouvrez une session sur le serveur à l’aide d’un client Secure Shell. Si vous êtes connecté au réseau Azure sur réseau privé virtuel, utilisez l’adresse IP interne. Sinon, utilisez l’adresse IP externe et le point de terminaison public SSH.
    3. Extraire les fichiers dans le programme d’installation compressés en exécutant : **tar – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![serveur cible maître Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Vérifiez que vous êtes dans l’annuaire dans lequel vous avez extrait le contenu du fichier tar.
    5. Copier le mot de passe de serveur de configuration dans un fichier local à l’aide de la commande * *écho* `<passphrase>` * > passphrase.txt**
    6. Exécutez la commande «**sudo. / Installer -t héberger les deux - a -R /usr/local/ASR -d MasterTarget -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt** ».

    ![Inscrire un serveur cible](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Patientez quelques minutes (10 à 15) et dans la page, vérifiez que le serveur maître cible est répertorié comme enregistré sur les **serveurs** > onglet **Détails du serveur** de**Configuration de serveurs** . Si vous exécutez Linux et qu’il n’a pas été Registre réexécutez l’hôte outil de configuration à partir /usr/local/ASR/Vx/bin/hostconfigcli. Vous devez définir des autorisations d’accès en exécutant chmod en tant que racine.

    ![Vérifier le serveur cible](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Il peut prendre jusqu'à 15 minutes après que l’inscription est terminée pour le serveur cible maître soit répertoriée dans le portail. Pour mettre à jour immédiatement, cliquez sur **Actualiser** dans la page **Configuration de serveurs** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Étape 4 : Déployer le serveur de processus en local

Avant de commencer, nous vous recommandons de configurer une adresse IP statique sur le serveur de processus afin qu’il a systématiquement à permanente réinitialisations.

1. Cliquez sur Quick Start > **installer processus serveur local** > **télécharger et installer le serveur de traitement**.

    ![Installer le serveur de traitement](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copiez le fichier zip téléchargé sur le serveur sur lequel vous allez installer le serveur de traitement. Le fichier zip contient deux fichiers d’installation :

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Décompressez l’archive et copiez les fichiers d’installation vers un emplacement sur le serveur.
4. Exécuter la **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** installation fichier et suivez les instructions. Cette procédure installe tierce composants nécessaires pour le déploiement.
5. Puis exécutez **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Dans la page **Mode de serveur** , sélectionnez **Serveur de traitement**.
7. Dans la page **Détails de l’environnement** , procédez comme suit :


    - Si vous voulez protéger virtuel VMware machines cliquez sur **Oui**
    - Si vous voulez uniquement protéger des serveurs physiques et donc n’est pas nécessaire vCLI VMware installé sur le serveur de traitement. Cliquez sur **non** et continuer.

8. Notez les points suivants lorsque vous installez VMware vCLI :

    - **Seul VMware vSphere infrastructure du langage commun 5.5.0 est pris en charge**. Le serveur de traitement ne fonctionne pas avec d’autres versions ou mises à jour de vSphere infrastructure du langage commun.
    - Télécharger vSphere infrastructure du langage commun 5.5.0 à partir de [ici.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Si vous avez installé vSphere infrastructure du langage commun juste avant que vous avez démarré l’installation du serveur de processus et le programme d’installation ne le détecte pas, patientez jusqu'à cinq minutes avant d’essayer à nouveau le programme d’installation. Ainsi que toutes les variables d’environnement nécessaires pour la détection d’infrastructure du langage commun vSphere ont été initialisés correctement.

9.  Dans **Sélection de carte réseau pour serveur de traitement** activez la carte réseau que le serveur de traitement doit utiliser.

    ![Sélectionner la carte](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. **Détails de serveur de Configuration**:

    - Pour l’adresse IP et le port, si vous vous connectez sur réseau privé virtuel, spécifiez l’adresse IP interne du serveur de configuration et 443 pour le port. Dans le cas contraire spécifier l’adresse IP publique virtuelle et mappée point de terminaison HTTP public.
    - Tapez le mot de passe du serveur de configuration.
    - Désactivez la **signature de logiciel service mobilité vérifier** si vous souhaitez désactiver la vérification lorsque vous utilisez push automatique pour installer le service. Vérification de la signature doit connectivité internet à partir du serveur de traitement.
    - Cliquez sur **suivant**.

    ![Serveur de configuration de Registre](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. Dans le **lecteur d’Installation sélectionnez** sélectionner un lecteur de cache. Le serveur de traitement doit un lecteur de cache au moins 600 Go d’espace libre. Cliquez sur **installer**. 

    ![Serveur de configuration de Registre](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Notez que vous devrez peut-être redémarrer le serveur pour terminer l’installation. Dans le **Serveur de Configuration** > **Détails du serveur** Vérifiez que le serveur de traitement apparaît et qu’il a été enregistré dans l’archivage sécurisé.

>[AZURE.NOTE]Il peut prendre jusqu'à 15 minutes après que l’inscription est terminée pour le serveur de processus apparaissent comme indiqué sous le serveur de configuration. Pour mettre à jour immédiatement, actualiser le serveur de configuration en cliquant sur le bouton Actualiser en bas de la page configuration de serveur
 
![Validation du serveur de processus](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Si vous n’avez pas désactiver la vérification de signature pour le service mobilité lorsque vous avez enregistré le serveur de traitement vous pouvez le faire ultérieurement comme suit :

1. Se connecter au serveur de processus en tant qu’administrateur et ouvrez le fichier C:\pushinstallsvc\pushinstaller.conf à modifier. Sous la section **[PushInstaller.transport]** ajoutez cette ligne : **SignatureVerificationChecks = « 0 »**. Enregistrez et fermez le fichier.
2. Redémarrez le service InMage PushInstall.


## <a name="step-5-update-site-recovery-components"></a>Étape 5 : Mise à jour Site récupération composants

Composants de récupération de site sont mises à jour de temps à autre. Lorsque les nouvelles mises à jour sont disponibles, vous devez les installer dans l’ordre suivant :

1. Serveur de configuration
2. Serveur de traitement
3. Serveur cible maître
4. Retour arrière outil (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Obtenir et installer les mises à jour


1. Vous pouvez obtenir des mises à jour de la configuration, de processus et les serveurs cibles maître dans le **tableau de bord**de récupération de Site. Pour l’installation de Linux extraire les fichiers dans le programme d’installation compressés et exécutez la commande « sudo. / Installer » pour installer la mise à jour.
2. [Téléchargez](http://go.microsoft.com/fwlink/?LinkID=533813) la dernière mise à jour la tool(vContinuum) retour arrière.
3. Si vous exécutez machines virtuelles ou des serveurs physiques qui ont déjà installé le service mobilité, vous pouvez obtenir les mises à jour pour le service comme suit :

    - **Option 1**: télécharger des mises à jour :
        - [Windows Server (64 bits uniquement)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 (64 bits uniquement)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (64 bits uniquement)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (64 bits uniquement)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Après mise à jour le serveur de traitement la version mise à jour du service mobilité seront disponible dans le dossier C:\pushinstallsvc\repository sur le serveur de traitement.
    - **Option 2**: Si vous avez un ordinateur avec une version antérieure du service mobilité installé, vous pouvez automatiquement mettre à niveau du service mobilité sur l’ordinateur à partir du portail de gestion.

        1. Assurez-vous que le serveur de traitement est mis à jour.
        2. Assurez-vous que l’ordinateur protégé est conforme à la [Configuration requise](#install-the-mobility-service-automatically) pour diffuser automatiquement le service mobilité, pour que la mise à jour fonctionne comme prévu.
        2. Sélectionnez le groupe de protection, mettez en surbrillance l’ordinateur protégé, puis cliquez sur **service mobilité de mise à jour**. Ce bouton n’est disponible que si une version plus récente du service mobilité. 

            ![Sélectionnez serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Sélectionnez les comptes spécifier le compte d’administrateur à utiliser pour mettre à jour le service mobilité sur le serveur protégé. Cliquez sur OK et attendez la fin du travail déclenché.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Étape 6 : Ajouter des serveurs vCenter ou vSphere hosts

1. Cliquez sur **les serveurs** > **Serveurs de Configuration** > serveur de configuration >**Ajout vCenter Server** pour ajouter un hôte serveur ou vSphere vCenter.

    ![Sélectionnez serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Spécifier les détails pour le serveur ou l’hôte, puis sélectionnez le serveur de processus qui permet de découvrir il.

    - Si le serveur vCenter n’est pas exécuté sur le port 443 par défaut, spécifiez le numéro de port sur lequel s’exécute le serveur vCenter.
    - Le serveur de traitement doit se trouver sur le même réseau que l’hôte de serveur/vSphere vCenter et doit avoir VMware vSphere infrastructure du langage commun 5.5.0 installé.

    ![paramètres du serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Une fois terminée la découverte le serveur vCenter apparaîtront sous les détails de serveur de configuration.

    ![paramètres du serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Si vous utilisez un compte autre qu’un administrateur pour ajouter le serveur ou l’hôte, vérifiez que le compte dispose des autorisations suivantes :

    - comptes vCenter doivent avoir centre de données, magasin de données, dossier, hôte, réseau, ressource, stockage affichages, machine virtuelle et les privilèges de commutateur distribué vSphere activés.
    - vSphere héberger des comptes doivent disposent centre de données, magasin de données, dossier, hôte, réseau, ressource, machine virtuelle vSphere commutateur distribué des droits activés



## <a name="step-7-create-a-protection-group"></a>Étape 7 : Créer un groupe de protection

1. Ouvrir les **Éléments protégé** > **Groupe Protection** > **créer un groupe protection**.

    ![Créer le groupe de protection](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Dans la page **Spécifier les paramètres du groupe Protection** spécifiez un nom pour le groupe et sélectionnez le serveur de configuration sur lequel vous voulez créer le groupe.

    ![Paramètres de protection de groupe](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Dans la page **Spécifier les paramètres de réplication** configurer les paramètres de réplication qui seront utilisés pour tous les ordinateurs dans le groupe.

    ![Réplication de groupe protection](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Paramètres :
    - **Machine virtuelle à plusieurs cohérence**: Si vous désactivez ce qu’il crée des points de récupération cohérents avec les applications partagées sur les ordinateurs dans le groupe de protection. Ce paramètre est plus pertinent lorsque tous les ordinateurs dans le groupe de protection utilisent la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Disponible uniquement pour les serveurs Windows.
    - **Seuil RPO**: alertes seront générées lors de la réplication de protection des données continues RPO dépasse la valeur de seuil RPO configurée.
    - **Rétention des points de récupération**: Spécifie la fenêtre de rétention. Machines protégées peuvent être récupérés à tout moment dans cette fenêtre.
    - **Application cohérente fréquence instantané**: Spécifie la fréquence de création des points de récupération contenant des instantanés cohérents avec les applications.

Vous pouvez surveiller le groupe de protection elles sont créées dans la page **Éléments protégé** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Étape 8 : Configurer des ordinateurs à protéger

Vous devez installer le service mobilité sur machines virtuelles et des serveurs physiques à protéger. Vous pouvez le faire de deux façons :

- Notifications push et installez le service sur chaque ordinateur à partir du serveur de processus automatiquement.
- Installer manuellement le service. 

### <a name="install-the-mobility-service-automatically"></a>Installer automatiquement le service mobilité

Lorsque vous ajoutez des ordinateurs à un groupe de protection le service mobilité est automatiquement poussé et installé sur chaque ordinateur par le serveur de traitement. 

**Push installer automatiquement le service mobilité sur des serveurs Windows :** 

1. Installer les dernières mises à jour pour le serveur de processus comme décrit dans [étape 5 : installer les dernières mises à jour](#step-5-install-latest-updates)et vérifiez que le serveur de processus est disponible. 
2. Vérifiez la connectivité réseau entre l’ordinateur source et le serveur de processus est et que l’ordinateur source est accessible à partir du serveur de processus.  
3. Configurer le pare-feu Windows pour autoriser le **fichier et le partage d’imprimante** et **Windows Management Instrumentation**. Sous paramètres du pare-feu Windows, sélectionnez l’option « Autoriser une application ou une fonctionnalité via le pare-feu », puis sélectionnez les applications comme le montre l’image ci-dessous. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

    ![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. Le compte utilisé pour effectuer l’installation push doit être placé dans le groupe Administrateurs sur l’ordinateur à protéger. Ces informations sont uniquement utilisées pour l’installation push du service mobilité et vous devez les fournir lorsque vous ajoutez un ordinateur à un groupe de protection.
5. Si le compte fourni n’est pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur à distance sur l’ordinateur local. Pour faire cela ajouter l’entrée de Registre LocalAccountTokenFilterPolicy DWORD avec une valeur de 1 sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Pour ajouter l’entrée de Registre à partir d’une infrastructure du langage commun ouvrir cmd ou powershell et entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Push installer automatiquement le service mobilité sur des serveurs Linux :**

1. Installer les dernières mises à jour pour le serveur de processus comme décrit dans [étape 5 : installer les dernières mises à jour](#step-5-install-latest-updates)et vérifiez que le serveur de processus est disponible.
2. Vérifiez la connectivité réseau entre l’ordinateur source et le serveur de processus est et que l’ordinateur source est accessible à partir du serveur de processus.  
3. Vérifiez que le compte est un utilisateur racine sur le serveur Linux source.
4. Vérifiez que le fichier/etc/hosts sur le serveur Linux source contienne des entrées correspondant au nom d’hôte local aux adresses IP associées à toutes les cartes réseau.
5. Installer la dernière openssh, openssh server, les logiciels d’openssl sur l’ordinateur à protéger.
6. Vérifiez que SSH est activée et en cours d’exécution sur le port 22. 
7. Activez l’authentification SFTP sous-système et mot de passe dans le fichier sshd_config comme suit : 

    - a) se connecter en tant que racine.
    - b) dans le fichier /etc/ssh/sshd_config de fichier, recherchez la ligne qui commence par **PasswordAuthentication**.
    - c) ne commentez pas la ligne et remplacez la valeur « non » à « Oui ».

        ![Mobilité Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) Recherchez la ligne qui commence par sous-système et supprimez la ligne.
    
        ![Mobilité de push Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Vérifier que la variante source machine Linux est pris en charge. 
 
### <a name="install-the-mobility-service-manually"></a>Installer manuellement le service mobilité

Les logiciels utilisés pour installer le service mobilité sont sur le serveur de processus dans C:\pushinstallsvc\repository. Ouvrez une session sur le serveur de traitement et copiez le package d’installation approprié dans la machine source basée sur le tableau ci-dessous :-

| Système d’exploitation source                               | Mobilité package de service du serveur de traitement                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (64 bits uniquement)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (64 bits uniquement)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Pour installer le service mobilité manuellement à partir de Windows server**, procédez comme suit :

1. Copiez le package **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** le chemin du répertoire processus serveur indiqué dans le tableau ci-dessus vers l’ordinateur source.
2. Installez le service mobilité en exécutant le fichier exécutable sur l’ordinateur source.
3. Suivez les instructions du programme d’installation.
4. Sélectionnez **service mobilité** que le rôle et cliquez sur **suivant**.
    
    ![Installer le service de mobilité](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Laissez l’annuaire de l’installation en tant que le chemin d’installation par défaut et cliquez sur **installer**.
6. Dans la **Configuration de l’Agent hôte** spécifier l’adresse IP et le port HTTPS du serveur de configuration.

    - Si vous vous connectez via internet spécifier l’adresse IP publique virtuelle et le point de terminaison HTTPS public comme port.
    - Si vous vous connectez sur réseau privé virtuel Spécifiez l’adresse IP interne et 443 pour le port. Laisser **Utiliser HTTPS** activé.

    ![Installer le service de mobilité](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Spécifiez le mot de passe de serveur de configuration, puis cliquez sur **OK** pour enregistrer le service mobilité avec le serveur de configuration.

**Exécuter à partir de la ligne de commande :**

1. Copier le mot de passe à partir de la CX vers le fichier « C:\connection.passphrase » sur le serveur et exécutez la commande suivante. Dans notre exemple CX i 104.40.75.37 et le port HTTPS est 62519 :

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installer le service mobilité manuellement sur un serveur Linux**:

1. Copiez l’archive tar appropriés en fonction du tableau ci-dessus, à partir du serveur de processus vers l’ordinateur source.
2. Ouvrez un programme shell et extraire l’archive compressé tar dans un chemin d’accès local en exécutant`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Créer un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar en entrant *`echo <passphrase> >passphrase.txt`* de shell.
4. Installer le service mobilité en entrant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifier l’adresse IP et le port :

    - Si vous vous connectez sur le serveur de configuration via internet spécifiez le serveur virtuel public adresse IP de configuration et le public point de terminaison HTTPS dans `<IP address>` et `<port>`.
    - Si vous vous connectez via une connexion VPN spécifier l’adresse IP interne et 443.

**Exécuter à partir de la ligne de commande**:

1. Copier le mot de passe à partir de la CX vers le fichier « passphrase.txt » sur le serveur et exécutez cette commandes. Dans notre exemple CX i 104.40.75.37 et le port HTTPS est 62519 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Installer sur le serveur cible :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Lorsque vous ajoutez à un groupe de protection des ordinateurs qui exécutent déjà une version appropriée du service mobilité puis l’installation push est ignorée.


## <a name="step-9-enable-protection"></a>Étape 9 : Activer la protection

Pour activer la protection ajouter machines virtuelles et des serveurs physiques à un groupe de protection. Avant de commencer, notez que :

- Machines virtuelles sont découvertes toutes les 15 minutes, et il peut prendre jusqu'à 15 minutes pour apparaître dans Azure Site récupération après la découverte.
- Modifications de l’environnement sur l’ordinateur virtuel (par exemple, installation des outils VMware) peuvent également prendre jusqu'à 15 minutes à mettre à jour dans récupération de Site.
- Vous pouvez vérifier la dernière fois découverte dans le champ **CONTACT dernière en** pour l’hôte de serveur/ESXi vCenter dans la page **Configuration de serveurs** .
- Si vous avez déjà créé un groupe de protection et ajouter un hôte serveur ou ESXi vCenter après cela, il faut quinze minutes pour le portail de récupération de Site Azure actualiser et pour les machines virtuelles soit répertoriée dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection** .
- Si vous voulez continuer immédiatement à l’ajout d’ordinateurs au groupe de protection sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser** .
- Lorsque vous ajoutez des machines virtuelles ou ordinateurs physiques à un groupe de protection, le serveur de traitement pousse automatiquement et installe le service mobilité sur le serveur source si le service informatique n’est pas déjà installé.
- Le mécanisme push automatique fonctionne Vérifiez que vous avez configuré vos machines protégées comme décrit dans l’étape précédente.

Ajouter des ordinateurs comme suit :

1. Cliquez sur **éléments protégés** > **Groupe Protection** > **Machines** > **Ajouter des ordinateurs**. Meilleurs résultats, nous vous recommandons de que les groupes de protection devraient refléter vos charges de travail afin que vous ajoutez des ordinateurs qui exécutent une application spécifique au même groupe.
2. Dans **Sélectionner des Machines virtuelles** si vous êtes protection des serveurs physiques, dans l’Assistant **Ajouter des Machines physiques** fournir l’adresse IP et un nom convivial. Sélectionnez ensuite la famille de systèmes d’exploitation.

    ![Ajouter un serveur V centre](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. Dans **Sélectionner des Machines virtuelles** si vous êtes protection des machines virtuelles VMware, sélectionnez un serveur vCenter qui gère votre machines virtuelles (ou l’hôte EXSi sur lequel ils exécutent), puis les ordinateurs.

    ![Ajouter un serveur V centre](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. Dans **Spécifier les ressources cible** sélectionnez les serveurs cibles maître et stockage à utiliser pour la réplication et indiquez si les paramètres doivent être utilisés pour toutes les charges de travail. Sélectionnez [Compte de stockage Premium](../storage/storage-premium-storage.md) lors de la configuration de la protection des charges de travail qui nécessitent cohérentes haute performance IO et faible latence pour héberger IO intensives charges de travail. Si vous souhaitez utiliser un compte de stockage Premium pour vos disques la charge de travail, vous devez utiliser la cible du masque de série de service d’annuaire. Vous ne pouvez pas utiliser disques de stockage Premium avec masque cible de série de service d’annuaire.

    >[AZURE.NOTE] Nous ne prennent pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) au sein de groupes de ressources.

    ![serveur vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. Dans **Spécifier les comptes** , sélectionnez le compte que vous voulez utiliser pour installer le service mobilité sur machines protégées. Les informations d’identification de compte sont nécessaires pour l’installation automatique du service mobilité. Si vous ne pouvez pas sélectionner un Assurez-vous de compte que vous configurez une comme décrit à l’étape 2. Notez que ce compte ne sont pas accessibles Azure. Pour Windows serveur le compte doit disposer de droits d’administrateur sur le serveur source. Pour Linux le compte doit être racine.

    ![Informations d’identification Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Activez la case à cocher pour finaliser l’ajout des ordinateurs au groupe de protection et lancer la réplication initiale pour chaque ordinateur. Vous pouvez surveiller l’état dans la page **tâches** .

    ![Ajouter un serveur V centre](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. En outre, vous pouvez surveiller l’état de protection en cliquant sur les **Éléments protégés par** > nom du groupe protection > **Machines virtuelles** . Après l’exécution de la réplication initiale et les ordinateurs sont synchronisation des données qu’ils vous montrent état **protégé** .

    ![Machine virtuelle travaux](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Propriétés de l’ordinateur ensemble protégé

1. Une fois un ordinateur a un état **protégé** , vous pouvez configurer ses propriétés basculement. Dans les détails du groupe protection sélectionnez l’ordinateur et ouvrez l’onglet **configurer** .
2. Vous pouvez modifier le nom qui sera attribué à l’ordinateur dans Azure après basculement et la taille de la machine virtuelle Azure. Vous pouvez également sélectionner le réseau Azure auquel l’ordinateur sera connecté après le basculement.

    > [AZURE.NOTE] [Migration des réseaux](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

    ![Définir les propriétés de la machine virtuelle](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Notez que :

- Le nom de l’ordinateur Azure doit être conformes aux exigences Azure.
- Par défaut répliquées machines virtuelles dans Azure n’êtes pas connectés à un réseau Azure. Si vous souhaitez répliquer machines virtuelles communiquer veillez à leur a le même réseau Azure.
- Si vous redimensionnez un volume sur un serveur physique ou machine virtuelle VMware il accède à l’état critique. Si vous n’avez pas besoin de modifier la taille, procédez comme suit :

    - a) modifiez le paramètre de taille.
    - b) dans l’onglet **Machines virtuelles** , sélectionnez la machine virtuelle, puis cliquez sur **Supprimer**.
    - c) dans **Supprimer un ordinateur virtuel** sélectionnez l’option **désactiver la protection (utilisé pour la récupération d’exploration et volume redimensionner)**. Cette option désactive la protection, mais conserve les points de récupération dans Azure.

        ![Définir les propriétés de la machine virtuelle](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) réactiver la protection de la machine virtuelle. Lorsque vous réactivez la protection des données pour le volume redimensionnée sera en Azure.

    

## <a name="step-10-run-a-failover"></a>Étape 10 : Exécuter un basculement

Vous ne pouvez actuellement exécuter basculement non planifié pour les machines virtuelles VMware protégés et des serveurs physiques. Notez les points suivants :



- Avant de lancer un basculement, assurez-vous que les configuration et masque les serveurs de cible sont en cours d’exécution et correct. Dans le cas contraire basculement échouera.
- Machines d’origine ne sont pas arrêtés dans le cadre d’un basculement non planifié. Effectuer un basculement non planifié cesse de réplication des données pour les serveurs protégés. Vous devez supprimer les ordinateurs du groupe de protection et les ajouter à nouveau pour lancer la protection des machines après le basculement non planifié.
- Si vous voulez basculer sans perdre vos données, assurez-vous que les ordinateurs virtuels site principal sont désactivés avant de lancer le basculement.

1. Dans les **Plans de** page et ajoutez un plan de récupération. Spécifier les détails pour le plan, puis sélectionnez **Azure** comme cible.

    ![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. Dans **Sélectionner l’ordinateur virtuel** sélectionnez un groupe de protection, puis sélectionnez machines dans le groupe auquel ajouter pour le plan de récupération. [Pour en savoir plus](site-recovery-create-recovery-plans.md) sur les offres de récupération.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Si nécessaire, vous pouvez personnaliser le plan pour créer des groupes et séquence l’ordre dans quels ordinateurs de la récupération plan est basculé. Vous pouvez également ajouter des invites pour les scripts et les actions manuelles. Les scripts lorsque vous récupérez vers Azure peuvent être ajoutés à l’aide de [Procédures opérationnelles Automation Azure](site-recovery-runbook-automation.md).

4. Dans la page **Plans de** sélectionner le plan, puis cliquez sur **Basculement non planifié**.
5. Dans le **Basculement confirmer** vérifier la direction de basculement (à Azure), puis sélectionnez le point de récupération pour basculer vers.
6. Attendez que la tâche basculement se termine et puis vérifiez que le basculement a fonctionné comme prévu et que les ordinateurs virtuels répliquées démarrer correctement dans Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Étape 11 : Fail revenir basculé machines à partir d’Azure

[En savoir plus](site-recovery-failback-azure-to-vmware-classic-legacy.md) sur la façon de mettre votre échec machines en cours d’exécution Azure revenir à votre environnement local.


## <a name="manage-your-process-servers"></a>Gérer vos serveurs de traitement

Le serveur de traitement découvre le nouvel ajoutés à un serveur vCenter des machines virtuelles VMware et envoie des données de réplication sur le serveur cible maître dans Azure. Dans les cas suivants, vous souhaiterez peut-être modifier le serveur de traitement dans votre déploiement :

- Si le serveur de traitement en cours s’arrête.
- Si votre récupération objectifs de point () augmente à un niveau inacceptable pour votre organisation.

Si nécessaire vous pouvez déplacer la réplication de tout ou partie de vos locaux des machines virtuelles VMware et les serveurs physiques à un autre serveur. Par exemple :

- **Échec**— si un serveur de traitement échoue ou n’est pas disponible, vous pouvez déplacer la réplication ordinateurs protégée à un autre serveur. Métadonnées de la source et réplica machine seront déplacées vers le nouveau serveur de processus et de données sont synchronisé de nouveau. Le nouveau serveur de processus se connecte automatiquement au serveur vCenter pour effectuer la découverte automatique. Vous pouvez surveiller l’état des serveurs de traitement du tableau de bord de récupération de Site.
- **Équilibrage de charge pour ajuster RPO**— pour équilibrage amélioré vous pouvez sélectionner un serveur de processus différent dans le portail de récupération de Site et y accéder réplication d’un ou plusieurs ordinateurs pour l’équilibrage de charge manuelle. Dans ce cas métadonnées des ordinateurs source et réplica sélectionnés sont déplacée vers le nouveau serveur de processus. Le serveur de processus d’origine reste connecté au serveur vCenter. 

### <a name="monitor-the-process-server"></a>Surveillez le serveur de processus

Si un serveur de processus est dans un état critique un avertissement concernant l’état s’affichera dans le tableau de bord de récupération de Site. Vous pouvez cliquer sur l’état pour ouvrir l’onglet Events et puis d’exploration des tâches spécifiques dans l’onglet tâches. 

### <a name="modify-the-process-server-used-for-replication"></a>Modifier le serveur de processus utilisé pour la réplication

1. Ouvrir des **serveurs** > **Serveurs de Configuration** > serveur de configuration > **Détails du serveur**.
2. Cliquez sur **Des serveurs de traitement** > **Changer de serveur de processus** en regard du serveur que vous souhaitez modifier.

    ![Modifier les serveurs de processus 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. Dans **Modifier les serveurs de processus** > **Serveur processus cible** sélectionnez le nouveau serveur que vous voulez utiliser, puis sélectionnez les machines virtuelles que vous souhaitez répliquer vers le nouveau serveur. Cliquez sur l’icône d’information en regard du nom du serveur pour plus d’informations d’espace libre et la mémoire utilisée. L’espace moyen doivent répliquer chaque ordinateur virtuel sélectionné vers le nouveau serveur de processus s’affiche pour vous aider à prendre des décisions de charger.

    ![Modifier les serveurs de processus 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Activez la case à cocher pour commencer la réplication vers le nouveau serveur de processus. Notez que si vous supprimez toutes les machines virtuelles d’un serveur de processus qui a été critique il doit n’est plus afficher un avertissement critique dans le tableau de bord.


## <a name="third-party-software-notices-and-information"></a>Informations relatives aux logiciels tiers et des informations

Ne pas traduire ou Localize

Le logiciel et le microprogramme en cours d’exécution dans le produit ou service Microsoft repose sur ou incorpore matériel à partir des projets ci-dessous (collectivement, « Code tiers »).  Microsoft est l’auteur pas d’origine du Code tiers.  La mention d’origine et la licence, sous lequel Microsoft reçu ce Code tiers, sont définis ci-dessous.

Les informations dans la Section A sont en ce qui concerne les composants de Code tiers à partir des projets présentées ci-après. Ces licences et ces informations sont fournies uniquement à des fins d’information.  Ce Code tiers est en cours relicensed pour vous par Microsoft sous les termes de licence logiciel de Microsoft pour le produit ou service Microsoft.  

Les informations dans la Section B sont en ce qui concerne les composants de Code tiers qui sont en cours mis à votre disposition par Microsoft sous les termes de licence d’origine.

Le fichier complet peut apparaître dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft réserve tous les droits non expressément concédés, que ce soit par implication, estoppel ou dans le cas contraire.
