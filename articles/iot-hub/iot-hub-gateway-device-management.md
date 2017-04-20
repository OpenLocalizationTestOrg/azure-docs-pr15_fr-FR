<properties
 pageTitle="Activer les périphériques gérées derrière une passerelle IoT | Microsoft Azure"
 description="Rubrique d’aide à l’aide d’une passerelle IoT créé en utilisant le Kit de développement de passerelle IoT ainsi que des périphériques gérés par IoT concentrateur."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Activer les périphériques gérées derrière une passerelle IoT

## <a name="basic-device-isolation"></a>Isolement périphériques de base

Organisations utilisent souvent des passerelles IoT pour accroître la sécurité de leurs solutions IoT globale. Certains appareils vous devez envoyer les données dans le cloud, mais ne sont pas en mesure de se protéger contre les menaces sur internet. Vous pouvez protéger les appareils suivants à partir de threads externes en les faisant à communiquer avec l’extérieur via une passerelle.

La passerelle se trouve sur la bordure entre un environnement sécurisé et internet ouvert. Appareils parler à la passerelle et la passerelle transmet les messages par rapport à la destination cloud correcte. La passerelle est renforcée contre les threads externes, bloque les requêtes non autorisés, permet le trafic entrant autorisé et transfère que le trafic entrant sur le périphérique approprié.

![][1]

Vous pouvez également placer périphériques qui peuvent se protéger derrière une passerelle pour un niveau de sécurité supplémentaire. Dans ce scénario, il vous suffit de maintenir la passerelle OS corrigée contre les dernières vulnérabilités, au lieu de la mise à jour le système d’exploitation sur chaque appareil.

## <a name="isolation-plus-intelligence"></a>Isolement plus intelligence

Un routeur renforcé est une passerelle suffisante pour simplement isoler appareils. Toutefois, les solutions de IoT nécessitent souvent qu’une passerelle fournit une intelligence plus que simplement isoler des appareils mobiles. Par exemple, vous souhaiterez peut-être gérer vos périphériques à partir du cloud. Vous êtes utiliser [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), un protocole de gestion des périphériques standard, pour le composant de gestion de cloud de la solution. Toutefois, les appareils envoyer télémétrie à l’aide d’un TCP/IP non activé le protocole. En outre, les appareils générer un nombre important de données et vous ne souhaitez pas télécharger un sous-ensemble de la télémétrie filtrées. Vous pouvez créer une solution qui incorpore une passerelle IoT capable de traiter avec deux flux distinctes de données. La passerelle, vous devez :

-   Comprendre la **télémétrie**, filtrage et puis téléchargez-le sur le cloud via la passerelle. La passerelle n’est plus simple routeur qui transfère simplement des données entre l’appareil et le cloud.

-   Exchange simplement les **données de gestion des périphériques LWM2M** entre les périphériques et le cloud. La passerelle n’a pas besoin de comprendre les données dans celle-ci et doit uniquement pour vous assurer que les données obtient passées en arrière entre les périphériques et le cloud.

La figure suivante illustre ce scénario :

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>La solution : gestion des périphériques IoT Azure et le Kit de développement de passerelle IoT 

Afficher un aperçu du public version de [Gestion des périphériques Azure IoT] [ lnk-device-management] et version bêta du [Kit de développement logiciel Azure IoT passerelle] activer ce scénario. La passerelle traite chaque flux de données comme suit :

-   **Télémétrie**: vous pouvez utiliser le Kit de développement de passerelle IoT pour créer un pipeline qui prend en charge, filtres et envoie des données de télémétrie dans le cloud. Le Kit de développement de passerelle IoT fournit un code qui mettent en œuvre, des parties de ce pipeline place du développeur. Vous trouverez plus d’informations sur l’architecture du Kit de développement du [IoT passerelle SDK - prise en main] [ lnk-gateway-get-started] didacticiel.

-   **Gestion des appareils**: gestion des appareils Azure fournit un client LWM2M qui s’exécute sur le périphérique, ainsi qu’une interface de cloud pour émettre des commandes de gestion à l’appareil.
    
    Vous ne nécessitez pas de logique particulière sur la passerelle, car elle n’a pas besoin de traiter les données LWM2M échangées entre le périphérique et votre plateforme IoT. Vous pouvez activer ICS, une fonctionnalité de nombreux systèmes d’exploitation modernes, sur la passerelle pour permettre l’échange de données LWM2M. Vous pouvez choisir un système d’exploitation approprié pour ce scénario, car le Kit de développement de passerelle IoT prend en charge une variété de systèmes d’exploitation. Voici des instructions pour l’activation de partage dans [Windows 10] et [Ubuntu]deux des nombreux systèmes d’exploitation pris en charge d’une connexion internet.

L’illustration suivante montre l’architecture de haut niveau utilisée pour activer ce scénario à l’aide de la [Gestion des périphériques Azure IoT] [ lnk-device-management] et le [Kit de développement logiciel Azure IoT passerelle].

![][3]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du Kit de développement de passerelle IoT, consultez les didacticiels suivants :

- [Passerelle IoT SDK - commencer à utiliser Linux][lnk-gateway-get-started]
- [IoT passerelle SDK – envoyer des messages avec un appareil simulé à l’aide de Linux appareil-nuage][lnk-gateway-simulated]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Kit de développement logiciel Azure IoT passerelle]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md