<properties
    pageTitle="WebJobs dans le Service d’application Azure"
    description="Apprenez à créer WebJobs pour exécuter les tests d’arrière-plan, interagir avec les services, tels que le stockage et Bus des services et créer des tâches planifiées."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>À l’aide de WebJobs dans le Service d’application Azure

Cet article liens vers des ressources de documentation sur l’utilisation Azure WebJobs et le Kit de développement WebJobs Azure. WebJobs Azure offrent un moyen facile pour exécuter des scripts ou des programmes en arrière-plan sur [l’Application de Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Vous pouvez télécharger et exécuter un fichier exécutable comme comme cmd, bat, exe (.NET), ps1, feuille, php, copier, js et jar. Ces programmes s’exécuter en tant que WebJobs sur un planning (cron) ou en permanence.

Le SDK WebJobs rend plus facile à utiliser le stockage Azure. Le SDK WebJobs a un système de déclencheur qui fonctionne avec Microsoft Azure stockage BLOB, files d’attente et Tables ainsi que files d’attente de Bus de Service et de liaison.

Création, le déploiement et gestion des WebJobs sont transparente avec des outils intégrés dans Visual Studio. Vous pouvez créer WebJobs à partir de modèles, publier et gérer (exécution/arrêter/moniteur/débogage) les.

Le tableau de bord WebJobs dans le portail Azure fournit les fonctionnalités de gestion puissantes qui vous donnent un contrôle total sur l’exécution de WebJobs, y compris la possibilité d’appeler des fonctions individuelles dans WebJobs. Le tableau de bord affiche également la sortie de journalisation et de fonction exécutions.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
