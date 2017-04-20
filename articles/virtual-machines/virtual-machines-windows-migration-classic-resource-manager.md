<properties
    pageTitle="Migration de plateforme prise en charge des ressources IaaS de classique au Gestionnaire de ressources Azure | Microsoft Azure"
    description="Cet article décrit la migration plateforme prise en charge des ressources de classique au Gestionnaire de ressources Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migration de plateforme prise en charge des ressources IaaS de classique au Gestionnaire de ressources Azure

Dans cet article, nous décrivent comment nous allons permettant la migration d’infrastructure par un service (IaaS) ressources à partir de la classique aux modèles de déploiement Gestionnaire de ressources. Vous pouvez en savoir plus sur [le Gestionnaire de ressources Azure fonctionnalités et avantages](../azure-resource-manager/resource-group-overview.md). Nous en détail comment connecter des ressources à partir des modèles de deux déploiement coexister dans votre abonnement à l’aide de passerelles de site à réseau virtuel. 

## <a name="goal-for-migration"></a>Objectif pour la migration

Gestionnaire de ressources permet de déployer des applications complexes grâce à des modèles, configure machines virtuelles à l’aide des extensions de machine virtuelle et incorpore la gestion de l’accès et le marquage. Le Gestionnaire de ressources Azure inclut le déploiement format SVG, en parallèle pour les machines virtuelles en ensembles de disponibilité. Le nouveau modèle de déploiement fournit également gestion du cycle de vie de calcul, de réseau et de stockage séparément. Enfin, il existe un focus sur l’activation de sécurité par défaut avec la mise en œuvre des machines virtuelles dans un réseau virtuel.

Presque toutes les fonctionnalités à partir du modèle de déploiement classique sont prises en charge pour le cluster, de réseau et de stockage sous le Gestionnaire de ressources Azure. Pour bénéficier de nouvelles fonctionnalités dans le Gestionnaire de ressources Azure, vous pouvez migrer les déploiements existants à partir du modèle de déploiement classique.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifications apportées à votre automation et outils après la migration

Dans le cadre de la migration de vos ressources à partir du modèle de déploiement classique vers le modèle de déploiement du Gestionnaire de ressources, vous devez mettre à jour votre automation existante ou outils pour vous assurer qu’il continue de fonctionner après la migration.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Sens de migration des ressources IaaS de classique au Gestionnaire de ressources

Avant de nous Explorer les détails, examinons la différence entre des données-plan et plan de gestion des opérations sur les ressources IaaS.

- *Plan de gestion* décrit les appels fournis dans le plan de gestion ou de l’API pour la modification des ressources. Par exemple, opérations, telles que la création d’une machine virtuelle, le redémarrage d’une machine virtuelle et mise à jour un réseau virtuel avec un nouveau sous-réseau gérer les ressources en cours d’exécution. Ils ne concernent pas directement se connecter aux instances.
- *Plan de données* (application) décrit l’exécution de l’application elle-même et implique une interaction avec les instances qui ne passent pas l’API Azure. Accéder à votre site Web ou extraire des données d’exécution d’une instance de SQL Server ou un serveur MongoDB est considérée comme données interaction plan ou de l’application. Copier un blob à partir d’un compte de stockage et l’accès à une adresse IP publique RDP ou SSH dans la machine virtuelle sont également plan de données. Ces opérations conserver l’application en cours d’exécution sur cluster, mise en réseau et de stockage.

>[AZURE.NOTE] Dans certains scénarios de migration, la plateforme Azure s’arrête, libère et redémarre vos machines virtuelles. Cela entraîne une courte interruption de plan de données.

## <a name="supported-scopes-of-migration"></a>Portées prises en charge de la migration

Il existe trois étendues de migration qui visent principalement informatiques, de réseau et de stockage. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migration des machines virtuelles (et non dans un réseau virtuel)

