<properties
   pageTitle="Vue d’ensemble de la configuration Azure Service TISSU fiable intervenants ReliableDictionaryActorStateProvider | Microsoft Azure"
   description="Obtenir des informations sur la configuration des intervenants dynamique Azure Service tissu de type ReliableDictionaryActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configuration des intervenants fiables--ReliableDictionaryActorStateProvider
Vous pouvez modifier la configuration par défaut de ReliableDictionaryActorStateProvider en modifiant le fichier settings.xml généré dans la racine du package Visual Studio sous le dossier de configuration pour l’acteur spécifié.

Le runtime Azure Service TISSU recherche des noms de section prédéfinie dans le fichier settings.xml et utilise les valeurs de configuration lors de la création des composants d’exécution sous-jacente.

>[AZURE.NOTE] Effectuez **pas** supprimer ou modifier les noms de section des configurations suivantes dans le fichier settings.xml généré dans la solution Visual Studio.

Il existe également des paramètres globaux qui affectent la configuration de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuration globale

La configuration globale est spécifiée dans le manifeste cluster pour le cluster sous la section KtlLogger. Il permet la configuration de l’emplacement du journal partagé et taille ainsi que les limites de mémoire globale utilisées par le journal. Notez que les modifications dans le manifeste cluster affectent tous les services qui utilisent ReliableDictionaryActorStateProvider et services avec état fiables.

Le manifeste cluster est un fichier XML qui contient les paramètres et les configurations qui s’appliquent à tous les nœuds et services dans le cluster. Le fichier est généralement appelé ClusterManifest.xml. Vous pouvez voir le cluster manifeste pour votre cluster à l’aide de la commande powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Ko.|8388608|Nombre minimal de Ko à allouer en mode noyau pour le pool de mémoire tampon journal écriture. Cette zone de mémoire est utilisée pour la mise en cache des informations d’état avant l’écriture sur le disque.|
|WriteBufferMemoryPoolMaximumInKB|Ko.|Aucune limite|Taille maximale à laquelle le journal écrire pool de mémoire tampon peut devenir.|
|SharedLogId|GUID|""|Spécifie un GUID unique à utiliser pour identifier le fichier journal partagé par défaut utilisé par tous les services fiables sur tous les nœuds du cluster qui ne spécifient pas le SharedLogId dans leur configuration spécifique au service. Si SharedLogId est spécifié, SharedLogPath doit également être spécifié.|
|SharedLogPath|Chemin d’accès complet|""|Spécifie le chemin d’accès complet où le fichier journal partagé utilisé par tous les services fiables sur tous les nœuds du cluster qui ne spécifient pas le SharedLogPath dans leur configuration spécifique au service. Toutefois, si SharedLogPath est spécifié, puis SharedLogId doit également être spécifié.|
|SharedLogSizeInMB|Mégaoctets|8192|Spécifie le nombre de Mo d’espace disque allouer de manière statique pour le journal partagé. La valeur doit être supérieure ou égale 2048.|

### <a name="sample-cluster-manifest-section"></a>Section manifeste exemple cluster
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Remarques
L’enregistreur dispose d’un pool global de mémoire alloué à partir de la mémoire du noyau non paginée qui n’est disponible pour tous les services fiables sur un nœud de mise en cache de données sur l’état avant écrits dans le journal dédié associé du réplica de service fiable. La taille du pool est contrôlée par les paramètres WriteBufferMemoryPoolMinimumInKB et WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB spécifie la taille initiale de cette zone de mémoire et la taille inférieure à laquelle il est possible que réduire le pool de mémoire. WriteBufferMemoryPoolMaximumInKB est la taille maximale que peut atteindre le pool de mémoire. Chaque réplica de service fiable est ouvert peut augmenter la taille du pool de mémoire par un montant système déterminé jusqu'à WriteBufferMemoryPoolMaximumInKB. S’il existe plusieurs à la demande de mémoire du pool de mémoire qu’il n’est disponible, demandes de mémoire seront retardées jusqu'à ce que la quantité de mémoire est disponible. Par conséquent, si le pool de mémoire tampon écriture est trop petit pour une configuration particulière performances peuvent diminuer.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisées ensemble pour définir le GUID et l’emplacement pour le journal partagé par défaut pour tous les nœuds du cluster. Le journal partagé par défaut est utilisé pour tous les services fiables qui ne spécifient pas les paramètres dans le settings.xml pour le service spécifique. Pour de meilleures performances, les fichiers journaux partagé sauvegardées sur des disques qui sont utilisés uniquement pour le fichier journal partagé pour réduire les conflits.

SharedLogSizeInMB spécifie la quantité d’espace disque à allouer pour le journal partagé par défaut sur tous les nœuds.  SharedLogId et SharedLogPath n’ont pas besoin être indiquée dans l’ordre des SharedLogSizeInMB être indiquée.

