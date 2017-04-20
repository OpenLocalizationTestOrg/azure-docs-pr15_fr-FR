<properties 
    pageTitle="Déplacer les ressources vers le nouveau groupe de ressources | Microsoft Azure" 
    description="Utilisez le Gestionnaire de ressources Azure pour déplacer des ressources d’un nouveau groupe de ressources ou d’abonnement." 
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
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Déplacer des ressources à nouveau groupe de ressources ou d’abonnement

Cette rubrique vous montre comment déplacer des ressources vers un nouvel abonnement ou un groupe de ressources dans le même abonnement. Vous pouvez utiliser le portail, PowerShell, Azure infrastructure du langage commun ou l’API REST pour déplacer des ressources. Les opérations de déplacement dans cette rubrique sont disponibles pour vous sans l’aide du support technique Azure.

En règle générale, vous déplacez des ressources lorsque vous décidez que :

- À des fins de facturation, une ressource doit résident dans un autre abonnement.
- Une ressource partage n’est plus le cycle de vie même en tant que les ressources qu’il a été précédemment groupé avec. Vous voulez déplacer vers un nouveau groupe de ressources qui vous pouvez de gérer cette ressource séparément à partir d’autres ressources.

Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant l’opération. Écrire et supprimer des opérations sont bloqués sur les groupes que le déplacement soit terminée.

Vous ne pouvez pas modifier l’emplacement de la ressource. Déplacement d’une ressource uniquement déplacer le message vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut avoir un autre emplacement, mais qui ne modifie pas l’emplacement de la ressource.

