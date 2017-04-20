<properties
   pageTitle="Planification des capacités pour les applications de Service TISSU | Microsoft Azure"
   description="Décrit comment identifier le nombre de nœuds de calcul requis pour une application de Service TISSU"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Planification pour les applications de Service tissu de la capacité


Ce document vous apprend à évaluer la quantité de ressources (UC, RAM, stockage sur disque), que vous devez exécuter vos applications Azure Service tissu. Il est courant pour vos besoins en ressources modifier le temps. En règle générale, vous avez besoin peu de ressources que vous développez/tester votre service, puis nécessitent davantage de ressources lorsque vous accédez en production et que votre application grandit en popularité. Lorsque vous concevez votre application, pensez selon les besoins à long terme et assurez-vous de choix permettant d’effectuer votre service à l’échelle pour répondre à forte demande des clients.

 Lorsque vous créez un cluster tissu de Service, vous déterminez les types de machines virtuelles (machines virtuelles) qui composent le cluster. Chaque machine virtuelle est fourni avec une quantité limitée de ressources dans le formulaire de processeurs (cœurs et la vitesse), la bande passante réseau, RAM et espace disque. Lorsque votre service s’étend au fil du temps, vous pouvez mettre à niveau vers machines virtuelles qui offrent davantage de ressources et/ou ajouter davantage d’ordinateurs virtuels à votre cluster. Pour effectuer ce dernier, vous devez concevoir votre service initiale afin qu’il peut tirer parti des nouvelles machines virtuelles dynamiquement ajoutées au cluster.

Certains services ne gérer peu aucune données sur les ordinateurs virtuels eux-mêmes. Par conséquent, planifier la capacité de ces services doit se concentrer principalement sur les performances, ce qui signifie que la sélection de l’UC appropriées (cœurs et la vitesse) des ordinateurs virtuels. En outre, vous devez prendre en compte la bande passante réseau, y compris la fréquence à laquelle les transferts de réseau sont produisent et que la quantité de données est transféré. Si votre service doit procéder ainsi que les augmentations de l’utilisation de service, vous pouvez ajouter plusieurs machines virtuelles du solde cluster et charger les demandes réseau sur tous les ordinateurs virtuels.

Pour les services qui gérer de grandes quantités de données sur les ordinateurs virtuels, la planification de la capacité doit se concentrer sur la taille. Par conséquent, vous devez prendre en compte avec soin la capacité de RAM de l’ordinateur et le stockage de disque. Le système de gestion de mémoire virtuelle dans Windows rend l’espace disque ressemblent RAM au code de l’application. En outre, le runtime Service TISSU fournit smart pagination tout en conservant les données actif uniquement en mémoire et déplacement des données à froid sur le disque. Les applications peuvent utiliser et donc plus de mémoire qu’est physiquement disponible sur l’ordinateur virtuel. Avoir plus de mémoire vive simplement améliore les performances, étant donné que la machine virtuelle peut laisser davantage de stockage disque RAM. La machine virtuelle que vous sélectionnez doit disposer d’un disque suffisant pour stocker les données que vous souhaitez sur l’ordinateur virtuel. De même, la machine virtuelle doit avoir suffisamment de mémoire vive afin d’améliorer les performances de que votre choix. Si les données de votre service augmentent au fil du temps, vous pouvez ajouter davantage d’ordinateurs virtuels pour le cluster et partition les données au sein de tous les ordinateurs virtuels.

## <a name="determine-how-many-nodes-you-need"></a>Déterminer le nombre de nœuds que vous avez besoin

Partition votre service vous permet de mettre à l’échelle des données de votre service. Pour plus d’informations sur partition, voir [Partition Service TISSU](service-fabric-concepts-partitioning.md). Chaque partition doit tenir dans une seule machine virtuelle, mais plusieurs partitions (petites) peuvent être placées sur un ordinateur virtuel unique. Par conséquent, avec plusieurs partitions petites offre davantage de souplesse plutôt que quelques partitions plus grandes. Le compromis est qu’ayant un nombre important de partitions augmente la charge de tissu de Service et vous ne pouvez pas effectuer les opérations traitées entre partitions. Il existe également le trafic réseau potentiels plus si votre code de service doit fréquemment accéder aux éléments de données qui résident dans des partitions différentes. Lorsque vous créez votre service, vous devez envisager avec soin ces avantages et inconvénients d’arriver à une stratégie efficace partition.

