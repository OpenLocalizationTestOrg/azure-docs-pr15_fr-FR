<properties
   pageTitle="Afficher les opérations de déploiement avec Azure infrastructure du langage commun | Microsoft Azure"
   description="Décrit comment utiliser l’infrastructure du langage commun Azure pour détecter les problèmes de déploiement du Gestionnaire de ressources."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Afficher les opérations de déploiement avec Azure infrastructure du langage commun

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure infrastructure du langage commun](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez reçu un message d’erreur lors du déploiement des ressources sur Azure, vous souhaiterez peut-être afficher plus de détails sur les opérations de déploiement qui ont été exécutées. Azure infrastructure du langage commun fournit des commandes qui permettent de vous trouver les erreurs afin de déterminer les corrections.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter des erreurs en validant votre infrastructure avant le déploiement et le modèle. Vous pouvez également enregistrer supplémentaires sur la demande et les informations de réponse au cours du déploiement qui peut-être être utile ultérieurement pour résoudre les problèmes. Pour en savoir plus sur la validation et les informations de demande et réponse journalisation, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>Utilisation des journaux d’audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Pour afficher les journaux d’audit, exécutez la commande **Afficher les journaux de groupe azure** . Vous pouvez inclure le **--dernier déploiement** option pour récupérer uniquement le journal pour le déploiement plus récent.

        azure group log show ExampleGroup --last-deployment

2. La commande **Afficher les journaux de groupe azure** renvoie un grand nombre d’informations. Pour résoudre les problèmes que vous voulez généralement concentrer sur les opérations impossibles. Le script suivant utilise l’option **--json** et l’utilitaire JSON [jq](https://stedolan.github.io/jq/) pour effectuer une recherche dans le journal des échecs de déploiement.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    Vous pouvez voir **Propriétés** inclut des informations dans json à propos de l’opération qui a échoué.

    Vous pouvez utiliser la **--détaillée** et options **- vv** pour afficher plus d’informations dans les journaux.  Utiliser la **--détaillée** option pour afficher les étapes les opérations traitée sur `stdout`. Pour un historique demande complète, utilisez l’option **- vv** . Les messages fournissent souvent précieux indices sur la cause des échecs.

3. Pour vous concentrer sur le message d’état pour les entrées échecs, utilisez la commande suivante :

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Opérations de déploiement permet de résoudre les problèmes

1. Obtenir le statut d’un déploiement avec la commande **déploiement d’un groupe azure show** global. Dans l’exemple ci-dessous, le déploiement a échoué.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Pour afficher le message pour les opérations de l’échecs d’un déploiement, utilisez :

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Étapes suivantes

- Pour vous aider à résoudre les erreurs de déploiement particulier, voir [résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).
