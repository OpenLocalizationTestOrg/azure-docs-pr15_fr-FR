<properties
   pageTitle="Internet des éléments sécurité recommandations | Microsoft Azure"
   description="L’article fournit une liste curated de Microsoft Internet de meilleures pratiques de sécurité des éléments et de recommandations générales."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet de meilleures pratiques de sécurité des éléments

Sécurisation de l’infrastructure Internet des objets (IoT) est une entreprise critique pour toute personne travaillant avec IoT solutions. Étant donné le nombre de périphériques impliqués et la nature distribuée de ces appareils, l’impact un événement de sécurité liées à compromettre des millions d’appareils IoT est important et peut avoir impact étendu.

Pour cette raison, la sécurité IoT doit une approche de sécurité en profondeur. Données doivent être en sécurité dans le cloud et lorsqu’il se déplace sur les réseaux publics et privés. Méthodes doivent être mis en place en toute sécurité mise en service les périphériques IoT eux-mêmes. Chaque calque à partir de l’appareil, au réseau, vers le cloud principale doit garanties de sécurité renforcée.

Meilleures pratiques IoT peuvent être classées comme suit :

- Fabricant du matériel IoT ou intégration
- Développeurs de solutions IoT
- Responsable du déploiement de solution IoT
- Opérateur de solution IoT

Cet article résume [Internet de choses meilleures pratiques de sécurité](../iot-suite/iot-security-best-practices.md). Consultez cet article pour plus d’informations.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricant du matériel IoT ou intégration

Suivre les meilleures pratiques ci-dessous si vous êtes un fabricant IoT ou une intégration matérielle :

- **Matériel étendue à la configuration minimale requise**: la conception du matériel doit inclure des fonctionnalités minimales requises pour l’opération du matériel et rien de plus. 
- **Assurez-vous matériel falsification**: créer en mécanismes de détecter la falsification physique du matériel, telles que l’ouverture de la page de garde appareil, en supprimant une partie de l’appareil, etc.. 
- **Créer autour de matériel sécurisé**: si les [coûts des produits vendus](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permettent, générer des fonctionnalités de sécurité tels que le stockage sécurisé et chiffré et les fonctionnalités de démarrage basé sur le Module plateforme sécurisée.
- **Vérifiez les mises à niveau sécurisé**: mise à jour pendant la durée de vie de l’appareil est inévitable.

## <a name="iot-solution-developer"></a>Développeurs de solutions IoT

Suivre les meilleures pratiques ci-dessous si vous êtes un développeur de solution IoT :

- **Méthodologie de développement de logiciels sécurisés suivre**: développement de logiciels sécurisés nécessite réflexion spécialisé sur sécurité depuis le commencement du projet tout à fait à sa mise en œuvre, de test et de déploiement.
- **Sélectionnez logiciel libres avec soin**: logiciels open source fournit une opportunité pour développer rapidement des solutions.
- **Intégrer avec soin**: la plupart des défauts de sécurité du logiciel existent à la limite de bibliothèques et d’API. 

## <a name="iot-solution-deployer"></a>Responsable du déploiement de solution IoT

Suivre les meilleures pratiques ci-dessous si vous êtes un responsable du déploiement de solution IoT :

- **Déployer matériel en toute sécurité**: IoT déploiements peuvent nécessiter du matériel à déployer dans des emplacements non sécurisés, comme dans les espaces publics ou des paramètres régionaux qui fonctionne sans surveillance.
- **Protéger les clés d’authentification**: au cours du déploiement, chaque appareil nécessite des ID de périphériques et associés clés d’authentification générés par le service cloud. Protégez ces touches physiquement même après le déploiement. Toute clé compromis peut être utilisée par un périphérique malveillant pour passer un périphérique existant.

## <a name="iot-solution-operator"></a>Opérateur de solution IoT

Suivre les meilleures pratiques ci-dessous si vous êtes un opérateur de solution IoT :

- **Systèmes de mise à jour**: vérifier tous les pilotes de périphériques et les systèmes d’exploitation appareil sont mises à jour avec les dernières versions. 
- **Protéger contre les pirates**: si le système d’exploitation permet, placez les capacités de protection contre les virus et logiciels malveillants plus récentes sur chaque système d’exploitation périphérique. 
- **Audit fréquemment**: IoT infrastructure d’audit pour les problèmes est clé lors de la réponse aux incidents de sécurité liés à la sécurité.
- **Protégez physiquement l’infrastructure IoT**: les pire sécurité attaques IoT infrastructure sont lancés à l’aide de l’accès physique aux périphériques.
- **Protéger les informations d’identification cloud**: nuage d’identification utilisées pour la configuration et l’utilisation d’un déploiement IoT est éventuellement le moyen le plus simple pour accéder et compromettre un système IoT. 
