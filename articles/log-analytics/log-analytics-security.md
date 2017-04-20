<properties
    pageTitle="Ouvrez une session sécurité des données Analytique | Microsoft Azure"
    description="Découvrez comment journal Analytique protège votre confidentialité et protège vos données."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Ouvrez une session sécurité des données Analytique

Microsoft s’engage à protéger votre confidentialité et sécurisez vos données, lors de l’exécution des logiciels et services qui vous aident à gérer l’infrastructure informatique de votre organisation. Nous reconnaissent que lorsque vous confier vos données à d’autres personnes, cette approbation doit être sécurisé rigoureux. Microsoft est conforme aux directives de sécurité et conformité strictes — depuis le codage à un service.

Sécuriser et protection des données sont une priorité de Microsoft. Contactez-nous avec les questions, des suggestions ou des problèmes sur les informations suivantes, y compris les mesures de sécurité à [options de support technique Azure](http://azure.microsoft.com/support/options/).

Cet article explique comment les données sont collectées, traitées et sécurisées par journal Analytique dans la Suite de gestion des opérations (OMS). Vous pouvez utiliser agents pour vous connecter au service web, utilisez System Center Operations Manager pour collecter les données opérationnelles ou récupérer des données à partir d’Azure diagnostics pour une utilisation par journal Analytique. Les données collectées sont envoyées via Internet à l’aide de l’authentification basée sur le certificat et 3 SSL dans le service de journal Analytique, qui est hébergé dans Microsoft Azure. Données sont compressées par l’agent avant d’être envoyé.

Le service de journal Analytique gère vos données sur le nuage en toute sécurité à l’aide des méthodes suivantes :

- répartition des données
- conservation des données
- sécurité physique
- gestion des incidents
- conformité
- certification des normes de sécurité


## <a name="data-segregation"></a>Répartition des données

Données client sont conservées logiquement séparées sur chaque composant dans l’ensemble du service OMS. Toutes les données sont référencées par l’organisation. Ce marquage persiste tout au long du cycle de vie des données, et elle est appliquée à chaque calque du service. Chaque client possède un blob Azure dédié qui héberge les données à long terme

## <a name="data-retention"></a>Conservation des données

Données de recherche de journal indexées sont stockées et conservées en fonction de votre plan de tarification. Pour plus d’informations, voir [Journal Analytique tarifs](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft supprime des données client 30 jours après la fermeture de l’espace de travail OMS. Microsoft supprime également le compte de stockage Azure où résident les données. Lorsque les données client sont supprimées, aucun des lecteurs physiques ne sont supprimés.

Le tableau suivant répertorie certains des solutions disponibles dans OMS et exemples les types de données collectées.

| **Solution** | **Types de données** |
| --- | --- |
| Évaluation de configuration | Données de configuration, les métadonnées et les données d’état |
| Planification de la capacité | Les données de performance et les métadonnées |
| Contre les logiciels malveillants | Données de configuration et de métadonnées |
| Évaluation du système de mise à jour | Données d’état et de métadonnées |
| Gestion des journaux | Définies par l’utilisateur les journaux d’événements, les journaux d’événements Windows et/ou journaux IIS |
| Suivi des modifications | Inventaire des logiciels et métadonnées du Service Windows |
| Évaluation de Active Directory et SQL | Données WMI, les données du Registre, les données de performance et gestion dynamique SQL Server afficher les résultats |

Le tableau suivant répertorie des exemples de types de données :

| **Type de données** | **Champs** |
| --- | --- |
| Alerte | Alerte nom, Description de l’alerte, BaseManagedEntityId, identification du problème, IsMonitorAlert, ID de la règle, ResolutionState, priorité, gravité, catégorie, propriétaire, résolu par, heure de déclenchement, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuration | CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Événement | EventId, EventOriginalID, BaseManagedEntityInternalId, ID de la règle, PublisherId, éditeur, FullNumber, nombre, catégorie, ChannelLevel, LoggingComputer, EventData, objets EventParameter, TimeGenerated, TimeAdded <br>**Remarque :** Lorsque vous vous connectez événements avec des champs personnalisés dans le journal des événements Windows, OMS collecte les. |
| Métadonnées | BaseManagedEntityId, ObjectStatus, unité d’organisation, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, adresse IP, ForestDNSName, Nom_ordinateur_netbios, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adresse IP, NetbiosDomainName, LogicalProcessors, NomDNS, DisplayName, DomainDnsName, ActiveDirectorySite, au PrincipalName, OffsetInMinuteFromGreenwichTime |
| Performances | Nom_objet, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| État | StateChangeEventId, identifiant d’état, NewHealthState, OldHealthState, contexte, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Sécurité physique

Le journal Analytique dans service OMS est constituée par le personnel de Microsoft et toutes les activités sont connectées et être etc. Le service s’exécute complètement dans Azure et respecte les critères d’ingénierie communs Azure. Vous pouvez afficher plus d’informations sur la sécurité physique des biens Azure page 18 de [Présentation de la sécurité Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Droits d’accès physique pour sécuriser les zones sont modifiés au sein d’un jour ouvré pour toutes les personnes qui n’a plus responsabilité pour le service OMS, y compris les transfert et arrêt. Vous pouvez en savoir plus sur l’infrastructure physique global que nous utilisons en [Centres de données Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gestion des incidents

OMS a un processus de gestion des incidents qui respectent tous les services Microsoft. Pour résumer, nous :

- Utiliser un modèle de partage des responsabilités où une partie du responsable de la sécurité appartient Microsoft pendant une partie appartient au client
- Gérer des incidents de sécurité Azure
  - Détecter un incident dès le premier signe, pour démarrer une enquête
  - Évaluez l’impact et la gravité d’un incident par un membre de l’équipe sur appel incidents. Basé sur une preuve, l’évaluation peut ou peut ne pas donner davantage mise à niveau vers l’équipe de réponse sécurité.
  - Diagnostiquer un incident par des experts de réponse de sécurité pour mener l’investigation technique ou post-mortem, identifier les stratégies imbrication d’atténuation et la solution de contournement. Si l’équipe de sécurité pense données client ont être exposées à une personne illégale ou non autorisée, l’exécution du processus de Notification d’Incident client en parallèle commence en parallèle.  
  - Placez son et récupérer à partir de l’incident. L’équipe de réponse aux incidents crée un plan de récupération pour résoudre le problème. Étapes d’imbrication crise tels que la mise en quarantaine systèmes concernés peuvent se produire immédiatement et en parallèle avec diagnostic. Plus minimisation de magasin de termes peut-être planifiée qui se produisent une fois le risque immédiat dépassé.  
  - Fermez l’incident et réaliser une analyse post-mortem. L’équipe de réponse aux incidents crée une analyse post-mortem qui met en évidence les détails de l’incident, dans le but de réviser les stratégies, les procédures et processus pour empêcher une répétition de l’événement.
- Informer les clients des incidents de sécurité
  - Déterminer l’étendue de clients concernés et pour fournir toute personne qui a un impact sur détaillées un avis que possible
  - Créer un avis pour fournir aux clients de détails suffisamment d’informations afin qu’ils peuvent effectuer une enquête sur leur fin et respecter les engagements qu’ils ont apportées aux utilisateurs finaux lors pas trop retarder le processus de notification.
  - Confirmer et déclarer l’incident, comme bon vous semble.
  - Informez vos clients avec une notification incidents sans retard excessive et conformément à aucun engagement légal ou contractuel. Notification des incidents de sécurité est remise à une ou plusieurs des administrateurs d’un client par tout moyen que sélectionne Microsoft, y compris par courrier électronique.
- Préparation de l’équipe conduite et formation
  - Personnel Microsoft est requis pour terminer la sécurité et la formation de sensibilisation, qui permet de les identifier et signaler des problèmes de sécurité suspect.  
  - Opérateurs travaillant sur le service Microsoft Azure disposez obligations de formation addition environnant leur accès aux systèmes sensibles hébergeant des données client.
  - Personnel de réponse de sécurité Microsoft reçoit une formation spécialisée pour leurs rôles


En cas de perte de données de n’importe quel client, nous informer chaque client dans la journée. Toutefois, perte de données client s’est produite jamais avec OMS. En outre, nous conserver des copies de données qui a été créées et il est distribué géographiquement.

Pour plus d’informations sur la façon dont Microsoft répond aux incidents de sécurité, voir [Microsoft Azure Security Response dans le Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Conformité

Programme de sécurité et de gouvernance d’informations OMS logiciel service et développement de l’équipe en charge ses besoins professionnels et est conforme aux lois et réglementations décrits sur le site [Centre de gestion de la confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) et [Conformité du centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Comment OMS établit en matière de sécurité identifie les contrôles de sécurité, gère et surveille risques sont également décrites il. Tous les ans, nous mener une révision de stratégies, normes et procédures des instructions.

Chaque membre de l’équipe développement OMS reçoit formation sur la sécurité formelle pour l’application. En interne, nous utilisons un système de contrôle de version pour le développement de logiciels. Chaque projet de logiciel est protégé par le système de contrôle de version.

Microsoft dispose d’une équipe de sécurité et conformité qui surveille et évalue tous les services Microsoft. Responsables de la sécurité informations composent l’équipe et ils ne sont pas associés avec les départements ingénieries développement OMS. Les responsables de la sécurité ont leur propre chaîne de gestion et réaliser des évaluations indépendantes de produits et services pour assurer la sécurité et conformité.

Conseil de Microsoft d’administration est informé par et programmes de rapport annuel sur l’ensemble de la sécurité des informations à Microsoft.

L’équipe de développement et de service de logiciel OMS travaille activement avec les équipes Microsoft légales et de conformité et autres partenaires d’acquérir une variété de certification.

## <a name="security-standards-certifications"></a>Certification des normes de sécurité

Connectez-vous Analytique OMS actuellement répondre aux normes de sécurité suivantes :

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) et [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) compatibles
- Paiement carte secteur compatible données sécurité Standard (PCI DSS) par le Conseil de normes de sécurité PCI.
- [Type de Service organisation contrôles (sécurité sociale) 1 1 et 2 de sécurité sociale Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) compatibles
- Critères techniques communs de Windows
- Certification Microsoft digne de confiance informatique
- Comme un service Azure, les composants OMS utilise respectent les exigences de conformité Azure. Vous pouvez en savoir plus en [Conformité du centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Le cloud computing des flux de données de sécurité
L’illustration ci-dessous présente une architecture de sécurité cloud en tant que le flux des informations de votre entreprise et comment il est sécurisé étant déplace vers le service de journal Analytique, finalement visible par vous dans le portail OMS. Plus d’informations sur chaque étape suit le diagramme.

![Image de la collecte de données OMS et de sécurité](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. vous inscrire journal Analytique et collecter des données

Pour votre organisation d’envoyer des données au journal Analytique, vous configurez les agents Windows, agents s’exécutant sur Azure machines virtuelles ou d’Agents OMS pour Linux. Si vous utilisez agents Operations Manager, vous allez utiliser un Assistant de configuration dans la console pour configurer les. Les utilisateurs (qui peuvent être vous, d’autres utilisateurs individuels ou un groupe de personnes) créer un ou plusieurs comptes OMS (espaces de travail OMS) et d’enregistrent des agents en utilisant l’un des comptes suivants :


- [ID d’organisation](../active-directory/sign-up-organization.md)

- [Compte Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un espace de travail OMS est l’endroit où données sont collectées, agrégées, analysées et présentées. Un espace de travail est utilisé principalement comme un moyen de données de partition, et chaque espace de travail est unique. Par exemple, vous souhaiterez peut-être avoir vos données de production gérées avec un espace de travail OMS et vos données de test gérées avec un autre espace de travail. Espaces de travail également un administrateur contrôler l’utilisateur accès aux données. Chaque espace de travail peut avoir plusieurs comptes d’utilisateur associés, et chaque compte utilisateur peut accéder à plusieurs espace de travail OMS. Créer des espaces de travail en fonction de région du centre de données. Chaque espace de travail est répliquée vers d’autres centres de données dans la région, principalement OMS disponibilité des services.

Pour Operations Manager, une fois l’Assistant configuration terminée, chaque groupe d’administration Operations Manager établit une connexion avec le service de journal Analytique. Puis, vous utilisez l’Assistant Ajouter des ordinateurs pour choisir quels ordinateurs dans le groupe d’administration sont autorisés à envoyer des données au service. Pour les autres types de l’agent, chacune se connecte en toute sécurité au service OMS.

Toutes les communications entre systèmes connectés et le service de journal Analytique sont chiffrée.  Le protocole TLS (HTTPS) est utilisé pour le chiffrement.  Le processus SDL de Microsoft est suivi pour vous assurer que le journal Analytique est à jour avec les plus récentes avancées dans les protocoles de chiffrement.

Chaque type d’agent de collecte des données pour journal Analytique. Le type de données qui sont collectées dépend des types de solutions utilisées. Vous pouvez consulter un résumé de la collecte de données au [solutions ajouter journal Analytique à partir de la galerie de Solutions](log-analytics-add-solutions.md). En outre, plus d’informations de collection de sites sont disponibles pour la plupart des solutions. Une solution est un ensemble de vues prédéfinies, journal des requêtes de recherche, les règles de collecte de données et une logique de traitement. Seuls les administrateurs peuvent utiliser journal Analytique importer une solution. Une fois la solution est importée, il est déplacé vers les serveurs d’administration Operations Manager (le cas échéant), puis à tous les agents que vous avez choisi. Ensuite, les agents collectent les données.

## <a name="2-send-data-from-agents"></a>2. envoyer des données à partir d’agents

Vous enregistrez tous les types de l’agent avec une clé d’inscription et établir une connexion sécurisée entre l’agent et le service de journal Analytique à l’aide de l’authentification basée sur le certificat et SSL avec le port 443. OMS utilise un magasin secret pour générer et mettre à jour des clés. Les clés privées pivotent tous les 90 jours et sont stockés dans Azure et sont gérés par les opérations Azure qui suivent stricts pratiques réglementations et conformité.

Avec Operations Manager, vous enregistrez un espace de travail avec le service de journal Analytique et une connexion sécurisée HTTPS est établie entre le serveur d’administration Operations Manager.

Pour les agents Windows s’exécutant sur Azure machines virtuelles, une clé de stockage en lecture seule est utilisée pour lire des événements de diagnostic dans les tables Azure.

Si un agent ne parvient pas à communiquer avec le service pour une raison quelconque, les données collectées sont stockées localement dans un cache temporaire et le serveur d’administration essaie de renvoyer les données toutes les minutes 8 pendant 2 heures. Données mises en cache de l’agent sont protégées par la banque d’informations d’identification du système d’exploitation. Si le service ne peut pas traiter les données après 2 heures, les agents met file d’attente les données. Si la file d’attente est pleine, OMS démarre déplacer des types de données, en commençant par les données de performance. La limite de file d’attente de l’agent est une clé de Registre afin de pouvoir modifier, si nécessaire. Les données collectées sont compressées et envoyées au service, en ignorant les bases de données locale, afin qu’il n’ajoute pas de charge leur. Une fois les données collectées sont envoyées, celui-ci est supprimé à partir du cache.

Comme décrit ci-dessus, les données à partir de vos agents sont envoyées aux centres de données Microsoft Azure via le protocole SSL. Si vous le souhaitez, vous pouvez utiliser ExpressRoute pour renforcer la sécurité pour les données. ExpressRoute consiste à se connecter directement à Azure à partir de votre réseau étendu existant, comme un protocole à plusieurs étiquettes changement (MPLS) VPN, fournie par un fournisseur de services de réseau. Pour plus d’informations, voir [ExpressRoute](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. le service de journal Analytique reçoit et traite les données

Le service de journal Analytique garantit entrant des données d’une source fiable en validant l’intégrité des données avec l’authentification Azure et certificats. Les données brutes non traitées sont ensuite stockées comme un objet blob dans le [Stockage Azure Microsoft](../storage/storage-introduction.md) et ne sont pas chiffrées. Toutefois, chaque blob Azure stockage possède un ensemble d’un ensemble unique de clés qui est accessible uniquement à l’utilisateur. Le type de données qui sont stockées dépend des types de solutions qui ont été importées et utilisées pour recueillir des données. Ensuite, le service de journal Analytique traite les données brutes pour le blog de stockage Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utilisez Analytique journal pour accéder aux données

Vous pouvez vous connecter au journal Analytique dans le portail OMS en utilisant le compte professionnel ou compte Microsoft que vous avez configuré précédemment. Tout le trafic entre le portail OMS et journal Analytique dans OMS est envoyé sur un canal sécurisé HTTPS. Lorsque vous utilisez le portail OMS, un ID de session est généré sur le client de l’utilisateur (navigateur web) et les données sont stockées dans un cache local jusqu'à la fin de la session. Lorsque terminée, le cache est supprimé. Cookies côté client, qui ne contiennent pas les informations d’identification personnelle, ne sont pas supprimées automatiquement. Les cookies de session sont marqués HTTPOnly et sont sécurisés. Après une période d’inactivité prédéterminée, la session de portail OMS est terminée.

À l’aide du portail OMS, vous pouvez exporter des données dans un fichier CSV et vous pouvez accéder aux données à l’aide des API de recherche. Exportation de fichiers CSV est limitée à 50 000 lignes par exporter et données API sont limitées à 5 000 lignes par recherche.

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main Analytique journal](log-analytics-get-started.md) pour en savoir plus sur les journaux Analytique et get opérationnel en quelques minutes.
