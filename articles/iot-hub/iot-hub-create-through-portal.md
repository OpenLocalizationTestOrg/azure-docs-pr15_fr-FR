<properties
     pageTitle="Utiliser le portail Azure pour créer un concentrateur IoT | Microsoft Azure"
     description="Découvrez comment créer et gérer des hubs IoT Azure via le portail d’Azure"
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

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Créer un concentrateur IoT à l’aide du portail Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introduction

Cet article décrit comment trouver le service IoT concentrateur dans le portail Azure et comment créer et gérer des hubs IoT.

## <a name="where-to-find-iot-hubs"></a>Où trouver hubs IoT

Il existe plusieurs emplacements où vous pouvez trouver IoT hubs.

1. **+ Nouveau**: **Azure IoT concentrateur** est un service IoT et vous pouvez trouver dans la catégorie **Internet des objets**, sous **+ Nouveau**, similaire à d’autres services.

2. IoT hubs sont également accessible via le marché en tant que le service hero sous **Internet des objets**.

## <a name="create-an-iot-hub"></a>Créer un concentrateur IoT

Vous pouvez créer un concentrateur IoT à l’aide des méthodes suivantes :

- Création d’un concentrateur IoT via l’option **+ Nouveau** permet d’accéder à la carte indiqué dans la capture d’écran suivante. Les étapes permettant de créer le hub IoT via cette méthode et à la place de marché sont identiques.

- Création d’un concentrateur IoT à la place de marché : cliquez sur **créer** pour ouvrir une carte qui est identique à la carte précédente pour une expérience **+ Nouveau** . Les sections suivantes répertorient les différentes étapes permettant de créer un concentrateur IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Sélectionnez le nom du concentrateur IoT

Pour créer un concentrateur IoT, vous devez nommer le hub. Ce nom doit être unique au sein des hubs. Aucun duplication des hubs n’est autorisé sur le serveur principal, il est conseillé que ce concentrateur est nommé de manière unique que possible.

### <a name="choose-the-pricing-tier"></a>Choisissez le niveau de tarification

Vous pouvez choisir parmi quatre niveaux : **libre**, **Standard 1** et **2 Standard**et **S3 Standard**. La couche gratuite permet uniquement 500 appareils pour être connecté au concentrateur IoT et jusqu'à 8 000 messages par jour.

**S1 Standard**: IoT Hubs S1 edition est conçu pour les solutions IoT ayant un grand nombre d’appareils génération de petites quantités de données par périphérique. Chaque unité de l’édition S1 permet jusqu'à 400 000 messages par jour sur tous les périphériques connectés.

**S2 Standard**: IoT concentrateur S2 edition est conçu pour les solutions IoT dans lequel appareils génèrent de grandes quantités de données. Chaque unité de l’édition S2 permet jusqu'à 6 millions de messages par jour entre tous les périphériques connectés.

**S3 Standard**: IoT concentrateur S3 edition est conçu pour les solutions IoT génèrent de grandes quantités de données. Chaque unité de l’édition S3 permet jusqu'à 300 millions de messages par jour entre tous les périphériques connectés.

![][4]

> [AZURE.NOTE] Concentrateur IoT n’autorise un concentrateur gratuit par abonnement Azure.

### <a name="iot-hub-units"></a>Unités de concentrateur IoT

Une unité de concentrateur IoT inclut un certain nombre de messages par jour. Le nombre total de messages pris en charge pour ce concentrateur est le nombre d’unités multiplié par le nombre de messages par jour pour ce niveau. Par exemple, si vous souhaitez que le hub IoT pour prendre en charge de pénétration de 700 000 messages, vous choisissez deux unités couche S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>APPAREIL partitions cloud et groupe de ressources

Vous pouvez modifier le nombre de partitions pour un concentrateur IoT. Partitions par défaut sont définies sur 4 ; Toutefois, vous pouvez choisir un nombre différent de partitions à partir d’une liste déroulante.

Pour les groupes de ressources, vous n’avez pas besoin de créer un groupe de ressources vide de manière explicite. Lorsque vous créez une ressource, vous pouvez choisir créer un nouveau groupe de ressources ou utiliser un groupe de ressources existant.

![][5]

### <a name="choose-subscriptions"></a>Choisissez abonnements

Concentrateur IoT Azure affiche automatiquement la liste des abonnements Azure auquel le compte d’utilisateur est lié. Vous pouvez choisir une des options ici pour associer le hub IoT à cet abonnement Azure.

### <a name="choose-the-location"></a>Choisissez l’emplacement

L’option d’emplacement fournit la liste des régions dans lesquels IoT concentrateur est proposé. Concentrateur IoT n’est disponible pour déployer aux emplacements suivants : Australie Orient, Australie sud-est, Asie de l’est, Asie du Sud-est, Europe du Nord, ouest Europe, Moyen-Orient Japon, Japon ouest, nous Moyen-Orient, nous ouest.

### <a name="create-the-iot-hub"></a>Créer le hub IoT

Lorsque toutes les étapes précédentes sont terminées, le hub IoT est prêt à créer. Cliquez sur **créer** pour démarrer le processus principal de la création de ce concentrateur IoT avec les options spécifiques et déployer vers l’emplacement spécifié.

Il peut prendre quelques minutes pour que le hub IoT à être créés de temps pour le déploiement principale se produire dans les serveurs de l’emplacement approprié.

