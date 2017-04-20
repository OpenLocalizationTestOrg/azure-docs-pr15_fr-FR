<properties
    pageTitle="Le programme d’installation de nouvelle base de données SQL avec PowerShell | Microsoft Azure"
    description="Découvrez comment créer une base de données SQL avec PowerShell. Tâches de configuration de base de données courantes peuvent être gérés par le biais applets de commande PowerShell."
    keywords="créer la nouvelle base de données sql, le programme d’installation de base de données"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Créer une base de données SQL et effectuer des tâches courantes de configuration de base de données avec les applets de commande PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Apprenez à créer une base de données SQL à l’aide des applets de commande PowerShell. (Pour la création de bases de données élastiques, voir [créer un nouveau pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md)).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Le programme d’installation de base de données : créer un groupe de ressources, le serveur et la règle de pare-feu

Une fois que vous avez accès à exécuter les applets de commande par rapport à votre abonnement Azure sélectionné, l’étape suivante consiste à établir le groupe de ressources qui contient le serveur dans lequel la base de données doit être créé. Vous pouvez modifier la commande suivante pour utiliser un autre emplacement valide que vous choisissez. Exécuter **(Get-AzureRmLocation | Where-Object {$_. Fournisseurs - eq « Microsoft.Sql »}). Emplacement** pour obtenir une liste des emplacements valides.

Exécutez la commande suivante pour créer un groupe de ressources :

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Création d’un serveur

Bases de données SQL sont créés à l’intérieur des serveurs de base de données SQL Azure. Exécuter le [New-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) pour créer un serveur. Le nom de votre serveur doit être unique à tous les serveurs de base de données SQL Azure. Si le nom du serveur est déjà effectuée, vous obtenez une erreur. Il faut également signaler est que cette commande peut prendre plusieurs minutes. Vous pouvez modifier la commande utiliser n’importe quel endroit valide choisie, mais vous devez utiliser le même emplacement que vous avez utilisé pour le groupe de ressources créé à l’étape précédente.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Lorsque vous exécutez cette commande, vous êtes invité à votre nom d’utilisateur et mot de passe. N’entrez vos informations d’identification Azure. À la place, entrez le nom d’utilisateur et mot de passe pour créer en tant que l’administrateur du serveur. Le script en bas de cet article montre comment définir les informations d’identification de serveur dans le code.

Les détails du serveur apparaissent une fois que le serveur est créé avec succès.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Configurer une règle de pare-feu serveur pour autoriser l’accès au serveur

Pour accéder au serveur, vous devez établir une règle de pare-feu. Exécuter le [New-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) commande, en remplaçant les adresses IP de début et de fin avec des valeurs valides pour votre ordinateur.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Les détails des règles pare-feu apparaissent une fois que la règle est créée avec succès.

Pour permettre à d’autres services accéder au serveur Azure, ajouter une règle de pare-feu et configurer les StartIpAddress et le EndIpAddress 0.0.0.0. Cette règle autorise le trafic Azure à partir de n’importe quel abonnement Azure pour accéder au serveur.

Pour plus d’informations, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md).


## <a name="create-a-sql-database"></a>Créer une base de données SQL

Vous avez maintenant une règle de pare-feu configuré afin que vous pouvez accéder au serveur, un serveur et un groupe de ressources.

Ce qui suit [New-AzureRmSqlDatabase] (commande https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) crée une base de données SQL (vide) au niveau de service Standard, avec un niveau de performance S1 :


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Les détails de la base de données s’affichent une fois la base de données est créée.

## <a name="create-a-sql-database-powershell-script"></a>Créer une base de données SQL script PowerShell

Le script PowerShell suivant crée une base de données SQL et toutes ses ressources dépendantes. Remplacer tout `{variables}` avec des valeurs spécifiques à votre abonnement et ressources (supprimer le **{}** lorsque vous définissez vos valeurs).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Étapes suivantes
Une fois que vous créez une base de données SQL et effectuez les tâches de configuration de base de la base de données, vous êtes prêt pour les éléments suivants :

- [Gérer la base de données SQL avec PowerShell](sql-database-manage-powershell.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>Ressources supplémentaires

- [Applets de commande de base de données SQL azure] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)
