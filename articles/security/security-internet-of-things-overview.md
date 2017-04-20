<properties
   pageTitle="Présentation de la sécurité Internet des objets | Microsoft Azure"
   description=" Azure internet de services d’éléments (IoT) offrent un large éventail de fonctions. Cet article vous aide à comprendre comment sécuriser vos solutions IoT dans Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Présentation de la sécurité Internet des objets

Azure internet de services d’éléments (IoT) offrent un large éventail de fonctions. Ces services de qualité entreprise vous permettent de :

- Collecter les données à partir d’appareils
- Analyser les flux dans-déplacement de données
- Stocker et grands ensembles de données de la requête
- Visualiser des données historiques et en temps réel
- Intégrer des systèmes back-office

Pour effectuer ces fonctionnalités, les logiciels de Azure IoT Suite ensemble plusieurs services Azure avec les extensions personnalisées en tant que solutions préconfigurées. Ces solutions préconfigurées sont mises en œuvre base de modèles de solution IoT courants qui vous aident à réduire le temps que vous prenez sur votre IoT des solutions. À l’aide des kits de développement logiciel IoT, vous pouvez personnaliser et étendre ces solutions pour répondre à vos besoins. Vous pouvez également utiliser ces solutions comme des exemples ou modèles lorsque vous développez des solutions IoT nouveau.

La suite IoT Azure est une solution puissante pour vos besoins IoT. Cependant, il est d’importance upmost que vos solutions IoT sont conçues avec la sécurité à l’esprit à partir du début. En raison du nombre élevé de périphériques IoT, un incident de sécurité peut devenir rapidement un événement étendu des conséquences significatives.

Pour vous aider à comprendre comment sécuriser vos solutions IoT, nous avons les informations suivantes.

## <a name="security-architecture"></a>Architecture de sécurité

Lorsque vous créez un système, il est important de comprendre les menaces potentielles à ce système, puis ajoutez défense approprié en conséquence, comme le système est conçu et architecture. Il est essentiel de concevoir le produit à partir du début avec la sécurité à l’esprit, car comprendre comment un intrus peut être en mesure de compromettre un système d’aide à rendre minimisation savez appropriées sont en place à partir du début.

Vous pouvez en savoir plus sur l’architecture de sécurité IoT en lisant l’article [Internet d’Architecture de sécurité d’éléments](../iot-suite/iot-security-architecture.md).

Cet article aborde les rubriques suivantes :

- [La sécurité commence par un modèle de menace](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Sécurité dans IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [L’Architecture de référence IoT Azure de modélisation des menaces](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Sécurité d’emblée

La IoT défis uniques sécurité, confidentialité et conformité à entreprises du monde entier. Contrairement à la technologie informatiques traditionnel où ces problèmes concernent logiciels et son fonctionnement, IoT concerne que se passe-t-il lors de l’informatique et les univers physiques convergent. Protection des solutions IoT nécessite garantissant la mise en service sécurisé des périphériques, une connexion sécurisée entre les appareils suivants le cloud et protection des données sécurisé dans le cloud au cours de traitement et de stockage. Utilisation par rapport à ces fonctionnalités, existe cependant appareils soumis à des contraintes de ressources, de distribution géographique des déploiements et un grand nombre d’appareils au sein d’une solution.

Vous pouvez apprendre à gérer la sécurité dans ces zones en lisant l’article [sécurité Internet des objets d’emblée](../iot-suite/securing-iot-ground-up.md).

L’article aborde les rubriques suivantes :

- [Infrastructure sécurisée d’emblée](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure – infrastructure IoT sécurisée pour votre entreprise](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Meilleures pratiques

Sécuriser une infrastructure IoT nécessite une stratégie de sécurité en profondeur rigoureuse. Chaque calque à partir de la protection des données dans le cloud, protège l’intégrité des données lors de leur transfert via internet public et offre la possibilité de mise en service en toute sécurité des appareils, crée une meilleure garantie de sécurité dans l’infrastructure globale.

Vous pouvez en savoir plus sur la sécurité Internet des objets recommandations en lisant les [meilleures pratiques de sécurité Internet des objets](../iot-suite/iot-security-best-practices.md).

L’article aborde les rubriques suivantes :

- [IoT matériel fabricant/intégration](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Développeurs de solutions IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Responsable du déploiement de solution IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Opérateur de solution IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
