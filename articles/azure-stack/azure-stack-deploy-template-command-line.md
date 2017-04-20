<properties
    pageTitle="Déployer des modèles avec la ligne de commande dans une pile Azure | Microsoft Azure"
    description="Découvrez comment utiliser l’interface de ligne de commande de disponibilité sur plusieurs plateformes (infrastructure du langage commun) pour déployer des modèles à partir d’à l’intérieur de la ClientVM ou après l’utilisation du réseau privé virtuel pour vous connecter à pile Azure."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Déployer des modèles dans la pile Azure à l’aide de la ligne de commande

Utilisez la ligne de commande pour déployer des modèles Azure le Gestionnaire de ressources sur le contrôle du concept pile Azure. Modèles de gestionnaire de ressources Azure déploiement et mise en service de toutes les ressources de votre application dans une seule opération coordonnée.

## <a name="download-template"></a>Télécharger le modèle        
Pour tester un déploiement avec l’infrastructure du langage commun, téléchargez les fichiers azuredeploy.json et azuredeploy.parameters.json à partir de la [créer l’exemple de modèle de stockage compte](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Modèle de déploiement
Accédez au dossier où ces fichiers ont été téléchargés et exécutez la commande suivante pour déployer le modèle :

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Cette commande déploie le modèle vers le groupe de ressources **cliRG** dans un emplacement par défaut de validations Azure pile fonctionnelles.

## <a name="validate-template-deployment"></a>Valider le déploiement de modèle
Pour afficher ce compte de stockage et le groupe de ressources, utilisez les commandes suivantes :

    azure group list

    azure storage account list

## <a name="next-steps"></a>Étapes suivantes

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
