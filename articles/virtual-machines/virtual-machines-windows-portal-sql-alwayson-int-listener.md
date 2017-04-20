<properties
   pageTitle="Créer des récepteur pour le groupe availabilty AlwaysOn pour SQL Server dans le Machines virtuelles Azure"
   description="Instructions détaillées pour la création d’un récepteur pour un groupe d’availabilty AlwaysOn pour SQL Server dans le Machines virtuelles Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurer un programme d’équilibrage de charge interne pour un groupe de disponibilité AlwaysOn dans Azure

Cette rubrique explique comment créer un équilibrage de charge interne pour un groupe de disponibilité de SQL Server AlwaysOn dans Azure machines virtuelles en cours d’exécution dans le modèle de gestionnaire de ressources. Un groupe de disponibilité AlwaysOn nécessite un équilibrage de charge lorsque les instances de SQL Server se trouvent sur Azure machines virtuelles. L’équilibrage de charge stocke l’adresse IP pour le récepteur de groupe de disponibilité. Si un groupe de disponibilité s’étend sur plusieurs régions, chaque région nécessite un équilibrage de charge.

Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité de SQL Server AlwaysOn déployé sur Azure machines virtuelles dans le modèle de gestionnaire de ressources. Les deux machines virtuelles SQL Server doit appartenir au même jeu de disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) crée automatiquement le groupe de disponibilité AlwaysOn dans le Gestionnaire de ressources Azure. Ce modèle crée automatiquement l’équilibrage de charge interne. 

Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique nécessite que vos groupes de disponibilité sont déjà configurées.  

