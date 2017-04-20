<properties 
    pageTitle="Verrouiller des ressources avec le Gestionnaire de ressources | Microsoft Azure" 
    description="Empêcher les utilisateurs de mettre à jour ou supprimer certaines ressources en appliquant une restriction à tous les utilisateurs et les rôles." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Verrouiller des ressources avec le Gestionnaire de ressources Azure

En tant qu’administrateur, vous devrez peut-être verrouiller un abonnement, groupe de ressources ou ressource pour empêcher d’autres utilisateurs de votre organisation à partir d’accidentellement en supprimant ou modifiant les ressources critiques. Vous pouvez définir le niveau de verrouillage pour **CanNotDelete** ou **en lecture seule**. 

- **CanNotDelete** signifie que les utilisateurs autorisés peuvent toujours lire et modifier une ressource, mais ils ne peuvent pas le supprimer. 
- **En lecture seule** signifie que les utilisateurs autorisés peuvent lire à partir d’une ressource, mais ils ne peuvent pas supprimer ou exécuter des actions sur celle-ci. L’autorisation sur la ressource est restreinte le rôle de **lecteur** . 

Application **en lecture seule** peut entraîner des résultats inattendus, car certaines opérations sembleront lire les opérations ont réellement besoin d’autres actions. Par exemple, en plaçant un verrou **en lecture seule** sur un compte de stockage empêche tous les utilisateurs d’afficher les touches de la liste. La liste opération clés est gérée par une requête POST, car les clés retournées sont disponibles pour les opérations d’écriture. Pour un autre exemple, en plaçant un verrou **en lecture seule** sur une ressource d’application Service empêche Visual Studio Server Explorer affiche des fichiers pour la ressource car cette interaction nécessite un accès en écriture.

Contrairement aux contrôle d’accès basé sur un rôle, verrous gestion vous permet d’appliquer une restriction sur tous les utilisateurs et les rôles. Pour en savoir plus sur la définition d’autorisations pour les utilisateurs et les rôles, consultez [Contrôle d’accès basé sur un rôle Azure](./active-directory/role-based-access-control-configure.md).

Lorsque vous appliquez un verrou avec une portée parent, toutes les ressources enfant héritent le même verrou. Même les ressources que vous ajouterez ultérieurement héritent le verrou du parent. Le verrou plus restrictif dans l’héritage est prioritaire.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Qui peut créer ou supprimer les verrous dans votre organisation

Pour créer ou supprimer les verrous gestion, vous devez avoir accès à **Microsoft.Authorization/\* ** ou **Microsoft.Authorization/locks/\* ** actions. Les rôles intégrés uniquement **propriétaire** et **l’Administrateur de l’accès utilisateur** sont accordées ces actions.

## <a name="creating-a-lock-through-the-portal"></a>Création d’un verrou via le portail

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Création d’un verrou dans un modèle

L’exemple suivant montre un modèle qui crée un verrou sur un compte de stockage. Le compte de stockage auquel appliquer le verrou est fourni sous forme d’un paramètre. Le nom du verrou est créé en concaténant le nom de la ressource avec **/Microsoft.Authorization/** et le nom du verrou, dans ce cas **myLock**.

Le type fourni est spécifique au type de ressource. Espace de stockage, définissez le type sur « Microsoft.Storage/storageaccounts/providers/locks ».

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Création d’un verrou avec l’API REST

Vous pouvez verrouiller des ressources déployées avec l' [API REST pour les verrous gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx). L’API REST vous permet de créer et supprimer les verrous et récupérer les informations sur les verrous existants.

Pour créer un verrou, exécutez :

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

L’étendue peut être un abonnement, le groupe de ressources ou la ressource. Le nom du verrou est tout ce que vous voulez appeler le verrou. Pour la version de l’api, utilisez **01 / 01/2015**.

Dans la demande, incluez un objet JSON qui spécifie les propriétés pour le verrou.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Pour obtenir des exemples, voir [API REST pour les verrous gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Création d’un verrou avec PowerShell Azure

Vous pouvez verrouiller les ressources déployées avec PowerShell Azure à l’aide du **Nouveau AzureRmResourceLock** comme le montre l’exemple suivant.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell fournit d’autres commandes d’utilisation verrous, tels **Définir AzureRmResourceLock** pour mettre à jour un verrou et **AzureRmResourceLock-supprimer** pour supprimer un verrou.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation des verrouillages des ressources, consultez [Verrouillage vers le bas vos ressources Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Pour apprendre à organiser logiquement vos ressources, voir [en utilisant des balises pour organiser vos ressources](resource-group-using-tags.md)
- Pour modifier une ressource réside dans quel groupe de ressources, voir [déplacer des ressources à nouveau groupe de ressources](resource-group-move-resources.md)
- Vous pouvez appliquer des restrictions et conventions au sein de votre abonnement avec des stratégies personnalisées. Pour plus d’informations, voir [Utiliser une stratégie pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).
