<properties
   pageTitle="Reprise d’activité et disponibilité des Applications Azure | Microsoft Azure"
   description="Présentations techniques et plus d’informations sur la conception d’applications à haute disponibilité et reprise après sinistre d’applications basées sur Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Reprise d’activité et la disponibilité des applications basées sur Microsoft Azure

##<a name="introduction"></a>Introduction

Cet article se concentre sur la disponibilité des applications qui s’exécutent dans Azure. Une stratégie globale de disponibilité comprend également l’aspect de sinistre. Planification des échecs et des incidents dans le cloud, vous devez reconnaître les échecs rapidement. Ensuite, vous implémentez une stratégie qui correspond à votre tolérance de temps d’arrêt de l’application. En outre, vous devez prendre en compte l’étendue de l’application peut tolérer sans provoquer conséquences indésirables entreprise lors de la restauration de perte de données.

La plupart des sociétés disent qu’ils sont prêts pour les échecs temporaires et à grande échelle. Toutefois, avant de vous répondez à cette question pour vous-même, le votre société visionner des ces échecs ? Tester la récupération des bases de données pour vous assurer que les processus corrects en place ? Probablement pas. C’est parce que sinistre réussie commence par un nombre important de planification et de conception pour mettre en œuvre ces processus. Comme plusieurs autres impératifs non fonctionnels, tels que sécurité, sinistre obtient rarement l’analyse tout se déroule correctement et l’allocation de temps qu'il nécessite. En outre, la plupart des sociétés n’avez pas le budget de régions géographiquement avec capacité redondante. Par conséquent, même les applications critiques sont exclues fréquemment planification de récupération d’urgence appropriée.

Plateformes cloud, tel que Azure, fournissent géographiquement régions du monde. Ces plates-formes offrent également des fonctionnalités qui prennent en charge de la disponibilité et un large éventail de récupération d’urgence. À présent, chaque application critique dans le nuage mission peut être donnée considération échéance pour urgence vérification du système. Azure a résilience et reprise intégré à un grand nombre de ses services. Vous devez étudier avec soin ces fonctionnalités de la plate-forme et compléter avec les stratégies d’application.

