<properties
    pageTitle="Votre application de test de charge à l’aide de Visual Studio Team Services | Microsoft Azure"
    description="Découvrez comment souligner test de vos applications Azure Service tissu à l’aide de Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Votre application de test de charge à l’aide de Visual Studio Team Services

Cet article vous explique comment utiliser les fonctionnalités de test de charge de Microsoft Visual Studio pour souligner test une application. Il utilise un service dynamique Azure Service TISSU back-end et un service sans état web frontal. L’exemple d’application utilisée ici est un simulator emplacement avion. Vous fournir un ID avion, heure de départ et de destination. Partie principale de l’application traite les demandes et le serveur frontal affiche sur une carte l’avion répondant aux critères.

Le diagramme suivant illustre l’application de Service tissu que vous devez tester.

![Diagramme de l’application exemple avion emplacement][0]

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer, vous devez effectuer les opérations suivantes :

- Obtenir un compte Visual Studio Team Services. Vous pouvez en obtenir un gratuitement en [Visual Studio Team Services](https://www.visualstudio.com).
- Obtenir et installer Visual Studio 2013 ou Visual Studio 2015. Cet article utilise Visual Studio 2015 Enterprise edition, mais Visual Studio 2013 et autres versions devraient fonctionner de la même manière.
- Déployer votre application dans un environnement de test. Pour plus d’informations, voir [comment déployer des applications sur un cluster distant à l’aide de Visual Studio](service-fabric-publish-app-remote-cluster.md) .
- Comprendre le modèle d’utilisation de votre application. Ces informations sont utilisées pour simuler le modèle de charge.
- Comprendre l’objectif de votre test de charge. Cela vous aide à interpréter et analyser les résultats.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Créer et exécuter le projet de performances de site Web et Test de charge

### <a name="create-a-web-performance-and-load-test-project"></a>Créer un projet de Test de charge et les performances de site Web

1. Ouvrir Visual Studio 2015. Sélectionnez **fichier** > **Nouveau** > **projet** dans la barre de menus pour ouvrir la boîte de dialogue **Nouveau projet** .

2. Développez le nœud **Visual c#** , puis choisissez **Test** > **projet de Test de charge et de performances de site Web**. Donnez un nom au projet, puis sur le bouton **OK** .

    ![Capture d’écran de la boîte de dialogue Nouveau projet][1]

    Vous devez voir un nouveau projet de Test de charge et les performances de site Web dans l’Explorateur de solutions.

    ![Capture d’écran de l’Explorateur de solutions affichant le nouveau projet][2]

### <a name="record-a-web-performance-test"></a>Enregistrer un test de performances web

1. Ouvrez le projet .webtest.

2. Cliquez sur l’icône **Ajouter l’enregistrement** pour démarrer une session d’enregistrement dans votre navigateur.

    ![Capture d’écran de l’icône Ajouter un enregistrement dans un navigateur][3]

    ![Capture d’écran du bouton Enregistrer dans un navigateur][4]

3. Accédez à l’application de Service tissu. Le panneau de configuration de l’enregistrement doit afficher les requêtes web.

    ![Capture d’écran de requêtes web dans le panneau de configuration de l’enregistrement][5]

4. Exécuter une séquence d’actions que vous attendez les utilisateurs à effectuer. Ces actions sont utilisées comme un motif pour générer la charge.

5. Lorsque vous avez terminé, cliquez sur le bouton **Arrêter** pour arrêter l’enregistrement.

    ![Capture d’écran du bouton Arrêter][6]

    Le projet .webtest dans Visual Studio doit avoir capturé une série de requêtes. Les paramètres dynamiques sont remplacés automatiquement. À ce stade, vous pouvez supprimer les demandes de dépendance supplémentaire, répétées qui ne font pas partie de votre scénario de test.

6. Enregistrez le projet, puis choisissez la commande **Exécuter les tests** pour exécuter le test de performances web localement et vérifier que tout fonctionne correctement.

    ![Capture d’écran de la commande Exécuter le Test][7]

### <a name="parameterize-the-web-performance-test"></a>Paramétrer le test de performances web

Vous pouvez paramétrer le test de performances web en convertissant à un test de performances web codé, puis en modifiant le code. Comme alternative, vous pouvez lier le test de performances web à une liste de données afin que le test parcourt les données. Pour plus d’informations sur la façon de convertir le test de performances web à un test codé, voir [Générer et exécuter un test de performances web codé](https://msdn.microsoft.com/library/ms182552.aspx) . Voir [Ajouter une source de données à des performances de site web de test](https://msdn.microsoft.com/library/ms243142.aspx) pour savoir comment lier des données à un test de performances web.

Dans cet exemple, nous allons convertir le test de performances web à un test codé afin que vous pouvez remplacer l’ID de l’avion avec un GUID généré et ajouter d’autres requêtes pour envoyer des vols à différents emplacements.

### <a name="create-a-load-test-project"></a>Créer un projet de test de charge

Un projet de test de charge est composé d’un ou plusieurs scénarios décrits par le test de performances web et le test d’unité, ainsi que les paramètres de test de charge spécifié supplémentaire. Les étapes suivantes montrent comment créer un projet de test de charge :

1. Dans le menu contextuel de votre projet de Test de charge et les performances de site Web, cliquez sur **Ajouter** > **Test de charge**. Dans l’Assistant **Test de charge** , cliquez sur le bouton **suivant** pour configurer les paramètres de test.

2. Dans la section **Modèle de charge** , choisissez si vous souhaitez une charge utilisateur constante ou une charge par étape, qui commence par quelques utilisateurs et améliore les utilisateurs au fil du temps.

    Si vous avez une bonne estimation de la quantité de charge de l’utilisateur et que vous voulez voir fonctionnement du système en cours, cliquez sur **Charger constante**. Si votre objectif est de découvrir si le système effectue régulièrement sous différentes charges, choisissez **Étape charger**.

3. Dans la section de **La combinaison de tests** , cliquez sur le bouton **Ajouter** , puis sélectionnez le test que vous souhaitez inclure dans le test de charge. Vous pouvez utiliser la colonne de **Distribution** pour indiquer le pourcentage du nombre total de tests exécuter pour chaque test.

4. Dans la section **Paramètres d’exécution** , spécifiez la durée de test de charge.

    >[AZURE.NOTE] L’option **Itérations de Test** est disponible uniquement lorsque vous exécutez un test de charge localement à l’aide de Visual Studio.

5. Dans la section **emplacement** des **Paramètres d’exécution**, spécifiez l’emplacement dans lequel les requêtes de test de charge sont générés. L’Assistant peut vous inviter à se connecter à votre compte Team Services. Se connecter, puis choisissez un emplacement géographique. Lorsque vous avez terminé, cliquez sur **Terminer** .

6. Une fois le test de charge est créé, ouvrez le projet .loadtest, puis sélectionnez le paramètre, telles que **Les paramètres d’exécution**d’exécution actuel > **exécution1 [actifs]**. Les paramètres d’exécution dans la fenêtre **Propriétés** s’ouvre.

7. Dans la section **résultats** de la fenêtre de propriétés de **Paramètres d’exécution** , le paramètre de **Stockage des détails de minutage** doit comporter **Aucun** que sa valeur par défaut. Modifier cette valeur pour **Tous les détails individuels** pour obtenir plus d’informations sur la charge les résultats de test. Pour plus d’informations sur la façon de vous connecter à Visual Studio Team Services et l’exécution d’un test de charge, voir [Charger test](https://www.visualstudio.com/load-testing.aspx) .

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Exécuter le test de charge à l’aide de Visual Studio Team Services

Sélectionner la commande **Exécuter le Test de charge** pour démarrer le test exécuter.

![Capture d’écran de la commande Exécuter les tests de charge][8]

## <a name="view-and-analyze-the-load-test-results"></a>Afficher et analyser les résultats de test de charge

Comme la charge de test est effectué, les informations de performance à représenter graphiquement. Vous devriez voir doit ressembler au graphique suivant.

![Capture d’écran du graphique des performances pour charger les résultats des tests][9]

1. Cliquez sur le lien **Télécharger le rapport** dans la partie supérieure de la page. Après avoir téléchargé le rapport, cliquez sur le bouton **Afficher le rapport** .

    Sous l’onglet **graphique** , vous pouvez voir graphiques pour plusieurs compteurs de performances. Sous l’onglet **Résumé** , les résultats de test générale apparaissent. L’onglet **Tables** affiche le nombre total de tests de charge réussites et les échecs.

2. Sélectionnez les liens de numéros sur le **Test** > **Échec** et les **erreurs** > **nombre** de colonnes pour afficher les détails de l’erreur.

    L’onglet **Détails** affiche virtuelles informations de scénario utilisateur et test de demandes a échoué. Ces données peuvent être utiles si le test de charge inclut plusieurs scénarios.

Pour plus d’informations sur l’affichage des résultats de test de charge, voir [Analyse de charger les résultats des tests dans la vue graphiques de l’Analyseur de Test de charge](https://www.visualstudio.com/load-testing.aspx) .

## <a name="automate-your-load-test"></a>Automatiser votre test de charge

Visual Studio Team Services charger Test fournit des API pour vous aider à gérer les tests de charge et analyser les résultats dans un compte Team Services. Pour plus d’informations, consultez [Cloud charge test reste API](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Étapes suivantes
- [Surveillance et diagnostic services dans une configuration de développement ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
