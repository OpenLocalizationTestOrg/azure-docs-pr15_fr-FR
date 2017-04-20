<properties
   pageTitle="Supprimer un cluster Azure et ses ressources | Microsoft Azure"
   description="Découvrez comment supprimer complètement un tissu Service cluster la suppression du groupe de ressources contenant le cluster ou en supprimant sélectivement des ressources."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Supprimer un cluster de Service tissu sur Azure et les ressources utilisées

Un cluster de Service tissu est constitué de nombreuses autres ressources Azure en plus de la ressource cluster proprement dite. Pour supprimer complètement un cluster de tissu de Service que vous devez également supprimer toutes les ressources, qu'il est constitué du.
Vous avez deux possibilités : soit supprimer le groupe de ressources figurant dans le cluster (qui supprime la ressource cluster et autres ressources dans le groupe de ressources) ou spécifiquement supprimer la ressource de cluster et il est associé à des ressources (mais pas d’autres ressources dans le groupe de ressources).

>[AZURE.NOTE] La suppression de la ressource cluster **ne** supprime toutes les autres ressources votre cluster tissu de Service est composée de.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Supprimer le groupe de ressources entière (routage) figurant dans le cluster tissu de Service

C’est la plus simple pour vous assurer que vous supprimez toutes les ressources associées à votre cluster, y compris le groupe de ressources. Vous pouvez supprimer le groupe de ressources à l’aide de PowerShell ou via le portail d’Azure. Si votre groupe de ressources comporte des ressources qui ne sont pas liées au cluster tissu de Service, vous pouvez supprimer des ressources spécifiques.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Supprimer le groupe de ressources à l’aide de PowerShell Azure

Vous pouvez également supprimer le groupe de ressources en exécutant les applets de commande PowerShell Azure. Assurez-vous que Azure PowerShell 1.0 ou ultérieure est installé sur votre ordinateur. Si vous le n'avez pas fait avant, suivez les étapes décrites dans [comment installer et configurer Azure PowerShell.](../powershell-install-configure.md)

Ouvrez une fenêtre PowerShell et exécutez les applets de commande PS :

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Vous obtenez une invite à confirmer la suppression si vous n’utilisez pas la *-Force* option. Confirmation de la passerelle résidentielle et toutes les ressources qu’il contient sont supprimés.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Supprimer un groupe de ressources dans le portail Azure  

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au cluster tissu de Service que vous voulez supprimer.
3. Cliquez sur le nom de groupe de ressources dans la page essentials cluster.
4. Cela affiche la page **Essentials de groupe de ressources** .
5. Cliquez sur **Supprimer**.
6. Suivez les instructions fournies pour terminer la suppression du groupe de ressources.

![Supprimer du groupe de ressources][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Supprimer la ressource de cluster et les ressources utilisées, mais pas d’autres ressources dans le groupe de ressources

Si votre groupe de ressources comporte uniquement les ressources qui sont liées au cluster tissu de Service que vous voulez supprimer, il est plus facile de supprimer le groupe de ressources entière. Si vous voulez supprimer sélectivement les ressources un par un dans votre groupe de ressources, puis procédez comme suit.

Si vous avez déployé votre cluster à l’aide du portail ou en utilisant l’un des modèles de gestionnaire de ressources de tissu de Service à partir de la galerie de modèles, toutes les ressources qui utilise le cluster sont marqués avec les balises de deux suivantes. Vous pouvez utiliser les pour déterminer les ressources auxquelles vous voulez supprimer.

***Ajouter des balises n ° 1 :*** Clé = nomducluster, valeur = « nom du cluster »

***Ajouter des balises #2 :*** Clé = resourceName, valeur = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Supprimer des ressources spécifiques dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au cluster tissu de Service que vous voulez supprimer.
3. Accédez à **tous les paramètres de** la carte essentials.
4. Cliquez sur **balises** sous **Gestion des ressources** dans la carte de paramètres.
5. Cliquez sur l’une des **balises** dans la carte de balises pour obtenir une liste de toutes les ressources avec cette balise.

    ![Balises de ressources][ResourceTags]

6. Une fois que la liste des ressources indicateurs, cliquez sur chacune des ressources et supprimez-les.

    ![Ressources balisés][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Supprimer les ressources à l’aide de PowerShell Azure

Vous pouvez supprimer les ressources un par un en exécutant les applets de commande PowerShell Azure. Assurez-vous que Azure PowerShell 1.0 ou ultérieure est installé sur votre ordinateur. Si vous le n'avez pas fait avant, suivez les étapes décrites dans [comment installer et configurer Azure PowerShell.](../powershell-install-configure.md)

Ouvrez une fenêtre PowerShell et exécutez les applets de commande PS :

```powershell
Login-AzureRmAccount
```
Pour chacune des ressources que vous voulez supprimer, exécutez la commande suivante :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Pour supprimer la ressource de cluster, exécutez la commande suivante :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Étapes suivantes
Lire les informations suivantes pour également en savoir plus sur la mise à niveau un cluster et partition services :

- [En savoir plus sur les mises à niveau cluster](service-fabric-cluster-upgrade.md)
- [En savoir plus sur partition avec état services pour échelle maximale](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
