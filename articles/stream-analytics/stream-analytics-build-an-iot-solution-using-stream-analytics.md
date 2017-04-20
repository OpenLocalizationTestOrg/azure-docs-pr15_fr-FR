<properties
    pageTitle="Créer une solution IoT à l’aide de flux de données Analytique | Microsoft Azure"
    description="Didacticiel mise en route de la solution flux Analytique IoT d’un scénario péage"
    keywords="solution IOT, fonctions de la fenêtre"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Créer une solution IoT à l’aide de flux de données Analytique

## <a name="introduction"></a>Introduction

Dans ce didacticiel, vous allez apprendre à utiliser Azure flux Analytique pour obtenir des informations en temps réel à partir de vos données. Les développeurs peuvent facilement combiner des flux de données, tels que des interactions utilisateur sur les journaux et événements générés par l’appareil, avec les enregistrements historiques ou les données de référence pour obtenir des informations d’entreprise. En tant que flux de données en temps réel, entièrement géré calcul service hébergé dans Microsoft Azure, Azure flux Analytique fournit la résilience intégrée, faible latence et extensibilité élevées pour obtenir opérationnel en quelques minutes.

Au terme de ce didacticiel, vous pourrez :

-   Familiarisez-vous avec le portail Azure flux Analytique.
-   Configurer et déployer un travail de diffusion en continu.
-   Expliquer les problèmes réels et de les résoudre à l’aide du langage de requête Analytique de flux de données.
-   Développer la diffusion en continu de solutions pour vos clients à l’aide de flux de données Analytique en toute sécurité.
-   Utilisez la surveillance et la journalisation expérience pour résoudre les problèmes.

## <a name="prerequisites"></a>Conditions préalables

Vous devez les composants requis suivants pour effectuer ce didacticiel :

