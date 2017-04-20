<properties
   pageTitle="Déployer des ressources à l’aide de PowerShell et modèle | Microsoft Azure"
   description="Utiliser le Gestionnaire de ressources Azure et Azure PowerShell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle de gestionnaire de ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer des ressources avec les modèles de gestionnaire de ressources et Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure infrastructure du langage commun](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique explique comment utiliser PowerShell Azure avec les modèles de gestionnaire de ressources pour déployer vos ressources sur Azure.  

> [AZURE.TIP] Pour obtenir de l’aide de débogage une erreur lors du déploiement, voir :
>
> - [Afficher les opérations de déploiement avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) pour en savoir plus sur l’obtention d’informations qui vous permet de résoudre votre erreur
> - [Résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md) pour savoir comment résoudre les erreurs de déploiement

Votre modèle peut être un fichier local ou un fichier externe qui est disponible via un URI. Lorsque votre modèle est enregistré dans un compte de stockage, vous pouvez limiter l’accès au modèle et fournir un jeton de signature (sa) accès partagé pendant le déploiement.

## <a name="quick-steps-to-deployment"></a>Étapes de déploiement rapides

Cet article décrit toutes les différentes options à votre disposition pendant le déploiement. Toutefois, fréquence à laquelle vous ne devez deux commandes. Pour démarrer rapidement de déploiement, utilisez les commandes suivantes :

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Pour en savoir plus sur les options de déploiement, qui peuvent être mieux adaptés à votre scénario, poursuivez la lecture de cet article.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

