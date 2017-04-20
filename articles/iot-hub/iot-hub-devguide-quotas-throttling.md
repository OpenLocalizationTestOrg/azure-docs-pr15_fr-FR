<properties
 pageTitle="Guide du développeur - quotas et des limitations | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - description des quotas qui s’appliquent à IoT concentrateur et le comportement de limitation attendu"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="reference---quotas-and-throttling"></a>Référence - Quotas et des limitations

## <a name="quotas-and-throttling"></a>Les quotas et la limitation

Chaque abonnement Azure peut avoir au maximum de 10 IoT hubs.

Chaque concentrateur IoT est configurée avec un certain nombre d’unités dans une référence SKU spécifique (pour plus d’informations, voir [Azure IoT concentrateur tarifs][lnk-pricing]). Les références SKU et le nombre d’unités de déterminent le quota quotidien maximal des messages que vous pouvez envoyer.

La référence (SKU) détermine également les limites de limitation IoT concentrateur applique sur toutes les opérations.

## <a name="operation-throttles"></a>Limitations de fonctionnement

Limitations de l’opération sont les limites de taux qui sont appliquées dans la zone plages minute et sont destinées à éviter abuse. Concentrateur IoT tente d’éviter le renvoi d’erreurs dès que possible, mais elle commence retourner des exceptions si la limitation n’est pas respectée trop longtemps.

Voici la liste des limitations appliquées. Valeurs font référence à un concentrateur individuel.

| Limitation | Valeur par concentrateur |
| -------- | ------------- |
| Opérations de Registre identité (créer, récupérer, list, mettre à jour, supprimer) | min/5000/unité (pour S3) <br/> min/100/unité (pour S1 et S2). |
| Connexions de périphériques | 6000/sec / (pour S3), 120/sec/unité (pour S2), s/12/unité (pour S1). <br/>Nombre minimal de 100/sec. <br/> Par exemple, deux unités S1 sont 2\*12 = 24/sec, mais que vous disposez au moins de 100/sec sur vos unités. Avec neuf S1 unités, vous avez 108/sec (9\*12) entre votre unités. |
| APPAREIL-nuage envoie | 6000/sec / (pour S3), 120/sec/unité (pour S2), s/12/unité (pour S1). <br/>Nombre minimal de 100/sec. <br/> Par exemple, deux unités S1 sont 2\*12 = 24/sec, mais que vous disposez au moins de 100/sec sur vos unités. Avec neuf S1 unités, vous avez 108/sec (9\*12) entre votre unités. |
| Envoie cloud vers le périphérique | 5000/min / (pour S3), 100/min/unité (pour S1 et S2). |
| Nuage-périphérique reçoit | 50000/min / (pour S3), 1000/min/unité (pour S1 et S2). |
| Opérations de téléchargement de fichier | téléchargement de fichiers 5000 notifications/min/unité (pour S3), téléchargement de fichier 100 notifications/min/unité (pour S1 et S2). <br/> 10000 sa MU peut être arrière pour un compte de stockage Azure en même temps.<br/> 10 associations de sécurité MU/périphérique peut être absence en même temps. | 

Il est important de clarifier que la limitation de *connexions du périphérique* régit la fréquence à laquelle nouvel appareil connexions peuvent être établies avec un concentrateur IoT et pas le nombre maximal de périphériques connectés simultanément. La limitation varie selon le nombre d’unités qui sont configurés pour le hub.

Par exemple, si vous achetez une seule unité S1, vous obtenez une limitation de 100 connexions par seconde. Cela signifie que pour vous connecter 100 000 appareils, il vous permet d’accéder au moins 1000 secondes (environ 16 minutes). Toutefois, vous pouvez avoir des appareils connectés simultanément autant que vous disposez des périphériques sont enregistrés dans le Registre identité appareil.

Pour une présentation détaillée du concentrateur IoT la limitation comportement, voir le billet de blog [concentrateur IoT la limitation et vous][lnk-throttle-blog].

>[AZURE.NOTE] À tout moment, il est possible d’augmenter les quotas ou les limites de limitation en augmentant le nombre d’unités générés dans un concentrateur IoT.

>[AZURE.IMPORTANT] Opérations de Registre identité sont destinées à utiliser lors de l’exécution de gestion des périphériques et la mise en service des scénarios. Lecture ou mise à jour un grand nombre d’identités de l’appareil est pris en charge par le biais [Importer et exporter des travaux][lnk-importexport].

## <a name="next-steps"></a>Étapes suivantes

Autres rubriques de référence dans ce guide pour les développeurs IoT concentrateur sont les suivantes :

- [Points de terminaison IoT concentrateur][lnk-devguide-endpoints]
- [Langage de requête pour jumeaux, méthodes et les tâches][lnk-devguide-query]
- [Prise en charge IoT concentrateur MQTT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md