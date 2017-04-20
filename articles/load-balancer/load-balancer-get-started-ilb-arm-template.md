<properties
   pageTitle="Créer un équilibrage de charge interne à l’aide d’un modèle dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à créer un équilibrage de charge interne à l’aide d’un modèle dans le Gestionnaire de ressources"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-a-template"></a>Créer un équilibrage de charge interne à l’aide d’un modèle

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployez le modèle en déployant à l’aide de clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle à l’aide de cliquez sur le déploiement, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), cliquez sur **déployer vers Azure**, remplacer les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## <a name="deploy-the-template-by-using-powershell"></a>Déployez le modèle à l’aide de PowerShell

Pour déployer le modèle que vous avez téléchargée à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../../articles/powershell-install-configure.md) et suivez les instructions tout à fait à la fin pour vous connecter à Azure et sélectionnez votre abonnement.
2. Téléchargez le fichier de paramètres sur votre disque local.
3. Modifier le fichier et enregistrez-le.
4. Exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour créer un groupe de ressources à l’aide du modèle.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployez le modèle à l’aide de l’infrastructure du langage commun Azure

Pour déployer le modèle à l’aide de l’infrastructure du langage commun Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécutez la commande **mode config azure** pour passer en mode directeur des ressources, comme illustré ci-dessous.

        azure config mode arm

    Voici le résultat attendu de la commande ci-dessus :

        info:    New mode is arm

3. Ouvrir le fichier de paramètres, sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Dans cet exemple, nous avons enregistré le fichier de paramètres à *parameters.json*.

4. Exécutez la commande **créer de déploiement d’un groupe azure** pour déployer l’équilibrage de charge interne nouveau en utilisant les fichiers de modèle et de paramètre vous avez téléchargé et de modifier les propriétés. La liste visible après la sortie explique les paramètres utilisés.

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge à l’aide de l’affinité IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)