Rubriques connexes sont les suivantes :

 - [Configurer les groupes de disponibilité AlwaysOn dans Azure machine virtuelle (graphique)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurer une connexion VNet à VNet en utilisant le Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Étapes

En parcourant ce document vous sera créer et configurer un programme d’équilibrage de charge dans le portail Azure. Une fois cette opération terminée, vous allez configurer le cluster pour utiliser l’adresse IP à partir de l’équilibrage de charge pour le récepteur de groupe de disponibilité AlwaysOn.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Créer et configurer l’équilibrage de charge dans le portail Azure

Dans cette partie de la tâche, vous allez effectuer les opérations suivantes dans le portail Azure :

1. Créer l’équilibrage de charge et configurer l’adresse IP

1. Configurer le pool de serveur principal

1. Créer la sonde 

1. Définir la règles d’équilibrage de charge

>[AZURE.NOTE] Si les serveurs SQL sont dans différents groupes de ressources et les régions, vous ne toutes ces étapes deux fois, une fois dans chaque groupe de ressources.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. créer l’équilibrage de charge et configurer l’adresse IP

La première étape consiste à créer l’équilibrage de charge. Dans le portail Azure, ouvrez le groupe de ressources qui contient les machines virtuelles SQL Server. Dans le groupe de ressources, cliquez sur **Ajouter**.

- Recherchez **l’équilibrage de charge**. À partir des résultats, sélectionnez **Équilibrage de charge**, qui est publié par **Microsoft**.

- Dans la carte **Équilibrage de charge** , cliquez sur **créer**.

- Sous **créer l’équilibrage de charge**, configurez le l’équilibrage de charge comme suit :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Un nom de texte qui représente l’équilibrage de charge. Par exemple, **sqlLB**. |
| **Schéma** | **Interne** |
| **Réseau virtuel** | Choisissez le réseau virtuel qui se trouvent dans les serveurs SQL Server.   |
| **Sous-réseau**  | Choisissez le sous-réseau figurant dans les serveurs SQL Server. |
| **Abonnement** | Si vous avez plusieurs abonnements, ce champ peut s’afficher. Sélectionnez l’abonnement auquel vous souhaitez associer à cette ressource. Il s’agit normalement le même abonnement en tant que toutes les ressources du groupe de disponibilité.  |
| **Groupe de ressources** | Sélectionnez le groupe de ressources figurant dans les serveurs SQL Server. | 
| **Emplacement** | Choisissez l’emplacement Azure figurant dans les serveurs SQL Server. |

- Cliquez sur **créer**. 

Azure crée l’équilibrage de charge que vous avez configuré ci-dessus. L’équilibrage de charge appartient à un réseau spécifique, un sous-réseau, un groupe de ressources et un emplacement. Une fois Azure terminée, vérifiez les paramètres d’équilibrage de charge dans Azure. 

À présent, configurez l’adresse IP d’équilibrage de la charge.  

- Dans la carte de **paramètres** équilibrage de charge, cliquez sur **adresse IP**. La carte de **l’adresse IP** indique qu’il s’agit d’un équilibrage de charge privé sur le même réseau virtuel que vos serveurs SQL. 

- Définir les paramètres suivants : 

| Paramètre | Valeur |
| ----- | ----- |
| **Sous-réseau** | Choisissez le sous-réseau figurant dans les serveurs SQL Server. |
| **Affectation** | **Statique** |
| **Adresse IP** | Tapez une adresse IP virtuelle inutilisée à partir du sous-réseau.  |

- Enregistrer les paramètres.

À présent l’équilibrage de charge possède une adresse IP. Enregistrez cette adresse IP. Vous allez utiliser cette adresse IP lorsque vous créez un récepteur sur le cluster. Dans un script PowerShell plus loin dans cet article, utilisez cette adresse pour la `$ILBIP` variable.



## <a name="2-configure-the-backend-pool"></a>2. configurer le pool principal

L’étape suivante consiste à créer un groupe d’adresses principal. Azure appelle le pool d’adresses principal *pool principal*. Dans ce cas, le pool principal est les adresses des deux serveurs SQL dans le groupe de votre disponibilité. 

- Dans votre groupe de ressources, cliquez sur l’équilibrage de charge que vous avez créé. 

- Sous **paramètres**, cliquez sur **pools principal**.

- Sous **pools d’adresses principal**, cliquez sur **Ajouter** pour créer un groupe d’adresses principal. 

- Sous **Ajouter principal pool** sous **nom**, tapez un nom pour le pool principal.

- Sous **machines virtuelles** cliquez sur **+ Ajouter une machine virtuelle**. 

- Sous **machines virtuelles choisir** cliquez sur **Choisir un jeu de disponibilité** et spécifier que les machines virtuelles SQL Server appartiennent à la valeur la disponibilité.

- Une fois que vous avez choisi le jeu de disponibilité, cliquez sur **Choisir les machines virtuelles**. Cliquez sur les deux machines virtuelles hébergeant les instances de SQL Server dans le groupe de disponibilité. Cliquez sur **Sélectionner**. 

- Cliquez sur **OK** pour fermer les cartes pour **machines virtuelles choisir**et **Ajouter principal pool**. 

Azure met à jour les paramètres du serveur principal pool d’adresses. Votre jeu de disponibilité comporte maintenant un pool de deux serveurs SQL.

## <a name="3-create-a-probe"></a>3. créer une sonde

L’étape suivante consiste à créer une sonde. La sonde définit comment Azure vérifie parmi les serveurs SQL possède actuellement le récepteur de groupe de disponibilité. Azure testent le service basé sur l’adresse IP sur un port que vous définissez lorsque vous créez la sonde.

- Dans la carte de **paramètres** équilibrage de charge, cliquez sur **teste**. 

- Dans la carte **teste** , cliquez sur **Ajouter**.

- Configurer la sonde sur la carte **sonde ajouter** . Utilisez les valeurs suivantes pour configurer la sonde :

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Un nom de texte qui représente la sonde. Par exemple, **SQLAlwaysOnEndPointProbe**. |
| **Protocole** | **TCP** |
| **Port** | Vous pouvez utiliser n’importe quel port disponible. Par exemple, *59999*.    |
| **Intervalle**  | *5* | 
| **Seuil incorrecte**  | *2* | 

- Cliquez sur **OK**. 

>[AZURE.NOTE] Vérifiez que le port que vous spécifiez est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs nécessitent une règle de trafic entrant pour le port TCP que vous utilisez. Pour plus d’informations, voir [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 

Azure crée la sonde. Azure utilisez la sonde pour tester les SQL Server contient le récepteur pour le groupe de disponibilité.

## <a name="4-set-the-load-balancing-rules"></a>4. la règles d’équilibrage de charge la valeur

Définir la règles d’équilibrage de charge. Les règles d’équilibrage de la charge configurer comment l’équilibrage de charge achemine le trafic vers les serveurs SQL Server. Pour cet équilibrage de charge vous permettra server direct retour, car un des deux serveurs SQL sera propriétaire jamais la ressource disponibilité groupe récepteur à la fois.

- Dans la carte de **paramètres** équilibrage de charge, cliquez sur **charger les règles d’équilibrage de charge**. 

- Dans la carte **l’équilibrage de charge règles** , cliquez sur **Ajouter**.

- Utiliser la carte **Ajouter charger les règles de l’équilibrage de charge** pour configurer la règle d’équilibrage de charge. Utilisez les paramètres suivants : 

| Paramètre | Valeur |
| ----- | ----- |
| **Nom** | Un nom de texte qui représente la règles d’équilibrage de charge. Par exemple, **SQLAlwaysOnEndPointListener**. |
| **Protocole** | **TCP** |
| **Port** | *1433*   |
| **Port principal** | *1433*. Notez que cela vont être désactivée car cette règle utilise **IP flottante (serveur direct retour)**.   |
| **Sonde** | Utilisez le nom de la sonde que vous avez créé pour cette équilibrage de charge. |
| **Persistance session**  | **Aucun** | 
| **Délai d’inactivité (en minutes)**  | *4* | 
| **Flottant IP (serveur direct retour)**  | **Activé** | 

 >[AZURE.NOTE] Vous devrez peut-être faire défiler vers le bas sur la carte pour afficher tous les paramètres.

- Cliquez sur **OK**. 

- Azure configure la règle d’équilibrage de charge. Maintenant, l’équilibrage de charge est configuré pour acheminer le trafic vers le serveur SQL qui héberge le récepteur pour le groupe de disponibilité. 

À ce stade du groupe de ressources comporte un programme d’équilibrage de charge, connexion à deux ordinateurs SQL Server. L’équilibrage de charge contienne également une adresse IP que l’auditeur de SQL Server AlwaysOn disponibilité groupe afin que l’ordinateur peut répondre aux demandes pour les groupes de disponibilité.

>[AZURE.NOTE] Si vos serveurs SQL se trouvent dans deux régions distinctes, répétez les étapes décrites dans l’autre région. Chaque région nécessite un équilibrage de charge. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour utiliser l’adresse IP d’équilibrage de la charge 

L’étape suivante consiste à configurer le récepteur sur le cluster et accédez à l’écoute en ligne. Pour ce faire, procédez comme suit : 

1. Créer le récepteur de groupe de disponibilité sur le cluster de basculement 

1. Mettre le récepteur en ligne

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. créer le récepteur de groupe de disponibilité sur le cluster de basculement

Dans cette étape, vous créez manuellement le récepteur de groupe de disponibilité dans le Gestionnaire de Cluster de basculement et SQL Server Management Studio (SSMS).

- Utiliser RDP pour vous connecter à la machine virtuelle Azure qui héberge réplica principal. 

- Ouvrez le Gestionnaire de Cluster de basculement.

- Sélectionnez le nœud **réseaux** et notez le nom du réseau cluster. Ce nom sera utilisé dans le `$ClusterNetworkName` variables dans le script PowerShell.

- Développez le nom du cluster, puis cliquez sur **rôles**.

- Dans le volet **rôles** , double-cliquez sur le nom de groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès Client**.

- Dans la zone **nom** , créez un nom pour ce nouveau récepteur, puis cliquez deux fois sur **suivant** , puis cliquez sur **Terminer**. Ne mettez pas l’écoute ou la ressource en ligne à ce stade.

 >[AZURE.NOTE] Le nom pour le nouveau récepteur est le nom de réseau applications utiliseront pour vous connecter à des bases de données dans le groupe de disponibilité de SQL Server.

- Cliquez sur l’onglet **ressources** , puis développez le Point d’accès Client que vous venez de créer. Avec le bouton droit de la ressource IP et cliquez sur Propriétés. Notez le nom de l’adresse IP. Vous allez utiliser ce nom dans la `$IPResourceName` variables dans le script PowerShell.

- Sous **Adresse IP** cliquez sur **Adresse IP statique** et définissez l’adresse IP statique à la même adresse que vous avez utilisé lorsque vous définissez l’adresse IP d’équilibrage de la charge sur le portail Azure. Activer NetBIOS pour cette adresse, puis cliquez sur **OK**. Répétez cette étape pour chaque ressource IP si votre solution s’étend sur plusieurs VNets Azure. 

- Sur le nœud de cluster qui héberge actuellement réplica principal, ouvrez une élevés PowerShell ISE et collez les commandes suivantes dans un nouveau script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Mettre à jour les variables et exécuter le script PowerShell pour configurer l’adresse IP et le port pour le nouveau récepteur.

 >[AZURE.NOTE] Si vos serveurs SQL se trouvent dans des zones distinctes, vous devez exécuter le script PowerShell à deux reprises. La première fois utilisent le nom de réseau cluster, nom de la ressource cluster IP et chargez adresse IP équilibrage du premier groupe de ressources. La deuxième fois utilisent le nom de réseau cluster, nom de la ressource cluster IP et chargez adresse IP équilibrage du deuxième groupe de ressources.

Maintenant le cluster a une ressource de récepteur de groupe de disponibilité.

## <a name="2-bring-the-listener-online"></a>2. Mettez le récepteur en ligne

Avec la ressource récepteur disponibilité groupe configurée, vous pouvez replacer que l’auditeur en ligne afin que les applications peuvent se connecter aux bases de données dans le groupe de disponibilité avec le récepteur.

- Accédez à basculement Cluster gestionnaire. Développez **rôles** , puis sélectionnez votre groupe de disponibilité. Sous l’onglet **ressources** , cliquez sur le nom du récepteur, puis cliquez sur **Propriétés**.

- Cliquez sur l’onglet **dépendances** . S’il existe plusieurs ressources répertoriées, vérifiez que les adresses IP disposent ou non AND, dépendances. Cliquez sur **OK**.

- Cliquez sur le nom du récepteur, puis cliquez sur **Mettre en ligne**.


- Une fois que l’auditeur online, à partir de l’onglet **ressources** , droit du groupe de disponibilité, puis cliquez sur **Propriétés**.

- Créer une dépendance sur la ressource de nom de récepteur (et non la IP adresse ressources nom). Cliquez sur **OK**.


- Démarrez SQL Server Management Studio, puis se connecter à réplica principal.


- Accédez à **la disponibilité AlwaysOn** | **groupes de disponibilité** | **écoute de groupe de disponibilité**. 


- Vous devez maintenant voir le nom du récepteur que vous avez créé dans le Gestionnaire de Cluster basculement. Cliquez sur le nom du récepteur, puis cliquez sur **Propriétés**.


- Dans la zone **Port** , spécifiez le numéro de port pour le récepteur de groupe de disponibilité à l’aide de la $EndpointPort vous avez utilisé auparavant (1433 a la valeur par défaut), puis cliquez sur **OK**.

Vous avez maintenant un groupe de disponibilité de SQL Server AlwaysOn dans Azure machines virtuelles en cours d’exécution en mode de gestionnaire de ressources. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion à l’écoute

Pour tester la connexion :

1. RDP sur un serveur SQL qui se trouve dans le même réseau virtuel, mais ne possède pas le réplica. Cela peut être autres SQL Server dans le cluster.

1. Utilitaire **sqlcmd** permet de tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** à réplica principal via le récepteur avec l’authentification Windows :

        sqlcmd -S <listenerName> -E

La connexion SQLCMD vous connecter automatiquement à n’importe quel instance de SQL Server héberge réplica principal. 

## <a name="guidelines-and-limitations"></a>Instructions et limitations

Notez les instructions suivantes écoute de groupe de disponibilité dans Azure à l’aide d’interne l’équilibrage de charge :

- Un seul récepteur de groupe disponibilité interne est pris en charge par le service cloud, car le récepteur est configuré pour l’équilibrage de charge, et qu’un seul programme d’équilibrage de charge interne. Toutefois, il est possible de créer plusieurs adresses récepteurs externes. 

- Avec un programme d’équilibrage de charge interne vous accédez uniquement le récepteur au sein du même réseau virtuel.
 
