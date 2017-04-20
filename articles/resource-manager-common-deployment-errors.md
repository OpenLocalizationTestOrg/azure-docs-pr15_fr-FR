<properties
   pageTitle="Résoudre les erreurs courantes de déploiement d’Azure | Microsoft Azure"
   description="Décrit comment résoudre les erreurs courantes lorsque vous déployez ressources Azure à l’aide du Gestionnaire de ressources Azure."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="Erreur de déploiement, déploiement d’azure, déployer sur azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Résoudre les erreurs courantes de déploiement d’Azure avec le Gestionnaire de ressources Azure

Cette rubrique décrit comment vous pouvez résoudre courantes que vous pouvez rencontrer des erreurs de déploiement d’Azure. Si vous avez besoin de plus d’informations sur la cause avec votre déploiement, voir [Afficher les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md) et puis revenez à cet article pour vous aider à résoudre l’erreur. Les sections dans cette rubrique répertorient le code d’erreur.

## <a name="invalid-template"></a>Modèle non valide

Cette erreur peut se produire à partir de différents types d’erreurs. 

### <a name="syntax-error"></a>Erreur de syntaxe

Si vous recevez un message d’erreur qui indique la validation du modèle a échoué, vous devrez un problème de syntaxe dans votre modèle. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Cette erreur est classique, car les expressions de modèle peuvent être compliquées. Par exemple, l’affectation du nom suivant pour un compte de stockage contient une paire de crochets, trois fonctions, trois jeux de parenthèses, un ensemble de guillemets simples et propriété :

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si vous ne fournissez pas la syntaxe de correspondance, le modèle de produit une valeur qui est différente de celui votre intention.

