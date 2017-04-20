<properties
   pageTitle="Vue d’ensemble de la configuration du Service tissu Azure Services fiable | Microsoft Azure"
   description="Obtenir des informations sur la configuration des Services fiables dynamique dans Azure Service tissu."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="sumukhs"/>

# <a name="configure-stateful-reliable-services"></a>Configurer les services fiables avec état

Il existe deux ensembles de paramètres de configuration de services fiables. Un seul ensemble est global pour tous les services fiables dans le cluster tandis que l’autre ensemble est spécifique à un service particulier fiable.

## <a name="global-configuration"></a>Configuration globale

La configuration du service fiable global spécifiée dans le manifeste cluster pour le cluster sous la section KtlLogger. Il permet la configuration de l’emplacement du journal partagé et taille ainsi que les limites de mémoire globale utilisées par le journal. Le manifeste cluster est un fichier XML qui contient les paramètres et les configurations qui s’appliquent à tous les nœuds et services dans le cluster. Le fichier est généralement appelé ClusterManifest.xml. Vous pouvez voir le cluster manifeste pour votre cluster à l’aide de la commande powershell Get-ServiceFabricClusterManifest.

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


## <a name="service-specific-configuration"></a>Configuration du service spécifique
Vous pouvez modifier les configurations par défaut des Services fiables dynamique en utilisant le package de configuration (Config) ou l’implémentation du service (code).

+ **Configuration de la** - Configuration via le package config s’effectue en modifiant le fichier Settings.xml généré dans la racine du package Microsoft Visual Studio sous le dossier Config pour chaque service dans l’application.
+ **Code** - Configuration via le code grâce à la création d’un ReliableStateManager à l’aide d’un objet ReliableStateManagerConfiguration à l’ensemble d’options appropriées.

Par défaut, le runtime Azure Service TISSU recherche des noms de section prédéfinie dans le fichier Settings.xml et utilise les valeurs de configuration lors de la création des composants d’exécution sous-jacente.

>[AZURE.NOTE] Ne **pas** supprimer les noms de section des configurations suivantes dans la Settings.xml de fichiers qui est généré dans la solution Visual Studio, sauf si vous envisagez de configurer votre service via le code.
Renommer les noms de package ou de la section config nécessite une modification du code lors de la configuration de la ReliableStateManager.


### <a name="replicator-security-configuration"></a>Configuration de la sécurité Replicator
Configurations de sécurité Replicator sont utilisées pour sécuriser les communications qui est utilisé lors de la réplication. Cela signifie que les services ne seront pas en mesure de voir l’autre le trafic de réplication, garantissant que les données sont apportées hautement disponibles soient également sécurisées. Par défaut, une section de configuration de sécurité vide empêche la sécurité de la réplication.

### <a name="default-section-name"></a>Nom de la section par défaut
ReplicatorSecurityConfig

>[AZURE.NOTE] Pour modifier le nom de cette section, remplace le paramètre replicatorSecuritySectionName au constructeur ReliableStateManagerConfiguration lors de la création du ReliableStateManager pour ce service.


### <a name="replicator-configuration"></a>Configuration Replicator
Configurations Replicator configurer le réplicateur qui est responsable de l’état fiable état du Service hautement fiable par réplication et conserver l’état localement.
La configuration par défaut est générée par le modèle Visual Studio et doit suffit. Cette section décrit les configurations supplémentaires sont disponibles pour régler le réplicateur.

### <a name="default-section-name"></a>Nom de la section par défaut
ReplicatorConfig

>[AZURE.NOTE] Pour modifier le nom de cette section, remplace le paramètre replicatorSettingsSectionName au constructeur ReliableStateManagerConfiguration lors de la création du ReliableStateManager pour ce service.