Cet article explique les étapes nécessaires architecture à suivre pour risque de sinistre un déploiement d’Azure. Vous pouvez implémenter le processus de continuité Professionnel plus grand. Un plan continuité de service est une feuille de route pour poursuivre les opérations dans le cadre des conditions difficiles. Cela peut être une défaillance avec la technologie, par exemple un service en panne ou de sinistre naturel, par exemple une panne vague d’ou power. La résilience application pour incidents est uniquement un sous-ensemble de la plus grande processus de reprise, comme décrit dans ce document NIST : [Guide de planification de plan d’urgence pour systèmes informatiques](https://www.fismacenter.com/sp800-34.pdf).

Les sections suivantes définissent différents niveaux d’échecs, techniques de traiter les et architectures prenant en charge ces techniques. Ces informations fournissent les entrées à votre processus de récupération d’urgence et les procédures, pour vérifier le bon que fonctionne de votre stratégie de récupération d’urgence correctement et efficacement.

##<a name="characteristics-of-resilient-cloud-applications"></a>Caractéristiques d’applications cloud résistant

Une application bien conçue résistance échecs de fonctionnalité à un niveau tactique, et il peut faire également face stratégiques défaillances de système à l’échelle au niveau de la région. Les sections suivantes définissent la terminologie référencée dans tout le document pour décrire les différents aspects des services en nuage résistant.

###<a name="high-availability"></a>Disponibilité

Une application en nuage hautement disponible met en œuvre les stratégies pour retirer la coupure de dépendances, tels que les services gérées offerts par la plateforme en nuage. Malgré les échecs possibles des fonctionnalités de la plateforme en nuage, cette approche permet à l’application de continuer à présentant les caractéristiques systémiques fonctionnels et non fonctionnels attendus. Ceci est examinée en détail dans la série de vidéos Channel 9, [secours : conseils pour des Architectures de Cloud résistant](https://channel9.msdn.com/Series/FailSafe).

Lorsque vous implémentez l’application, vous devez prendre en compte la probabilité d’une interruption de fonctionnalité. En outre, vous pouvez l’impact d’une panne sur l’application à partir de la perspective d’entreprise, avant de plonger approfondie dans les stratégies d’implémentation. Sans date d’échéance en considération l’impact et la probabilité de réussir la condition du risque, l’implémentation peut être coûteux et potentiellement inutiles.

Envisagez d’une véhicule analogie de disponibilité. Même composants de qualité et ingénierie supérieure n’empêche pas les échecs occasionnels. Par exemple, lorsque votre voiture Obtient un pneumatique plat, la voiture s’exécute toujours, mais il fonctionne avec la fonctionnalité de dégradé. Si vous planifiée pour cette occurrence potentielle, vous pouvez utiliser une de ces pneumatiques rechange avec bordure mince jusqu'à ce que vous atteigniez un magasin de réparation. Bien que la roue de secours ne permet pas de vitesse plus rapide, vous pouvez toujours utiliser véhicule jusqu'à ce que vous remplacez le pneumatique. De même, un service cloud qui planifie risque de perte de fonctionnalités peut empêcher un problème mineur relativement interrompre l’ensemble de l’application. C’est vrai même si le service de nuage doit s’exécuter avec la fonctionnalité de dégradé.

Il existe quelques caractéristiques clés des services en nuage hautement disponibles : disponibilité, tolérance de panne et extensibilité élevées. Bien que ces caractéristiques sont liées, il est important de comprendre chacune, et comment ils contribuer à la disponibilité globale de la solution.

###<a name="availability"></a>Disponibilité

Une application disponible estime la disponibilité de ses infrastructure sous-jacente et les services qui en dépendent. Applications disponibles supprimer les points d’échec via redondance et la conception résistante. Lorsque vous élargir l’étendue à prendre en considération disponibilité dans Azure, il est important de comprendre le concept de la disponibilité de la plate-forme efficace. Disponibilité efficace considère le Service niveau accords de chaque service dépendant et leur effet sur la disponibilité du système total cumulé.

Disponibilité du système est la mesure du pourcentage d’une fenêtre de temps que le système seront en mesure de fonctionner. Par exemple, la disponibilité SLA au moins deux instances d’un rôle web ou collaborateur dans Azure est 99,95 % (hors 100 pour cent). Il ne mesure pas les performances des services s’exécutant sur ces rôles. Toutefois, la disponibilité de votre service cloud efficace est également affectée par les différents accords des autres services dépendants. Les autres éléments au sein du système, le soin plus, que vous devez suivre pour garantir l’application peut élastique critères de disponibilité de ses utilisateurs finaux.

Prendre en compte les SLA suivantes pour un service Azure qui utilise des services Azure : calcul, base de données SQL Azure et le stockage Azure.

|Service Azure|SLA   |Potentiels minutes interruptions/mois (30 jours)|
|:------------|:-----|:----------------------------------------:|
|Cluster      |99,95 %|21,6 minutes                              |
|Base de données SQL |99,99 %|4.3 minutes                              |
|Espace de stockage      |99,90 %|43,2 minutes                              |

Vous devez planifier pour tous les services potentiellement descendre à des moments différents. Dans cet exemple simplifié, le nombre total de minutes par mois l’application peut être vers le bas est 108 minutes. Un mois de 30 jours comporte un total de 43,200 minutes. 108 minutes est.25 pour cent du nombre total de minutes dans un mois de 30 jours (43,200 minutes). Cela vous donne une disponibilité efficace de 99.75 % pour le service cloud.

Toutefois, à l’aide des techniques de disponibilité décrits dans ce document peut améliorer cette situation. Par exemple, si vous concevez votre application pour continuer à exécuter lors de la base de données SQL n’est pas disponible, vous pouvez supprimer qu’à partir de l’équation. Il peut que cela signifie que l’application est exécutée avec fonctionnalités réduites, il existe également des besoins de l’entreprise à prendre en compte. Pour obtenir une liste complète Azure niveaux de service requis, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Extensibilité élevées

Extensibilité du affecte directement la disponibilité. Une application qui échoue sous augmentation de la charge n’est plus disponible. Format SVG applications sont en mesure de répondre à la demande accrue avec des résultats cohérents dans windows de temps acceptable. Lorsqu’un système est format SVG, elle s’adapte horizontalement ou verticalement pour gérer les augmentations de charge tout en conservant la cohérence des performances. En termes de base, l’évolution horizontale ajoute plus de machines de la même taille (processeur, la mémoire et la bande passante), tout en vertical augmente mise à l’échelle la taille des ordinateurs existants. Pour Azure, vous disposez des options de mise à l’échelle verticales pour la sélection de différentes tailles machine pour le cluster. Toutefois, la modification de la taille de l’ordinateur requiert un ré déploiement. Par conséquent, les solutions plus flexibles sont conçues pour suivre l’évolution horizontale. Il s’agit particulièrement vrai pour le cluster, étant donné que vous pouvez facilement augmenter le nombre d’instances de n’importe quel rôle web ou de travail en cours d’exécution. Ces instances supplémentaires gérer une augmentation du trafic via le portail Web Azure, des scripts PowerShell ou code. Baser cette décision sur les augmentations d’indicateurs spécifiques. Dans ce scénario, performances de l’utilisateur ou métriques ne sont pas confrontées une baisse visible tendu. En règle générale, les rôles web et collaborateur stockent un état avec l’extérieur. Cela permet l’équilibrage de charge flexible et gestion progressive de toute modification apportée au nombre d’instance. Mise à l’échelle horizontale fonctionne également bien avec les services, tels que le stockage Azure, qui ne fournissent pas hiérarchisées options pour suivre l’évolution verticale.

Déploiements dans le cloud doivent être considérées comme une collection d’unités d’échelle. Cela permet à l’application à être élastique prise en charge des besoins débit des utilisateurs finaux. Les unités sont plus faciles à visualiser au niveau du serveur web et d’application. C’est parce que Azure fournit déjà des nœuds de calcul sans état par le biais des rôles web et de travail. Ajout que plus calculent des unités d’échelle pour le déploiement n’entraîne pas d’effets application état gestion secondaires, des unités d’échelle cluster étant sans état. Une unité d’échelle stockage est chargée de gérer une partition des données (structurées ou non structurées). Les exemples de stockage des unités d’échelle de Table Azure partition, conteneur Blob Azure et base de données SQL Azure. Même l’utilisation de plusieurs comptes de stockage Azure a un impact direct sur l’extensibilité élevées application. Vous devez concevoir un service cloud hautement scalable d’incorporer plusieurs stockage des unités d’échelle. Par exemple, si une application utilise des données relationnelles, diviser les données entre plusieurs bases de données SQL. Ainsi, le stockage au même niveau que le modèle d’unité d’échelle cluster élastique. De même, le stockage Azure permet partitions jeux nécessitant des conceptions délibérées en fonction des besoins de débit du calque cluster de données. Pour une liste des meilleures pratiques pour la conception des services cloud scalable, voir [Meilleures pratiques pour la création de Services à grande échelle sur Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###<a name="fault-tolerance"></a>Tolérance de panne

Applications considérez que chaque fonctionnalité cloud dépendantes pouvez passera vers le bas à un moment donné dans le temps. Une application à tolérance de panne détecte et manœuvres concurrentielles autour des éléments échecs, pour continuer et retourner des résultats corrects au sein d’une durée spécifique. Conditions d’erreur temporaire, d’un système à tolérance de panne emploie une stratégie de réessayer. Pour plus de graves erreurs, l’application peut détecter les problèmes et basculer vers un matériel de secours ou les plans d’urgence jusqu'à ce que le problème est résolu. Une application fiable peut correctement gérer l’échec d’une ou plusieurs parties et continuer à fonctionner correctement. Applications à tolérance de panne peuvent utiliser une ou plusieurs stratégies de conception, tels que redondance, réplication ou des fonctionnalités qui fonctionnement différemment.

##<a name="disaster-recovery"></a>Récupération d’urgence

Un déploiement de cloud peut cesser de fonctionner en raison d’une panne systémique des services dépendants ou l’infrastructure sous-jacente. Dans ces conditions, un plan continuité de service déclenche le processus de reprise. Ce processus implique généralement personnel des opérations et des procédures automatisées pour réactiver l’application dans une zone disponible. Cette fonctionnalité nécessite le transfert des utilisateurs de l’application, les données et services vers la nouvelle zone. Il implique également l’utilisation de supports de sauvegarde et de réplication en cours.

Pensez à l’exemple précédent qui comparés disponibilité la capacité à récupérer à partir d’une roue plate au moyen d’un disque de remplacement. En revanche, récupération d’urgence implique les étapes effectuées après une panne de voiture, où la voiture n’est plus opérationnelle. Dans ce cas, la meilleure solution consiste à trouver un moyen efficace de changer de voitures, en appelant un service de voyage ou à un ami. Dans ce scénario, il est probablement agir en un délai plu en récupérant en déplacement. Il est également plus complexe dans la réparation et de retour au véhicule d’origine. Dans la même manière, reprise dans une autre région est une tâche complexe qui implique généralement quelques temps d’arrêt et risque de perte de données. Pour mieux comprendre et évaluer les stratégies de récupération, il est important de définir les deux conditions : récupération heure objectifs et restauration, pointez sur l’objectif.

###<a name="recovery-time-objective"></a>Objectifs de temps

Le RTO est la durée maximale pendant laquelle allouée à la restauration des fonctionnalités de l’application. Ceci est basé sur les besoins de l’entreprise, et il est lié à l’importance de l’application. Applications métiers critiques nécessitent une faible RTO.

###<a name="recovery-point-objective"></a>Objectif de point de récupération

Le RPO est la fenêtre de temps acceptable de perte de données en raison du processus de récupération. Par exemple, si le RPO est une heure, vous devez complètement sauvegarder ou répliquer les données au moins toutes les heures. Une fois que vous faire apparaître l’application dans une autre zone, les données de sauvegarde peut-être manquantes jusqu'à une heure de données. Comme RTO, applications critiques ciblent un RPO beaucoup plus petit.

##<a name="checklist"></a>Liste de vérification

Nous allons résumer les points clés qui ont été traitées dans cet article (et ses articles supplémentaires sur la [disponibilité](./resiliency-high-availability-azure-applications.md) et [reprise](./resiliency-disaster-recovery-azure-applications.md) des applications Azure). Ce résumé servira à une liste de vérification des éléments que vous devez prendre en compte pour vos propres disponibilité et planification de la récupération d’urgence. Il s’agit des recommandations qui ont été utiles pour les clients qui souhaitent Devenez un implémentation d’une solution réussie.

1. Réaliser une analyse de risque pour chaque application, étant donné que chacun peut avoir des exigences différentes. Certaines applications sont plus critiques que d’autres personnes et susceptible de justifier le coût pour concevoir les sinistre supplémentaire.
1. Utilisez ces informations pour définir les RTO et RPO pour chaque application.
1. Création de l’échec, en commençant par l’architecture d’application.
1. Mettre en œuvre les meilleures pratiques de disponibilité, tout en équilibrant les coûts, la complexité et les risques.
1. Mettre en œuvre des plans de reprise et processus.
  * Envisagez d’échecs qui s’étalent sur le niveau de module tout à fait à une interruption de service cloud complète.
  * Établir des stratégies de sauvegarde pour tous les informations de référence et les données.
  * Choisissez une architecture de récupération après sinistre sur plusieurs sites.
1. Définir un propriétaire spécifique pour le processus de récupération d’urgence, automation et le test. Le propriétaire doit gérer et êtes propriétaire de l’ensemble du processus.
1. Documenter les processus afin qu’elles soient facilement répétitifs. Bien qu’un seul propriétaire, plusieurs personnes doivent être en mesure de comprendre et suivez les procédures en cas d’urgence.
1. Former le personnel pour implémenter le processus.
1. Utilisez simulations urgence normal de formation et validation du processus.

##<a name="summary"></a>Résumé

Lorsque matériel ou les applications échouent dans Azure, les techniques et stratégies pour les gérer sont différentes à lorsque échec a lieu sur les systèmes locaux. La principale raison est que solutions en nuage ont généralement plus de dépendances sur infrastructure répercuté sur une zone Azure, et géré en tant que services distinctes. Vous devez traiter échecs partielles à l’aide des techniques de disponibilité. Pour gérer plus sérieuses défaillances, éventuellement en raison d’un événement d’urgence, utilisez stratégies de récupération.

Azure détecte et gère les échecs répétés, mais il existe de nombreux types d’échecs qui nécessitent des stratégies spécifiques à l’application. Vous devez activement préparer et gérer les échecs d’applications, les services et les données.

Lorsque vous créez la disponibilité et le plan de récupération de votre application, envisagez les conséquences d’entreprise d’échec de l’application. Définition des processus, stratégies et procédures pour restaurer les systèmes critiques après qu’un sinistre prend du temps, planification et engagement. Et une fois que vous avez établi les offres, vous ne pouvez pas empêcher il. Vous devez analyser régulièrement, tester et améliorer les offres basés sur votre portefeuille d’applications, les besoins professionnels et les technologies disponibles pour vous. Azure fournit des nouvelles fonctionnalités et génère de nouveaux défis pour la création des applications fiables qui résistance échecs.

##<a name="additional-resources"></a>Ressources supplémentaires

[Disponibilité des applications basées sur Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Reprise des applications basées sur Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Conseils techniques résilience Azure](resiliency-technical-guidance.md)

[Vue d’ensemble : Cloud continuité et base de données reprise avec la base de données SQL](../sql-database/sql-database-business-continuity.md)

[Haute disponibilité et récupération après incident pour SQL Server dans le Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Sécurité intégrée : Conseils pour des architectures de cloud résistant](https://channel9.msdn.com/Series/FailSafe)

[Meilleures pratiques pour la conception de services à grande échelle sur les Services en nuage Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série d’articles axée sur sinistre et disponibilité des applications Azure. L’article suivant de cette série est [haute disponibilité des applications basées sur Microsoft Azure](resiliency-high-availability-azure-applications.md).
