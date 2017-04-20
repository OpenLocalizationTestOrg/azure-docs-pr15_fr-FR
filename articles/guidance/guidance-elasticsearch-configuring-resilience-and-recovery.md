<properties
   pageTitle="Configuration de résilience et récupération sur Elasticsearch sur Azure"
   description="Considérations liées à la résilience et de récupération pour Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="configuring-resilience-and-recovery-on-elasticsearch-on-azure"></a>Configuration de résilience et récupération sur Elasticsearch sur Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série](guidance-elasticsearch.md). 

La prise en charge qu’il fournit pour résilience en cas de défaillances de nœud et/ou événements partition réseau est une fonctionnalité clé de Elasticsearch. La réplication est la façon la plus évidente dans lequel vous pouvez améliorer la résilience d’un cluster, l’activation Elasticsearch pour vous assurer que plus d’une copie d’un élément de données est disponible sur des nœuds différents au cas où un nœud doit inaccessibles. Si un nœud est temporairement indisponible, autres nœuds contenant des copies de données à partir du nœud manquant peuvent prendre en charge les données manquantes jusqu'à ce que le problème est résolu. Si un problème survient d’un plus long terme, le nœud manquant peut être remplacé par une nouvelle, et Elasticsearch peut restaurer les données vers le nouveau nœud à partir des réplicas.

Ici nous synthétiser les options de résilience et de récupération disponibles avec Elasticsearch lorsque hébergé dans Azure et décrire certains aspects importants d’un cluster Elasticsearch que vous devez prendre en compte pour réduire les risques de perte de données et les heures de récupération de données étendu.

Cet article explique également quelques exemples de tests qui ont été effectuées pour afficher les effets de différents types d’échecs sur un cluster Elasticsearch et la manière dont le système répond lorsqu’elle recouvre.

Un cluster Elasticsearch utilise réplicas pour maintenir la disponibilité et améliorer les performances de lecture. Réplicas doivent être stockées sur différentes machines virtuelles à partir d’au milieu des fragments principales qui ils répliqueront. L’objectif est que si la machine virtuelle qui héberge un nœud de données échoue ou n’est plus disponible, le système peut continuer à fonctionner à l’aide d’ordinateurs virtuels en maintenant les réplicas.

## <a name="using-dedicated-master-nodes"></a>L’utilisation de nœuds maîtres dédiés

Un nœud dans un cluster Elasticsearch est choisi comme le nœud principal. L’objectif de ce nœud consiste à effectuer les opérations de gestion de cluster telles que :

- Détection des nœuds défaillants et basculer vers réplicas.

- Déplacement milieu des fragments pour établir un nœud équilibre.

- Récupération milieu des fragments lorsqu’un nœud est remis en ligne.

Vous devez envisagez d’utiliser des nœuds maîtres dédiés dans clusters critiques et vérifiez qu’il existe 3 nœuds dédiés dont le seul rôle doit être maître. Cette configuration permet de réduire la quantité de travail intensive ressource que ces nœuds ont à effectuer (ils ne stockez pas données ni gérer les requêtes) et contribue à améliorer leur stabilité. Seulement l’un de ces nœuds est être choisi, mais les autres colonnes contiendront une copie de l’état du système et que vous peuvent prendre la forme de base choisi échoue.

## <a name="controlling-high-availability-with-azure--update-domains-and-fault-domains"></a>Contrôle de disponibilité avec Azure – mise à jour les domaines et défaillance 

Machines virtuelles différents peuvent partager le même matériel. Dans un centre de données Azure, un seul rack peut héberger un certain nombre de machines virtuelles et tous ces ordinateurs virtuels partagent un commutateur de source et de réseau power courantes. Une erreur au niveau du rack peut affecter par conséquent, un certain nombre de machines virtuelles. Azure utilise le concept de domaines d’erreur pour essayer de se propager ce risque. Un domaine d’erreur correspond approximativement à un groupe de machines virtuelles qui partagent le même rack. Pour vous assurer qu’un échec au niveau du rack ne bloque pas un nœud et les nœuds contient l’ensemble de ses réplicas simultanément, vous devez vous assurer que les ordinateurs virtuels sont réparties sur les domaines d’erreur.