Lorsque vous recevez ce type d’erreur, attentivement la syntaxe d’expression. Envisagez d’utiliser un éditeur de JSON tel que [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou de [Code de Visual Studio](resource-manager-vs-code.md), qui vous avertit des erreurs de syntaxe. 

### <a name="incorrect-segment-lengths"></a>Longueurs des segments incorrect

Un autre modèle non valide erreur se produit lorsque le nom de la ressource n’est pas au format correct.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Une ressource de niveau racine doit avoir un moins segment dans le nom que dans le type de ressource. Chaque segment est se distinguent par une barre oblique. Dans l’exemple suivant, le type possède deux segments et le nom comporte un seul segment, afin qu’elle soit un **nom valide**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Mais l’exemple suivant n’est **pas un nom valide** , car il a le même nombre de segments comme type de.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Pour les ressources enfant, le type et le nom ont le même nombre de segments. Ce nombre de segments a du sens, car le nom complet et le type de l’enfant inclut le nom du parent et le type. Par conséquent, le nom complet comporte toujours un segment de moins que le type de total. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Obtenir les segments droite peut s’avérer difficile avec des types de gestionnaire de ressources qui sont appliqués au sein de fournisseurs de ressources. Par exemple, appliquer un verrouillage de la ressource à un site web nécessite un type avec quatre segments. Par conséquent, le nom est trois segments :

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Index de copie n’est pas prévu

Vous rencontrez cette erreur **InvalidTemplate** lorsque vous avez appliqué l’élément **copie** à un composant de modèle qui ne prend pas en charge cet élément. Vous pouvez uniquement appliquer l’élément copie à un type de ressource. Vous ne pouvez pas appliquer la copie sur une propriété au sein d’un type de ressource. Par exemple, vous appliquez la copie à une machine virtuelle, mais vous ne pouvez pas l’appliquer aux disques du système d’exploitation pour une machine virtuelle. Dans certains cas, vous pouvez convertir une ressource enfant à une ressource parent pour créer une boucle de copie. Pour plus d’informations sur l’utilisation de copie, voir [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md).

### <a name="parameter-is-not-valid"></a>Paramètre n’est pas valide

Si le modèle spécifie les valeurs autorisées pour un paramètre, et vous fournir une valeur qui ne fait pas partie de ces valeurs, vous recevez un message semblable à l’erreur suivante :

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Appuyez deux fois vérifier les valeurs autorisées dans le modèle, puis fournir une pendant le déploiement.

## <a name="not-found-or-resource-not-found"></a>Pas (ou ressource introuvable)

Lorsque votre modèle inclut le nom d’une ressource ne peut pas être résolu, vous recevez une erreur semblable à :

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Si vous tentez de déployer la ressource manquante dans le modèle, vérifiez si vous avez besoin d’ajouter une dépendance. Gestionnaire de ressources optimise déploiement en créant des ressources en parallèle, lorsque cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** dans votre modèle pour créer une dépendance sur la ressource d’autres. Par exemple, lorsque vous déployez une application web, le plan de services d’application doit exister. Si vous n’avez pas spécifié que l’application web varie selon le plan de services d’application, le Gestionnaire de ressources crée les deux ressources en même temps. Vous recevez une erreur indiquant que la ressource de plan de Service d’application ne peut pas être trouvée, car il n’existe pas encore lorsque vous tentez de configurer des propriétés de l’application web. Vous éviter cette erreur en définissant la dépendance dans l’application web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
Vous voyez également cette erreur lorsque la ressource se trouve dans un groupe de ressources autre que celui déployé pour. Dans ce cas, utilisez la [fonction resourceId](resource-group-template-functions.md#resourceid) pour obtenir le nom complet de la ressource.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Si vous essayez d’utiliser les fonctions de [référence](resource-group-template-functions.md#referenc) ou [listKeys](resource-group-template-functions.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez l’erreur suivante :

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Recherchez une expression qui inclut la fonction de **référence** . Vérifiez que les valeurs de paramètres sont corrects.

## <a name="storage-account-already-exists-or-already-taken"></a>Compte de stockage existe déjà (ou déjà pris)

Pour les comptes de stockage, vous devez fournir un nom pour la ressource qui est unique dans Azure. Si vous ne fournissez pas un nom unique, vous recevez un message d’erreur comme :

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Vous pouvez créer un nom unique en concaténant votre convention d’appellation avec le résultat de la fonction [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Si vous déployez un compte de stockage portant le même nom qu’un compte de stockage existant dans votre abonnement, mais que vous fournissez un autre emplacement, vous recevez une erreur indiquant que le compte de stockage existe déjà dans un autre emplacement. Supprimez le compte de stockage existant, ou fournissez au même emplacement que le compte de stockage existant.

## <a name="account-name-invalid"></a>Nom du compte non valide

Vous voyez l’erreur **AccountNameInvalid** lorsque vous tentez de donnez un nom qui inclut à un compte de stockage communiquons caractères. Noms des comptes de stockage doivent être comprise entre 3 et 24 caractères et utiliser des nombres et les lettres minuscules uniquement.

## <a name="no-registered-provider-found"></a>Aucun fournisseur enregistré trouvé

Lorsque vous déployez des ressources, vous pouvez recevoir le code d’erreur suivant et le message :

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Vous recevez cette erreur pour l’une des trois raisons :

1. Emplacement non pris en charge pour le type de ressource
2. Version de l’API non prises en charge pour le type de ressource
3. Le fournisseur de ressources n’a pas été enregistré pour votre abonnement

Le message d’erreur devrait vous donner des suggestions pour les emplacements pris en charge et les versions de l’API. Vous pouvez modifier votre modèle à l’une des valeurs suggérées. La plupart des fournisseurs sont enregistrées automatiquement par le portail Azure ou l’interface de ligne de commande que vous utilisez, mais pas toutes. Si vous n’avez pas utilisé un fournisseur de ressources particulier avant, vous devrez peut-être enregistrer ce fournisseur. Vous pouvez en savoir plus sur les fournisseurs de ressources à l’aide de PowerShell ou Azure infrastructure du langage commun.

### <a name="powershell"></a>PowerShell

Pour afficher votre statut d’enregistrement, utilisez **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Pour enregistrer un fournisseur, utilisez **Registre AzureRmResourceProvider** et indiquez le nom du fournisseur de ressources que vous souhaitez enregistrer.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Pour obtenir les emplacements pris en charge pour un type particulier de ressource, utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Pour obtenir les versions de l’API pris en charge pour un type particulier de ressource, utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>Azure infrastructure du langage commun

Pour voir si le fournisseur est enregistré, utilisez la `azure provider list` commande.

    azure provider list

Pour enregistrer un fournisseur de ressources, utilisez la `azure provider register` de commande, puis spécifiez l' *espace de noms* pour enregistrer.

    azure provider register Microsoft.Cdn

Pour afficher les emplacements pris en charge et les versions de l’API pour un fournisseur de ressources, utilisez :

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Opération non autorisée

Vous pouvez rencontrer des problèmes lorsque déploiement dépasse un quota, ce qui pourrait être par groupe de ressources, les abonnements, les comptes et les autres étendues. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs d’une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que le montant autorisé, vous recevez une erreur indiquant que le quota dépassé.
Pour plus d’informations de quota terminée, voir [abonnement Azure et les limites de service, les quotas et les contraintes](azure-subscription-service-limits.md).

Pour examiner les quotas de votre abonnement pour cœurs, vous pouvez utiliser la `azure vm list-usage` commande dans l’infrastructure du langage commun Azure. L’exemple suivant montre que le quota de base d’un compte d’évaluation gratuit est 4 :

    azure vm list-usage

Qui retourne :

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si vous déployez un modèle qui crée plus de quatre cœurs dans la région Ouest américain, vous obtenez une erreur de déploiement qui ressemble à :

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

Ou dans PowerShell, vous pouvez utiliser l’applet de commande **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Qui retourne :

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Dans ce cas, vous devez accédez au portail et un problème de support pour déclencher votre quota pour la région dans lequel vous voulez déployer des fichiers.

> [AZURE.NOTE] N’oubliez pas que pour les groupes de ressource, le quota de chaque région individuelle, pas pour tout l’abonnement. Si vous avez besoin déployer 30 cœurs ouest aux États-Unis, vous devez demander à 30 cœurs directeur des ressources aux États-Unis Ouest. Si vous avez besoin déployer 30 cœurs dans une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Gestionnaire de ressources dans toutes les régions.

## <a name="invalid-content-link"></a>Lien de contenu non valide

Lorsque vous recevez le message d’erreur :

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Vous avez tenté probablement créer un lien vers un modèle imbriqué n’est pas disponible. Vérifiez l’URI que vous avez fourni pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, vérifiez que l’URI est accessible. Vous devrez peut-être passer un jeton associations de sécurité. Pour plus d’informations, voir [l’aide de modèles liées avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Échoué de l’autorisation

Vous pouvez recevoir un message d’erreur lors du déploiement, car le compte ou le service d’essayer de déployer les ressources principal n’a pas accès à ces actions. Vous permet d’Azure Active Directory ou votre administrateur pour contrôler les identités qui peut accéder à quelles ressources avec un degré élevé de précision. Par exemple, si votre compte est affecté le rôle de lecteur, vous n’êtes pas en mesure de créer des ressources. Dans ce cas, vous voyez un message d’erreur indiquant que l’autorisation a échoué.

Pour plus d’informations sur le contrôle d’accès basé sur un rôle, voir [Azure Role-Based le contrôle d’accès](./active-directory/role-based-access-control-configure.md).

En plus de contrôle d’accès basé sur un rôle, vos actions de déploiement limitation possible par les stratégies de l’abonnement. Par le biais des stratégies, l’administrateur peut appliquer les conventions sur toutes les ressources déployées dans l’abonnement. Par exemple, un administrateur peut exiger qu’une valeur de balise particulière est fournie pour un type de ressource. Si vous ne répondent pas à des critères de la stratégie, vous recevez un message d’erreur pendant le déploiement. Pour plus d’informations sur les stratégies, voir [Utiliser une stratégie pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Machines virtuelles de résolution des problèmes

| Erreur | Articles |
| -------- | ----------- |
| Erreurs d’extension de script personnalisé | [Échecs d’extension machine virtuelle Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />ou<br />[Échecs d’extension Linux VM](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Image du système d’exploitation mise en service des erreurs | [Nouvelles erreurs machine virtuelle Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />ou<br />[Nouvelles erreurs Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Échecs d’allocation | [Échecs d’allocation de mémoire virtuelle Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />ou<br />[Échecs d’allocation Linux VM](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Sécuriser les erreurs SSH (Shell) lorsque vous tentez de vous connecter | [Sécuriser les connexions Shell à Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Erreurs de connexion à l’application qui s’exécute sur machine virtuelle | [Application s’exécutant sur machine virtuelle Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />ou<br />[Application s’exécutant sur un Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Erreurs de connexion Bureau à distance | [Connexions de bureau à distance à la machine virtuelle Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Erreurs de connexion résolus en redéployez | [Redéployez Machine virtuelle à nouveau nœud Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Erreurs du service cloud | [Problèmes de déploiement de service cloud](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Résolution des problèmes d’autres services

Le tableau suivant n’est pas une liste complète des rubriques de dépannage pour Azure. Au lieu de cela, il se concentre sur les problèmes liés au déploiement ou configurez des ressources. Si vous avez besoin d’aide pour résoudre les problèmes d’exécution avec une ressource, consultez la documentation de ce service Azure.

| Service | Article |
| -------- | -------- |
| Automatisation | [Conseils de dépannage des erreurs courantes dans Azure Automation](./automation/automation-troubleshooting-automation-errors.md) |
| Pile Azure | [Résolution des problèmes de Microsoft Azure pile](./azure-stack/azure-stack-troubleshooting.md) |
| Pile Azure | [Applications Web et la pile Azure](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Données par défaut | [Résoudre les problèmes de données par défaut](./data-factory/data-factory-troubleshoot.md) |
| TISSU de service | [Résoudre les problèmes courants lorsque vous déployez services sur tissu de Service Azure](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Récupération de site | [Surveiller et résoudre les problèmes de protection des machines virtuelles et des serveurs physiques](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Espace de stockage | [Surveiller, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Résoudre les problèmes de déploiement de périphérique StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Base de données SQL | [Résoudre les problèmes de connexion à la base de données SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Data Warehouse SQL | [Résolution des problèmes de Data Warehouse SQL Azure](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Comprendre qu’un déploiement est prêt

Le Gestionnaire de ressources Azure signale de réussite sur un déploiement lorsque tous les fournisseurs de retour à partir de déploiement avec succès. Toutefois, ce message ne pas nécessairement que votre groupe de ressources est « active et vous êtes prêt pour vos utilisateurs. » Par exemple, un déploiement devrez télécharger mises à jour, attendez sur des ressources sans modèle ou installer des scripts complexes. Gestionnaire de ressources ne sait pas lorsque ces tâches terminées, car ils ne sont pas des activités qui effectue le suivi un fournisseur. Dans ce cas, il peut être certain temps avant que vos ressources sont prêts à l’emploi réel. Par conséquent, vous devez vous attendre que l’état du déploiement réussit peu de temps avant de pouvoir utiliser votre déploiement.

Vous pouvez empêcher Azure de réussite déploiement, cependant, en créant un script personnalisé pour votre modèle personnalisé. Le script sait comment contrôler la totalité du déploiement pour la mise en réseau et retourne correctement uniquement lorsque les utilisateurs peuvent interagir avec la totalité du déploiement. Si vous souhaitez vous assurer que votre numéro de poste est le dernier à exécuter, utilisez la propriété **dependsOn** dans votre modèle.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’audit des actions, consultez [opérations d’Audit avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour connaître les actions pour déterminer les erreurs au cours du déploiement, voir [Afficher les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md).