Dans le modèle de déploiement Gestionnaire de ressources, la sécurité est appliquée à vos applications par défaut. Tous les ordinateurs virtuels doivent être dans un réseau virtuel dans le modèle de gestionnaire de ressources. Le redémarrage de la plateforme Azure (`Stop`, `Deallocate`, et `Start`) les ordinateurs virtuels dans le cadre de la migration. Vous avez deux possibilités pour les réseaux virtuels :

- Vous pouvez demander la plateforme pour créer un nouveau réseau virtuel et migrer la machine virtuelle vers le réseau virtuel.
- Vous pouvez migrer la machine virtuelle dans un réseau virtuel existant dans le Gestionnaire de ressources.

>[AZURE.NOTE] Dans cette portée de migration, les opérations de plan de gestion et les opérations de plan de données ne pourra être pour une période donnée lors de la migration.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migration des machines virtuelles (dans un réseau virtuel)

Pour la plupart des configurations machine virtuelle, seules les métadonnées migre entre les modèles de déploiement classique et Gestionnaire de ressources. Les ordinateurs virtuels sous-jacentes sont en cours d’exécution sur le même matériel, dans le même réseau et avec le même emplacement de stockage. Les opérations de plan de gestion ne pourra être pour une certaine période de temps lors de la migration. Toutefois, le plan de données continue de fonctionner. Autrement dit, vos applications en cours d’exécution en haut des machines virtuelles (classiques) n’entraînent pas de temps d’arrêt lors de la migration.

Les configurations suivantes ne sont actuellement pas pris en charge. Si la prise en charge est ajoutée à l’avenir, certaines machines virtuelles dans cette configuration peut subir des interruptions de service (accédez via taquet, libérer, puis redémarrez opérations machine virtuelle).

-   Vous avez plusieurs disponibilité définie dans un service cloud unique.
-   Vous avez un ou plusieurs jeux de disponibilité et machines virtuelles qui ne sont pas dans une disponibilité définie dans un service cloud unique.

>[AZURE.NOTE] Dans cette portée de migration, le plan de gestion ne pourra être pour une période donnée lors de la migration. Pour certaines configurations comme décrit plus haut, temps d’arrêt du plan de données se produit.

### <a name="storage-accounts-migration"></a>Migration des comptes de stockage

Pour permettre à une migration transparente, vous pouvez déployer des machines virtuelles du Gestionnaire de ressources dans un compte de stockage classique. Avec cette fonctionnalité, ressources informatiques et réseau peuvent et doivent être migrés indépendamment des comptes de stockage. Une fois la migration sur votre Machines virtuelles et des réseaux virtuels, vous devez migrer vos comptes de stockage pour terminer le processus de migration. 

>[AZURE.NOTE] Le modèle de déploiement du Gestionnaire de ressources n’a pas le concept d’images standard et des disques. Lorsque le compte de stockage correspond à des images a été déplacés, classiques et disques ne sont pas visibles dans la pile Gestionnaire de ressources, mais la sauvegarde disques durs virtuels restent dans le compte de stockage. 

## <a name="unsupported-features-and-configurations"></a>Configurations et fonctionnalités non prises en charge

Nous ne pas actuellement en charge des fonctionnalités et des configurations. Les sections suivantes décrivent nos recommandations autour d’eux.

### <a name="unsupported-features"></a>Fonctionnalités non prises en charge

Les fonctionnalités suivantes ne sont actuellement pas pris en charge. Vous pouvez éventuellement supprimer ces paramètres, migrer les ordinateurs virtuels et puis réactivez les paramètres dans le modèle de déploiement du Gestionnaire de ressources.

Fournisseur de ressources | Fonctionnalité
---------- | ------------
Cluster | Machine virtuelle non associées disques.
Cluster | Machine virtuelle images.
Réseau | Utilisateurs de point de terminaison.
Réseau | Passerelles réseau virtuel (Azure ExpressRoute, passerelle d’application, de site à site, pointez sur site).
Réseau | Réseaux virtuels à l’aide de VNet Peering. (Migration VNet vers processeur, puis homologue) En savoir plus sur [VNet Peering] (.. /Virtual-Network/Virtual-Network-Peering-Overview.MD).
Réseau | Profils de trafic Manager.

