
<properties
   pageTitle="Créer une passerelle d’application à l’aide de modèles de gestionnaire de ressources Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour créer une passerelle d’application Azure en utilisant le modèle de gestionnaire de ressources Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Créer une passerelle d’application à l’aide du modèle de gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classique](application-gateway-create-gateway.md)
- [Modèle de gestionnaire de ressources Azure](application-gateway-create-gateway-arm-template.md)
- [Azure infrastructure du langage commun](application-gateway-create-gateway-cli.md)

Azure Application passerelle est un équilibrage de charge de calque à 7. Il propose basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Application passerelle fournit de nombreuses fonctionnalités contrôleur de remise Application (ADC), y compris l’équilibrage de charge HTTP, affinité session basée sur les cookies, calque SSL (Secure Sockets) décharger, état personnalisé sondes, prise en charge de plusieurs sites et bien d’autres. Pour rechercher une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble de la passerelle Application](application-gateway-introduction.md)

Vous découvrez comment télécharger et modifier un modèle de gestionnaire de ressources Azure existant à partir de GitHub et déployez le modèle à partir de l’infrastructure du langage commun Azure GitHub et PowerShell.

Si vous déployez le modèle de gestionnaire de ressources Azure directement à partir de GitHub sans effectuer de modifications simplement, passez pour déployer un modèle à partir de GitHub.

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez :

- Créer une passerelle d’application avec deux instances.
- Créer un réseau virtuel nommé VirtualNetwork1 avec un bloc CIDR réservé de 10.0.0.0/16.
- Créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme son bloc CIDR.
- Configurer deux configuré précédemment principale adresses IP pour les serveurs web que vous voulez équilibrer le trafic. Dans cet exemple de modèle, les adresses IP principale sont 10.0.1.10 et 10.0.1.11.

>[AZURE.NOTE] Ces paramètres sont les paramètres de ce modèle. Pour personnaliser le modèle, vous pouvez modifier les règles et que l’auditeur SSL qui s’ouvre le azuredeploy.json.

