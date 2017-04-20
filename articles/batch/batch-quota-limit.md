<properties
    pageTitle="Les quotas de service et des limites de commandes | Microsoft Azure"
    description="En savoir plus sur les contraintes, les limites et les quotas Azure lot par défaut, et comment demander quota augmentent"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Les quotas et les limites pour le service lot Azure

Comme avec d’autres services Azure sont les limites de certaines ressources associés au service lot. La plupart de ces limites sont appliqués par Azure au niveau de l’abonnement ou compte les quotas par défaut. Cet article décrit ces paramètres par défaut, et comment vous pouvez demander le quota augmente.

Si vous envisagez d’exécuter des charges de travail dans le lot, vous devrez peut-être augmenter un ou plusieurs des quotas au-dessus de la valeur par défaut. Si vous voulez déclencher un quota, vous pouvez ouvrir un en ligne [demande de support client](#increase-a-quota) sans frais.

>[AZURE.NOTE] Un quota est une limite de crédit, pas une garantie capacité. Si vous avez besoin de grande capacité, contactez le support Azure.

## <a name="subscription-quotas"></a>Quotas d’abonnement
**Ressource**|**Limite par défaut**|**Limite maximale**
---|---|---
Comptes lot par région par abonnement | 1 | 50

## <a name="batch-account-quotas"></a>Compte les quotas de traitement par lots
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Autres limites
**Ressource**|**Limite maximale**
---|---
[Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul | nombre x 4 de cœurs nœud
[Applications](batch-application-packages.md) par compte lot        | 20
Packages d’application par application  | 40
Taille de package d’application (chacun)       | Environ 195 Go<sup>1</sup>

Limite de stockage azure <sup>1</sup> pour taille de blob bloc maximale

## <a name="view-batch-quotas"></a>Afficher les quotas par lots

Afficher votre compte les quotas de traitement par lots dans le [portail Azure][portal].

1. Sélectionnez **comptes lot** dans le portail, puis sélectionnez le compte lot que qui vous intéresse.

2. Cliquez sur **Propriétés** dans la carte de menu du compte par lots

3. La carte de propriétés affiche les **quotas** actuellement appliqué au compte lot

    ![Compte les quotas de traitement par lots][account_quotas]

## <a name="increase-a-quota"></a>Augmenter un quota

Suivez les étapes ci-dessous pour demander un quota augmentent à l’aide du [portail Azure][portal].

1. Sélectionnez la vignette **aide + prise en charge** dans votre tableau de bord du portail ou le point d’interrogation (**?**) dans le coin supérieur droit du portail.

2. Sélectionnez **nouveau demande d’assistance** > **Concepts de base**.

3. Sur la carte **Concepts de base** :

    un. **Type de problème** > **Quota**

    b. Sélectionnez votre abonnement.

    c. **Type de quota** > **lot**

    d. **Prendre en charge plan** > **prise en charge de Quota - inclus**

    Cliquez sur **suivant**.

4. Sur la carte de **problème** :

    un. Sélectionnez une **gravité** en fonction de votre [impact sur l’activité][support_sev].

    b. Dans **Détails**, spécifiez chaque quota que vous souhaitez modifier le nom de compte et la nouvelle limite.

    Cliquez sur **suivant**.

5. Sur la carte **les informations de Contact** :

    un. Sélectionnez un **mode de communication préféré**.

    b. Vérifier et entrez les informations de contact requises.

    Cliquez sur **créer** pour envoyer la demande de support.

Une fois que vous avez envoyé votre demande de support, Azure support technique vous contactera. Notez que la fin de la requête peut prendre jusqu'à 2 jours ouvrés.

## <a name="related-topics"></a>Rubriques connexes

* [Créer un compte Azure lot à l’aide du portail Azure](batch-account-create-portal.md)

* [Présentation des fonctionnalités de lot Azure](batch-api-basics.md)

* [Abonnement Azure et limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
