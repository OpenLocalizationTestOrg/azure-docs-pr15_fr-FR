<properties
    pageTitle="Prise en main des solutions préconfigurées | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à déployer une solution Azure IoT Suite préconfiguré."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Didacticiel : Prise en main les solutions préconfigurées

## <a name="introduction"></a>Introduction

Azure IoT Suite [solutions préconfigurées] [ lnk-preconfigured-solutions] combiner plusieurs services Azure IoT pour fournir des solutions de bout en bout pour mettre en œuvre des scénarios d’entreprise courants IoT. La solution de *contrôle à distance* préconfiguré se connecte à et surveille vos appareils. Vous pouvez utiliser la solution pour analyser le flux de données à partir de vos périphériques et d’améliorer les résultats métiers en effectuant les processus à répondre automatiquement à ce flux de données.

Ce didacticiel montre la mise en service de la solution préconfigurée d’analyse à distance. Il vous guide également dans les fonctionnalités de base de la solution d’analyse à distance. Vous pouvez accéder à la plupart de ces fonctionnalités via le tableau de bord solution déploie ainsi que la solution préconfigurée :

![Contrôle à distance préconfigurés du tableau de bord solution][img-dashboard]

Pour effectuer ce didacticiel, vous avez besoin d’un abonnement Azure actif.

> [AZURE.NOTE]  Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Version d’évaluation gratuite Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Afficher le tableau de bord de solution

Le tableau de bord solution permet de gérer la solution déployée. Par exemple, vous pouvez afficher télémétrie, ajoutez des périphériques et configurer des règles.

1.  Lorsque la mise en service et la vignette de votre solution préconfigurée indique **prêt**, cliquez sur la **barre de lancement** pour ouvrir votre portail solution contrôle à distance dans un nouvel onglet.

    ![Lancer la solution préconfigurée][img-launch-solution]

2.  Par défaut, le portail de solution affiche le *tableau de bord de solution*. Vous pouvez sélectionner d’autres affichages à l’aide du menu à gauche.

    ![Contrôle à distance préconfigurés du tableau de bord solution][img-dashboard]

Le tableau de bord affiche les informations suivantes :

- La carte s’affiche à l’emplacement de chaque périphérique est connecté à la solution. Lorsque vous exécutez tout d’abord la solution, il existe quatre périphériques simulés. Les appareils simulés sont implémentées comme WebJobs Azure, et la solution utilise l’API Bing Maps pour tracer des informations sur la carte.
- Le panneau **Historique de télémétrie** trace télémétrie hygrométrie et de température d’une unité sélectionnée dans près affiche agréger les données comme humidité maximale et minimale, moyenne et en temps réel.
- Le panneau **D’alarme historique** affiche les événements d’alarme récents lorsqu’une valeur de télémétrie a dépassé le seuil. Vous pouvez définir vos propres alarmes outre les exemples créés par la solution préconfigurée.

## <a name="view-the-device-list"></a>Afficher la liste des périphériques

La liste des périphériques affiche tous les appareils enregistrés dans la solution. Vous permet d’afficher et modifiez les métadonnées d’appareil, ajoutez ou supprimez des périphériques et envoyez des commandes aux périphériques.

1.  Cliquez sur **périphériques** dans le menu de gauche pour afficher la *liste des périphériques* pour cette solution.

    ![Liste des périphériques dans le tableau de bord][img-devicelist]

2.  La liste des périphériques indique qu’il existe quatre périphériques simulés créés par le processus de mise en service.

3.  Cliquez sur un appareil dans la liste des périphériques pour afficher les détails de l’appareil.

    ![Détails de l’appareil dans le tableau de bord][img-devicedetails]

Le panneau **Informations sur le périphérique** contient trois sections :

- La section **Actions** répertorie les actions que vous pouvez effectuer sur le périphérique. Si vous désactivez le périphérique, il n’est plus autorisé à envoyer de télémétrie ou recevoir des commandes. Si vous désactivez un périphérique, vous pouvez ensuite l’activer à nouveau. Vous pouvez ajouter une règle associée au périphérique qui déclenche une alarme lorsqu’une valeur de télémétrie dépasse un seuil. Vous pouvez également envoyer une commande à un appareil. Un périphérique se connecte tout d’abord, vous indique la solution les commandes qu’il peut y répondre.
- La section **Propriétés de l’appareil** répertorie les métadonnées de l’appareil. Certaines de ces métadonnées provient le périphérique lui-même (par exemple, le fabricant) et certaines est généré par la solution (par exemple, l’heure de création). Vous pouvez modifier les métadonnées de l’appareil à partir de là.
- La section **Clés d’authentification** répertorie les touches de que l’appareil peut utiliser pour vous authentifier avec la solution.

