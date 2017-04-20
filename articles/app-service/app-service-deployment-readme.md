<properties
    pageTitle="Déploiement des Applications sur Azure Application Service"
    description="Découvrez comment déployer des applications à travail de Service d’application"
    keywords="service d’application de service, azure application, déploiement, de déploiement"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="dariagrigoriu"/>

# <a name="azure-app-service-deployment-overview"></a>Vue d’ensemble du déploiement d’Azure Application Service

Service application Azure fournit une fonctionnalité riche et intégrée définie pour prendre en charge la création de flux de travail de déploiement puissant et flexible. Le déploiement des applications peut exploiter les options qui incluent l’intégration continue ou un contrôle de source locale de publication, WebDeploy et FTP. La méthode recommandée pour le déploiement des applications production est échange d’emplacement de déploiement. Emplacements de déploiement représentent les environnements de test et l’intégration associés aux applications de production. Emplacements de déploiement peuvent être configurés et destinée avec le trafic web de validation et le trafic peut être transférée à la demande pour le déploiement en production sans heure vers le bas et automatisée mise en route. Les étapes d’un flux de travail de déploiement peuvent être facilement automatisées via versions des produits tels que Visual Studio relâchez Management gestion. Ceci est utile pour association avec d’autres ressources de la solution (par exemple, stockent les données,) périodicité et la réplication sur plusieurs unités de déploiement. 

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]
