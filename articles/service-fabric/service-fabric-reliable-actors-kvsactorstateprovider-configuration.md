<properties
   pageTitle="Vue d’ensemble de la configuration Azure Service TISSU fiable intervenants KVSActorStateProvider | Microsoft Azure"
   description="Obtenir des informations sur la configuration des intervenants dynamique Azure Service tissu de type KVSActorStateProvider."
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
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configuration des intervenants fiables--KVSActorStateProvider
Vous pouvez modifier la configuration par défaut de KVSActorStateProvider en modifiant le fichier settings.xml qui est généré dans la racine du package Microsoft Visual Studio sous le dossier de configuration pour l’acteur spécifié.

Le runtime Azure Service TISSU recherche des noms de section prédéfinie dans le fichier settings.xml et utilise les valeurs de configuration lors de la création des composants d’exécution sous-jacente.

>[AZURE.NOTE] Effectuez **pas** supprimer ou modifier les noms de section des configurations suivantes dans le fichier settings.xml généré dans la solution Visual Studio.

## <a name="replicator-security-configuration"></a>Configuration de la sécurité Replicator
Configurations de sécurité Replicator sont utilisées pour sécuriser les communications qui est utilisé lors de la réplication. Cela signifie que vous ne voyez pas l’autre le trafic de réplication, garantissant que les données sont apportées hautement disponibles soient également sécurisées pour les services.
Par défaut, une section de configuration de sécurité vide empêche la sécurité de la réplication.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuration Replicator
Configurations Replicator configurer le réplicateur qui est responsable de l’état d’un acteur état fournisseur hautement fiable.
La configuration par défaut est générée par le modèle Visual Studio et doit suffit. Cette section décrit les configurations supplémentaires sont disponibles pour régler le réplicateur.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0.015|Période de temps pour lequel le réplicateur en l’attend secondaire après avoir reçu une opération avant d’envoyer à un accusé de réception principal. N’importe quel autres accusés de réception à envoyer pour les opérations traitées dans cet intervalle sont envoyés comme une réponse.|
|ReplicatorEndpoint|N/A|Aucune valeur par défaut : paramètre requis|Adresse IP et port que le réplicateur principal et secondaire utilisera pour communiquer avec d’autres fabricants du réplica définis. Cela doit faire référence à un point de terminaison TCP ressource le manifeste de service. Consultez les [ressources de manifeste de Service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources de point de terminaison dans le manifeste de service. |
|RetryInterval|Secondes|5|Période de temps après laquelle le réplicateur retransmission un message si elle ne reçoit pas d’accusé de réception pour une opération.|
|MaxReplicationMessageSize|Octets|50 MO|Taille maximale des données de réplication qui peuvent être transmises en un seul message.|
|MaxPrimaryReplicationQueueSize|Nombre d’opérations|1024|Nombre maximal d’opérations dans la file d’attente principale. Une opération est libérée une fois que le réplicateur primaire reçu un accusé de réception auprès de tous les fabricants secondaires. Cette valeur doit être supérieure à 64 et à une puissance de 2.|
|MaxSecondaryReplicationQueueSize|Nombre d’opérations|2048|Nombre maximal d’opérations dans la file d’attente secondaire. Une opération est libérée après avoir apporté son état hautement disponibles via persistance. Cette valeur doit être supérieure à 64 et à une puissance de 2.|

## <a name="store-configuration"></a>Configuration de la banque
Configurations Store servent à configurer le magasin local qui est utilisé pour conserver l’état dans lequel est répliquée.
La configuration par défaut est générée par le modèle Visual Studio et doit suffit. Cette section décrit les configurations supplémentaires sont disponibles pour le magasin local.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Millisecondes|200|Définit la valeur maximale le traitement par lots intervalle les validations résistant magasin local.|
|MaxVerPages|Nombre de pages|16384|Le nombre maximal de pages de version de l’ordinateur local stocker la base de données. Il détermine le nombre maximal de transactions en attente.|

## <a name="sample-configuration-file"></a>Exemple de fichier de configuration

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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
