<properties
pageTitle="Considérations sur l’utilisation d’images de machine virtuelle Oracle | Microsoft Azure"
description="Obtenir des informations sur les configurations prises en charge et limitations pour une machine virtuelle Oracle sur Windows Server dans Azure avant de déployer."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Considérations relatives à divers pour les images de machine virtuelle Oracle



Cet article aborde les considérations relatives à des machines virtuelles Oracle dans Azure, qui sont basés sur des images de logiciels Oracle fournis par Oracle.  

-  Images de machine virtuelle de base de données Oracle
-  Images des machines virtuelles WebLogic Server Oracle
-  Images des machines virtuelles JDK Oracle

##<a name="oracle-database-virtual-machine-images"></a>Images de machine virtuelle de base de données Oracle
### <a name="clustering-rac-is-not-supported"></a>Cluster (RAC) n’est pas pris en charge

Azure ne prend pas en charge Oracle Real Application Clusters (RAC) de la base de données Oracle. Seules les instances autonome de base de données Oracle sont possibles. C’est parce que Azure n’autorise pas actuellement partage de disque virtuel d’une manière en lecture/écriture entre plusieurs instances de machine virtuelle. UDP multidiffusion n’est pas prise en charge également.

### <a name="no-static-internal-ip"></a>Aucune adresse IP interne statique

