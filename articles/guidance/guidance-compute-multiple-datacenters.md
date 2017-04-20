<properties
   pageTitle="Machines virtuelles Windows en cours d’exécution dans plusieurs zones de disponibilité | Architecture de référence | Microsoft Azure"
   description="Comment déployer des machines virtuelles dans plusieurs zones sur Azure pour la disponibilité et la résilience."
   services=""
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
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>Machines virtuelles Windows en cours d’exécution dans plusieurs zones de disponibilité

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Machines virtuelles Linux en cours d’exécution dans plusieurs zones de disponibilité](guidance-compute-multiple-datacenters-linux.md)
- [Machines virtuelles Windows en cours d’exécution dans plusieurs zones de disponibilité](guidance-compute-multiple-datacenters.md)

Dans cet article, nous vous recommandons d’un ensemble de pratiques pour s’exécuter machines virtuelles Windows (machines virtuelles) en plusieurs zones Azure, pour obtenir une disponibilité et une infrastructure de reprise robuste.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource groups] et classique. Cet article utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Une architecture de plusieurs région peut fournir une plus grande disponibilité que le déploiement à une seule région. Si une panne régionale affecte la zone principale, vous pouvez utiliser [Le trafic Manager] [ traffic-manager] pour basculer vers la zone secondaire. Cette architecture permettent également en cas d’échec de l’application un sous-SYSTEME individuel. 

Il existe plusieurs approches générales de disponibilité élevée sur centres de données :

- Actif/passif avec veille. Le trafic accède à une zone géographique, lors de l’autre attend en veille. Machines virtuelles dans la région secondaire sont en cours d’exécution et alloué à tout moment.

- Actif/passif avec de secours. Mais machines virtuelles dans la zone secondaire ne sont pas affectés jusqu'à ce que nécessaire pour le basculement. Cette approche coûts inférieure à exécuter, mais n’auront généralement plus indisponibilité lors d’une défaillance.

- Actif/actif. Les deux régions sont actifs et requêtes sont réparties entre elles. Si un centre de données deviendrait indisponible, il est considérée se déconnecter de rotation. 

Cette architecture se concentre sur actif/passif avec veille, l’aide du Gestionnaire de trafic pour le basculement. Notez que vous pouvez déployer un petit nombre d’ordinateurs virtuels pour veille et puis évoluer selon vos besoins.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant s’appuie sur l’architecture de la [fiabilité ajout à une architecture multicouches sur Azure](guidance-compute-n-tier-vm.md).

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « cluster - page de région (Windows) à plusieurs.

[! [0]][0]

- **Zones principales et secondaires**. Cette architecture utilise deux régions pour obtenir une plus grande disponibilité. Une est la région principale. Tout se déroule normalement, le trafic réseau est routé vers la zone principale. Mais si qui n’est pas disponible, le trafic est acheminé vers la zone secondaire.

