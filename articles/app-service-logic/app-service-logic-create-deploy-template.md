<properties
   pageTitle="Créer un modèle de déploiement de l’application logique | Microsoft Azure"
   description="Découvrez comment créer un modèle de déploiement de l’application logique et utilisez-le pour la gestion des versions"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Créer un modèle de déploiement de l’application logique

Une fois une application logique a été créée, vous souhaiterez peut-être créer en tant que gestionnaire de ressources Azure modèle. De cette façon, vous pouvez déployer facilement l’application logique à n’importe quel environnement ou un groupe de ressources où vous en aurez besoin. Pour une introduction aux modèles Gestionnaire de ressources, veillez à voir les articles sur [propos Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md) et [déployer des ressources à l’aide de modèles Azure le Gestionnaire de ressources](../resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modèle de déploiement d’application logique

Une application logique comporte trois composants de base :

* **Ressources de l’application logique**. Cette ressource contient des informations sur les éléments tels que les tarifs plan, l’emplacement et la définition de flux de travail.
* **Définition de flux de travail**. Voici ce qui s’affiche dans le mode code. Il inclut la définition des étapes de flux et comment le moteur doit exécuter. Il s’agit de la `definition` propriété de la ressource Application logique.
* **Connexions**. Il s’agit des ressources distinctes qui stockent les métadonnées relatives à toutes les connexions connecteur, par exemple une chaîne de connexion et un jeton d’accès en toute sécurité. Vous faites référence à ces éléments dans une application logique dans le `parameters` section de la ressource Application logique.

Vous pouvez afficher ces éléments pour les applications logique existant à l’aide d’un outil comme dans [l’Explorateur de ressources Azure](http://resources.azure.com).

Pour créer un modèle pour une application logique à utiliser avec les déploiements de groupe de ressources, vous devez définir les ressources et paramétrer selon vos besoins. Par exemple, si vous déployez pour un environnement de production, de test et de développement, vous souhaiterez probablement utiliser différentes chaînes de connexion à une base de données SQL dans chaque environnement. Ou, vous souhaiterez peut-être déployer dans différents abonnements ou groupes de ressources.  

## <a name="create-a-logic-app-deployment-template"></a>Créer un modèle de déploiement de l’application logique

Pour avoir un modèle de déploiement d’application logique valide, le plus simple consiste à utiliser les [Outils Visual Studio pour les applications logique](./app-service-logic-deploy-from-vs.md).  Les outils Visual Studio génèrent un modèle de déploiement valide qui peut être utilisé sur n’importe quel abonnement ou emplacement.

Quelques autres outils peuvent vous aider à mesure que vous créez un modèle de déploiement d’application logique. Vous pouvez créer de manuellement, c'est-à-dire en utilisant les ressources déjà mentionnés ici pour créer des paramètres selon vos besoins. Une autre option consiste à utiliser un module PowerShell [créateur de modèle d’application logique](https://github.com/jeffhollan/LogicAppTemplateCreator) . Ce module open source évalue d’abord l’application logique et toutes les connexions qu’elle utilise et puis génère des ressources de modèle avec les paramètres nécessaires pour le déploiement. Par exemple, si vous avez une application logique qui permet de recevoir un message à partir d’une file d’attente Bus des services Azure et ajoute des données à une base de données SQL Azure, l’outil préserver l’intégralité de la logique d’orchestration et paramétrer les chaînes de connexion SQL et Bus des services afin qu’ils peuvent être définies au déploiement.

>[AZURE.NOTE] Connexions doivent figurer dans le même groupe de ressources que l’application logique.

### <a name="install-the-logic-app-template-powershell-module"></a>Installez le module logique application modèle PowerShell

Le moyen le plus simple pour installer le module est via la [Galerie de PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), à l’aide de la commande `Install-Module -Name LogicAppTemplate`.  

Vous pouvez également installer le module PowerShell manuellement :

1. Téléchargez la dernière version du [créateur de modèle d’application logique](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extraire le dossier dans votre dossier de module PowerShell (généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Pour le module fonctionner avec tout accès client et d’abonnement jeton, nous vous conseillons d’utiliser celle-ci avec l’outil de ligne de commande [ARMClient](https://github.com/projectkudu/ARMClient) .  Ce [billet de blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) traite ARMClient plus en détail.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Générer un modèle d’application logique à l’aide de PowerShell

Une fois que PowerShell est installé, vous pouvez générer un modèle à l’aide de la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`l’ID de l’abonnement Azure. Cette ligne obtient d’abord un accès jeton via ARMClient, puis canaux à l’aide pour le script PowerShell, puis crée le modèle dans un fichier JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Ajouter des paramètres à un modèle d’application logique

Après avoir créé votre modèle d’application logique, vous pouvez continuer à ajouter ou modifier les paramètres que vous devrez peut-être. Par exemple, si la définition de votre inclut un ID de ressource à une fonction Azure ou imbriquée du flux de travail que vous prévoyez de déployer dans un seul déploiement, vous pouvez ajouter des ressources à votre modèle et paramétrer ID selon vos besoins. La même s’applique à toutes les références à personnalisé API ou Swagger points de terminaison que vous pensez déployer avec chaque groupe de ressources.

## <a name="deploy-a-logic-app-template"></a>Déployer un modèle d’application logique

Vous pouvez déployer votre modèle à l’aide de n’importe quel nombre d’outils, dont PowerShell, API REST, gestion des versions Visual Studio et le déploiement du modèle portail Azure. Consultez cet article sur le [déploiement des ressources à l’aide de modèles Azure le Gestionnaire de ressources](../resource-group-template-deploy.md) pour plus d’informations. En outre, nous vous recommandons de que vous créez un [fichier de paramètres](../resource-group-template-deploy.md#parameter-file) pour stocker les valeurs pour le paramètre.

### <a name="authorize-oauth-connections"></a>Autoriser les connexions OAuth

Après le déploiement, l’application logique fonctionne de bout en bout avec des paramètres valides. Toutefois, connexions OAuth toujours devez être autorisé à générer un jeton d’accès valide. Vous pouvez le faire en ouvrant l’application logique dans le concepteur et puis autoriser les connexions. Ou, si vous souhaitez automatiser, vous pouvez utiliser un script pour consentement pour chaque connexion OAuth. Il existe un exemple de script sur GitHub sous le projet [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

## <a name="visual-studio-release-management"></a>Gestion des versions Visual Studio

Un scénario courant pour déployer et gérer un environnement consiste à utiliser un outil de gestion des versions Visual Studio, avec un modèle de déploiement d’application logique. Visual Studio Team Services comprend une tâche de [Déploiement d’un groupe de ressources Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que vous pouvez ajouter à n’importe quelle build ou libérer pipeline. Vous devez disposer d’un [service principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) d’autorisation à déployer et vous pouvez ensuite générer la définition de version.

1. Dans la gestion des versions, pour créer une nouvelle définition, sélectionnez **vide** pour démarrer avec une définition vide.

    ![Créer une définition vide][1]   

1. Sélectionnez les ressources que vous avez besoin pour cela. Il s’agit probablement pas le modèle d’application logique généré manuellement ou dans le cadre du processus de génération.
1. Ajouter une tâche de **Déploiement de groupe de ressources Azure** .
1. Configurer avec un [service principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)et font référence aux fichiers de modèle et les paramètres de modèle.
1. Continuer à générer des étapes de la procédure de publication pour tout autre environnement, test automatisé ou approbateurs selon vos besoins.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
