<properties
   pageTitle="Afficher les opérations de déploiement avec PowerShell | Microsoft Azure"
   description="Décrit comment utiliser la session PowerShell Azure pour détecter les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Afficher les opérations de déploiement avec PowerShell Azure

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure infrastructure du langage commun](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Vous pouvez afficher les opérations pour un déploiement via la session PowerShell Azure. Vous intéresser plus dans l’affichage les opérations lorsque vous avez reçu un message d’erreur pendant le déploiement afin de cet article se concentre sur l’affichage des opérations qui n’ont pas pu. PowerShell fournit des applets de commande qui vous permettent de facilement rechercher les erreurs et déterminer les corrections.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter des erreurs en validant votre infrastructure avant le déploiement et le modèle. Vous pouvez également enregistrer supplémentaires sur la demande et les informations de réponse au cours du déploiement qui peut-être être utile ultérieurement pour résoudre les problèmes. Pour en savoir plus sur la validation et les informations de demande et réponse journalisation, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Opérations de déploiement permet de résoudre les problèmes

1. Pour obtenir le statut d’un déploiement global, utilisez la commande **Get-AzureRmResourceGroupDeployment** . Vous pouvez filtrer les résultats pour seulement ces déploiements qui n’ont pas pu.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Qui renvoie les déploiements échecs au format suivant :
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Chaque déploiement est généralement constitué de plusieurs opérations, et chaque opération qui représente une étape du processus de déploiement. Pour découvrir la cause avec un déploiement, vous devez généralement afficher des détails sur les opérations de déploiement. Vous pouvez afficher l’état des opérations avec **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Qui retourne plusieurs opérations avec chacun d’eux dans le format suivant :
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Pour obtenir plus d’informations sur les opérations échecs, récupérer les propriétés pour les opérations avec l’état **a échoué** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Qui renvoie toutes les opérations échecs avec chacun d’eux dans le format suivant :
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Notez l’ID de suivi de l’opération. Vous allez utiliser que dans l’étape suivante pour vous concentrer sur une opération particulière.

4. Pour obtenir le message d’état d’une opération échec particulière, utilisez la commande suivante :

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Qui retourne :
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>Utilisation des journaux d’audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Pour extraire les entrées de journal, exécutez la commande **Get-AzureRmLog** . Vous pouvez utiliser les paramètres **ResourceGroup** et **l’état** pour renvoyer uniquement les événements qui a échoué pour un groupe de ressources unique. Si vous ne spécifiez pas une heure de début et de fin, entrées pour l’heure de dernière sont retournées.
Par exemple, pour récupérer les opérations échecs de la dernière heure exécuter :

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Vous pouvez spécifier un timespan particulier. Dans l’exemple suivant, nous allons examiner les actions en échec du dernier jour. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Ou bien, vous pouvez définir un exacte début et heure de fin les actions en échec :

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si cette commande renvoie trop d’entrées et propriétés, vous pouvez vous concentrer vos efforts audit en récupérant la propriété **Propriétés** . Nous allons également inclure le paramètre **DetailedOutput** pour afficher les messages d’erreur.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Qui renvoie les propriétés des entrées du journal au format suivant :
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. En fonction de ces résultats, nous allons vous concentrer sur le deuxième élément. Vous pouvez affiner les résultats en consultant le message d’état pour cette entrée.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Qui retourne :
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Étapes suivantes

- Pour vous aider à résoudre les erreurs de déploiement particulier, voir [résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).