![Scénario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Télécharger et comprendre le modèle de gestionnaire de ressources Azure

Vous pouvez télécharger le modèle de gestionnaire de ressources Azure existant pour créer un réseau virtuel et deux sous-réseaux à partir de GitHub, apportez les modifications peuvent souhaité, puis le réutiliser. Pour ce faire, procédez comme suit :

1. Accédez à [créer l’Application passerelle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Cliquez sur **azuredeploy.json**, puis cliquez sur **premières**.
3. Enregistrez le fichier dans un dossier local sur votre ordinateur.
4. Si vous êtes habitué avec les modèles de gestionnaire de ressources Azure, passez à l’étape 7.
5. Ouvrez le fichier que vous avez enregistré et examinez le contenu sous l’onglet **paramètres** dans la ligne 5. Paramètres de modèle de gestionnaire de ressources Azure fournissent un espace réservé pour les valeurs qui peuvent être remplis pendant le déploiement.

  	| Paramètre | Description |
  	|---|---|
  	| **emplacement** | Azure région dans lequel la passerelle d’application est créée |
  	| **VirtualNetwork1** | Nom pour le réseau virtuel |
  	| **addressPrefix** | Espace d’adressage pour le réseau virtuel, au format CIDR |
  	| **ApplicationGatewaysubnet** | Nom du sous-réseau de passerelle d’application |
  	| **subnetPrefix** | Bloc CIDR pour le sous-réseau de passerelle d’application |
  	| **SKUName** | Taille d’instance de référence (SKU) |
  	| **capacité** | Nombre d’instances |
  	| **backendaddress1** | Adresse IP du premier serveur web |
  	| **backendaddress2** | Adresse IP du deuxième serveur web |


    >[AZURE.IMPORTANT] Modèles Gestionnaire de ressources Azure conservées dans GitHub peuvent changer dans le temps. Vérifiez que le modèle avant de l’utiliser.

6. Vérifier le contenu sous **ressources** et observez les points suivants :

    - **type**. Type de ressource créé par le modèle. Dans ce cas, le type est **Microsoft.Network/applicationGateways**, qui représente une passerelle d’application.
    - **nom**. Nom de la ressource. Notez l’utilisation de **[parameters('applicationGatewayName')]**, ce qui signifie que le nom est fourni sous forme d’entrée par vous-même ou d’un fichier de paramètres pendant le déploiement.
    - **Propriétés**. Liste des propriétés de la ressource. Ce modèle utilise le réseau virtuel et l’adresse IP publique lors de la création de passerelle d’application.

7. Accédez à [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Cliquez sur **azuredeploy paremeters.json**, puis cliquez sur **premières**.
9. Enregistrez le fichier dans un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous avez enregistré et modifiez les valeurs pour les paramètres. Utilisez les valeurs suivantes pour déployer la passerelle d’application décrite dans notre scénario.

        {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }

11. Enregistrez le fichier. Vous pouvez tester les JSON et paramètre à l’aide des outils de validation JSON en ligne comme [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Déployez le modèle de gestionnaire de ressources Azure à l’aide de PowerShell

Si vous n’avez jamais utilisé Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) et suivez les instructions pour vous connecter à Azure et sélectionnez votre abonnement.

### <a name="step-1"></a>Étape 1

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription

Vous êtes invité à vous authentifier avec vos informations d’identification.

### <a name="step-3"></a>Étape 3

Sélectionner les options de vos abonnements Azure à utiliser.


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Étape 4


Si nécessaire, créez un groupe de ressources à l’aide de l’applet de commande **New-AzureResourceGroup** . Dans l’exemple suivant, vous créez un groupe de ressources appelé AppgatewayRG emplacement américain Extrême-Orient.

    New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"

Exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour déployer le réseau virtuel à l’aide du modèle précédent et les fichiers de paramètres que vous avez téléchargé et modifié.

    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
        -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Déployez le modèle de gestionnaire de ressources Azure à l’aide de l’infrastructure du langage commun Azure

Pour déployer le modèle Azure le Gestionnaire de ressources que vous avez téléchargée à l’aide de Azure infrastructure du langage commun, procédez comme suit :

### <a name="step-1"></a>Étape 1

Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

### <a name="step-2"></a>Étape 2

Exécutez la commande **mode config azure** pour passer en mode directeur des ressources, comme illustré ci-dessous.

    azure config mode arm

Voici le résultat attendu de la commande ci-dessus :

    info:   New mode is arm

### <a name="step-3"></a>Étape 3

Si nécessaire, exécutez la commande **groupe azure créer** pour créer un nouveau groupe de ressources, comme illustré ci-dessous. Notez la sortie de la commande. La liste visible après la sortie explique les paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez [Présentation du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

    azure group create -n appgatewayRG -l eastus

**-n (ou--nom)**. Nom du nouveau groupe de ressources. Pour notre scénario, il est *appgatewayRG*.

**-l (ou--emplacement)**. Région Azure où le nouveau groupe de ressources est créé. Pour notre scénario, il est *eastus*.

### <a name="step-4"></a>Étape 4

Exécutez l’applet de commande **créer de déploiement d’un groupe azure** pour déployer le réseau virtuel en utilisant les fichiers de modèle et de paramètres vous avez téléchargé et de modifier les propriétés. La liste visible après la sortie explique les paramètres utilisés.

    azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Déployez le modèle de gestionnaire de ressources Azure à l’aide de cliquer sur le déploiement

Cliquez sur à déployer est un autre moyen d’utiliser le Gestionnaire de ressources Azure modèles. Il est un moyen facile à utiliser des modèles avec le portail Azure.

### <a name="step-1"></a>Étape 1

Accédez à [créer une passerelle d’application avec adresse IP publique](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).

### <a name="step-2"></a>Étape 2

Cliquez sur **déployer sur Azure**.

![Déploiement d’Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>Étape 3

Renseignez les paramètres pour le modèle de déploiement sur le portail et cliquez sur **OK**.

![Paramètres](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>Étape 4

Sélectionnez les **conditions juridiques** et cliquez sur **acheter**.

### <a name="step-5"></a>Étape 5

Dans la carte de déploiement personnalisé, cliquez sur **créer**.

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez configurer déchargement SSL, voir [configurer une passerelle d’application pour SSL décharger](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un programme d’équilibrage de charge interne, voir [créer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur Charger les options d’équilibrage de charge en général, visitez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