Supposons que votre application comporte un seul service dynamique ayant une taille de la banque que vous prévoyez d’atteindre DB_Size Go par an. Vous êtes prêt à ajouter plus d’applications (et partitions) que vous rencontrez croissance au-delà de cette année.  Le facteur de réplication (HF), qui détermine le nombre de réplica pour votre service a un impact sur la DB_Size total. Le total DB_Size entre tous les réplicas est le facteur de réplication multiplié par DB_Size.  Node_Size représente l’espace disque/de RAM par nœud que vous souhaitez utiliser pour votre service. Pour de meilleures performances, la DB_Size doit mise en page en mémoire au sein du cluster et un Node_Size autour de la RAM de la machine virtuelle doivent être choisies. En attribuant un Node_Size qui est supérieure à la capacité de RAM, vous reposent sur la pagination fourni par le runtime tissu de Service. Par conséquent, les performances ne soient pas optimales si vos données entières sont considéré comme actif (depuis les données sont chargées dans/arrière). Toutefois, pour de nombreux services où qu’une fraction des données est active, il est plus économique.

Le nombre de nœuds requis pour optimiser les performances peut être calculé comme suit :

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Compte pour la croissance

Vous souhaiterez peut-être calculer le nombre de nœuds basés sur DB_Size que vous attendez votre service atteigne, outre le DB_Size que vous avez commencé avec. Ensuite, augmenter le nombre de nœuds fur et à votre service de sorte que vous le nombre de nœuds n’êtes pas trop mise en service. Mais le nombre de partitions doit être en fonction du nombre de nœuds qui sont nécessaires lorsque vous exécutez votre service lors de la croissance maximale.

Il est préférable d’avoir certains ordinateurs supplémentaires disponibles à tout moment pour que vous pouvez gérer les pointes inattendus ou l’échec (par exemple, si plusieurs machines virtuelles aller vers le bas).  Tandis que la capacité supplémentaire doit être déterminée à l’aide de votre souris attendus, un point de départ consiste à réserver quelques machines virtuelles supplémentaires (5 à 10 % supplémentaire).

Ce qui précède suppose un service dynamique unique. Si vous avez plusieurs services avec état, vous devez ajouter la DB_Size associés aux autres services dans l’équation. Vous pouvez également calculer le nombre de nœuds séparément pour chaque service dynamique.  Votre service peut avoir réplicas ou partitions qui ne sont pas équilibrées. N’oubliez pas que les partitions peuvent aussi avoir plus de données que d’autres personnes. Pour plus d’informations sur partition, voir [l’article sur les meilleures pratiques de partition](service-fabric-concepts-partitioning.md). Toutefois, l’équation précédente est partition et réplica indépendante, car TISSU Service garantit que les réplicas sont répartis à travers les nœuds de manière optimisée.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utiliser une feuille de calcul pour le calcul des coûts

Maintenant nous allons placer certains nombres réels dans la formule. Une [feuille de calcul exemple](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) indique comment planifier la capacité pour une application qui contient trois types d’objets de données. Pour chaque objet, nous rapprocher sa taille et objets combien que nous prévoyons d’avoir. Nous allons sélectionner également réplicas combien nous voulons de chaque type d’objet. La feuille de calcul calcule le montant total de mémoire stocké dans le cluster.

Ensuite, nous entrer une taille de mémoire virtuelle et un coût mensuel. Selon la taille de la mémoire virtuelle, la feuille de calcul indique le nombre minimal de partitions que vous permet de fractionner vos données pour l’ajuster physiquement sur les nœuds. Souhaitée un grand nombre de partitions pour prendre en charge de calcul spécifiques de votre application et doit être le trafic réseau. La feuille de calcul indique le nombre de partitions que gérez les objets de profil utilisateur est passé à partir d’un à six.

À présent, la feuille de calcul selon toutes ces informations, indique que vous pouvez obtenir physiquement toutes les données avec les partitions souhaitées et duplications sur un cluster 26 nœuds. Toutefois, ce cluster serait être forte compressé, afin de vous souhaiterez peut-être certains des nœuds supplémentaires pour prendre en charge les mises à jour et échecs de nœuds. La feuille de calcul affiche également qu’avoir plus de 57 nœuds ne fournit aucune valeur supplémentaire, car vous devez nœuds vides. Là encore, vous souhaiterez peut-être accédez au-dessus 57 nœuds quand même à prendre en compte les mises à jour et échecs de nœuds. Vous pouvez peaufiner la feuille de calcul pour correspondre à des besoins spécifiques de votre application.   

![Feuille de calcul pour le calcul des coûts][Image1]



## <a name="next-steps"></a>Étapes suivantes

Consultez les [services de structure de Service partition] [ 10] pour en savoir plus sur partition votre service.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