-   La dernière version [d’Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 ou la [Communauté de Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) gratuit
-   Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilèges d’administrateur sur l’ordinateur
-   Téléchargement de [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) à partir du centre de téléchargement Microsoft
-   Facultatif : Code Source pour le Générateur d’événement TollApp dans [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Présentation de scénario : « Bonjour, numéro payant ! »


Un poste numéro payant est un phénomène courantes. Vous rencontrez les nombreux celle, ponts et tunnel dans le monde entier. Chaque console payants comporte plusieurs stands payant. À partir de stands manuels, vous arrêtez pour payer le numéro payant pour un standard. À stands automatisés, un capteur en haut de chaque booth analyse une carte RFID qui est placée sur le pare-brise de votre véhicule lorsque vous passez le stand payant. Il est facile de visualiser le passage des véhicules à travers ces postes payants comme un flux d’événements pendant laquelle les opérations intéressantes peuvent être effectuées.

![Image de voitures en stands payants](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Données entrantes

Ce didacticiel fonctionne avec deux flux de données. Capteurs installés dans l’ouverture et de fermeture des postes payants de produisent le premier flux. Le deuxième flux est un jeu de données de recherche statiques qui contient les données de l’enregistrement d’un véhicule.

### <a name="entry-data-stream"></a>Flux d’entrée de données

Le flux de données d’entrée contient des informations sur voitures lorsqu’ils entrent des postes payant.


| TollID | EntryTime               | LicensePlate | État | Rendre   | Modèle   | VehicleType | VehicleWeight | Numéro payant | Balise       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 12:01:00.000 2014-09-10 | JNB 7001     | NY    | Honda  | COURBE     | 1           | 0             | 7    |           |
| 1      | 12:02:00.000 2014-09-10 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 12:02:00.000 2014-09-10 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 12:03:00.000 2014-09-10 | XYZ 1003     | CT    | Toyota | Pétales | 1           | 0             | 4    |           |
| 1      | 12:03:00.000 2014-09-10 | BNJ 1007     | NY    | Honda  | COURBE     | 1           | 0             | 5    | 789123456 |
| 2      | 12:05:00.000 2014-09-10 | CDE 1007     | NJ    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Voici une brève description des colonnes :

| Colonne | Description |
|--------------|--------------------------------------------------------------------|
| TollID       | L’ID de stand payant qui identifie un stand payants            |
| EntryTime    | La date et l’heure de l’entrée du véhicule pour le stand payants UTC |
| LicensePlate | Le nombre de licences plaque de véhicule                            |
| État        | Un état des États-Unis                                           |
| Rendre         | Le fabricant de la voiture                                 |
| Modèle        | Numéro de modèle de la voiture                                 |
| VehicleType  | 1 pour les véhicules ou 2 pour véhicules       |
| WeightType   | Poids d’un véhicule en tonnes ; 0 pour voitures                   |
| Numéro payant         | La valeur de numéro payant en euros                                              |
| Balise          | E-sur la balise voiture permettant d’automatiser paiement ; vide dans laquelle le remboursement effectué manuellement |


### <a name="exit-data-stream"></a>Flux de données de sortie

Le flux de données de sortie contient des informations sur course quitter la console payants.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

Voici une brève description des colonnes :


| Colonne | Description |
|--------------|-----------------------------------------------------------------|
| TollID       | L’ID de stand payants qui identifie un stand payants         |
| ExitTime     | La date et l’heure de sortie du véhicule booth payant au format UTC |
| LicensePlate | Le nombre de licences plaque de véhicule                         |

### <a name="commercial-vehicle-registration-data"></a>Données d’inscription véhicule commerciale

Le didacticiel utilise un instantané d’une base de données de l’enregistrement de véhicule utilitaire statique.


| LicensePlate | RegistrationId | Expiré |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| COÛT DE RÉFÉRENCE 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Voici une brève description des colonnes :


| Colonne | Description |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Le nombre de licences plaque de véhicule                         |
| RegistrationId     | ID de l’enregistrement du véhicule |
| Expiré | L’état de l’inscription du véhicule : 0 si l’inscription d’un véhicule est active, 1 si l’abonnement a expiré                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurer l’environnement pour Azure flux Analytique

Pour effectuer ce didacticiel, vous avez besoin d’un abonnement Microsoft Azure. Microsoft propose une version d’évaluation gratuite pour les services Microsoft Azure.

Si vous n’avez pas un compte Azure, vous pouvez [demander une version d’évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Pour vous inscrire pour une version d’évaluation gratuite, vous avez besoin d’un appareil mobile doit peut recevoir des messages texte et une carte de crédit valide.

Veillez à suivre les étapes décrites dans la section « Nettoyer votre compte Azure » à la fin de cet article afin que vous puissiez effectuer le meilleur parti de votre 200 € libre crédit Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Configurer les ressources Azure requis pour le didacticiel

Ce didacticiel requiert deux hubs événement pour recevoir des flux de données *d’entrée* et *Quitter* . Base de données SQL Azure renvoie les résultats des tâches de flux de données Analytique. Stockage Azure stocke les données de référence sur les enregistrements d’un véhicule.

Vous pouvez utiliser le script Setup.ps1 dans le dossier TollApp sur GitHub pour créer toutes les ressources requises. Pour des raisons de temps, nous vous recommandons de que vous l’exécuter. Si vous voulez en savoir plus sur la configuration de ces ressources dans le portail Azure, consultez l’annexe « Configuration des ressources didacticiels portail Azure ».

Téléchargez et enregistrez les fichiers et le dossier [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) prise en charge.

Ouvrir un **Microsoft Azure PowerShell** fenêtre _en tant qu’administrateur_. Si vous n’avez pas encore Azure PowerShell, suivez les instructions dans [installer et configurer Azure PowerShell](../powershell-install-configure.md) pour l’installer.

Étant donné que Windows bloque automatiquement .ps1, .dll et les fichiers .exe, vous devez définir la stratégie d’exécution avant d’exécuter le script. Vérifiez que la fenêtre PowerShell Azure s’exécute _en tant qu’administrateur_. Exécutez **Set-ExecutionPolicy illimité**. Lorsque vous y êtes invité, tapez **O**.

![Capture d’écran de « Set-ExecutionPolicy illimité » en cours d’exécution dans la fenêtre PowerShell Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Exécutez **Get-ExecutionPolicy** pour vous assurer que la commande travaillé.

![Capture d’écran de « Get-ExecutionPolicy » en cours d’exécution dans la fenêtre PowerShell Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Accédez au répertoire qui comporte les scripts et les applications générateur.

![Capture d’écran de « cd .\TollApp\TollApp » en cours d’exécution dans la fenêtre PowerShell Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Type de **.\\ Setup.ps1** pour configurer votre compte Azure, créer et configurer les ressources obligatoires et commencer à générer des événements. Le script capte aléatoirement une région pour créer vos ressources. Pour spécifier explicitement une région, vous pouvez passer le **-emplacement** paramètre comme dans l’exemple suivant :

**. \\Setup.ps1-emplacement « US centrale »**

![Capture d’écran de la page de connexion Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Le script ouvre la page **Se connecter** à Microsoft Azure. Entrez vos informations d’identification de compte.

> [AZURE.NOTE] Si votre compte a accès à plusieurs abonnements, vous devrez entrer le nom de l’abonnement que vous souhaitez utiliser pour le didacticiel.

Le script peut prendre plusieurs minutes. Une fois terminé, le résultat doit ressembler à la capture d’écran suivante.

![Capture d’écran de sortie du script dans la fenêtre PowerShell Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Vous verrez également une autre fenêtre similaire à la capture d’écran suivante. Cette application envoie des événements aux Hubs Azure événement, qui est requis pour exécuter le didacticiel. Par conséquent, n’arrêter l’application ou fermez cette fenêtre jusqu'à ce que vous avez terminé le didacticiel.

![Capture d’écran de « L’envoi événement concentrateur de données »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Vous devriez pouvoir afficher toutes les ressources créées dans Azure portal maintenant. Accédez à <https://manage.windowsazure.com>et connectez-vous à l’aide de vos informations d’identification de compte.

### <a name="azure-event-hubs"></a>Événement Azure Hubs

Cliquez sur **BUS des services** sur le côté gauche du portail Azure pour voir hubs événement que le script créé dans la section précédente.

![Bus des services](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Vous verrez tous les espaces de noms disponibles dans votre abonnement. Cliquez sur l’option qui commence par *tolldata* (tolldata4637388511 dans notre exemple). Cliquez sur l’onglet **Événement HUBS** .

![Onglet de Hubs événement dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Vous verrez deux hubs événement *entrée* nommées et *Quitter* créé dans cet espace de noms.

![Capture d’écran de hubs événement « entrée » et « fermeture » dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Conteneur de stockage Azure

1. Cliquez sur le **stockage** sur le côté gauche du portail Azure pour afficher le conteneur de stockage Azure qui est utilisé dans le didacticiel.

    ![Élément de menu de stockage](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Cliquez sur l’option qui commencent par *tolldata* (tolldata4637388511 dans notre exemple). Cliquez sur l’onglet **conteneurs** pour voir le conteneur créé.

    ![Onglet conteneurs dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Cliquez sur le conteneur **tolldata** pour afficher le fichier téléchargé JSON qui contient les données de l’enregistrement d’un véhicule.

    ![Capture d’écran du fichier registration.json dans le conteneur](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Base de données SQL Azure

1. Cliquez sur **Bases de données SQL** sur le côté gauche du portail Azure pour afficher la base de données SQL qui est utilisé dans le didacticiel.

    ![Bases de données SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Cliquez sur **tolldatadb**.

    ![Capture d’écran de la base de données SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copier le nom du serveur sans le numéro de port (*nom du serveur*. database.windows.net, par exemple).

## <a name="connect-to-the-database-from-visual-studio"></a>Connectez-vous à la base de données à partir de Visual Studio

Utilisez Visual Studio pour accéder aux résultats de la requête dans la base de données de sortie.

Connectez-vous à la base de données SQL (la destination) à partir de Visual Studio :

1. Ouvrir Visual Studio, puis cliquez sur **Outils** > **se connecter à la base de données**.

2. Si vous êtes invité, cliquez sur **Microsoft SQL Server** comme source de données.

    ![Boîte de dialogue Modifier Source de données](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. Dans le champ **nom du serveur** , collez le nom que vous avez copiée dans la section précédente à partir du portail Azure (autrement dit, le *nom du serveur*. database.windows.net).

4. Cliquez sur **utiliser l’authentification SQL Server**.

5. Entrez **tolladmin** dans le champ **nom d’utilisateur** et **123toll !** dans le champ **mot de passe** .

6. Cliquez sur **Sélectionner ou entrez un nom de base de données**, puis sélectionnez **TollDataDB** en tant que la base de données.

    ![Ajouter la boîte de dialogue Connexion](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Cliquez sur **OK**.

8. Ouvrez l’Explorateur de serveur.

    ![Explorateur de serveurs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Voir quatre tables dans la base de données TollDataDB.

    ![Tables dans la base de données TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Générateur d’événement : TollApp exemple de projet

Le script PowerShell commence automatiquement à envoyer des événements à l’aide du programme d’application exemple TollApp. Vous n’avez pas besoin d’effectuer des étapes supplémentaires.

Toutefois, si vous êtes intéressé détails d’implémentation, vous trouverez le code source de l’application TollApp dans GitHub [Exemples/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Capture d’écran du code exemple affiché dans Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

1. Dans le portail Azure, ouvrez Analytique flux et cliquez sur **Nouveau** dans le coin inférieur gauche de la page pour créer une nouvelle tâche analytique.

    ![Bouton Nouveau](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Cliquez sur **créer rapide**. Sélectionnez la même région où vos autres ressources sont créés par le script.

3. Pour que le paramètre de **Compte de stockage de surveillance régionales** , sélectionnez **Créer un nouveau compte de stockage** et lui attribuer un nom unique. Azure flux Analytique utilise ce compte pour stocker les informations d’analyse pour toutes vos opérations futures.

4. Cliquez sur **Créer un flux de données d’analyse de travail** dans la partie inférieure de la page.

    ![Option flux Analytique travail créer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Définissez des sources d’entrée

1. Cliquez sur la tâche analytique créée dans le portail.

    ![Capture d’écran de la tâche analytique dans le portail](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Cliquez sur l’onglet **entrées** pour définir la source de données.

    ![L’onglet entrées](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Cliquez sur **Ajouter une entrée**.

    ![Ajouter une option d’entrée](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Cliquez sur **flux de données** sur la première page.

    ![L’option de flux de données](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Sur la deuxième page de l’Assistant, cliquez sur **Événement concentrateur** .

    ![L’option événement concentrateur](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Entrez **EntryStream** comme **ALIAS d’entrée**.

7. Cliquez sur le menu déroulant **Concentrateur de l’événement** , puis sélectionnez celui qui commence par « TollData » (par exemple, TollData9518658221).

8. Sélectionnez **entrée** comme nom de concentrateur d’événements et **tout** en tant que le nom de la stratégie concentrateur événement.

    Vos paramètres seront présente comme :

    ![Paramètres de concentrateur d’événement](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. Dans la page suivante, sélectionnez **JSON** pour **Événement sérialisation FORMAT** et **UTF8** pour **codage**.

    ![Paramètres de sérialisation](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Cliquez sur **OK** au bas de la page pour terminer l’Assistant.

    ![Capture d’écran de EntryStream entrée dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    À présent que vous avez créé le flux d’entrée, vous sera suivre les mêmes étapes pour créer le flux de sortie. Dans la troisième page de l’Assistant, veillez à entrer des valeurs en tant que sur l’écran suivant.

    ![Paramètres pour le flux de sortie](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Vous avez défini deux flux d’entrée :

    ![Flux d’entrée définis dans le portail Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    Ensuite, vous allez ajouter entrée de données de référence pour le fichier blob qui contient des données d’inscription voiture.

11. Cliquez sur **Ajouter une entrée**, puis cliquez sur **Données de référence**.

    ![Options « Ajouter une entrée » avec les données de référence sélectionnées](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Dans la page suivante, sélectionnez le compte de stockage qui commence par **tolldata**. Le nom du conteneur doit être **tolldata**, et le nom d’objets blob sous **Motif chemin d’accès** doit être **registration.json**. Ce nom de fichier respecte la casse et doit être en minuscules.

    ![Paramètres de stockage de blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Sélectionnez les valeurs comme indiqué dans l’écran suivant dans la page suivante, puis cliquez sur **OK** pour fermer l’Assistant.

    ![Sélection de JSON pour « format sérialisation même » et UTF8 pour « Codage »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

À présent toutes les entrées sont définies.

![Capture d’écran des entrées définies trois](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Définir la sortie

1. Cliquez sur l’onglet **sortie** , puis sur **Ajouter un résultat**.

    ![L’option « Ajouter une sortie » et un onglet sortie](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Cliquez sur **base de données SQL**.

3. Sélectionnez le nom du serveur qui a été utilisé dans la section « Se connecter à base de données à partir de Visual Studio » de l’article. Le nom de la base de données est **TollDataDB**.

4. Entrez **tolladmin** dans le champ **nom d’utilisateur** , **123toll !** dans le champ **mot de passe** et **TollDataRefJoin** dans le champ de **TABLE** .

    ![Paramètres de base de données SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Azure requête analytique de flux de données

L’onglet **requête** contient une requête SQL qui convertit les données entrantes.

![Une requête ajoutée à l’onglet requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Ce didacticiel tente de répondre à plusieurs questions professionnels associés aux payant constructions et données requêtes de flux Analytique qui peuvent être utilisés dans Azure flux Analytique pour fournir une réponse pertinente.

Avant de commencer votre première tâche de flux de données Analytique, Découvrons quelques scénarios et la syntaxe de la requête.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Présentation de langage de requête Analytique de flux de données Azure
-----------------------------------------------------

Supposons que vous avez besoin de compter le nombre de véhicules qui permet d’entrer un stand payant. Comme il s’agit d’un flux continu d’événements, vous devez définir une « période de temps ». Nous allons modifier la question pour être « véhicules combien permet d’entrer un stand payants toutes les trois minutes ? ». Couramment appelé le nombre qui s’écroulent.

Examinons la requête Azure flux Analytique qui répond à cette question :

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Comme vous pouvez le voir, Azure flux Analytique utilise un langage de requête comme SQL et ajoute quelques extensions pour spécifier les aspects liés à l’heure de la requête.

Pour plus d’informations, consultez [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et [fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) constructions utilisées dans la requête à partir de MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Test de requêtes Azure flux Analytique

À présent que vous avez écrit votre première requête Analytique de flux de données Azure, il est temps à tester à l’aide de fichiers d’exemples de données dans votre dossier TollApp à l’emplacement suivant :

**.. \\TollApp\\TollApp\\données**

Ce dossier contient les fichiers suivants :

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Question 1 : Nombre de véhicules entrer un stand payants

1. Ouvrez le portail Azure et accédez à votre travail Azure flux Analytique créé. Cliquez sur l’onglet **requête** et collez la requête à partir de la section précédente.

    ![Requête collé dans l’onglet requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Pour valider cette requête contre les exemples de données, cliquez sur le bouton de **Test** . Dans la boîte de dialogue qui s’ouvre, accédez à Entry.json, un fichier contenant des exemples de données à partir du flux d’événement **EntryTime** .

    ![Capture d’écran du fichier Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Vérifier que le résultat de la requête est normalement :

    ![Résultats du test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Question 2 : Déclarer des heures total pour chaque voiture à passer par le stand payants

La durée moyenne nécessaire pour une voiture à passer par le numéro payant vous aident à évaluer l’efficacité du processus et de l’expérience client.

Pour rechercher la durée totale, vous devez joindre le flux EntryTime avec le flux ExitTime. Vous allez joindre les flux de données sur les colonnes TollId et LicencePlate. L’opérateur **JOIN** , vous devez spécifier suffisamment temporel décrivant la différence de temps acceptable entre les événements joints. Fonction **DATEDIFF** vous permet de spécifier que les événements doivent être pas plus de 15 minutes entre eux. Vous s’appliquent également la fonction **DATEDIFF** pour quitter et heures de saisie pour calculer le temps effectif qu’une voiture passe en le poste numéro payant. Notez la différence de l’utilisation de **DATEDIFF** lorsqu’elle est utilisée dans une instruction **SELECT** au lieu d’une condition de **jointure** .

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Pour tester cette requête, mettre à jour la requête sous l’onglet **requête** de votre travail :

    ![Requête mise à jour dans l’onglet requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Cliquez sur **tester** et spécifiez les fichiers d’exemples d’entrée de EntryTime et ExitTime.

    ![Capture d’écran de fichiers d’entrée sélectionnés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Activez la case à cocher pour tester la requête et afficher le résultat :

    ![Résultat du test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Question 3 : Signaler tous les véhicules avec l’enregistrement qui a expiré

Azure flux Analytique permet de participer avec des flux de données temporelles des instantanés statiques des données. Pour illustrer cette fonctionnalité, utilisez la question suivante exemple.

Si un véhicule utilitaire est enregistré avec l’entreprise numéro payant, il peut passer via le stand payants sans être arrêté pour le contrôle. Vous allez utiliser la table de choix véhicule utilitaire d’inscription pour identifier la totalité des véhicules commerciaux qui ont expiré des enregistrements.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Pour tester une requête à l’aide de données de référence, vous devez définir une source d’entrée pour les données de référence, vous avez déjà fait.

1. Pour tester cette requête, collez la requête dans l’onglet **requête** , cliquez sur **tester**et spécifiez les deux sources d’entrée :

    ![Capture d’écran de fichiers d’entrée sélectionnés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Afficher les résultats de la requête :

    ![Capture d’écran du résultat de la requête](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Démarrer la tâche de flux de données Analytique


Maintenant, il est temps pour terminer la configuration et démarrer la tâche. Enregistrer la requête à partir de la Question 3, qui produira un résultat qui correspond au schéma de la table de sortie **TollDataRefJoin** .

Accédez à la tâche de **tableau de bord**, puis cliquez sur **Démarrer**.

![Capture d’écran du bouton Démarrer dans le tableau de bord de travail](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Dans la boîte de dialogue qui s’ouvre, modifier l’heure de **Sortie démarrer** heure **personnalisés**. Modifier l’heure à une heure avant l’heure actuelle. Cette modification garantit que tous les événements à partir du hub événement sont traités dans la mesure où vous avez commencé à générer les événements au début de ce didacticiel. Maintenant, cliquez sur la case à cocher pour démarrer le travail.

![Sélection d’heure personnalisés](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Démarrage de la tâche peut prendre quelques minutes. Vous pouvez voir l’état dans la page de niveau supérieur pour flux Analytique.

![Capture d’écran de l’état de la tâche](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Vérifier les résultats dans Visual Studio

1. Ouvrez l’Explorateur de serveur Visual Studio et avec le bouton droit de la table **TollDataRefJoin** .
2. Cliquez sur **Afficher les données de Table** pour voir les résultats de votre travail.

    ![Sélection de « Afficher des données de Table » dans l’Explorateur de serveurs](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Azure flux Analytique évoluer des tâches

Azure Analytique de flux de données est conçue pour ELASTIQUEMENT échelle afin qu’il puisse gérer un grand nombre de données. La requête Azure flux Analytique peut utiliser une clause **PARTITION BY** pour indiquer le système qui sera évoluer cette étape. **IDPartition** est une colonne spéciale qui ajoute le système pour correspondre à l’ID de partition de l’entrée (concentrateur événement).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Arrêter la tâche en cours, la requête dans l’onglet **requête** mise à jour et ouvrez l’onglet **échelle** .

    **Unités de diffusion en continu** définissez la quantité de puissance de calcul qui la tâche peut recevoir.

2. Déplacez le curseur à 6.

    ![Capture d’écran de sélection de 6 unités de diffusion en continu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Accédez à l’onglet **des sorties** de changer le nom de la table SQL pour **TollDataTumblingCountPartitioned**.

À présent, si vous démarrez le travail Azure flux Analytique pouvez distribuer le travail sur davantage de ressources cluster et obtenir de meilleurs résultats. Veuillez noter que l’application TollApp envoie également des événements partitionnées par TollId.

## <a name="monitor"></a>Moniteur

L’onglet **Moniteur** contient des statistiques relatives à la tâche en cours d’exécution.

![Capture d’écran des statistiques sur l’exécution de tâches](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Vous pouvez accéder **Journaux d’opération** à partir de l’onglet **tableau de bord** .

![L’option « Opération journaux »](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Capture d’écran des journaux d’opération où vous pouvez consulter l’état des tâches](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Pour afficher plus d’informations sur un événement particulier, cliquez sur l’événement, puis cliquez sur le bouton **Détails** .

![Capture d’écran de détails sur un événement sélectionné](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusion

Ce didacticiel présenté le service Azure flux Analytique. Il montre comment configurer les entrées et les sorties pour la tâche de flux de données Analytique. Le didacticiel utilise le scénario de données payants, expliqués types communs de situer dans l’espace des données mouvement et comment ils peuvent être résolus avec simples requêtes SQL dans Azure flux Analytique. Le didacticiel décrites constructions d’extension SQL pour l’utilisation de données temporelles. Il a montré comment participer à des flux de données, comment enrichir le flux de données avec les données de référence statique et explique comment faire évoluer une requête pour obtenir un débit plus élevé.

Bien que ce didacticiel fournit une bonne introduction, il n’est pas terminée par tout moyen. Vous pouvez trouver plusieurs modèles de requête en utilisant le langage SAQL en [exemples de requête pour des modèles de l’utilisation Analytique des flux communs](stream-analytics-stream-analytics-query-patterns.md).
Consultez la [documentation en ligne](https://azure.microsoft.com/documentation/services/stream-analytics/) pour en savoir plus sur Azure flux Analytique.

## <a name="clean-up-your-azure-account"></a>Nettoyer votre compte Azure

1. Arrêter la tâche de flux Analytique dans le portail Azure.

    Le script Setup.ps1 crée deux hubs événement et une base de données SQL. Suivez les instructions ci-dessous vous aident à nettoyer des ressources à la fin du didacticiel.

2. Dans une fenêtre PowerShell, tapez **.\\ CleanUp.ps1** pour démarrer le script de suppression des ressources utilisées dans le didacticiel.

    > [AZURE.NOTE] Ressources identifiées par le nom. Vérifiez que vous examinez avec soin chaque élément avant de confirmer la suppression.

    ![Capture d’écran du processus de nettoyage de disque](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
