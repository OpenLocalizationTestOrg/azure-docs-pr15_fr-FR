<properties
    pageTitle="Se connecter à Azure pile avec infrastructure du langage commun | Microsoft Azure"
    description="Découvrez comment utiliser l’interface de ligne de disponibilité sur plusieurs plateformes (commande) pour gérer et déployer des ressources sur Azure pile"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Installer et configurer l’infrastructure du langage commun pile Azure

Dans ce document, nous vous guide dans le processus d’utilisation Azure Interface de ligne de commande (CLI) pour gérer les ressources de pile Azure sur Linux et Mac client.  

## <a name="install-azure-stack-cli"></a>Installer pile Azure infrastructure du langage commun

Si vous êtes sur Mac ou Linux, vous pouvez obtenir l’infrastructure du langage commun à l’aide de la commande suivante :
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Se connecter à pile Azure
Dans la procédure suivante, vous configurez Azure infrastructure du langage commun pour vous connecter à pile Azure. Puis vous connectez et récupérer les informations sur l’abonnement.

1.  Récupérer la valeur active directory ressource ID par l’exécution de cette PowerShell :
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  La commande CLI suivante permet d’ajouter l’environnement pile Azure, en prenant soin de mettre à jour *--répertoire actif-Nº ressource* avec les données de l’URL est extraite de l’étape précédente :

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Se connecter à l’aide de la commande suivante (remplacer *nom d’utilisateur* avec votre nom d’utilisateur) :

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Si vous rencontrez des problèmes de validation de certificat, désactivez la validation de certificat en exécutant la commande `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Définir le mode de configuration Azure au Gestionnaire de ressources Azure à l’aide de la commande suivante :

        azure config mode arm

5.  Récupérer une liste des abonnements.

        azure account list     

## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec Azure infrastructure du langage commun](azure-stack-deploy-template-command-line.md)

[Se connecter avec PowerShell](azure-stack-connect-powershell.md)

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