> [AZURE.NOTE] Cet article décrit comment déplacer des ressources au sein d’un Azure existant du compte d’offres. Si vous avez vraiment besoin de modifier votre compte Azure offrant (par exemple, la mise à niveau à partir de paiement pour prépayer) tout en continuant à travailler avec vos ressources existantes, voir [changer votre abonnement Azure à une autre offre](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Liste de vérification avant de déplacer des ressources

Il existe certaines étapes importantes à effectuer avant de passer d’une ressource. En vérifiant ces conditions, vous pouvez éviter les erreurs.

1. Le service doit activer la possibilité de déplacer des ressources. Consultez la liste ci-dessous pour plus d’informations sur les [services permettent déplacement des ressources](#services-that-enable-move).
1. Les abonnements source et de destination doivent exister dans le même [client Active Directory](./active-directory/active-directory-howto-tenant.md). Pour déplacer vers un nouveau client, appelez le support.
2. L’abonnement de destination doit être inscrit pour le fournisseur de ressources de la ressource est déplacé. Dans le cas contraire, vous recevez un message d’erreur indiquant que l' **abonnement n’est pas enregistré pour un type de ressource**. Vous pouvez rencontrer ce problème lors du déplacement d’une ressource à un nouvel abonnement, mais que l’abonnement n’a jamais été utilisé avec ce type de ressource. Pour savoir comment vérifier l’état d’enregistrement et enregistrer des fournisseurs de ressources, consultez [types et fournisseurs de ressources](../resource-manager-supported-services.md#resource-providers-and-types).
4. Si vous déplacez une application de Service d’application, vous avez vérifié [limitations du Service d’application](#app-service-limitations).
4. Si vous déplacez des ressources associés aux Services de récupération, vous avez vérifié les [limitations des Services de récupération](#recovery-services-limitations)
5. Si vous déplacez des ressources déployées via modèle classique, vous avez vérifié [limites du déploiement classique](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Quand appeler le support

Vous pouvez déplacer la plupart des ressources via les opérations libre-service présentées dans cette rubrique. Utiliser les opérations libre-service pour :

- Déplacer des ressources du Gestionnaire de ressources.
- Déplacer des ressources classiques selon les [limites du déploiement classique](#classic-deployment-limitations). 

Appeler le support lorsque vous avez besoin :

- Déplacer vos ressources et à un nouveau compte Azure (client Active Directory).
- Déplacer des ressources classiques, mais avez des difficultés avec les limitations.

## <a name="services-that-enable-move"></a>Services d’activation de déplacement

Pour l’instant, les services qui permettent de déplacement vers un nouveau groupe de ressources et de l’abonnement sont :

- Gestion des API
- Applications de Service d’application (applications web) - voir [limitations du Service d’application](#app-service-limitations)
- Automatisation
- Traitement par lots
- CDN
- Services en nuage - voir [limitations de déploiement classique](#classic-deployment-limitations)
- Données par défaut
- DNS
- DocumentDB
- HDInsight clusters
- Hubs IoT
- Archivage sécurisé clé 
- Services de support
- Engagement mobile
- Notification Hubs
- Perspectives opérationnelles
- Redis Cache
- Planificateur
- Recherche
- Bus des services
- Espace de stockage
- Stockage (standard) - voir [limitations de déploiement classique](#classic-deployment-limitations)
- Serveur de base de données SQL - la base de données et le serveur doit se trouver dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL server, toutes les bases de données sont également déplacés.
- Machines virtuelles - Toutefois, est pas prise en charge déplacer vers un nouvel abonnement lorsque ses certificats sont stockés dans un archivage sécurisé clé
- Machines virtuelles (standard) - voir [limitations de déploiement classique](#classic-deployment-limitations)
- Réseaux virtuels

## <a name="services-that-do-not-enable-move"></a>Services qui ne permettent pas de déplacement

Les services actuellement n’activez pas le déplacement d’une ressource sont :

- Passerelle d’application
- Analyse de l’application
- Voie Express
- Services de récupération vault - également ne pas déplacer les ressources informatiques, de réseau et de stockage associées à l’archivage sécurisé aux Services de récupération, voir [limitations des Services de récupération](#recovery-services-limitations).
- Machines virtuelles avec certificat stocké dans l’archivage sécurisé clé
- Machines virtuelles échelle jeux
- Réseaux virtuels (standard) - voir [limitations de déploiement classique](#classic-deployment-limitations)
- Passerelle VPN

## <a name="app-service-limitations"></a>Limitations du Service d’application

Lorsque vous travaillez avec des applications de Service d’application, vous ne pouvez pas déplacer uniquement un plan de services d’application. Pour déplacer des applications de Service d’application, les options disponibles sont :

- Déplacer le plan de services d’application et toutes les autres ressources du Service d’application dans ce groupe de ressources à un nouveau groupe de ressources qui ne dispose pas encore de ressources du Service d’application. Cette exigence signifie que vous devez déplacer même les ressources de Service d’application qui ne sont pas associés au plan de services d’application. 
- Déplacez les applications à un groupe de ressources différent, tout en conservant toutes les offres de Service d’application dans le groupe de ressources d’origine.

Si votre groupe de ressources d’origine comprend également une ressource d’Application perspectives, vous ne pouvez pas déplacer cette ressource car Insights Application ne permet pas actuellement l’opération de déplacement. Si vous incluez la ressource Insights Application lorsque vous déplacez les applications de Service d’application, l’opération de déplacement entière échoue. Toutefois, l’analyse des applications et plan de services d’application non dois-je résident dans le même groupe de ressources en tant que l’application pour l’application de fonctionner correctement.

Par exemple, si votre groupe de ressources contient :

- **Web-a** qui sont associés à **un plan** et **analyse de l’application a**
- **-b web** qui est associée avec **plan b** et **application-b perspectives**

Les options disponibles sont :

- Déplacer **un web**, **un plan**, **web-b**et **plan b**
- Déplacer **un web** et **web b**
- Déplacer **un web**
- Déplacer le **web-b**

Toutes les autres combinaisons impliquent déplaçant un type de ressource qui ne peut pas déplacer (Application Insights) ou en laissant à la place un type de ressource ne peut pas être conservé lorsque vous déplacez un plan de services d’application (n’importe quel type de ressource de Service d’application).

Si votre application web réside dans un groupe de ressources différent de son offre de Service d’application mais que vous voulez déplacer les deux, à un groupe de ressources, vous devez effectuer le déplacement en deux étapes. Par exemple :

- **un Web** réside dans le **groupe web**
- **un plan** réside dans le **groupe de plan**
- Vous voulez **web-a** et **un plan** se trouve dans le **groupe combinés**

Pour effectuer cette opération, effectuez deux opérations distinctes déplacer dans l’ordre suivant :

1. Déplacer le **web-a** au **groupe de plan**
2. Déplacer **un web** et **offre un** au **groupe combinées**.

Vous pouvez déplacer un certificat de Service d’application d’un nouveau groupe de ressources ou d’abonnement sans problème. Toutefois, si votre application web inclut un certificat SSL que vous avez acheté à l’extérieur et téléchargé dans l’application, vous devez supprimer le certificat avant de passer de l’application web. Par exemple, vous pouvez effectuer les opérations suivantes :

1. Supprimer le certificat de télécharger à partir de l’application web
2. Déplacer l’application web
3. Télécharger le certificat à l’application web

## <a name="recovery-services-limitations"></a>Limitations des Services de récupération

Déplacer n’est pas activée pour le stockage, réseau, ou le calcul des ressources utilisées pour configurer sinistre avec récupération de Site Azure. 

Par exemple, supposons que vous avez défini la réplication de vos ordinateurs locaux pour un compte de stockage (stockage1) et que vous souhaitez que l’ordinateur protégé afin de parvenir après le basculement vers Azure comme une machine virtuelle (VM1) jointe à un réseau virtuel (périphériques.1). Une de ces ressources Azure - stockage1 VM1 et périphériques.1 - Impossible de déplacer au sein de groupes de ressources dans le même abonnement ou abonnements.

## <a name="classic-deployment-limitations"></a>Limitations de déploiement classique

Les options de déplacement des ressources déployés via le modèle classique varient en fonction de si vous déplacez les ressources au sein d’un abonnement ou vers un nouvel abonnement. 

### <a name="same-subscription"></a>Même abonnement

Lorsque vous déplacez des ressources à partir d’un groupe de ressources à un autre groupe de ressources dans le même abonnement, les restrictions suivantes s’appliquent :

- Réseaux virtuels (classiques) ne peuvent pas être déplacés.
- Machines virtuelles (classique) doivent être déplacés avec le service cloud. 
- Service cloud peut uniquement être déplacé lorsque le déplacement inclut toutes ses machines virtuelles.
- Service cloud qu’un seul peut être déplacé à la fois.
- Un seul compte de stockage (classique) peut être déplacé à la fois.
- Compte de stockage (classique) ne peuvent pas être déplacé dans la même opération avec une machine virtuelle ou un service cloud.

Pour déplacer des ressources classiques à un nouveau groupe de ressources dans le même abonnement, utilisez les actions de déplacement standard via le [portail](#use-portal), [Azure PowerShell](#use-powershell), [Azure infrastructure du langage commun](#use-azure-cli)ou [API REST](#use-rest-api). Vous utilisez les mêmes opérations que vous utilisez pour le déplacement des ressources du Gestionnaire de ressources.

### <a name="new-subscription"></a>Nouvel abonnement

Lorsque vous déplacez des ressources vers un nouvel abonnement, les restrictions suivantes s’appliquent :

- Toutes les ressources classiques de l’abonnement doivent être déplacés dans la même opération.
- L’abonnement cible ne doit pas contenir d’autres ressources classiques.
- Le déplacement peut uniquement être demandé via une API REST distincts pour les déplacements classiques. Les commandes de déplacer le Gestionnaire de ressources standard ne fonctionnent pas lorsque vous déplacez des ressources classiques vers un nouvel abonnement.

Pour déplacer des ressources classiques vers un nouvel abonnement, vous devez utiliser opérations reste qui sont spécifiques aux ressources classiques. Procédez comme suit pour déplacer des ressources classiques vers un nouvel abonnement.

1. Vérifier si l’abonnement source peut participer à un déplacement entre abonnement. Utilisez l’opération suivante :

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     Dans le corps de la demande, sont les suivantes :

         {
           "role": "source"
         }

     La réponse pour l’opération de validation est au format suivant :

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Vérifier si l’abonnement de destination peut participer à un déplacement entre abonnement. Utilisez l’opération suivante :

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     Dans le corps de la demande, sont les suivantes :

         {
           "role": "target"
         }

     La réponse se trouve dans le même format que la validation d’abonnement source.

3. Si les deux abonnements passent le contrôle, déplacer toutes les ressources classiques d’un abonnement à un autre abonnement avec l’opération suivante :

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    Dans le corps de la demande, sont les suivantes :

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

L’opération peut prendre plusieurs minutes. 

## <a name="use-portal"></a>Utiliser le portail

Pour déplacer des ressources à un nouveau groupe de ressources dans **même abonnement**, sélectionnez le groupe de ressources contenant ces ressources, puis le bouton **déplacer** .

![déplacer des ressources](./media/resource-group-move-resources/edit-rg-icon.png)

Ou, pour déplacer des ressources vers un **nouvel abonnement**, sélectionnez le groupe de ressources contenant ces ressources, puis l’icône d’abonnement de modification.

![déplacer des ressources](./media/resource-group-move-resources/change-subscription.png)

Sélectionnez les ressources à déplacer et le groupe de ressources de destination. Acceptez que vous devez mettre à jour les scripts pour ces ressources, puis sélectionnez **OK**. Si vous avez sélectionné l’icône d’abonnement de modification à l’étape précédente, vous devez également sélectionner l’abonnement de destination.

![sélectionner une destination](./media/resource-group-move-resources/select-destination.png)

Dans **les Notifications**, vous constatez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsqu’elle est terminée, vous êtes averti du résultat.

![afficher les résultats de déplacement](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Utiliser PowerShell

Pour déplacer des ressources existantes d’un autre groupe de ressources ou d’abonnement, utilisez la commande **Déplacer AzureRmResource** .

Le premier exemple montre comment déplacer une ressource à un nouveau groupe de ressources.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Le second exemple montre comment déplacer plusieurs ressources à un nouveau groupe de ressources.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Pour déplacer vers un nouvel abonnement, inclure une valeur pour le paramètre **DestinationSubscriptionId** .

Vous êtes invité à confirmer que vous voulez déplacer les ressources spécifiées.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Utiliser Azure infrastructure du langage commun

Pour déplacer des ressources existantes d’un autre groupe de ressources ou d’abonnement, utilisez la commande **déplacer ressource azure** . Vous devez fournir les ID de ressources des ressources à déplacer. Vous pouvez obtenir des ID de ressources avec la commande suivante :

    azure resource list -g sourceGroup --json

Qui retourne le format suivant :

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

L’exemple suivant montre comment déplacer un compte de stockage à un nouveau groupe de ressources. Dans le paramètre **-i** , fournir une liste séparée par des virgules de l’id de ressource à déplacer.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Vous êtes invité à confirmer que vous voulez déplacer la ressource spécifiée.

## <a name="use-rest-api"></a>Utiliser des API REST

Pour déplacer des ressources existantes d’un autre groupe de ressources ou d’abonnement, exécutez :

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Dans le corps de la requête, vous spécifiez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération de reste déplacement, voir [déplacer des ressources](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les applets de commande PowerShell pour gérer votre abonnement, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources](powershell-azure-resource-manager.md).
- Pour en savoir plus sur les commandes Azure infrastructure du langage commun pour la gestion de votre abonnement, reportez-vous [à l’aide de l’infrastructure du langage commun Azure avec le Gestionnaire de ressources](xplat-cli-azure-resource-manager.md).
- Pour en savoir plus sur les fonctionnalités du portail de gestion de votre abonnement, voir [l’aide du portail Azure pour gérer les ressources](./azure-portal/resource-group-portal.md).
- Pour en savoir plus sur l’application d’une organisation logique à vos ressources, voir [en utilisant des balises pour organiser vos ressources](resource-group-using-tags.md).