## <a name="send-a-command-to-a-device"></a>Envoyer une commande à un appareil

Le volet de détails appareil affiche toutes les commandes qui prend en charge un périphérique spécifique et permet d’envoyer des commandes à un appareil. Premier démarrage d’un périphérique, il envoie des informations sur les commandes que qui prend en charge pour la solution.

1.  Cliquez sur **commandes** dans le volet de détails de périphérique pour le périphérique sélectionné.

    ![Commandes de périphériques dans le tableau de bord][img-devicecommands]

2.  Sélectionnez **PingDevice** dans la liste de commande.

3.  Cliquez sur **Envoyer une commande**.

4.  Vous pouvez afficher l’état de la commande dans l’historique des commandes.

    ![État de la commande dans le tableau de bord][img-pingcommand]

La solution effectue le suivi de l’état de chaque commande qu'il envoie. Au départ, le résultat est **en attente**. Lorsque le périphérique signale qu’il a exécuté la commande, le résultat est défini sur **succès**.

## <a name="add-a-new-simulated-device"></a>Ajouter un nouveau périphérique simulé

Lorsque vous déployez la solution préconfigurée, vous devez configurer automatiquement les quatre périphériques exemple que vous pouvez voir dans la liste des périphériques. Ces périphériques sont *simulés appareils* exécutant dans un WebJob Azure. Appareils simulés facilitent l’expérimenter la solution préconfigurée sans avoir à déployer des périphériques réels, physiques. Si vous ne souhaitez pas vous connecter un périphérique réel à la solution, voir la [solution préconfigurée de surveillance se connecter votre périphérique au serveur distant] [ lnk-connect-rm] didacticiel.

Les étapes suivantes vous montrent comment ajouter un périphérique simulé à la solution :

1.  Accédez à la liste des périphériques.

2.  Cliquez sur **+ Ajouter un périphérique** dans le coin inférieur gauche pour ajouter un périphérique.

    ![Ajouter un périphérique à la solution préconfigurée][img-adddevice]

3.  Cliquez sur **Ajouter un nouveau** sur la vignette **Simulé à l’appareil** .

    ![Définir les détails de l’appareil dans le tableau de bord][img-addnew]
    
    Outre la création d’un nouvel appareil simulé, vous pouvez également ajouter un périphérique physique si vous choisissez de créer un **Périphérique personnalisé**. Pour plus d’informations sur la connexion de périphériques physiques à la solution, voir [se connecter votre appareil à la Suite IoT surveillance solution préconfigurée à distance][lnk-connect-rm].

4.  Sélectionnez **me laisser définir mon propre ID de périphérique**et entrez un nom de ID d’appareil unique tel que **mydevice_01**.

5.  Cliquez sur **créer**.

    ![Enregistrer un nouvel appareil][img-definedevice]

6. À l’étape 3 de **Ajouter un périphérique simulé**, cliquez sur **terminé** pour revenir à la liste des périphériques.

7. Vous pouvez afficher votre périphérique **en cours d’exécution** dans la liste des périphériques.

    ![Nouvel appareil de mode dans la liste des périphériques][img-runningnew]

