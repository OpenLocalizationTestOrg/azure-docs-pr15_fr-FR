<properties
   pageTitle="Responsable de ressources Cluster Service TISSU - groupes d’Application | Microsoft Azure"
   description="Vue d’ensemble des fonctionnalités du groupe d’applications dans le Gestionnaire de ressources Cluster Service TISSU"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introduction-to-application-groups"></a>Présentation des groupes d’applications
En règle générale, le Gestionnaire de ressources du service TISSU Cluster gère les ressources de cluster en étalant la charge (représentée par le biais métriques) uniformément tout au long du cluster. Service TISSU gère également la capacité des nœuds dans le cluster et le cluster dans son ensemble via la notion de capacité. Ceci est idéal pour un grand nombre de différents types de charges de travail, mais les modèles qui en font une utilisation intensive autre tissu Application d’Instances de Service parfois avancer configuration requise supplémentaire. Quelques conditions supplémentaires sont généralement :

- Possibilité de réserver de la capacité pour les services d’une Instance de l’Application sur un certain nombre de nœuds
- Possibilité de limiter le nombre total de nœuds un ensemble de services au sein d’une application est autorisé à s’exécuter sur
- Définition des capacités sur l’instance de l’application afin de limiter le nombre ou la consommation des ressources des services qu’elle contient

Pour répondre à ces exigences, nous avons développé prise en charge de ce que nous appelons groupes d’applications.

## <a name="managing-application-capacity"></a>Gestion de la capacité de l’Application
Capacité de l’application peut servir à limiter le nombre de nœuds fractionnés par une application, ainsi que la charge totale métrique de ces instances d’applications sur des nœuds individuels. Il peut également être utilisé pour réserver des ressources dans le cluster pour l’application.

Capacité de l’application peut être définie pour les nouvelles applications lorsqu’ils sont créés ; Il peut également mis à jour pour les applications existantes qui ont été créées sans spécification de capacité de l’Application.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limiter le nombre maximal de nœuds
Le cas d’utilisation la plus simple pour la capacité de l’Application est lorsqu’une instanciation d’application doit être limité à un certain nombre maximal de nœuds. Si aucune capacité Application n’est spécifiée, le Gestionnaire de ressources de Service TISSU Cluster sera instanciation réplicas conforme aux règles normales (équilibrage de charge ou défragmentation), ce qui signifie généralement que ses services seront réparties sur tous les nœuds disponibles dans le cluster, ou si certaines arbitraire, mais plus petit nombre de nœuds défragmentation est activée.

L’image suivante montre la position d’une instance d’application potentielle sans le nombre maximal de nœuds définis et puis même application avec un nombre maximal de nœuds définie. Notez qu’il n’existe aucune garantie concernant les réplicas ou les instances dont services seront obtenir placés entre eux.

![Définir le nombre maximal de nœuds Instance de l’application][Image1]

Dans l’exemple de gauche, l’application n’a pas la capacité Application définir et qu’il possède trois services. CRM a décidé logique d’aérer tous les réplicas sur six nœuds disponibles afin d’optimiser l’équilibre dans le cluster. Dans l’exemple de droite, nous voyons la même application qui est limité à trois nœuds et où Service TISSU CRM a atteint l’équilibre optimal pour les réplicas de services de l’application.

Le paramètre qui détermine ce comportement est appelé MaximumNodes. Ce paramètre peut être défini lors de la création de l’application, ou mis à jour pour une instance d’application qui a été déjà en cours d’exécution, dans lequel cas Service TISSU CRM va limiter les répliques des services de l’application pour le nombre maximal défini des nœuds.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Capacité de chargement et des mesures de l’application
Groupes d’application permettent également vous permet de définir des mesures associées à une instance d’application donné, ainsi que la capacité de l’application en ce qui concerne les indicateurs. Par exemple vous pouvez définir que nombreux services que vous le souhaitez pourraient être créés dans

Pour chaque mesure, il existe 2 valeurs pouvant être définies pour décrire la capacité de cette instance d’application :

-   Nombre total de Application capacité – représente la capacité totale d’une application à une mesure particulière. Service TISSU CRM essaiera limiter la somme de charges métriques de services de cette application à la valeur spécifiée ; en outre, si les services de l’application utilisent déjà charge jusqu'à cette limite, Gestionnaire de ressources de Service TISSU Cluster permet de désactiver la création de nouveaux services ou partitions qui entraînent la charge totale passer en revue cette limite.
-   Capacité maximale du nœud – indique la charge totale maximale pour copies de services d’applications sur un seul nœud. Si la charge totale sur le nœud dépasse cette capacité, Service TISSU CRM tentera de déplacer réplicas vers d’autres nœuds afin que la contrainte de capacité est respectée.