### <a name="unsupported-configurations"></a>Configurations non prises en charge

Les configurations suivantes ne sont actuellement pas pris en charge.

Service | Configuration | Recommandation
---------- | ------------ | ------------
Gestionnaire de ressources | Rôle en fonction de contrôle d’accès (RBAC) pour les ressources classiques | Étant donné que l’URI des ressources est modifié après la migration, il est recommandé que vous prévoyez des mises à jour de stratégie RBAC devant se produire après la migration.
Cluster | Plusieurs sous réseaux associé à une machine virtuelle | Mettre à jour la configuration de sous-réseau pour faire référence à sous-réseaux uniquement.
Cluster | Machines virtuelles qui appartiennent à un réseau virtuel mais n’avez pas un sous-réseau explicit affecté | Vous pouvez éventuellement supprimer la machine virtuelle.
Cluster | Machines virtuelles ayant des alertes, à l’échelle des stratégies | La migration parcourt et ces paramètres sont supprimées. Il est recommandé d’évaluer votre environnement avant d’effectuer la migration. Par ailleurs, vous pouvez reconfigurer les paramètres d’alerte une fois la migration terminée.
Cluster | Extensions XML VM (BGInfo 1.*, débogueur Visual Studio, déploiement Web et débogage distant) | Ce n’est pas pris en charge. Il est recommandé de vous supprimez ces extensions de la machine virtuelle pour continuer la migration ou qu’ils seront supprimées automatiquement au cours du processus de migration.
Cluster | Diagnostics démarrage avec stockage Premium | Désactiver la fonctionnalité démarrage Diagnostics pour les ordinateurs virtuels avant de poursuivre la migration. Vous pouvez réactiver diagnostics démarrage dans la pile Gestionnaire de ressources une fois la migration terminée. En outre, des objets BLOB qui sont utilisés pour la capture d’écran et des journaux séries doivent être supprimés afin que vous n’avez plus sont facturés ces objets BLOB.
Cluster | Services en nuage qui contiennent des rôles web/collaborateur | Il n’est actuellement pas pris en charge.
Réseau | Réseaux virtuels qui contiennent des machines virtuelles et les rôles de travail/web |  Il n’est actuellement pas pris en charge.
Service application Azure | Réseaux virtuels qui contiennent des environnements de Service d’application | Il n’est actuellement pas pris en charge.
Azure HDInsight | Réseaux virtuels qui contiennent des services HDInsight | Il n’est actuellement pas pris en charge.
Services du cycle de vie Microsoft Dynamics | Réseaux virtuels qui contiennent des machines virtuelles qui sont gérés par les Services du cycle de vie Dynamics | Il n’est actuellement pas pris en charge.
Cluster | Extensions Azure le centre de sécurité avec un VNET qui comporte une passerelle VPN ou restauration d’urgence avec serveur DNS local | Centre de sécurité Azure installe automatiquement les extensions sur vos ordinateurs virtuels pour surveiller leur sécurité et générer des alertes. Ces extensions obtenir généralement installées automatiquement si la stratégie Azure le centre de sécurité est activée sur l’abonnement. Migration de la passerelle n’est pas pris en charge actuellement et la passerelle doit être supprimé avant de procéder à la validation de la migration, l’accès à internet au compte de stockage mémoire virtuelle est perdu lorsque la passerelle est supprimée. La migration ne va pas dans ce cas que le blob invité agent état ne peuvent pas être rempli. Il est recommandé de désactiver le centre de sécurité Azure de stratégie de l’abonnement 3 heures avant de procéder à la migration.

## <a name="the-migration-experience"></a>L’expérience de migration

Avant de commencer l’expérience de migration, les recommandations suivantes :