Azure affecte chaque machine virtuelle une adresse IP interne. À moins que la machine virtuelle fait partie d’un réseau virtuel, l’adresse IP de la machine virtuelle est dynamique et peut changer après le redémarrage de l’ordinateur virtuel. Cela peut entraîner des problèmes, car la base de données Oracle attend l’adresse IP statique. Pour éviter ce problème, vous pouvez ajouter la machine virtuelle à un réseau virtuel Azure. Pour plus d’informations, voir [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) et [créer un réseau virtuel dans Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="attached-disk-configuration-options"></a>Options de configuration de disque connecté

Vous pouvez placer les fichiers de données soit le disque de système d’exploitation de la machine virtuelle ou sur les disques attachés, également connu sous le nom des disques de données. Disques connectés peuvent offrir davantage de flexibilité performances et la taille que le disque système d’exploitation. Le disque système d’exploitation peut être préférable uniquement pour les bases de données sous 10 gigaoctets (Go).

Disques connectés s’appuient sur le service de stockage Blob Azure. Chaque disque est capable de maximum théorique d’environ 500 entrées/sorties par seconde (sorties par). Les performances de disques connectés ne soient pas optimaux initiale et ses performances peuvent améliorer considérablement après une période « gravure in » d’environ 60 et 90 minutes d’opération continue. Si un disque par la suite reste inactif, ses performances risque de diminuer jusqu'à une autre période de gravure d’utilisation continue. En bref, la plus active un disque, plus il est de performances optimales approche.

Bien que la plus simple consiste à joindre un seul disque à la machine virtuelle et placer les fichiers de base de données qu’il contient, cette approche est également plus limitation en termes de performances. À la place, vous pouvez souvent améliorer les performances efficace si vous utilisez plusieurs disques connectés, répartir la base de données entre eux et puis Oracle Automatic Storage Management (ASM). Pour plus d’informations, voir [vue d’ensemble du stockage automatique Oracle](http://www.oracle.com/technetwork/database/index-100339.html) . Bien qu’il soit possible d’utiliser la répartition de plusieurs disques au niveau du système d’exploitation, cette approche est déconseillée, car il n’est pas connue pour améliorer ses performances.

Tenez compte des deux façons de joindre plusieurs disques selon que vous voulez classer par priorité les performances d’opérations de lecture ou d’écriture opérations pour votre base de données :

- **Oracle ASM dans sa propre** est susceptible de meilleures performances des opérations d’écriture, mais pire sorties par pour les opérations de lecture par rapport à l’approche à l’aide de tableaux de disque. L’illustration suivante représente logiquement cette disposition.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Évaluer le compromis entre les performances d’écriture et les performances de lecture d’un cas par cas. Vos résultats réels peuvent varier lorsque vous utilisez cette option.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Considérations relatives à la récupération après incident et disponibilité élevée

Lors de l’utilisation de la base de données Oracle dans des machines virtuelles Azure, vous êtes responsable de l’implémentation d’une solution récupération disponibilité et urgence élevée pour éviter les temps d’arrêt. Vous êtes également responsable de la sauvegarde de vos propres données et des applications.

Haute disponibilité et récupération d’urgence pour Oracle de base de données Enterprise Edition (sans RAC) sur Azure peut être obtenue à l’aide de la [protection des données, de protection de données Active](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)ou [Oracle forte porte](http://www.oracle.com/technetwork/middleware/goldengate), avec deux bases de données dans deux machines virtuelles distinctes. Les deux machines virtuelles doit être dans le même [service cloud](virtual-machines-linux-classic-connect-vms.md) et le même [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour vous assurer qu’ils peuvent accéder à eux via l’adresse IP permanente privée.  En outre, nous vous recommandons d’en plaçant les machines virtuelles dans la même [disponibilité définie](virtual-machines-windows-manage-availability.md) pour autoriser Azure placez-les dans les domaines d’erreur distincte et mettre à niveau des domaines. Uniquement des ordinateurs virtuels dans le service cloud même peut intervenir dans le même jeu de disponibilité. Chaque machine virtuelle doit avoir au moins de 2 Go de mémoire et 5 Go d’espace disque.

Avec la protection des données Oracle, haute disponibilité peut être obtenue avec une base de données principale dans une machine virtuelle, une base de données secondaire (en attente) dans une autre machine virtuelle et la réplication à sens unique configurer entre elles. Le résultat est accès en lecture à la copie de la base de données. Avec Oracle GoldenGate, vous pouvez configurer une réplication bidirectionnelle entre deux bases de données. Pour savoir comment configurer une solution de disponibilité pour vos bases de données à l’aide de ces outils, consultez documentation de [Protection de données Active](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sur le site Web Oracle. Si vous avez besoin d’accès en lecture-écriture à la copie de la base de données, vous pouvez utiliser la [Protection de données Oracle Active](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Images des machines virtuelles WebLogic Server Oracle

-  **Mise en cluster est pris en charge sur Enterprise Edition uniquement.** Vous êtes autorisé à utiliser WebLogic cluster uniquement lorsque vous utilisez WebLogic serveur Enterprise Edition. N’utilisez pas cluster avec WebLogic Server Standard Edition.

-  **Délais de connexion :** Si votre application repose sur les connexions aux points de terminaison publics d’un autre service cloud Azure (par exemple, un service de niveau de base de données), Azure se ferme ces connexions ouvertes après quatre minutes d’inactivité. Cela peut affecter les fonctionnalités et les applications basées sur des pools de connexion, car les connexions inactives depuis plus de cette limite peuvent rester n’est plus valides. Si cela affecte votre application, pensez à activer la logique « persistante » sur vos pools de connexion.

    Si un point de terminaison est *interne* à votre déploiement de service cloud Azure (par exemple, un ordinateur de virtuel autonome de base de données au sein du service cloud *même* en tant que vos ordinateurs virtuels WebLogic), puis la connexion est directe et ne dépend pas l’équilibrage de charge Azure et par conséquent n’est pas soumis à un délai de connexion.

-  **Multidiffusion UDP n’est pas pris en charge.** Azure prend en charge UDP multidiffuse, mais pas la multidiffusion ou diffusion. WebLogic Server peut s’appuient sur les fonctionnalités de monodiffusion UDP Azure. Pour mieux résultats dépendre monodiffusion UDP, nous vous recommandons que la taille de cluster WebLogic doivent être conservées statique, ou être conservés avec pas plus de 10 serveurs gérés inclus dans le cluster.

-  **WebLogic Server attend publiques et privées ports pour les mêmes pour l’accès T3 (par exemple, lorsque vous utilisez Enterprise JavaBeans).** Envisagez un scénario à plusieurs niveaux où une application de service de calque (EJB) est en cours d’exécution sur un cluster WebLogic Server composé de deux ou plusieurs serveurs gérés, dans un service cloud nommé **SLWLS**. Le niveau client se trouve dans un service de nuage différent, exécution d’un programme Java simple, essayez d’appeler EJB dans la couche de service. Comme il est nécessaire équilibrer la couche de service, un point de terminaison public équilibrage de charge doit être créé pour les Machines virtuelles du cluster WebLogic. Si le port privé que vous spécifiez pour ce point de terminaison est différent du port public (par exemple, 7006:7008), une erreur telle que la suivante se produit :

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    C’est parce que pour un accès à distance T3, WebLogic Server attend le port d’équilibrage de charge et le port du serveur WebLogic gérée identiques. Dans le cas ci-dessus, le client pour accéder au port 7006 (le port équilibrage de charge) et le serveur géré écoute 7008 (le port privé). Cette limitation s’applique uniquement aux T3 access, non HTTP.

    Pour éviter ce problème, utilisez une des solutions suivantes :

    -  Utiliser le même numéro de port publiques et privées pour les points de terminaison équilibrée de charge dédiés à access T3.

    -  Inclure le paramètre machine virtuelle Java suivant lorsque vous démarrez WebLogic Server :

            -Dweblogic.rjvm.enableprotocolswitch=true

Pour plus d’informations, voir de l’article KB **860340.1** en <http://support.oracle.com>.

-  **Dynamique cluster et limitations d’équilibrage.** Supposons que vous voulez utiliser un cluster dynamique dans WebLogic Server et exposer via un seul, public équilibrage point de terminaison dans Azure. Vous pouvez le faire tant que vous utilisez un numéro de port fixes pour chacun des serveurs gérés (ne sont pas automatiquement affectées à partir d’une plage) et ne commencez pas plus de serveurs gérés qu’il ne sont a machines effectue le suivi de l’administrateur (autrement dit, ne dépasse pas un serveur géré par machine virtuelle). Si votre configuration se traduit par davantage de serveurs WebLogic démarrage qu’il n’y a machines virtuelles (autrement dit, dans laquelle plusieurs instances WebLogic Server partagent la même machine virtuelle), puis il n’est pas possible pour plusieurs de ces instances de serveurs WebLogic Server pour lier à un numéro de port donné – les autres colonnes sur cet ordinateur virtuel échouera.

    En revanche, si vous configurez le serveur d’administration pour affecter automatiquement des numéros de port uniques aux serveurs qui lui sont gérées, puis l’équilibrage de charge n’est pas possible, car Azure ne prend pas en charge mappage à partir d’un seul port public à plusieurs ports privés, comme cela est nécessaire pour cette configuration.

-  **Plusieurs instances Weblogic Server sur une machine virtuelle.** En fonction des besoins de votre déploiement, vous pouvez envisager de l’option de l’exécution de plusieurs instances de WebLogic Server sur la même machine virtuelle, si la machine virtuelle est assez grande. Par exemple, sur une machine virtuelle taille moyenne, qui contient deux cœurs, vous pouvez choisir d’exécuter deux instances du WebLogic Server. Notez toutefois que nous recommandons toujours éviter d’introduire des points de défaillance uniques dans votre architecture, qui est le cas si vous avez utilisé une machine virtuelle exécutant plusieurs instances de WebLogic Server. À l’aide d’au moins deux machines virtuelles peut être une meilleure approche, et chacune de ces machines virtuelles pourrait alors exécuter plusieurs instances de WebLogic Server. Chacun de ces instances de WebLogic Server peut toujours faire partie du même cluster. Note, cependant, il est actuellement pas possible d’utiliser Azure aux points de terminaison l’équilibrage de charge qui sont exposés par ces déploiements WebLogic Server au sein de la même machine virtuelle, car équilibrage de charge Azure nécessite les serveurs d’équilibrage de charge soit réparti entre machines virtuelles uniques.

##<a name="oracle-jdk-virtual-machine-images"></a>Images des machines virtuelles JDK Oracle

-  **JDK 6 et 7 dernières mises à jour.** Tandis que nous vous recommandons d’utiliser la version plus récente publique et pris en charge de Java (actuellement Java 8), Azure, JDK 6 et 7 images disponibles. Ceci est destinée aux applications héritées qui ne sont pas encore prêtes à être mis à niveau vers JDK 8. Tandis que les mises à jour pour les images JDK précédentes ne soit plus disponibles au public, compte tenu du partenariat Microsoft pour Oracle, le JDK 6 et 7 images fournies par Azure doivent contenir une mise à jour non public plus récent normalement offerte par Oracle pour seulement un groupe de clients pris en charge d’Oracle. Nouvelles versions des images JDK sera disponibles au fil du temps avec les versions mises à jour de JDK 6 et 7.

    JDK disponible dans cet JDK 6 et 7 images et le machines virtuelles et les images dérivés de celles-ci, peut uniquement être utilisé dans Azure.

-  **JDK 64 bits.** Les images de machine virtuelle Oracle WebLogic Server et les images de machine virtuelle JDK Oracle fournies par Azure contiennent les versions 64 bits de Windows Server et JDK.

##<a name="additional-resources"></a>Ressources supplémentaires
[Images de machine virtuelle Oracle pour Azure](virtual-machines-linux-classic-oracle-images.md)