8. Vous pouvez également consulter la télémétrie simulé à partir de votre nouvel appareil sur le tableau de bord :

    ![Affichage télémétrie de nouvel appareil][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Modifier les métadonnées de l’appareil

Lorsqu’un appareil connecté une première fois à la solution, il envoie ses métadonnées à la solution. Lorsque vous modifiez les métadonnées de l’appareil par le biais du tableau de bord solution, il envoie les nouvelles valeurs de métadonnées à l’appareil et stocke les nouvelles valeurs dans la base de données DocumentDB solution. Pour plus d’informations, voir [Registre d’identité appareil et DocumentDB][lnk-devicemetadata].

1.  Accédez à la liste des périphériques.

2.  Sélectionnez votre nouvel appareil dans la **Liste des périphériques**, puis cliquez sur **Modifier** pour modifier les **Propriétés de l’appareil**:

    ![Modifier les métadonnées d’appareil][img-editdevice]

3. Faites défiler vers le bas et apportez une modification à la rupture latitude et longitude. Cliquez sur **Enregistrer les modifications dans le Registre de périphérique**.

    ![Modifier les métadonnées d’appareil][img-editdevice2]

4. Revenir au tableau de bord, l’emplacement du périphérique a changé sur la carte :

    ![Modifier les métadonnées d’appareil][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Ajouter une règle pour le nouvel appareil

Il n’existe pas de règles pour le nouvel appareil que vous venez d’ajouter. Dans cette section, vous ajoutez une règle qui déclenche une alarme lorsque la température signalée par le nouvel appareil dépasse 47 degrés. Avant de commencer, notez que l’historique de télémétrie pour le nouvel appareil du tableau de bord affiche que la température appareil ne dépasse jamais 45 degrés.

1.  Accédez à la liste des périphériques.

2.  Sélectionnez votre nouvel appareil dans la **Liste des périphériques**, puis cliquez sur **Ajouter une règle** pour ajouter une règle pour l’appareil.

3. Créer une règle qui utilise la **température** en tant que le champ de données et utilise **AlarmTemp** comme la sortie lorsque la température dépasse degrés 47 :

    ![Ajouter une règle de périphériques][img-adddevicerule]

4. Cliquez sur **Enregistrer et afficher les règles** pour enregistrer vos modifications.

5.  Cliquez sur **commandes** dans le volet de détails appareil pour le nouvel appareil.

    ![Ajouter une règle de périphériques][img-adddevicerule2]

6.  Sélectionnez **ChangeSetPointTemp** dans la liste de commande et valeur **SetPointTemp** 45. Cliquez ensuite sur la **Commande Envoyer**:

    ![Ajouter une règle de périphériques][img-adddevicerule3]

7.  Accédez au tableau de bord solution. Après quelques instants, vous verrez une nouvelle entrée dans le volet **Historique alarme** lorsque la température signalée par votre nouvel appareil dépasse le seuil 47 degré :

    ![Ajouter une règle de périphériques][img-adddevicerule4]

8. Vous pouvez consulter et modifier toutes les règles dans la page **règles** du tableau de bord :

    ![Règles de liste d’appareil][img-rules]

9. Vous pouvez consulter et modifier toutes les actions qui peuvent être prises en réponse à une règle dans la page **Actions** du tableau de bord :

    ![Liste des actions sur les périphériques][img-actions]

> [AZURE.NOTE] Il est possible de définir des actions qui peuvent envoyer un message électronique ou SMS en réponse à une règle ou intégrer avec un système de métier via une [Application logique][lnk-logic-apps]. Pour plus d’informations, voir l' [application d’une logique de connecter votre Azure IoT Suite au contrôle à distance solution préconfigurée][lnk-logicapptutorial].

## <a name="other-features"></a>Autres fonctionnalités

À l’aide du portail de solution que vous pouvez effectuer une recherche pour les appareils dotés de caractéristiques spécifiques, par exemple un numéro de modèle :

![Recherchez un appareil][img-search]

Vous pouvez désactiver un périphérique, et lorsque celui-ci est désactivé vous pouvez la supprimer :

![Désactiver et supprimer un appareil][img-disable]

## <a name="behind-the-scenes"></a>En coulisses

Lorsque vous déployez une solution préconfigurée, le processus de déploiement crée plusieurs ressources dans l’abonnement Azure que vous avez sélectionné. Vous pouvez afficher ces ressources dans le [portail]Azure[lnk-portal]. Le processus de déploiement crée un **groupe de ressources** avec un nom basé sur le nom que vous choisissez pour votre solution préconfigurée :

![Solution préconfigurée dans le portail Azure][img-portal]

Vous pouvez afficher les paramètres de chaque ressource en la sélectionnant dans la liste des ressources dans le groupe de ressources.

Vous pouvez également afficher le code source de la solution préconfiguré. Le code source de solution préconfigurés contrôle à distance se trouve dans la [azure-iot-de surveillance à distance] [ lnk-rmgithub] GitHub référentiel :

- Le dossier **DeviceAdministration** contient le code source pour le tableau de bord.
- Le dossier **Simulator** contient le code source du périphérique simulé.
- Le dossier **EventProcessor** contient le code source pour le processus principale qui gère la télémétrie entrant.

Lorsque vous avez terminé, vous pouvez supprimer la solution préconfigurée de votre abonnement Azure sur [azureiotsuite.com] [ lnk-azureiotsuite] site. Ce site permet de supprimer facilement toutes les ressources qui ont été mis en service lorsque vous avez créé la solution préconfigurée.

> [AZURE.NOTE] Pour vous assurer que vous supprimez tout ce qui concerne la solution préconfigurée, supprimez-le sur [azureiotsuite.com] [ lnk-azureiotsuite] du site et ne supprimez pas simplement le groupe de ressources dans le portail.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé une solution de travail préconfiguré, vous pouvez continuer prise en main IoT Suite, lisez les articles suivants :

- [Contrôle à distance préconfiguré solution procédure pas à pas][lnk-rm-walkthrough]
- [Connectez votre appareil à la solution préconfigurée contrôle à distance][lnk-connect-rm]
- [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