De même, machines virtuelles peuvent être effectués vers le bas par le [Contrôleur de tissu Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) pour effectuer des mises à niveau de système d’exploitation et de la maintenance planifiées. Azure affecte machines virtuelles pour mettre à jour des domaines. En cas d’un événement de maintenance planifiée, machines virtuelles uniquement dans un domaine unique mise à jour sont effectuées à un moment donné. Machines virtuelles dans d’autres domaines de mise à jour restent en cours d’exécution jusqu'à ce que les ordinateurs virtuels dans le domaine de mise à jour de mise à jour sont remis en ligne. Par conséquent, vous devez également vous assurer que les machines virtuelles qui héberge les nœuds et leurs répliques appartiennent à mettre à jour différents domaines lorsque cela est possible.

> [AZURE.NOTE] Pour plus d’informations sur les domaines de défaillance et mise à jour, voir [Gérer la disponibilité des machines virtuelles][].

Vous ne pouvez pas explicitement allouer une machine virtuelle à un domaine de mise à jour spécifique et un domaine d’erreur. Cette affectation est contrôlée par Azure lors de la création des machines virtuelles. Toutefois, vous pouvez spécifier que les ordinateurs virtuels doivent être créés dans le cadre d’un jeu de disponibilité. Machines virtuelles dans le même jeu de disponibilité seront réparties sur les domaines de mise à jour et de défaillance. Si vous créez manuellement des machines virtuelles, Azure crée chaque disponibilité définie avec deux domaines d’erreur et de cinq domaines de mise à jour. Machines virtuelles sont affectés à ces domaines d’erreur et mettre à jour des domaines, cycle arrondi comme davantage machines virtuelles sont mis en service, comme suit : 

| MACHINE VIRTUELLE | Domaine d’erreur | Mettre à jour domaine |
|----|--------------|---------------|
|  1 |            0 |             0 |
|  2 |            1 |             1 |
|  3 |            0 |             2 |
|  4 |            1 |             3 |
|  5 |            0 |             4 |
|  6 |            1 |             0 |
|  7 |            0 |             1 |

> [AZURE.IMPORTANT] Si vous créez des machines virtuelles à l’aide du Gestionnaire de ressources Azure, chaque jeu de disponibilité peut allouer jusqu'à 3 défaillance les domaines et 20 mise à jour. Il s’agit d’une bonne raison pour l’utilisation du Gestionnaire de ressources.

En règle générale, placez tous les ordinateurs virtuels qui remplissent les mêmes fonctions dans le même jeu de disponibilité, mais créer des jeux de disponibilité différentes pour les machines virtuelles qui exécutent des fonctions différentes. Avec Elasticsearch, que cela signifie que vous devez prendre en compte création disponibilité au moins distincte de jeux pour :

- Machines virtuelles hébergeant des nœuds de données.
- Machines virtuelles d’hébergement nœuds clients (si vous utilisez les).
- Machines virtuelles nœuds maîtres d’hébergement.

En outre, vous devez vous assurer que chaque nœud dans un cluster connaît le domaine de mise à jour et le domaine d’erreur qu'il appartient. Ces informations peuvent vous aider à vous assurer que Elasticsearch ne pas créer des milieu des fragments et leurs dans la même erreur et mettre à jour les domaines, en réduisant la possibilité d’un partagé et ses réplicas soient prises vers le bas en même temps. Vous pouvez configurer un nœud Elasticsearch afin de refléter la distribution de matériel du cluster en configurant [sensibilisation d’allocation partagé](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Par exemple, vous pouvez définir une paire d’attributs nœud personnalisé intitulé *faultDomain* et *updateDomain* dans le fichier elasticsearch.yml, comme suit :

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

Dans ce cas, les attributs sont définis en utilisant les valeurs contenues dans les * \${FAULTDOMAIN}* et * \${UPDATEDOMAIN}* variables d’environnement quand Elasticsearch est démarré. Vous devez également ajouter les entrées suivantes dans le fichier Elasticsearch.yml pour indiquer que *faultDomain* et *updateDomain* sont allocation attributs de sensibilisation et spécifient les ensembles de valeurs acceptables pour ces attributs :

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Vous pouvez utiliser sensibilisation d’allocation éclater conjointement avec le [filtrage de répartition partagé](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) pour spécifier de manière explicite les nœuds peuvent héberger milieu des fragments d’un index déterminé.