## <a name="reserving-capacity"></a>Réservation de capacité
Une autre utilisation courante pour les groupes d’application consiste à vous assurer que les ressources au sein du cluster sont réservées pour une instance de l’application donnée, même si l’instance d’application ne dispose pas encore des services qu’il contient, ou même si elles ne sont pas encore consomme les ressources. Examinons comment qui se déroule.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Spécifier un nombre minimal de nœuds et réservation de ressources
Réservation des ressources pour une instance d’application requiert spécifiant des paramètres supplémentaires quelques : *MinimumNodes* et *NodeReservationCapacity*

- MinimumNodes - comme spécifiant un nombre maximal de cible des nœuds les services au sein d’une application peuvent s’exécuter sur, vous pouvez également spécifier le nombre minimal de nœuds une application doit s’exécuter sur. Ce paramètre définit efficacement le nombre de nœuds les ressources doivent être réservés au moins, garantissant capacité au sein du cluster dans le cadre de la création de l’instance d’application.
- NodeReservationCapacity - la NodeReservationCapacity peut être défini pour chaque métrique dans l’application. Définit la quantité de charge métrique réservé pour l’application sur n’importe quel nœud où sont trouvent aucun réplica ou instances des services qu’il contient.

Examinons un exemple de réservation de capacité :

![Instances de l’application définition capacité réservée][Image2]

Dans l’exemple de gauche, les applications n’ont pas de n’importe quel capacité Application définie. Gestionnaire de ressources de service TISSU Cluster répartit réplicas services enfant de l’application et les instances ainsi que celles d’autres services (en dehors de l’application) pour vous assurer que solde dans le cluster.

Dans l’exemple sur la droite, supposons que l’application a été créée avec une MinimumNodes 2, MaximumNodes la valeur 3 et une application métrique défini avec une réservation de capacité max 20, sur un nœud de 50 % et la capacité totale d’application de 100, la valeur Service TISSU réserve de la capacité sur deux nœuds pour l’application bleue et ne permet pas d’autres réplicas dans le cluster consommer que la capacité. Cette application réservés capacité sera considérée consommées et comptés par rapport à la capacité de cluster restante.

Lorsqu’une application est créée avec réservation, le Gestionnaire de ressources Cluster réserve de la capacité égale à MinimumNodes * NodeReservationCapacity dans le cluster, mais pas réserve de la capacité sur des nœuds spécifiques jusqu'à ce que les répliques des services de l’application sont créés et placés. Cela permet de souplesse, étant donné que les nœuds sont choisis pour les nouveaux réplicas uniquement lorsqu’elles sont créées. La capacité est réservée sur un nœud spécifique lorsqu’au moins un réplica est placé dessus.

## <a name="obtaining-the-application-load-information"></a>Obtention d’informations sur le chargement de l’application
Pour chaque application que Application capacité définie vous pouvez obtenir les informations relatives à la charge totale signalée par copies de ses services. Service TISSU fournit des requêtes PowerShell et de l’API managée à cet effet.

Par exemple, chargement peut être récupéré à l’aide de l’applet de commande PowerShell suivante :

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

Le résultat de cette requête contient les informations de base sur la capacité d’Application qui a été spécifié pour l’application, telles que les nœuds Minimum et Maximum. Il sera également des informations sur le nombre de nœuds que l’application utilise actuellement. Par conséquent, pour chaque métrique charge il sera informations sur :
- : Métrique nom de la métrique.
-   Capacité réservation : La capacité de Cluster réservée dans le cluster pour cette Application.
-   Chargement de l’application : Charge totale de réplicas enfant de cette Application.
-   Capacité de l’application : Maximale autorisée valeur charge de l’Application.

## <a name="removing-application-capacity"></a>Suppression des capacités des applications
Une fois que les paramètres de capacité d’Application sont définis pour une application, ils peuvent être supprimés à l’aide des applets de commande PowerShell ou des interfaces API Application de mise à jour. Par exemple :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Cette commande supprime tous les paramètres de capacité de l’Application à partir de l’application, puis Gestionnaire de ressources de Service TISSU Cluster démarre traitant de cette application comme toute autre application dans le cluster qui n’a pas de ces paramètres définis. L’effet de la commande est immédiat et le Gestionnaire de ressources Cluster va supprimer tous les paramètres de capacité de l’Application de cette application ; les spécifier à nouveau de verser API d’Application de mise à jour être appelé avec les paramètres appropriés.

