<properties
    pageTitle="Meilleures pratiques pour autoscaling Azure moniteur. | Microsoft Azure"
    description="Découvrez les principes pour utiliser efficacement autoscaling moniteur d’Azure."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Meilleures pratiques pour autoscaling moniteur Azure

Les sections suivantes dans ce document vous aideront à comprendre les pratiques recommandées pour échelle dans Azure. Après avoir consulté ces informations, vous pourrez mieux utiliser efficacement possible échelle dans votre infrastructure Azure.

## <a name="autoscale-concepts"></a>Concepts de l’échelle

- Une ressource peut avoir uniquement *un* paramètre d’échelle
- Un paramètre d’échelle peut comporter un ou plusieurs profils et chaque profil peuvent avoir une ou plusieurs règles d’échelle.
- Un paramètre d’échelle nuances instances horizontalement, *qui est en augmentant *les instances et* en réduisant le nombre d’instances* .
 Un paramètre d’échelle automatique a un maximum, minimum et valeur par défaut d’instances.
- Une tâche d’échelle lit toujours la métrique associée à l’échelle par, si elle a atteint le seuil configuré pour horizontale ou d’échelle de la vérification. Vous pouvez afficher une liste des mesures qu’échelle peut évoluer au [Moniteur Azure autoscaling courantes indicateurs](insights-autoscale-common-metrics.md).
- Tous les seuils sont calculées au niveau de l’instance. Par exemple, « échelle en 1 instance moyenne lorsque processeur > 80 % lorsque le nombre d’instances indique 2 », signifie horizontale lorsque l’UC moyenne dans toutes les instances est supérieure à 80 %.
- Vous recevez toujours les notifications d’échec par courrier électronique. Plus précisément, le propriétaire, les collaborateurs et les lecteurs de la ressource cible recevoir des courriers électroniques. Vous recevez également toujours une adresse de messagerie *récupération* lorsque échelle récupère une défaillance et commence fonctionne normalement.
- Vous pouvez désactiver cette connexion pour recevoir une notification d’action réussie échelle par courrier électronique et webhooks.

## <a name="autoscale-best-practices"></a>Meilleures pratiques échelle automatique

Utilisez les meilleures pratiques suivantes lorsque vous utilisez échelle automatique.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Assurez-vous que les valeurs maximales et minimales sont différentes et ont une marge suffisante entre elles
Si vous avez un paramètre qui a minimum = 2, maximum = 2 et en cours nombre d’instances est 2, aucune action échelle ne peut se produire. Conserver une marge suffisante entre les nombres de l’instance maximale et minimale, qui sont inclus. Échelle systématiquement à l’échelle entre ces limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Mise à l’échelle manuelle est réinitialisé en max, min échelle automatique
Si vous mettre à jour manuellement le nombre d’instances à une valeur inférieures ou supérieures à la valeur maximale, le moteur d’échelle s’ajuste automatiquement au minimum (si elle est inférieure) ou la valeur maximale (s’il est supérieur). Par exemple, vous définissez la plage comprise entre 3 et 6. Si vous avez une instance en cours d’exécution, le moteur d’échelle s’adapte à 3 instances lors de son exécution suivante. De même, il serait échelle-dans 8 instances sauvegarde à 6 sa prochaine exécution.  Mise à l’échelle manuelle est très temporaire, sauf si vous réinitialisez les règles d’échelle.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Toujours utiliser une combinaison de la règle horizontale et échelle dans qui effectue une augmenter et diminuer
Si vous utilisez uniquement une partie de la combinaison, échelle échelle complémentaire qui unique, ou, jusqu'à ce que la valeur maximale ou minimale, est atteint.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Ne pas basculer entre le portail Azure et le portail classique Azure lors de la gestion d’échelle
Pour les Services de Cloud et Services d’application (applications Web), utilisez le portail Azure (portal.azure.com) pour créer et gérer les paramètres d’échelle. Pour les jeux d’échelle Machine virtuelle utilisez PoSH, infrastructure du langage commun ou API REST pour créer et gérer les paramètres d’échelle. Ne pas basculer entre le portail classique Azure (manage.windowsazure.com) et le portail Azure (portal.azure.com) lors de la gestion des configurations échelle automatique. Le portail classique Azure et sa version serveur sous-jacent comporte des limitations. Déplacer vers le portail Azure pour gérer l’échelle à l’aide d’une interface utilisateur graphique. Les options sont à utiliser l’échelle PowerShell, infrastructure du langage commun ou API REST (via Azure Explorateur des ressources).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Choisissez la statistique appropriée pour votre métrique diagnostics
Pour les mesures diagnostics, vous pouvez choisir parmi *Average*, *Minimum*, *Maximum* et *Total* comme métrique à l’échelle par. La statistique plus courante est *moyenne*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Choisissez les seuils avec soin pour tous les types de mesure
Nous vous recommandons de choisir avec soin des seuils différents pour horizontale et échelle dans basé sur les pratiques.

