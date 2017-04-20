<properties
   pageTitle="Explorateur de ressources Azure | Microsoft Azure"
   description="Décrit l’Explorateur de ressources Azure et comment elle peut être utilisée pour afficher et mettre à jour les déploiements via le Gestionnaire de ressources Azure"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Explorateur de ressources Azure permet d’afficher et modifier des ressources
L' [Explorateur de ressources Azure](https://resources.azure.com) est un excellent outil pour effectuer des recherches sur la page ressources que vous avez déjà créés dans votre abonnement. À l’aide de cet outil, vous pouvez comprendre comment les ressources sont structurés et voir les propriétés affectées à chaque ressource. Vous pouvez en savoir plus sur les opérations de l’API REST et les applets de commande PowerShell disponibles pour un type de ressource, et vous pouvez émettre des commandes via l’interface. Explorateur de ressources peut être particulièrement utile lorsque vous créez des modèles de gestionnaire de ressources, car il vous permet d’afficher les propriétés pour les ressources existantes.

La source de l’outil Explorateur des ressources est disponible sur [github](https://github.com/projectkudu/ARMExplorer), qui fournit une référence intéressante si vous avez besoin implémenter un comportement semblable dans vos propres applications.

## <a name="view-resources"></a>Affichage des ressources
Accédez à [https://resources.azure.com](https://resources.azure.com) et connectez-vous avec les mêmes informations d’identification que vous utiliseriez pour le [Portail Azure](https://portal.azure.com).

Une fois chargé, l’arborescence affichée sur la gauche vous permet d’exploration vers le bas dans vos abonnements et les groupes de ressources :

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Lorsque vous accédez à un groupe de ressources, vous verrez les fournisseurs pour lesquelles il existe ressources dans ce groupe :

![fournisseurs](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

À partir de là, vous pouvez démarrer extraire dans les instances de ressource. Dans la capture d’écran ci-dessous, vous pouvez voir la `sltest` instance de SQL Server dans l’arborescence. Sur le côté droit, vous pouvez voir les informations sur les requêtes de l’API REST que vous pouvez utiliser avec cette ressource. En naviguant vers le nœud pour une ressource, Explorateur de ressources effectue automatiquement la requête GET pour récupérer des informations sur la ressource. Dans la zone de texte en dessous de l’URL, vous verrez la réponse de l’API. 

Comme vous familiariser avec le Gestionnaire de ressources modèles le contenu du corps commence à être familière ! La section **Propriétés** de la réponse correspondent aux valeurs que vous pouvez fournir dans la section **Propriétés** de votre modèle.

![SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Explorateur de ressources permet de conserver extraire vers le bas pour Explorer les ressources enfants, dans le cas de la base de données SQL Server, il existe ressources enfants pour des éléments tels que des bases de données et les règles de pare-feu.

Exploration d’une base de données nous affiche les propriétés de cette base de données. Dans la capture d’écran ci-dessous, nous pouvons voir que la base de données `edition` est `Standard` et la `serviceLevelObjective` (ou niveau base de données) est `S1`.

![base de données SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Modifier les ressources

Une fois que vous avez accédé à une ressource, vous pouvez sélectionner le bouton Modifier pour modifier le contenu JSON. Vous pouvez ensuite utiliser l’Explorateur de ressources pour modifier le JSON et envoyez une demande de place pour modifier la ressource. Par exemple, l’image ci-dessous montre le niveau de base de données modifié en `S0`:

![base de données - demande place](./media/resource-manager-resource-explorer/are-05-database-put.png)

En sélectionnant **placer** vous soumettez la demande. 

Une fois que la demande a été envoyée Explorateur des ressources de problèmes de nouveau la requête GET pour actualiser le statut. Dans ce cas, vous pouvez constater que la `requestedServiceObjectiveId` a été mis à jour et est la différence entre la `currentServiceObjectiveId` indiquant qu’une opération de mise à l’échelle est en cours. Vous pouvez cliquer sur le bouton obtenir pour actualiser manuellement le statut.

![base de données - GET MAX2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Effectuer des Actions sur les ressources

L’onglet **Actions** vous permet de consulter et effectuer d’autres opérations reste. Par exemple, lorsque vous avez sélectionné une ressource de site web, l’onglet Actions présente une longue liste d’opérations disponibles, certaines d'entre elles sont affichées ci-dessous.

![Web - demande de billet](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>Appel de l’API via PowerShell
L’onglet PowerShell dans l’Explorateur de ressources affiche les applets de commande à utiliser pour interagir avec la ressource à laquelle vous êtes en train d’Explorer. Selon le type de ressource que vous avez sélectionnée, le script PowerShell affiché peut être comprise entre les applets de commande simple (tel que `Get-AzureRmResource` et `Set-AzureRmResource`) aux applets de commande plus complexe (par exemple, le remplacement des emplacements sur un site web). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Pour plus d’informations sur les applets de commande PowerShell Azure The, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](powershell-azure-resource-manager.md)

## <a name="summary"></a>Résumé
Lorsque vous travaillez avec le Gestionnaire de ressources, l’Explorateur de ressources peut être un outil extrêmement utile. Il est un excellent moyen Découvrez comment utiliser PowerShell pour interroger et apporter des modifications. Si vous travaillez avec l’API REST il est un excellent moyen de prise en main et tester rapidement des appels d’API avant de commencer à écrire du code. Et si vous écrivez des modèles et qu'il peut être un excellent moyen pour comprendre la hiérarchie des ressources et rechercher où placer les configuration - vous pouvez effectuez une modification dans le portail et recherchez les entrées correspondantes dans l’Explorateur de ressources !

Pour plus d’informations, regardez la [vidéo avec Scott Hanselman et David Ebbo Channel 9](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


