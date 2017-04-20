<properties
   pageTitle="Planification de la capacité de cluster Service TISSU | Microsoft Azure"
   description="Service cluster capacité considérations à la planification. Niveaux NodeTypes, résistance et fiabilité"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considérations à la planification de la capacité de cluster tissu de service

Pour tout déploiement de production, la planification de la capacité est un pas important. Voici quelques éléments à prendre en compte dans le cadre de ce processus.

- Le nombre de types de nœud avec que votre cluster doit commencer
- Les propriétés de chaque type de nœud (taille, principal, internet en nombre de machines virtuelles, etc.).
- Les caractéristiques de cluster la fiabilité et la longévité

Nous allons vous présenter brièvement chacun de ces éléments.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Le nombre de types de nœud avec que votre cluster doit commencer

Tout d’abord, vous devez savoir ce qui se passe le cluster que vous créez à utiliser pour et quels types d’applications que vous envisagez de déployer dans ce groupe. Si vous n’êtes pas effacer l’objectif du cluster, vous probablement ne sont pas encore prêt à entrer la processus de planification de la capacité.

Définir le nombre de types de nœud avec que votre cluster doit commencer.  Chaque type de nœud est mappé sur une échelle de Machine virtuelle définie. Chaque type de nœud peut ensuite être mise à l’échelle vers le haut ou vers le bas séparément, présentent différents jeux de ports ouverts et peut contenir des indicateurs de capacité différente. Si la décision du nombre de types de nœuds pour l’essentiel se résume aux considérations suivantes :

- Votre application a-t-il plusieurs services, et une d’elles doivent être public ou permettant l’accès internet ? Parmi les applications contiennent un service passerelle frontale qui reçoit l’entrée d’un client et un ou plusieurs services principaux de communiquer avec les services frontaux. Donc dans ce cas, vous obtenez ainsi au moins deux types de nœuds.

- Vos services (qui composent votre application) dois-je en matière d’infrastructure différentes comme RAM supérieur ou supérieurs cycles processeur ? Par exemple, supposons que l’application que vous voulez déployer contient un service frontal et un service de données principale. Le service frontal peut s’exécuter sur machines virtuelles inférieures (tailles machine virtuelle comme D2) qui ont des ports ouvertes à internet.  Le service de données principale, cependant, est accrue de calcul et doit s’exécuter sur machines virtuelles supérieures (dont la taille de la mémoire virtuelle comme D4, D6, D15) qui ne sont pas internet face.

 Dans cet exemple, bien que vous pouvez décider de placer tous les services sur le type d’un nœud, il est recommandé de placer les dans un cluster avec deux types de nœuds.  Ainsi, pour chaque type de nœud d’avoir distinctes propriétés telles que la connectivité internet ou taille de mémoire virtuelle. Le nombre de machines virtuelles peut être mise à l’échelle indépendamment, ainsi que.  

- Dans la mesure où vous ne pouvez pas prédire l’avenir, accédez à l’aide des éléments que vous connaissez et décider du nombre de types de nœuds vos applications ont besoin pour commencer avec. Vous pouvez toujours ajouter ou supprimer des types de nœuds ultérieurement. Un cluster de tissu de Service doit avoir au moins un nœud type.

## <a name="the-properties-of-each-node-type"></a>Les propriétés de chaque type de nœud

Le **type de nœud** peut être considéré comme équivalent aux rôles dans les Services en nuage. Types de nœud définissent les tailles de mémoire virtuelle, le nombre de machines virtuelles et leurs propriétés. Chaque type de nœud défini dans un cluster de tissu de Service a été configurée en tant que jeu d’une échelle Machine virtuelle distincte. Machine virtuelle échelle jeux sont un Azure calculer des ressources, vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles comme un ensemble. Étant définie comme distinct machine virtuelle échelle définit, chaque type de nœud peut ensuite être mise à l’échelle vers le haut ou vers le bas séparément, présentent différents jeux de ports ouverts et peut contenir des indicateurs de capacité différente.

Votre cluster peut avoir plusieurs types de nœud, mais le type de nœud principal (première celle que vous définissez sur le portail) doit avoir au moins cinq machines virtuelles pour clusters utilisés pour les charges de travail (ou au moins trois machines virtuelles pour les clusters de test). Si vous créez le cluster à l’aide d’un modèle de gestionnaire de ressources, vous trouverez un attribut **est principal** sous la définition de type de nœud. Le type de nœud principal est le type de nœud où sont trouvent les services de système de structure de Service.  

### <a name="primary-node-type"></a>Type de nœud principal
Pour un cluster avec plusieurs types de nœud, vous devrez choisir un d’eux pour être principal. Voici les caractéristiques d’un type de nœud principal :

