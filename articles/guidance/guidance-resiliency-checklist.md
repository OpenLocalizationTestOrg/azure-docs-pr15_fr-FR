<properties
   pageTitle="Liste de vérification de la résilience | Microsoft Azure"
   description="Liste de vérification qui fournit des conseils pour ce qui concerne la résilience pendant la conception."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Conseils supplémentaires sur la résilience Azure : liste de vérification de la résilience

Conception de votre application la résilience des nécessite la planification et la réduction des divers modes de panne qui peut se produire. Passez en revue les éléments de cette liste de vérification par rapport à votre conception d’application pour le rendre plus résistant.

## <a name="requirements"></a>Configuration requise

- **Définir des exigences de disponibilité du client.** Votre client aura des exigences de disponibilité des composants dans votre application et cela détermineront la conception de votre application. Obtenir l’accord à partir de votre client pour les objectifs de disponibilité de chaque partie de votre application, sinon votre conception ne répondent pas aux attentes des clients. Pour plus d’informations, consultez la section [définition de vos besoins en matière de résilience](guidance-resiliency-overview.md#defining-your-resiliency-requirements) du document de [conception d’applications résistants pour Azure](guidance-resiliency-overview.md) .

## <a name="failure-mode-analysis"></a>Échec du Mode analyse

- **Effectuer une analyse de mode échec (FMA) pour votre application.** FMA est un processus pour la création de la résilience dans une application au début de la phase de conception. Les objectifs d’un FMA sont les suivantes :  

    - Identifiez les types d’échecs rencontrés une application. 
    - Capturer les effets et impact de chaque type d’échec de l’application.
    - Identifier les stratégies de récupération. 

    Pour plus d’informations, voir [conception d’applications résistants pour Azure : analyse de l’échec en mode][fma].  

## <a name="application"></a>Application

- **Déployer plusieurs instances de services.** Services échouera inévitablement, et si une seule instance d’un service dépend de votre application il inévitablement échouera également. Pour activer plusieurs instances pour [Azure Application Service](../app-service/app-service-value-prop-what-is.md), sélectionnez un [Plan de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) qui offre plusieurs instances. Pour les Services de Cloud Azure, configurez chacun des rôles d’utiliser [plusieurs instances](../cloud-services/cloud-services-choose-me.md#scaling-and-management). [Machines virtuelles Azure (machines virtuelles)](../virtual-machines/virtual-machines-windows-about.md), vérifiez que votre architecture machine virtuelle inclut plusieurs machine virtuelle et que chaque machine virtuelle est inclus dans une [disponibilité définie][availability-sets].   

- **Utiliser un programme d’équilibrage de charge pour distribuer les requêtes.** Un équilibrage de charge répartit les demandes de votre application sur des instances de service correct en supprimant des instances incorrectes de rotation. Si votre service utilise application Azure ou les Services de Cloud Azure, il est déjà équilibrer pour vous. Toutefois, si votre application utilise machines virtuelles Azure, vous devez fournir un équilibrage de charge. Afficher un aperçu [d’Équilibrage de charge Azure](../load-balancer/load-balancer-overview.md) pour plus d’informations. 

- **Configurer les passerelles d’Application Azure pour utiliser plusieurs instances.** En fonction des besoins de votre application, une [Passerelle d’Application Azure](../application-gateway/application-gateway-introduction.md) peut être mieux adapté à la distribution des demandes de services de votre application. Toutefois, seul instances du service passerelle d’Application ne sont pas garanties par accord, il est possible que votre application peut échouer cas d’échec de l’instance de passerelle d’Application. Mise en service de plusieurs instances de passerelle d’Application moyenne ou plus grandes afin de garantir la disponibilité du service selon les termes de la [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Utiliser des ensembles de disponibilité pour chaque niveau de l’application**. En plaçant vos instances dans une [disponibilité définie] [ availability-sets] garantit la connectivité au moins une instance de machine virtuelle dans le cadre de la [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Si vos ordinateurs virtuels n’apparaissent pas dans un jeu de disponibilité que vous n’avez pas garantit leur protection et il est possible qu’ils peuvent tous échouer ou être mis à jour simultanément. 

- **Pensez à déployer votre application dans plusieurs régions.** Si votre application est déployée sur une seule région, dans l’événement rare l’ensemble de la région n’est plus disponible, votre application également n’est pas disponible. Cela peut être inacceptable selon les termes du contrat SLA de votre application. Si c’est le cas, pensez à déployer votre application et ses services dans plusieurs régions. Un déploiement de plusieurs région peut utiliser un motif actif (répartir demandes plusieurs instances actives) ou un actif-passif (tout en conservant une instance de « chaude » en réserve, en cas d’échec de l’instance principale). Nous vous recommandons de déployer plusieurs instances de services de votre application sur paires régionaux. Pour plus d’informations, voir [entreprise continuité et récupération d’urgence (BCDR) : régions appariés Azure](../best-practices-availability-paired-regions.md).

- **Mettre en œuvre des motifs résilience pour les opérations distantes le cas échéant.** Si votre application dépend de la communication entre les services à distance, le chemin d’accès de communication inévitablement échouera. S’il existe plusieurs échecs, les autres instances exact de services de votre application pourraient dépassés avec les demandes. Il existe plusieurs modèles utile pour gérer les échecs courants, y compris le modèle de délai d’attente, [réessayez de motif]de la[retry-pattern], le [séparateur de circuit] [ circuit-breaker] motif et autres personnes. Pour plus d’informations, voir [conception d’applications résistants pour Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies). 

- **Utiliser autoscaling pour répondre aux augmentations de charge.** Si votre application n’est pas configurée pour évoluer automatiquement lors de la charge augmente, il est possible que les services de votre application échouera si ils deviennent saturés ayant des demandes d’utilisateur. Pour plus d’informations, voir les rubriques suivantes :

    - Général : [liste de vérification extensibilité élevées](../best-practices-scalability-checklist.md) 
    - Service d’application Azure : [nombre d’instances de l’échelle manuellement ou automatiquement][app-service-autoscale]
    - Services de cloud : [comment un service cloud à l’échelle automatique][cloud-service-autoscale]
    - Machines virtuelles : [définit automatique machine virtuelle et mise à l’échelle de l’échelle][vmss-autoscale]


- **Mise en œuvre d’opérations asynchrones dès que possible.** Opérations synchrones peuvent monopoliser les ressources et bloquer d’autres opérations tandis que l’appelant attend le processus se terminer. Concevoir chaque partie de votre application afin de permettre les opérations asynchrones dès que possible. Pour plus d’informations sur la mise en œuvre programmation asynchrone en c#, voir [programmation asynchrone avec asynchrone et attend][asynchronous-c-sharp].

- **Utilisez le Gestionnaire de trafic Azure pour acheminer le trafic de votre application pour différentes régions.**  [Gestionnaire de trafic Azure] [ traffic-manager] effectue au niveau du DNS d’équilibrage de charge et peuvent acheminer le trafic vers différentes régions basées sur le [routage du trafic] [ traffic-manager-routing] méthode que vous spécifiez et la santé des points de terminaison de votre application. 

- **Configurer et tester les tests d’intégrité pour vos programmes d’équilibrage de charge et les responsables du trafic.** Assurez-vous que votre logique d’intégrité vérifie les parties critiques du système et répond en conséquence santé sondes.

    - Teste l’intégrité [Azure le trafic Gestionnaire] [ traffic-manager] et [Équilibrage de charge Azure] [ load-balancer] remplissent une fonction spécifique. Pour le trafic Manager, la sonde santé détermine si vous voulez basculer vers une autre région. Pour un programme d’équilibrage de charge, il détermine si vous souhaitez supprimer une machine virtuelle de rotation.      

    - Pour une sonde le trafic Manager, votre point de terminaison d’intégrité doit vérifier toutes les dépendances critiques qui sont déployés au sein de la même région, et dont l’échec doit déclencher un basculement vers une autre région.  

    - Pour un programme d’équilibrage de charge, le point de terminaison d’intégrité doit signaler l’état de la machine virtuelle. N’incluez pas d’autres niveaux ou des services externes. Sinon, une défaillance qui apparaît à l’extérieur de la machine virtuelle va entraîner l’équilibrage de charge supprimer la machine virtuelle de rotation.

    - Pour des instructions sur l’implémentation de contrôle d’état dans votre application, voir [Intégrité du point de terminaison de surveillance motif](https://msdn.microsoft.com/library/dn589789.aspx).

- **Surveiller les services tiers.** Si votre application comporte des dépendances sur les services tiers, identifier l’emplacement où et comment ces services tiers peuvent échouer et les effets que ces échecs auront sur votre application. Un service tiers ne peut pas inclure de surveillance et les diagnostics, il est important de se connecter à vos appels d'entre eux et de les faire correspondre dont l’état de votre application et la journalisation à l’aide d’un identificateur unique. Pour plus d’informations sur les meilleures pratiques pour la surveillance et les diagnostics, consultez les [conseils de surveillance et les Diagnostics de] [ monitoring-and-diagnostics-guidance] document.

- **Assurez-vous que n’importe quel service tiers que vous consommez fournit accord.** Si un service tiers dépend de votre application, mais le tiers ne fournit aucune garantie de disponibilité sous la forme d’accord, disponibilité de votre application ne peut pas garantie. Votre SLA n’est aussi bien que le composant moins disponible de votre application.


## <a name="data-management"></a>Gestion des données

- **Comprendre les méthodes de réplication de sources de données de votre application.** Vos données d’application seront stockées dans différentes sources de données et ont des exigences différentes disponibles. Évaluer les méthodes de réplication pour chaque type de stockage des données dans Azure, y compris [La réplication du stockage Azure](../storage/storage-redundancy.md) et [SQL de base de données Active Geo réplication](../sql-database/sql-database-geo-replication-overview.md) pour vous assurer que les exigences de votre application sont satisfaites.

- **Vérifiez qu’aucune seul compte d’utilisateur n’a accès aux données de production et de sauvegarde.** Des sauvegardes de vos données sont compromis si un seul compte d’utilisateur dispose des autorisations nécessaires pour écrire dans production et sources de sauvegarde. Un utilisateur malveillant peut supprimer intentionnellement toutes vos données, pendant un utilisateur régulier pu accidentellement supprimez-le. Concevoir votre application pour limiter les autorisations de chaque compte d’utilisateur afin que seuls les utilisateurs qui nécessitent un accès en écriture ont un accès en écriture et il est uniquement de production ou sauvegarde, mais pas les deux.

- **Document votre source de données basculer et revenir traite et tester.** Dans le cas où votre source de données échoue inopinée, un opérateur humain doivent respecter un jeu d’instructions relatives à basculer vers une nouvelle source de données. Si les étapes documentation comportent des erreurs, un opérateur ne pourrez pas correctement suivez-les et basculer sur la ressource. Tester régulièrement les étapes d’instructions pour vérifier qu’un opérateur leur suite est en mesure de basculer correctement et basculer la source de données.

- **Valider des sauvegardes de vos données.** Vérifier régulièrement que vos données de sauvegarde soient ce que vous attendiez en exécutant un script pour valider les requêtes, de schéma et l’intégrité des données. Il n’existe aucun point ayant une sauvegarde s’il n’est pas utile restaurer vos sources de données. Connectez-vous et signaler les éventuelles incohérences détectées pour que le service de sauvegarde peut être réparé.

- **Envisagez d’utiliser un type de compte de stockage geo redondantes.** Données stockées dans un compte de stockage Azure sont toujours répliquées localement. Toutefois, il existe plusieurs stratégies de réplication pour choisir parmi lors d’un compte de stockage est mis en service. Sélectionner le [Stockage redondants Azure accès en lecture Geo (GRS RA)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) pour protéger vos données d’application contre la casse rare lorsqu’une région entière est plus disponible.

    > [AZURE.NOTE] Pour les machines virtuelles, ne comptez pas sur la réplication RA GRS restaurer les disques machine virtuelle (fichiers de disque dur virtuel). À la place, utilisez [Azure sauvegarde][azure-backup].   

## <a name="operations"></a>Opérations

- **Mise en œuvre d’analyse et d’alarme meilleures pratiques dans votre application.** Sans NOMPROPRE surveillance, diagnostics et d’alerte, il est inutile pour détecter les échecs dans votre application et signaler un opérateur pour les résoudre. Pour plus d’informations sur les meilleures pratiques, consultez les [instructions de surveillance et Diagnostics] [ monitoring-and-diagnostics-guidance] document.

- **Mesurer des statistiques d’appel distant et rendre les informations disponibles à l’équipe de l’application.**  Si vous ne le suivi et signaler les statistiques d’appel distant en temps réel et offrent un moyen simple de lire ces informations, l’équipe d’exploitation n’ont pas une vue instantanée dans l’état de votre application. Et si vous seulement mesurer moyenne des appels à distance le temps, vous n’avez pas suffisamment d’informations pour afficher des problèmes dans les services. Résumer les indicateurs d’appel distant tels que les erreurs dans les centiles 99 et 95 latence et débit. Effectuer des analyses statistiques sur les mesures pour identifier les erreurs qui se produisent dans chaque centile.

- **Suivre le nombre d’exceptions transitoires et nouvelles tentatives sur une période appropriée.** Si vous ne suivre et surveiller les exceptions transitoires et nouvelles tentatives au fil du temps, il est possible qu’un problème ou l’échec peut être masqué par la logique de nouvelle tentative de votre application. Autrement dit, si votre analyse et la journalisation uniquement indique la réussite ou l’échec d’une opération, le fait que l’opération devait être retentée plusieurs fois en raison des exceptions est masqué. Une tendance de l’augmentation des exceptions au fil du temps indique que le service a rencontré un problème et peut échouer. Pour plus d’informations, voir [Réessayer des recommandations spécifiques service][retry-service-guidance].

- **Mise en œuvre d’un système d’avertissement rapide qui alerte un opérateur.** Identifier les performances clés indicateurs d’état de votre application, tels que des exceptions transitoires et à distance latence d’appel et définir les valeurs seuils appropriées pour chacun d’eux. Envoyer une alerte aux opérations quand la valeur de seuil est atteint. Définissez ces seuils à des niveaux qui identifient les problèmes avant qu’ils deviennent critiques et exigent une réponse de récupération.

- **Documenter le processus de publication pour votre application.** Sans documentation du processus de lancement détaillé, un opérateur peut déployer une mise à jour incorrecte ou incorrectement configurer les paramètres de votre application. Définir clairement et votre processus des versions de document et ce qu’il soit disponible pour l’ensemble de l’équipe opérations. Meilleures pratiques pour le déploiement résistant de votre application qui sont en détail dans le [déploiement résistant] [ guidance-resilient-deployment] section du document résilience recommandations.

- **Vous assurer que plus d’une personne de l’équipe est formée pour contrôler l’application et effectuer les étapes de récupération manuelle.** Si vous disposez d’un seul opérateur de l’équipe qui peut contrôler l’application et démarrer des étapes de récupération, cette personne devient un point de défaillance unique. Former plusieurs personnes sur détection et récupération et assurez-vous qu’il existe toujours au moins une active à tout moment.

- **Automatiser le processus de déploiement de votre application.** Si votre équipe opérationnelle est requise pour déployer manuellement votre application, l’erreur humaine peut entraîner le déploiement échec. Pour plus d’informations sur les meilleures pratiques pour l’automatisation de déploiement de l’application, voir le [déploiement résistant] [ guidance-resilient-deployment] section du document résilience recommandations.

- **Concevoir votre processus de publication pour optimiser la disponibilité des applications.** Si votre processus de publication nécessite des services pour passer en mode hors connexion au cours du déploiement, votre application n’est pas disponible jusqu'à ce qu’ils sont en ligne. Utiliser la technique de déploiement [bleu/vert](http://martinfowler.com/bliki/BlueGreenDeployment.html) ou [Canaries release](http://martinfowler.com/bliki/CanaryRelease.html) pour déployer votre application en production. Ces deux techniques impliquent déploiement votre code de la version à côté de code de production afin que les utilisateurs de code de la version peuvent être redirigés vers le code de production en cas de panne. Pour plus d’informations, voir le [déploiement résistant] [ guidance-resilient-deployment] section du document résilience recommandations.

- **Journalisation et audit des déploiements de votre application.** Si vous utilisez transférées techniques de déploiement tels que les versions bleu/vert ou canary il y ait plus d’une version de votre application en cours d’exécution en production. Si un problème survient, il est essentiel de déterminer la version de votre application est à l’origine d’un problème. Mise en œuvre d’une stratégie de journalisation fiable pour capturer autant que possible des informations spécifiques à la version. 

- **Assurez-vous que votre application ne s’exécute pas respectivement [abonnement Azure limite](../azure-subscription-service-limits.md).** Abonnements Azure disposez limites sur certains types de ressources, telles que le nombre de groupes de ressources, le nombre de cœurs et le nombre de comptes de stockage.  Si les besoins de votre application dépassent la limite d’abonnements Azure, créer un autre Azure abonnement et mise en service des ressources suffisantes il.

- **Assurez-vous que votre application ne s’exécute pas respectivement [par service limite](../azure-subscription-service-limits.md).** Les différents services Azure disposent limites de consommation &mdash; par exemple, les limites de stockage, débit, le nombre de connexions de requêtes par seconde et autres mesures. Votre application échouera s’il tente d’utiliser des ressources au-delà de ces limites. Cela provoquera d’interruption de service possibles et la limitation pour les utilisateurs affectés. 

    Selon le service et les exigences de votre application, vous pouvez souvent éviter ces limites en le faisant (par exemple, en choisissant un autre niveau de tarification) ou horizontale (ajout de nouvelles instances).  

- **Créer des besoins de stockage de votre application pour être comprises dans les cibles de performances et extensibilité élevées stockage Azure.** Stockage Azure est conçu pour fonctionner dans cibles de performances et extensibilité élevées prédéfinies, vous devez concevoir votre application pour utiliser le stockage dans ces cibles. Si vous dépassez ces cibles votre application risque d’être la limitation de stockage. Pour résoudre ce problème, mise en service de stockage des comptes supplémentaires. Si vous exécutez respectivement la limite de compte de stockage, mise en service des abonnements supplémentaires Azure et puis mise en service des comptes de stockage. Pour plus d’informations, voir [extensibilité du stockage Azure et des objectifs de Performance](../storage/storage-scalability-targets.md).

- **Sélectionnez la taille de mémoire virtuelle appropriée pour votre application.** Mesurez l’UC réelles, mémoire, disque et e/s vos machines virtuelles en production et vérifiez que la taille de la mémoire virtuelle que vous avez sélectionné est suffisante. Dans le cas contraire, votre application peut rencontrer des problèmes de capacité lorsque les ordinateurs virtuels approchent de leurs limites. Taille de la mémoire virtuelle est décrites en détail dans le document de [taille pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) .

- **Déterminer si la charge de travail de votre application est stable ou fluctuante au fil du temps.** Si votre charge de travail varie au fil du temps, utilisez machine virtuelle Azure échelle définit sur une échelle d’automatiquement le nombre d’instances de machine virtuelle. Dans le cas contraire, vous devrez manuellement augmenter ou diminuer le nombre de machines virtuelles. Pour plus d’informations, consultez la [Vue d’ensemble des jeux de Machine virtuelle échelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

- **Sélectionnez le niveau de service approprié pour la base de données SQL Azure.** Si votre application utilise la base de données SQL Azure, assurez-vous que vous avez sélectionné le niveau de service approprié. Si vous sélectionnez un niveau qui n’est pas répondre aux exigences d’unité (DTU) de transaction de base de données de votre application, votre utilisation des données est limitée. Pour plus d’informations sur la sélection de l’offre de service correct, voir la [performances et options de base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](../sql-database/sql-database-service-tiers.md) document. 

- **Disposer d’un plan d’annulation pour le déploiement.** Il est possible que votre déploiement d’application peut échouer et provoquer votre application ne sont plus disponibles. Concevoir un processus de retour arrière pour revenir à une dernière bonne version connue et réduire les temps d’arrêt. Consultez le [déploiement résistant] [ guidance-resilient-deployment] section du document résilience conseils pour plus d’informations. 

- **Créer un processus pour interagir avec Azure prise en charge.** Si le processus permettant de contacter le [support Azure](https://azure.microsoft.com/support/plans/) n’est pas défini avant la nécessité contacter le support technique, temps d’arrêt sera prolongée comme cible de la navigation pour la première fois le processus de prise en charge. Inclure le processus permettant de contacter prise en charge et transmission des problèmes dans le cadre de la résilience de votre application dès le début.

- **Assurez-vous que votre application n’utilise pas plus que le nombre maximal de comptes de stockage par abonnement.** Azure autorise un maximum de 200 comptes de stockage par abonnement. Si votre application requiert plus de comptes de stockage que ne sont actuellement disponibles dans votre abonnement, vous devez créer un nouvel abonnement et créer des comptes d’espace de stockage supplémentaire il. Pour plus d’informations, voir [abonnement Azure et les limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md#storage-limits).

- **Assurez-vous que votre application ne dépasse pas les cibles extensibilité élevées pour machine virtuelle disques.** Un ordinateur virtuel IaaS Azure prend en charge en joignant un certain nombre de disques de données en fonction de plusieurs facteurs, notamment la taille de mémoire virtuelle et le type de compte de stockage. Si votre application dépasse les cibles extensibilité élevées des disques machine virtuelle, mise en service des comptes d’espace de stockage supplémentaire et créer les disques machine virtuelle il. Pour plus d’informations, voir [cibles de performances et extensibilité du stockage Azure] (… / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Test

- **Effectuer le basculement et restauration de votre application.** Si vous n’avez pas entièrement testé basculement et restauration, vous ne peut pas être sûr que les services dépendants dans votre application sont revenir vers le haut de manière synchronisée au cours de sinistre. Assurez-vous que dépendant de votre application services basculement et fail dans l’ordre correct.

- **Effectuer des tests pour votre application injection d’erreurs.** Votre application peut échouer pour différentes raisons, par exemple d’expiration du certificat, épuisement des ressources système dans une machine virtuelle ou défaillances de stockage. Tester votre application dans un environnement aussi proche que possible de production, par simulation ou défaillances réels de déclenchement. Par exemple, supprimer les certificats, artificiellement utilisent des ressources système ou supprimer une source de stockage. Vérifier les capacités de votre application pour récupérer à partir de tous les types de défauts, seuls et en combinaison. Vérifier que les échecs ne sont pas se propager ou en cascade sur votre système.

- **Exécuter les tests de production à l’aide des données utilisateur synthétique et réel.** Test et production sont rarement identiques, il est important utiliser bleu/vert ou un déploiement canary et tester votre application en production. Ainsi, vous permettent de tester votre application en production en charge réel et vous assurer qu’il fonctionnera comme prévu lorsque totalement déployée.

## <a name="security"></a>Sécurité

- **Mise en œuvre au niveau de l’application de protection contre distribué attaques de refus de service (DDoS).** Services Azure sont protégées contre les attaques DDos au niveau du réseau. Toutefois, Azure ne peut pas protéger contre les attaques de couches d’application, car il est difficile à faire la distinction entre les demandes d’utilisateur true à partir de demandes d’utilisateurs malveillants. Pour plus d’informations sur la protection contre les attaques DDoS couches d’application, consultez la section « Protection contre DDoS » de la [Sécurité du réseau Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (téléchargement PDF).

- **Mettre en œuvre le principe du moindre privilège pour l’accès aux ressources de l’application.** La valeur par défaut pour l’accès aux ressources de l’application doit être aussi restrictif que possible. Accorder des autorisations de niveau supérieur sur une base de l’approbation. Accorder l’accès trop permissif aux ressources de votre application par défaut peut entraîner une personne intentionnellement ou accidentellement suppression de ressources. Azure fournit [un contrôle d’accès basé sur un rôle](../active-directory/role-based-access-built-in-roles.md) pour gérer les privilèges d’utilisateur, mais il est important de vérifier les autorisations de privilèges minimum pour d’autres ressources qui ont leurs propres systèmes autorisations telles que SQL Server. 

## <a name="telemetry"></a>Télémétrie

- **Enregistrer les données de télémétrie pendant l’exécution de l’application dans l’environnement de production.** Capturer les informations de télémétrie robuste lors de l’application est en cours d’exécution dans l’environnement de production ou vous n’aurez pas de suffisamment d’informations pour diagnostiquer l’origine des problèmes pendant qu’il est occupé par les utilisateurs. Informations supplémentaires sont disponibles dans la journalisation meilleures pratiques est disponible dans les [instructions de surveillance et Diagnostics] [ monitoring-and-diagnostics-guidance] document.

- **Mettre en œuvre la journalisation à l’aide d’un modèle asynchrone.** Si les opérations de journalisation sont synchrones, il peut se bloquer votre code de l’application. Vous assurer que vos opérations de journalisation sont implémentées comme des opérations asynchrones. 

- **Faire correspondre les données du journal au-delà des limites de service.** Dans une application multicouches classique, une demande de l’utilisateur peut parcourir plusieurs frontières des services. Par exemple, une demande de l’utilisateur provient généralement dans la couche web est passée à la couche d’entreprise et enfin conservée dans la couche de données. Dans les scénarios plus complexes, une demande de l’utilisateur peut être distribuée à différents magasins services et des données. Assurez-vous que votre système de journalisation corrélation appels au-delà des limites de service pour suivre la demande dans l’ensemble de votre application.

##  <a name="azure-resources"></a>Ressources Azure 

- **Utiliser des modèles Azure le Gestionnaire de ressources vers des ressources de mise en service.** Modèles de gestionnaire de ressources automatiser les déploiements via PowerShell ou de l’infrastructure du langage commun Azure, qui permet d’accéder à un processus de déploiement plus fiable plus facilement. Pour plus d’informations, voir [Présentation du Gestionnaire de ressources Azure][resource-manager].

- **Donnez des noms parlants à ressources.** Donner des noms significatifs aux ressources simplifie localiser une ressource spécifique et comprendre son rôle. Pour plus d’informations, voir [recommandé conventions d’appellation des ressources Azure](guidance-naming-conventions.md) 

- **Utiliser le contrôle d’accès basé sur un rôle (RBAC)**. RBAC permet de contrôler l’accès aux ressources Azure que vous déployez. RBAC vous permet d’affecter des rôles d’autorisations aux membres de votre équipe DevOps, pour empêcher toute suppression accidentelle ou des modifications aux ressources déployées. Pour plus d’informations, voir [prise en main la gestion des accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md) 

- **Utiliser les verrouillages des ressources pour les ressources critiques, tels que des machines virtuelles.** Verrouillages des ressources empêchent un opérateur de suppression accidentelle d’une ressource. Pour plus d’informations, consultez [ressources de verrouillage avec le Gestionnaire de ressources Azure](../resource-group-lock-resources.md) 

- **Paires régionaux.** Lorsque vous déployez à deux régions, choisissez régions dans la même paire régionale. En cas de panne large, récupération d’une région est prioritaire se déconnecter de toutes les paires. Certains services tels que stockage Geo redondants fournissent la réplication automatique à la région appariée. Pour plus d’informations, voir [entreprise continuité et récupération d’urgence (BCDR) : Azure appariés régions](../best-practices-availability-paired-regions.md) 

- **Organiser les groupes de ressources par fonction et du cycle de vie**.  En règle générale, un groupe de ressources doit contenir des ressources qui partagent le même cycle de vie. Cela rend plus facile à gérer les déploiements, supprimez les déploiements de test et affecter des droits d’accès, réduire les risques qu’un déploiement de production est accidentellement supprimé ou modifié. Créer des groupes de ressources distincts pour production, développement et environnements de test. Dans un déploiement de plusieurs région, placez ressources pour chaque région dans les groupes de ressources distincts. Cela facilite la redéployez une région sans affecter l’ou les autres régions. 

## <a name="azure-services"></a>Services Azure 

Les éléments de liste de vérification suivantes s’appliquent aux services spécifiques dans Azure.

###  <a name="app-service"></a>Service d’application 

- **Utiliser la couche Standard ou Premium.** Ces niveaux de prise en charge des emplacements de mise en attente et sauvegardes automatisées. Pour plus d’informations, voir [vue d’ensemble détaillée de Service d’application Azure d’offre](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Évitez de mise à l’échelle vers le haut ou vers le bas.** Au lieu de cela, sélectionnez un niveau et taille d’instance qui répondent à vos besoins en matière de performances sous charge classique, puis [mise à l’échelle](../app-service-web/web-sites-scale.md) les instances de gérer les modifications dans le volume de trafic. Mise à l’échelle monter et Descendre peut déclencher le redémarrage de l’application.  

- **Configuration de la banque en tant que paramètres de l’application.** Utiliser les paramètres de l’application pour stocker les paramètres de configuration en tant que paramètres de l’application. Définir les paramètres de votre gestionnaire de ressources des modèles ou à l’aide de PowerShell, afin que vous pouvez les appliquer dans le cadre d’un déploiement automatisé / des mises à jour, qui est plus fiable. Pour plus d’informations, voir [Configuration des applications web dans le Service d’application Azure](../app-service-web/web-sites-configure.md). 

- **Créer des plans application Service distinctes de production et de test.** N’utilisez pas emplacements dans votre déploiement de production pour le test.  Toutes les applications au sein de la même offre de Service d’application partagent les mêmes instances machine virtuelle. Si vous stockez les déploiements de test et de production dans le même plan, il peut nuire au déploiement en production. Par exemple, les tests de charge peuvent se dégrader le site de production en direct. En plaçant les déploiements de test dans un plan distinct, vous isoler à partir de la version de production.  

- **Applications web distinctes à partir du web API**. Si votre solution possède un site web frontaux et un site web API, vous pouvez décomposer les applications de Service d’application distincte. Cette conception facilite la décomposer la solution par la charge de travail. Vous pouvez exécuter l’application web et l’API dans les plans de Service d’application distincts, afin qu’ils peuvent être mis à l’échelle séparément. Si vous n’avez pas besoin ce niveau d’extensibilité élevées en tout d’abord, vous pouvez déployer les applications dans le même plan et les déplacer dans des plans distincts plus tard, si nécessaire.

- **Évitez d’utiliser la fonctionnalité de sauvegarde de Service d’application pour sauvegarder des bases de données SQL Azure.** À la place, utilisez [sauvegardes automatisées de base de données SQL][sql-backup]. Sauvegarde application Service exporte la base de données dans un fichier de .bacpac SQL, car il coûte DTUs.  

- **Déployer vers un emplacement intermédiaire.** Créer un emplacement de déploiement de mise en attente. Déployer des mises à jour de l’application à l’emplacement intermédiaire, puis vérifiez le déploiement avant inversion en production. Cela permet de réduire les risques d’une mise à jour incorrecte en production. Elle garantit également que toutes les instances sont échauffés avant échangées en production. De nombreuses applications ont une préparation significative et l’heure de début de froid. Pour plus d’informations, voir [configurer la mise en environnements pour les applications web dans le Service d’application Azure](../app-service-web/web-sites-staged-publishing.md). 

-  **Créer un emplacement de déploiement pour mettre en attente le déploiement (connue) dernier vérifié.** Lorsque vous déployez une mise à jour en production, déplacez le déploiement de production précédent dans l’emplacement connue. Cela facilite la restaurer un déploiement incorrect. Si vous rencontrez un problème plus tard, vous pouvez rapidement revenir à la version connue. Pour plus d’informations, voir [architecture de référence Azure : application web base](guidance-web-apps-basic.md). 

- **Activer la journalisation des diagnostics**, y compris la journalisation de l’application et journalisation d’un serveur web. La journalisation est importante pour la surveillance et les diagnostics. Consultez [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Journal de stockage blob**. Cela facilite collecter et analyser les données. 

- **Créer un compte de stockage séparé pour les journaux.** N’utilisez pas le même compte de stockage pour les journaux et les données d’application. Cela permet d’empêcher la journalisation la réduction des performances de l’application. 

- **Surveiller les performances.** Utilisez un service tel que [Nouveau Relic](http://newrelic.com/) ou [Application perspectives](../application-insights/app-insights-overview.md) pour Analyseur de performances application et son comportement sous charge d’analyse des performances.  Analyse des performances vous donne un aperçu en temps réel dans l’application. Il vous permet de diagnostiquer les problèmes et effectuer une analyse des causes des échecs. 


###  <a name="application-gateway"></a>Passerelle d’application 

- **Mise en service au moins deux instances.** Déploiement d’Application passerelle avec au moins deux instances. Une instance unique est un point de défaillance unique. Utiliser deux ou plusieurs instances de redondance et extensibilité élevées. Pour prétendre pour le [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), vous devez configurer deux ou plusieurs instances plus grandes ou moyennes. 

### <a name="azure-search"></a>Recherche Azure

- **Mise en service de plusieurs réplica.** Utiliser des doubles au moins deux pour lecture haute disponibilité ou trois pour disponibilité en lecture / écriture.

- **Configurer les indexeurs pour les déploiements de plusieurs régions.** Si vous avez un déploiement de plusieurs région, envisagez les options disponibles pour la continuité d’indexation.

    - Si la source de données est répliquée geo, pointez sur chaque indexeur de chaque service de recherche Azure régionaux son réplica de source de données locales.  

    - Si la source de données n’est pas répliquée geo, pointez sur plusieurs indexeurs à la même source de données, afin que les services de recherche Azure dans plusieurs régions en permanence et indépendamment indexer à partir de la source de données. Pour plus d’informations, voir [Considérations de performances et d’optimisation Azure recherche][search-optimization].

###  <a name="azure-storage"></a>Stockage Azure 

- **Données d’application, utilisez le stockage de geo redondants accès en lecture (RA GRS).** Stockage RA GRS réplique les données à une zone secondaire et offre un accès en lecture seule à partir de la zone secondaire. S’il existe une panne de stockage dans la région principale, l’application peut lire les données à partir de la zone secondaire. Pour plus d’informations, voir [la réplication du stockage Azure](../storage/storage-redundancy.md).

- **Disques pour machine virtuelle, utilisez le stockage Premium** Pour plus d’informations, voir [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md).

- **Pour le stockage de file d’attente, créez une sauvegarde file d’attente dans un autre région.** Espace de stockage file d’attente, une copie en lecture seule s’en trouve limité, car vous ne pouvez pas en file d’attente ou retirer des éléments. À la place, créez une sauvegarde file d’attente dans un compte de stockage dans une autre zone. S’il existe une panne de stockage, l’application peut utiliser la file d’attente sauvegarde, jusqu'à ce que la zone primaire redevient disponible. De cette façon, l’application peut toujours traiter les nouvelles requêtes.  

###  <a name="documentdb"></a>DocumentDB 

- **Répliquer la base de données au sein de régions.** Avec un compte plusieurs région, votre base de données DocumentDB a une opération d’écriture région et plusieurs zones en lecture. S’il existe une défaillance dans la région d’écriture, vous pouvez lire à partir d’un autre réplica. Le Kit de développement logiciel Client gère cette valeur automatiquement. Vous pouvez également basculer vers la zone d’écriture dans une autre région. Pour plus d’informations, voir [données répartir globalement avec DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>Base de données SQL 

- **Utiliser la couche Standard ou Premium.** Ces niveaux proposent une période plus point-à-temps restaurer (35 jours). Pour plus d’informations, voir [options de base de données SQL et les performances](../sql-database/sql-database-service-tiers.md).

- **Activer l’audit de base de données SQL.** Audit peut être utilisé pour identifier les attaques ou les erreurs humaines. Pour plus d’informations, voir [prise en main d’audit de base de données SQL](../sql-database/sql-database-auditing-get-started.md). 

- **Utiliser la Geo-réplication Active** Geo-réplication Active permet de créer un secondaire lisible dans une zone différente.  Si votre base de données principale échoue, ou simplement doit être mis hors connexion, effectuez un basculement manuel à la base de données secondaire.  Jusqu'à ce que vous le basculement, la base de données secondaire reste en lecture seule.  Pour plus d’informations, voir [SQL de base de données Active Geo réplication](../sql-database/sql-database-geo-replication-overview.md). 

- **Ont utilisation**. Envisagez d’utiliser ont à la base de données de partition horizontalement. Ont peut fournir des pannes. Pour plus d’informations, voir [mise à l’échelle avec la base de données SQL Azure](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Restauration de point-à-temps permet de récupérer par une erreur humaine.**  Restauration de point-à-temps retourne votre base de données à un point antérieur dans le temps. Pour plus d’informations, voir [récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées][sql-restore].

- **Geo-restaurer permet de récupérer une interruption de service.** Geo-restaurer restaure une base de données à partir d’une sauvegarde geo redondants.  Pour plus d’informations, voir [récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (en cours d’exécution dans une machine virtuelle)

- **Répliquer la base de données.** SQL Server toujours sur les groupes de disponibilité permet de répliquer la base de données. Fournit une disponibilité élevée si Échec d’une instance de SQL Server. Pour plus d’informations, voir [plus d’informations...](guidance-compute-n-tier-vm.md) 

- **Sauvegarder la base de données**. Si vous utilisez déjà [Sauvegarde Azure](https://azure.microsoft.com/documentation/services/backup/) pour sauvegarder vos ordinateurs virtuels, envisagez d’utiliser [Sauvegarde Azure pour les charges de travail de SQL Server à l’aide de DPM](../backup/backup-azure-backup-sql.md). Avec cette approche, il existe un seul rôle d’administrateur secondaire pour l’organisation et une procédure de récupération unifiée des machines virtuelles SQL Server. Dans le cas contraire, utilisez [Sauvegarde SQL Server gérés à Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Gestionnaire de trafic 

- **Effectuer le retour arrière manuel.** Après un basculement le trafic Manager, effectuer retour arrière manuel, plutôt que d’automatiquement ignore précédent. Avant l’échec précédent, vérifiez que tous les sous-systèmes d’application fonctionnent correctement.  Dans le cas contraire, vous pouvez créer une situation où l’application retourne en arrière entre centres de données. Pour plus d’informations, voir [Machines virtuelles en cours d’exécution dans plusieurs régions](guidance-compute-multiple-datacenters.md).

- **Créer un point de terminaison sonde d’intégrité**. Créer un point de terminaison personnalisé qui indique l’état général de l’application. Cela permet au Gestionnaire de trafic basculer en cas d’échec de n’importe quel chemin critique, pas seulement le serveur frontal. Le point de terminaison doit renvoyer un code d’erreur HTTP si toute dépendance critique est incorrecte ou inaccessible. Ne pas signaler les erreurs dans les services non critiques, toutefois. Dans le cas contraire, la sonde santé peut déclencher le basculement lorsqu’elle n’est pas nécessaire, création fausses. Pour plus d’informations, voir [Gestionnaire de trafic point de terminaison surveillance et basculement](../traffic-manager/traffic-manager-monitoring.md).


###  <a name="virtual-machines"></a>Machines virtuelles 

- **Évitez d’exécuter un travail de production sur un ordinateur virtuel unique.** Déploiement d’un seul ordinateur virtuel n’est pas résistant à la maintenance planifiée ou non. À la place, placez plusieurs machines virtuelles dans un jeu de disponibilité ou un ensemble d’échelle machine virtuelle, avec un équilibrage de charge au premier plan. Pour prétendre pour le contrat SLA, au moins deux Machines virtuelles doit être déployé dans le même jeu de disponibilité. Pour plus d’informations, voir [Vue d’ensemble des jeux de Machine virtuelle échelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 

- **Spécifier la disponibilité définie lorsque vous configurez la machine virtuelle.** Actuellement, il n’existe aucun moyen d’ajouter un gestionnaire de ressources VM à disponibilité définie une fois la machine virtuelle est mis en service. Lorsque vous ajoutez une nouvelle machine virtuelle pour une disponibilité existante définir, veillez à créer une carte réseau pour la machine virtuelle et ajouter la carte réseau à la liste principale adresse sur l’équilibrage de charge. Dans le cas contraire, l’équilibrage de charge ne route le trafic réseau à cette machine virtuelle. 

- **Placez chaque niveau de l’application dans un jeu de disponibilité distincte.** Dans une application multicouches, ne placez machines virtuelles à partir de différents niveaux dans le même jeu de disponibilité. Machines virtuelles dans un jeu de disponibilité sont placés entre des domaines de défaillance (FDs) et mettre à jour des domaines (UD). Toutefois, pour obtenir le bénéfice redondance des FDs et UDs, chaque machine virtuelle dans l’ensemble de disponibilité doit être en mesure de gérer les demandes des clients même. 

- **Sélectionnez la taille de mémoire virtuelle droite en fonction des besoins de performances.** Lorsque vous déplacez une charge de travail existant vers Azure, précédez la taille de la mémoire virtuelle qui correspond le plus proche à vos serveurs locale. Mesurer les performances de votre charge de travail réel en ce qui concerne l’UC, la mémoire et disque sorties par, puis ajustez la taille si nécessaire. Cela permet de garantir que l’application se comporte comme prévu dans un environnement cloud. En outre, si vous avez besoin de plusieurs cartes réseau, sachez de la limite de carte réseau pour chaque taille. 

- **Utilisez le stockage premium pour disques durs virtuels.** Stockage Premium Azure prend en charge de disque hautes performances et à faible latence. Pour plus d’informations, voir [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md) choisir une taille de mémoire virtuelle qui prend en charge le stockage premium. 

- **Créer un compte de stockage distincte pour chaque machine virtuelle.** Placez les disques durs virtuels pour une machine virtuelle dans un compte de stockage distinct. Cela permet d’éviter de passer les limites sorties par pour les comptes de stockage. Pour plus d’informations, voir [extensibilité du stockage Azure et des objectifs de Performance](../storage/storage-scalability-targets.md). Toutefois, si vous déployez un grand nombre de machines virtuelles, sachez de la limite par abonnement pour les comptes de stockage. Voir les [limites de stockage](../azure-subscription-service-limits.md#storage-limits).

- **Créer un compte de stockage distincts pour les journaux de diagnostic**. Les journaux de diagnostic d’écriture pour le même compte de stockage que les disques durs virtuels, afin d’éviter que la journalisation des diagnostics n’affectent la sorties par pour les disques machine virtuelle. Un compte de stockage localement redondantes (LRS) standard est suffisant pour les journaux de diagnostic. 

- **Installer des applications sur un disque de données, n’est pas le disque du système d’exploitation.** Dans le cas contraire, vous risquez d’atteindre la limite de taille de disque. 

- **Utiliser Azure sauvegarde pour sauvegarder des machines virtuelles.** Effectuer des sauvegardes régulières protègent contre la perte de données accidentelle. Pour plus d’informations, voir [Protéger des machines virtuelles Azure avec un archivage sécurisé des services de récupération](../backup/backup-azure-vms-first-look-arm.md).

- **Activer les journaux de diagnostic**, y compris les mesures de fonctionnement de base, les journaux d’infrastructure et les [diagnostics de démarrage][boot-diagnostics]. Démarrage diagnostics peuvent vous aider à identifier un échec de démarrage si votre ordinateur virtuel Obtient dans un état non démarrage. Pour plus d’informations, voir [Vue d’ensemble de Azure les journaux de Diagnostic][diagnostics-logs].

- **Utiliser l’extension AzureLogCollector**. (Windows machines virtuelles uniquement). Cette extension regroupe les journaux de plateforme Azure et les transfère vers le stockage Azure, sans l’opérateur journalisation à distance de la machine virtuelle. Pour plus d’informations, voir [AzureLogCollector Extension](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Réseau virtuel 

- **Liste d’autorisation ou un bloc les adresses IP publiques, ajouter un NSG au sous-réseau.** Bloquer l’accès des utilisateurs malveillants, ou autoriser l’accès uniquement à partir d’autres utilisateurs qui possèdent des privilèges pour accéder à l’application.  

- **Créer une sonde d’état personnalisé.** Charge équilibrage santé teste pouvez tester HTTP ou TCP. Si une machine virtuelle s’exécute un serveur HTTP, la sonde HTTP est un meilleur indicateur d’état à une sonde TCP. Pour une sonde HTTP, utilisez un point de terminaison personnalisé qui indique l’état général de l’application, y compris toutes les dépendances critiques. Pour plus d’informations, voir [vue d’ensemble de l’équilibrage de charge Azure](../load-balancer/load-balancer-overview.md).

- **Ne pas bloquer la sonde d’intégrité.** La sonde santé d’équilibrage de charge est envoyée à partir d’une adresse IP connue, 168.63.129.16. Ne pas bloquer le trafic vers ou à partir de cette adresse IP dans les stratégies de pare-feu ou les règles de groupe (NSG) de sécurité de réseau. Bloquer la sonde santé provoque l’équilibrage de charge supprimer la machine virtuelle de rotation. 

- **Activer la journalisation pour équilibrage de charge.** Les journaux indiquent combien machines virtuelles sur le serveur principal ne reçoivent pas le trafic réseau en raison de réponses sonde a échoué. Pour plus d’informations, voir [analytique journal pour équilibrage de charge Azure](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
