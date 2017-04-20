<properties
 pageTitle="Taille des services en nuage | Microsoft Azure"
 description="Répertorie les tailles de machine virtuelle différente (et ID) pour les rôles Azure cloud service web et de travail."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Taille des Services en nuage

Cette rubrique décrit les tailles disponibles et les options pour les instances de rôle Service Cloud (rôles web et les rôles de travail). Il fournit également les considérations relatives au déploiement pour tenir compte des lorsque vous envisagez d’utiliser ces ressources. La taille de chaque dispose d’un ID que vous allez placer dans votre [fichier de définition de service](cloud-services-model-and-package.md#csdef).

Les Services en nuage figure parmi plusieurs types de ressources cluster offertes par Azure. Cliquez [ici](cloud-services-choose-me.md) pour obtenir plus d’informations sur les Services en nuage.

> [AZURE.NOTE]Pour afficher les limites Azure connexes, voir [abonnement Azure et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Taille des instances de rôle web et travail

Il existe plusieurs formats standard pour choisir sur Azure. Considérations sur certaines de ces tailles sont les suivantes :

* Machines virtuelles série D sont conçues pour exécuter des applications qui exigent la puissance de calcul supérieure et les performances de disque temporaire. Machines virtuelles série D prévoient le disque temporaire processeurs plus rapides, un taux de mémoire-à-core plus élevé et un lecteur SSD (SSD). Pour plus d’informations, voir l’annonce sur le blog Azure, [Nouvelles tailles Machine virtuelle série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Dv2-série, un suivi de la série D d’origine, comporte une UC plus puissante. L’UC Dv2 série est d’environ 35 % plus rapidement que l’UC de série D. Il est basé sur la dernière génération 2,4 GHz Intel Xeon® E5-2673 v3 processeur (Haswell), avec la technologie 2.0 Intel Turbo Ampli, permet d’accéder au 3.1 GHz. La série Dv2 a les mêmes configurations de mémoire et de disque que la série D.

*   Machines virtuelles série G offrent le plus de mémoire et exécutent sur des hôtes ayant familles de processeurs Intel Xeon E5 V3.

*   Les ordinateurs virtuels A série peuvent être déployés sur un grand nombre de processeurs et types de matériel. La taille est limitée, basé sur le matériel, pour proposer des performances du processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel, sur qu'il est déployé. Pour déterminer le matériel sur lequel est déployée cette taille, la requête le matériel virtuel à partir de la machine virtuelle.

*   La taille A0 est trop abonnée sur le matériel. Pour cette taille spécifique, d’autres déploiements clients peuvent avoir un impact sur les performances de votre charge de travail en cours d’exécution. Les performances relatives sont décrite ci-dessous en tant que le planning de référence attendu, soumis à une variabilité approximative de 15 %.


La taille de la machine virtuelle affecte la tarification. La taille affecte également la capacité de stockage, mémoire et du traitement de la machine virtuelle. Les coûts de stockage sont calculés séparément reposant sur les pages utilisées dans le compte de stockage. Pour plus d’informations, voir [Détails tarifs Machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) et les [Prix de stockage Azure](https://azure.microsoft.com/pricing/details/storage/). 


Les considérations suivantes peuvent vous aider à choisir une taille :


* Les tailles A8 A11 et H-série sont également appelés *instances cluster accrue*. Le matériel qui s’exécute ces tailles est conçu et optimisé pour cluster accrue et applications de réseau accrue, y compris informatique High performance (HPC) cluster simulations, modélisation et les applications. La série A8 A11 utilise Intel Xeon E5-2670 @ 2,6 GHZ et la série H utilise Intel Xeon E5-2667 v3 @ 3,2 GHz. Pour des informations détaillées et les considérations sur l’utilisation de ces tailles, voir [à propos des ordinateurs virtuels A série H-série et cluster accrue](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Série de Dv2, D-série, G-série, sont idéal pour les applications qui exigent des processeurs plus rapides, local meilleur performances de disque, ou que vous augmente la demande de mémoire.  Ils offrent une combinaison puissante pour de nombreuses applications d’entreprise.

*   Certaines des hôtes physiques des centres de données Azure ne peut-être pas en charge volumineux machine virtuelle, tel que A5 – A11. Par conséquent, vous pouvez voir le message d’erreur **Impossible à configurer machine virtuelle {nom de l’ordinateur}** ou **Création machine virtuelle {nom de l’ordinateur} a échoué** lors du redimensionnement d’une machine virtuelle existante à une nouvelle taille ; Création d’une nouvelle machine virtuelle dans un réseau virtuel créé avant le 16 avril 2013 ; ou en ajoutant une nouvelle machine virtuelle à un service cloud existant. Voir [erreur : « Échec de la configuration de machine virtuelle »](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) sur le forum de support pour les solutions de contournement pour chaque scénario de déploiement.  

* Votre abonnement peut également limiter le nombre de cœurs que vous pouvez déployer dans certaines familles de taille. Pour augmenter un quota, contactez le Support Azure.


## <a name="performance-considerations"></a>Considérations relatives aux performances

Nous avons créé le concept de Azure Calculer unité (ACU) pour offrir un moyen de la comparaison des performances de cluster (UC) entre les références SKU Azure. Cela vous aidera à identifier qui référence (SKU) est probablement répondre à vos besoins de performances.  ACU est actuellement normalisé dans un petit (Standard_A1) machine virtuelle en cours 100 et toutes les autres références puis représente environ comment beaucoup plus rapidement que référence (SKU) peut s’exécuter un test d’évaluation standard. 

>[AZURE.IMPORTANT] L’ACU n'est qu’une indication.  Les résultats de votre charge de travail peuvent varier. 

<br>

|Famille de référence (SKU) |ACU/Core |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1-14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs marqués avec une * utiliser la technologie Intel® Turbo pour augmenter la fréquence du processeur et fournir une amélioration des performances.  Le montant de l’Ampli peut varier selon la taille de la mémoire virtuelle, la charge de travail et autres charges de travail en cours d’exécution sur le même hôte.

## <a name="size-tables"></a>Tables de tailles

Les tableaux suivants présentent les tailles et les capacités qu’ils fournissent.

* Capacité de stockage est présentée dans les unités de talon ou 1024 ^ 3 octets. Comparaison des disques lorsque mesurée en Go (1000 ^ 3 octets) vers des disques mesurées en talon (1024 ^ 3) n’oubliez pas que les numéros de capacité figurant dans le talon peuvent s’afficher plus petits. Par exemple, le talon 1023 = 1098.4 Go

* Débit de disque est mesuré en entrées/sorties par seconde (sorties par) et MBps où MBps = 10 ^ 6 octets/sec.

* Disques de données peuvent fonctionner en mode mis en cache ou non mis en cache. Opération de disque de données mises en cache, le mode de cache hôte est défini sur **en lecture seule** ou **ReadWrite**.  Opération de disque de données non mis en cache, le mode de cache hôte est défini sur **Aucun**.

* Bande passante réseau maximale la maximale agrégé la bande passante est attribué et affectées par type de machine virtuelle. La bande passante maximale fournit des indications pour sélectionner le type de machine virtuelle droite pour assurer une capacité réseau adéquate sont disponible. Lorsque vous déplacez entre faible, moyenne, haute et très haute, le débit augmente en conséquence. Les performances du réseau réel dépend de nombreux facteurs tels que le réseau et les charges d’application et les paramètres réseau de l’application.


## <a name="a-series"></a>Une série

| Taille        | Cœurs de processeur | Mémoire : talon | Disque dur local : talon | Disques de données max | Max disque débit : sorties par | Cartes réseau max / la bande passante réseau |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / faible                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / modéré              |
| Standard_A2 | 2         | 3,5 GO       | 135                   | 4              | 500 x 4              | 1 / modéré              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / haute                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / haute                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 500 X 4              | 1 / modéré              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / haute                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / haute                  |

## <a name="a-series---compute-intensive-instances"></a>Série A - instances cluster accrue

Pour des informations et les considérations sur l’utilisation de ces tailles, voir [à propos des machines virtuelles A série H-série et cluster accrue](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Taille         | Cœurs de processeur | Mémoire : talon | Disque dur local : talon | Disques de données max | Max disque débit : sorties par | Cartes réseau max / la bande passante réseau |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / haute                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / très haute             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / haute                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / très haute             |

* RDMA capable

## <a name="d-series"></a>Série D


| Taille         | Cœurs de processeur | Mémoire : talon | SSD local : talon | Disques de données max | Max disque débit : sorties par | Cartes réseau max / la bande passante réseau |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / modéré              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 500 x 4              | 2 / haute                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 500 x 4              | 2 / haute                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | très haute / 8             |

## <a name="dv2-series"></a>Série Dv2

| Taille            | Cœurs de processeur | Mémoire : talon | SSD local : talon | Disques de données max | Max disque débit : sorties par | Cartes réseau max / la bande passante réseau |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / modéré              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 500 x 4              | 2 / haute                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | très haute / 8        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 500 x 4              | 2 / haute                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / haute                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / haute                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | très haute / 8        |
| Standard_D15_v2 | 20        | 140          | 1 000                | 40             | 40 x 500             | très haute / 8        |

## <a name="g-series"></a>Série G

| Taille        | Cœurs de processeur | Mémoire : talon  | SSD local : talon  | Disques de données max | Débit de disque max : sorties par | Cartes réseau max / la bande passante réseau |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 500 x 4            | 1 / haute                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / haute                  |
| Standard_G3 | 8         | 112          | 1,536                | 16             | 16 x 500           | 4 / très haute             |
| Standard_G4 | 16        | 224          | 3 072                | 32             | 32 x 500           | très haute / 8        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 500 x 64           | très haute / 8        |


## <a name="h-series"></a>Série de H

Azure machines H-série sont la prochaine génération haute performance computing que machines virtuelles visant à des besoins de haut de gamme calcul, telles que la modélisation moléculaire et calcul des fluides. Ces 8 et 16 core machines virtuelles reposent sur la technologie processeur Intel Haswell E5 2667 V3 DDR4 mémoire et de stockage SSD basé local. 

Outre la puissance du processeur significative, la série H propose des options centralisées pour le réseau de RDMA faible latence à l’aide de plusieurs configurations de mémoire et InfiniBand FDR pour prendre en charge des exigences de calcul intensive de mémoire.


| Taille           | Cœurs de processeur | Mémoire : talon | SSD local : talon | Disques de données max | Débit de disque max : sorties par | Cartes réseau max / la bande passante réseau |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / haute                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | très haute / 8                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / haute                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | très haute / 8                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | très haute / 8                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | très haute / 8                  |


* RDMA capable

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Remarques : Standard A0 - A4 à l’aide d’infrastructure du langage commun et PowerShell 

Dans le modèle de déploiement classique, certains noms de taille de la mémoire virtuelle sont légèrement différents dans infrastructure du langage commun et PowerShell :

* Standard_A0 est ExtraSmall 
* Standard_A1 est petit
* Standard_A2 est adaptée au support
* Standard_A3 est grande
* Standard_A4 est ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurer les tailles des Services en nuage

Vous pouvez spécifier la taille de la Machine virtuelle d’une instance de rôle dans le cadre du modèle de service décrit par le [fichier de définition de service](cloud-services-model-and-package.md#csdef). La taille du rôle détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille de système de fichiers local est affectée à une instance en cours d’exécution. Sélectionnez la taille de rôle basée sur la spécification de ressources de votre application.

Voici un exemple de configuration de la taille de rôle doit être [Standard_D2](#general-purpose-d) pour une instance de rôle Web :

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [abonnement azure et les limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md).
- Obtenir des informations [sur les ordinateurs virtuels A série H-série et cluster accrue](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) pour charges de travail comme High performance Computing (HPC).