Nous *ne recommandons pas* échelle paramètres tels que les exemples ci-dessous avec les valeurs seuil même ou très similaire pour extraction et conditions :

- Augmenter les instances de 1 compter quand nombre de threads < = 600
- Réduire les instances de 1 compter quand nombre de threads > = 600

Examinons un exemple de ce qui peut entraîner un comportement qui peut sembler confus. Cosider la séquence suivante.

1. Supposons 2 instances sont dans un premier temps et puis nombre moyen de threads par instance s’agrandit 625.
2. Ajout d’une instance 3e échelle s’adapte.
3. Supposons ensuite que le nombre de threads moyenne au sein de l’instance se situe à 575.
4. Avant la mise à l’échelle vers le bas, échelle essaie d’estimer quel l’état final sera si elle est redimensionnée dans. Par exemple, 575 x 3 (nombre d’instances en cours) = 1,725 / 2 (final nombre d’instances lors de la mise à l’échelle vers le bas) = 862.5 threads. Cela signifie échelle devra immédiatement horizontale à nouveau même une fois que celle-ci à l’échelle, si le nombre de threads moyenne reste la même ou même situe uniquement une petite partie. Toutefois, si elle agrandies à nouveau, l’ensemble du processus serait répéter, et causer une boucle sans fin.
5. Pour éviter cette situation (appelée « ailes »), échelle n’est pas adaptée vers le bas du tout. Au lieu de cela, il vous permet d’ignorer et réévalue la condition la prochaine que tâche du service s’exécute. Cela pourrait confondez de nombreuses personnes car échelle automatique ne semble pas fonctionner lorsque le nombre de threads moyenne était 575.

Estimation lors d’une échelle à est conçue pour éviter les situations « flappy ». Vous devez garder ce comportement à l’esprit lorsque vous choisissez les mêmes seuils pour horizontale et dans.

Nous vous recommandons de choisir une marge suffisante entre la distribution horizontale et les seuils. Par exemple, vous pouvez la combinaison règle mieux suivante.

- Augmenter les instances de 1 compter quand % processeur > = 80
- Réduire les instances de 1 compter quand % processeur < = 60

Dans ce cas  

1. Supposons sont 2 instances d’origine.
2. Si le pourcentage processeur moyen pour plusieurs instances accède à 80, échelle évolue d’ajout d’une troisième instance.
3. Supposons maintenant que le temps % de l’UC se situe à 60.
4. Règles d’échelle d’échelle calcule l’état final s’il s’agissait à l’échelle. Par exemple, 60 x 3 (nombre d’instances en cours) = 180 / 2 (final nombre d’instances lors de la mise à l’échelle vers le bas) = 90. Donc échelle ne pas échelle complémentaire, car il devra horizontale redémarre immédiatement. Au lieu de cela, il ignore la mise à l’échelle vers le bas.
5. L’échelle de temps suivant vérifie, l’UC continue à 50. Il estime à nouveau - 50 x 3 instance = 150 / 2 instances = 75, est inférieur au seuil horizontale de 80, afin qu’il s’adapte dans correctement à 2 instances.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considérations relatives à la mise à l’échelle des valeurs de seuil d’indicateurs spéciaux
 Pour les indicateurs spéciaux tels que métrique longueur file d’attente de Bus de Service ou de stockage, le seuil est le nombre moyen de messages disponibles par nombre actuel d’instances. Choisissez avec soin la choisir la valeur seuil pour cette mesure.

Nous allons illustrer par exemple pour vous assurer que vous comprenez le comportement mieux.

- Augmenter les instances par 1 nombre lorsque le nombre de messages file d’attente stockage > = 50
- Réduire les instances de 1 count lorsque le nombre de messages file d’attente stockage < = 10

Prenez en compte l’ordre suivant :

1. Il existe 2 instances de file d’attente de stockage.
2. Messages conservent bientôt et lorsque vous passez en revue la file d’attente de stockage, le nombre total lit 50. Vous pouvez alors supposer qu’échelle doit commencer une action horizontale. Toutefois, notez qu’il est toujours 50/2 = 25 messages par instance. Par conséquent, horizontale n’a pas lieu. Pour la première horizontale se produise, le nombre total de messages dans la file d’attente de stockage doit être 100.
3. Supposons ensuite que le nombre de messages total atteint 100.
4. Une instance de file d’attente de stockage 3e est ajoutée en raison d’une action horizontale.  L’action horizontale suivante se produit pas jusqu'à ce que le nombre total de messages dans la file d’attente atteigne 150 car 150/3 = 50.
5. À présent le nombre de messages dans la file d’attente diminue. Avec 3 instances, la première action d’échelle qui se produit lorsque le nombre total de messages dans toutes les files d’ajouter jusqu'à 30, car 30/3 = 10 messages par instance, ce qui est le seuil d’échelle.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considérations relatives à la mise à l’échelle lorsque plusieurs profils sont configurés dans un paramètre d’échelle

