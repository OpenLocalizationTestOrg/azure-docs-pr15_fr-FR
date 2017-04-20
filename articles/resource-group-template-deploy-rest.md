<properties
   pageTitle="Déployer des ressources avec l’API REST et modèle | Microsoft Azure"
   description="Utiliser le Gestionnaire de ressources Azure et REST API Gestionnaire de ressources pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle de gestionnaire de ressources."
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
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Déployer des ressources avec les modèles de gestionnaire de ressources et des REST API Gestionnaire de ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure infrastructure du langage commun](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cet article explique comment utiliser le REST API Gestionnaire de ressources avec le Gestionnaire de ressources modèles à déployer vos ressources Azure.  

> [AZURE.TIP] Pour obtenir de l’aide de débogage une erreur lors du déploiement, voir :
>
> - [Afficher les opérations de déploiement avec l’API REST](resource-manager-troubleshoot-deployments-rest.md) pour en savoir plus sur l’obtention d’informations qui vous permet de résoudre votre erreur
> - [Résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md) pour savoir comment résoudre les erreurs de déploiement

Votre modèle peut être un fichier local ou un fichier externe qui est disponible via un URI. Lorsque votre modèle est enregistré dans un compte de stockage, vous pouvez limiter l’accès au modèle et fournir un jeton de signature (sa) accès partagé pendant le déploiement.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Déployer avec l’API REST
1. Définissez [des en-têtes et des paramètres communs](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), y compris les jetons d’authentification.
2. Si vous ne disposez pas d’un groupe de ressources existant, créez un groupe de ressources. Fournir votre id de l’abonnement, le nom du nouveau groupe de ressources et emplacement dont vous avez besoin pour votre solution. Pour plus d’informations, voir [créer un groupe de ressources](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Validez votre déploiement avant l’exécution en exécutant l’opération [valider un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Lorsque vous testez le déploiement, fournissent des paramètres exactement comme vous le feriez lors de l’exécution du déploiement (comme illustré dans l’étape suivante).

3. Création d’un déploiement. Fournir votre id de l’abonnement, le nom du groupe de ressources pour déployer, le nom du déploiement et un lien vers votre modèle. Pour plus d’informations sur le fichier de modèle, voir le [fichier de paramètres](#parameter-file). Pour plus d’informations sur l’API REST pour créer un groupe de ressources, voir [créer un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx). Notez que le **mode** est défini sur **incrémentiel**. Pour exécuter un déploiement complet, définissez le **mode** sur **terminée**. Être prudent lorsque vous utilisez le mode complète que vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Si vous voulez connecter le contenu de la réponse, demandent le contenu ou les deux, incluent **debugSetting** dans la demande.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Vous pouvez configurer votre compte de stockage pour utiliser un jeton de signature (sa) accès partagé. Pour plus d’informations, voir [Délégation de l’accès avec une Signature accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obtenir l’état du déploiement modèle. Pour plus d’informations, consultez [obtenir des informations sur un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources via la bibliothèque de client .NET, consultez [ressources de déploiement à l’aide de bibliothèques .NET et un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir les paramètres de modèle, voir [modèles de création](resource-group-authoring-templates.md#parameters).
- Pour des instructions sur le déploiement de votre solution aux différents environnements, voir [environnements de développement et test dans Microsoft Azure](solution-dev-test-environments.md).
- Pour plus d’informations sur l’utilisation d’une référence KeyVault pour passer des valeurs sécurisés, voir [passer des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md).
