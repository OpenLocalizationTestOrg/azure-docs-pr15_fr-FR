<properties 
   pageTitle="Quel est le Gestionnaire d’instantanés StorSimple ? | Microsoft Azure"
   description="Décrit le Gestionnaire d’instantanés StorSimple, son architecture et ses fonctionnalités."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="what-is-storsimple-snapshot-manager"></a>Quel est le Gestionnaire d’instantanés StorSimple ?

## <a name="overview"></a>Vue d’ensemble

Gestionnaire d’instantanés StorSimple est un composant logiciel enfichable Microsoft Management Console (MMC) qui simplifie la protection des données et la gestion des sauvegardes dans un environnement de Microsoft Azure StorSimple. Avec StorSimple instantané Manager, vous pouvez gérer les données Microsoft Azure StorSimple dans le centre de données et dans le cloud comme une solution de stockage intégrée unique, et donc simplifier le processus de sauvegarde et réduire les coûts.

Cette présentation présente le Gestionnaire d’instantanés StorSimple, décrit ses fonctionnalités et explique son rôle dans Microsoft Azure StorSimple. 

Pour une vue d’ensemble de l’ensemble du système de Microsoft Azure StorSimple, y compris le périphérique StorSimple, le service StorSimple Manager, gestionnaire d’instantanés StorSimple et StorSimple carte pour SharePoint, voir [série 8000 StorSimple : une solution de stockage cloud hybride](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- Vous ne pouvez pas utiliser Gestionnaire d’instantanés StorSimple pour gérer les tableaux virtuels Microsoft Azure StorSimple (également appelé StorSimple locaux périphériques virtuels).
>
>- Si vous envisagez d’installer StorSimple mise à jour 2 sur votre appareil StorSimple, veillez à télécharger la dernière version du Gestionnaire de capture instantanée StorSimple et installez-le **avant d’installer StorSimple mise à jour 2**. La dernière version du Gestionnaire de capture instantanée StorSimple une compatibilité descendante et fonctionne avec toutes les versions de Microsoft Azure StorSimple. Si vous utilisez la version précédente du Gestionnaire d’instantané StorSimple, vous devez mettre à jour (vous n’avez pas besoin de désinstaller la version précédente avant d’installer la nouvelle version).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Architecture et Gestionnaire d’instantanés StorSimple objectif

Gestionnaire d’instantanés StorSimple fournit une console de gestion centrale que vous pouvez utiliser pour créer cohérentes, copies de sauvegarde de point dans le temps de local et données cloud. Par exemple, vous pouvez utiliser la console pour :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volume pour vous assurer que les données sauvegardées est cohérentes avec les applications.
- Gérer les stratégies de sauvegarde afin que les données sont sauvegardées sur une planification prédéfinie.
- Créer local et en nuage des instantanés, qui peuvent être stockées dans le cloud et utilisés pour la reprise.

Le Gestionnaire d’instantanés StorSimple extrait la liste des applications enregistrées avec le fournisseur VSS sur l’hôte. Ensuite, pour créer des sauvegardes cohérentes avec les applications, il vérifie les volumes utilisés par une application et suggère des groupes de volumes à configurer. Gestionnaire d’instantanés StorSimple utilise ces groupes pour générer des copies de sauvegarde sont cohérentes avec les applications. (La cohérence des applications existe lorsque tous les fichiers associés et bases de données sont synchronisés et représentent l’état de l’application à un point précis en temps réel.) 

Gestionnaire d’instantanés StorSimple sauvegardes prennent la forme d’instantanés incrémentiels qui capture uniquement les modifications apportées depuis la dernière sauvegarde. Par conséquent, des sauvegardes nécessitent moins de stockage et peuvent être créées et restaurées rapidement. Gestionnaire d’instantanés StorSimple utilise Windows Volume Shadow Copy Service (VSS) pour vous assurer que des instantanés capturer des données cohérentes avec les applications. (Pour plus d’informations, accédez à l’intégration avec la section Windows Volume Shadow Copy Service). Avec StorSimple instantané Manager, vous pouvez créer des plannings de sauvegarde ou effectuer des sauvegardes immédiates selon vos besoins. Si vous avez besoin restaurer les données à partir d’une sauvegarde, vous permet de gestionnaire d’instantanés StorSimple vous sélectionnez à partir d’un catalogue local ou instantanés cloud. Azure StorSimple restaure uniquement les données qui sont nécessaires pour qu’il est nécessaire, qui empêche les retards dans la disponibilité des données au cours d’opérations de restauration.)

