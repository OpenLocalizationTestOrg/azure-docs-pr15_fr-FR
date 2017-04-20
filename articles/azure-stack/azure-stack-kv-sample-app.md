<properties
    pageTitle="Autoriser les applications à secrets de l’archivage sécurisé Azure pile clé revtrieve | Microsoft Azure"
    description="Utiliser une application exemple avec l’archivage sécurisé Azure pile clé"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="run-the-sample-application-for-key-vault"></a>Exécutez l’application d’exemple pour l’archivage sécurisé clé 

Dans ce guide, vous allez utiliser un exemple d’application pour récupérer secrets et les mots de passe de clé de l’archivage sécurisé.

## <a name="download-the-samples-and-prepare"></a>Téléchargez les exemples et vous préparer

Téléchargez les exemples de client de l’archivage sécurisé de clé Azure à partir de la [page des exemples de l’archivage sécurisé de clé Azure client](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Extraire le contenu du fichier zip sur votre ordinateur local.

Lire le fichier **README.md** (il s’agit d’un fichier texte) et suivez les instructions.

## <a name="run-sample-1--hellokeyvault"></a>Exécuter exemple #1 : HelloKeyVault
HelloKeyVault est une application console qui décrit les scénarios clés sont pris en charge par l’archivage sécurisé clé :

  1. Créer/importation une touche (HSM ou logiciel)
  2. Chiffrer un secret à l’aide d’une clé de contenu
  3. Renvoyer à la ligne de la clé de contenu à l’aide d’une clé de l’archivage sécurisé clé
  4. Renvoi de la clé de contenu
  5. Déchiffrer le code secret
  6. Définir un secret

Cette application console doit s’exécuter avec aucune modification, sauf que les paramètres de configuration appropriées dans App.Config seront mise à jour selon les étapes suivantes :

1. Mettre à jour les paramètres de configuration d’application dans HelloKeyVault\App.config URL de l’archivage sécurisé, ID principal de l’application et code secret. Les informations peuvent également être générées à l’aide de **scripts\GetAppConfigSettings.ps1**.
2. Mettre à jour les valeurs des variables obligatoires dans GetAppConfigSettings.ps1.
3. Lancer la fenêtre de Windows PowerShell.
4. Exécuter le script GetAppConfigSettings.ps1 dans la fenêtre PowerShell.
5. Copier les résultats du script vers le fichier HelloKeyVault\App.config.


## <a name="next-steps"></a>Étapes suivantes

[Déployer une machine virtuelle avec un mot de passe de l’archivage sécurisé clé](azure-stack-kv-deploy-vm-with-secret.md)

[Déployer une machine virtuelle avec un certificat de l’archivage sécurisé clé](azure-stack-kv-push-secret-into-vm.md)