1. Connectez-vous à votre compte Azure.

        Add-AzureRmAccount

     Un résumé de votre compte est renvoyé.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Si vous avez plusieurs abonnements, fournissent l’ID de l’abonnement que vous souhaitez utiliser pour le déploiement avec la commande **Set-AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. En règle générale, lorsque vous déployez un nouveau modèle, vous souhaitez créer un groupe de ressources pour contenir les ressources. Si vous avez un groupe de ressources existant que vous souhaitez déployer sur, vous pouvez ignorer cette étape et utiliser ce groupe de ressources. 

     Pour créer un groupe de ressources, fournissent un nom et un emplacement pour votre groupe de ressources. Vous devez fournir un emplacement pour le groupe de ressources, car le groupe de ressources stocke les métadonnées relatives aux ressources. Pour des raisons de conformité, vous souhaiterez spécifier l’emplacement de stockage que les métadonnées. En règle générale, nous vous recommandons de spécifier un emplacement dans lequel se trouve la plupart de vos ressources. À l’aide de la même emplacement peut simplifier un modèle.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Un résumé du nouveau groupe de ressources est renvoyé.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Avant d’exécuter votre déploiement, vous pouvez vérifier vos paramètres de déploiement. L’applet de commande **Test-AzureRmResourceGroupDeployment** permet de rechercher les problèmes avant de créer des ressources réelles. L’exemple suivant montre comment valider un déploiement.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Pour déployer des ressources à votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et fournissez les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle que vous avez créé et d’autres paramètres nécessaires pour votre scénario. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut **incrémentiel** est utilisée. Pour exécuter un déploiement complet, définissez le **Mode** sur **terminée**. Être prudent lorsque vous utilisez le mode complète que vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.

     Pour déployer un modèle local, utilisez le paramètre **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Pour déployer un modèle externe, utilisez le paramètre **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Vous disposez des options suivantes pour fournir des valeurs de paramètre : 
   
     1. Utiliser des paramètres en ligne.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Utiliser un objet de paramètre.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Utiliser un fichier de paramètres locaux. Pour plus d’informations sur le fichier de modèle, voir le [fichier de paramètres](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Utiliser un fichier de paramètres externe. Pour plus d’informations sur le fichier de modèle, voir le [fichier de paramètres](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas passer à d’autres valeurs inline ou à partir d’un fichier local. Pour plus d’informations, voir [la priorité des paramètres](#parameter-precendence).

     Une fois que les ressources ont été déployés, vous verrez une synthèse du déploiement.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Si votre modèle inclut un paramètre avec le même nom que l’un des paramètres dans la commande PowerShell, vous êtes invité à fournir une valeur pour ce paramètre. Le paramètre à partir du modèle doit inclure le suffixe **modèle**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) . Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En règle générale, vous devez éviter cette confusion par nommer ne pas les paramètres du même nom en tant que paramètres utilisés pour les opérations de déploiement.

6. Si vous voulez enregistrer les informations supplémentaires sur le déploiement qui peut-être vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **DeploymentDebugLogLevel** . Vous pouvez spécifier que demandent le contenu, le contenu de la réponse ou les deux être enregistrés avec l’opération de déploiement.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Pour plus d’informations sur l’utilisation de ce contenu débogage pour résoudre les déploiements hybrides, voir [déploiements de groupe de ressources de résolution des problèmes avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Déployez le modèle à partir du stockage avec jeton associations de sécurité

Vous pouvez ajouter vos modèles à un compte de stockage et lier ses pendant le déploiement avec un jeton associations de sécurité.

> [AZURE.IMPORTANT] En suivant les étapes ci-dessous, le blob qui contient le modèle est accessible à seul le propriétaire du compte. Toutefois, lorsque vous créez un jeton associations de sécurité pour l’objets blob, le blob est accessible à toutes les personnes avec cet URI. Si un autre utilisateur intercepte l’URI, cet utilisateur ne peut accéder au modèle. À l’aide d’un jeton de sa constitue un bon moyen de limiter l’accès à vos modèles, mais vous ne pas incluez des données sensibles telles que les mots de passe directement dans le modèle.

### <a name="add-private-template-to-storage-account"></a>Ajouter modèle privé à un compte de stockage

Les étapes suivantes configurer un compte de stockage des modèles :

1. Créer un groupe de ressources.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Créer un compte de stockage. Le nom de compte de stockage doit être unique dans Azure, donc fournir votre propre nom du compte.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Définir le compte de stockage en cours.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Créer un conteneur. L’autorisation est définie sur **désactiver** ce qui signifie que le conteneur est accessible uniquement au propriétaire.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Ajouter votre modèle au conteneur.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fournir des associations de sécurité jeton pendant le déploiement

Pour déployer un modèle privé dans un compte de stockage, récupérer un jeton associations de sécurité et l’inclure dans l’URI pour le modèle.

1. Si vous avez modifié le compte de stockage actuel, configurez le compte de stockage en cours celui qui contient vos modèles.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Créer un jeton associations de sécurité avec les autorisations de lecture et un délai d’expiration pour limiter l’accès. Récupérer l’URI complet du modèle, y compris le jeton associations de sécurité.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Déployez le modèle en fournissant l’URI qui inclut le jeton associations de sécurité.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Pour obtenir un exemple de l’utilisation d’un jeton associations de sécurité avec les modèles liées, voir [l’aide de modèles liées avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Priorité de paramètre

Vous pouvez utiliser les paramètres inline et un fichier de paramètre local dans la même opération de déploiement. Par exemple, vous pouvez spécifier des valeurs dans le fichier de paramètres locaux et ajouter d’autres en ligne des valeurs au cours du déploiement. Si vous indiquez les valeurs pour un paramètre dans le fichier de paramètres locaux et en ligne, la valeur inline est prioritaire.

Toutefois, vous ne pouvez pas utiliser les paramètres inline avec un fichier de paramètres externe. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri** , tous les paramètres inline sont ignorées. Vous devez fournir toutes les valeurs de paramètre dans le fichier externe. Si votre modèle inclut une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajouter cette valeur à un archivage sécurisé clé et faire référence à l’archivage sécurisé clé dans votre fichier de paramètres externe ou fournir dynamiquement toutes les paramètre valeurs en ligne.

Pour plus d’informations sur l’utilisation d’une référence KeyVault pour passer des valeurs sécurisés, voir [passer des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources via la bibliothèque de client .NET, consultez [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir les paramètres de modèle, voir [modèles de création](resource-group-authoring-templates.md#parameters).
- Pour des instructions sur le déploiement de votre solution aux différents environnements, voir [environnements de développement et test dans Microsoft Azure](solution-dev-test-environments.md).

