<properties
    pageTitle="Échelle de calculer automatiquement des nœuds dans un pool lot Azure | Microsoft Azure"
    description="Activer le dimensionnement automatique sur un pool de cloud pour ajuster dynamiquement le nombre de nœuds de calcul dans le pool."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Ajuster automatiquement des nœuds de calcul dans un pool lot Azure

Avec échelle automatique, le service Azure lot peut dynamiquement ajouter ou supprimer les nœuds de calcul dans un pool en fonction des paramètres que vous définissez. Vous pouvez éventuellement gagner du temps et argent en ajustant automatiquement la quantité de puissance informatique utilisée par votre application : ajouter des nœuds comme augmentation de demandes de tâche de votre travail, puis supprimez-les lorsqu’ils réduire.

Activer le dimensionnement automatique sur un pool de nœuds de calcul en associant une *formule échelle automatique* que vous définissez, avec la [PoolOperations.EnableAutoScale] [ net_enableautoscale] méthode dans la bibliothèque [.NET lot](batch-dotnet-get-started.md) . Le service lot utilise ensuite cette formule pour déterminer le nombre de nœuds de calcul sont nécessaires pour exécuter votre charge de travail. Lot répond aux métriques de service des exemples de données qui sont collectées à intervalles réguliers et ajuste le nombre de nœuds de calcul dans le pool après un intervalle configurable en fonction de votre formule.

Vous pouvez activer la mise à l’échelle automatique lors de la création d’une liste ou d’un pool existant. Vous pouvez également modifier une formule existante sur un pool est « échelle » est activée. Traitement par lots permet d’évaluer vos formules avant leur affectation à des pools, ainsi que de surveiller l’état des séries de mise à l’échelle automatiques.

## <a name="automatic-scaling-formulas"></a>Mise à l’échelle automatique les formules