Dans un paramètre d’échelle, vous pouvez choisir un profil par défaut, qui est toujours appliqué sans toute dépendance sur Planification ou d’heure, ou vous pouvez choisir un profil périodique ou un profil pour une période fixe avec une plage de date et heure.

Lorsque le service échelle traite les, elle vérifie toujours dans l’ordre suivant :

1. Profil de Date fixe
2. Profil périodique
3. (« Toujours ») profil par défaut

Si une condition profil est remplie, échelle automatique ne vérifie pas la prochaine condition de profil située en dessous. Échelle traite uniquement un profil à la fois. Cela signifie que si vous souhaitez également inclure une condition de traitement d’un profil de niveau inférieur, vous devez également inclure ces règles dans le profil en cours.

Examinons cela à l’aide d’un exemple :

L’image suivante montre un paramètre d’échelle avec un profil par défaut des instances minimales = 2 et maximales instances = 10. Dans cet exemple, les règles sont configurés pour horizontale lorsque le nombre de messages dans la file d’attente est supérieur à 10 et échelle connexion lorsque le nombre de messages dans la file d’attente est inférieur à 3. La ressource peut désormais évoluer entre 2 et 10 instances.

Par ailleurs, il est un profil périodique défini pour lundi. Il a la valeur minimale instances = 2 et maximales instances = 12. Cela signifie que le lundi, l’échelle de temps première vérifie cette condition, si le nombre d’instances de 2, elle s’adapte à la nouvelle valeur minimale de 3. Dans la mesure où échelle continue trouver cette condition profil correspondance (lundi), il traite uniquement les UC échelle-out/in règles configurés pour ce profil. Pour le moment, il ne vérifie pas la file d’attente. Toutefois, si vous souhaitez également la condition de longueur file d’attente être vérifié, vous devez inclure également ces règles à partir du profil par défaut dans votre profil lundi.

De même, lors de l’échelle bascule vers le profil par défaut, il vérifie d’abord si les conditions minimales et maximales sont remplies. Si le nombre d’instances en temps est 12, elle s’adapte à 10, la valeur maximale autorisée pour le profil par défaut dans.

![paramètres d’échelle](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considérations relatives à la mise à l’échelle lorsque plusieurs règles sont configurés dans un profil
Il existe des cas dans lesquels vous devez définir plusieurs règles dans un profil. L’ensemble des règles d’échelle suivantes servent à l’aide de services lorsque plusieurs règles sont définies.

*Mise à l’échelle*, échelle automatique s’exécute si une règle est remplie.
*Dans l’échelle*, échelle nécessitent toutes les règles à respecter.

Pour illustrer, supposons que vous avez 4 règles échelle suivantes :

- Si UC < 30 %, échelle dans par 1
- Si la quantité de mémoire < 50 %, échelle dans par 1
- Si UC > 75 %, horizontale par 1
- Si la quantité de mémoire > 75 %, horizontale par 1

Puis suivez se produit :

- Si UC est 76 % et mémoire 50 %, nous horizontale.
- Si UC est 50 % et mémoire 76 % nous horizontale.

En revanche, si UC est 25 % et mémoire 51 % échelle ne **pas** dans l’échelle. De manière à échelle dans, processeur doit être 29 % et la mémoire 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Sélectionnez toujours un nombre d’instances de valeur par défaut sécurisée
Le nombre d’instances par défaut est important échelle nuances de votre service ce nombre quand les mesures ne sont pas disponibles. Par conséquent, sélectionnez un nombre d’instances par défaut est suffisamment pour vos charges de travail.

### <a name="configure-autoscale-notifications"></a>Configurer les notifications par échelle automatique
Échelle informe les administrateurs et les collaborateurs de la ressource par courrier électronique si une des conditions suivantes se produit :

- service échelle automatique ne parvient pas à effectuer une action.
- Indicateurs ne sont pas disponibles pour service échelle prendre une décision échelle.
- Métriques sont disponible (récupération) à nouveau pour prendre une décision échelle.
Outre les conditions ci-dessus, vous pouvez configurer les notifications de message électronique ou webhook pour recevoir une notification pour les actions échelle réussie.