- ** [Azure le trafic Gestionnaire] [ traffic-manager] ** achemine des demandes entrantes vers la région principale. Si cette zone est plus disponible, le trafic Gestionnaire bascule vers la zone secondaire. Pour plus d’informations, voir la section [Configuration du gestionnaire le trafic](#configuring-traffic-manager).

- **Groupes de ressources**. Créer des [groupes de ressources] distincts[ resource groups] principal à votre région, la région secondaire et pour le Gestionnaire de trafic. Cela vous donne la possibilité de gérer chaque région comme un ensemble unique de ressources. Par exemple, vous pourriez redéployez une région, sans interruption de l’option autres. [Lier les groupes de ressources][resource-group-links], de sorte que vous pouvez exécuter une requête pour répertorier toutes les ressources de l’application.

- **VNets**. Créer un VNet distincte pour chaque région. Vérifiez que les espaces d’adressage ne se chevauchent pas. 

- **SQL Server toujours sur le groupe de disponibilité**. Si vous utilisez SQL Server, nous vous recommandons de [Toujours sur Availabilty groupes SQL] [ sql-always-on] de disponibilité. Créer un groupe de disponibilité unique qui inclut les instances de SQL Server dans les deux régions. Pour plus d’informations, consultez la section [configuration du groupe de disponibilité SQL Server toujours sur](#configuring-the-sql-server-alwayson-availability-group ).

> [AZURE.NOTE] Envisagez également [De base de données SQL Azure][azure-sql-db], qui fournit une base de données relationnel comme un service cloud. Base de données SQL, vous n’avez pas besoin configurer un groupe de disponibilité ou gérer le basculement.  

- **Passerelles VPN**: créer une [passerelle VPN] [ vpn-gateway] dans chaque VNet et configurer une [connexion VNet-VNet][vnet-to-vnet], pour activer le trafic réseau entre les deux VNets. Cela est nécessaire pour le groupe de disponibilité SQL toujours sur.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence suivez ces recommandations, à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="regional-pairing"></a>Le jumelage régionaux

Chaque région Azure est associée à une autre région dans le même emplacement géographique. En règle générale, choisissez régions dans la même paire régionale (par exemple, US Extrême-Orient 2 et nous Central). Cela les avantages suivants :

- S’il existe une panne large, récupération d’au moins une région se déconnecter de toutes les paires est prioritaire.

- Mises à jour système Azure planifié sont transférées aux régions appariées dans un ordre séquentiel, pour réduire le temps d’arrêt possibles.

- Paires se trouvent sur la même géographie, répondre aux exigences de la délégation de données.

Toutefois, assurez-vous que les deux régions prennent en charge tous les services Azure nécessaires pour votre application (consultez [Services par région][services-by-region]). Pour plus d’informations sur les paires de régionaux, voir [entreprise continuité et récupération d’urgence (BCDR) : régions appariés Azure][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configuration du Gestionnaire de trafic

Prendre en compte les points suivants lorsque vous configurez le trafic gestionnaire à votre situation :

- **Routage.** Le trafic Manager prend en charge plusieurs [algorithmes de routage][tm-routing]. Pour le scénario décrit dans cet article, utiliser le routage de _priorité_ (anciennement appelé _basculement_ routage). Avec ce paramètre, le trafic gestionnaire envoie toutes les demandes à la zone principale, à moins que la zone primaire deviendrait inaccessible. À ce stade, il bascule automatiquement à la zone secondaire. Consultez [configurer le basculement de routage méthode][tm-configure-failover].

- **Sonde d’intégrité.** Gestionnaire de trafic utilise un HTTP (ou HTTPS) [sonde] [ tm-monitoring] de contrôler la disponibilité de chaque région. La sonde vérifie pour une réponse HTTP 200 pour un chemin d’URL spécifiée. Pour obtenir les meilleurs résultats, créer un point de terminaison qui indique l’état global de l’application et utiliser ce point de terminaison de la sonde d’intégrité. Dans le cas contraire, la sonde peut signaler un point de terminaison « correct » lorsque sont réellement une défaillance critiques parties de l’application. Pour plus d’informations, voir [État du point de terminaison de surveillance motif][health-endpoint-monitoring-pattern].   

Quand le trafic Gestionnaire bascule, il existe un certain temps lorsque le client ne peut pas accéder à l’application, qui peut prendre quelques minutes. Deux facteurs affectent la durée totale :

- La sonde de santé doit détecter que le centre de données principal est devenu inaccessible.

- Serveurs DNS doivent mettre à jour les enregistrements DNS mis en cache pour l’adresse IP, qui dépend du DNS time to live (TTL). La durée de vie par défaut est 300 secondes (5 minutes), mais vous pouvez configurer cette valeur lorsque vous créez le profil Manager le trafic.

Pour plus d’informations, voir [à propos du trafic Gestionnaire surveillance][tm-monitoring]. 

Si le trafic Gestionnaire bascule, nous vous recommandons d’effectuer un retour arrière manuel, plutôt que d’automatiquement ignore précédent. Vérifiez que tous les sous-systèmes d’application sont tout d’abord correct. Dans le cas contraire, vous pouvez créer une situation où l’application retourne en arrière entre centres de données.

Par défaut, le trafic gestionnaire est automatiquement restauré. Pour éviter ce problème, diminuer manuellement la priorité de la région primaire après un événement de basculement. Par exemple, supposons que la zone primaire est de priorité 1 et le moniteur secondaire est priorité 2. Après un basculement, définir la région principale pour priority 3, afin d’empêcher la restauration automatique. Lorsque vous êtes prêt à revenir, mettez à jour la priorité 1.

La commande suivante Azure infrastructure du langage commun met à jour la priorité :

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

Un autre moyen d’éviter bascule consiste à désactiver temporairement le point de terminaison :

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```

Selon la cause d’un basculement, vous devrez peut-être redploy les ressources au sein d’une région. Avant l’échec précédent, effectuez un test opérationnelle. Le test doit vérifier des éléments tels que :

- Machines virtuelles sont correctement configurés. (Tous les logiciels requis sont installé, IIS est en cours d’exécution, etc.).

- Sous-systèmes d’application fonctionnent correctement. 

- Tests de fonctionnement. (Par exemple, le niveau de base de données est accessible à partir de la couche web.)

### <a name="sql-server-always-on-configuration"></a>SQL Server toujours de configuration

SQL Server toujours sur les groupes de disponibilité nécessitent un contrôleur de domaine. Tous les nœuds dans le groupe de disponibilité doivent être placé dans le même domaine Active Directory. Les points suivants fournissent des instructions concernant la configuration d’un groupe de disponibilité SQL Server toujours sur :

- Au minimum, placer deux superflus dans chaque région. 

- Donnez à chaque contrôleur de domaine à une adresse IP statique.

- Créer une connexion VNet-VNet pour activer la communication entre le VNets.

- Pour chaque VNet, ajoutez les adresses IP contrôleur de domaine (dans les deux régions) à la liste des serveurs DNS. Vous pouvez utiliser la commande suivante infrastructure du langage commun. Plus d’informations, voir [gérer DNS servers utilisés par un réseau virtuel (VNet)][vnet-dns].

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- Créer un [Cluster de basculement Windows Server] [ wsfc] cluster (WSFC) qui inclut les instances de SQL Server dans les deux régions. 

- Créer un groupe de disponibilité SQL Server toujours sur qui inclut les instances de SQL Server dans les zones principales et secondaires. Pour connaître la procédure, voir [Extension toujours dans le groupe de disponibilité à Azure centre de données distant (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) . 

- Placez le réplica principal dans la région principale.

- Placer un ou plusieurs réplicas secondaires dans la région principale. Configurer ces derniers pour utiliser la validation synchrone avec basculement automatique.

- Placez un ou plusieurs réplicas secondaires dans la zone secondaire. Configurer ces derniers pour utiliser la validation *asynchrone* , pour des raisons de performances. (Dans le cas contraire, toutes les transactions SQL doivent pas attendre lors d’un aller-retour sur le réseau vers la zone secondaire.) 

> [AZURE.NOTE] Réplicas validation asynchrone ne prennent pas en charge basculement automatique. 

Pour plus d’informations, voir [En cours d’exécution des machines virtuelles Windows pour une architecture multicouches sur Azure](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group).

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Avec une application multicouches complexe, vous devrez pas répliquer l’ensemble de l’application dans la zone secondaire. À la place, vous pouvez répliquer simplement un sous-système critique qui est nécessaire pour prendre en charge la continuité des activités.

Gestionnaire de trafic est un point de défaillance dans le système. Si le service échoue, les clients ne peuvent pas accéder à votre application pendant l’interruption. Passez en revue le [Gestionnaire de trafic SLA][tm-sla]et déterminez si l’aide du Gestionnaire de trafic seul répond à vos besoins de disponibilité. Si ce n’est pas le cas, vous pouvez ajouter une autre solution de gestion du trafic comme un retour arrière. Si le service Gestionnaire de trafic Azure échoue, changez vos enregistrements CNAME dans le système DNS pour qu’il pointe vers l’autre service de gestion du trafic. (Cette étape doit être effectuée manuellement, et votre application ne sera pas disponible jusqu'à ce que les modifications DNS sont diffusées). 

Pour le cluster SQL Server, il existe deux scénarios de basculement à prendre en compte :

1. Tous les réplica SQL dans la région primaire échouent. Par exemple, cela peut se produire pendant une panne régionale. Dans ce cas, vous devez basculer manuellement vers le groupe de disponibilité SQL, même si le trafic Gestionnaire bascule automatiquement sur le serveur frontal. Suivez la procédure décrite dans [effectuer un basculement manuel forcé d’un groupe de disponibilité de SQL Server](https://msdn.microsoft.com/library/ff877957.aspx), qui explique comment effectuer un basculement forcé à l’aide de PowerShell, Transact-SQL ou SQL Server Management Studio dans SQL Server 2016. 

    > [AZURE.WARNING] Avec le basculement forcé, il existe un risque de perte de données. Une fois que la zone primaire est en ligne, prendre un instantané de la base de données et [tablediff] permet de rechercher les différences.

2. Gestionnaire de trafic bascule vers la zone secondaire, mais réplica SQL principal est toujours disponible. Par exemple, la couche frontale peut échouer sans affecter le machines virtuelles SQL. Dans ce cas, le trafic Internet est routé vers la zone secondaire, et cette zone peut toujours se connecter au réplica SQL principal. Toutefois, il sera latence accrue, car les connexions SQL passent au sein de régions. Dans ce cas, vous devez effectuer un basculement manuel comme suit : 

    - Basculer temporairement un réplica SQL dans la zone secondaire pour valider *synchrone* . Ainsi, il n’y avoir perte de données pendant le basculement.

    - Basculer vers ce réplica SQL. 
    
    - Lorsque vous ne parvenez pas à la région principale, restaurer le paramètre de validation asynchrone. 

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Lorsque vous mettez à jour votre déploiement, mettre à jour une région à la fois, afin de réduire le risque d’une défaillance global à partir d’une configuration incorrecte ou une erreur dans l’application.

Testez la résilience du système pour les échecs. Voici quelques scénarios courants échec pour tester :

- Fermez les instances de machine virtuelle.

- Ressources de pression tels que la charge du processeur et de mémoire.

- Réseau déconnecter/retard.

- Arrêter le processus.

- Expiration des certificats.

- Simuler dysfonctionnements matériels.

- Arrêter le service DNS sur le domaine.

Mesurer les temps de récupération et vérifiez qu’elles correspondent à vos besoins. Combinaisons de modes d’échec, ainsi que de test.

## <a name="next-steps"></a>Étapes suivantes

Cette série a axée sur les déploiements dans le cloud intégral. Scénarios d’entreprise nécessitent souvent un réseau hybride, connexion d’un réseau local avec un réseau virtuel Azure. Pour découvrir comment créer un tel réseau hybride, consultez [implémentation d’une Architecture réseau hybride avec Azure et en local VPN][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc.png "Architecture de réseau hautement disponible pour les applications multicouches Azure"