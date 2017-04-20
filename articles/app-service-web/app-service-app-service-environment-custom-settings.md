<properties
    pageTitle="Paramètres personnalisés pour les environnements de Service d’application"
    description="Paramètres de configuration personnalisés pour les environnements de Service d’application"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Paramètres de configuration personnalisés pour les environnements de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Environnements de Service d’application étant isolées à un seul client, il existe certains paramètres de configuration qui peuvent être appliquées en mode exclusif pour les environnements de Service d’application. Cet article explique les différentes personnalisations spécifiques qui sont disponibles pour les environnements de Service d’application.

Si vous ne disposez pas d’un environnement de Service d’application, voir [comment créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md).

Vous pouvez stocker les personnalisations de l’environnement de Service d’application à l’aide d’un tableau dans le nouvel attribut **clusterSettings** . Cet attribut se trouve dans le dictionnaire « Propriétés » de la *hostingEnvironments* entité Azure le Gestionnaire de ressources.

L’extrait de modèle Gestionnaire de ressources abrégé suivant illustre l’attribut **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

L’attribut **clusterSettings** peut être incluse dans un modèle de gestionnaire de ressources pour mettre à jour de l’environnement de Service d’application.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilisez l’Explorateur de ressources Azure pour mettre à jour un environnement de Service d’application
Par ailleurs, vous pouvez mettre à jour l’environnement de Service d’application à l’aide de [L’Explorateur de ressources Azure](https://resources.azure.com).  

1. Dans l’Explorateur de ressources, accédez au nœud pour l’environnement de Service d’application (**abonnements** > **resourceGroups** > **fournisseurs** > **Micrososft.Web** > **hostingEnvironments**). Cliquez ensuite sur l’environnement de Service application spécifique que vous souhaitez mettre à jour.

2. Dans le volet droit, cliquez sur **Lecture/écriture** dans la barre d’outils supérieure pour autoriser interactifs édition dans l’Explorateur de ressources.  

3. Cliquez sur le bleu **Modifier** située à modifier le modèle de gestionnaire de ressources.

4. Faites défiler vers le bas du volet de droite. L’attribut **clusterSettings** est tout en bas, où vous pouvez entrer ou mettre à jour sa valeur.

5. Tapez (ou copiez et collez) du tableau de valeurs de configuration que vous voulez dans l’attribut **clusterSettings** .  

6. Cliquez sur le vert **placer** le bouton qui a située en haut du volet de droite pour appliquer la modification à l’environnement de Service d’application.

Alors que vous soumettez la modification, il faut à peu près 30 minutes multipliés par le nombre de frontaux dans l’environnement de Service d’application pour que la modification prenne effet.
Par exemple, si un environnement de Service d’application comporte quatre frontaux, vous devrez patienter environ deux heures pour la mise à jour de configuration terminer. Tandis que la modification de configuration est en cours transférée, aucune autre mise à l’échelle opérations ou configuration modification puisse avoir lieu dans l’environnement de Service d’application.

## <a name="disable-tls-10"></a>Désactiver TLS 1.0 ##
Une question périodique des clients, notamment les clients qui sont les deuxièmes conformité PCI audits, pour désactiver explicitement TLS 1.0 pour leurs applications.

TLS 1.0 peut être désactivé via l’entrée **clusterSettings** suivante :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Modifier l’ordre des suite chiffrement TLS ##
Une autre question des clients est qu’ils peuvent modifier la liste des chiffres négocié par leur serveur et ceci peut être réalisé en modifiant la **clusterSettings** comme illustré ci-dessous. La liste des suites de chiffrement disponibles peut être extraite [cet article MSDN] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Si des valeurs incorrectes sont définies pour la suite de chiffrement SChannel ne comprend pas, toutes les communications TLS à votre serveur peuvent cesser de fonctionner. Dans ce cas, vous devez supprimer l’entrée *FrontEndSSLCipherSuiteOrder* **clusterSettings** et soumettre le modèle mis à jour le Gestionnaire de ressources pour rétablir les paramètres de suite de chiffrement par défaut.  Utilisez cette fonctionnalité avec prudence.

## <a name="get-started"></a>Prise en main
Le site de modèle Gestionnaire de ressources de démarrage rapide Azure inclut un modèle avec la définition de base pour la [Création d’un environnement de Service d’application](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
