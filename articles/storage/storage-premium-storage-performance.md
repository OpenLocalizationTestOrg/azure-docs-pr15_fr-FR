<properties
    pageTitle="Stockage Azure Premium : Concevoir des performances | Microsoft Azure"
    description="Créer des applications High performance à l’aide de stockage Premium Azure. Stockage Premium prend en charge disque High performance, faible latence je/O-accrue les charges de travail en cours d’exécution sur le Machines virtuelles Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Stockage Azure Premium : Création pour optimiser les performances

## <a name="overview"></a>Vue d’ensemble  
Cet article fournit des instructions pour la création d’applications haute performance à l’aide de stockage Premium Azure. Vous pouvez utiliser les instructions fournies dans ce document combiné avec les procédures recommandées applicables aux technologies utilisées par votre application. Pour illustrer les instructions, nous avons utilisé SQL Server exécuté sur un stockage Premium comme exemple dans ce document.

Tandis que nous traitent des scénarios de performances du calque stockage dans cet article, vous avez besoin optimiser la couche d’application. Par exemple, si vous hébergez une batterie de serveurs SharePoint sur le stockage Azure Premium, vous pouvez utiliser les exemples de SQL Server à partir de cet article pour optimiser le serveur de base de données. En outre, optimiser la batterie de serveurs SharePoint serveur Web et serveur d’Application pour obtenir les meilleures performances.

Cet article vous aidera answer suivre des questions fréquentes sur l’optimisation des performances de l’application sur un stockage Azure Premium

-   Comment mesurer les performances de votre application ?  
-   Pourquoi voient attendu haute performance pas ?  
-   Les facteurs qui influencent les performances de votre application sur le stockage Premium ?  
-   Comment les facteurs suivants influencent performances de votre application sur le stockage Premium ?  
-   Comment pouvez-vous optimiser pour sorties par, la bande passante et la latence ?  

Nous vous fournissons ces instructions spécifiquement pour le stockage Premium, car les charges de travail en cours d’exécution sur le stockage Premium sont très sensibles aux performances. Nous vous fournissons exemples le cas échéant. Vous pouvez également appliquer certaines de ces instructions aux applications s’exécutant sur machines virtuelles IaaS avec disques de stockage Standard.