- La taille minimale de machines virtuelles pour le type de nœud principal est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut pour la couche durabilité est Bronze. Faites défiler vers le bas pour plus d’informations sur la couche durabilité et les valeurs que peut prendre.  

- Le nombre minimal de machines virtuelles pour le type de nœud principal est déterminé par le niveau de fiabilité que vous choisissez. La valeur par défaut pour le niveau de fiabilité est argent. Faites défiler vers le bas pour plus d’informations sur le niveau de la fiabilité et les valeurs que peut prendre.

- Les services de système tissu de Service (par exemple, le service du Gestionnaire de Cluster ou service banque d’Image) sont placées sur le type de nœud principal et donc la fiabilité et la longévité du cluster est déterminé par la valeur de couche de la fiabilité et la valeur de niveau durabilité vous sélectionnez pour le type de nœud principal.

![Capture d’écran d’un cluster qui comporte deux Types de nœuds ][SystemServices]


### <a name="non-primary-node-type"></a>Type de nœud secondaire
Pour un cluster avec plusieurs types de nœud, il existe un type de nœud principal et le reste d'entre elles sont secondaires. Voici les caractéristiques d’un type de nœud principal :

- La taille minimale de machines virtuelles pour ce type de nœud est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut pour la couche durabilité est Bronze. Faites défiler vers le bas pour plus d’informations sur la couche durabilité et les valeurs que peut prendre.  

- Le nombre minimal de machines virtuelles pour ce type de nœud peut s’agir. Toutefois, vous devez installer ce numéro en fonction du nombre de copies de l’application/services que vous voulez exécuter dans ce type de nœud. Le nombre de machines virtuelles dans un type de nœud peut être augmenté après avoir déployé le cluster.


## <a name="the-durability-characteristics-of-the-cluster"></a>Les caractéristiques de durabilité du cluster

La couche durabilité est utilisée pour indiquer au système les privilèges vos ordinateurs virtuels dotées de l’infrastructure Azure sous-jacente. Dans le type de nœud principal, ce privilège permet de tissu de Service en pause toute demande de niveau infrastructure machine virtuelle (par exemple, un redémarrage machine virtuelle, créer une nouvelle image machine virtuelle ou de migration de machine virtuelle) ayant un impact sur la configuration requise quorum pour les services système et votre état. Dans les types de nœud principal, ce privilège permet de tissu de Service en pause toute demande de niveau infrastructure machine virtuelle, tels que redémarrage machine virtuelle, créer une nouvelle image machine virtuelle, migration machine virtuelle etc. qui ont une incidence sur la configuration requise quorum pour vos services avec état en cours d’exécution qu’il contient.

Ce privilège est exprimé en les valeurs suivantes :

- Or - les travaux d’infrastructure peut être suspendu pendant une durée de 2 heures par UD

- Argent - les travaux d’infrastructure peut être suspendu pendant une durée de 30 minutes par UD (cela n’est actuellement pas activée pour une utilisation. Une fois activée cela sera disponible sur tous les ordinateurs virtuels standards de cœur unique ou plus).

- Bronze - aucun privilège. Il s’agit de la valeur par défaut.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Les caractéristiques de fiabilité du cluster

Le niveau de fiabilité est utilisé pour définir le nombre de copies des services système que vous voulez exécuter sur le type de nœud principal de ce groupe. Plus le nombre de répliques, le plus fiable les services système figurent dans votre cluster.  

Le niveau de fiabilité peut prendre les valeurs suivantes.

- PLATINE - exécuter les services système avec une cible réplica définie le nombre de 9

- Or - exécuter les services système avec une cible réplica définie le nombre de 7

- Argent - exécuter les services système avec une cible réplica définie le nombre de 5

- Bronze - exécuter les services système avec une cible réplica définie le nombre de 3

>[AZURE.NOTE] Le niveau de fiabilité que vous choisissez détermine le nombre minimal de nœuds que votre type de nœud principal doit avoir. Le niveau de fiabilité n’a pas d’incidence sur la taille maximale du cluster. Vous pouvez faire un cluster 20 nœud, qui s’exécute en fiabilité Bronze.

 Vous pouvez mettre à jour la fiabilité de votre cluster d’un niveau à l’autre. Cela se déclenche les mises à niveau cluster nécessaires pour modifier le réplica de services système définir le nombre. Attendez que la mise à niveau en cours pour terminer avant d’apporter d’autres modifications au cluster, telles que l’ajout de nœuds etc..  Vous pouvez suivre la progression de la mise à niveau dans l’Explorateur de tissu de Service ou en exécutant [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé votre planification de la capacité et configurer un cluster, lisez ce qui suit :
- [Sécurité du cluster tissu de service](service-fabric-cluster-security.md)
- [Présentation des modèle tissu de service d’intégrité](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