- Vérifiez que les ressources que vous voulez migrer n’utilisent des fonctionnalités non prises en charge ou des configurations. En règle générale, la plateforme détecte ces problèmes et génère une erreur.
- Si vous avez machines virtuelles qui ne sont pas dans un réseau virtuel, elles seront arrêtés et libérés dans le cadre de l’opération de préparation. Si vous ne voulez pas perdre l’adresse IP publique, regardez dans réserver l’adresse IP avant de déclencher l’opération de préparation. Toutefois, si les ordinateurs virtuels se trouvent dans un réseau virtuel, ils ne sont pas arrêtés et libérés.
- Planifier la migration en non des heures ouvrables afin de résoudre les erreurs éventuelles inattendus peuvent se produire pendant la migration.
- Téléchargez la configuration actuelle de vos ordinateurs virtuels à l’aide de PowerShell, les commandes de l’interface de ligne (commande) ou les API REST pour faciliter leur de validation une fois l’étape préparer terminée.
- Mettre à jour vos scripts automation/operationalization pour gérer le modèle de déploiement du Gestionnaire de ressources avant de commencer la migration. Vous pouvez également effectuer les opérations GET lorsque les ressources sont en état préparé.
- Évaluer les stratégies RBAC qui sont configurés sur les ressources IaaS classiques et planifier une fois la migration terminée.

Le flux de travail de migration se présente comme suit

