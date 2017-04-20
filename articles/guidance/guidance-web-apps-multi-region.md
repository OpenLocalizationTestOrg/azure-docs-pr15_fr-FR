<properties
   pageTitle="Application Web avec disponibilité | Architecture de référence Azure | Microsoft Azure"
   description="Architecture recommandée pour l’application web avec haute disponibilité, en cours d’exécution dans Microsoft Azure."
   services="app-service,app-service\web,sql-database" 
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/27/2016"
   ms.author="mwasson"/>

# <a name="azure-reference-architecture-web-application-with-high-availability"></a>Architecture de référence Azure : application Web avec haute disponibilité

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article vous explique une architecture recommandée pour une application web avec disponibilité, s’exécutant sur Microsoft Azure. L’architecture s’appuie sur [architecture de référence Azure : améliorer extensibilité élevées dans une application web][guidance-web-apps-scalability].

## <a name="architecture-diagram"></a>Diagramme d’architecture

![Architecture de référence : application Web avec haute disponibilité](media/blueprints/paas-web-app-multi-region.png)

Cette architecture s’appuie sur celui illustré [amélioration extensibilité élevées dans une application web][guidance-web-apps-scalability]. Les principales différences sont :

- **Zones principales et secondaires**. Cette architecture utilise deux régions pour obtenir une plus grande disponibilité. L’application est déployée sur chaque régions. Tout se déroule normalement, le trafic réseau est routé vers la zone principale. Mais si qui n’est pas disponible, le trafic est acheminé vers la zone secondaire. Pour plus d’informations sur le basculement, voir [Gestion des basculement](#managing-failover-and-failback).

- **Gestionnaire de trafic azure**. [Gestionnaire de trafic] [ traffic-manager] achemine des demandes entrantes vers la région principale. Si l’application en cours d’exécution cette zone deviendrait indisponible, le trafic Gestionnaire bascule vers la zone secondaire. 

- **Geo réplication** de base de données SQL et DocumentDB.

## <a name="recommendations"></a>Recommandations

### <a name="regional-pairing"></a>Le jumelage régionaux

Chaque région Azure est associée à une autre zone dans la même géographie. En règle générale, choisissez régions dans la même paire régionale (par exemple, US Extrême-Orient 2 et US Central). Cela les avantages suivants :

- S’il existe une panne large, récupération d’au moins une région se déconnecter de toutes les paires est prioritaire.
- Mises à jour système Azure planifié sont transférées aux régions appariées dans un ordre séquentiel, pour réduire le temps d’arrêt possibles.
- Dans la plupart des cas, les paires résident dans la même géographie, répondre aux exigences de la délégation de données.

Toutefois, assurez-vous que les deux régions prennent en charge tous les services Azure nécessaires pour votre application. Consultez [Services par région][services-by-region]. Pour plus d’informations sur les paires de régionaux, voir [entreprise continuité et récupération d’urgence (BCDR) : régions appariés Azure][regional-pairs].

### <a name="resource-groups"></a>Groupes de ressources

Envisagez de placer la région principale, zone secondaire et le trafic Manager dans des [groupes de ressources]distincts[resource groups]. Cela vous permet de gérer les ressources déployés sur chaque région comme une même collection &mdash; vous pouvez déployer les séparément, supprimer le déploiement et ainsi de suite. 

### <a name="traffic-manager"></a>Gestionnaire de trafic

**Routage.** Le trafic Manager prend en charge plusieurs [algorithmes de routage][tm-routing]. Pour le scénario décrit dans cet article, utiliser le routage de _priorité_ (anciennement appelé _basculement_ routage). Avec ce paramètre, le trafic gestionnaire envoie toutes les demandes à la région principale, sauf si le point de terminaison pour la région deviendrait inaccessible. À ce stade, il bascule automatiquement à la zone secondaire. Consultez [configurer le basculement de routage méthode][tm-configure-failover].

**Sonde d’intégrité.** Le trafic Manager utilise une sonde HTTP (ou HTTPS) pour contrôler la disponibilité de chaque point de terminaison. La sonde permet le trafic gestionnaire un test de réussite/échec pour basculer vers la zone secondaire. Il fonctionne en envoyant une requête à un chemin d’URL spécifiée. S’il obtient une réponse non-200 dans un délai d’expiration, la sonde échoue. Après quatre requêtes échecs, le trafic Gestionnaire marque le point de terminaison comme étant dégradé et bascule vers l’autre extrémité. Pour plus d’informations, voir [le trafic Gestionnaire de point de terminaison surveillance et basculement][tm-monitoring].

Pour obtenir les meilleurs résultats, créer un point de terminaison sonde état indique l’état générale de l’application et utiliser ce point de terminaison de la sonde d’intégrité. Le point de terminaison doit vérifier les dépendances critiques tels que les applications de Service d’application, file d’attente de stockage et base de données SQL. Dans le cas contraire, la sonde peut signaler un point de terminaison « correct » lorsque sont réellement une défaillance critiques parties de l’application. 

En revanche, n’utilisez la sonde santé pour vérifier les services de priorité inférieures. Par exemple, si un service de messagerie s’arrête, l’application peut basculer vers un fournisseur de deuxième ou uniquement envoyer des messages électroniques plus tard. L’application ne doivent pas probablement basculer dans cette situation. Pour plus d’informations, voir [État du point de terminaison de surveillance motif][health-endpoint-monitoring-pattern].
  
### <a name="sql-database"></a>Base de données SQL

Utiliser la [Réplication de Geo Active] [ sql-replication] pour créer un secondaire lisible dans une zone différente. Vous pouvez avoir jusqu'à quatre secondaires lisibles. Si votre base de données principale échoue, ou simplement doit être mis en mode hors connexion, vous pouvez basculer à certaines de vos bases de données secondaires. Réplication de Geo active peut être configurée pour une base de données dans n’importe quel pool élastique de base de données.

### <a name="documentdb"></a>DocumentDB

DocumentDB prend en charge la réplication geo au sein de régions. Une zone géographique est désignée comme accessible en écriture, et les autres colonnes sont réplicas en lecture seule. 

S’il existe une panne régionale, vous pouvez échouer en charge par la sélection d’une autre zone à être la région d’écriture. Le client DocumentDB SDK envoie automatiquement écrire des demandes dans la zone en cours d’écriture, vous n’avez pas besoin de mettre à jour de la configuration du client après un basculement. Pour plus d’informations, voir [données répartir globalement avec DocumentDB][docdb-geo]. 

> [AZURE.NOTE] Tout réplica appartiennent au même groupe de ressources.

### <a name="storage"></a>Espace de stockage

Espace de stockage Azure, utilisez le [stockage geo redondants accès en lecture] [ ra-grs] (RA GRS). Grâce au stockage RA GRS, les données sont répliquées à une zone secondaire. Vous avez accès en lecture seule aux données dans la zone secondaire, via un point de terminaison distincte. S’il existe une panne régionale ou un incident, l’équipe de stockage Azure peut-être déterminer réaliser un basculement de geo à la zone secondaire. Il n’existe aucune action client requise pour ce basculement.

Pour le stockage de file d’attente, créez une file d’attente sauvegarde dans la zone secondaire. Pendant le basculement, l’application peut utiliser la file d’attente sauvegarde, jusqu'à ce que la zone primaire redevient disponible. De cette façon, l’application peut toujours traiter les nouvelles requêtes. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Une architecture de plusieurs région peut fournir une plus grande disponibilité que le déploiement à une région. Si une panne régionale affecte la zone principale, vous pouvez basculer vers à la zone secondaire. Cette architecture permettent également en cas d’échec de l’application un sous-SYSTEME individuel.  
     
Il existe plusieurs approches générales à disponibilité élevée sur centres de données :      
- Actif/passif avec veille. Le trafic accède à une zone géographique, lors de l’autre attend en veille. L’application est déployée et en cours d’exécution dans la zone secondaire. Vous pouvez commencer avec un nombre d’instances de plus petite taille dans le centre de données secondaire et puis évoluer selon vos besoins. 

- Actif/passif avec de secours. Mais application n’est pas déployée jusqu'à ce que nécessaire pour le basculement. Cette approche coûts inférieure à exécuter, mais n’auront généralement plus indisponibilité lors d’une défaillance. 

- Actif/actif. Les deux régions sont actifs et requêtes sont réparties entre elles. Si un centre de données deviendrait indisponible, il est considérée se déconnecter de rotation. 

Cet article se concentre sur actif/passif avec veille à l’aide du [Gestionnaire de trafic Azure] [ traffic-manager] pour acheminer le trafic vers la zone. 

### <a name="traffic-manager"></a>Gestionnaire de trafic

Gestionnaire de trafic bascule automatiquement sur si la région principale n’est plus disponible. Quand le trafic Gestionnaire bascule, il existe un certain temps lorsque le client ne peut pas accéder à l’application, qui peut prendre quelques minutes. Deux facteurs affectent la durée totale :

- La sonde santé doit détecter que le centre de données principal est devenu inaccessible.

- Serveurs DNS doivent mettre à jour les enregistrements DNS mis en cache pour l’adresse IP, qui dépend de la DNS time to live (TTL). La durée de vie par défaut est 300 secondes (5 minutes), mais vous pouvez configurer cette valeur lorsque vous créez le profil Manager le trafic.

Pour plus d’informations, voir [à propos du trafic Gestionnaire surveillance][tm-monitoring]. 

Gestionnaire de trafic est un point de défaillance dans le système. Si le service échoue, les clients ne peuvent pas accéder à votre application pendant l’interruption. Passez en revue le [Gestionnaire de trafic SLA][tm-sla]et déterminez si l’aide du Gestionnaire de trafic seul répond à vos besoins de disponibilité. Si ce n’est pas le cas, vous pouvez ajouter une autre solution de gestion du trafic comme un retour arrière. Si le service Gestionnaire de trafic Azure échoue, changez vos enregistrements CNAME dans le système DNS pour qu’il pointe vers l’autre service de gestion du trafic. (Cette étape doit être effectuée manuellement, et votre application ne sera pas disponible jusqu'à ce que les modifications DNS sont propagées). 

### <a name="sql-database"></a>Base de données SQL

La récupération objectifs de point () et l’heure de récupération estimée (Insér) pour la base de données SQL sont décrites [ici][sql-rpo]. 

### <a name="storage"></a>Espace de stockage

Stockage de RA GRS fournit un stockage résistant, mais il est important de comprendre ce qui peut se produire pendant une panne : 

- En cas de panne de stockage, il sera un certain temps lorsque vous n’avez accès en écriture aux données. Vous pouvez toujours lire à partir du point de terminaison secondaire pendant la coupure.

- Si une coupure régionaux ou un incident affecte l’emplacement principal et les données ne peuvent pas être récupérées, l’équipe de stockage Azure peut-être déterminer réaliser un basculement de geo à la zone secondaire. 

- Réplication de données à la zone secondaire est exécutée en mode asynchrone. Par conséquent, si un basculement geo est effectué, des pertes de données sont possible, si les données ne peuvent pas être récupérées à partir de la région principale.

- Échecs temporaires, comme une panne du réseau, ne seront pas déclenchée un basculement de stockage. Concevoir votre application soit résistant aux pannes transitoires. Contournement possibles :

    - Lire à partir de l’image secondaire.

    - Basculer temporairement vers un autre compte de stockage pour les nouvelles opérations d’écriture (par exemple, pour les messages de file d’attente). 

    - Copier des données à partir de l’image secondaire à un autre compte de stockage.

    - Fournir des fonctionnalités réduites jusqu'à ce que le système restaure automatiquement.

Pour plus d’informations, voir [ce qu’il faut faire en cas de panne stockage Azure][storage-outage].

## <a name="managing-failover-and-failback"></a>Gestion de basculement et restauration

### <a name="traffic-manager"></a>Gestionnaire de trafic

Gestionnaire de trafic bascule automatiquement sur si la région principale n’est plus disponible. Par défaut, elle aussi automatiquement échouera, une fois que la zone primaire redevient disponible.

Toutefois, nous vous recommandons d’exécution du retour arrière manuel, plutôt que d’automatiquement ignore précédent. Avant l’échec précédent, vérifiez que tous les sous-systèmes d’application fonctionnent correctement. Dans le cas contraire, vous pouvez créer une situation où l’application retourne en arrière entre centres de données. 

Pour empêcher la restauration automatique, réduire manuellement la priorité de la région primaire après un événement de basculement. Par exemple, supposons que la zone primaire est de priorité 1 et le moniteur secondaire est priorité 2. Après un basculement, définir la région principale pour priority 3, afin d’empêcher la restauration automatique. Lorsque vous êtes prêt à revenir, rétablir la priorité à 1.

Les commandes suivantes mettre à jour la priorité.

**PowerShell** 

```bat
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name <endpoint> -ProfileName <profile> -ResourceGroupName <resource-group> -Type AzureEndpoints
$endpoint.Priority = 3
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

Pour plus d’informations, voir [Applets de commande Gestionnaire de trafic Azure][tm-ps].

**Azure infrastructure du langage commun**

```bat
azure network traffic-manager endpoint set --name <endpoint> --profile-name <profile> --resource-group <resource-group> --type AzureEndpoints --priority 3
```    

### <a name="sql-database"></a>Base de données SQL

Si la base de données principale échoue, effectuez un basculement manuel vers la base de données secondaire. Voir [restaurer une base de données SQL Azure ou le basculement sur secondaire][sql-failover]. Jusqu'à ce que vous le basculement, la base de données secondaire reste en lecture seule. 


<!-- links -->

[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[docdb-geo]: ../documentdb/documentdb-distribute-data-globally.md
[guidance-web-apps-scalability]: guidance-web-apps-scalability.md
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[ra-grs]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../resource-group-overview.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-failover]: ../sql-database/sql-database-disaster-recovery.md
[sql-replication]: ../sql-database/sql-database-geo-replication-overview.md
[sql-rpo]: ../sql-database/sql-database-business-continuity.md#sql-database-business-continuity-features
[storage-outage]: ../storage/storage-disaster-recovery-guidance.md
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-ps]: https://msdn.microsoft.com/en-us/library/mt125941.aspx
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
