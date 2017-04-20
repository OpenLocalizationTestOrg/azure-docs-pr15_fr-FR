<properties
     pageTitle="Utiliser le portail Azure pour configurer le téléchargement de fichier | Microsoft Azure"
     description="Découvrez comment configurer le transfert des fichiers à l’aide du portail Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurer les téléchargements de fichiers à l’aide du portail Azure

## <a name="file-upload"></a>Téléchargement de fichier

Utiliser la [fonctionnalité dans IoT concentrateur de téléchargement de fichiers][lnk-upload], vous devez tout d’abord associer un compte de stockage Azure à votre plateforme. Sélectionnez les paramètres de **téléchargement de fichiers** pour afficher une liste des propriétés de téléchargement de fichier pour le hub IoT qui est en cours de modification.

![][13]

**Conteneur de stockage**: utiliser le portail Azure pour sélectionner un conteneur blob dans un compte de stockage Azure dans votre abonnement actuel Azure à associer à votre plateforme IoT. Si nécessaire, vous pouvez créer un compte de stockage Azure sur le conteneur de carte et blob **comptes de stockage** sur la carte de **conteneurs** . Concentrateur IoT génère automatiquement MU associations de sécurité avec les autorisations d’écriture pour ce conteneur blob pour les périphériques à utiliser lors de télécharger des fichiers.

![][14]

**Recevoir des notifications pour les fichiers téléchargés**: activer ou désactiver les notifications de téléchargement de fichier via le bouton bascule.

**TTL associations de sécurité**: ce paramètre est le temps de vie des associations de sécurité URI renvoyées à l’appareil par IoT concentrateur. Définir une heure par défaut, mais peut être personnalisée à d’autres valeurs à l’aide du curseur.

**Notification de fichier de paramètres par défaut TTL**: notification le temps de vie d’un fichier de téléchargement avant qu’il a expiré. Un jour la valeur par défaut, mais peut être personnalisée à d’autres valeurs à l’aide du curseur.

**Nombre de remise maximale de notification de fichiers**: le nombre de fois où le IoT Hub tente de remettre un fichier de notification de téléchargement. La valeur 10 par défaut, mais peut être personnalisée à d’autres valeurs à l’aide du curseur.

![][15]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les fonctionnalités de téléchargement de fichier du concentrateur IoT, voir [télécharger des fichiers à partir d’un périphérique] [ lnk-upload] dans le guide du développeur.

Suivez ces liens pour en savoir plus sur la gestion des Azure IoT concentrateur :

- [Gérer les appareils IoT en bloc][lnk-bulk]
- [Mesures de l’utilisation][lnk-metrics]
- [Contrôle des opérations][lnk-monitor]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]
- [Sécurisez votre solution IoT notions fondamentales sur vers le haut][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md