## <a name="change-the-settings-of-the-iot-hub"></a>Modifier les paramètres du concentrateur IoT

Vous pouvez modifier les paramètres d’un concentrateur IoT existant après sa création à partir de la carte IoT concentrateur.

![][8]

**Stratégies d’accès partagés**: ces politiques définissent les autorisations pour les appareils et services pour vous connecter à IoT concentrateur. Vous pouvez accéder à ces politiques en cliquant sur les **Stratégies d’accès partagés** sous **Général**. Dans cette carte, vous pouvez modifier les stratégies existantes ou ajouter une nouvelle stratégie.

### <a name="create-a-policy"></a>Créer une stratégie

- Cliquez sur **Ajouter** pour ouvrir une carte. Vous pouvez saisir le nouveau nom de stratégie et les autorisations que vous souhaitez associer à cette stratégie, comme illustré dans l’illustration suivante.

    Il existe plusieurs autorisations qui peuvent être associées à ces politiques partagés. Les deux premières stratégies, **Registre lire** et **écrire du Registre**, grant lire et droits d’accès en écriture dans le magasin d’identités appareil ou le Registre identité. Choix de l’option d’écriture automatiquement choisit l’option de lecture.

    La stratégie de **Service se connecter** accorde l’autorisation d’accéder aux points de terminaison côté cloud tel que le groupe grand public pour les services connexion au concentrateur IoT. La stratégie de **connexion de périphérique** sont accordées pour envoyer et recevoir des messages sur les points de terminaison côté périphérique du concentrateur IoT.

- Cliquez sur **créer** pour ajouter ce nouveau stratégie à la liste existante.

![][10]

## <a name="messaging"></a>Messagerie

Cliquez sur **la messagerie** pour afficher une liste de propriétés pour le hub IoT qui est en cours de modification de la messagerie. Il existe deux principaux types de propriétés que vous pouvez modifier ou copier : **appareils dans le Cloud**et **Cloud à l’appareil** .

- Paramètres de **cloud sur périphérique** : ce paramètre comporte deux sous-Paramètres : **nuage appareil TTL** (durée de vie) et la **durée de rétention** pour les messages. Lorsque vous créez le hub IoT, ces deux paramètres sont créées avec une valeur par défaut d’une heure. Pour ajuster ces valeurs, utilisez les curseurs ou tapez les valeurs.

- Paramètres du **périphérique dans le Cloud** : ce paramètre n’a plusieurs sous-Paramètres, certaines d'entre elles sont nommé/affectées lorsque le hub IoT est créé et ne peut être copié à d’autres sous-Paramètres personnalisables. Ces paramètres sont indiqués dans la section suivante.

**Partitions**: cette valeur est définie lorsque le hub IoT est créé et peut être modifié via ce paramètre.

**Point de terminaison et nom de l’événement concentrateur compatible**: lorsque IoT le hub est créé, un concentrateur événement est créé en interne que vous devrez peut-être l’accès à certaines conditions. Ce nom d’événement concentrateur compatible et le point de terminaison ne peut pas être personnalisé mais n’est disponibles pour une utilisation via le bouton **Copier** .

**Durée de conservation**: un jour la valeur par défaut, mais peut être personnalisée à d’autres valeurs à l’aide de la liste déroulante. Cette valeur est en jours pour appareil dans le Cloud et non dans les heures, ainsi que le paramètre similaire pour le Cloud à l’appareil.

**Groupes de consommateurs**: les consommateurs sont un paramètre similaire à d’autres systèmes de messagerie qui peuvent être utilisés pour extraire des données d’une manière spécifique pour vous connecter d’autres applications ou des services à IoT concentrateur. Chaque concentrateur IoT est créé avec un groupe de fournisseurs par défaut. Toutefois, vous pouvez ajouter ou supprimer des groupes de consommateurs vers votre hubs IoT.

> [AZURE.NOTE] Le groupe consommateur par défaut ne peut pas être modifié ni supprimé.

![][11]

## <a name="pricing-and-scale"></a>Tarifs et échelle

Le prix d’un concentrateur IoT existant peut être modifié via les paramètres de **tarification** , avec les exceptions suivantes :

- Dans l’implémentation actuelle, un concentrateur IoT avec une référence SKU gratuit ne pouvez pas modifier les niveaux à l’un des références SKU payants, ou vice versa.
- Il ne peut être un concentrateur IoT couche libre dans l’abonnement Azure.

![][12]

Déplacement d’un niveau supérieur (S2 ou S3) afin de réduire les niveaux (S1 ou S2) est autorisée uniquement lorsque le nombre de messages envoyés pour ce jour n’est pas en conflit. Par exemple, si le nombre de messages par jour dépasse 400 000, puis la couche pour le hub IoT peut être modifiée. Toutefois, si vous modifiez la couche S1 le hub est limité pour ce jour.

## <a name="delete-the-iot-hub"></a>Supprimer le hub IoT

Vous pouvez parcourir le hub IoT à supprimer en cliquant sur **Parcourir**, puis en choisissant le hub approprié pour le supprimer. Cliquez sur le bouton **Supprimer** sous le nom du concentrateur pour supprimer le hub.

## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur la gestion des Azure IoT concentrateur :

- [Gérer les appareils IoT en bloc][lnk-bulk]
- [Mesures de l’utilisation][lnk-metrics]
- [Contrôle des opérations][lnk-monitor]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]
- [Sécurisez votre solution IoT notions fondamentales sur vers le haut][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md