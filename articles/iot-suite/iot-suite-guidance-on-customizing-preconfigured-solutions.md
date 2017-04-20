<properties
    pageTitle="Personnalisation des solutions préconfigurés | Microsoft Azure"
    description="Fournit des conseils sur la façon de personnaliser les solutions Azure IoT Suite préconfiguré."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Personnaliser une solution préconfigurée

Les solutions préconfigurées fournies avec la gamme IoT Azure montrent les services au sein de la suite travaillant ensemble pour fournir une solution de bout en bout. À partir de ce point de départ, il existe des emplacements dans lesquels vous pouvez étendre et personnaliser la solution pour des scénarios spécifiques. Les sections suivantes décrivent ces points de personnalisation courantes.

## <a name="finding-the-source-code"></a>Trouver le code source

Le code source pour les solutions préconfigurés est disponible sur GitHub dans les référentiels suivants :

- Contrôle à distance : [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Maintenance prédictive : [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Le code source pour les solutions préconfigurés est fourni pour illustrer les modèles et les pratiques utilisés pour implémenter les fonctionnalités de bout en bout d’une solution IoT à l’aide Azure IoT Suite. Vous trouverez plus d’informations sur la façon de créer et déployer les solutions dans les référentiels GitHub.

## <a name="changing-the-preconfigured-rules"></a>Modifier les règles préconfigurés

La solution d’analyse à distance inclut trois tâches [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) pour implémenter appareil logique d’informations, télémétrie et règles affichée pour la solution.

Les tâches analytique trois flux et leur syntaxe sont décrites en détail dans la [procédure pas à pas de solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md). 

Vous pouvez modifier ces tâches directement pour modifier la logique ou ajouter une logique spécifique à votre scénario. Vous pouvez trouver les tâches de flux de données Analytique comme suit :
 
1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez au groupe de ressources avec le même nom que votre solution IoT. 
3. Sélectionnez la tâche Azure flux Analytique que vous souhaitez modifier. 
4. Arrêter le travail en sélectionnant **Arrêter**dans l’ensemble des commandes. 
5. Modifier les entrées, les requêtes et les sorties.

    Une modification simple consiste à modifier la requête pour le travail de **règles** à utiliser un **« < »** au lieu d’un **« > »**. Le portail de solution s’affichent toujours **» > «** lorsque vous modifiez une règle, mais vous remarquerez que le comportement est retourné en raison de la modification de la tâche sous-jacente.

6. Démarrer le travail

> [AZURE.NOTE] Le tableau de bord de surveillance à distance dépend des données spécifiques, afin de modifier les tâches peut entraîner le tableau de bord échec.

## <a name="adding-your-own-rules"></a>Ajout de vos propres règles

Outre la modification des tâches Azure flux Analytique préconfigurés, vous pouvez utiliser le portail Azure pour ajouter de nouveaux travaux ou ajouter de nouvelles requêtes aux projets existants.

## <a name="customizing-devices"></a>Personnalisation des appareils mobiles

Une des activités d’extension plus courantes fonctionne avec des appareils spécifiques à votre scénario. Il existe plusieurs méthodes pour travailler avec des appareils. Ces méthodes incluent modifier un périphérique simulé conformément à votre scénario, ou en utilisant le [Kit de développement logiciel IoT appareil][] pour connecter votre périphérique physique à la solution.

Pour des instructions détaillées sur l’ajout de périphériques à la solution préconfigurée contrôle à distance, voir [Iot Suite connexion appareils](iot-suite-connecting-devices.md) et l' [Distant exemple SDK C surveillance](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) qui est conçu pour fonctionner avec la solution préconfigurée d’analyse à distance.

### <a name="creating-your-own-simulated-device"></a>Création de votre propre simulé à l’appareil

Inclus dans le code de source solution contrôle à distance (cité ci-dessus), est un simulator .NET. Cette simulator fait partie d’une mise en service dans le cadre de la solution et peut être modifiée pour envoyer des métadonnées différentes, télémétrie ou répondre à des commandes différentes.

Le simulator préconfiguré dans la solution préconfiguré contrôle à distance est un appareil REFROIDISSEUR qui émet télémétrie température et humidité, vous pouvez modifier le simulator dans le projet [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) lorsque vous avez dupliquée le référentiel GitHub.

### <a name="available-locations-for-simulated-devices"></a>Emplacements disponibles pour les appareils simulés

Le jeu par défaut des emplacements est Seattle/Redmond, Washington, États-Unis d’Amérique. Vous pouvez modifier ces emplacements dans [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Création et utilisation de votre propre unité (physique)

La [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) fournissent des bibliothèques pour la connexion de nombreux types de périphériques (langues et systèmes d’exploitation) dans des solutions IoT.

## <a name="modifying-dashboard-limits"></a>Modification des limites de tableau de bord

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Nombre d’unités affichées dans la liste déroulante du tableau de bord

La valeur par défaut est de 200. Vous pouvez modifier ce numéro dans [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Nombre de codes confidentiels à afficher dans le contrôle de carte Bing

La valeur par défaut est de 200. Vous pouvez modifier ce numéro dans [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Période de graphique de télémétrie

La valeur par défaut est 10 minutes. Vous pouvez le modifier dans [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configurer manuellement les rôles d’application

La procédure suivante explique comment ajouter des rôles **d’administrateur** et **en lecture seule** application à une solution préconfigurée. Notez que les solutions préconfigurées sa mise en service à partir du site azureiotsuite.com déjà inclure les rôles **d’administrateur** et **en lecture seule** .

Les membres de ce rôle **en lecture seule** peuvent voir le tableau de bord et la liste des périphériques, mais ne sont pas autorisés pour ajouter des périphériques, modifier les attributs du périphérique ou envoyer des commandes.  Les membres du rôle **administrateur** ont un accès complet à toutes les fonctionnalités de la solution.

1. Accédez au [portail classique Azure][lnk-classic-portal].

2. Sélectionnez **Active Directory**.

3. Cliquez sur le nom du client DAS que vous avez utilisé lorsque vous sa mise en service de votre solution.

4. Cliquez sur **Applications**.

5. Cliquez sur le nom de l’application qui correspond au nom de votre solution préconfiguré. Si vous ne voyez pas votre application dans la liste, sélectionnez **Applications propriétaire de mon entreprise** au format **Afficher** de liste déroulante et cliquez sur la coche.

6.  Dans la partie inférieure de la page, cliquez sur **Gérer manifeste** , puis **Télécharger manifeste**.

7. Ceci permet de télécharger un fichier .json sur votre ordinateur local.  Ouvrir ce fichier pour modification dans un éditeur de texte de votre choix.

8. Sur la troisième ligne du fichier .json, vous trouverez :

  ```
  "appRoles" : [],
  ```
  Remplacez ceci avec les éléments suivants :

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Enregistrez le fichier mis à jour .json (vous pouvez remplacer le fichier existant).

10.  Dans le portail de gestion Azure, en bas de la page, sélectionnez **Gérer manifeste** puis **Télécharger manifeste** à télécharger le fichier .json que vous avez enregistrée à l’étape précédente.

11. Vous avez maintenant ajouté les rôles **d’administrateur** et **en lecture seule** à votre application.

12. Pour affecter un de ces rôles à un utilisateur dans votre annuaire, voir [autorisations sur le site azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Commentaires

Offrez-vous une personnalisation vous souhaiteriez couvert dans ce document ? Ajoutez les suggestions de fonctionnalité à [Utilisateur vocale](https://feedback.azure.com/forums/321918-azure-iot), ou un commentaire dans cet article ci-dessous. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options permettant de personnaliser les solutions préconfigurées, voir :

- [Connecter une logique de l’application à votre solution Azure IoT Suite surveillance préconfiguré][lnk-logicapp]
- [Solution préconfigurée de télémétrie dynamique utiliser avec le contrôle à distance][lnk-dynamic]
- [Solution préconfigurée appareil informations métadonnées dans le contrôle à distance][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[APPAREIL IoT SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
