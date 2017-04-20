<properties 
    pageTitle="Modifier le niveau de performances et de niveau de service d’une base de données SQL Azure à l’aide de PowerShell | Microsoft Azure" 
    description="Modifier le niveau de service et niveau de performance d’une base de données SQL Azure montre comment mettre à l’échelle de votre base de données SQL vers le haut ou vers le bas avec PowerShell. Modifier le niveau de tarification d’une base de données SQL Azure avec PowerShell." 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL avec PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Niveaux de service et de performances décrivent les fonctionnalités et ressources disponibles pour votre base de données SQL et peuvent être mis à jour selon les besoins de votre application. Pour plus d’informations, voir [Niveaux de Service](sql-database-service-tiers.md).

Remarquez que la modification du niveau de service et/ou niveau de performances d’une base de données crée une copie de la base de données d’origine dans le nouveau niveau de performance et avant de passer les connexions au réplica. Aucune donnée n’est perdue au cours du processus mais pendant la brève intervention quand nous basculer vers le réplica, les connexions à la base de données sont désactivées, certaines transactions en vol peuvent être annulées. Cette fenêtre varie, mais est en moyenne moins de 4 secondes et dans plus de 99 % des cas est inférieur à 30 secondes. Très rarement, en particulier si grande numéros de transactions dans vol aux connexions échantillonnage sont désactivés, cette fenêtre peut être plus longue.  

La durée de l’ensemble du processus à distance échelle dépend de la couche service et taille de la base de données avant et après la modification. Par exemple, une base de données de 250 Go change vers, d’ou au sein d’un niveau de service Standard, doit se terminer dans les 6 heures. Pour une base de données de la même taille que la modification de niveaux de performances dans le niveau de service Premium est, elle doit se terminer au sein de 3 heures.


- Pour mettre à niveau une base de données, la base de données doit être inférieure à la taille maximale autorisée du niveau de service cible. 
- Lors de la mise à niveau d’une base de données avec [La réplication Geo](sql-database-geo-replication-portal.md) est activée, vous devez d’abord passer ses bases de données secondaires vers le niveau de performance souhaité avant la mise à niveau de la base de données principale.
- Lors de la mise à niveau d’un niveau de service Premium, vous devez tout d’abord terminer toutes les relations Geo réplication. Vous pouvez suivre les étapes décrites dans la rubrique [récupérer en cas de panne](sql-database-disaster-recovery.md) pour arrêter le processus de réplication entre le principal et les bases de données secondaires actives.
- Les offres de service restaurer sont différentes pour les différents niveaux de service. Si vous êtes mise à niveau vous risquez de perdre la possibilité de restaurer à un point dans le temps, ou dispose d’un délai de conservation des sauvegardes inférieur. Pour plus d’informations, voir [sauvegarde de base de données SQL Azure et de restauration](sql-database-business-continuity.md).
- Les nouvelles propriétés de la base de données ne sont pas appliquées jusqu'à ce que les modifications sont terminées.



**Pour terminer cet article vous besoin des éléments suivants :**

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Compte gratuit** en haut de cette page et puis revenez à la fin de cet article.
- Une base de données SQL Azure. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données SQL Azure](sql-database-get-started.md).
- PowerShell Azure.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Modifier le niveau de service niveau et les performances de votre base de données SQL

Exécuter [Set-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) et set **-RequestedServiceObjectiveName** pour le niveau de performance de la couche tarification souhaité ; par exemple *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Exemple de script PowerShell pour modifier le niveau de performances et de niveau de service de votre base de données SQL

Remplacer ```{variables}``` avec vos valeurs (n’incluez pas les accolades).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Étapes suivantes

- [Mettre à l’échelle, puis dans](sql-database-elastic-scale-get-started.md)
- [Se connecter et une base de données SQL SSMS de requête](sql-database-connect-query-ssms.md)
- [Exporter une base de données SQL Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- [Documentation de base de données SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Applets de commande de base de données SQL azure] (http://msdn.microsoft.com/library/azure/mt574084https  :/ / msdn.microsoft.com/bibliothèque/azure/mt619433(v=azure.300\).aspx.aspx)