Si vous avez besoin de mettre à l’échelle au-delà du nombre de domaines d’erreur et mettre à jour des domaines dans un jeu de disponibilité, vous pouvez créer des machines virtuelles dans les jeux de davantage de disponibilité. Toutefois, vous devez comprendre que nœuds dans les jeux de disponibilité différentes peuvent être prises vers le bas pour maintenance simultanément. Essayez de vous assurer que chaque partagé et au moins une de ses réplicas sont contenus dans le même jeu de disponibilité.

> [AZURE.NOTE] Il existe actuellement une limite de 100 machines par jeu de disponibilité. Pour plus d’informations, voir [abonnement Azure et les limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md).

### <a name="backup-and-restore"></a>Sauvegarde et restauration

Utilisation de duplications ne fournit pas une protection complète de panne (par exemple, la suppression accidentelle de l’ensemble du cluster). Vous devez vous assurer que vous sauvegardez les données dans un cluster régulièrement, et que vous disposez d’une stratégie testée et pour restaurer le système à partir de ces sauvegardes.

Utilisez les Elasticsearch [instantané API et restauration](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) : élastique ne limiter ces. >> pour sauvegarder et restaurer à partir d’index. Instantanés peuvent être enregistrés dans un système de fichiers partagé. Par ailleurs, plug-ins sont disponibles qui peut écrire des instantanés dans le système de fichiers Hadoop distribué (HDFS) (le [plug-in HADOOP](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) ou au stockage Azure (le [plug-in Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Lorsque vous sélectionnez le mécanisme de stockage instantané, tenez compte des points suivants :

- Vous pouvez utiliser [le stockage de fichiers Azure](https://azure.microsoft.com/services/storage/files/) pour implémenter un système de fichiers partagé accessible à partir de tous les nœuds.

- Utilisez le plug-in HADOOP uniquement si vous exécutez Elasticsearch conjointement avec Hadoop.

- Le plug-in HADOOP, vous devez désactiver le Gestionnaire de sécurité Java exécute à l’intérieur de l’instance Elasticsearch de la machine virtuelle (machine virtuelle Java).

- Le plug-in HADOOP prend en charge un système de fichiers compatibles avec HADOOP autant que la configuration Hadoop correcte est utilisée avec Elasticsearch.

  
## <a name="handling-intermittent-connectivity-between-nodes"></a>Gestion de connectivité discontinue entre les nœuds

Problèmes de réseau intermittents, machine virtuelle redémarre après la maintenance de routine auprès du centre de données, et d’autres événements similaires peuvent entraîner des nœuds temporairement inaccessibles. Dans ce cas, où l’événement est susceptible d’être courte durée de vie, la charge de rééquilibrer au milieu des fragments apparaît deux fois dans rapide successivement (une fois lorsque l’échec est détecté et à nouveau lorsque le nœud deviennent visibles pour le masque des) peut devenir une charge significative qui affecte les performances. Vous pouvez empêcher est inaccessible nœud temporaire d’à l’origine de la forme de base rééquilibrer le cluster en définissant la *retardée\_délai d’expiration* propriété d’un index, ou pour tous les index. L’exemple ci-dessous définit le délai à 5 minutes :

```http
PUT /_all/settings
{
    "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
    }
}
```

Pour plus d’informations, voir [l’allocation retardant lorsqu’un nœud quitte](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

Dans un réseau est susceptible d’interruption, vous pouvez également modifier les paramètres de configurer une forme de base pour détecter lorsqu’un autre nœud n’est plus accessible. Ces paramètres sont partie du module [découverte simplicité](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fourni avec Elasticsearch, et vous pouvez les définir dans le fichier Elasticsearch.yml. Par exemple, le paramètre *discovery.zen.fd.ping.retries* Spécifie le nombre de fois un nœud maître tentera de commande ping sur un autre nœud dans le cluster avant de décider qui a échoué. Ce paramètre par défaut est 3, mais vous pouvez le modifier comme suit :

```yaml
discovery.zen.fd.ping_retries: 6
```

## <a name="controlling-recovery"></a>Contrôle de restauration

Lors de la connectivité à un nœud est restaurée après une défaillance, tout milieu des fragments sur ce nœud devrez être récupérés pour les mettre à jour. Par défaut, Elasticsearch restaure milieu des fragments dans l’ordre suivant :

- Par date de création d’index inverse. Index plus récentes sont restaurés avant index les plus anciens.

- Par nom de l’index inverse. Tout d’abord à partir d’index qui portent des noms qui sont dans l’ordre alphanumérique supérieures à celles d’autres personnes est restaurées.

Si certains index est plus critiques que d’autres personnes, mais ne correspondent pas à ces critères que vous pouvez ignorer l’ordre de priorité d’index en définissant la propriété *index.priority* . Index avec une valeur supérieure pour cette propriété seront récupérées avant d’index qui ont une valeur inférieure :

```http
PUT low_priority_index
{
    "settings": {
        "index.priority": 1
    }
}

PUT high_priority_index
{
    "settings": {
        "index.priority": 10
    }
}
```

Pour plus d’informations, voir [Index récupération hiérarchisation](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

Vous pouvez surveiller le processus de récupération pour un ou plusieurs index à l’aide de la * \_récupération* API :

```http
GET /high_priority_index/_recovery?pretty=true
```

Pour plus d’informations, voir [Récupération Indices](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Un cluster avec milieu des fragments nécessitant une récupération aura un état *jaune* pour indiquer que milieu pas tous des fragments sont actuellement disponibles. Lorsque tous les au milieu des fragments sont disponibles, l’état du cluster doit rétablir le *vert*. Un cluster dont l’état de *rouge* indique que milieu un ou plusieurs des fragments sont physiquement manquantes, il peut être nécessaire restaurer les données à partir d’une sauvegarde.

## <a name="preventing-split-brain"></a>Empêcher le fractionnement 

Un fractionnement peut se produire si les connexions entre les nœuds échouent. Si un nœud maître devient inaccessible à une partie du cluster, un choix a lieu dans le segment de réseau reste contactables et un autre nœud deviendra le maître. Dans un cluster configuré mal, il est possible pour chaque partie du cluster d’avoir différentes formes de base entraînant des incohérences de données ou corruption. Ce phénomène est appelé un *fractionnement*.

Vous pouvez réduire les risques d’un fractionnement en configurant la *minimale\_maître\_nœuds* propriété du module de découverte, dans le fichier elasticsearch.yml. Cette propriété spécifie le nombre de nœuds doit être disponible pour activer la sélection d’une forme de base. L’exemple suivant définit la valeur de cette propriété sur 2 :

```yaml
discovery.zen.minimum_master_nodes: 2
```

Cette valeur doit être définie pour la plupart les plus basses au nombre de nœuds qui sont en mesure de remplir le rôle de maître. Par exemple, si votre cluster comporte 3 nœuds maîtres, *minimale\_maître\_nœuds* doit être définie sur 2. Si vous avez 5 nœuds maîtres, *minimale\_maître\_nœuds* doit être définie sur 3. Dans l’idéal, vous devez disposer d’un nombre de nœuds maîtres impair.

> [AZURE.NOTE] Il est possible pour un fractionnement se produire si plusieurs nœuds maîtres dans le même cluster sont démarrés simultanément. Pendant cette occurrence rare, vous pouvez empêcher celui-ci en démarrant nœuds en série avec un court délai (5 secondes) entre chacun d’eux. 

## <a name="handling-rolling-updates"></a>Gestion des mises à jour des yeux

Si vous effectuez une mise à niveau logicielle nœuds vous-même (par exemple, migration vers une version plus récente ou l’exécution d’un correctif), vous devrez peut-être effectuer le travail sur des nœuds individuels qui requiert mettant en mode hors connexion tout en conservant le reste du cluster disponible. Dans ce cas, envisagez d’implémenter le processus suivant. 

1. Assurez-vous que réaffectation partagé est retardée suffisamment pour éviter que la forme de base choisi rééquilibrage milieu des fragments à partir d’un nœud manquant sur le reste du cluster. Par défaut, réaffectation partagé est retardée pendant une minute, mais vous pouvez augmenter la durée si un nœud est susceptible d’être indisponibles pendant une durée plus longue. L’exemple suivant augmente le délai à 5 minutes :

    ```http
    PUT /_all/_settings
    {
        "settings": {
            "index.unassigned.node_left.delayed_timeout": "5m"
        }
    }
    ```

    > [AZURE.IMPORTANT] Vous pouvez également désactiver réaffectation éclater complètement en définissant la *cluster.routing.allocation.enable* du cluster sur *Aucun*. Toutefois, évitez d’utiliser cette approche si de nouveaux index sont susceptibles d’être créé alors que le nœud est en mode hors connexion cela peut entraîner allocation d’index échec résultant dans un cluster avec l’état rouge.

2. Arrêter Elasticsearch sur le nœud à être conservées. Si Elasticsearch est en cours d’exécution en tant que service, vous pourrez peut-être arrêter le processus de manière contrôlée à l’aide d’une commande du système d’exploitation. L’exemple suivant montre comment arrêter le service Elasticsearch sur un seul nœud s’exécutant sur Ubuntu :

    ```bash
    service elasticsearch stop
    ```

    Vous pouvez également utiliser l’API arrêt directement sur le nœud :

    ```http
    POST /_cluster/nodes/_local/_shutdown
    ```

3. Effectuez les opérations de maintenance nécessaires sur le nœud

4. Redémarrez le nœud et attendez pour lui permettre de rejoindre le cluster.

5. Réactiver allocation partagé :

    ```http
    PUT /_cluster/settings
    {
        "transient": {
            "cluster.routing.allocation.enable": "all"
        }
    }
    ```

> [AZURE.NOTE] Si vous avez besoin de mettre à jour plusieurs nœuds, répétez les étapes 2&ndash;4 sur chaque nœud avant la réactivation d’allocation partagé.

Si vous le pouvez, arrêtez l’indexation de nouvelles données au cours du processus. Cela vous aidera à réduire les temps de récupération lorsque les nœuds sont remis en ligne et rejoindre le cluster.

Prise en compte pour des mises à jour automatiques à des éléments tels que la machine virtuelle Java (préférence, désactiver mises à jour automatiques pour ces éléments), en particulier lorsque vous exécutez Elasticsearch sous Windows. L’agent de mise à jour Java peut télécharger la version la plus récente de Java automatiquement, mais peut-être nécessiter Elasticsearch être redémarré pour la mise à jour prenne effet. Cela peut entraîner la perte temporaire non coordonnée des nœuds, selon la configuration de l’agent de mise à jour Java. Cela peut également entraîner dans différentes instances de Elasticsearch dans le même cluster exécutant différentes versions de la machine virtuelle Java qui peut entraîner des problèmes de compatibilité.

## <a name="testing-and-analyzing-elasticsearch-resilience-and-recovery"></a>Test et analyse Elasticsearch résilience et restauration

Cette section décrit une série de tests qui ont été effectuées pour évaluer la résilience et la restauration d’un cluster Elasticsearch qui contient trois nœuds de données et trois maître.

Les scénarios suivants ont été testés : 

- Échec du nœud et redémarrez avec aucune perte de données. Un nœud de données est arrêté et redémarré après 5 minutes. Elasticsearch a été configuré pour ne pas pour réaffecter milieu des fragments manquants dans cet intervalle, afin qu’aucune e/s supplémentaires n’est prévu en déplacement milieu des fragments. Lorsque le nœud redémarre, le processus de récupération affiche au milieu des fragments sur ce nœud à jour.

- Échec de nœud sans perte de données grave. Un nœud de données est arrêté et les données qu’il contient sont permanente pour simuler défaillance grave du disque. Le nœud a puis redémarré (après 5 minutes), efficacement agissant en tant que texte de remplacement souhaité pour le nœud d’origine. Le processus de récupération nécessite une reconstruction les données manquantes pour ce nœud et peut impliquer déplacement milieu des fragments conservées sur d’autres nœuds.

- Échec du nœud et redémarrez avec aucune perte de données, mais avec réaffectation partagé. Un nœud de données est arrêté et au milieu des fragments qu’il contient sont réaffectées à d’autres nœuds. Le nœud est puis redémarré et réaffectation plus se produit pour rééquilibrer le cluster.

- Mises à jour des yeux. Chaque nœud du cluster est arrêté puis redémarré après un court intervalle pour simuler machines redémarrés après une mise à jour logicielle. Un seul nœud est arrêté à un moment donné. Milieu des fragments ne sont pas réaffectées pendant un nœud vers le bas.

Chaque scénario a été soumis à la même charge de travail, y compris un mélange de tâches de réception de données, les agrégations et filtrer des requêtes tandis que les nœuds ont été prises en mode hors connexion et récupéré. La plus grande partie des opérations d’insertion dans la charge de travail chacune 1000 documents enregistrés et ont été effectuées par rapport à un seul index pendant les agrégations et requêtes de filtre utilisé un index distinct contenant des millions de plusieurs documents. Il s’agissait pour activer les performances des requêtes à être évalués séparément depuis les insertions en bloc. Chaque index contenu milieu des cinq fragments et une réplique.

Les sections suivantes synthétisent les résultats des tests, noter toute dégradation des performances pendant qu’un nœud est en mode hors connexion ou en cours de restauration, ainsi que les erreurs qui ont été signalés. Les résultats sont présentés sous forme de graphique, mise en surbrillance les points à laquelle un ou plusieurs nœuds sont manquants et estimer le temps nécessaire pour que le système entièrement récupérer et obtenir un niveau de performance qui était présente avant les nœuds hors semblable.

> [AZURE.NOTE] La maîtrise de test permettant d’effectuer ces tests est disponibles en ligne. Vous pouvez adapter et utiliser ces ateliers pour vérifier la résilience et la restauration de vos propres configurations cluster. Pour plus d’informations, voir [l’exécution des tests résilience Elasticsearch automatisés][].

## <a name="node-failure-and-restart-with-no-data-loss-results"></a>Échec du nœud et redémarrer avec aucune perte de données : résultats

<!-- TODO; reformat this pdf for display inline --> 

Les résultats de ce test s’affichent dans le fichier [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario1.pdf). Les graphiques indiquent le profil de performance de la charge et des ressources physiques pour chaque nœud du cluster. La partie initiale des graphiques, afficher le système qui exécute normalement pendant environ 20 minutes, après quoi nœud 0 est arrêté 5 minutes avant le redémarrage. Les statistiques d’une autre minutes 20 sont illustrées ; le système vous permet d’accéder à environ 10 minutes pour récupérer et placez son. Ceci est illustré par le taux de transaction et le temps de réponse pour les charges de travail différentes.

Notez les points suivants :

- Pendant le test, aucune erreur ont été signalés. Aucune donnée n’a été interrompue, et toutes les opérations effectuées avec succès.

- Les taux de transaction pour les trois types d’opération (insertion en bloc, requête d’agrégation et requête filtrée) supprimées et les heures de réponse moyen augmenté tandis que le nœud 0 a été en mode hors connexion.

- Opérations restaurées progressivement pendant la période de récupération, le taux de transaction et le temps de réponse pour la requête d’agrégation et une requête de filtre. Les performances d’insertion en bloc récupéré pour un court tandis qu’avant diminue. Toutefois, cela est probablement en raison du volume de données à l’origine de l’index utilisé par l’insertion en bloc pour agrandir et les taux de transaction pour cette opération peuvent être vu pour ralentir la même avant de nœud 0 est mis en mode hors connexion.

- Le graphique d’utilisation du processeur pour nœud 0 indique activité réduite pendant la phase de récupération, c’est en raison du disque accru et activités réseau générées par le mécanisme de récupération, le nœud a réaligner toutes les données qu’il a manqué bien qu’il soit en mode hors connexion et mettre à jour d’au milieu des fragments qu’il contient.

- Au milieu des fragments des index ne sont pas exactement distribuées également sur tous les nœuds. Il existe deux index contenant 5 milieu des fragments et 1 réplica chaque, pour un total de 20 milieu des fragments. Deux nœuds contiendra donc 6 milieu des fragments alors que les deux autres détiennent 7 chaque. Ceci est évident dans les graphiques de l’utilisation du processeur pendant la période initiale de 20 minutes, nœud 0 est moins occupé que les deux autres. Une fois la récupération terminée, certaines passer semble se produisent lorsque le nœud 2 apparaît devienne le nœud plus légèrement chargé.

    
## <a name="node-failure-with-catastrophic-data-loss-results"></a>Échec du nœud sans perte de données grave : résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test sont représentées dans le fichier [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario2.pdf). Comme avec le premier test, la partie initiale des graphiques montre le système qui exécute normalement pendant environ 20 minutes, à quel point nœud 0 est arrêté pendant 5 minutes. Pendant cette période, les données Elasticsearch sur ce nœud sont supprimées, simulation de perte de données grave avant le redémarrage. Récupération complète apparaît et 12 à 15 minutes avant que les niveaux de performances vu avant le test sont restaurées.

Notez les points suivants :

- Pendant le test, aucune erreur ont été signalés. Aucune donnée n’a été interrompue, et toutes les opérations effectuées avec succès.

- Les taux de transaction pour les trois types d’opération (insertion en bloc, requête d’agrégation et requête filtrée) supprimées et les heures de réponse moyen augmenté tandis que le nœud 0 a été en mode hors connexion. À ce stade, le profil de performances du test est similaire au premier scénario. Ce n’est pas étonnant en tant que, à ce stade, les scénarios sont identiques.

- Pendant la période de récupération, le taux de transaction et temps de réponse ont été restaurés, bien que pendant ce temps est survenu volatilité beaucoup plus dans les chiffres. Cela est probablement le travail supplémentaire que les nœuds du cluster effectuez, fournir les données pour restaurer au milieu des fragments manquants. Ce travail supplémentaire est évident dans l’utilisation du processeur, l’activité du disque et des graphiques d’activité réseau.

- Le graphique d’utilisation du processeur pour les nœuds 0 et 1 indique activité réduite pendant la phase de récupération, qu'il s’agit en raison de la disque accrue et activités réseau générées par le processus de récupération. Dans le premier scénario, seulement survenir avec le nœud en cours de restauration, mais dans ce scénario il est probable que la plupart des données manquantes pour nœud 0 est en cours de restauration du nœud 1.

- L’activité e/s pour nœud 0 est en réalité réduite par rapport au premier scénario. Cela peut être dû à l’efficacité e/s simplement copier les données pour un ensemble partagé plutôt que la série de plus petite taille demandes e/s requises pour mettre un éclater existante à jour.

- L’activité du réseau pour tous les trois nœuds indiquent Records d’activité transmises et la réception entre des nœuds de données. Dans le scénario 1, seul le nœud de 0 présenté comme activité réseau, mais cette activité semble être maintenu pendant une longue période. Là encore, cette différence peut être dû à de l’efficacité de la transmission des données pour un éclater entiers comme une seule demande au lieu de la série de requêtes plus petits reçues lorsque vous restaurez un partagé.

## <a name="node-failure-and-restart-with-shard-reallocation-results"></a>Échec du nœud et redémarrer avec réaffectation éclater : résultats

<!-- TODO; reformat this pdf for display inline -->

Le fichier [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario3.pdf) montre les résultats de ce test. Comme avec le premier test, la partie initiale des graphiques montrent le système qui exécute normalement pendant environ 20 minutes, à quel point nœud 0 est arrêté pendant 5 minutes. À ce stade, le cluster Elasticsearch tente de recréer au milieu des fragments manquants et rééquilibrer au milieu des fragments sur les nœuds restants. Après le nœud de 5 minutes, 0 est remis en ligne, puis de nouveau le cluster possède rééquilibrer au milieu des fragments. Performances sont restauré après 12 à 15 minutes.

Notez les points suivants :

- Pendant le test, aucune erreur ont été signalés. Aucune donnée n’a été interrompue, et toutes les opérations effectuées avec succès.

- Les taux de transaction pour les trois types d’opération (insertion en bloc, requête d’agrégation et requête filtrée) supprimé et les temps de réponse moyens augmenté de manière significative lors de nœud 0 en mode hors connexion par rapport aux deux tests précédente. Il s’agit en raison de l’activité du cluster accrue recréer au milieu des fragments manquants et rééquilibrer le cluster comme le montre clairement les chiffres de relief d’activité disque et de réseau pour les nœuds 1 et 2 pour cette période.

- Pendant la période après que nœud 0 est remis en ligne, le taux de transaction et de réactivité reste volatiles.

- Les UC l’utilisation du disque activité graphiques et de nœud 0 indique très réduites initiale action pendant la phase de récupération. C’est parce qu’à ce stade, nœud 0 ne traite pas de toutes les données. Après une période d’environ 5 minutes, le nœud se termine en action < RBC : il m’a fait snort à haute voix. Je ne suis pas venir avec une meilleure autrement dit bien.  >> telle qu’indiquée par l’augmentation de lancer une activité de l’UC, disque et le réseau. Il est probablement par le cluster redistribuer le milieu des fragments sur les nœuds. Le nœud 0 indique puis activité normale.
  
## <a name="rolling-updates-results"></a>Mises à jour des yeux : résultats

<!-- TODO; reformat this pdf for display inline -->

Les résultats de ce test, dans le fichier [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario4.pdf), montrent comment chaque nœud est mis hors connexion, puis restaurée à nouveau à la suite. Chaque nœud est arrêté 5 minutes avant le redémarrage à partir de laquelle le nœud suivant dans l’ordre est arrêté.

Notez les points suivants :

- Bien que chaque nœud changent, les performances en termes de débit et réactivité restent raisonnablement même.

- Activité du disque augmente pour chaque nœud quelques instants comme il est remis en ligne. Il est très probablement le processus de récupération reprise toutes les modifications qui se sont produites alors que le nœud était vers le bas.

- Lorsqu’un nœud est mis en mode hors connexion, pointes de l’activité du réseau se produisent dans les nœuds restants. Pointes également se produisent lors du redémarrage de nœud.

- Une fois le nœud final suppression initiale, le système entre une période de volatilité significative. Cela est probablement causé par le processus de récupération avoir à synchroniser les modifications entre chaque nœud et assurez-vous que tous les doubles et leurs milieu des fragments correspondantes sont cohérentes. À un moment donné, cette consécutifs causes d’effort insérez opérations au délai d’attente et échouer. Les erreurs signalées chaque cas ont été :

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Tests suivants a montré qu’introduire un délai de quelques minutes entre cycle chaque nœud d'éliminer cette erreur, afin qu’il est probablement dû au fait conflits entre le processus de récupération tentative de restauration simultanée de plusieurs nœuds et la plus grande partie des opérations tentative de stockage des milliers de nouveaux documents d’insertion.


## <a name="summary"></a>Résumé

Les tests effectués indiqué que :

- Elasticsearch a été extrêmement résistant aux modes les plus courants de panne susceptible de se produire dans un cluster.

- Elasticsearch pouvez récupérer rapidement si un cluster bien conçu est soumis à perte de données grave sur un nœud. Cela peut arriver si vous configurez Elasticsearch pour enregistrer les données dans un stockage éphémère et le nœud est configuré par la suite après un redémarrage. Ces résultats montrent que même dans ce cas, les risques liés à l’utilisation du stockage éphémère sont probables compensé par les avantages de performances qui fournit ce cours de stockage.

- Dans les trois premières scénarios, aucune erreur ne lors dans insertion en bloc simultanées, l’agrégation et filtre les charges de travail requête un nœud a été mis en mode hors connexion et récupéré.

- Uniquement le dernier scénario indiqué perte de données potentielle, et cette perte affectées uniquement Ajout de nouvelles données. Il est recommandé dans les applications effectuant réception de données à limiter cette probabilité en effectuant les opérations d’insertion qui n’ont pas que le type d’erreur rapportée est très probablement transitoires à nouveau.

- Les résultats du dernier test montrent également que si vous effectuez la maintenance planifiée des nœuds dans un cluster, performances lui facilitera si vous autorisez plusieurs minutes entre cycle un nœud et le suivant. Dans une situation planifiée (par exemple, le centre de données recyclage nœuds après avoir effectué une mise à jour du système d’exploitation), vous avez moins de contrôle sur quand et comment les nœuds sont redirigés vers le bas et redémarrés. Le conflit se produit lorsque Elasticsearch tente de récupérer l’état du cluster cas de panne nœud séquentiel peut entraîner des délais d’expiration et des erreurs. 

[Gérer la disponibilité des Machines virtuelles]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[Exécution des Tests de résilience Elasticsearch automatisé]: guidance-elasticsearch-running-automated-resilience-tests.md
