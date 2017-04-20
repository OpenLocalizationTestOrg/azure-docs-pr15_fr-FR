<properties 
    pageTitle="Déplacement de données entre les bases de données plus grande échelle cloud | Microsoft Azure" 
    description="Explique comment manipuler milieu des fragments et déplacer des données via un service autonome à l’aide d’API élastique bases de données." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>Déplacement de données entre les bases de données plus grande échelle cloud

Si vous êtes un logiciel en tant que Service développeur et soudainement votre application est soumis à la demande considérable, vous devez prendre en charge la croissance. Si vous ajoutez des bases de données plus (milieu des fragments). Comment vous redistribuer les données pour les nouvelles bases de données sans interrompre l’intégrité des données ? Utilisez l' **outil de fusion de fractionnement** pour déplacer des données à partir de bases de données limitées pour les nouvelles bases de données.  

L’outil de fusion de fractionnement s’exécute comme un service web Azure. Un administrateur ou un développeur utilise l’outil pour déplacer shardlets (données à partir d’un éclater) entre les bases de données différentes (milieu des fragments). L’outil utilise éclater carte management pour mettre à jour de la base de données de métadonnées de service et vérifiez les mappages cohérentes.

![Vue d’ensemble][1]

## <a name="download"></a>Télécharger
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Documentation
1. [Didacticiel outil de base de données élastique fusion de fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Configuration de la sécurité de fusion de fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Considérations sur la sécurité de fusion de fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Gestion de carte partagé](sql-database-elastic-scale-shard-map-management.md)
* [Migrer des bases de données existantes à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Outils de base de données élastique](sql-database-elastic-scale-introduction.md)
* [Élastique glossaire d’outils de base de données](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Pourquoi utiliser l’outil de fusion de fractionnement ?

**Flexibilité**

Applications doivent étirer flexible au-delà des limites d’une seule base de données de base de données SQL Azure. Utilisez l’outil pour déplacer des données selon vos besoins pour les nouvelles bases de données tout en conservant l’intégrité référentielle.

**Fractionner pour agrandir** 

Vous devez augmenter la capacité globale de gérer la croissance exponentielle. Pour ce faire, créer des capacités supplémentaires en ont les données et en distribution entre bases de données par incréments plus jusqu'à ce que les besoins sont remplies. Il s’agit d’un exemple principal de la fonctionnalité « fractionner ». 

**Publipostage pour réduire**

Capacité doit réduire en raison de la saisonnalité de l’entreprise. L’outil vous permet de mettre à l’échelle vers le bas à moins d’unités d’échelle lorsque entreprise ralentit. La fonctionnalité « Fusion » dans le Service de fusion et fractionnement échelle élastique couvre cette exigence. 

**Gérer les zones sensibles en déplaçant shardlets**

Avec plusieurs clients par base de données, l’allocation de shardlets au milieu des fragments peut entraîner des engorgements capacité sur certains milieu des fragments. Cette fonctionnalité nécessite réaffectation shardlets ou en la déplaçant shardlets occupé (e) au milieu des fragments nouveau ou moins utilisés. 

## <a name="concepts--key-features"></a>Concepts et fonctionnalités clés

**Services hébergés par client**

La fusion de fractionnement est fournie sous forme d’un service hébergé par un client. Vous devez déployer et héberger le service dans votre abonnement Microsoft Azure. Le package que vous téléchargez à partir de NuGet contient un modèle de configuration pour terminer avec les informations relatives à votre déploiement spécifique. Consultez le [didacticiel split-fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md) pour plus d’informations. Étant donné que le service est exécuté dans votre abonnement Azure, vous pouvez contrôler et configurer la plupart des aspects de sécurité du service. Le modèle par défaut inclut les options pour configurer SSL, l’authentification basée sur certificat client, le chiffrement des informations d’identification stockées, DoS protégeant restrictions IP. Vous trouverez plus d’informations sur la sécurité dans la [configuration de la sécurité de fusion et fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)de document suivante.

La valeur par défaut déployé avec un collaborateur et un rôle web le service s’exécute. Chaque utilise la taille de mémoire virtuelle A1 dans Azure Cloud Services. Pendant que vous ne pouvez pas modifier ces paramètres lors du déploiement du package, vous pouvez décider de les après un déploiement réussi dans le service cloud en cours d’exécution, (via le portail Azure). Notez que le rôle de travail ne doit pas être configuré pour plus d’informations d’une instance unique pour des raisons techniques. 

**Intégration de mappage partagé**

Le service de fractionnement fusion interagit avec la carte partagé de l’application. Lorsque vous utilisez le service de fusion de fractionnement pour fractionner ou fusionner des plages ou pour passer shardlets milieu des fragments, le service conserve automatiquement le mappage éclater à jour. Pour ce faire, le service se connecte à la base de données Gestionnaire de carte partagé de l’application et conserve les mappages et les plages que l’avancement des demandes de fusionner/fractionner/déplacements. Ainsi que la carte éclater présente toujours une vue à jour sont en opérations de fusion de fractionnement. Opérations de déplacement de fusionner et shardlet fractionner, sont implémentées en déplaçant un lot de shardlets de l’éclater source vers l’éclater cible. Pendant la shardlet mouvement opération la shardlets couvertes par le lot actuel sont marqués comme étant hors connexion dans la carte partagé et ne sont pas disponible pour les connexions de routage dépendant des données à l’aide de l’API **OpenConnectionForKey** . 

**Connexions shardlet cohérentes**

Au démarrage de déplacement de données pour un nouveau lot de shardlets, tout dépendant des données éclater-map fournie routage les connexions à l’éclater le stockage de la shardlet sont tuées et à venir des connexions à partir de la carte éclater API à ces shardlets sont bloqués alors que le déplacement de données est en cours afin d’éviter les incohérences. Connexions à d’autres shardlets sur le même éclater seront également obtenir supprimées mais aboutit à nouveau immédiatement sur Réessayer. Une fois que le lot est déplacé, les shardlets sont marqués en ligne à nouveau pour l’éclater cible et la source de données est supprimé de l’éclater source. Le service accède ces étapes pour chaque lot jusqu'à ce que tous les shardlets ont été déplacées. Cela vous aboutissez à plusieurs opérations d’arrêt de connexion au cours de l’opération de fusion/fractionnement/déplacer complète.  

**Gestion de la disponibilité shardlet**

Limitation de la connexion de suppression du lot en cours de shardlets comme indiqué ci-dessus limite l’étendue de l’indisponibilité à un seul lot de shardlets à la fois. Il est préférable une approche où l’éclater complète reste en mode hors connexion pour tous ses shardlets au cours d’une opération de fractionnement ou de fusion. La taille d’un lot, définie comme étant le nombre de shardlets distincts pour déplacer à la fois, est un paramètre de configuration. Il peut être défini pour chaque opération de fusion et publipostage fractionnée en fonction des besoins de disponibilité et les performances de l’application. Notez que la plage est verrouillée dans la carte partagé peut être supérieure à la taille du lot spécifiée. C’est parce que le service renvoie la taille de la plage dont le nombre réel ont des valeurs de clé dans les données correspond à environ à la taille du lot. Ceci est important à retenir en particulier pour les clés ont peu rempli. 

**Stockage de métadonnées**

Le service de fractionnement fusion utilise une base de données pour maintenir son statut et conserver des journaux pendant le traitement de demande. L’utilisateur crée cette base de données de son abonnement et lui fournit la chaîne de connexion dans le fichier de configuration pour le déploiement de service. Les administrateurs d’organisation de l’utilisateur peuvent également se connecter à cette base de données pour visualiser l’avancement demande et rechercher des informations détaillées concernant défaillances potentielles.

**Ont sensibilisation**

Le service de fractionnement fusion fait la distinction entre les tables (1) sharded, des tableaux de référence (2) et tables (3) normales. La sémantique d’une opération de fusion/fractionnement/déplacement dépendre du type de la table utilisée et est définie comme suit : 

* **Tables sharded**: fractionnement, publipostage et opérations de déplacement déplacent shardlets de source vers éclater cible. Après la réussite de la demande globale, ces shardlets ne sont plus présents sur la source. Notez que les tables cible doivent figurer sur l’éclater cible et ne doivent pas contenir des données dans la plage cible avant le traitement de l’opération. 

* **Tables de référence**: pour les tables de référence, le fractionnement, fusionner et déplacer des opérations de copier les données provenant de la source dans le partagé cible. Notez qu’aucune modification se produire sur l’éclater cible pour une table donnée si n’importe quelle ligne est déjà présente dans cette table sur la cible. Le tableau doit être vide pour une opération de copie de référence sur la table traités.

* **Autres Tables**: autres tables peuvent être présents sur la source ou cible d’une opération de fusion et publipostage fractionnée. Le service de fractionnement fusion ignore ces tables pour toutes les opérations de copie ou déplacement de données. Notez qu’ils peuvent interférer avec ces opérations en cas de contraintes.

Les informations de référence et les tables sharded sont fournies par le **SchemaInfo** API sur la carte partagé. L’exemple suivant illustre l’utilisation de ces API sur un smm éclater donnée carte gestionnaire objet : 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

La région « tables » et « pays » sont définis sous forme de tables de référence et seront copiées avec opérations de fusionner/fractionner/déplacement. « client » et « orders » à son tour sont définies en tant que tables sharded. C_CUSTKEY et O_CUSTKEY servent de la touche ont. 

**L’intégrité référentielle**

Le service de fractionnement fusion analyse des dépendances entre les tables et utilise les relations de clé étrangère de clé primaire pour les opérations de déplacement des tables de référence et shardlets de l’étape. En règle générale, tables de références sont d’abord copiées dans l’ordre de dépendance, puis shardlets sont copiées dans l’ordre de leurs dépendances au sein de chaque lot. Cela est nécessaire pour que les contraintes FK CP l’éclater cible sont prises en charge que les nouvelles données arrivent. 

**Éclater carte cohérence et son achèvement éventuel**

En présence d’erreurs, le service de fractionnement fusion reprend les opérations après toute coupure de courant et a pour but d’effectuer l’une de l’avancement des demandes. Toutefois, il peut arriver irrécupérables, par exemple, lorsque l’éclater cible est perdu ou compromis au-delà de réparation. Dans ce cas, certaines shardlets qui ont été censée être déplacés peuvent continuer à se trouvent sur l’éclater source. Le service garantit que les mappages shardlet sont à jour uniquement après que les données nécessaires a été correctement copiées à la cible. Shardlets ne sont supprimés de la source une fois que toutes ses données a été copié à la cible et les mappages correspondantes ont été mis à jour avec succès. L’opération de suppression se produit en arrière-plan tandis que la plage est déjà en ligne sur l’éclater cible. Le service de fractionnement fusion toujours garantit la cohérence des mappages stockées dans la carte partagé.


## <a name="the-split-merge-user-interface"></a>L’interface utilisateur de la fusion de fractionnement

Le package de service de fractionnement fusion inclut un rôle de collaborateur et un rôle web. Le rôle web est utilisé pour envoyer des demandes de fusion et fractionnement d’une manière interactive. Principaux composants de l’interface utilisateur sont les suivantes :

-    Type d’opération : Le type d’opération est une case d’option qui détermine le type d’opération effectuée par le service pour cette requête. Vous pouvez choisir entre le fractionnement, fusionner et déplacer des scénarios. Vous pouvez également annuler une opération soumise précédemment. Vous pouvez utiliser fractionnement, fusionner et déplacer des demandes de plage éclater cartes. Éclater liste mappe uniquement les opérations de déplacement de prise en charge.

-    Éclater carte : La section suivante de paramètres de demande présentent les informations sur la carte partagé et la base de données qui héberge votre carte partagé. En particulier, vous devez fournir le nom du serveur de base de données SQL Azure et de base de données qui héberge la shardmap, les informations d’identification pour vous connecter à la base de données de carte éclater et enfin le nom de la carte partagé. Pour l’instant, l’opération n’accepte qu’un ensemble unique d’informations d’identification. Ces informations d’identification doivent disposer des autorisations suffisantes d’apporter des modifications au mappage d’éclater également en ce qui concerne les données utilisateur au milieu des fragments.

-    Plage source (fractionner ou fusionner) : une opération de fusion et publipostage fractionnement traite une plage à l’aide de sa clé seuils. Pour spécifier une opération avec une valeur de clé haute illimitée, cochez la case à cocher « clé haute est max » et laissez le champ de clé haut vide. La plage de valeurs clés que vous spécifiez est inutile de précisément faire correspondre un mappage et ses limites dans votre carte partagé. Si vous ne spécifiez pas les limites de la plage à tout le service déduit la plage le plus proche pour vous automatiquement. Vous pouvez utiliser le script PowerShell GetMappings.ps1 pour récupérer les mappages actuels dans une carte éclater donnée.

-    Comportement de la Source fractionnée (split) : pour les opérations de fractionnement, définissez le point pour fractionner la plage source. Pour cela, en fournissant la clé ont l’endroit où vous voulez effectuer le fractionnement. Utiliser le bouton d’option indiquer que vous souhaitez la partie inférieure de la plage (à l’exception de la clé de fractionnement) pour vous déplacer, ou si vous souhaitez que la partie supérieure à déplacer (y compris la clé de fractionnement).

-    Source Shardlet (va) : déplacer les opérations sont différentes des opérations de fractionnement ou de fusion qu’ils ne nécessitent pas une plage pour décrire la source. Une source pour déplacer simplement identifiée par la valeur de clé ont que vous souhaitez déplacer.

-    Cibler partagé (split) : une fois que vous avez fourni les informations sur la source de votre opération de fractionnement, vous devez définir l’emplacement souhaité pour les données à copier vers en fournissant le nom de serveur et la base de données de base de données SQL Azure pour la cible.

-    Plage cible (de fusion) : opérations de fusion shardlets atteindre un partagé existant. Vous identifiez l’éclater existant en fournissant les limites de la plage de la plage existante que vous souhaitez fusionner avec.

-    Taille du lot : La taille du lot détermine le nombre de shardlets passer en mode hors connexion à un moment pendant le déplacement de données. Il s’agit d’un nombre entier où vous pouvez utiliser des valeurs plus petites lorsque vous êtes sensible aux longues périodes de temps d’arrêt pour shardlets. Des valeurs plus grandes augmentera le temps qui est un shardlet donné mais en mode hors connexion peut améliorer les performances.

-    Id de l’opération (Annuler) : Si vous avez une opération en cours n’est plus nécessaire, vous pouvez annuler l’opération grâce à son ID opération dans ce champ. Vous pouvez récupérer l’ID d’opération de la table demande d’état (voir Section 8.1) ou de la sortie dans un navigateur web dans laquelle vous avez envoyé la demande.


## <a name="requirements-and-limitations"></a>Configuration requise et Limitations 

L’implémentation actuelle du service split-fusion est soumis à la configuration requise et les restrictions suivantes : 

* Au milieu des fragments doivent exister et être enregistré dans la carte éclater avant de pouvoir effectuer une opération de fusion et fractionnement sur ces milieu des fragments. 

* Le service ne crée pas de tables ou autres objets de base de données automatiquement dans le cadre de ses opérations. Cela signifie que le schéma pour sharded toutes les tables et des tables de référence doivent figurer sur l’éclater cible avant toute opération Fusionner/fractionner/déplacer. Sharded les tables en particulier doivent être vide dans la plage où shardlets nouveau doivent être ajoutés par une opération de fusion/fractionnement/déplacement. Dans le cas contraire, l’opération échouera la cohérence initial de l’éclater cible. Notez également cette référence de données sont copiées uniquement si la référence de table est vide et qu’il n’y a aucune garantie de la cohérence en ce qui concerne les autres simultané écrivent des opérations sur les tableaux de référence. Nous vous recommandons de ceci : lors de l’exécution des opérations Fusionner/fractionner, sans autres opérations d’écriture apporter des modifications aux tables de références.

* Le service repose sur identité ligne établie par un index unique ou une clé qui comporte la touche ont afin d’améliorer les performances et la fiabilité de grande shardlets. Cela permet au service déplacer des données à un même précise à simplement la valeur de clé ont. Cela permet de réduire la quantité maximale de l’espace du journal et les verrous requis pendant l’opération. Envisager de créer un index unique ou une clé primaire, y compris la clé ont sur une table donnée si vous voulez utiliser cette table avec les demandes de fusionner/fractionner/déplacer. Pour des raisons de performances, la clé ont doit être la colonne début de la clé ou l’index.

* Pendant le traitement des demandes, certaines données shardlet peuvent être présentes à la fois sur la source et l’éclater cible. Cela est nécessaire pour protéger des échecs pendant le déplacement de shardlet. L’intégration de fractionnement-fusion et publipostage avec la carte éclater garantit que les connexions à travers les données dépendantes de l’API à l’aide de la méthode **OpenConnectionForKey** sur la carte éclater le routage ne voient pas les états intermédiaires n’est pas cohérente. Toutefois, lorsque vous vous connectez à la source ou au milieu des fragments cible sans utiliser la méthode **OpenConnectionForKey** , n’est pas cohérente états intermédiaires peuvent être visibles lorsque les demandes de fusionner/fractionner/déplacer passent. Ces connexions peuvent afficher les résultats partielles ou aux doublons selon le minutage ou l’éclater la connexion sous-jacente. Cette limitation actuellement inclut les connexions effectuées par les échelle élastique Multi-Shard-requêtes.

* La base de données de métadonnées du service de fusion et fractionnement ne doit pas être partagé entre différents rôles. Par exemple, un rôle du service split-fusion en cours d’exécution dans l’environnement intermédiaire doit pointer vers une base de données de métadonnées autre que le rôle de production.
 

## <a name="billing"></a>Facturation 

Le service de fractionnement fusion fonctionne comme un service cloud dans votre abonnement Microsoft Azure. Par conséquent, frais des services en nuage applicables à votre instance du service. Sauf si vous effectuez souvent des opérations de fusionner/fractionner/déplacement, nous vous recommandons de que vous supprimez votre service cloud split-fusion. Qui enregistre les coûts pour l’exécution ou déployé instances de service cloud. Vous pouvez redéployez et démarrer la configuration de votre exécutable facilement dès que vous souhaitez effectuer des opérations de fractionnement ou de fusion. 
 
## <a name="monitoring"></a>Surveillance des mots clés 
### <a name="status-tables"></a>Tableaux d’état 

Le Service de fusion et fractionnement fournit la table **d’état de la demande** dans les métadonnées stocker la base de données pour une analyse de demandes terminées et en cours. Le tableau répertorie une ligne pour chaque demande de fusion de fractionnement qui a été soumis à cette instance du service de publipostage de fractionnement. Il vous propose les informations suivantes pour chaque requête :

* **Horodatage**: l’heure et la date de début de la demande.

* **OperationId**: un GUID qui identifie la demande. Cette requête permet également d’annuler l’opération alors qu’il est toujours en cours.

* **État**: l’état actuel de la demande. Pour les demandes en cours, il répertorie également la phase actuelle dans lequel la demande est.

* **CancelRequest**: un indicateur qui indique si la demande a été annulée.

* **Cours**: une estimation du pourcentage d’achèvement de l’opération. Une valeur de 50 indique que l’opération est achevée à 50 % environ.

* **Détails**: valeur XML qui fournit un rapport de progression plus détaillé. Le rapport d’avancement est régulièrement mise à jour comme ensembles de lignes sont copiées à partir de la source cible. En cas d’erreurs ou des exceptions, cette colonne inclut également des informations plus détaillées sur l’échec.


### <a name="azure-diagnostics"></a>Diagnostics de Windows Azure

Le service de fusion et fractionnement utilise Diagnostics Azure basé sur Azure SDK 2.5 pour surveillance et les diagnostics. Vous contrôlez la configuration de diagnostics comme indiqué ici : [L’activation des Diagnostics dans Azure Cloud Services et Machines virtuelles](../cloud-services/cloud-services-dotnet-diagnostics.md). Le package de téléchargement inclut deux configurations diagnostics – un pour le rôle web et un pour le rôle de collaborateur. Ces configurations diagnostics pour le service suivent les instructions de [Principes de base de Service Cloud dans Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Il inclut les définitions pour enregistrer les journaux compteurs de Performance, IIS, les journaux d’événements Windows et d’application de fusion de fractionner les journaux d’événements. 

## <a name="deploy-diagnostics"></a>Déployer des Diagnostics 

Pour activer la surveillance et les diagnostics à l’aide de la configuration de diagnostic pour les rôles web et collaborateur fournis par le package NuGet, exécutez les commandes suivantes à l’aide de PowerShell Azure : 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Vous pouvez trouver plus d’informations sur la façon de configurer et déployer des paramètres des diagnostics ici : [L’activation des Diagnostics dans Azure Cloud Services et Machines virtuelles](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Récupérer des diagnostics 

Vous pouvez facilement accéder votre diagnostics à partir de l’Explorateur de serveurs Visual Studio dans la partie de l’arborescence de l’Explorateur de serveurs Azure. Ouvrez une instance de Visual Studio, puis dans la barre de menus, cliquez sur Affichage et Explorateur de serveurs. Cliquez sur l’icône pour vous connecter à votre abonnement Azure Azure. Accéder aux Azure -> stockage -> <your storage account> -> tableaux -> WADLogsTable. Pour plus d’informations, consultez [Ressources de stockage de navigation avec l’Explorateur de serveur](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

Le WADLogsTable mis en surbrillance dans la figure ci-dessus contient les événements détaillées journal d’application du service de publipostage de fractionnement. Notez que la configuration par défaut du package téléchargé est adaptée à un déploiement de production. Par conséquent l’intervalle auquel les journaux et les compteurs sont extraites par les instances de service est volumineux (5 minutes). Pour tester et le développement, réduire l’intervalle en ajustant les paramètres des diagnostics du web ou du rôle de collaborateur à vos besoins. Avec le bouton droit sur le rôle dans l’Explorateur de serveur de Visual Studio (voir ci-dessus), puis ajustez la période transférer dans la boîte de dialogue pour les paramètres de configuration de Diagnostics : 

![Configuration][3]


## <a name="performance"></a>Performances

En règle générale, les meilleures performances consiste à attendre d’est élevée, plus performant niveaux de service dans la base de données SQL Azure. Supérieures affectations IO, du processeur et la mémoire pour les niveaux de service plus élevés tirer la copie en bloc et opérations qui utilise le service de fractionnement-fusion de suppression. Pour cette raison, augmenter le niveau de service uniquement pour les bases de données pour une période limitée, définie.

Le service effectue également des requêtes de validation dans le cadre de ses opérations. Ces requêtes validation Vérifiez inattendus présence de données dans la plage cible et assurez-vous que toutes les opérations Fusionner/fractionner/déplacer démarre à partir d’un état cohérent. Ces toutes les requêtes fonctionnent dans les plages de clés ont défini par l’étendue de l’opération et la taille du lot fournis dans le cadre de la définition de requête. Ces requêtes fonctionnent mieux si un index est établie qui possède la clé ont comme étant la colonne non significatif. 

En outre, une propriété unicité avec la touche ont comme étant la colonne début permettra au service d’utiliser une approche optimisée qui limite l’utilisation des ressources en termes d’espace et la mémoire. Cette propriété unicité est nécessaire pour déplacer des tailles de grande quantité de données (généralement supérieures à 1 Go). 

## <a name="how-to-upgrade"></a>Comment mettre à niveau

1. Suivez les étapes de [déploiement d’un service de fusion et fractionner](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Modifier votre fichier de configuration de service cloud pour votre déploiement de fractionnement fusion refléter les nouveaux paramètres de configuration. Un nouveau paramètre obligatoire est les informations relatives au certificat utilisé pour le chiffrement. Un moyen simple pour effectuer cette action consiste à comparer le nouveau fichier de modèle de configuration à partir du téléchargement par rapport à votre configuration existante. Assurez-vous que vous ajoutez les paramètres de « DataEncryptionPrimaryCertificateThumbprint » et « DataEncryptionPrimary » pour le site web et le rôle de travail.
3. Avant de déployer la mise à jour vers Azure, assurez-vous que toutes les opérations de fusion en cours d’exécution de fractionnement ont fini. Vous pouvez facilement ce faire, interrogation de l’état de la demande et PendingWorkflows tables dans la base de données de fusion et fractionnement métadonnées pour les demandes en cours.
4. Mettre à jour votre déploiement de service cloud existant pour la fusion de fractionnement dans votre abonnement Azure avec le nouveau lot et votre fichier de configuration de service mis à jour.

Vous n’avez pas besoin de mise en service une nouvelle base de données de métadonnées pour la fusion de fractionner une mise à niveau. La nouvelle version met automatiquement à niveau votre base de données de métadonnées existante vers la nouvelle version. 

## <a name="best-practices--troubleshooting"></a>Meilleures pratiques et résolution des problèmes
 
-    Définir un client de test et d’exercer vos opérations Fusionner/fractionner/déplacer les plus importantes avec le client de test sur plusieurs milieu des fragments. Assurez-vous que toutes les métadonnées sont correctement définie dans votre carte partagé et que les opérations ne pas violer contraintes ou clés étrangères.
-    Conserver le client de test problèmes liés à la taille de données au-dessus de la taille maximale des données de votre client pour vous assurer que vous rencontrez pas taille des données plus grande. Cela permet d’évaluer une limite supérieure sur le temps que nécessaire pour déplacer un seul client. 
-    Assurez-vous que votre schéma autorise les suppressions. Le service de fusion et fractionnement requiert la possibilité de supprimer des données à partir de l’éclater source une fois que les données ont été copiées avec succès à la cible. Par exemple, **Supprimer déclencheurs** peut empêcher le service de la suppression de la source de données et peut entraîner l’échec d’opérations.
-    La clé ont doit être la colonne principale dans votre clé primaire ou la définition d’index unique. Qui garantit les meilleures performances, les requêtes de validation fractionnement ou fusion et pour les opérations de déplacement et la suppression de données réelles qui fonctionnent toujours sur les plages clés ont.
-    Colocaliser votre service de fusion et fractionnement dans le centre de région et de données où se trouvent vos bases de données. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
