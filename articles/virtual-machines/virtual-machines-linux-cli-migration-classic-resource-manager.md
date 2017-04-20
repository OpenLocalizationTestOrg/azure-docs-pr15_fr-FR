<properties
    pageTitle="Migrez ressources IaaS classique au Gestionnaire de ressources Azure à l’aide de Azure infrastructure du langage commun | Microsoft Azure"
    description="Cet article décrit la migration plateforme prise en charge des ressources de classique au Gestionnaire de ressources Azure à l’aide de Azure infrastructure du langage commun"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrez ressources IaaS classique au Gestionnaire de ressources Azure à l’aide de Azure infrastructure du langage commun

Ces étapes montrent comment utiliser les commandes de l’interface de ligne Azure (commande) pour migrer infrastructure comme une ressource de service (IaaS) à partir du modèle de déploiement classique dans le modèle de déploiement d’Azure le Gestionnaire de ressources. L’article nécessite l' [Infrastructure du langage commun Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotents. Si vous rencontrez un problème différent une fonctionnalité non prise en charge ou une erreur de configuration, nous vous recommandons de relancer la préparer, l’annulation ou validation opération. La plateforme essaiera puis à nouveau l’action.

## <a name="step-1-prepare-for-migration"></a>Étape 1 : Préparer la migration

Voici quelques recommandations que nous vous recommandons lorsque vous évaluez migration IaaS des ressources à partir de classique au Gestionnaire de ressources :

- Parcourez la [liste des fonctionnalités ou des configurations non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez machines virtuelles qui utilisent des configurations non prises en charge ou fonctionnalités, nous vous recommandons d’attendre la prise en charge de la configuration des fonctionnalités à annoncé. Par ailleurs, vous pouvez supprimer cette fonctionnalité ou retirer cette configuration permettant la migration si elle ne répond à vos besoins.
-   Si vous avez automatisée scripts que déploient votre infrastructure et vos applications aujourd'hui, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Par ailleurs, vous pouvez configurer les environnements exemple à l’aide du portail Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Étape 2 : Configurer votre abonnement et enregistrer le fournisseur

Scénarios de migration, vous devez configurer votre environnement pour les deux classique et Gestionnaire de ressources. [Installer Azure infrastructure du langage commun](../xplat-cli-install.md) , puis [Sélectionnez votre abonnement](../xplat-cli-connect.md).

Connectez-vous à votre compte.
    
    azure login

Sélectionnez l’abonnement Azure à l’aide de la commande suivante.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] L’enregistrement est une étape de temps, mais il doit être effectuée une seule fois avant d’essayer de migration. Sans l’enregistrer, vous verrez le message d’erreur suivant 

>   *BadRequest : Abonnement n’est pas enregistré pour la migration.* 

Inscrire avec le fournisseur de ressources de migration à l’aide de la commande suivante. Notez que dans certains cas, cette commande arrive à expiration. Toutefois, l’inscription sera réussie.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Patientez cinq minutes pour l’enregistrement terminer. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante. Vérifiez que RegistrationState est `Registered` avant de poursuivre.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Passez maintenant infrastructure du langage commun à la `asm` mode.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Étape 3 : Vérifiez que vous disposez de suffisamment cœurs Machine virtuelle de gestionnaire de ressources Azure dans la zone Azure de votre déploiement actuel ou VNET

Pour cette étape, vous aurez besoin passer à `arm` mode. Cela avec la commande suivante.

```
azure config mode arm
```

Vous pouvez utiliser la commande suivante infrastructure du langage commun pour vérifier la quantité de cœurs que dans le Gestionnaire de ressources Azure. Pour en savoir plus sur les quotas de base, voir [limites et le Gestionnaire de ressources Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Une fois que vous avez terminé Vérifiez cette étape, vous pouvez basculer vers `asm` mode.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Étape 4 : Option 1 - migrer les machines virtuelles dans un service cloud 

Obtenir la liste des services cloud à l’aide de la commande suivante, puis puis sélectionnez le service en nuage que vous voulez migrer. Notez que si les ordinateurs virtuels dans le service cloud sont dans un réseau virtuel ou s’ils ont des rôles web/collaborateur, vous obtenez un message d’erreur.

    azure service list

Exécutez la commande suivante pour obtenir le nom de déploiement pour le service de nuage à partir de la sortie des commentaires. Dans la plupart des cas, le nom du déploiement est le même que le nom du service cloud.

    azure service show <serviceName> -vv

Préparer les machines virtuelles dans le service cloud pour la migration. Vous avez deux possibilités pour choisir parmi.

Si vous voulez migrer les ordinateurs virtuels à un réseau virtuel créé plateforme, utilisez la commande suivante.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Si vous voulez migrer vers un réseau virtuel existant dans le modèle de déploiement du Gestionnaire de ressources, utilisez la commande suivante.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Une fois l’opération de préparation réussie, vous pouvez examiner la sortie détaillée pour obtenir l’état de migration des ordinateurs virtuels et vous assurer qu’ils figurent dans la `Prepared` état.

    azure vm show <vmName> -vv

Vérifiez la configuration pour les ressources préparés à l’aide de l’infrastructure du langage commun ou le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande suivante.

    azure service deployment abort-migration <serviceName> <deploymentName>

Si la configuration préparée de test vous convient, vous pouvez déplacer vers l’avant et valider les ressources à l’aide de la commande suivante.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Étape 4 : Option 2 - migrer les machines virtuelles dans un réseau virtuel

Choisissez le réseau virtuel que vous voulez migrer. Notez que si le réseau virtuel contient des rôles web/collaborateur ou machines virtuelles avec des configurations non prises en charge, vous recevrez un message d’erreur de validation.

Obtenez tous les réseaux virtuels dans l’abonnement à l’aide de la commande suivante.

    azure network vnet list
    
La sortie doit ressembler à ceci :

![Capture d’écran de la ligne de commande avec le nom de réseau virtuel entier en surbrillance.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Dans l’exemple ci-dessus, la **virtualNetworkName** est le nom complet **« Groupe classicubuntu16 classicubuntu16 »**.

Préparer le réseau virtuel de votre choix pour la migration à l’aide de la commande suivante.

    azure network vnet prepare-migration <virtualNetworkName>

Vérifiez la configuration pour les ordinateurs virtuels préparés à l’aide de l’infrastructure du langage commun ou le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande suivante.

    azure network vnet abort-migration <virtualNetworkName>

Si la configuration préparée de test vous convient, vous pouvez déplacer vers l’avant et valider les ressources à l’aide de la commande suivante.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Étape 5 : Migrer un compte de stockage

Une fois que vous avez terminé les machines virtuelles la migration, nous vous recommandons de vous migrez du compte de stockage.

Préparer le compte de stockage pour la migration à l’aide de la commande suivante

    azure storage account prepare-migration <storageAccountName>

Vérifiez la configuration du compte de stockage préparé à l’aide de l’infrastructure du langage commun ou le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande suivante.

    azure storage account abort-migration <storageAccountName>

Si la configuration préparée de test vous convient, vous pouvez déplacer vers l’avant et valider les ressources à l’aide de la commande suivante.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Étapes suivantes

- [Migration de plateforme prise en charge des ressources IaaS de classique au Gestionnaire de ressources](virtual-machines-windows-migration-classic-resource-manager.md)
- [Technique approfondie sur la plateforme prise en charge la migration de classique au Gestionnaire de ressources](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