![Gestionnaire d’instantanés StorSimple architecture](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Gestionnaire d’instantanés StorSimple architecture** 

## <a name="support-for-multiple-volume-types"></a>Prise en charge de plusieurs types de volume

Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour configurer et sauvegarder les types de volumes suivants : 

- **Volumes de base** – un volume de base est une partition unique sur un disque de base. 

- **Volumes simples** – un volume simple est un volume dynamique qui contient l’espace disque d’un seul disque dynamique. Un volume simple est composée d’une région unique sur un disque ou plusieurs régions qui sont liées entre eux sur le même disque. (Vous pouvez créer des volumes simples uniquement sur des disques dynamiques). Les volumes simples ne sont pas tolérance de panne.

- **Volumes dynamiques** – un volume dynamique est un volume créé sur un disque dynamique. Disques dynamiques utilisent une base de données pour effectuer le suivi des informations sur les volumes qui se trouvent sur des disques dynamiques d’un ordinateur. 

- **Volumes dynamiques avec la mise en miroir** – volumes dynamiques avec la mise en miroir s’appuient sur l’architecture RAID 1. RAID 1, des données identiques écrit sur disque deux ou plusieurs, obtention d’un ensemble en miroir. Une demande de lecture peut ensuite être traitée par n’importe quel disque contenant les données demandées.

- **Volumes partagés cluster** – avec des volumes partagés de cluster (CSVs), plusieurs nœuds dans un cluster de basculement peuvent simultanément lire ou écrire sur le même disque. Basculement d’un nœud vers un autre nœud peut se produire rapidement, sans avoir besoin d’une modification dans la propriété du lecteur ou de montage, démontage et la suppression d’un volume. 

>[AZURE.IMPORTANT] Ne pas mélanger CSVs et non CSVs dans le même instantané. Mélange CSVs et non CSVs dans un instantané n’est pas pris en charge. 
 
Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour restaurer des groupes de volume entier ou cloner des volumes et récupérer des fichiers individuels.

- [Groupes de volumes et en volume](#volumes-and-volume-groups) 
- [Types de sauvegarde et de stratégies de sauvegarde](#backup-types-and-backup-policies) 

Pour plus d’informations sur les fonctionnalités de gestionnaire d’instantanés StorSimple et comment les utiliser, voir [Gestionnaire d’instantanés StorSimple l’interface utilisateur](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Groupes de volumes et en volume

Avec le gestionnaire instantané StorSimple, créer des volumes, puis les configurer en groupes en volume. 

Gestionnaire d’instantanés StorSimple utilise des groupes de volume pour créer des copies de sauvegarde sont cohérentes avec les applications. La cohérence des applications existe lorsque tous les fichiers associés et bases de données sont synchronisés et représentent l’état d’une application à un point précis en temps réel. Groupes en volume (qui sont également connu sous le nom des *groupes de cohérence*) forment la base d’une sauvegarde ou restaurer la tâche.

Groupes de volumes ne sont pas le même en tant que conteneurs de volume. Un conteneur de volume contient un ou plusieurs volumes qui partagent un compte de stockage cloud et d’autres attributs, tels que la consommation de chiffrement et la bande passante. Un conteneur de volume unique peut contenir jusqu'à 256 volumes StorSimple légèrement mis en service. Pour plus d’informations sur les conteneurs de volume, accédez à [Gérer les conteneurs de volume](storsimple-manage-volume-containers.md). Groupes de volumes sont des ensembles de volumes que vous configurez pour faciliter les opérations de sauvegarde. Si vous sélectionnez deux volumes qui appartiennent aux conteneurs de volume différent, placez-les dans un groupe de volume unique et puis créer une stratégie de sauvegarde pour ce groupe en volume, chaque volume est sauvegardé dans le conteneur de volume approprié, en utilisant le compte de stockage approprié.

>[AZURE.NOTE] Tous les volumes dans un groupe de volumes doivent provenir d’un fournisseur de services de cloud unique.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Intégration avec le Service Windows Volume ombre

Gestionnaire d’instantanés StorSimple utilise Windows Volume Shadow Copy Service (VSS) pour capturer des données cohérentes avec les applications. VSS facilite la cohérence des applications en communication avec des applications prenant en charge VSS pour coordonner la création d’instantanés incrémentiels. VSS garantit que les applications sont temporairement inactif ou repos, lorsque des instantanés. 

L’implémentation StorSimple Gestionnaire d’instantanés de VSS fonctionne avec SQL Server et NTFS générique. Le processus est la suivante : 

1. Un demandeur, qui est généralement une gestion des données et la solution de protection (tel que StorSimple instantané Manager) ou une application de sauvegarde, appelle VSS et vous invite à fournir pour collecter les informations sur le logiciel writer dans l’application cible.

2. VSS contacte le composant writer pour récupérer une description des données. L’enregistreur renvoie la description des données à sauvegarder. 

3. VSS signale le writer pour préparer l’application pour la sauvegarde. Le writer prépare les données pour la sauvegarde en procédant de transactions en cours, mise à jour des journaux des transactions et ainsi de suite et puis vous avertit VSS.

4. VSS demande au temporairement arrêter banques de données de l’application et vérifiez qu’aucune donnée est écrite dans le volume lors de la création de la copie ombre lecteur. Cette étape garantit la cohérence des données et ne prend pas plus de 60 secondes.

5. VSS indique le fournisseur afin de créer la copie secondaire. Fournisseurs, qui peuvent être logicielle ou matérielle basés, gérer les volumes qui sont en cours d’exécution et créer des clichés d'entre eux à la demande. Le fournisseur crée la copie d’ombre et vous avertit VSS lorsqu’il est terminé.

6. VSS contacte le writer pour signaler à l’application e/s peut reprendre et également pour confirmer qu’e/s a été suspendu lors de la création ombre. 

7. Si la copie a réussi, VSS renvoie l’emplacement de la copie au demandeur. 

8. Si les données ont été écrites lors de la création de la copie de l’ombre, la sauvegarde sera incohérente. VSS supprime la copie d’ombre et vous avertit le demandeur. Le demandeur pouvez répéter le processus de sauvegarde automatiquement ou signaler à l’administrateur pour recommencer ultérieurement.

Consultez l’illustration suivante.

![Processus VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processus Windows Volume Shadow Copy Service** 

## <a name="backup-types-and-backup-policies"></a>Types de sauvegarde et de stratégies de sauvegarde

Avec StorSimple instantané Manager, vous pouvez sauvegarder vos données et stocker localement et dans le cloud. Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour sauvegarder des données immédiatement, ou vous pouvez utiliser une stratégie de sauvegarde pour créer une planification pour la prise de sauvegardes automatiquement. Stratégies de sauvegarde également activer vous permettent de spécifier des instantanés combien sont conservées. 

### <a name="backup-types"></a>Types de sauvegarde

Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour créer les types de sauvegardes suivants :

- **Instantanés locales** – instantanés locaux sont des copies de point dans le temps de données de volume qui sont stockées sur le périphérique StorSimple. En règle générale, ce type de sauvegarde peut être créé et restauré rapidement. Vous pouvez utiliser un instantané local comme vous le feriez pour une copie de sauvegarde locale.

- **Instantanés cloud** : nuage instantanés sont des copies de point dans le temps de données de volume qui sont stockées dans le cloud. Un instantané de cloud équivaut à un instantané répliqué sur un système de stockage différent, hors site. Cloud instantanés sont particulièrement utiles dans les scénarios de récupération d’urgence.

### <a name="on-demand-and-scheduled-backups"></a>Sauvegardes à la demande et planifiées

Avec StorSimple instantané Manager, vous pouvez lancer une sauvegarde unique à créer immédiatement, ou vous pouvez utiliser une stratégie de sauvegarde pour planifier les opérations de sauvegarde périodiques.

Une stratégie de sauvegarde est un ensemble de règles automatiques que vous pouvez utiliser pour planifier des sauvegardes régulières. Une stratégie de sauvegarde vous permet de définir la fréquence et les paramètres pour prendre des instantanés d’un groupe de volume spécifique. Vous pouvez utiliser les stratégies pour spécifier les dates de début et d’expiration, des heures, fréquence et exigences de rétention pour les deux local et en nuage instantanés. Une stratégie est appliquée immédiatement après avoir défini il. 

Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour configurer ou reconfigurer les stratégies de sauvegarde dès que nécessaire. 

Vous configurez les informations suivantes pour chaque stratégie de sauvegarde que vous créez :

- **Nom** : nom unique de la stratégie de sauvegarde sélectionnée.

- **Type** – le type de stratégie de sauvegarde ; instantané local ou un instantané de cloud.

- **Groupe de volumes** – le groupe de volume vers lequel la stratégie de sauvegarde sélectionnée est affectée.

- **Rétention** – le nombre de copies de sauvegarde à conserver. Si vous cochez la case **tous** , toutes les copies de sauvegarde sont conservés jusqu'à ce que le nombre maximal de copies de sauvegarde par volume est atteint, moment auquel la stratégie échoue et générer un message d’erreur. Vous pouvez également spécifier un nombre de sauvegardes à conserver (compris entre 1 et 64).

- **Date** – date de création de la stratégie de sauvegarde.

Pour plus d’informations sur la configuration des stratégies de sauvegarde, accédez à [Utiliser le Gestionnaire d’instantané StorSimple pour créer et gérer des règles de sauvegarde](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Gestion et contrôle du travail de sauvegarde

Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour surveiller et gérer les travaux de sauvegarde à venir, planifiée et terminées. En outre, gestionnaire d’instantanés StorSimple fournit un catalogue de jusqu'à 64 sauvegardes terminées. Vous pouvez utiliser le catalogue pour rechercher et restaurer des volumes ou des fichiers individuels. 

Pour plus d’informations sur l’analyse des travaux de sauvegarde, accédez à [Utiliser le Gestionnaire d’instantané StorSimple pour afficher et gérer les travaux de sauvegarde](storsimple-snapshot-manager-manage-backup-jobs.md).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’utilisation de gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).

- Télécharger le [Gestionnaire de StorSimple instantané](https://www.microsoft.com/download/details.aspx?id=44220).
