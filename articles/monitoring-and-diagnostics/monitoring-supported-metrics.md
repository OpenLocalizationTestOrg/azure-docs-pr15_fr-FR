<properties
    pageTitle="Métrique moniteur Azure - mesures pris en charge par type de ressource | Microsoft Azure"
    description="Liste des mesures disponibles pour chaque type de ressource avec Azure moniteur."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Indicateurs pris en charge avec le moniteur d’Azure

Moniteur Azure fournit plusieurs méthodes pour interagir avec les mesures, y compris les graphiques dans le portail, y accéder à l’API REST ou les requêtes à l’aide de PowerShell ou infrastructure du langage commun. Une liste complète de toutes les mesures en dessous est actuellement disponible avec pipeline métrique du moniteur Azure.

> [AZURE.NOTE] Autres mesures peuvent être disponibles dans le portail ou à l’aide d’API héritées. Cette liste inclut uniquement les indicateurs de version d’évaluation disponible à l’aide de la version d’évaluation du pipeline métrique Azure moniteur consolidée.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CoreCount|Nombre de base|Nombre|Total|Nombre total de cœurs dans le compte par lots|
|TotalNodeCount|Nombre de nœuds|Nombre|Total|Nombre total de nœuds dans le compte par lots|
|CreatingNodeCount|Nombre de nœuds de création|Nombre|Total|Nombre de nœuds en cours de création|
|StartingNodeCount|Nombre de nœuds départ|Nombre|Total|Nombre de nœuds en commençant|
|WaitingForStartTaskNodeCount|En attente pour le nombre de nœuds tâche début|Nombre|Total|Nombre de nœuds en attente de la tâche se termine Démarrer|
|StartTaskFailedNodeCount|Nombre de nœuds Échec de la tâche de début|Nombre|Total|Nombre de nœuds dans lesquels la tâche Démarrer a échoué|
|IdleNodeCount|Nombre de nœuds inactives|Nombre|Total|Nombre de nœuds inactives|
|OfflineNodeCount|Nombre de nœuds en mode hors connexion|Nombre|Total|Nombre de nœuds en mode hors connexion|
|RebootingNodeCount|Nombre de nœuds redémarrage|Nombre|Total|Nombre de nœuds de redémarrage|
|ReimagingNodeCount|Nombre de nœuds disponibles|Nombre|Total|Nombre de nœuds disponibles|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Nombre|Total|Nombre de nœuds en cours d’exécution|
|LeavingPoolNodeCount|Quitter le nombre de nœuds Pool|Nombre|Total|Nombre de nœuds quitter le Pool|
|UnusableNodeCount|Nombre de nœuds inutilisable|Nombre|Total|Nombre de nœuds inutilisables|
|TaskStartEvent|Événements de démarrage de tâche|Nombre|Total|Nombre total de tâches qui ont commencé|
|TaskCompleteEvent|Événements Complete de tâche|Nombre|Total|Nombre total de tâches terminées|
|TaskFailEvent|Tâche Fail événements|Nombre|Total|Nombre total de tâches terminées dans un état a échoué|
|PoolCreateEvent|Pool de créer des événements|Nombre|Total|Nombre total de qui ont été créées|
|PoolResizeStartEvent|Événements de démarrage pool de redimensionnement|Nombre|Total|Nombre total de redimensionne pool qui ont commencé|
|PoolResizeCompleteEvent|Événements complète du pool de redimensionnement|Nombre|Total|Nombre total de redimensionne pool qui se sont terminées|
|PoolDeleteStartEvent|Supprimer les événements début du pool|Nombre|Total|Nombre total de suppressions pool qui ont commencé|
|PoolDeleteCompleteEvent|Événements complète du pool SUPPR|Nombre|Total|Nombre total de suppressions pool qui se sont terminées|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|connectedclients|Clients connectés|Nombre|Valeur maximale||
|totalcommandsprocessed|Total des opérations|Nombre|Total||
|CacheHits|Accès au cache|Nombre|Total||
|cachemisses|Échecs du cache|Nombre|Total||
|GetCommands|Obtient|Nombre|Total||
|setcommands|Jeux|Nombre|Total||
|evictedkeys|Touches supprimés|Nombre|Total||
|totalkeys|Nombre total de clés|Nombre|Valeur maximale||
|expiredkeys|Touches qui a expiré|Nombre|Total||
|usedmemory|Mémoire utilisée|Octets|Valeur maximale||
|usedmemoryRss|Mémoire utilisée RSS|Octets|Valeur maximale||
|serverLoad|Charge du serveur|Pourcentage|Valeur maximale||
|cacheWrite|Cache d’écriture|BytesPerSecond|Valeur maximale||
|cacheRead|Lecture du cache|BytesPerSecond|Valeur maximale||
|percentProcessorTime|PROCESSEUR|Pourcentage|Valeur maximale||
|connectedclients0|Clients connectés (éclater 0)|Nombre|Valeur maximale||
|totalcommandsprocessed0|Total des opérations (éclater 0)|Nombre|Total||
|cachehits0|Accès au cache (éclater 0)|Nombre|Total||
|cachemisses0|Échecs du cache (éclater 0)|Nombre|Total||
|getcommands0|Obtient (éclater 0)|Nombre|Total||
|setcommands0|Jeux (éclater 0)|Nombre|Total||
|evictedkeys0|Touches supprimés (éclater 0)|Nombre|Total||
|totalkeys0|Nombre total de clés (nœud 0)|Nombre|Valeur maximale||
|expiredkeys0|Touches qui a expiré (éclater 0)|Nombre|Total||
|usedmemory0|Mémoire utilisée (éclater 0)|Octets|Valeur maximale||
|usedmemoryRss0|Mémoire utilisée RSS (éclater 0)|Octets|Valeur maximale||
|serverLoad0|Charge du serveur (éclater 0)|Pourcentage|Valeur maximale||
|cacheWrite0|Cache d’écriture (éclater 0)|BytesPerSecond|Valeur maximale||
|cacheRead0|Cache en lecture (éclater 0)|BytesPerSecond|Valeur maximale||
|percentProcessorTime0|Processeur (éclater 0)|Pourcentage|Valeur maximale||
|connectedclients1|Clients connectés (éclater 1)|Nombre|Valeur maximale||
|totalcommandsprocessed1|Total des opérations (éclater 1)|Nombre|Total||
|cachehits1|Accès au cache (éclater 1)|Nombre|Total||
|cachemisses1|Échecs du cache (éclater 1)|Nombre|Total||
|getcommands1|Obtient (éclater 1)|Nombre|Total||
|setcommands1|Jeux (éclater 1)|Nombre|Total||
|evictedkeys1|Touches supprimés (éclater 1)|Nombre|Total||
|totalkeys1|Nombre total de clés (nœud 1)|Nombre|Valeur maximale||
|expiredkeys1|Touches qui a expiré (éclater 1)|Nombre|Total||
|usedmemory1|Mémoire utilisée (éclater 1)|Octets|Valeur maximale||
|usedmemoryRss1|Mémoire utilisée RSS (éclater 1)|Octets|Valeur maximale||
|serverLoad1|Charge du serveur (éclater 1)|Pourcentage|Valeur maximale||
|cacheWrite1|Cache d’écriture (éclater 1)|BytesPerSecond|Valeur maximale||
|cacheRead1|Cache en lecture (éclater 1)|BytesPerSecond|Valeur maximale||
|percentProcessorTime1|Processeur (éclater 1)|Pourcentage|Valeur maximale||
|connectedclients2|Clients connectés (éclater 2)|Nombre|Valeur maximale||
|totalcommandsprocessed2|Total des opérations (éclater 2)|Nombre|Total||
|cachehits2|Accès au cache (éclater 2)|Nombre|Total||
|cachemisses2|Échecs du cache (éclater 2)|Nombre|Total||
|getcommands2|Obtient (éclater 2)|Nombre|Total||
|setcommands2|Jeux (éclater 2)|Nombre|Total||
|evictedkeys2|Touches supprimés (éclater 2)|Nombre|Total||
|totalkeys2|Nombre total de clés (nœud 2)|Nombre|Valeur maximale||
|expiredkeys2|Touches qui a expiré (éclater 2)|Nombre|Total||
|usedmemory2|Mémoire utilisée (éclater 2)|Octets|Valeur maximale||
|usedmemoryRss2|Mémoire utilisée RSS (éclater 2)|Octets|Valeur maximale||
|serverLoad2|Charge du serveur (éclater 2)|Pourcentage|Valeur maximale||
|cacheWrite2|Cache d’écriture (éclater 2)|BytesPerSecond|Valeur maximale||
|cacheRead2|Cache en lecture (éclater 2)|BytesPerSecond|Valeur maximale||
|percentProcessorTime2|Processeur (éclater 2)|Pourcentage|Valeur maximale||
|connectedclients3|Clients connectés (éclater 3)|Nombre|Valeur maximale||
|totalcommandsprocessed3|Total des opérations (éclater 3)|Nombre|Total||
|cachehits3|Accès au cache (éclater 3)|Nombre|Total||
|cachemisses3|Échecs du cache (éclater 3)|Nombre|Total||
|getcommands3|Obtient (éclater 3)|Nombre|Total||
|setcommands3|Jeux (éclater 3)|Nombre|Total||
|evictedkeys3|Touches supprimés (éclater 3)|Nombre|Total||
|totalkeys3|Nombre total de clés (nœud 3)|Nombre|Valeur maximale||
|expiredkeys3|Touches qui a expiré (éclater 3)|Nombre|Total||
|usedmemory3|Mémoire utilisée (éclater 3)|Octets|Valeur maximale||
|usedmemoryRss3|Mémoire utilisée RSS (éclater 3)|Octets|Valeur maximale||
|serverLoad3|Charge du serveur (éclater 3)|Pourcentage|Valeur maximale||
|cacheWrite3|Cache d’écriture (éclater 3)|BytesPerSecond|Valeur maximale||
|cacheRead3|Cache en lecture (éclater 3)|BytesPerSecond|Valeur maximale||
|percentProcessorTime3|Processeur (éclater 3)|Pourcentage|Valeur maximale||
|connectedclients4|Clients connectés (éclater 4)|Nombre|Valeur maximale||
|totalcommandsprocessed4|Total des opérations (éclater 4)|Nombre|Total||
|cachehits4|Accès au cache (éclater 4)|Nombre|Total||
|cachemisses4|Échecs du cache (éclater 4)|Nombre|Total||
|getcommands4|Obtient (éclater 4)|Nombre|Total||
|setcommands4|Jeux (éclater 4)|Nombre|Total||
|evictedkeys4|Touches supprimés (éclater 4)|Nombre|Total||
|totalkeys4|Nombre total de clés (nœud 4)|Nombre|Valeur maximale||
|expiredkeys4|Touches qui a expiré (éclater 4)|Nombre|Total||
|usedmemory4|Mémoire utilisée (éclater 4)|Octets|Valeur maximale||
|usedmemoryRss4|Mémoire utilisée RSS (éclater 4)|Octets|Valeur maximale||
|serverLoad4|Charge du serveur (éclater 4)|Pourcentage|Valeur maximale||
|cacheWrite4|Cache d’écriture (éclater 4)|BytesPerSecond|Valeur maximale||
|cacheRead4|Cache en lecture (éclater 4)|BytesPerSecond|Valeur maximale||
|percentProcessorTime4|Processeur (éclater 4)|Pourcentage|Valeur maximale||
|connectedclients5|Clients connectés (éclater 5)|Nombre|Valeur maximale||
|totalcommandsprocessed5|Total des opérations (éclater 5)|Nombre|Total||
|cachehits5|Accès au cache (éclater 5)|Nombre|Total||
|cachemisses5|Échecs du cache (éclater 5)|Nombre|Total||
|getcommands5|Obtient (éclater 5)|Nombre|Total||
|setcommands5|Jeux (éclater 5)|Nombre|Total||
|evictedkeys5|Touches supprimés (éclater 5)|Nombre|Total||
|totalkeys5|Nombre total de clés (nœud 5)|Nombre|Valeur maximale||
|expiredkeys5|Touches qui a expiré (éclater 5)|Nombre|Total||
|usedmemory5|Mémoire utilisée (éclater 5)|Octets|Valeur maximale||
|usedmemoryRss5|Mémoire utilisée RSS (éclater 5)|Octets|Valeur maximale||
|serverLoad5|Charge du serveur (éclater 5)|Pourcentage|Valeur maximale||
|cacheWrite5|Cache d’écriture (éclater 5)|BytesPerSecond|Valeur maximale||
|cacheRead5|Cache en lecture (éclater 5)|BytesPerSecond|Valeur maximale||
|percentProcessorTime5|Processeur (éclater 5)|Pourcentage|Valeur maximale||
|connectedclients6|Clients connectés (éclater 6)|Nombre|Valeur maximale||
|totalcommandsprocessed6|Total des opérations (éclater 6)|Nombre|Total||
|cachehits6|Accès au cache (éclater 6)|Nombre|Total||
|cachemisses6|Échecs du cache (éclater 6)|Nombre|Total||
|getcommands6|Obtient (éclater 6)|Nombre|Total||
|setcommands6|Jeux (éclater 6)|Nombre|Total||
|evictedkeys6|Touches supprimés (éclater 6)|Nombre|Total||
|totalkeys6|Nombre total de clés (nœud 6)|Nombre|Valeur maximale||
|expiredkeys6|Touches qui a expiré (éclater 6)|Nombre|Total||
|usedmemory6|Mémoire utilisée (éclater 6)|Octets|Valeur maximale||
|usedmemoryRss6|Mémoire utilisée RSS (éclater 6)|Octets|Valeur maximale||
|serverLoad6|Charge du serveur (éclater 6)|Pourcentage|Valeur maximale||
|cacheWrite6|Cache d’écriture (éclater 6)|BytesPerSecond|Valeur maximale||
|cacheRead6|Cache en lecture (éclater 6)|BytesPerSecond|Valeur maximale||
|percentProcessorTime6|Processeur (éclater 6)|Pourcentage|Valeur maximale||
|connectedclients7|Clients connectés (éclater 7)|Nombre|Valeur maximale||
|totalcommandsprocessed7|Total des opérations (éclater 7)|Nombre|Total||
|cachehits7|Accès au cache (éclater 7)|Nombre|Total||
|cachemisses7|Échecs du cache (éclater 7)|Nombre|Total||
|getcommands7|Obtient (éclater 7)|Nombre|Total||
|setcommands7|Jeux (éclater 7)|Nombre|Total||
|evictedkeys7|Touches supprimés (éclater 7)|Nombre|Total||
|totalkeys7|Nombre total de clés (nœud 7)|Nombre|Valeur maximale||
|expiredkeys7|Touches qui a expiré (éclater 7)|Nombre|Total||
|usedmemory7|Mémoire utilisée (éclater 7)|Octets|Valeur maximale||
|usedmemoryRss7|Mémoire utilisée RSS (éclater 7)|Octets|Valeur maximale||
|serverLoad7|Charge du serveur (éclater 7)|Pourcentage|Valeur maximale||
|cacheWrite7|Cache d’écriture (éclater 7)|BytesPerSecond|Valeur maximale||
|cacheRead7|Cache en lecture (éclater 7)|BytesPerSecond|Valeur maximale||
|percentProcessorTime7|Processeur (éclater 7)|Pourcentage|Valeur maximale||
|connectedclients8|Clients connectés (éclater 8)|Nombre|Valeur maximale||
|totalcommandsprocessed8|Total des opérations (éclater 8)|Nombre|Total||
|cachehits8|Accès au cache (éclater 8)|Nombre|Total||
|cachemisses8|Échecs du cache (éclater 8)|Nombre|Total||
|getcommands8|Obtient (éclater 8)|Nombre|Total||
|setcommands8|Jeux (éclater 8)|Nombre|Total||
|evictedkeys8|Touches supprimés (éclater 8)|Nombre|Total||
|totalkeys8|Nombre total de clés (nœud 8)|Nombre|Valeur maximale||
|expiredkeys8|Touches qui a expiré (éclater 8)|Nombre|Total||
|usedmemory8|Mémoire utilisée (éclater 8)|Octets|Valeur maximale||
|usedmemoryRss8|Mémoire utilisée RSS (éclater 8)|Octets|Valeur maximale||
|serverLoad8|Charge du serveur (éclater 8)|Pourcentage|Valeur maximale||
|cacheWrite8|Cache d’écriture (éclater 8)|BytesPerSecond|Valeur maximale||
|cacheRead8|Cache en lecture (éclater 8)|BytesPerSecond|Valeur maximale||
|percentProcessorTime8|Processeur (éclater 8)|Pourcentage|Valeur maximale||
|connectedclients9|Clients connectés (éclater 9)|Nombre|Valeur maximale||
|totalcommandsprocessed9|Total des opérations (éclater 9)|Nombre|Total||
|cachehits9|Accès au cache (éclater 9)|Nombre|Total||
|cachemisses9|Échecs du cache (éclater 9)|Nombre|Total||
|getcommands9|Obtient (éclater 9)|Nombre|Total||
|setcommands9|Jeux (éclater 9)|Nombre|Total||
|evictedkeys9|Touches supprimés (éclater 9)|Nombre|Total||
|totalkeys9|Nombre total de clés (nœud 9)|Nombre|Valeur maximale||
|expiredkeys9|Touches qui a expiré (éclater 9)|Nombre|Total||
|usedmemory9|Mémoire utilisée (éclater 9)|Octets|Valeur maximale||
|usedmemoryRss9|Mémoire utilisée RSS (éclater 9)|Octets|Valeur maximale||
|serverLoad9|Charge du serveur (éclater 9)|Pourcentage|Valeur maximale||
|cacheWrite9|Cache d’écriture (éclater 9)|BytesPerSecond|Valeur maximale||
|cacheRead9|Cache en lecture (éclater 9)|BytesPerSecond|Valeur maximale||
|percentProcessorTime9|Processeur (éclater 9)|Pourcentage|Valeur maximale||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|NumberOfCalls|Nombre total d’appels API|Nombre|Total|Nombre total d’appels d’API.|
|NumberOfFailedCalls|Nombre total d’appels API a échoué|Nombre|Total|Nombre total d’appels d’API a échoué.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Pourcentage du processeur|Pourcentage du processeur|Pourcentage|Moyenne|Le pourcentage d’unités allouées cluster qui sont actuellement en cours d’utilisation par les ordinateurs virtuels|
|Réseau dans|Réseau dans|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Déconnecter du réseau|Déconnecter du réseau|Octets|Total|Le nombre d’octets sur toutes les interfaces réseau par les ordinateurs virtuels (le trafic sortant)|
|Octets de lecture de disque|Octets de lecture de disque|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|
|Octets écriture sur disque|Octets écriture sur disque|Octets|Total|Nombre total d’octets écrit sur le disque pendant la période d’analyse|
|Opérations de lecture/s de disque|Opérations de lecture/s de disque|CountPerSecond|Moyenne|Lecture du disque sorties par|
|Opérations d’écriture disque/s|Opérations d’écriture disque/s|CountPerSecond|Moyenne|Écriture disque sorties par|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Pourcentage du processeur|Pourcentage du processeur|Pourcentage|Moyenne|Le pourcentage d’unités allouées cluster qui sont actuellement en cours d’utilisation par les ordinateurs virtuels|
|Réseau dans|Réseau dans|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Déconnecter du réseau|Déconnecter du réseau|Octets|Total|Le nombre d’octets sur toutes les interfaces réseau par les ordinateurs virtuels (le trafic sortant)|
|Octets de lecture de disque|Octets de lecture de disque|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|
|Octets écriture sur disque|Octets écriture sur disque|Octets|Total|Nombre total d’octets écrit sur le disque pendant la période d’analyse|
|Opérations de lecture/s de disque|Opérations de lecture/s de disque|CountPerSecond|Moyenne|Lecture du disque sorties par|
|Opérations d’écriture disque/s|Opérations d’écriture disque/s|CountPerSecond|Moyenne|Écriture disque sorties par|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Pourcentage du processeur|Pourcentage du processeur|Pourcentage|Moyenne|Le pourcentage d’unités allouées cluster qui sont actuellement en cours d’utilisation par les ordinateurs virtuels|
|Réseau dans|Réseau dans|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Déconnecter du réseau|Déconnecter du réseau|Octets|Total|Le nombre d’octets sur toutes les interfaces réseau par les ordinateurs virtuels (le trafic sortant)|
|Octets de lecture de disque|Octets de lecture de disque|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|
|Octets écriture sur disque|Octets écriture sur disque|Octets|Total|Nombre total d’octets écrit sur le disque pendant la période d’analyse|
|Opérations de lecture/s de disque|Opérations de lecture/s de disque|CountPerSecond|Moyenne|Lecture du disque sorties par|
|Opérations d’écriture disque/s|Opérations d’écriture disque/s|CountPerSecond|Moyenne|Écriture disque sorties par|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Tentatives d’envoi de Message de télémétrie|Nombre|Total|Numéro du périphérique vers le Cloud messages de télémétrie essayé envoyées à votre plateforme IoT|
|d2c.telemetry.Ingress.Success|Messages de télémétrie envoyés|Nombre|Total|Nombre de périphérique aux messages de télémétrie Cloud envoyé à votre concentrateur IoT|
|c2d.Commands.egress.Complete.Success|Commandes terminés|Nombre|Total|Nombre de Cloud commandes appareil terminée par le périphérique|
|c2d.Commands.egress.Abandon.Success|Commandes abandonnées|Nombre|Total|Nombre de Cloud commandes appareil abandonnés par le périphérique|
|c2d.Commands.egress.Reject.Success|Commandes refusées|Nombre|Total|Nombre de Cloud commandes appareil rejeté par le périphérique|
|devices.totalDevices|Appareils total|Nombre|Total|Nombre d’unités enregistrées à votre concentrateur IoT|
|devices.connectedDevices.allProtocol|Appareils connectés|Nombre|Total|Nombre d’appareils connectés à votre plateforme IoT|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|INREQS|Demandes entrantes|Nombre|Total|Événement concentrateur entrant débit des messages pour un espace de noms|
|SUCCREQ|Demandes réussies|Nombre|Total|Nombre total de demandes réussi pour un espace de noms|
|FAILREQ|Échecs de requêtes|Nombre|Total|Total échecs de requêtes pour un espace de noms|
|SVRBSY|Erreurs occupé (e) du serveur|Nombre|Total|Erreurs occupé (e) du serveur total pour un espace de noms|
|INTERR|Erreurs internes du serveur|Nombre|Total|Erreurs du serveur interne total pour un espace de noms|
|MISCERR|Autres erreurs|Nombre|Total|Total échecs de requêtes pour un espace de noms|
|INMSGS|Messages entrants|Nombre|Total|Nombre total de messages entrant pour un espace de noms|
|OUTMSGS|Messages sortants|Nombre|Total|Total messages pour un espace de noms sortants|
|EHINMBS|Octets entrants par seconde|BytesPerSecond|Total|Événement concentrateur entrant débit des messages pour un espace de noms|
|EHOUTMBS|Octets sortants par seconde|BytesPerSecond|Total|Total messages pour un espace de noms sortants|
|EHABL|Archiver les messages en retard|Nombre|Total|Événement concentrateur archiver les messages en souffrance pour un espace de noms|
|EHAMSGS|Archivage des messages|Nombre|Total|Concentrateur événement des messages dans un espace de noms archivés|
|EHAMBS|Débit archivage des messages|BytesPerSecond|Total|Débit des messages archivés concentrateur événement dans un espace de noms|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|RunsStarted|S’exécute en main|Nombre|Total|Nombre de flux de travail s’exécute en main.|
|RunsCompleted|S’exécute terminé|Nombre|Total|Nombre de flux de travail s’exécute terminée.|
|RunsSucceeded|S’exécute a réussi.|Nombre|Total|Nombre de flux de travail s’exécute a réussi.|
|RunsFailed|S’exécute a échoué|Nombre|Total|Nombre de flux de travail s’exécute a échoué.|
|RunsCancelled|S’exécute annulé|Nombre|Total|Nombre de flux de travail s’exécute annulée.|
|RunLatency|Exécuter la latence|Secondes|Moyenne|Latence de flux de travail terminé s’exécute.|
|RunSuccessLatency|Exécuter la latence succès|Secondes|Moyenne|Latence de flux de travail a réussi s’exécute.|
|RunThrottledEvents|Exécuter les événements accélérées|Nombre|Total|Nombre d’actions de flux de travail ou un déclencheur limité événements.|
|RunFailurePercentage|Exécuter l’échec du pourcentage|Pourcentage|Total|Pourcentage de flux de travail s’exécute a échoué.|
|ActionsStarted|Actions en main |Nombre|Total|Nombre d’actions de flux de travail démarré.|
|ActionsCompleted|Actions terminées |Nombre|Total|Nombre d’actions de flux de travail terminé.|
|ActionsSucceeded|Actions a réussi. |Nombre|Total|Nombre d’actions de flux de travail a réussi.|
|ActionsFailed|Actions a échoué|Nombre|Total|Nombre d’actions de flux de travail a échoué.|
|ActionsSkipped|Actions ignorées |Nombre|Total|Nombre d’actions de flux de travail ignorés.|
|ActionLatency|Latence d’action |Secondes|Moyenne|Latence des actions de flux de travail terminé.|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Moyenne|Latence des actions de flux de travail a réussi.|
|ActionThrottledEvents|Action limitée des événements|Nombre|Total|Nombre d’actions de flux de travail limité événements...|
|TriggersStarted|Déclencheurs en main |Nombre|Total|Nombre de flux de travail déclencheurs démarré.|
|TriggersCompleted|Déclencheurs terminés |Nombre|Total|Nombre de déclencheurs de flux de travail terminé.|
|TriggersSucceeded|Déclencheurs a réussi. |Nombre|Total|Nombre de flux de travail déclencheurs a réussi.|
|TriggersFailed|Déclencheurs a échoué |Nombre|Total|Nombre de déclencheurs de flux de travail a échoué.|
|TriggersSkipped|Déclencheurs ignorés|Nombre|Total|Nombre de flux de travail déclencheurs ignoré.|
|TriggersFired|Déclencheurs déclenchés |Nombre|Total|Nombre de flux de travail déclencheurs déclenché.|
|TriggerLatency|Latence de déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail terminé.|
|TriggerFireLatency|Déclencheur Fire latence |Secondes|Moyenne|Latence de déclenche déclencheurs de flux de travail.|
|TriggerSuccessLatency|Latence de réussite déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail a réussi.|
|TriggerThrottledEvents|Déclencher la lecture d’événements accélérées|Nombre|Total|Nombre de déclenchement de flux de travail limité événements.|
|BillableActionExecutions|Action facturables exécutions|Nombre|Total|Nombre d’exécutions d’action de flux de travail prise facturés.|
|BillableTriggerExecutions|Exécutions du déclencheur facturables|Nombre|Total|Nombre d’exécutions du déclencheur du flux de travail prise facturés.|
|TotalBillableExecutions|Nombre total d’exécutions facturable|Nombre|Total|Nombre d’exécutions de flux de travail prise facturés.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Débit|Débit|BytesPerSecond|Moyenne||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Moyenne|Latence moyenne de recherche pour le service de recherche|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Moyenne|Requêtes de recherche par seconde pour le service de recherche|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche accélérée|Pourcentage|Moyenne|Pourcentage de requêtes de recherche qui ont été limitée pour le service de recherche|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CPUXNS|Utilisation de l’UC par espace de noms|Pourcentage|Valeur maximale|Métrique d’utilisation du processeur de l’espace de noms de service bus premium|
|WSXNS|Utilisation de la taille de la mémoire par un espace de noms|Pourcentage|Valeur maximale|Métrique d’utilisation de mémoire service bus premium espace de noms|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage processeur|Pourcentage|Moyenne|Pourcentage processeur|
|physical_data_read_percent|Pourcentage IO de données|Pourcentage|Moyenne|Pourcentage IO de données|
|log_write_percent|Journal IO pourcentage|Pourcentage|Moyenne|Journal IO pourcentage|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|espace de stockage|Taille totale de la base de données|Octets|Valeur maximale|Taille totale de la base de données|
|connection_successful|Connexions réussies|Nombre|Total|Connexions réussies|
|connection_failed|Échec de connexion|Nombre|Total|Échec de connexion|
|blocked_by_firewall|Bloqué par le pare-feu|Nombre|Total|Bloqué par le pare-feu|
|blocage|Blocages|Nombre|Total|Blocages|
|storage_percent|Pourcentage de taille de base de données|Pourcentage|Valeur maximale|Pourcentage de taille de base de données|
|xtp_storage_percent|Percent(Preview) de stockage en mémoire OLTP|Pourcentage|Moyenne|Percent(Preview) de stockage en mémoire OLTP|
|workers_percent|Pourcentage travailleurs|Pourcentage|Moyenne|Pour cent travailleurs|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|
|dtu_limit|Limite DTU|Nombre|Moyenne|Limite DTU|
|dtu_used|DTU utilisé|Nombre|Moyenne|DTU utilisé|
|service_level_objective|Objectif de niveau de service de la base de données|Nombre|Total|Objectif de niveau de service de la base de données|
|dwu_limit|limite de dwu|Nombre|Valeur maximale|limite de dwu|
|dwu_consumption_percent|Pourcentage DWU|Pourcentage|Moyenne|Pourcentage DWU|
|dwu_used|DWU utilisé|Nombre|Moyenne|DWU utilisé|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage processeur|Pourcentage|Moyenne|Pourcentage processeur|
|physical_data_read_percent|Pourcentage IO de données|Pourcentage|Moyenne|Pourcentage IO de données|
|log_write_percent|Journal IO pourcentage|Pourcentage|Moyenne|Journal IO pourcentage|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|
|workers_percent|Pour cent travailleurs|Pourcentage|Moyenne|Pour cent travailleurs|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|
|eDTU_limit|limite d’eDTU|Nombre|Moyenne|limite d’eDTU|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|
|eDTU_used|eDTU utilisé|Nombre|Moyenne|eDTU utilisé|
|storage_used|Espace de stockage utilisé|Octets|Moyenne|Espace de stockage utilisé|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|ResourceUtilization|Taux d’utilisation % so|Pourcentage|Valeur maximale|Taux d’utilisation % so|
|InputEvents|Événements d’entrée|Nombre|Total|Événements d’entrée|
|InputEventBytes|Octets événement d’entrée|Octets|Total|Octets événement d’entrée|
|LateInputEvents|Événements d’entrée en retard|Nombre|Total|Événements d’entrée en retard|
|OutputEvents|Événements de sortie|Nombre|Total|Événements de sortie|
|ConversionErrors|Erreurs de Conversion de données|Nombre|Total|Erreurs de Conversion de données|
|Erreurs|Erreurs d’exécution|Nombre|Total|Erreurs d’exécution|
|DroppedOrAdjustedEvents|Événements désordre|Nombre|Total|Événements désordre|
|AMLCalloutRequests|Demandes de fonction|Nombre|Total|Demandes de fonction|
|AMLCalloutFailedRequests|Demandes de fonction qui a échoué|Nombre|Total|Demandes de fonction qui a échoué|
|AMLCalloutInputEvents|Événements de fonction|Nombre|Total|Événements de fonction|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuPercentage|Pourcentage processeur|Pourcentage|Moyenne|Pourcentage processeur|
|MemoryPercentage|Pourcentage de la mémoire|Pourcentage|Moyenne|Pourcentage de la mémoire|
|DiskQueueLength|Long.|Nombre|Total|Long.|
|HttpQueueLength|File d’attente http|Nombre|Total|File d’attente http|
|BytesReceived|Données dans|Octets|Total|Données dans|
|Octets envoyés|Données|Octets|Total|Données|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|
|Demandes|Demandes|Nombre|Total|Demandes|
|BytesReceived|Données dans|Octets|Total|Données dans|
|Octets envoyés|Données|Octets|Total|Données|
|Http2xx|2xx http|Nombre|Total|2xx http|
|Http3xx|3xx http|Nombre|Total|3xx http|
|Http401|HTTP 401|Nombre|Total|HTTP 401|
|Http403|HTTP 403|Nombre|Total|HTTP 403|
|Http404|HTTP 404|Nombre|Total|HTTP 404|
|Http406|HTTP 406|Nombre|Total|HTTP 406|
|Http4xx|4xx http|Nombre|Total|4xx http|
|Http5xx|Erreurs de serveur HTTP|Nombre|Total|Erreurs de serveur HTTP|
|MemoryWorkingSet|Utilisation de la mémoire|Octets|Total|Utilisation de la mémoire|
|AverageMemoryWorkingSet|Mémoire moyenne jeu de travail|Octets|Moyenne|Mémoire moyenne jeu de travail|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrique|Nom d’affichage métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|
|Demandes|Demandes|Nombre|Total|Demandes|
|BytesReceived|Données dans|Octets|Total|Données dans|
|Octets envoyés|Données|Octets|Total|Données|
|Http2xx|2xx http|Nombre|Total|2xx http|
|Http3xx|3xx http|Nombre|Total|3xx http|
|Http401|HTTP 401|Nombre|Total|HTTP 401|
|Http403|HTTP 403|Nombre|Total|HTTP 403|
|Http404|HTTP 404|Nombre|Total|HTTP 404|
|Http406|HTTP 406|Nombre|Total|HTTP 406|
|Http4xx|4xx http|Nombre|Total|4xx http|
|Http5xx|Erreurs de serveur HTTP|Nombre|Total|Erreurs de serveur HTTP|
|MemoryWorkingSet|Utilisation de la mémoire|Octets|Total|Utilisation de la mémoire|
|AverageMemoryWorkingSet|Mémoire moyenne jeu de travail|Octets|Moyenne|Mémoire moyenne jeu de travail|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les indicateurs dans le moniteur d’Azure](./monitoring-overview.md#monitoring-sources)
- [Créer des alertes sur indicateurs](./insights-receive-alert-notifications.md)
- [Exporter métriques de stockage, concentrateur événement ou journal Analytique](./monitoring-overview-of-diagnostic-logs.md)