### <a name="configuration-names"></a>Noms de configuration
|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0.015|Période de temps pour lequel le réplicateur en l’attend secondaire après avoir reçu une opération avant d’envoyer à un accusé de réception principal. N’importe quel autres accusés de réception à envoyer pour les opérations traitées dans cet intervalle sont envoyés comme une réponse.|
|ReplicatorEndpoint|N/A|Aucune valeur par défaut : paramètre requis|Adresse IP et port que le réplicateur principal et secondaire utilisera pour communiquer avec d’autres fabricants du réplica définis. Cela doit faire référence à un point de terminaison TCP ressource le manifeste de service. Consultez les [ressources de manifeste de Service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources de point de terminaison dans un manifeste de service. |
|MaxPrimaryReplicationQueueSize|Nombre d’opérations|8192|Nombre maximal d’opérations dans la file d’attente principale. Une opération est libérée une fois que le réplicateur primaire reçu un accusé de réception auprès de tous les fabricants secondaires. Cette valeur doit être supérieure à 64 et à une puissance de 2.|
|MaxSecondaryReplicationQueueSize|Nombre d’opérations|16384|Nombre maximal d’opérations dans la file d’attente secondaire. Une opération est libérée après avoir apporté son état hautement disponibles via persistance. Cette valeur doit être supérieure à 64 et à une puissance de 2.|
|CheckpointThresholdInMB|MO|50|Quantité d’espace de fichier journal après laquelle l’état est contrôlée.|
|MaxRecordSizeInKB|BASE DE CONNAISSANCES|1024|Plus grande taille d’enregistrement le réplicateur peut écrire dans le journal. Cette valeur doit être un multiple de 4 et supérieures à 16.|
|MinLogSizeInMB|MO|0 (système déterminé)|Taille minimale du journal des transactions. Le journal ne pourra pas tronquer vers une taille inférieure à ce paramètre. 0 indique que le réplicateur déterminera la taille minimale du journal. Augmentation de cette valeur augmente la possibilité de faire les copies partielles et incrémentielles depuis chances d’enregistrements pertinents tronqués sont réduits.|
|TruncationThresholdFactor|Facteur|2|Détermine en taille du journal, troncation sera déclenchée. Seuil troncature est déterminé par MinLogSizeInMB multiplié par TruncationThresholdFactor. TruncationThresholdFactor doit être supérieure à 1. MinLogSizeInMB * TruncationThresholdFactor doit être inférieure à MaxStreamSizeInMB.|
|ThrottlingThresholdFactor|Facteur|4|Détermine la taille du journal, le réplica début en limitée. Seuil de limitation (en Mo) est déterminé par Max ((MinLogSizeInMB *ThrottlingThresholdFactor),(CheckpointThresholdInMB* ThrottlingThresholdFactor)). Limitation seuil (en Mo) doit être supérieur au seuil troncation (en Mo). Seuil troncation (en Mo) doit être inférieure à MaxStreamSizeInMB.|
|MaxAccumulatedBackupLogSizeInMB|MO|800|Taille (en Mo) des journaux de sauvegarde dans une chaîne de journal de sauvegarde donné accumulé max. Une sauvegarde demandes incrémentielles échouera si la sauvegarde incrémentielle génère un journal de sauvegarde qui entraînerait les journaux de sauvegarde cumulés depuis la sauvegarde complète pertinente soit plus grande taille. Dans ce cas, l’utilisateur doit effectuer une sauvegarde complète.|
|SharedLogId|GUID|""|Spécifie un GUID unique à utiliser pour identifier le fichier journal partagé utilisé avec ce réplica. En règle générale, services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogId est spécifié, puis SharedLogPath doit également être spécifié.|
|SharedLogPath|Chemin d’accès complet|""|Spécifie le chemin d’accès complet où le fichier journal partagé pour ce réplica sera créé. En règle générale, services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogPath est spécifié, puis SharedLogId doit également être spécifié.|
|SlowApiMonitoringDuration|Secondes|300|Définit l’intervalle d’analyse pour les appels API managées. Exemple : fonction de sauvegarde de rappel fourni par l’utilisateur. Une fois l’intervalle écoulé, un rapport d’intégrité avertissement est envoyée vers le Gestionnaire d’état.|

### <a name="sample-configuration-via-code"></a>Exemple de configuration via le code
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Exemple de fichier de configuration
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Remarques
Latence de la réplication BatchAcknowledgementInterval contrôles. Une valeur de « 0 » comme résultat latence minimale, au prix de débit (comme des accusés de réception plus devant être envoyées et traitées, chacun d’eux contenant un nombre moins élevé accusés de réception).
La valeur la plus grande pour BatchAcknowledgementInterval, plus le réplication débit global, au détriment de la latence opération plus élevée. Cela se traduit par directement à la latence des validations de transactions.

La valeur pour CheckpointThresholdInMB contrôle la quantité d’espace disque utilisé par le réplicateur pour stocker les informations d’état dans le fichier journal dédié du réplica. Augmenter cette à une valeur supérieure à la valeur par défaut peut entraîner reconfiguration plus rapides lorsqu’un nouveau réplica est ajouté à l’ensemble. Cela est dû le transfert partielle de l’état qui a lieu en raison de la disponibilité des détails de l’historique des opérations dans le journal. Cela peut potentiellement accroître le temps de récupération d’un réplica après une défaillance.

Le paramètre MaxRecordSizeInKB définit la taille maximale d’un enregistrement qui peut être écrite par le réplicateur dans le fichier journal. Dans la plupart des cas, la taille d’enregistrement de 1024 Ko par défaut est optimale. Toutefois, si le service est à l’origine des éléments de données plus grande partie des informations d’état, cette valeur devrez être augmentée. Il est peu d’intérêt dans en appliquant MaxRecordSizeInKB est inférieure à 1024, comme enregistrements plus petits utilisent uniquement l’espace nécessaire pour l’enregistrement plus petit. Nous espérons que cette valeur devront être modifiés dans certains cas rares uniquement.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisés ensemble à un service d’utiliser un journal partagé distinct à partir du journal partagé par défaut pour le nœud. Pour plus d’efficacité, services autant que possible doivent spécifier le même journal partagé. Fichiers journaux partagés doivent être placés sur des disques qui sont utilisés uniquement pour le fichier journal partagé pour réduire les conflits de déplacement des têtes. Nous espérons que cette valeur devront être modifiés dans certains cas rares uniquement.

## <a name="next-steps"></a>Étapes suivantes
 - [Déboguer votre application de Service tissu dans Visual Studio](service-fabric-debugging-your-application.md)
 - [Référence du développeur pour des Services fiables](https://msdn.microsoft.com/library/azure/dn706529.aspx)