## <a name="replicator-security-configuration"></a>Configuration de la sécurité Replicator
Configurations de sécurité Replicator sont utilisées pour sécuriser les communications qui est utilisé lors de la réplication. Cela signifie que vous ne voyez pas l’autre le trafic de réplication, garantissant les données qui sont hautement disponibles sont également sécurisées pour les services.
Par défaut, une section de configuration de sécurité vide empêche la sécurité de la réplication.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuration Replicator
Configurations Replicator sont utilisées pour configurer le réplicateur qui est responsable de l’état d’un acteur état fournisseur hautement fiable par réplication et conserver l’état localement.
La configuration par défaut est générée par le modèle Visual Studio et doit suffit. Cette section décrit les configurations supplémentaires sont disponibles pour régler le réplicateur.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0.015|Période de temps pour lequel le réplicateur en l’attend secondaire après avoir reçu une opération avant d’envoyer à un accusé de réception principal. N’importe quel autres accusés de réception à envoyer pour les opérations traitées dans cet intervalle sont envoyés comme une réponse.||
|ReplicatorEndpoint|N/A|Aucune valeur par défaut : paramètre requis|Adresse IP et port que le réplicateur principal et secondaire utilisera pour communiquer avec d’autres fabricants du réplica définis. Cela doit faire référence à un point de terminaison TCP ressource le manifeste de service. Consultez les [ressources de manifeste de Service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources de point de terminaison dans manifeste de service. |
|MaxReplicationMessageSize|Octets|50 MO|Taille maximale des données de réplication qui peuvent être transmises en un seul message.|
|MaxPrimaryReplicationQueueSize|Nombre d’opérations|8192|Nombre maximal d’opérations dans la file d’attente principale. Une opération est libérée une fois que le réplicateur primaire reçu un accusé de réception auprès de tous les fabricants secondaires. Cette valeur doit être supérieure à 64 et à une puissance de 2.|
|MaxSecondaryReplicationQueueSize|Nombre d’opérations|16384|Nombre maximal d’opérations dans la file d’attente secondaire. Une opération est libérée après avoir apporté son état hautement disponibles via persistance. Cette valeur doit être supérieure à 64 et à une puissance de 2.|
|CheckpointThresholdInMB|MO|200|Quantité d’espace de fichier journal après laquelle l’état est contrôlée.|
|MaxRecordSizeInKB|BASE DE CONNAISSANCES|1024|Plus grande taille d’enregistrement le réplicateur peut écrire dans le journal. Cette valeur doit être un multiple de 4 et supérieures à 16.|
|OptimizeLogForLowerDiskUsage|Valeur booléenne|Vrai|Valeur True indique que le journal est configuré afin que le fichier journal dédié du réplica est créé à l’aide d’un fichier incomplet NTFS. Cela permet de réduire l’utilisation de l’espace disque réel pour le fichier. Valeur False indique que le fichier est créé avec affectations fixes, qui fournissent que les meilleures performances d’écriture.|
|SharedLogId|GUID|""|Spécifie un guid unique à utiliser pour identifier le fichier journal partagé utilisé avec ce réplica. En règle générale, services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogId est spécifié, puis SharedLogPath doit également être spécifié.|
|SharedLogPath|Chemin d’accès complet|""|Spécifie le chemin d’accès complet où le fichier journal partagé pour ce réplica sera créé. En règle générale, services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogPath est spécifié, puis SharedLogId doit également être spécifié.|


## <a name="sample-configuration-file"></a>Exemple de fichier de configuration

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Remarques
Le paramètre BatchAcknowledgementInterval contrôle la latence de réplication. Une valeur de « 0 » comme résultat latence minimale, au prix de débit (comme des accusés de réception plus devant être envoyées et traitées, chacun d’eux contenant un nombre moins élevé accusés de réception).
La valeur la plus grande pour BatchAcknowledgementInterval, plus le réplication débit global, au détriment de la latence opération plus élevée. Cela se traduit par directement à la latence des validations de transactions.

Le paramètre CheckpointThresholdInMB contrôle la quantité d’espace disque utilisé par le réplicateur pour stocker les informations d’état dans le fichier journal dédié du réplica. Augmenter cette à une valeur supérieure à la valeur par défaut peut entraîner reconfiguration plus rapides lorsqu’un nouveau réplica est ajouté à l’ensemble. Cela est dû le transfert partielle de l’état qui a lieu en raison de la disponibilité des détails de l’historique des opérations dans le journal. Cela peut potentiellement accroître le temps de récupération d’un réplica après une défaillance.

Si vous définissez OptimizeForLowerDiskUsage sur true, espace de fichier journal sera trop généré afin que réplicas actifs peuvent stocker des informations supplémentaires sur l’état dans leurs fichiers journaux, tandis que réplicas inactifs utilise moins d’espace disque. Cela permet d’héberger davantage de réplicas sur un nœud. Si vous définissez OptimizeForLowerDiskUsage sur false, les informations d’état sont écrit plus rapidement dans les fichiers journaux.

Le paramètre MaxRecordSizeInKB définit la taille maximale d’un enregistrement qui peut être écrite par le réplicateur dans le fichier journal. Dans la plupart des cas, la taille d’enregistrement de 1024 Ko par défaut est optimale. Toutefois, si le service est à l’origine des éléments de données plus grande partie des informations d’état, cette valeur devrez être augmentée. Il est peu d’intérêt dans en appliquant MaxRecordSizeInKB est inférieure à 1024, comme enregistrements plus petits utilisent uniquement l’espace nécessaire pour l’enregistrement plus petit. Nous espérons que cette valeur devront être modifiées uniquement dans certains cas rares.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisés ensemble à un service d’utiliser un journal partagé distinct à partir du journal partagé par défaut pour le nœud. Pour plus d’efficacité, services autant que possible doivent spécifier le même journal partagé. Fichiers journaux partagés doivent être placés sur des disques qui sont utilisés uniquement pour le fichier journal partagé, pour réduire les conflits de déplacement des têtes. Nous espérons que ces valeurs devront être modifiées uniquement dans certains cas rares.