![Capture d’écran montrant le flux de travail de migration](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Toutes les opérations décrites dans les sections suivantes sont idempotents. Si vous rencontrez un problème différent une fonctionnalité non prise en charge ou une erreur de configuration, il est recommandé de relancer la préparer, l’annulation ou validation opération. La plateforme Azure tente de l’action à nouveau.

### <a name="validate"></a>Valider

L’opération de valider est la première étape du processus de migration. L’objectif de cette étape consiste à analyser des données en arrière-plan pour les ressources en cours de migration et renvoyer Successful ou si les ressources sont en mesure de migration.

Vous sélectionnez le réseau virtuel ou le service hébergé (s’il n’est pas un réseau virtuel) que vous souhaitez valider pour la migration.

* Si la ressource n’est pas capable de migration, la plateforme Azure répertorie toutes les raisons pour pourquoi il n'est pas pris en charge pour la migration.

### <a name="prepare"></a>Préparer

L’opération de préparation est la deuxième étape du processus de migration. L’objectif de cette étape consiste à simuler la transformation des ressources IaaS de classique aux ressources du Gestionnaire de ressources et de les présenter côte à côte pour vous permettre de visualiser.

Vous sélectionnez le réseau virtuel ou le service hébergé (s’il n’est pas un réseau virtuel) que vous voulez préparer la migration.

* Si la ressource n’est pas capable de migration, la plateforme Azure arrête le processus de migration et répertorie la raison de l’opération de préparation l’échec.
* Si la ressource est capable de migration, la plateforme Azure première verrouille les opérations de plan de gestion des ressources en cours de migration. Par exemple, vous n’êtes pas en mesure d’ajouter un disque de données pour une machine virtuelle en cours de migration.

La plateforme Azure commence alors à la migration de métadonnées de classique au Gestionnaire de ressources pour les ressources de la migration.

Une fois l’opération de préparation terminée, vous avez la possibilité de visualiser les ressources dans les deux classique et Gestionnaire de ressources. Pour chaque service cloud dans le modèle de déploiement classique, la plateforme Azure crée un nom de groupe de ressources qui comporte le modèle `cloud-service-name>-migrated`.

>[AZURE.NOTE] Machines virtuelles qui ne sont pas dans un réseau virtuel classique sont arrêtés désalloué de cette phase de migration.

### <a name="check-manual-or-scripted"></a>Vérifier (manuelle ou script)

Dans l’étape de vérification, vous pouvez éventuellement utiliser la configuration que vous avez téléchargé précédemment pour valider la migration est à votre goût. Par ailleurs, vous pouvez vous connecter au portail et sondages les propriétés et les ressources pour valider les métadonnées migration de test vous convient.

Si vous migrez un réseau virtuel, la configuration de la plupart des machines virtuelles n’est pas redémarrée. Pour les applications sur ces machines virtuelles, vous pouvez vérifier que l’application est toujours en cours d’exécution.

Vous pouvez tester votre surveillance/automation et les scripts d’exploitation pour voir si les ordinateurs virtuels fonctionnent comme prévu et si vos scripts mis à jour fonctionnent correctement. Seules les opérations GET sont prises en charge lorsque les ressources sont en état préparé.

Il n’existe aucune fenêtre jeu avant laquelle vous avez besoin valider la migration. Vous pouvez prendre autant de temps que vous le souhaitez dans cet état. Toutefois, le plan de gestion est verrouillé pour ces ressources jusqu'à ce que vous l’annulation ou la validez.

Si vous voyez des problèmes, vous pouvez toujours abandonner la migration et revenir au modèle de déploiement classique. Une fois que vous utilisez, la plateforme Azure s’ouvre les plan de gestion des opérations sur les ressources afin que vous pouvez reprendre les opérations normalement sur ces machines virtuelles dans le modèle de déploiement classique.

### <a name="abort"></a>Abandonner

Abandon est une étape facultative que vous pouvez utiliser pour annuler vos modifications au modèle de déploiement classique et arrêter la migration.

>[AZURE.NOTE] Cette opération ne peut pas être exécutée une fois que vous avez déclenché l’opération de validation.  

### <a name="commit"></a>Valider

Une fois que vous avez terminé la validation, vous pouvez valider la migration. Ressources n’apparaissent pas plus dans classique et sont disponibles uniquement dans le modèle de déploiement du Gestionnaire de ressources. Les ressources migrés peuvent être gérés uniquement dans le nouveau portail.

>[AZURE.NOTE] Il s’agit d’une opération équipotente. En cas d’échec, il est recommandé de relancer l’opération. S’il continue à échouer, créez une demande d’assistance ou créez un billet avec une balise ClassicIaaSMigration sur notre [forum machine virtuelle](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Forum aux questions

**Ce plan de migration affecte-t-elle un de mes services existants ou les applications qui s’exécutent sur des machines virtuelles Azure ?**

Non. Les ordinateurs virtuels (classiques) sont entièrement prise en charge des services dans officielle. Vous pouvez continuer à utiliser ces ressources pour développer votre empreinte sur Microsoft Azure.

**Qu’arrive-t-il à mes machines virtuelles si je ne prévoyez pas sur la migration prochainement ?**

Nous ne sommes pas Déconseiller l’API classique existantes et le modèle de ressources. Nous voulons faciliter la migration, envisagez les fonctionnalités avancées qui sont disponibles dans le modèle de déploiement du Gestionnaire de ressources. Nous vous recommandons vivement de prendre connaissance de [certaines des avancées](virtual-machines-windows-compare-deployment-models.md) qui font partie d’IaaS sous le Gestionnaire de ressources.

**Que signifie ce plan de migration pour mon outils existant ?**

Mettre à jour vos outils vers le modèle de déploiement du Gestionnaire de ressources est une des modifications plus importantes dont vous disposez pour prendre en compte dans vos plans de migration.

**Combien de temps sera le temps d’arrêt du plan de gestion ?**

En fonction du nombre de ressources qui sont en cours de migration. Pour les déploiements plus petits (quelques dizaines de machines virtuelles), la migration entier doit prendre moins d’une heure. Pour les déploiements à grande échelle (des centaines de machines virtuelles), la migration peut prendre quelques heures.

**Puis-je annuler une fois mon migration des ressources sont validées dans le Gestionnaire de ressources ?**

Vous pouvez annuler votre migration dans la mesure où les ressources sont en état préparé. Annulation n’est pas prise en charge une fois que les ressources ont été correctement migrés via l’opération de validation.

**Puis-je restaurer mon migration cas d’échec de l’opération de validation ?**

Vous ne pouvez pas abandonner migration cas d’échec de l’opération de validation. Toutes les opérations de migration, y compris l’opération de validation, sont idempotents. Nous vous recommandons de relancer l’opération après quelques instants. Si vous rencontrez toujours un message d’erreur, créez un tickets de support ou créer un billet par la balise ClassicIaaSMigration sur notre [forum machine virtuelle](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**Je dois acheter un autre circuit voie express si je dois utiliser IaaS sous le Gestionnaire de ressources**

Non. Nous activé récemment [déplacement circuits ExpressRoute de la classique au modèle de déploiement de gestionnaire de ressources](../expressroute/expressroute-move.md). Vous n’êtes pas obligé d’acheter un nouveau circuit ExpressRoute si vous avez déjà.

**Que se passe-t-il si j’avais configuré les stratégies de contrôle d’accès basé sur un rôle pour mes ressources IaaS classiques ?**

Lors de la migration, les ressources transforment de classique au Gestionnaire de ressources. Ainsi, nous vous recommandons que vous prévoyez des mises à jour de stratégie RBAC devant se produire après la migration.

**Que se passe-t-il si j’utilise récupération de Site Azure ou sauvegarde Azure aujourd'hui ?**

Pour migrer votre Machine virtuelle qui sont activées pour la sauvegarde, voir [que j’ai sauvegardé mon machines virtuelles classiques dans l’archivage sécurisé sauvegarde. Je souhaite migrer mes machines virtuelles à partir du mode classique au mode directeur des ressources. Comment effectuer une sauvegarde les dans l’archivage sécurisé des services de récupération ?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**Puis-je valider mon abonnement ou ressources pour voir si elles sont capables de migration ?**

Oui. L’option plateforme prise en charge la migration, la première étape de la préparation de migration est pour valider que les ressources sont en mesure de la migration. En cas d’échec de l’opération de valider, vous recevez des messages pour toutes les raisons que la migration ne peut pas être effectuée.

**Que se passe-t-il si j’exécute dans une erreur de quota lors de la préparation les ressources IaaS migration ?**

Nous vous conseillons abandonner votre migration et ouvrez une demande de support pour augmenter les quotas dans la région dans lequel vous migrez les ordinateurs virtuels. Une fois la demande de quota approuvée, vous pouvez démarrer l’exécution de la procédure de migration à nouveau.

**Comment signaler un problème ?**

Publier vos problèmes et questions sur la migration sur notre [forum machine virtuelle](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), avec le mot clé ClassicIaaSMigration. Nous vous recommandons de validation toutes vos questions sur ce forum. Si vous avez un contrat de support, vous êtes invité à se connecter à une demande d’assistance.

**Que se passe-t-il si je n’aime pas les noms des ressources de la plateforme choisie lors de la migration ?**

Toutes les ressources qui vous fournissez explicitement des noms dans le modèle de déploiement classique sont conservées pendant la migration. Dans certains cas, de nouvelles ressources sont créés. Par exemple : une interface réseau est créée pour chaque machine virtuelle. Nous ne pris en charge la possibilité de contrôler les noms de ces nouvelles ressources créés pendant la migration. Ouvrez une session votre voix pour cette fonctionnalité sur le [forum de commentaires Azure](http://feedback.azure.com).

* *je reçois un message indiquant *« machine virtuelle signale l’état de l’agent global comme n’étant pas opérationnel. Par conséquent, la machine virtuelle ne peut pas être migrée. Assurez-vous que l’Agent machine virtuelle signale globale état de l’agent prêt »* ou *« machine virtuelle contient Extension dont l’état n’est pas signalée à partir de la machine virtuelle. Par conséquent, cette machine virtuelle ne peut pas être migrée. »***

Ce message est affiché lorsque la machine virtuelle n’a pas de connectivité sortante à internet. L’agent machine virtuelle utilise connectivité sortante pour atteindre le compte de stockage Azure pour mettre à jour l’état de l’agent de cinq minutes.


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez la migration de ressources IaaS classiques au Gestionnaire de ressources, vous pouvez commencer à déplacer les ressources.

- [Technique approfondie sur la plateforme prise en charge la migration de classique au Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Utiliser PowerShell pour déplacer des ressources IaaS de classique au Gestionnaire de ressources Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Infrastructure du langage commun permet de migrer des ressources IaaS de classique au Gestionnaire de ressources Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Dupliquer une machine virtuelle classique au Gestionnaire de ressources Azure à l’aide des scripts PowerShell Communauté](virtual-machines-windows-migration-scripts.md)