## <a name="restrictions-on-application-capacity"></a>Restrictions sur les capacités des applications
Il existe plusieurs restrictions sur les paramètres de capacité d’Application qui doivent être respectées. En cas d’erreurs de validation, la création ou mise à jour de l’application est rejetée avec une erreur.
Tous les paramètres d’entier doivent être des nombres positifs.
En outre, pour chaque paramètre de restrictions sont les suivantes :

-   MinimumNodes ne doivent jamais être supérieur à MaximumNodes.
-   Si des capacités pour une mesure de charge sont définies, ils doivent suivez ces règles :
  - Nœud réservation capacité ne doit pas être supérieure à la capacité maximale du nœud. Par exemple, vous ne pouvez pas limiter la capacité de métrique « Processeur » sur le nœud à 2 unités et essayez réserver 3 unités sur chaque nœud.
  - Si MaximumNodes est spécifié, le produit de MaximumNodes et capacité maximale du nœud ne doit pas être supérieur à la capacité totale Application. Par exemple, si vous définissez la capacité maximale nœud pour charger métrique « Processeur » à 8 et que vous définissez les nœuds au Maximum sur 10, capacité Application Total doit être supérieure à 80 pour cette métrique charge.

Les restrictions sont appliquées à la fois lors de la création d’application (sur le côté client) et pendant la mise à jour de l’application (sur le côté serveur). Lors de la création, il s’agit d’un exemple d’une violation claire de la configuration requise depuis MaximumNodes < MinimumNodes et la commande échouera dans le client avant que la demande est envoyée même cluster tissu de Service :

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Voici un exemple de mise à jour non valide. Si nous prendre une application existante et mettre à jour des nœuds au maximum à une valeur, la mise à jour passera :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Après cela, nous pouvons tenter de mettre à jour des nœuds minimales :

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

Le client n’a pas assez de contexte relatives à l’application afin que cela permet la mise à jour passer au cluster tissu de Service. Toutefois, dans le cluster, Service TISSU valide le nouveau paramètre ainsi que les paramètres existants et échouera l’opération de mise à jour, car les nœuds de minimale ennemi valeur est supérieure à la valeur de nœuds au maximum. Dans ce cas, les paramètres de capacité de l’Application reste inchangées.

Ces restrictions sont mises en place pour le Gestionnaire de ressources Cluster puisse être en mesure de fournir le meilleur placement pour réplicas de services d’applications.

## <a name="how-not-to-use-application-capacity"></a>Comment ne pas utiliser les capacités des applications

-   N’utilisez pas la capacité d’Application pour limiter l’application à un sous-ensemble spécifique des nœuds : bien que tissu de Service permet de garantir que les nœuds au Maximum soit respecté pour chaque application ayant capacité Application spécifiée, les utilisateurs ne peut pas déterminer les nœuds il sera instanciation sur. Ceci peut être réalisé à l’aide des contraintes de placement pour les services.
-   N’utilisez pas la capacité d’Application pour vous assurer que deux services à partir de la même application sont toujours placés en parallèle avec eux. Pour ce faire à l’aide de l’AFFINITE entre les services et affinité peut être limitée uniquement pour les services qui en fait doivent être placées entre eux.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations Cluster le Gestionnaire de ressources disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)
- Pour savoir plus sur comment le Gestionnaire de ressources Cluster gère les soldes et chargement du cluster, consultez l’article sur [l’équilibrage de charge](service-fabric-cluster-resource-manager-balancing.md)
- Démarrer à partir du début et [obtenir une Introduction au Gestionnaire de ressources Cluster Service TISSU](service-fabric-cluster-resource-manager-introduction.md)
- Pour plus d’informations sur la façon dont les mesures en règle générale, poursuivez votre lecture [Performances de chargement des services TISSU](service-fabric-cluster-resource-manager-metrics.md)
- Le Gestionnaire de ressources Cluster comporte beaucoup d’options pour décrire le cluster. Pour en savoir plus sur les consultez cet article sur [décrivant un cluster tissu de Service](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