Avant de commencer, si vous ne connaissez pas le stockage Premium, lisez tout d’abord la [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](storage-premium-storage.md) article et [extensibilité élevées de stockage Azure Premium et des objectifs de Performance](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Application des indicateurs de Performance  
Nous déterminer si une application s’exécute correctement ou non à l’aide de tels que des indicateurs de performance, la vitesse une application traite une demande de l’utilisateur, la quantité de données est une application de traitement par demande, le nombre de requêtes est un traitement de l’application dans une certaine heure, la durée pendant laquelle qu'un utilisateur a peut-être patienter avant d’obtenir une réponse après l’envoi de leur demande. Les termes techniques pour ces indicateurs de performance sont, sorties par, débit ou la bande passante et la latence.

Dans cette section, nous allons étudier les indicateurs de performance courants dans le contexte de stockage Premium. Dans la section suivante, collecte des exigences des applications, vous allez apprendre à mesurer ces indicateurs de performance pour votre application. Plus loin dans l’optimisation des performances de l’Application, vous allez découvrir les facteurs qui affectent ces indicateurs de performance et des recommandations pour optimiser les.

## <a name="iops"></a>SORTIES PAR  
E/s est le nombre de requêtes qui envoie votre application pour les disques de stockage en une seconde. Une opération d’entrée/sortie peut être lu ou écrire, séquentielle ou aléatoire. Applications OLTP comme un site Web en ligne de vente au détail doivent traitent de nombreuses demandes simultanées utilisateur immédiatement. L’utilisateur demande est insérer et mettre à jour les transactions de base de données intensive, dont l’application doit traiter rapidement. Par conséquent, les applications OLTP requièrent sorties par très haute. Ces applications gérer des millions de requêtes IO petites et aléatoires. Si vous avez une telle application, vous devez concevoir l’infrastructure d’application pour l’optimisation sorties par. Dans la section ultérieure, *Optimisation des performances de l’Application*, tous les éléments dont vous devez tenir compte pour obtenir des sorties par haute traiter en détail.

Lorsque vous joignez un disque de stockage premium sur votre ordinateur virtuel, une échelle de haute Azure dispositions pour vous un nombre de sorties par garanties conformément à la spécification de disque. Par exemple, un disque P30 dispositions sorties 5000 par. Chaque échelle de grande taille de mémoire virtuelle est également une limite de sorties par spécifique il peut soutenir. Par exemple, une machine virtuelle GS5 Standard sa sorties par 80 000 limite.

## <a name="throughput"></a>Débit  
Débit ou la bande passante est la quantité de données qui envoie votre application pour les disques de stockage dans un intervalle spécifié. Si votre application effectue les opérations d’entrée/sortie avec une grande taille d’unité IO, il nécessite haut débit. Applications de Data warehouse tendent à émettre intensive opérations d’analyse accéder à des grandes portions de données à la fois et fréquemment effectuent les opérations en bloc. En d’autres termes, ces applications nécessitent un débit plus élevé. Si vous avez une telle application, vous devez concevoir son infrastructure pour optimiser pour débit. Dans la section suivante, nous décrivent en détail les facteurs que vous devez régler pour y parvenir.

Lorsque vous joignez un disque de stockage premium à une grande échelle machine virtuelle, Azure dispositions débit conformément à cette spécification de disque. Par exemple, un disque P30 dispositions 200 Mo par seconde disque débit. Chaque échelle de grande taille de mémoire virtuelle est également comme spécifique limite de débit il peut soutenir. Par exemple, Standard GS5 machine virtuelle contient un débit maximal de 2 000 Mo par seconde.

Il existe une relation entre débit et sorties par comme indiqué dans la formule ci-dessous.

![](media/storage-premium-storage-performance/image1.png)

Par conséquent, il est important de déterminer les valeurs optimales débit et sorties par requis par votre application. Lorsque vous essayez d’optimiser une, l’autre également obtient concerné. Dans une section ultérieure, *Optimisation des performances de l’Application*, nous aborderons dans plus de détails sur l’optimisation des sorties par et débit.

## <a name="latency"></a>Latence  
Latence est le temps nécessaire une application pour recevoir une seule demande, envoyer vers les disques de stockage et envoyer une réponse au client. Il s’agit d’une mesure critique des performances d’une application en plus de débit et sorties par. La latence d’un disque de stockage premium est le temps que nécessaire pour récupérer les informations relatives à une demande et communiquer avec votre application. Stockage Premium fournit une latence faible cohérentes. Si vous activez hôte en lecture seule la mise en cache sur disques de stockage premium, vous pouvez obtenir une latence lecture quantité inférieure. Nous allons aborder la mise en cache disque plus en détail dans la section ultérieure sur *Optimisation des performances de l’Application*.

Lorsque vous optimisez votre application pour obtenir une augmentation des sorties par et débit, il affecte la latence de votre application. Au terme de réglage des performances de l’application, toujours évalue la latence de l’application pour éviter tout comportement inattendu latence élevée.

## <a name="gather-application-performance-requirements"></a>Collecter les performances d’Application requises  
La première étape de la conception d’applications haute performance s’exécutant sur le stockage Azure Premium est de comprendre les conditions de performances de votre application. Après avoir recueilli des exigences de performances, vous pouvez optimiser votre application pour optimiser les performances plus.

Dans la section précédente, nous avons expliqué les indicateurs de performance courantes, sorties par, débit et la latence. Vous devez identifier lequel de ces indicateurs de performance sont essentiels à votre application pour envoyer l’expérience utilisateur de votre choix. Par exemple, sorties par haute importent applications OLTP traitement des millions de transactions dans une seconde. Alors que haut débit est essentiel pour les applications de Data Warehouse traitement de grandes quantités de données dans une seconde. Extrêmement faible latence est cruciale pour les applications en temps réel telles que la vidéo live en continu des sites Web.

Mesurer ensuite la configuration requise pour optimiser les performances de votre application dans l’ensemble de sa durée de vie. Utiliser l’aide-mémoire exemple ci-dessous comme un début. Enregistrer la configuration requise pour optimiser les performances pendant normal, pointe et dehors périodes de charge de travail. En identifiant les conditions requises pour tous les niveaux de charges de travail, vous ne pourrez pas déterminer l’exigence de performance générale de votre application. Par exemple, la charge de travail normal d’un site Web de commerce seront les transactions qu'il sert au cours de la plupart des jours par an. La charge de travail pointe du site Web seront les transactions qu'il sert pendant les vacances ou des événements de vente spécial. La charge de travail pointe est généralement expérimenté pour une période limitée, mais peut nécessiter votre application à l’échelle à deux ou plusieurs fois son fonctionnement normal. Trouver les 50 centile, centile 90 et configuration requise centile 99. Cela permet de filtrer les valeurs aberrantes dans les exigences de performances et vous pouvez vous concentrer vos efforts sur l’optimisation pour les valeurs correctes.

**Liste de vérification application performances configuration requise**

| **Exigences de performances** | **50 centile** | **Centile 90** | **Centile 99** |
|---|---|---|---|
| Max. Transactions par seconde | | | |
| Opérations de lecture %            | | | |
| Opérations d’écriture %           | | | |
| Opérations aléatoires %          | | | |
| Opérations séquentielles %      | | | |
| Taille de la requête IO              | | | |
| Moyenne de débit           | | | |
| Max. Débit              | | | |
| Min. Latence                 | | | |
| Latence moyenne              | | | |
| Max. PROCESSEUR                     | | | |
| Moyenne du processeur                  | | | |
| Max. Mémoire                  | | | |
| Mémoire moyenne               | | | |
| Longueur file d’attente                  | | | |

>**Remarque importante :**  
>Vous devez prendre en compte l’évolution de ces nombres en fonction de la croissance prévue de votre application. Il est recommandé de planifier la croissance avance, car il pourrait être plus difficile à modifier l’infrastructure pour améliorer les performances ultérieurement.

Si vous avez une application existante et que vous voulez déplacer vers le stockage Premium, tout d’abord créer la liste de vérification au-dessus de l’application existante. Ensuite, un prototype de votre application sur le stockage Premium et créer l’application selon les instructions décrites dans l' *Optimisation des performances de l’Application* dans une section ultérieure de ce document. La section suivante décrit les outils que vous pouvez utiliser pour recueillir les mesures de performances.

Créer une liste de vérification semblable à votre application existante pour le prototype. À l’aide des outils Benchmarking vous pouvez simuler les charges de travail et mesurer les performances de l’application prototype. Consultez la section sur [Benchmarking](#benchmarking) pour en savoir plus. En procédant ainsi, vous pouvez déterminer si stockage Premium peut faire correspondre ou dépasser les exigences de performances de votre application. Vous pouvez implémenter les mêmes indications pour votre application de production.

### <a name="counters-to-measure-application-performance-requirements"></a>Compteurs pour mesurer les performances d’application requises  
La meilleure façon de mesurer des exigences de performances de votre application, consiste à utiliser les outils d’analyse des performances fournies par le système d’exploitation du serveur. Vous pouvez utiliser l’Analyseur de performances pour Windows et iostat pour Linux. Ces outils capturent compteurs correspondant à chaque action expliquée dans la section ci-dessus. Vous devez capturer les valeurs de ces compteurs lorsque votre application s’exécute ses normal, les charges de travail pointe et dehors.

Compteurs sont disponibles pour processeur, mémoire, chaque disque logique et physique disque de votre serveur. Lorsque vous utilisez des disques de stockage premium avec une machine virtuelle, les compteurs disque physique pour chaque disque de stockage premium, et compteurs disque logique vous pour chaque volume créée sur les stockage des disques premium. Vous devez capturer les valeurs pour les disques qui hébergent votre application charge de travail. S’il existe un mappage un-à-un entre les disques logiques et physiques, vous pouvez faire référence à des compteurs disque physique ; dans le cas contraire, reportez-vous aux compteurs disque logique. Sous Linux, la commande iostat génère un rapport d’utilisation du processeur et de disque. Le rapport d’utilisation disque fournit des statistiques par périphérique physique ou une partition. Si vous avez un serveur de base de données avec ses données et des journaux sur des disques distincts, collecter ces données pour les deux disques. Tableau ci-dessous décrit les compteurs pour disques, processeur et la mémoire :

| Compteur | Description | Analyseur de performances | Iostat |
|---|---|---|---|
| **Sorties par ou Transactions par seconde** | Nombre de requêtes envoyées au disque de stockage par seconde. | Lectures disque/s <br> Écritures disque/s | support <br> r/s <br> w/s |
| **Lectures disque et écritures** | % de lectures opérations effectuées sur le disque et d’écriture. | Temps de lecture du disque % <br> % Temps écriture du disque | r/s <br> w/s |
| **Débit** | Quantité de données lues ou écrites sur le disque par seconde. | Disque octets lus/s <br> Écriture disque octets/s | kB_read/s <br> kB_wrtn/s |
| **Latence** | Durée totale d’une demande de IO disque. | Moyen disque s/lecture <br> Moyenne disque s/écriture | attendre <br> svctm |
| **Taille IO** | Problèmes pour les disques de stockage de demandes de la taille des e/s. | Moyenne disque, octets/lecture <br> Moyenne disque, octets/écriture | avgrq sz |
| **Longueur file d’attente** | Nombre d’e demandes en attente d’être à lire ou écrites sur le disque de stockage. | Long. | avgqu sz |
| **Max. Mémoire** | Quantité de mémoire requise pour le fonctionnement des applications | % Octets dédiés utilisés | Utiliser vmstat |
| **Max. PROCESSEUR** | Montant du processeur requis pour le fonctionnement des applications | Temps du processeur | util % |

En savoir plus sur [iostat](http://linuxcommand.org/man_pages/iostat1.html) et [Analyseur de performances](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Optimisation des performances de l’Application  
Les principaux facteurs qui influencent les performances d’une application en cours d’exécution sur le stockage Premium sont Nature de IO demandes, taille de mémoire virtuelle, la taille du disque, nombre de disques, la mise en cache du disque, Multithreading et profondeur file d’attente. Vous pouvez contrôler certaines de ces facteurs avec boutons fournis par le système. La plupart des applications peuvent vous offre pas une option pour modifier la taille de IO et la file d’attente profondeur directement. Par exemple, si vous utilisez SQL Server, vous ne pouvez pas choisir la taille et file d’attente de la profondeur IO. SQL Server choisit les optimale IO taille et la file d’attente profondeur valeurs pour obtenir les meilleures performances. Il est important de comprendre les effets de ces deux types de facteurs sur les performances de votre application, afin que vous pouvez configurer des ressources appropriées pour répondre aux besoins de performances.

Dans cette section, consultez l’aide-mémoire des conditions requises application que vous avez créé, afin d’identifier combien vous avez besoin optimiser les performances de votre application. Sur la base, vous pourrez déterminer quels facteurs à partir de cette section vous devez régler. Assister les effets de chaque facteur sur les performances de votre application, utilisez les outils évaluation sur votre installation d’application. Reportez-vous à la section [Benchmarking](#Benchmarking) à la fin de cet article pour obtenir la procédure exécuter des outils d’évaluation courants sous Windows et Linux machines virtuelles.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimisation des sorties par, débit et la latence en un clin de œil  
Le tableau ci-dessous résume tous les facteurs de performances et les étapes pour optimiser sorties par, débit et la latence. Les sections suivantes ce résumé décrira chacune facteurs est beaucoup plus en détail.

| | **SORTIES PAR** | **Débit** | **Latence** |
|---|---|---|---|
| **Exemple de scénario** | Application OLTP entreprise nécessitant des transactions très haute taux par seconde.                                                                                                                                 | Données d’entreprise entrepôt application traitement de grandes quantités de données. | Près applications en temps réel nécessitant des instantanés réponses aux demandes de l’utilisateur, tels que des jeux en ligne. |
| Facteurs de performances  | | | |
| **Taille IO** | Taille IO donne sorties par une version ultérieure.                                                                                                                                                                           | Augmentation de la taille IO au produit débit plus élevé. | |
| **Taille de mémoire virtuelle** | Utilisez une taille de mémoire virtuelle qui propose des sorties par supérieure à vos besoins d’application. Voir les tailles de mémoire virtuelle et leurs limites sorties par ici. | Utiliser une taille de mémoire virtuelle avec limite de débit supérieure à vos besoins d’application. Voir les tailles de mémoire virtuelle et leurs limites de débit ici. | Utilisez une taille de mémoire virtuelle que des offres d’échelle limites supérieures à vos besoins d’application. Voir les tailles de mémoire virtuelle et leurs limites ici. |
| **Taille du disque** | Utilisez une taille de disque qui propose des sorties par supérieure à vos besoins d’application. Voir les tailles de disque et leurs limites sorties par ici. | Utiliser une taille de disque avec limite de débit supérieure à vos besoins d’application. Voir les tailles de disque et leurs limites de débit ici. | Utilisez une taille de disque que des offres d’échelle limites supérieures à vos besoins d’application. Voir les tailles de disque et leurs limites ici. |
| **Machine virtuelle et limites d’échelle de disque** | Sorties par limite de la taille de la mémoire virtuelle choisie doit être supérieure à sorties par total pilotées par les disques de stockage premium est jointes à. | Limite de débit de la taille de la mémoire virtuelle choisie doit être supérieure à débit total piloté par les disques de stockage premium est jointes à. | Limites d’échelle de la taille de la mémoire virtuelle choisi doivent être supérieures à limites échelle total premium joints des disques de stockage. |
| **Mise en cache disque** | Activer le Cache en lecture seule sur disques de stockage premium avec opérations lourdes de lecture pour obtenir des sorties par lecture plus élevé. | | Activer le Cache en lecture seule sur disques de stockage premium avec opérations dense prêtes pour accéder en lecture très faible latence. |
| **Répartition des disques** | Utiliser plusieurs disques et répartir les tâches pour obtenir une limite de débit et sorties par plus élevée combinée. Notez que la limite combinée par machine virtuelle doit être supérieure aux limites combinées de disques premium joints. | |
| **Taille de répartition** | Plus petite taille de répartition pour motif IO petite aléatoire vu dans des applications OLTP. Par exemple, utilisez taille de répartition de 64 Ko pour application OLTP SQL Server. | Augmentation de la taille rayures pour séquentiel motif IO grande vu dans les applications de Data Warehouse. Par exemple, utilisez taille de répartition 256 Ko pour l’application de SQL Server Data warehouse. | |
| **Multithread** | Utilisation multithread au plus grand nombre de demandes de transmission au stockage de Premium, vous aboutissez à une augmentation des sorties par et débit. Par exemple, sur SQL Server définir une valeur MAXDOP élevée à allouer plus de processeurs à SQL Server. | |
| **Longueur file d’attente** | Plus grande profondeur de file d’attente génère des sorties par une version ultérieure. | Plus grande profondeur de file d’attente donne un débit plus élevé. | Plus petit file d’attente profondeur donne latence inférieur. |

## <a name="nature-of-io-requests"></a>Nature des demandes IO  
Une requête IO est une unité de l’opération d’entrée/sortie chargés de votre application. Identification de la nature de requêtes IO, aléatoires ou séquentielles, lecture ou écriture, petite ou grande, vous permettra de déterminer les exigences de performances de votre application. Il est très important de comprendre la nature de requêtes IO, pour prendre les bonnes décisions lors de la conception de votre infrastructure d’application.

Taille IO est un des facteurs plus importants. La taille IO est la taille de la demande d’entrée/sortie opération générée par votre application. La taille IO a un impact sur les performances en particulier dans les sorties par la bande passante que l’application est en mesure d’atteindre. La formule suivante montre la relation entre sorties par, IO taille et la bande passante/débit.  
    ![](media/storage-premium-storage-performance/image1.png)

Certaines applications vous permettent de modifier leur taille IO, tandis que certaines applications ne pas autoriser. Par exemple, SQL Server détermine la taille IO optimale lui-même et ne fournit pas les utilisateurs avec les boutons Modifier. En revanche, Oracle fournit un paramètre appelé [DB\_bloquer\_taille](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) à l’aide de laquelle vous pouvez configurer la taille de demande d’e de la base de données.

Si vous utilisez une application qui n’autorise pas vous permet de modifier la taille IO, suivez les instructions de cet article pour optimiser les performances indicateur de performance clé qui correspond le mieux à votre application. Par exemple,

-   Une application OLTP génère des millions de requêtes IO petites et aléatoires. Pour gérer ces types de requêtes IO, vous devez concevoir votre infrastructure application afin d’obtenir des sorties par une version ultérieure.  
-   Un data warehouse application génère des demandes de IO volumineux et séquentielles. Pour gérer ces types de requêtes IO, vous devez concevoir votre infrastructure d’application pour obtenir plus de bande passante ou de débit.

Si vous utilisez une application, ce qui permet de modifier la taille IO, utilisez cette règle générale pour la taille IO en plus des autres directives de performances,

-   Taille IO pour obtenir des sorties par une version ultérieure. Par exemple, 8 Ko pour une application OLTP.  
-   Augmentation de la taille IO pour obtenir la bande passante/débit plus élevé. Par exemple, 1 024 Ko pour une application de data warehouse.

Voici un exemple sur comment calculer les sorties par débit/bande passante pour votre application. Prenez une application à l’aide d’un disque P30. La valeur maximale sorties par et débit/bande passante un disque P30 peuvent obtenir est sorties 5000 par et 200 Mo par seconde respectivement. Si votre application requiert les sorties par maximale à partir du disque P30 et que vous utilisez une plus petite taille IO comme 8 Ko, la bande passante qui en résulte vous ne pourrez pas obtenir est désormais 40 Mo par seconde. Toutefois, si votre application requiert la débit/bande passante maximale à partir du disque P30 et que vous utilisez une plus grande taille IO comme 1 024 Ko, les sorties par résultante sera inférieure, sorties 200 par. Par conséquent, réglez la taille IO tels qu’ils répondent aux deux votre sorties par et débit/bande passante nécessaires à une application. Tableau ci-dessous résume les différentes tailles IO et leurs sorties par et débit correspondant pour un disque P30.

| **Requis par l’application** | **Taille e/s** | **SORTIES PAR** | **Débit/bande passante** |
|-----------------------------|--------------|----------|--------------------------|
| Sorties par max                    | 8 KO         | 5 000    | 40 Mo par seconde         |
| Débit max              | 1 024 KO      | 200      | 200 Mo par seconde        |
| Max Throughput + sorties par haute  | 64 KO        | 3 200    | 200 Mo par seconde        |
| Max IOPS + haut débit  | 32 KO        | 5 000    | 160 Mo par seconde        |

Pour obtenir des sorties par et la bande passante supérieure à la valeur maximale d’un disque de stockage prime unique, utilisez plusieurs disques premium réparties ensemble. Par exemple, répartition deux P30 disques afin d’obtenir un combiné sorties par de 10 000 sorties par ou un débit combiné de 400 Mo par seconde. Comme indiqué dans la section suivante, vous devez utiliser une taille de mémoire virtuelle qui prend en charge le combiné de disque sorties par et débit.

>**Remarque :**  
>Si vous augmentez sorties par ou l’autre augmente également de débit, vérifiez que vous ne cliquez pas sur débit ou limites sorties par du disque ou machine virtuelle lorsque vous augmentez le d’eux.

Assister les effets de taille IO sur les performances de l’application, vous pouvez exécuter outils évaluation sur votre machine virtuelle et des disques. Créer plusieurs séries de tests et taille IO différente pour chaque série permet d’afficher l’impact. Reportez-vous à la section [Benchmarking](#Benchmarking) à la fin de cet article pour plus d’informations.

## <a name="high-scale-vm-sizes"></a>Grande échelle machine virtuelle tailles  
Lorsque vous commencez la conception d’une application, un des premières choses à faire est, sélectionnez une machine virtuelle pour héberger votre application. Stockage Premium est fourni avec les tailles de grande échelle machine virtuelle peuvent exécuter nécessitant la puissance de calcul supérieure ou un disque local haut les performances des applications. Ces machines virtuelles fournissent des processeurs plus rapides, un taux de mémoire-à-core plus élevé et un Solid-State lecteur (SSD) pour le disque local. Exemples de grande échelle machines virtuelles Premium stockage de prise en charge sont les séries DS, DSv2 et GS machines virtuelles.

Machines virtuelles échelle haute sont disponibles dans différentes tailles avec un nombre différent de cœurs du processeur, mémoire, du système d’exploitation et taille du disque temporaire. La taille de chaque machine virtuelle est également le nombre maximal de disques de données que vous pouvez joindre à la machine virtuelle. Par conséquent, la taille de mémoire virtuelle choisie détermineront la quantité de traitement, mémoire, et capacité de stockage est disponible pour votre application. Il affecte également le cluster et les coûts du stockage. Par exemple, voici les spécifications de la plus grande taille de mémoire virtuelle dans une série de service d’annuaire, série DSv2 et une série GS :

| Taille de mémoire virtuelle | Cœurs de processeur | Mémoire | Taille des disques machine virtuelle | Max. disques de données | Taille de cache | SORTIES PAR | Les limites de bande passante Cache IO |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | 112 GO | SYSTÈME D’EXPLOITATION = 1023 GO <br> SSD local = 224 Go | 32 | 576 GO | SORTIES PAR 50 000 <br> 512 Mo par seconde | Sorties par 4 000 et 33 Mo par seconde |
| Standard_GS5 | 32 | GO 448 | SYSTÈME D’EXPLOITATION = 1023 GO <br> SSD local = 896 Go | 64 | 4224 GO | SORTIES PAR 80 000 <br> 2 000 Mo par seconde | 5 000 e/s et 50 Mo par seconde |

Pour afficher une liste complète de toutes tailles machine virtuelle Azure disponibles, reportez-vous à [machine virtuelle Windows tailles](../virtual-machines/virtual-machines-windows-sizes.md) ou [tailles de machine virtuelle Linux](../virtual-machines/virtual-machines-linux-sizes.md). Choisir une taille de mémoire virtuelle qui peut se réunir et adapter aux besoins de performances de votre application souhaitée. En outre, prendre en compte suivant doivent tenir compte lors du choix de tailles de mémoire virtuelle.


*Limites d’échelle*  
Les limites maximales de sorties par machine virtuelle et par disque sont différents et indépendants entre eux. Assurez-vous que l’application est conduite sorties par les limites de la machine virtuelle, ainsi que les disques premium associés à celui-ci. Dans le cas contraire, performances de l’application conséquences la limitation.

Par exemple, supposons qu'une demande d’application est un maximum de 4 000 sorties par. Pour ce faire, vous devez configurer un disque P30 sur un ordinateur virtuel DS1. Le disque P30 pour pouvoir délivrer sorties par jusqu'à 5 000. Toutefois, la machine virtuelle DS1 est limitée aux sorties par 3 200. Par conséquent, les performances de l’application seront restreinte par la limite de mémoire virtuelle en sorties par 3 200 et il sera baisse des performances. Pour éviter que cette situation, choisissez une taille de machine virtuelle et le disque qui permettra à des répondre aux exigences de l’application.

*Coût de fonctionnement*  
Dans de nombreux cas, il est possible que votre coût total de l’opération à l’aide de stockage Premium est inférieur à l’utilisation du stockage Standard.

Par exemple, considérez une application nécessitant des sorties par 16 000. Pour obtenir des performances, vous devez une norme\_D14 Azure IaaS machine virtuelle, qui peut donner un sorties par maximale de 16 000 à l’aide de 32 disques de 1 To de stockage standard. Pour obtenir un maximum de 500 sorties par chaque disque de stockage standard de 1 To. La valeur estimée de cette machine virtuelle par mois sera $1,570. Le coût mensuel de 32 disques de stockage standard sera $1,638. Le coût mensuel total estimé sera $3,208.

Toutefois, si vous hébergée la même application sur le stockage Premium, vous devez une plus petite taille de la mémoire virtuelle et des disques de stockage moins premium, réduisant le coût total. Une norme\_DS13 machine virtuelle peut répondre au besoin sorties par 16 000 à l’aide de quatre disques P30. La machine virtuelle DS13 a un sorties par maximale de 25,600 et chaque disque P30 a un sorties par maximum de 5 000. En général, cette configuration peut atteindre 5 000 x 4 = 20 000 sorties par. La valeur estimée de cette machine virtuelle par mois sera $1,003. Le coût mensuel de quatre disques de stockage premium P30 sera $544.34. Le coût mensuel total estimé sera 1,544 $.

Tableau ci-dessous résume l’analyse des coûts de ce scénario pour le stockage de Premium et Standard.

| | **Standard** | **Premium** |
|---|---|---|
| **Coût de la machine virtuelle par mois** | $1,570.58 (standard\_D14)   | $1,003.66 (standard\_DS13) |
| **Coût de disques par mois** | $1,638.40 (disques 32 x 1 To) | $544.34 (4 x P30 disques) |
| **Coût total par mois**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Avec le stockage Azure Premium, vous obtenez le même niveau de Performance pour les machines virtuelles exécutant Windows et Linux. Nous prenons en charge plusieurs types de Linux distros, et vous pouvez voir la liste complète [ici](../virtual-machines/virtual-machines-linux-endorsed-distros.md). Il est important de noter que distros différents sont mieux adaptées aux différents types de charges de travail. Vous verrez les différents niveaux de performance selon la distro que votre charge de travail est en cours d’exécution. Tester la distros Linux avec votre application, puis sélectionnez celui qui convient le mieux.


Lorsque vous exécutez Linux avec un stockage Premium, vérifiez les dernières mises à jour sur les pilotes requis pour garantir haute performance.

## <a name="premium-storage-disk-sizes"></a>Taille des disques stockage Premium  
Stockage Premium Azure propose trois tailles de disque actuellement. La taille de chaque disque a une limite échelle différente pour sorties par, de bande passante et de stockage. Sélectionnez la taille de disque de stockage Premium selon les besoins des applications et l’échelle de grande taille de mémoire virtuelle de droite. Le tableau ci-dessous indique les tailles de trois disques et de leurs fonctions.

| **Type de disque**       | **P10**           | **P20**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Taille du disque           | Talon 128           | 512 talon           | 1024 talon (1 To)   |
| Disque par seconde       | 500               | 2300              | 5000              |
| Débit par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde |

Combien de disques que vous choisissez dépend du disque dimensionner choisi. Vous pouvez utiliser un seul P30 ou disques plusieurs P10 pour répondre aux besoins de votre application. Prendre en considération compte ci-dessous lorsque vous effectuez le choix.

*Limites d’échelle (sorties par et débit)*  
Les limites de débit et sorties par de chaque taille du disque Premium est différente et indépendamment des limites échelle machine virtuelle. Assurez-vous que les sorties par total et le débit à partir des disques sont limites d’échelle de la taille de mémoire virtuelle choisie.

Par exemple, si une demande d’application est un maximum de 250 Mo/sec débit et que vous utilisez une machine virtuelle DS4 avec un seul disque P30. La machine virtuelle DS4 peut donner jusqu'à 256 Mo/sec débit. Toutefois, un seul disque P30 a limite de débit de 200 Mo/s. Par conséquent, l’application sera restreinte à 200 Mo/s en raison de la limite de disque. Pour contourner cette limite, mise en service de plusieurs disques de données de la machine virtuelle.

>**Remarque :**  
>Lectures pris en charge par le cache ne sont pas inclus dans le disque sorties par et débit, donc pas soumis aux limites de disque. Le cache comporte sa limite de débit et sorties par séparé par machine virtuelle.
>
>Par exemple, initialement votre lecture et en écriture est 60 Mo/s et 40 Mo/s respectivement. Dans le temps, le cache préchauffage et sert de plus en plus de la lecture à partir du cache. Ensuite, vous pouvez obtenir plus élevé écriture débit à partir du disque.

*Nombre de disques*  
Déterminer le nombre de disques que vous aurez besoin à évaluer les besoins de l’application. La taille de chaque machine virtuelle a également une limite sur le nombre de disques que vous pouvez joindre à la machine virtuelle. En règle générale, il s’agit de deux fois le nombre de cœurs. Assurez-vous que la taille de la mémoire virtuelle que vous choisissez peut prendre en charge le nombre de disques requis.

N’oubliez pas les disques de stockage Premium ont plus élevées capacités de performances par rapport aux disques de stockage Standard. Par conséquent, si vous migrez votre application à partir d’Azure IaaS virtuelle à l’aide de stockage Standard au stockage Premium, vous devrez probablement moins de disques premium pour obtenir les performances même ou une version ultérieure pour votre application.

## <a name="disk-caching"></a>Mise en cache disque  
Machines virtuelles échelle haute qui s’appuient sur le stockage Azure Premium ont une technologie de mise en cache à plusieurs niveaux appelée BlobCache. BlobCache utilise une combinaison de la Machine virtuelle RAM et SSD local pour la mise en cache. Ce cache est disponible pour les disques permanentes Premium stockage et le local machine virtuelle. Par défaut, ce paramètre cache est défini en lecture/écriture pour disques du système d’exploitation et en lecture seule pour les données disques hébergés sur stockage Premium. Avec mise en cache disque activé sur les disques de stockage Premium, l’échelle haute machines virtuelles peut atteindre extrêmement élevées de performances qui dépassent les performances de disque sous-jacente.

>[AZURE.WARNING] Modifier le paramètre de cache d’un disque Azure détache et attache de nouveau le disque cible. S’il s’agit du disque système d’exploitation, la machine virtuelle est redémarrée. Arrêtez toutes les applications/services qui peuvent être affectées par cette interruption avant de modifier le paramètre cache du disque.

Pour plus d’informations sur le fonctionne de BlobCache, reportez-vous à l’intérieur billet de blog [Le stockage Azure Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Il est important activer le cache sur le jeu de disques. Doit gérer les que vous devez activer la mise en cache du disque sur un disque premium ou pas varient selon le modèle de charge de travail qu’il contient. Tableau ci-dessous indique la valeur par défaut des paramètres de cache de disques du système d’exploitation et de données.

| **Type de disque** | **Paramètre de Cache par défaut** |
|---|---|
| Disque du système d’exploitation | ReadWrite |
| Disque de données | Aucun |

Voici les paramètres de cache de disque recommandé pour les disques de données,

| **Paramètre de la mise en cache** | **Recommandation à quel moment utiliser ce paramètre** |
|---|---|
| Aucun | Configurez cache hôte comme aucun des disques écriture seule et écriture lourds. |
| En lecture seule | Configurer le cache de l’hôte comme en lecture seule pour disques en lecture seule et en lecture / écriture. |
| ReadWrite | Configurer le cache de l’hôte comme ReadWrite uniquement si votre application gère correctement écrire des données mises en cache sur disques permanentes lorsque cela est nécessaire. |

*En lecture seule*  
En configurant en lecture seule la mémoire cache de données du stockage Premium disques, vous pouvez obtenir faible latence de lecture et obtenir très haute sorties par lecture et débit pour votre application. Voici deux raisons d’échéance

1.  Lectures effectuées à partir de cache, qui se trouve sur la mémoire virtuelle et SSD local, sont beaucoup plus rapides que les lectures à partir du disque de données, qui se trouve sur le stockage blob Azure.  
2.  Stockage Premium ne compte pas les lectures provenant de cache, vers le disque sorties par et débit. Par conséquent, votre application est en mesure de réaliser des sorties par total supérieur et débit.

*ReadWrite*  
Par défaut, les disques du système d’exploitation ReadWrite cache sont activé. Nous avons récemment ajouté de prise en charge de ReadWrite la mémoire cache de données ainsi que des disques. Si vous utilisez ReadWrite mise en cache, vous devez disposer d’un moyen approprié pour écrire les données à partir du cache sur disques permanentes. Par exemple, SQL Server gère l’écriture de données mises en cache sur les disques de stockage permanent dans sa propre. À l’aide de cache ReadWrite avec une application qui ne traite pas conserver les données nécessaires peut affecter les pertes de données, si la machine virtuelle se bloque.

Par exemple, vous pouvez appliquer ces instructions à SQL Server exécuté sur un stockage Premium en procédant ainsi,

1.  Configurer des disques de stockage premium hébergeant des fichiers de données cache « En lecture seule ».  
    un.  L’introduction lit cache inférieur de la durée de requête SQL Server dans la mesure où les pages de données sont extraites beaucoup plus rapidement le cache par rapport à directement à partir des données des disques.  
    b.  Traitement lectures à partir du cache, signifie que débit supplémentaire est disponible à partir des disques de données premium. SQL Server peut utiliser ce débit supplémentaire vers récupération d’autres pages de données et d’autres opérations telles que la sauvegarde et de restauration, par lots charges et index est reconstruit.  
2.  Configurer « Aucun » en cache sur disques de stockage premium qui héberge les fichiers journaux.  
    un.  Les fichiers journaux ont principalement les opérations d’écriture lourds. Par conséquent, ils ne bénéficient pas à partir du cache en lecture seule.

## <a name="disk-striping"></a>Répartition des disques  
Lorsqu’une échelle haute que machine virtuelle est attachée avec plusieurs disques permanente stockage premium, les disques peuvent être réparties ensemble pour agréger leurs sorties par, la bande passante et la capacité de stockage.

Sous Windows, vous permet les espaces de stockage répartition disques ensemble. Vous devez configurer une colonne pour chaque disque dans un pool. Dans le cas contraire, les performances globales du volume fractionné peuvent être plus basse que prévu, en raison d’inégale du trafic entre les disques.

Important : À l’aide de UI Server Manager, vous pouvez définir le nombre total de colonnes jusqu'à 8 pour un volume fractionné. Lorsque vous joignez plus de 8 disques, utiliser PowerShell pour créer le volume. À l’aide de PowerShell, vous pouvez définir le nombre de colonnes égal au nombre de disques. Par exemple, s’il existe 16 disques dans un agrégat unique ; Spécifiez 16 colonnes dans le paramètre *NumberOfColumns* de l’applet de commande PowerShell *New-VirtualDisk* .


Sous Linux, utilisez l’utilitaire MDADM pour disques répartition gagnante. Pour plus d’informations sur des disques répartition Linux reportez-vous à [Configurer les logiciels RAID sous Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Taille de répartition*  
Une configuration importantes dans agrégat est la taille de répartition. La taille de la répartition ou la taille de bloc est le plus petit morceau de données application peut répondre sur un volume fractionné. La taille de répartition que vous configurez dépend du type d’application et son modèle de requête. Si vous choisissez la taille de répartition incorrect, il peut entraîner le décalage IO, qui permet d’accéder à affecter les performances de votre application.

Par exemple, si une demande de IO générée par votre application est supérieure à la taille de répartition du disque, le système de stockage écrit au-delà des limites de répartition unité sur plus d’un disque. Lorsqu’il est temps pour accéder à ces données, il faudra effectuer de recherches sur plusieurs unités de bande pour terminer la demande. L’effet cumulé d’un tel comportement peut entraîner une dégradation importante des performances. En revanche, si la taille de la requête IO est inférieure à la taille de la répartition et s’il s’agit effectue de manière aléatoire, les requêtes IO peuvent ajouter sur le même disque à l’origine d’un critique et finalement dégrader les performances IO.


Selon le type de charge de travail votre application est en cours d’exécution, choisissez une taille de répartition appropriée. Pour les demandes IO peu aléatoires, utilisez une plus petite taille de répartition. Tandis que, pour les grande IO séquentiel demandes utilisent une plus grande taille de répartition. Découvrez les recommandations répartition taille pour l’application que vous exécuterez sur le stockage Premium. Pour SQL Server, configurez taille de répartition de 64 Ko pour les charges de travail OLTP et 256 Ko pour les charges de travail entrepôt de données. Voir [les procédures recommandées pour SQL Server sur machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) pour en savoir plus.


>**Remarque :**  
>Vous pouvez répartir un maximum de 32 premium stockage disques sur une série de service d’annuaire machine virtuelle et 64 premium stockage sur une série GS machine virtuelle.

## <a name="multi-threading"></a>Multi-thread  
Azure conçue plate-forme de stockage Premium à être considérablement en parallèle. Par conséquent, une application multi-thread atteint la quantité performances supérieures à celles d’une application à thread unique. Une application multi-thread fractionne ses tâches entre plusieurs threads et augmente l’efficacité de son exécution en utilisant les ressources machine virtuelle et le disque à la valeur maximale.

Par exemple, si votre application s’exécute sur un seul cœur machine virtuelle à l’aide de deux threads, l’UC pouvez basculer entre les deux threads pour atteindre l’efficacité. Pendant un thread est en attente sur un disque IO pour terminer, l’UC pouvez basculer vers l’autre thread. De cette façon, deux threads peuvent faire plus qu’un seul thread. Si la machine virtuelle possède plusieurs core, il réduit davantage la durée en cours d’exécution dans la mesure où chaque cœur peut exécuter des tâches en parallèle.

Vous ne pourrez pas modifier la façon dont une application prête à mettent en œuvre, seul thread ou multi-thread. Par exemple, SQL Server est capable de gérer plusieurs UC et multi-core. Toutefois, SQL Server décide les conditions dans lesquelles il sera exploiter un ou plusieurs threads pour traiter une requête. Il peut exécuter des requêtes et créer des index à l’aide de la fonction multithread. Pour une requête qui entraîne la participation à grands tableaux et tri des données avant de revenir à l’utilisateur, SQL Server utilisera probablement plusieurs threads. Toutefois, un utilisateur ne peut pas contrôler si SQL Server exécute une requête à l’aide d’un ou plusieurs threads plusieurs unique.

Il existe des paramètres de configuration que vous pouvez modifier pour influencer ce multi-thread ou en parallèle traitement d’une application. Par exemple, en cas de SQL Server, il est la configuration de degré de parallélisme maximale. Ce paramètre appelé MAXDOP, vous pouvez configurer le nombre maximal de processeurs que SQL Server peut utiliser lorsque parallèles de traitement. Vous pouvez configurer MAXDOP pour les requêtes individuels ou les opérations d’indexation. Il s’agit utile lorsque vous voulez équilibrer les ressources de votre système pour une application stratégique performances.

Par exemple, supposons que votre application à l’aide de SQL Server s’exécute une requête de grande taille et une opération d’index en même temps. Supposons que vous voulez que l’opération d’indexation soit plus performant par rapport à la requête de grande taille. Dans ce cas, vous pouvez définir la valeur MAXDOP de l’opération d’indexation supérieure à la valeur MAXDOP pour la requête. De cette façon, SQL Server a plus nombre de processeurs qui il peut s’appuient pour l’opération d’indexation par rapport au nombre de processeurs qu'il peut dédier à la requête de grande taille. N’oubliez pas, vous ne contrôlez pas le nombre de threads SQL Server utilisera pour chaque opération. Vous pouvez contrôler le nombre maximal de processeurs étant dédiée pour multi-thread.

En savoir plus sur les [Degrés de parallélisme](https://technet.microsoft.com/library/ms188611.aspx) dans SQL Server. Découvrez ces paramètres qui influencent multi-thread dans votre application et leur configuration pour optimiser les performances.

## <a name="queue-depth"></a>Longueur file d’attente  
La longueur file d’attente ou la file d’attente ou la taille de la file d’attente est le nombre de requêtes IO en attente dans le système. La valeur de profondeur file d’attente détermine le nombre d’opérations IO votre application peut d’aligner, qui va traiter les disques de stockage. Il affecte tous les indicateurs de performance trois application nous mentionnés dans cet article savoir, sorties par, débit et la latence.

Axe de profondeur et multi-thread sont en file d’attente étroitement liées. La valeur de profondeur file d’attente indique la quantité multi-thread qui peut être atteint par l’application. Si la file d’attente est volumineuse, application peut exécuter plusieurs opérations simultanément, en d’autres termes, plus multi-thread. Si la file d’attente est petite, même si l’application est multi-thread, il n’ont pas suffisamment demandes alignés pour une exécution simultanée.

En règle générale, désactiver l’étagère applications autorisent pas à modifier la file d’attente, car si défini incorrectement il va faire plus de mal que de bien. Applications définira la valeur correcte de profondeur file d’attente pour obtenir des performances optimales. Toutefois, il est important de comprendre ce concept afin que vous pouvez résoudre les problèmes de performances avec votre application. Vous pouvez également observer les effets de profondeur de file d’attente en exécutant des outils d’évaluation sur votre système.

Certaines applications fournissent des paramètres pour influencer la file d’attente. Par exemple, le paramètre (degré maximal de parallélisme) MAXDOP dans SQL Server expliqués dans la section précédente. MAXDOP est un moyen d’influencer profondeur file d’attente et multi-thread, même si elle ne change pas directement la valeur de profondeur de file d’attente de SQL Server.

*Axe de profondeur haute file d’attente*  
Une profondeur haute file d’attente s’aligne plusieurs opérations sur le disque. Le disque sait la requête suivante dans la file d’attente à l’avance. Par conséquent, le disque peut planifier des opérations d’avance et de les traiter dans une séquence optimale. Étant donné que l’application envoie plus de requêtes sur le disque, le disque peut traiter plus IOs parallèles. Pour finir, l’application seront en mesure de réaliser des sorties par une version ultérieure. Étant donné que l’application est en train de traiter plus de requêtes, le débit total de l’application augmente également.

En règle générale, une application peut optimiser le débit avec 8-16 + IOs en suspens par disque connecté. Si un axe de profondeur file d’attente, application n’est pas reçue suffisamment IOs au système, et il traite moins le montant de sur une période donnée. En d’autres termes, un débit inférieur.

Par exemple, dans SQL Server, définition de la valeur MAXDOP d’une requête à « 4 » vous informe SQL Server qu’il peut utiliser jusqu'à quatre cœurs pour exécuter la requête. SQL Server détermine quelle est la meilleure valeur profondeur file d’attente et le nombre de cœurs pour l’exécution des requêtes.

*Axe de profondeur optimale file d’attente*  
Valeur de profondeur très haute file d’attente est également ses inconvénients. Si la valeur de profondeur file d’attente est trop élevé, l’application essaiera de sorties très haute par le lecteur. À moins d’avoir application a disques permanentes avec sorties par généré suffisant, cela peut nuire aux latence d’applications. Suivi formule montre la relation entre les sorties par, de latence et de profondeur de file d’attente.  
    ![](media/storage-premium-storage-performance/image6.png)

Vous ne devez pas configurer profondeur file d’attente à n’importe quelle valeur haute, mais à une valeur optimale pouvant atteindre suffisamment sorties par pour l’application sans affecter la latence. Par exemple, si la latence des applications doit être 1 millisecondes, la file d’attente requis pour atteindre 5 000 sorties par est, QD = 5000 x 0.001 = 5.

*Longueur file d’attente pour agrégé*  
Pour un volume fractionné, mettre à jour une profondeur file d’attente suffisamment élevé tel que chaque disque a une profondeur de file d’attente pointe individuellement. Par exemple, considérez une application qui envoie une profondeur file d’attente de 2 et il existe 4 disques de la répartition. Les deux demandes IO seront dirigés vers deux disques et restant deux disques sera inactif. Par conséquent, configurez la file d’attente telles que tous les disques peuvent être occupé (e). Formule ci-dessous montre comment déterminer la file d’attente des volumes rayées.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>La limitation  
Dispositions Premium stockage Azure spécifié nombre de débit et sorties par selon les tailles de machine virtuelle et les tailles de disque que vous choisissez. Chaque fois que votre application tente de lecteur sorties par ou débit au-dessus de ces limites de ce que la machine virtuelle ou un disque peut gérer, stockage Premium sera l’accélération. Cela manifestes sous la forme d’affecter les performances de votre application. Cela peut signifie latence plus élevée, réduire débit ou abaisser sorties par. Si le stockage Premium ne se limite pas, votre application peut échouer complètement en dépassant que ses ressources sont capables d’atteindre. Par conséquent, pour éviter des problèmes de performances en raison de la limitation, toujours mise en service des ressources suffisantes pour votre application. Prendre en considération que nous mentionnés dans les sections de tailles de disque ci-dessus et tailles de machine virtuelle. Analyse comparative est la meilleure façon d’identifier les ressources auxquelles vous devez héberger votre application.

## <a name="benchmarking"></a>Analyse comparative  
Analyse comparative est le processus de la simulation différentes charges de travail sur votre application et de mesurer les performances de l’application pour chaque charge de travail. À l’aide de la procédure décrite dans une section précédente, vous avez collectées des performances requises. En exécutant les outils d’évaluation sur les ordinateurs virtuels qui héberge l’application, vous pouvez déterminer les niveaux de performances que votre application peut faire avec stockage Premium. Dans cette section, nous vous fournir exemples d’évaluation d’une machine virtuelle DS14 Standard mis en service avec disques de stockage de Premium Azure.

Nous avons utilisé des outils évaluation courants Iometer et FIO, pour Windows et Linux respectivement. Ces outils créer plusieurs threads simuler une production comme charge de travail et les performances du système de mesure. L’aide des outils vous pouvez également configurer des paramètres tels que la profondeur bloc de taille et la file d’attente, que vous ne pouvez pas normalement modifier pour une application. Cela vous donne davantage de flexibilité pour optimiser les performances du lecteur sur une échelle haute machine virtuelle sa mise en service avec disques premium pour les différents types de charges de travail application. Pour en savoir que plus sur chaque outil tests d’évaluation, visitez [Iometer](http://www.iometer.org/) et [FIO](http://freecode.com/projects/fio).

Pour suivre les exemples ci-dessous, créez une machine virtuelle DS14 Standard, joignez 11 disques Premium stockage de la machine virtuelle. Des 11 disques, configurez 10 disques par hôte de la mise en cache en tant que « Aucun » et les répartir dans un volume nommé NoCacheWrites. Configurer l’hôte cache en tant que « En lecture seule » sur le disque restant et créer un volume appelé CacheReads avec ce disque. À l’aide de ce programme d’installation, vous serez en mesure de voir les performances de lecture et d’écriture maximale à partir d’un ordinateur virtuel DS14 Standard. Pour plus d’informations sur la création d’une machine virtuelle DS14 avec disques premium, accédez à [créer et utiliser compte de stockage Premium pour un disque de données machine virtuelle](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Mise en route du Cache*  
Le disque avec hôte en lecture seule la mise en cache sera en mesure de donner des sorties par une version ultérieure à la limite de disque. Tout d’abord pour optimiser les performances cette lecture à partir du cache hôte, vous devez mat le cache de ce disque. Ainsi que le fichier en lecture IOs quel outil évaluation déterminera sur volume CacheReads réellement atteint le cache et n’est pas le disque directement. Le résultat de correspondances de cache dans sorties par supplémentaires à partir du cache unique activé disque.

>**Important :**  
>Vous devez mat le cache avant d’exécuter comparative, chaque redémarrage machine virtuelle.

#### <a name="iometer"></a>Iometer   
[Téléchargez l’outil Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) sur l’ordinateur virtuel.

*Fichier de test*  
Iometer utilise un fichier de test qui se trouve sur le volume sur lequel vous exécuterez le test d’évaluation. Il lit les lecteurs et écrit sur ce fichier de test mesurer le disque e/s et débit. Iometer crée ce fichier de test si vous n’avez pas indiqué. Créer un fichier de test 200 Go appelé iobw.tst sur les volumes CacheReads et NoCacheWrites.

*Caractéristiques de Microsoft Access*  
Les spécifications, demander IO taille, % en lecture/écriture, % aléatoire/séquentielle sont configurés d’à l’aide de l’onglet « Caractéristiques de Microsoft Access » dans Iometer. Créer une spécification d’accès pour chacun des scénarios décrites ci-dessous. Créez les spécifications access et « Enregistrer » avec un nom similaire – RandomWrites\_8K, RandomReads\_8K. Sélectionnez la spécification correspondante lorsque vous exécutez le scénario de test.

Voici un exemple de caractéristiques de Microsoft access pour le scénario sorties par écrire maximal ci-dessous,  
    ![](media/storage-premium-storage-performance/image8.png)

*Spécifications de Test sorties par maximale*  
Pour illustrer sorties par maximales, utilisez plus petite taille de la demande. Utiliser la taille de la requête 8 Ko et créer les caractéristiques de lecture et l’écrit aléatoire.

| Spécification d’accès | Taille de la requête | % Aléatoire | % De lecture |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K     | 8K           | 100      | 0      |
| RandomReads\_8K      | 8K           | 100      | 100    |

*Spécifications de Test de débit maximal*  
Pour illustrer débit maximal, utilisez plus grande taille de la requête. Utiliser la taille de la requête 64 Ko et créer les caractéristiques de lecture et l’écrit aléatoire.

| Spécification d’accès | Taille de la requête | % Aléatoire | % De lecture |
|----------------------|--------------|----------|--------|
| RandomWrites\_64 Ko    | 64 KO          | 100      | 0      |
| RandomReads\_64 Ko     | 64 KO          | 100      | 100    |

*Exécution des tests Iometer*  
Effectuer les étapes ci-dessous pour mat cache

1.  Créer deux caractéristiques d’access avec les valeurs ci-dessous,

  	| Nom              | Taille de la requête | % Aléatoire | % De lecture |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1 Mo | 1 MO          | 100      | 0      |
  	| RandomReads\_1 Mo  | 1 MO          | 100      | 100    |

2.  Exécutez le test Iometer pour l’initialisation de cache disque avec les paramètres suivants. Utilisez les trois threads de travail pour le volume cible et une profondeur file d’attente de 128. Définissez la durée « Exécution » du test sur 2hrs sous l’onglet « Tester le programme d’installation ».

  	| Scénario              | Volume cible | Nom              | Durée |
  	|-----------------------|---------------|-------------------|----------|
  	| Initialisation du Cache disque | CacheReads    | RandomWrites\_1 Mo | 2hrs     |

3.  Exécutez le test Iometer pour la mise en route cache disque avec les paramètres suivants. Utilisez les trois threads de travail pour le volume cible et une profondeur file d’attente de 128. Définissez la durée « Exécution » du test sur 2hrs sous l’onglet « Tester le programme d’installation ».

  	| Scénario           | Volume cible | Nom             | Durée |
  	|--------------------|---------------|------------------|----------|
  	| Chaudes le Cache disque | CacheReads    | RandomReads\_1 Mo | 2hrs     |

Une fois le cache disque est mis en température vers le haut, poursuivre les scénarios de test présentées ci-après. Pour exécuter le test Iometer, utilisez au moins trois threads de travail pour **chaque** volume cible. Pour chaque thread de travail, sélectionner le volume cible, définissez une profondeur de file d’attente et sélectionnez une des spécifications tests enregistré, comme indiqué dans le tableau ci-dessous, pour exécuter le scénario de test correspondant. Le tableau indique également les résultats attendus pour débit et sorties par lors de l’exécution de ces tests. Pour tous les scénarios, une petite taille de IO de 8 Ko et une profondeur file d’attente élevé de 128 sont utilisés.

| Scénario de test      | Volume cible | Nom              | Résultat       |
|--------------------|---------------|-------------------|--------------|
| Max. Sorties par lecture     | CacheReads    | RandomWrites\_8K  | SORTIES PAR 50 000  |
| Max. Écriture    | NoCacheWrites | RandomReads\_8K   | 64 000 SORTIES PAR  |
| Max. Sorties par combinées | CacheReads    | RandomWrites\_8K  | SORTIES PAR 100 000 |
|                    | NoCacheWrites | RandomReads\_8K   |              |
| Max. Lecture Mo/s   | CacheReads    | RandomWrites\_64 Ko | Mo 524/sec   |
| Max. Écriture Mo/s  | NoCacheWrites | RandomReads\_64 Ko  | Mo 524/sec   |
| Combinées Mo/sec    | CacheReads    | RandomWrites\_64 Ko | 1 000 Mo/sec  |
|                    | NoCacheWrites | RandomReads\_64 Ko  |              |

Voici les captures d’écran de la Iometer tester les résultats des scénarios sorties par et débit combinées.

*Lectures combinées et écritures sorties par maximale*  
![](media/storage-premium-storage-performance/image9.png)

*Lectures combinées et écritures débit maximal*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO est un outil populaire au stockage de référence sur les ordinateurs virtuels Linux. Il a la flexibilité pour sélectionner différentes tailles IO séquentielles ou lectures aléatoires et écrit. Qu’il engendre threads de travail ou processus pour effectuer les opérations d’e/s spécifiées. Vous pouvez spécifier le type d’opérations e/s que chaque thread de travail doit effectuer à l’aide de fichiers de travail. Nous avons créé un fichier de projet par scénario illustré dans les exemples ci-dessous. Vous pouvez modifier les spécifications dans ces fichiers de travail pour tester différentes charges de travail en cours d’exécution sur le stockage Premium. Dans les exemples, nous utilisons un service d’annuaire 14 ordinateur virtuel Standard **Ubuntu**en cours d’exécution. Utiliser la même configuration décrite au début de la [section Benchmarking](#Benchmarking) et chaude le cache avant d’exécuter les tests d’évaluation.

Avant de commencer, [Téléchargez FIO](https://github.com/axboe/fio) et installez-le sur votre ordinateur virtuel.

Exécutez la commande suivante pour Ubuntu,

        apt-get install fio

Nous allons utiliser quatre threads pour susciter opérations d’écriture et quatre threads de travail pour conduite opérations de lecture sur les disques. Les travailleurs de l’écriture seront conduite le trafic sur le volume « nocache », c'est-à-dire 10 disques avec cache défini sur « Aucune ». Les travailleurs de lecture seront conduite le trafic sur le volume « readcache », c'est-à-dire 1 disque avec cache défini sur « En lecture seule ».

*Écriture maximale sorties par*  
Créer le fichier de travail avec les spécifications suivantes pour obtenir maximale sorties par écrire. Nommez-la « fiowrite.ini ».

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Remarque Les événements clés suivre qui sont en conformité avec les règles de conception mentionnés dans les sections précédentes. Ces caractéristiques sont essentiels pour piloter sorties par maximale,  
-   Une profondeur haute file d’attente de 256.  
-   Un blocs de petite taille de 8 Ko.  
-   Plusieurs threads exécutant écritures aléatoires.

Exécutez la commande suivante pour démarrer le test FIO pendant 30 secondes,  

    sudo fio --runtime 30 fiowrite.ini

Pendant l’exécution du test, vous ne pourrez pas voir le nombre d’écriture pour la machine virtuelle et des disques Premium délivrerez. Comme le montre l’exemple ci-dessous, la machine virtuelle DS14 a décidé de son écriture maximale limite sorties par des sorties par 50 000.  
    ![](media/storage-premium-storage-performance/image11.png)

*Nombre maximal de lectures sorties par*  
Créer le fichier de travail avec les spécifications suivantes pour obtenir des sorties par lecture maximale. Nommez-la « fioread.ini ».

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Remarque Les événements clés suivre qui sont en conformité avec les règles de conception mentionnés dans les sections précédentes. Ces caractéristiques sont essentiels pour piloter sorties par maximale,

-   Une profondeur haute file d’attente de 256.  
-   Un blocs de petite taille de 8 Ko.  
-   Plusieurs threads exécutant écritures aléatoires.

Exécutez la commande suivante pour démarrer le test FIO pendant 30 secondes,

    sudo fio --runtime 30 fioread.ini

Pendant l’exécution du test, vous ne pourrez pas voir le nombre de lire sorties par la machine virtuelle et des disques Premium délivrerez. Comme le montre l’exemple ci-dessous, la machine virtuelle DS14 a décidé de plus de 64 000 e/s en lecture. Il s’agit d’une combinaison du disque et les performances du cache.  
    ![](media/storage-premium-storage-performance/image12.png)

*Maximum lire et écrire des sorties par*  
Créer le fichier de projet par suivante spécifications maximale combinées lire et écrire des sorties par. Nommez-la « fioreadwrite.ini ».

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Remarque Les événements clés suivre qui sont en conformité avec les règles de conception mentionnés dans les sections précédentes. Ces caractéristiques sont essentiels pour piloter sorties par maximale,

-   Une profondeur file d’attente élevé de 128.  
-   Un blocs de petite taille de 4 Ko.  
-   Plusieurs threads exécutant aléatoire lit et écrit.

Exécutez la commande suivante pour démarrer le test FIO pendant 30 secondes,

    sudo fio --runtime 30 fioreadwrite.ini

Pendant que le test s’exécute, vous ne pourrez pas voir le nombre de combinées en lecture et en écriture pour la machine virtuelle et des disques Premium délivrerez. Comme le montre l’exemple ci-dessous, la machine virtuelle DS14 propose plus de 100 000 lecture combinées et sorties par écrire. Il s’agit d’une combinaison du disque et les performances du cache.  
    ![](media/storage-premium-storage-performance/image13.png)

*Maximum combinées débit*  
Pour obtenir la valeur maximale combinées lire et écrire débit, utilisez une plus grande taille de bloc et profondeur grande file d’attente avec plusieurs threads exécutant lecture et en écriture. Vous pouvez utiliser une taille de bloc de 64 Ko et profondeur file d’attente de 128.

## <a name="next-steps"></a>Étapes suivantes  

En savoir plus sur le stockage Azure Premium :

- [: Premium High Performance de stockage pour les charges de travail Azure Machine virtuelle](storage-premium-storage.md)  

Pour les utilisateurs de SQL Server, lisez les articles sur les procédures recommandées pour SQL Server :

- [Les procédures recommandées pour SQL Server dans des Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Stockage Premium Azure propose plus haute performance pour SQL Server dans la machine virtuelle Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
