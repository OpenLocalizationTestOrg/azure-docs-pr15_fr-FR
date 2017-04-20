<properties
   pageTitle="Déployer des ressources avec Azure infrastructure du langage commun et modèle | Microsoft Azure"
   description="Utilisez le Gestionnaire de ressources Azure et Azure infrastructure du langage commun à déployer un ressources Azure. Les ressources sont définies dans un modèle de gestionnaire de ressources."
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

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Déployer des modèles de gestionnaire de ressources et Azure infrastructure du langage commun des ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure infrastructure du langage commun](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique explique comment utiliser Azure infrastructure du langage commun avec les modèles de gestionnaire de ressources pour déployer vos ressources sur Azure.  

> [AZURE.TIP] Pour obtenir de l’aide de débogage une erreur lors du déploiement, voir :
>
> - [Afficher les opérations de déploiement avec Azure infrastructure du langage commun](resource-manager-troubleshoot-deployments-cli.md) pour en savoir plus sur l’obtention d’informations qui vous permet de résoudre votre erreur
> - [Résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md) pour savoir comment résoudre les erreurs de déploiement

Votre modèle peut être un fichier local ou un fichier externe qui est disponible via un URI. Lorsque votre modèle est enregistré dans un compte de stockage, vous pouvez limiter l’accès au modèle et fournir un jeton de signature (sa) accès partagé pendant le déploiement.

## <a name="quick-steps-to-deployment"></a>Étapes de déploiement rapides

Cet article décrit toutes les différentes options à votre disposition pendant le déploiement. Toutefois, fréquence à laquelle vous ne devez deux commandes. Pour démarrer rapidement de déploiement, utilisez les commandes suivantes :

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Pour en savoir plus sur les options de déploiement, qui peuvent être mieux adaptés à votre scénario, poursuivez la lecture de cet article.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Déployer avec Azure infrastructure du langage commun

Si vous n'avez pas déjà utilisé Azure infrastructure du langage commun avec le Gestionnaire de ressources, reportez-vous [à l’aide de l’infrastructure du langage commun Azure pour Mac, Linux et Windows Azure Gestion des ressources](xplat-cli-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure. Après avoir fourni vos informations d’identification, la commande renvoie le résultat de votre nom d’utilisateur.

        azure login
  
        ...
        info:    login command OK

2. Si vous avez plusieurs abonnements, fournissent l’id de l’abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez vers le Gestionnaire de ressources Azure module. Vous recevez la confirmation du nouveau mode.

        azure config mode arm
   
        info:     New mode is arm

4. Si vous ne disposez pas d’un groupe de ressources existant, créez un groupe de ressources. Indiquez le nom du groupe de ressources et emplacement dont vous avez besoin pour votre solution. Vous devez fournir un emplacement pour le groupe de ressources, car le groupe de ressources stocke les métadonnées relatives aux ressources. Pour des raisons de conformité, vous souhaiterez spécifier l’emplacement de stockage que les métadonnées. En règle générale, nous vous recommandons de spécifier un emplacement dans lequel se trouve la plupart de vos ressources. À l’aide de la même emplacement peut simplifier un modèle.

        azure group create -n ExampleResourceGroup -l "West US"

     Un résumé du nouveau groupe de ressources est renvoyé.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Validez votre déploiement avant l’exécution en exécutant la commande **modèle azure groupe valider** . Lorsque vous testez le déploiement, fournissent des paramètres exactement comme vous le feriez lors de l’exécution du déploiement (comme illustré dans l’étape suivante).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Pour déployer des ressources à votre groupe de ressources, exécutez la commande suivante et fournissez les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle et d’autres paramètres nécessaires pour votre scénario. 
   
     Vous avez trois options permettant de fournir des valeurs de paramètre suivantes : 

     1. Utilisez les paramètres inline et un modèle local. Chaque paramètre est au format : `"ParameterName": { "value": "ParameterValue" }`. L’exemple suivant affiche les paramètres avec des caractères d’échappement.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Utiliser des paramètres en ligne et un lien vers un modèle.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Utiliser un fichier de paramètres. Pour plus d’informations sur le fichier de modèle, voir le [fichier de paramètres](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Une fois que les ressources ont été déployés via un des trois méthodes ci-dessus, vous verrez une synthèse du déploiement.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Pour exécuter un déploiement complet, définissez le **mode** sur **terminée**. Être prudent lorsque vous utilisez ce mode que vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Si vous voulez enregistrer les informations supplémentaires sur le déploiement qui peut-être vous aider à résoudre des erreurs de déploiement, utilisez le paramètre de **débogage** . Vous pouvez spécifier que demandent le contenu, le contenu de la réponse ou les deux être enregistrés avec l’opération de déploiement.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Déployez le modèle à partir du stockage avec jeton associations de sécurité

Vous pouvez ajouter vos modèles à un compte de stockage et lier ses pendant le déploiement avec un jeton associations de sécurité.

> [AZURE.IMPORTANT] En suivant les étapes ci-dessous, le blob qui contient le modèle est accessible à seul le propriétaire du compte. Toutefois, lorsque vous créez un jeton associations de sécurité pour l’objets blob, le blob est accessible à toutes les personnes avec cet URI. Si un autre utilisateur intercepte l’URI, cet utilisateur ne peut accéder au modèle. À l’aide d’un jeton de sa constitue un bon moyen de limiter l’accès à vos modèles, mais vous ne pas incluez des données sensibles telles que les mots de passe directement dans le modèle.

### <a name="add-private-template-to-storage-account"></a>Ajouter modèle privé à un compte de stockage

Les étapes suivantes configurer un compte de stockage des modèles :

1. Créer un groupe de ressources.

        azure group create -n "ManageGroup" -l "westus"

2. Créer un compte de stockage. Le nom de compte de stockage doit être unique dans Azure, donc fournir votre propre nom du compte.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Définir des variables pour le compte de stockage et la clé.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Créer un conteneur. L’autorisation est définie sur **désactiver** ce qui signifie que le conteneur est accessible uniquement au propriétaire.

        azure storage container create --container templates -p Off 
        
4. Ajouter votre modèle au conteneur.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fournir des associations de sécurité jeton pendant le déploiement

Pour déployer un modèle privé dans un compte de stockage, récupérer un jeton associations de sécurité et l’inclure dans l’URI pour le modèle.

1. Créer un jeton associations de sécurité avec les autorisations de lecture et un délai d’expiration pour limiter l’accès. Définir le délai d’expiration pour permettre suffisamment de temps effectuer le déploiement. Récupérer l’URI complet du modèle, y compris le jeton associations de sécurité.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Déployez le modèle en fournissant l’URI qui inclut le jeton associations de sécurité.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Pour obtenir un exemple de l’utilisation d’un jeton associations de sécurité avec les modèles liées, voir [l’aide de modèles liées avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources via la bibliothèque de client .NET, consultez [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir les paramètres de modèle, voir [modèles de création](resource-group-authoring-templates.md#parameters).
- Pour des instructions sur le déploiement de votre solution aux différents environnements, voir [environnements de développement et test dans Microsoft Azure](solution-dev-test-environments.md).
- Pour plus d’informations sur l’utilisation d’une référence KeyVault pour passer des valeurs sécurisés, voir [passer des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md).

