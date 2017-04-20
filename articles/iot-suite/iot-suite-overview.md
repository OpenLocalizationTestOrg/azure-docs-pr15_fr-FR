<properties
    pageTitle="Vue d’ensemble de Microsoft Azure IoT Suite | Microsoft Azure"
    description="Découvrez comment Azure IoT Suite propose internet des solutions préconfigurés choses pour collecter, analyser et stocker des données, fournissent des visualisations et intégrer avec d’autres systèmes."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>Qu’est Azure IoT Suite ?

Azure internet de services d’éléments (IoT) offrent un large éventail de fonctions. Ces services de qualité entreprise vous permettent de :

- Collecter les données à partir d’appareils
- Analyser les flux dans-déplacement de données
- Stocker et grands ensembles de données de la requête
- Visualiser des données historiques et en temps réel
- Intégrer des systèmes back-office

Pour effectuer ces fonctionnalités, les logiciels de Azure IoT Suite ensemble plusieurs services Azure avec les extensions personnalisées en tant que *solutions préconfigurées*. Ces solutions préconfigurées sont mises en œuvre base de modèles de solution IoT courants qui vous aident à réduire le temps que vous prenez sur votre IoT des solutions. Utilisation de la [kits de développement logiciel IoT][lnk-sdks], vous pouvez personnaliser et étendre ces solutions pour répondre à vos besoins. Vous pouvez également utiliser ces solutions comme des exemples ou modèles lorsque vous développez des solutions IoT nouveau.

La vidéo suivante fournit une introduction à la Suite de IoT Azure :

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT services dans Azure IoT Suite

Les solutions préconfigurées utilisent généralement les services suivants :

- Cœur à Azure IoT Suite est le [Azure IoT concentrateur] [ lnk-iot-hub] service. Ce service fournit les fonctionnalités de messagerie appareil-nuage et cloud vers le périphérique et sert à la passerelle vers le cloud et les autres services IoT Suite clés. Le service vous permet de recevoir des messages à partir de vos périphériques à l’échelle, et envoyer des commandes à vos appareils.

- [Azure flux Analytique] [ lnk-asa] fournit une analyse de données en mouvement. IoT Suite s’appuie sur ce service pour traiter télémétrie entrant, effectuer l’agrégation et détecter les événements. Les solutions préconfigurées utilisent également analytique de flux de données pour traiter les messages d’information qui contiennent des données telles que les réponses de métadonnées ou une commande à partir d’appareils. Les solutions utilisent flux Analytique pour traiter les messages à partir de vos périphériques et remettre les messages vers d’autres services.

- [Stockage Azure] [ lnk-azure-storage] et [Azure DocumentDB] [ lnk-document-db] offrent les fonctionnalités de stockage de données. Les solutions préconfigurées utilisent stockage blob pour stocker télémétrie et à le rendre disponible pour l’analyse. Les solutions utilisent DocumentDB pour stocker les métadonnées de l’appareil et activer les fonctionnalités de gestion des périphériques des solutions.

- [Applications Web Azure] [ lnk-web-apps] et [Microsoft Power BI] [ lnk-power-bi] offrent les fonctionnalités de visualisation des données. La flexibilité de Power BI permet de créer rapidement vos propres tableaux de bord interactifs qui utilisent des données IoT Suite.

Pour une vue d’ensemble de l’architecture d’une solution IoT classique, voir [Microsoft Azure et l’Internet des objets (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Solutions préconfigurées

IoT Suite inclut des solutions préconfigurées qui vous permettent de commencer rapidement à l’utiliser et pour Explorer les scénarios courants IoT, telles que le *contrôle à distance* et *maintenance prédictive*, qui permet d’Azure IoT Suite. Vous pouvez déployer ces solutions à votre abonnement Azure, puis exécutez un scénario IoT complète et de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble d’offertes IoT Suite et quels sont ses principaux éléments, vous pouvez en savoir plus sur les solutions préconfigurées dans IoT Suite, voir [Quels sont les IoT Azure préconfiguré solutions ?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