Une formule de mise à l’échelle automatique est une valeur de chaîne que vous définissez qui contient une ou plusieurs instructions et est affectée à d’un pool [autoScaleFormula] [ rest_autoscaleformula] élément (lot reste) ou [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propriété (lot .NET). Lorsque affectée à un pool, le service lot utilise votre formule pour déterminer le nombre cible des nœuds de calcul dans le pool pour le prochain intervalle de traitement (plus intervalles ultérieurement). La chaîne de la formule ne peut pas dépasser 8 Ko taille, peut inclure jusqu'à 100 instructions qui sont séparées par des points-virgules et peuvent inclure les sauts de ligne et les commentaires.

Vous pouvez considérer des formules de mise à l’échelle automatiques en utilisant une échelle lot « langue ». Instructions formules sont des expressions forme libre qui peuvent inclure des variables définies par le service (variables définies par le service lot) et les variables définies par l’utilisateur (variables que vous définissez). Ils peuvent effectuer diverses opérations sur ces valeurs à l’aide de fonctions, opérateurs et des types intégrés. Par exemple, une instruction peut prendre la forme suivante :

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formules contiennent généralement plusieurs instructions qui effectuent des opérations sur les valeurs qui sont obtenus dans les instructions précédentes. Par exemple, tout d’abord nous obtenir une valeur pour `variable1`, puis la passer à une fonction pour remplir `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Avec ces instructions dans votre formule, votre objectif est d’arriver à un certain nombre de nœuds de calcul le pool doit être mise à l’échelle, le nombre **cible** de **nœuds dédiés**. Ce numéro peut être plus élevé, minuscule et la même comme étant le nombre de nœuds dans le pool actuel. Lot évalue formule d’échelle d’un pool à un intervalle spécifique ([intervalles de mise à l’échelle automatiques](#automatic-scaling-interval) sont décrites ci-dessous). Puis il ajuste le nombre cible de nœuds dans le pool au numéro de votre formule échelle spécifie au moment de l’évaluation.

Un exemple rapide, cette formule deux lignes échelle indique que le nombre de nœuds doit être ajusté en fonction du nombre de tâches actives, avec un maximum de 10 nœuds de calcul :

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Les sections suivantes de cet article discuter différentes entités qui composent vos formules échelle, y compris les variables, opérateurs, opérations et fonctions. Vous découvrirez comment obtenir diverses métriques ressource et tâche cluster au sein du lot. Vous pouvez utiliser les mesures pour ajuster intelligemment nombre de nœuds de votre pool en fonction de l’état tâches et l’utilisation des ressources. Vous allez découvrir puis comment construire une formule et activation à l’échelle automatique sur une liste en utilisant le reste du lot et l’API .NET. Nous allons terminer avec quelques exemples de formules.

> [AZURE.IMPORTANT] Chaque compte Azure lot est limité à un nombre maximal de cœurs (et par conséquent des nœuds de calcul) qui peut être utilisé pour le traitement. Le service lot créera nœuds uniquement jusqu'à cette limite core. Par conséquent, il ne peut pas atteindre le nombre de nœuds de calcul cible qui a été spécifié par une formule. Pour plus d’informations sur l’affichage et augmenter les quotas de votre compte, voir [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md) .

## <a name="variables"></a>Variables

Vous pouvez utiliser des variables à la fois **service défini** et **définies par l’utilisateur** dans vos formules échelle automatique. Les variables définies par le service sont intégré au service lot--certains sont en lecture / écriture et certaines sont en lecture seule. Variables définies par l’utilisateur sont des variables que *vous* définissez. Dans la formule de deux lignes exemple ci-dessus, `$TargetDedicated` est définie par un service variable, tout en `$averageActiveTaskCount` est une variable définie par l’utilisateur.

Les tableaux ci-dessous présentent variables en lecture / écriture et en lecture seule qui sont définies par le service lot.

Vous pouvez **obtenir** et **définir** les valeurs de ces variables service défini pour gérer le nombre de nœuds de calcul dans un pool :

| En lecture / écriture service défini variables | Description |
| --- | --- |
| $TargetDedicated | Le nombre **cible** de **dédié des nœuds de calcul** pour le pool. Il s’agit le nombre de nœuds de calcul le pool doit être mise à l’échelle à. C’est un nombre « cible » dans la mesure où il est possible pour un pool ne pas atteindre le nombre de postes cibles. Cela peut se produire si le nombre cible de nœuds est modifié à nouveau par une évaluation échelle suivantes avant le pool a atteint la cible initiale. Il peut également se produire si un lot compte nœud ou core du quota est atteint avant le nombre cible de nœuds est atteint. |
| $NodeDeallocationOption | L’action qui se produit lorsque les nœuds de calcul sont supprimés à partir d’un pool. Les valeurs possibles sont :<ul><li>**remettre**--termine immédiatement les tâches et les place dans la file d’attente afin qu’ils sont replanifiées.<li>**mettre fin à**--termine immédiatement les tâches et les supprime de la file d’attente.<li>**taskcompletion**--attend en cours d’exécution pour terminer les tâches, puis supprime le nœud à partir du pool.<li>**retaineddata**--attend que toutes les données conservées tâche locales sur le nœud à nettoyé avant de supprimer le nœud à partir du pool.</ul> |

Vous pouvez **obtenir** la valeur de ces variables service défini pour effectuer des ajustements basés sur des indicateurs à partir du service lot :

| Variables service défini en lecture seule | Description |
| --- | --- |
| $CPUPercent | Le pourcentage moyen de l’UC. |
| $WallClockSeconds | Le nombre de secondes consommées. |
| $MemoryBytes | Nombre moyen de mégaoctets utilisé. |
| $DiskBytes | Nombre moyen de gigaoctets utilisées sur les disques locaux. |
| $DiskReadBytes | Le nombre d’octets lus. |
| $DiskWriteBytes | Le nombre d’octets écrits. |
| $DiskReadOps | Le nombre de lecture disque opérations effectuées. |
| $DiskWriteOps | Le nombre d’écriture disque opérations effectuées. |
| $NetworkInBytes | Le nombre d’octets entrants. |
| $NetworkOutBytes | Le nombre d’octets sortants. |
| $SampleNodeCount | Le nombre de nœuds de calcul. |
| $ActiveTasks | Le nombre de tâches dans un état actif. |
| $RunningTasks | Le nombre de tâches en cours d’exécution. |
| $PendingTasks | La somme de $ActiveTasks et $RunningTasks. |
| $SucceededTasks | Le nombre de tâches que vous avez terminé avec succès. |
| $FailedTasks | Le nombre de tâches qui a échoué. |
| $CurrentDedicated | Le nombre actuel de dédié des nœuds de calcul. |

> [AZURE.TIP] Les variables en lecture seule, service défini qui sont ci-dessus sont les *objets* qui fournissent différentes méthodes pour accéder aux données associées à chaque. Pour plus d’informations, consultez [obtenir exemples de données](#getsampledata) en dessous.

## <a name="types"></a>Types de

Ces **types** sont pris en charge dans une formule.

- Double
- doubleVec
- doubleVecList
- chaîne
- horodatage--horodatage est une structure composée qui contient les membres suivants :

    - année
    - mois (1 à 12)
    - jour (1 à 31)
    - JOURSEM (dans le format de nombre, par exemple, 1 pour lundi)
    - heure (dans le format de nombre 24 heures, par exemple, 13 signifie 1 PM)
    - minute (00 à 59)
    - seconde (00 à 59)
- TimeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Opérations

Ces **opérations** sont autorisées sur les types qui sont répertoriées ci-dessus.

| Opération                             | Opérateurs pris en charge   | Type de résultat   |
| ------------------------------------- | --------------------- | ------------- |
| Double *opérateur* double              | +, -, *, /            | Double            |
| Double *opérateur* timeinterval        | *                     | TimeInterval      |
| doubleVec *opérateur* double           | +, -, *, /            | doubleVec         |
| doubleVec *opérateur* doubleVec        | +, -, *, /            | doubleVec         |
| TimeInterval *opérateur* double        | *, /                  | TimeInterval      |
| TimeInterval *opérateur* timeinterval  | +, -                  | TimeInterval      |
| TimeInterval *opérateur* horodatage     | +                     | horodatage         |
| horodatage *opérateur* timeinterval     | +                     | horodatage         |
| horodatage *opérateur* horodatage        | -                     | TimeInterval      |
| *opérateur*double                      | -, !                  | Double            |
| *opérateur*timeinterval                | -                     | TimeInterval      |
| Double *opérateur* double              | <, < =, ==, > =, >, ! =  | Double            |
| *opérateur de* chaîne              | <, < =, ==, > =, >, ! =  | Double            |
| horodatage *opérateur* horodatage        | <, < =, ==, > =, >, ! =  | Double            |
| TimeInterval *opérateur* timeinterval  | <, < =, ==, > =, >, ! =  | Double            |
| Double *opérateur* double              | & &, & #124 ; & #124 ;      | Double            |

Lorsque vous testez un double avec un opérateur ternaire (`double ? statement1 : statement2`) différente de zéro est **vraie**et zéro est **fausse**.

## <a name="functions"></a>Fonctions

Ces **fonctions** prédéfinis sont disponibles pour vous permet d’utiliser dans la définition d’une formule de mise à l’échelle automatique.

| Fonction                          | Valeur de retour   | Description
| --------------------------------- | ------------- | --------- |
| AVG(doubleVecList)                | Double        | Renvoie la valeur moyenne de toutes les valeurs dans le doubleVecList.
| Len(doubleVecList)                | Double        | Renvoie la longueur du vecteur est créé à partir de la doubleVecList.
| LG(double)                        | Double        | Renvoie le journal de base 2 du double.
| LG(doubleVecList)                 | doubleVec     | Renvoie le journal d’un base 2 de la doubleVecList. Un vec(double) doit être passé explicitement pour le paramètre. Dans le cas contraire, la version lg(double) double est supposé égal.
| ln(double)                        | Double        | Renvoie le logarithme népérien de la valeur double.
| ln(doubleVecList)                 | doubleVec     | Renvoie le journal d’un base 2 de la doubleVecList. Un vec(double) doit être passé explicitement pour le paramètre. Dans le cas contraire, la version lg(double) double est supposé égal.
| log(double)                       | Double        | Renvoie le journal du double en base 10.
| log(doubleVecList)                | doubleVec     | Renvoie le journal un la doubleVecList en base 10. Un vec(double) doit être passé explicitement pour le paramètre de type double unique. Dans le cas contraire, la version log(double) double est supposé égal.
| max(doubleVecList)                | Double        | Renvoie la valeur maximale dans la doubleVecList.
| min(doubleVecList)                | Double        | Renvoie la valeur minimale contenue dans la doubleVecList.
| Norm(doubleVecList)               | Double        | Renvoie la norme de deux du vecteur est créé à partir de la doubleVecList.
| centile (v doubleVec, p double) | Double        | Retourne l’élément centile du vecteur v.
| ALEA)                            | Double        | Renvoie une valeur aléatoire entre 0,0 et 1,0.
| Range(doubleVecList)              | Double        | Renvoie la différence entre les valeurs minimale et maximale dans la doubleVecList.
| STD(doubleVecList)                | Double        | Retourne l’écart type exemple des valeurs dans la doubleVecList.
| Stop()                            |               | Arrête l’évaluation de l’expression autoscaling.
| SUM(doubleVecList)                | Double        | Renvoie la somme de tous les composants de la doubleVecList.
| heure (dateTime de chaîne = « »)          | horodatage     | Renvoie l’horodatage de l’heure actuelle si aucun paramètres ne sont passés ou l’horodatage de la chaîne dateTime s’il est passé. Formats de date/heure pris en charge sont W3C DTF et RFC 1123.
| Val (v doubleVec, i double)        | Double        | Renvoie la valeur de l’élément qui se trouve à l’emplacement i dans vecteur v, avec un index de départ de zéro.

Certaines fonctions qui sont décrites dans le tableau ci-dessus peuvent accepter une liste en tant qu’argument. La liste séparées par des virgules est n’importe quelle combinaison de *double* et *doubleVec*. Par exemple :

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

La valeur *doubleVecList* est convertie en un seul *doubleVec* avant d’évaluation. Par exemple, si `v = [1,2,3]`, puis en appelant `avg(v)` équivaut à l’appel `avg(1,2,3)`. Appel `avg(v, 7)` équivaut à l’appel `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtenir des exemples de données

Formules d’échelle agissent sur les données de métrique (exemples) qui sont fournies par le service lot. Une formule augmente ou diminue la taille du pool basée sur les valeurs obtenues dans le service. Les variables définies par le service décrites ci-dessus sont des objets qui fournissent différentes méthodes pour accéder aux données qui est associées à cet objet. Par exemple, l’expression suivante montre une demande pour obtenir les cinq dernières minutes de l’UC :

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Méthode | Description |
| --- | --- |
| GetSample() | La `GetSample()` méthode retourne un vecteur des exemples de données.<br/><br/>Un échantillon est de 30 secondes important des données de métrique. En d’autres termes, exemples sont obtenus toutes les 30 secondes. Mais, comme indiqué ci-dessous, il y a un délai entre lorsqu’un échantillon est collecté et lorsqu’il est disponible pour une formule. Dès lors, pas tous les exemples dans une période donnée peuvent être disponibles pour une évaluation par une formule.<ul><li>`doubleVec GetSample(double count)`<br/>Spécifie le nombre d’échantillons à obtenir à partir des plus récentes échantillons collectés.<br/><br/>`GetSample(1)`Renvoie le dernier échantillonnage disponible. Pour des mesures comme `$CPUPercent`, cependant, il ne doit pas être utilisé car il est impossible de savoir *lors de la* collecte de l’échantillon. Il peut s’avérer récent, ou en raison de problèmes système, il peut être beaucoup plus anciens. Il est préférable dans ce cas d’utiliser un intervalle de temps, comme illustré ci-dessous.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Spécifie un intervalle de temps pour la collecte de données d’exemple. Si vous le souhaitez, il spécifie également le pourcentage d’échantillons qui doivent être disponibles dans la période demandée.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`retournerait 20 échantillons si tous les exemples dans les dix dernières minutes sont présents dans l’historique de CPUPercent. Toutefois, si la dernière minute de l’historique n’était pas disponible, uniquement 18 exemples est retournés. Dans ce cas :<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`échoue, car que 90 % des échantillons sont disponibles.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`devraient réussir.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Spécifie un intervalle de temps pour la collecte de données, avec une heure de début et une heure de fin.<br/><br/>Comme indiqué ci-dessus, il y a un délai entre lorsqu’un échantillon est collecté et lorsqu’il est disponible pour une formule. Cela doit être considéré comme lorsque vous utilisez la `GetSample` méthode. Voir `GetSamplePercent` ci-dessous.|
| GetSamplePeriod() | Renvoie la période d’échantillons qui ont été pris dans un jeu de données d’exemple historiques. |
| Nb() | Renvoie le nombre total d’échantillons dans l’historique métrique. |
| HistoryBeginTime() | Renvoie l’horodatage de l’échantillon de données disponibles plus ancien pour la mesure. |
| GetSamplePercent() |Renvoie le pourcentage d’exemples qui sont disponibles pour un intervalle de temps donné. Par exemple :<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Étant donné que la `GetSample` méthode échoue si le pourcentage d’échantillons retournée est inférieure à la `samplePercent` spécifié, vous pouvez utiliser la `GetSamplePercent` méthode pour vérifier tout d’abord. Ensuite, vous pouvez effectuer une autre action si exemples insuffisants sont présents, sans arrêter l’évaluation de mise à l’échelle automatique.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemples, pourcentage d’échantillonnage et la méthode *GetSample()*

L’opération de base d’une formule échelle automatique est d’obtenir des données métriques de tâches et des ressources, puis ajustez la taille du pool basée sur ces données. Par conséquent, il est important de disposer d’un bien comprendre comment échelle formules interagissent avec les données de métrique ou « exemples ».

**Exemples**

Le service lot prend *exemples* d’indicateurs de tâches et des ressources régulièrement et les rend disponibles pour vos formules échelle automatique. Ces exemples sont enregistrées toutes les 30 secondes par le service lot. Cependant, il est généralement une latence qui provoque un retard entre lorsque ces exemples ont été enregistrées et lorsqu’ils sont mis à disposition (et peuvent être lus par) vos formules échelle automatique. En outre, en raison de plusieurs facteurs tels que le réseau ou d’autres problèmes d’infrastructure, exemples pas ont pu être enregistrées pour un intervalle spécifique. Le résultat « absent » exemples.

**Pourcentage d’échantillonnage**

Lorsque `samplePercent` est passé à la `GetSample()` méthode ou le `GetSamplePercent()` est appelée, « pourcentage » fait référence à une comparaison entre le nombre total *possible* d’échantillons qui sont enregistrés par le service de commandes et le nombre d’échantillons qui sont réellement *disponibles* pour votre formule échelle automatique.

Nous allons étudier timespan 10 minutes par exemple. Étant donné que les exemples sont enregistrées toutes les 30 secondes, au sein d’un timespan 10 minutes, le nombre maximal de total des échantillons qui sont enregistrés par lot serait 20 échantillons (2 par minute). Toutefois, en raison de la latence intrinsèque à du mécanisme de création de rapports ou un autre problème au sein de l’infrastructure Azure, il peut y avoir uniquement 15 échantillons qui sont disponibles pour votre formule échelle pour la lecture. Cela signifie que, pour cette période de 10 minutes, que **75 %** du nombre total d’échantillons enregistré sont réellement disponible pour votre formule.

**Plages GetSample() et exemple**

Vos formules échelle allez croissance et la réduction des pools : ajout de nœuds ou en supprimant des nœuds. Étant donné que les nœuds coûtent de l’argent, que vous voulez s’assurer que vos formules utilisent une méthode intelligente d’analyse basé sur des données suffisantes. Par conséquent, nous vous recommandons d’utiliser une analyse de type analyse des tendances dans vos formules. Ce type sera agrandir et réduire des pools basées sur une *plage* d’échantillons collectés.

Pour ce faire, utilisez `GetSample(interval look-back start, interval look-back end)` pour renvoyer un **vecteur** des exemples :

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Lorsque la ligne ci-dessus est évaluée par lot, il renverra une plage d’échantillons comme un vecteur de valeurs. Par exemple :

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Une fois que vous avez collectées le vecteur des échantillons, vous pouvez ensuite utiliser des fonctions, telles que `min()`, `max()`, et `avg()` à dérivent les valeurs de la plage collecte.

Pour renforcer la sécurité, vous pouvez forcer une évaluation de formule *Échec* si inférieur à un certain pourcentage d’exemples n’est disponible pour une période donnée. Lorsque vous forcez une évaluation de formule échec, vous invitez lot de cesser l’évaluation de la formule davantage si le pourcentage spécifié d’exemples n’est pas disponible, et aucune modification de la taille du pool ne sera. Pour spécifier un pourcentage requis d’échantillons de l’évaluation réussir, définissez-le comme étant le troisième paramètre à `GetSample()`. Ici, une obligation de 75 % des échantillons est spécifiée :

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Il est également important, en raison de délai indiqué précédemment en disponibilité exemple, toujours spécifier une plage horaire avec une heure de début apparence précédent est antérieure à une minute. Il s’agit parce qu’il faut environ une minute des exemples de se propager dans le système, donc exemples dans la plage `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` souvent ne seront plus disponibles. Là encore, vous pouvez utiliser le paramètre de pourcentage de `GetSample()` pour forcer une obligation pourcentage exemple particulier.

> [AZURE.IMPORTANT] Nous **vous recommandons vivement** que vous * *Évitez d’avoir recours *uniquement* sur `GetSample(1)` dans vos formules échelle **. C’est parce que `GetSample(1)` dit globalement au service lot, « donnez-moi du dernier échantillon avoir, quel que soit Combien ça. » Dans la mesure où il n'est qu’un échantillon unique, et il peut être un exemple plus ancien, il peut ne pas être représentant de l’image plus grande de tâches récentes ou état de la ressource. Si vous utilisez `GetSample(1)`, assurez-vous qu’il fait partie d’une instruction plus grande et non le point uniquement les données qui dépend de votre formule.

## <a name="metrics"></a>Indicateurs

Vous pouvez utiliser les mesures de **ressource** et **tâche** lorsque vous définissez une formule. Vous ajustez le nombre cible de nœuds dédiés dans le pool basé sur les données d’indicateurs que vous obtenez et évaluez. Voir la section [Variables](#variables) ci-dessus pour plus d’informations sur chaque métrique.

<table>
  <tr>
    <th>Métrique</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><b>Ressource</b></td>
    <td><p><b>Indicateurs de ressource</b> basés sur l’utilisation du processeur, la bande passante et la mémoire des nœuds de calcul, ainsi que le nombre de nœuds.</p>
        <p> Ces variables service défini sont utiles pour effectuer des ajustements en fonction du nombre de nœuds :</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ces variables service défini sont utiles pour effectuer des ajustements en fonction de l’utilisation des ressources nœud :</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tâche</b></td>
    <td><p><b>Indicateurs de la tâche</b> basées sur l’état des tâches, telles qu’actif, en attente et terminée. Les variables définies par le service suivantes sont utiles pour effectuer des ajustements taille du pool selon des métriques de tâche :</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Rédiger une formule échelle automatique

Vous créez une formule échelle en formant les instructions qui utilisent les composants ci-dessus, puis combinez ces instructions dans une formule complète. Dans cette section, nous allons créer un exemple de formule échelle automatique qui peut effectuer certaines décisions de mise à l’échelle réelle.

Tout d’abord, nous allons définir la configuration requise pour notre nouvelle formule échelle automatique. La formule, vous devez :

1. **Augmenter** le nombre cible de nœuds de calcul dans un pool si l’UC est élevé.
2. **Réduire** le nombre cible de nœuds de calcul dans un pool lors de l’UC est bas.
3. Toujours limiter le nombre **maximal** de nœuds à 400.

Vous pouvez *augmenter* le nombre de nœuds au cours d’utilisation intensive du processeur, nous définir l’instruction qui remplit une variable définie par l’utilisateur (`$totalNodes`) avec une valeur qui est 110 pour cent du nombre de nœuds, mais seulement si cible actuel moyenne minimale de l’UC pendant les 10 dernières minutes a été supérieure à 70 %. Dans le cas contraire, nous utilisons la valeur dédiée actuelle.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Pour *réduire* le nombre de nœuds lors de l’UC faible, l’instruction suivante dans notre formule définit le même `$totalNodes` variable à 90 pour cent du nombre de nœuds si la moyenne de l’UC dans les dernières 60 minutes était sous 20 % cible actuel. Dans le cas contraire, utilisez la valeur actuelle de `$totalNodes` qui nous rempli dans l’instruction ci-dessus.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Maintenant limiter le nombre de cible dédié des nœuds de calcul sur un **maximum** de 400 :

```
$TargetDedicated = min(400, $totalNodes)
```

Voici la formule complète :

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Créer un pool compatibles avec échelle automatique

Pour créer un nouveau pool avec autoscaling activé, vous pouvez utiliser une des méthodes suivantes :

**Lot .NET**

1. Créer le pool avec [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Définissez la propriété [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) sur `true`.
1. Définissez la propriété [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) avec votre formule échelle automatique.
1. (Facultatif) Définissez la propriété [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (valeur par défaut est 15 minutes).
1. Valider le pool avec [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) ou [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Lot API REST**

* [Ajouter un pool à un compte](https://msdn.microsoft.com/library/azure/dn820174.aspx): spécifiez la `enableAutoScale` et `autoScaleFormula` éléments dans votre demande API REST pour configurer une échelle automatique d’un pool lors de sa création.

L’extrait de code suivant crée un pool compatibles avec échelle automatique en utilisant le [Lot .NET] [ net_api] bibliothèque. Formule d’échelle du pool définit le nombre cible de nœuds à 5 le lundi et 1 sur tous les jours de la semaine. L' [intervalle de mise à l’échelle automatique](#automatic-scaling-interval) est défini sur 30 minutes. Dans ce guide et les autres extraits c# dans cet article, « myBatchClient » est une instance correctement initialisée de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Outre l’API REST lot et le Kit de développement .NET, vous pouvez utiliser un des autres [SDK lot](batch-technical-overview.md#batch-development-apis), [applets de commande PowerShell lot](batch-powershell-cmdlets-get-started.md)et l' [Infrastructure du langage commun lot](batch-cli-get-started.md) pour l’utiliser avec autoscaling.

> [AZURE.IMPORTANT] Lorsque vous créez un pool compatibles avec échelle, vous devez **pas** spécifier la `targetDedicated` paramètre. En outre, si vous voulez manuellement redimensionner un pool compatibles avec échelle (par exemple, avec [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), puis vous devez première **désactiver** échelle sur le pool automatique, puis la redimensionnez.

### <a name="automatic-scaling-interval"></a>Intervalle de mise à l’échelle automatique

Par défaut, le service de traitement par lots ajuste la taille d’un pool en fonction de sa formule échelle automatique toutes **les 15 minutes**. Cet intervalle est configurable, cependant, en utilisant les propriétés du pool suivants :

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (lot .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (API REST)

L’intervalle minimal est de cinq minutes, et la valeur maximale est 168 heures. Si un intervalle en dehors de cette plage est spécifié, le service lot renverra une erreur (400) demande incorrecte.

> [AZURE.NOTE] AutoScaling n’est pas actuellement destinée à répondre aux changements en moins d’une minute, mais préférez est conçu pour ajuster la taille de votre pool progressivement lorsque vous exécutez une charge de travail.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Activer autoscaling sur un pool existant

Si vous avez déjà créé un pool avec un nombre défini de nœuds de calcul en utilisant le paramètre *targetDedicated* , vous pouvez toujours activer autoscaling sur le pool. Chaque SDK lot fournit une opération « activer échelle automatique », par exemple :

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (lot .NET)
*  [Activer le dimensionnement automatique sur un pool] [ rest_enableautoscale] (API REST)

Lorsque vous activez autoscaling sur un pool existant, les éléments suivants s’applique :

* Si le dimensionnement automatique est actuellement **désactivé** sur le pool lorsque vous exécutez la requête « activer échelle automatique », vous *devez* spécifier une formule d’échelle valide lorsque vous exécutez la requête. Vous pouvez *éventuellement* spécifier un intervalle d’échelle d’évaluation. Si vous ne spécifiez pas un intervalle, la valeur par défaut de 15 minutes est utilisée.

* Si l’échelle automatique est actuellement **activé** sur le pool, vous pouvez spécifier une formule échelle, un intervalle d’évaluation ou les deux. Vous ne pouvez pas omettre les deux propriétés.

  * Si vous spécifiez un intervalle échelle d’évaluation, puis la planification d’évaluation existante est arrêtée et un planning est démarré. Heure de début de la nouvelle planification est le moment auquel la demande « activer échelle automatique » a été émise.

  * Si vous omettez l’échelle automatique de formule ou intervalle d’évaluation, le service lot continue à utiliser la valeur actuelle de ce paramètre.

> [AZURE.NOTE] Si une valeur a été spécifiée pour le paramètre *targetDedicated* lorsque le pool a été créé, il est ignoré lorsque la formule de mise à l’échelle automatique est évaluée.

Cet extrait de code c# utilise le [Lot .NET] [ net_api] bibliothèque pour activer autoscaling sur un pool existant :

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Mettre à jour une formule échelle automatique

Vous utilisez la même demande à *mettre à jour* la formule « activer échelle automatique » sur un pool compatibles avec échelle existant (par exemple, avec [EnableAutoScale] [ net_enableautoscale] dans .NET lot). Aucune opération « mettre à jour d’échelle automatique » spécial est. Par exemple, si autoscaling est déjà activé sur « myexistingpool » lorsque le code suivant est exécuté, sa formule échelle automatique est remplacé par le contenu de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Mise à jour de l’intervalle d’échelle

Comme avec la mise à jour une formule échelle, vous utilisez même [EnableAutoScale] [ net_enableautoscale] méthode pour modifier l’intervalle d’évaluation échelle automatique d’un pool compatibles avec échelle existant. Par exemple, pour définir un intervalle d’échelle d’évaluation à 60 minutes d’un pool est déjà activé échelle automatique :

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Évaluer une formule échelle automatique

Vous pouvez évaluer une formule avant d’appliquer à un pool. De cette façon, vous pouvez effectuer un « test exécuter » de la formule pour voir comment ses instructions évaluent avant de placer la formule en production.

Pour évaluer une formule échelle, vous devez **Activer autoscaling** sur le pool avec une **formule valide**. Si vous souhaitez tester une formule dans une liste qui ne dispose pas encore autoscaling activé, vous pouvez utiliser la formule d’une ligne `$TargetDedicated = 0` lorsque vous activez tout d’abord autoscaling. Utilisez ensuite une des opérations suivantes pour évaluer la formule que vous souhaitez tester :

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Ces méthodes lot .NET requièrent l’ID d’un pool existant et une chaîne qui contient la formule échelle doit être évaluée. Résultats de l’évaluation sont contenues dans l’instance [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) retournée.

* [Évaluer une formule de mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    Dans cette requête API REST, spécifiez l’ID du pool dans l’URI et la formule échelle automatique dans l’élément *autoScaleFormula* du corps de la demande. La réponse de l’opération contient les informations d’erreur pouvant être liés à la formule.

Dans ce [Lot .NET] [ net_api] extrait de code, nous évaluer une formule avant d’appliquer à la [CloudPool][net_cloudpool]. Si le regroupement ne dispose pas autoscaling activé, nous activez-le tout d’abord.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Évaluation réussie de la formule dans cet extrait de code créent une sortie similaire à ce qui suit :

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtenir des informations sur échelle automatique s’exécute

Pour vous assurer que votre formule fonctionne comme prévu, nous vous recommandons de que vous vérifiez régulièrement les résultats de l’autoscaling « s’exécute « lot effectue sur votre pool. Pour exécuter ce cas, obtenir (ou Actualiser) une référence à la liste et examinez les propriétés de son échelle dernière exécuter.

Dans .NET lot, la propriété [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) possède plusieurs propriétés fournissant des informations sur la dernière échelle automatique exécuter sur le pool effectuées par le service lot.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Dans l’API REST, la demande [d’obtenir des informations sur un pool](https://msdn.microsoft.com/library/dn820165.aspx) renvoie des informations sur le regroupement, qui inclut la dernière échelle automatique, exécutez informations dans [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

L’extrait de code c# suivant utilise la bibliothèque .NET lot pour imprimer des informations sur la dernière autoscaling s’exécutent sur pool « monpool » :

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemple de sortie de l’extrait précédent :

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Exemples de formules échelle automatique

Examinons quelques formules qui montrent différentes façons d’ajuster la quantité de ressources de cluster dans une liste.

### <a name="example-1-time-based-adjustment"></a>Exemple 1 : Temporelles ajustement

Vous souhaitez peut-être ajuster la taille du pool basée sur le jour de la semaine et l’heure du jour, pour augmenter ou diminuer le nombre de nœuds dans le pool en conséquence.

Cette formule obtient d’abord l’heure actuelle. S’il s’agit d’un jour de la semaine (1-5) et les heures de travail (8 h à 6 h), la taille du pool cible est définie sur 20 postes. Dans le cas contraire, il est défini sur 10 postes.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemple 2 : Basées sur tâche ajustement

Dans cet exemple, la taille du pool est ajustée en fonction du nombre de tâches dans la file d’attente. Notez que les commentaires et les sauts de ligne sont acceptables dans les chaînes de formule.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemple 3 : Gestion des tâches en parallèle

Il s’agit d’un autre exemple qui ajuste la taille du pool en fonction du nombre de tâches. Cette formule prend également en considération les [MaxTasksPerComputeNode] [ net_maxtasks] valeur qui a été défini pour le pool. Ceci est particulièrement utile dans les cas où [l’exécution des tâches en parallèle](batch-parallel-node-tasks.md) a été activée sur votre pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemple 4 : Définir une taille de regroupement initial

Cet exemple montre un extrait de code c# avec une formule échelle qui définit la taille du pool à un certain nombre de nœuds pour une période initiale. Puis elle s’ajuste la taille du pool en fonction du nombre d’en cours d’exécution et active les tâches une fois la période initiale écoulée.

La formule dans l’extrait de code suivantes :

- Définit la taille du pool initial à quatre nœuds.
- Ne modifie pas la taille du pool dans les 10 premières minutes du cycle de vie du pool.
- Après 10 minutes, obtient la valeur maximale du nombre de tâches en cours d’exécution et actives le 60 minutes passées.
  - Si les deux valeurs sont 0 (indiquant qu’aucune tâche ont été en cours d’exécution ou active dans les dernières 60 minutes), la taille du pool est définie sur 0.
  - Si des valeurs sont supérieur à zéro, aucune modification est effectuée.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Étapes suivantes

* [Agrandir le lot Azure calculer l’utilisation des ressources à effectuer les tâches simultanées nœud](batch-parallel-node-tasks.md) contient des informations sur la façon dont vous pouvez effectuer plusieurs tâches simultanément sur les nœuds de calcul dans le pool de. Outre autoscaling, cette fonctionnalité peut vous aider à réduire la durée des tâches pour certaines charges de travail, vous épargner.

* Pour un autre booster l’efficacité, assurez-vous que votre application lot requêtes le service de traitement par lots de façon plus optimale. Dans [interroger le service Azure lot efficacement](batch-efficient-list-queries.md), vous allez découvrir comment vous pouvez limiter la quantité de données croisant le fil lorsque vous exécutez une requête l’état des milliers de nœuds de calcul ou des tâches.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
