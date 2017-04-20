<properties
   pageTitle="Afficher les opérations de déploiement avec l’API REST | Microsoft Azure"
   description="Décrit comment utiliser le REST API de gestionnaire de ressources Azure pour détecter les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Afficher les opérations de déploiement avec le REST API Azure Gestionnaire de ressources

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure infrastructure du langage commun](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez reçu un message d’erreur lors du déploiement des ressources sur Azure, vous souhaiterez peut-être afficher plus de détails sur les opérations de déploiement qui ont été exécutées. L’API REST fournit des opérations qui vous permettent de rechercher les erreurs et déterminer les corrections.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter des erreurs en validant votre infrastructure avant le déploiement et le modèle. Vous pouvez également enregistrer supplémentaires sur la demande et les informations de réponse au cours du déploiement qui peut-être être utile ultérieurement pour résoudre les problèmes. Pour en savoir plus sur la validation et les informations de demande et réponse journalisation, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Résoudre les problèmes avec l’API REST

1. Déployer vos ressources avec l’opération de [Création d’un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Pour conserver les informations qui peuvent être utiles pour le débogage, définissez la propriété **debugSetting** dans demande JSON à **requestContent** et/ou **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
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
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Par défaut, la valeur **debugSetting** est définie sur **Aucun**. Lorsque vous spécifiez la valeur **debugSetting** , avec soin le type d’informations que vous passez dans pendant le déploiement. En enregistrant les informations relatives à la demande ou une réponse, vous pouvez potentiellement exposer des données sensibles sont extraite via les opérations de déploiement. 

2. Obtenir des informations sur un déploiement avec l’opération [d’obtenir des informations sur un déploiement du modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Dans la réponse, notez en particulier les éléments **provisioningState** , **ID de corrélation** et **d’erreur** . L' **ID de corrélation** est utilisé pour suivre les événements liés et peut s’avérer utile lorsque vous travaillez avec le support technique pour résoudre un problème.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. Obtenir des informations sur les opérations de déploiement avec l’opération de [toutes les opérations de déploiement de modèle de liste](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    La réponse inclut des informations de demande et/ou réponse basées sur ce que vous avez spécifié dans la propriété **debugSetting** pendant le déploiement.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Obtenir les événements à partir des journaux d’audit pour le déploiement avec l’opération [dans un abonnement, les événements de gestion de la liste](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Étapes suivantes

- Pour vous aider à résoudre les erreurs de déploiement particulier, voir [résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).
