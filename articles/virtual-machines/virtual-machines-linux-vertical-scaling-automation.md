<properties
    pageTitle="Évolution verticale Azure machine virtuelle avec Azure automatisation | Microsoft Azure"
    description="Comment mettre à l’échelle verticalement une Machine virtuelle Linux en réponse aux alertes avec Azure Automation de surveillance"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machine-with-azure-automation"></a>Évolution verticale Azure machine virtuelle avec Azure Automation

Échelle verticale consiste à augmenter ou diminuer les ressources d’un ordinateur en réponse à la charge de travail. Dans Azure cela est possible en modifiant la taille de la Machine virtuelle. Cela peut aider dans les scénarios suivants :

- Si la Machine virtuelle n’est pas utilisé fréquemment, vous pouvez la redimensionner à une taille inférieure à réduire les coûts mensuels
- Si la Machine virtuelle voit une charge pointe, elle peut être redimensionnée à une taille supérieure à augmenter ses capacités

Le plan pour les étapes de cette phase est comme ci-dessous

1. Le programme d’installation Azure Automation pour accéder à vos Machines virtuelles
2. Importer les procédures opérationnelles Azure Automation échelle verticale dans votre abonnement
3. Ajouter un webhook à votre runbook
4. Ajouter une alerte à votre Machine virtuelle

> [AZURE.NOTE] En raison de la taille de la première Machine virtuelle, les tailles à l’échelle, peut être limité en raison de la disponibilité des autres formats dans le cluster dans que machine virtuelle actuelle est déployé. Dans les procédures opérationnelles automation publiés utilisées dans cet article nous prendre en charge ce cas et évoluer uniquement au sein du dessous paires de taille de la mémoire virtuelle. Cela signifie qu’une Machine virtuelle Standard_D1v2 ne seront pas soudainement agrandies à Standard_G5 ou mise à l’échelle vers le bas pour Basic_A0.

>| Machine virtuelle tailles paire mise à l’échelle |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Le programme d’installation Azure Automation pour accéder à vos Machines virtuelles

La première chose à faire est de créer un compte Azure Automation hébergeant les procédures opérationnelles utilisée pour redéfinir les instances machine virtuelle échelle définie. Récemment le service Automation ainsi que la fonctionnalité « Exécuter en tant que compte » qui facilite la configuration de l’entité de Service permettant d’exécuter les procédures opérationnelles part de l’utilisateur très simple. Vous pouvez en savoir plus sur ce dans l’article ci-dessous :

* [Authentifier procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importer les procédures opérationnelles Azure Automation échelle verticale dans votre abonnement

Les procédures opérationnelles nécessaires à l’échelle verticalement votre Machine virtuelle sont déjà publiées dans la galerie Azure Automation Runbook. Vous devrez les importer dans votre abonnement. Découvrez comment importer des procédures opérationnelles en lisant l’article suivant.

* [Galeries Runbook et module Azure Automation](../automation/automation-runbook-gallery.md)

Les procédures opérationnelles qui doivent être importées sont affichées dans l’image ci-dessous

![Importer des procédures opérationnelles](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Ajouter un webhook à votre runbook

Une fois que vous avez importé les procédures opérationnelles que vous devez ajouter un webhook à la runbook afin qu’il peut être déclenchée par une alerte à partir d’une Machine virtuelle. Détails de la création d’un webhook pour votre Runbook peuvent être lus ici

* [Azure Automation webhooks](../automation/automation-webhooks.md)

Vérifiez que vous copiez la webhook avant de fermer la boîte de dialogue webhook comme vous en avez besoin dans la section suivante.

## <a name="add-an-alert-to-your-virtual-machine"></a>Ajouter une alerte à votre Machine virtuelle

1. Sélectionnez paramètres de Machine virtuelle
2. Sélectionnez « Règles d’alerte »
3. Sélectionnez « Ajouter alerte »
4. Sélectionner une mesure déclencher l’alerte sur
5. Sélectionnez une condition, où remplies va entraîner déclencher l’alerte
6. Sélectionnez un seuil pour la condition à l’étape 5. à remplir
7. Sélectionnez une période pendant laquelle le service de surveillance doit vérifier pour la condition et le seuil dans les étapes 5 et 6
8. Collez le webhook que vous avez copiée à partir de la section précédente.

![Ajout d’alerte à Machine virtuelle 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Ajout d’alerte pour Machine virtuelle 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)