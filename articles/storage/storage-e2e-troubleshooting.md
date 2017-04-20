<properties
    pageTitle="Dépannage à l’aide de l’Analyseur de Message métriques de stockage Azure et journalisation et AzCopy bout | Microsoft Azure"
    description="Un didacticiel illustrant la résolution des problèmes de bout en bout avec Azure stockage Analytique, AzCopy et Analyseur de Message de Microsoft"
    services="storage"
    documentationCenter="dotnet"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Résolution des problèmes de bout en bout à l’aide de métriques de stockage Azure et journalisation, AzCopy et Analyseur de Message

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Vue d’ensemble

Diagnostic et résolution des problèmes sont essentielle pour la création et la prise en charge des applications clientes avec Microsoft Azure stockage. En raison de la nature distribuée d’une application Azure, diagnostic et résolution des erreurs et problèmes de performances peuvent être plus complexes que dans les environnements traditionnels.

Dans ce didacticiel, vous allez apprendre à identifier client certaines erreurs qui peuvent affecter les performances et résoudre les problèmes de ces erreurs de bout en bout à l’aide des outils fournis par Microsoft et le stockage Azure, afin d’optimiser l’application cliente.

Ce didacticiel fournit une exploration pratique d’un scénario de dépannage de bout en bout. Pour un guide détaillé conceptuel pour la résolution des problèmes d’applications de stockage Azure, voir [moniteur, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Outils de résolution des applications de stockage Azure

Pour résoudre les applications clientes à l’aide de Microsoft Azure stockage, vous pouvez utiliser une combinaison des outils pour déterminer si un problème s’est produit et quelle peut être la cause du problème. Ces outils incluent :

- **Stockage azure Analytique**. [Azure stockage Analytique](http://msdn.microsoft.com/library/azure/hh343270.aspx) fournit des métriques et journalisation pour le stockage Azure.
    - **Métriques de stockage** effectue le suivi de métriques de transactions et d’indicateurs de capacité pour votre compte de stockage. À l’aide d’indicateurs, vous pouvez déterminer comment votre application s’exécute en fonction d’une variété de différentes mesures. Pour plus d’informations sur les types d’indicateurs de suivi de stockage Analytique, consultez [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx) .

    - **Stockage journalisation** consigne chaque demande dans les services de stockage Azure dans un journal côté serveur. Le journal contient des données détaillées de chaque demande, y compris l’opération, l’état de l’opération et les informations de latence. Pour plus d’informations sur les données de demande et réponse sont écrites dans les journaux par stockage Analytique, voir [Format de stockage du journal Analytique](http://msdn.microsoft.com/library/azure/hh343259.aspx) .

> [AZURE.NOTE] Comptes de stockage à un type de réplication de stockage Zone redondantes (ZRS) n’ont pas la métriques ou la fonction de journalisation pour le moment. 

- **Portail azure**. Vous pouvez configurer les mesures et journalisation pour votre compte de stockage sur le [Portail Azure](https://portal.azure.com). Vous pouvez également afficher des graphiques montrant les performances de votre application dans le temps et configurer des alertes pour être averti si votre application effectue pas comme prévu pour une mesure spécifique.

    Pour plus d’informations sur la configuration de l’analyse dans le portail Azure, voir [moniteur un compte de stockage dans le portail Azure](storage-monitor-storage-account.md) .

- **AzCopy**. Journaux du serveur pour le stockage Azure sont stockées sous forme d’objets BLOB, afin d’utiliser AzCopy pour copier les objets BLOB journal dans un répertoire local pour l’analyse de l’Analyseur de Message de Microsoft. Pour plus d’informations sur AzCopy, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) .

- **Analyseur de Message de Microsoft**. Analyseur de message est un outil qui utilise les fichiers journaux et affiche les données du journal dans un format visuel qui facilite filtre, recherche et regrouper des données de journal en ensembles utiles que vous pouvez utiliser pour analyser les erreurs et problèmes de performances. Voir [Microsoft Message analyseur d’exploitation Guide](http://technet.microsoft.com/library/jj649776.aspx) pour plus d’informations sur l’Analyseur de Message.

## <a name="about-the-sample-scenario"></a>À propos de l’exemple de scénario

Pour ce didacticiel, nous allons examiner un scénario où métriques de stockage Azure indique un taux de réussite pourcentage faible pour une application qui appelle stockage Azure. La mesure de taux faible succès pourcentage (indiquée comme **PercentSuccess** dans le [Portail Azure](https://portal.azure.com) et dans les tableaux métriques) suit opérations que réussir, mais qui retournent un code d’état HTTP qui est supérieur à 299. Ces opérations sont enregistrées dans les fichiers journaux de stockage côté serveur, avec un statut de **ClientOtherErrors**transaction. Pour plus d’informations sur la métrique succès pourcentage faible, voir [Afficher les mesures PercentSuccess basse ou entrées de journal analytique avoir opérations statut de ClientOtherErrors transaction](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Opérations de stockage Azure peuvent renvoyer les codes d’état HTTP supérieur à 299 dans le cadre de leurs fonctionnalités normale. Mais dans certains cas, elles indiquent que vous pourrez peut-être optimiser votre application cliente pour améliorer les performances.

Dans ce scénario, nous allons étudier un taux de réussite pourcentage faible sur autre chose inférieure à 100 %. Vous pouvez choisir un autre niveau métrique, cependant, en fonction de vos besoins. Nous vous recommandons que pendant le test de votre application, vous établissez une tolérance de référence pour vos indicateurs de performance clés. Par exemple, vous pouvez déterminer, basé sur un test, que votre application doit avoir un taux de réussite pourcentage cohérentes de 90 %, soit 85 %. Si vos données métriques indiquent que l’application est entraînerait ce numéro, puis vous pouvez examiner la cause l’augmentation.

Pour le scénario de notre exemple, une fois que nous avons établi que la mesure de taux de réussite pourcentage est inférieur à 100 %, nous allons examiner les journaux pour trouver les erreurs qui correspondent aux mesures et les utiliser pour identifier le d'où vient le taux de réussite pourcentage inférieur. Nous allons examiner spécifiquement sur les erreurs dans la plage 400. Puis nous allons étudier plus près 404 erreurs (introuvable).

### <a name="some-causes-of-400-range-errors"></a>Causes fréquentes des messages d’erreur 400-plage

Les exemples ci-dessous montre quelques exemples d’erreurs 400-plage pour demandes auprès de stockage d’objets Blob Azure et leurs causes possibles. Un de ces erreurs, ainsi que les erreurs dans la plage 300 et la plage 500, peut contribuer à un taux de réussite pourcentage faible.

Notez que les listes ci-dessous sont loin d’être terminée. Consultez [l’état et Codes d’erreur](http://msdn.microsoft.com/library/azure/dd179382.aspx) sur MSDN pour plus d’informations sur les erreurs de stockage Azure générales et à propos des erreurs spécifiques à chacun des services de stockage.

**Exemples de Code 404 (non trouvée) d’état**

Se produit lorsqu’une opération de lecture par rapport à un conteneur ou blob échoue, car le blob ou le conteneur n’est pas trouvée.

- Se produit si un conteneur ou blob a été supprimé par un autre client avant de cette demande.
- Se produit si vous utilisez un appel API qui crée le conteneur ou blob après l’archivage s’il existe. Les CreateIfNotExists APIs passer un appel de tête tout d’abord pour vérifier l’existence du conteneur ou blob ; Si elle n’existe pas, une erreur 404 est renvoyée, et puis un deuxième place appel est effectué pour écrire le conteneur ou blob.

**Code d’état de 409 (conflit) Exemples**

- Se produit si vous utilisez une API créer pour créer un nouveau conteneur ou blob, sans vérification de l’existence tout d’abord, et un conteneur ou blob portant le même nom existe déjà.
- Se produit si un conteneur est supprimé, et que vous tentez de créer un nouveau conteneur portant le même nom avant de l’opération de suppression est terminée.
- Se produit si vous spécifiez une location sur un conteneur ou blob, et il existe déjà une location présente.

**Code d’état 412 (préalable a échoué) Exemples**

- Se produit lorsque la condition spécifiée par un en-tête conditionnel n’a pas été atteint.
- Se produit lorsque l’ID de location spécifié ne correspond pas à l’ID de location sur le conteneur ou blob.

## <a name="generate-log-files-for-analysis"></a>Générer des fichiers journaux d’analyse

Dans ce didacticiel, nous allons utiliser Analyseur de Message pour l’utiliser avec trois différents types de fichiers journaux, bien que vous pouvez choisir de travailler avec n’importe laquelle d'entre eux :

- Le **journal du serveur**, qui est créée lorsque vous activez l’enregistrement de stockage Azure. Le journal du serveur contient des données relatives à chaque opération appelée par rapport à un des services de stockage Azure - blob, file d’attente, table et fichier. Le journal du serveur indique l’opération était appelée et le code d’état a été retournées, ainsi que d’autres informations sur la demande et réponse.
- Le **journal de client .NET**, qui est créée lorsque vous activez la journalisation côté client à partir de votre application .NET. Le journal de client comprend des informations détaillées sur la façon dont le client prépare la demande et reçoit et traite la réponse.
- Le **journal de suivi réseau HTTP**, qui collecte des données sur données demande et réponse HTTP/HTTPS, notamment pour les opérations sur le stockage Azure. Dans ce didacticiel, nous allons générer la trace réseau via l’Analyseur de Message.

### <a name="configure-server-side-logging-and-metrics"></a>Configurer les indicateurs et journalisation côté serveur

Tout d’abord, nous devez configurer l’enregistrement de stockage Azure et les mesures, afin que nous avons des données à partir de l’application cliente à analyser. Vous pouvez configurer la journalisation et indicateurs de diverses façons, via le [Portail Azure](https://portal.azure.com), à l’aide de PowerShell, ou par programme. Consultez [l’activation des indicateurs de stockage et l’affichage des données de métrique](http://msdn.microsoft.com/library/azure/dn782843.aspx) et [l’activation de la journalisation de stockage et d’accès aux données journal](http://msdn.microsoft.com/library/azure/dn782840.aspx) sur MSDN pour plus d’informations sur la configuration de journalisation et indicateurs.

**Via le portail Azure**

Pour configurer la journalisation et indicateurs de votre compte de stockage à l’aide du [Portail Azure](https://portal.azure.com), suivez les instructions à [un compte de stockage dans le portail Azure moniteur](storage-monitor-storage-account.md).

> [AZURE.NOTE] Il n’est pas possible de définir des indicateurs minute à l’aide du portail Azure. Toutefois, nous vous recommandons de que vous ne les définissez le cadre de ce didacticiel et pour rechercher les problèmes de performances avec votre application. Vous pouvez définir des indicateurs minute à l’aide de PowerShell comme indiqué ci-dessous, ou par programme à l’aide de la bibliothèque de stockage client.
>
> Notez que le portail Azure ne peut pas afficher les mesures minutes, uniquement les mesures de toutes les heures.

**Via PowerShell**

Pour commencer à utiliser PowerShell pour Azure, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

1. Utiliser l’applet de commande [Add AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) pour ajouter votre compte d’utilisateur Azure dans la fenêtre PowerShell :

    ```
    Add-AzureAccount
    ```

2. Dans la fenêtre **se connecter à Microsoft Azure** , tapez l’adresse de messagerie et le mot de passe associés à votre compte. Azure authentifie enregistre les informations d’identification et puis ferme la fenêtre.
3. Définir le compte de stockage par défaut pour le compte de stockage à l’aide pour le didacticiel par l’exécution de ces commandes dans la fenêtre PowerShell :

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Activer la journalisation de stockage pour le service Blob :

    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```
5. Activer métriques de stockage pour le service Blob, en veillant à la valeur **- MetricsType** `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configurer la journalisation côté client .NET

Pour configurer la journalisation côté client pour une application .NET, activer les diagnostics de .NET dans le fichier de configuration de l’application (web.config ou app.config). Consultez [journalisation côté Client avec la bibliothèque .NET stockage Client](http://msdn.microsoft.com/library/azure/dn782839.aspx) et [côté Client journalisation avec Microsoft Azure stockage SDK pour Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) sur MSDN pour plus d’informations.

Le journaux côté client inclut des informations détaillées sur la façon dont le client prépare la demande et reçoit et traite la réponse.

La bibliothèque de stockage Client stocke les données du journal côté client à l’emplacement spécifié dans le fichier de configuration de l’application (web.config ou app.config).

### <a name="collect-a-network-trace"></a>Collecte une trace réseau

Vous pouvez utiliser l’Analyseur de Message pour collecter une trace réseau HTTP/HTTPS pendant que votre application cliente est en cours d’exécution. Analyseur de message utilise [Fiddler](http://www.telerik.com/fiddler) sur le serveur principal. Avant de collecter la trace réseau, nous vous recommandons de configurer Fiddler pour enregistrer le trafic HTTPS non chiffré :

1. Installez [Fiddler](http://www.telerik.com/download/fiddler).
2. Lancez Fiddler.
2. Sélectionnez outils **| Options de Fiddler**.
3. Dans la boîte de dialogue Options, assurez-vous que **Capturer HTTPS connecte** et **Déchiffrer le trafic HTTPS** sont les deux sélectionnés, comme illustré ci-dessous.

![Configurer les Options de Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Dans le didacticiel, collecter et enregistrer tout d’abord une trace réseau dans l’Analyseur de Message, puis créer une session d’analyse pour analyser la trace et les journaux. Pour recueillir une trace réseau dans l’Analyseur de Message :

1. Dans l’Analyseur de Message, sélectionnez **fichier | Trace rapide | Non chiffré HTTPS**.
2. La trace commence immédiatement. Cliquez sur **Arrêter** pour arrêter le suivi des afin que nous puissions configurez-le pour le trafic de stockage trace uniquement.
3. Sélectionner **Modifier** pour modifier la session de suivi.
4. Sélectionnez le lien **configurer** à droite du fournisseur ETW **Microsoft-Pef-WebProxy** .
5. Dans la boîte de dialogue **Paramètres avancés** , cliquez sur l’onglet **fournisseur** .
6. Dans le champ **Nom d’hôte filtre** , spécifier vos points de terminaison de stockage, séparés par des espaces. Par exemple, vous pouvez spécifier vos points de terminaison comme suit : modifier `storagesample` le nom de votre compte de stockage :

    ```
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Quitter la boîte de dialogue, puis cliquez sur **redémarrer** pour commencer la collecte la trace avec le filtre de nom d’hôte en place, afin que seul le trafic réseau de stockage Azure est inclus dans la trace.

>[AZURE.NOTE] Une fois que vous avez fini de collecter votre trace réseau, nous vous recommandons vivement de rétablir les paramètres que vous avez peut-être modifié dans Fiddler pour le trafic HTTPS déchiffrement. Dans la boîte de dialogue Options de Fiddler, décochez les cases **Capturer HTTPS connecte** et **Déchiffrer le trafic HTTPS** .

Consultez [l’aide des fonctionnalités de suivi réseau](http://technet.microsoft.com/library/jj674819.aspx) sur Technet pour plus d’informations.

## <a name="review-metrics-data-in-the-azure-portal"></a>Passez en revue les données de mesures dans le portail Azure

Une fois que votre application a fonctionné pendant une période donnée, vous pouvez examiner les graphiques indicateurs qui s’affichent dans le [Portail Azure](https://portal.azure.com) pour observer la façon dont votre service a été fonctionnent. Tout d’abord, accédez à votre compte de stockage dans le portail Azure et ajoutez un graphique de la mesure de **Pourcentage de succès** .

Dans le portail Azure, vous voyez le **Pourcentage de succès** dans le graphique de surveillance, ainsi que les autres mesures que vous avez ajouté. Dans le scénario nous allons étudier suivant en analysant les journaux dans l’Analyseur de Message, le taux de réussite pourcentage est un peu inférieure à 100 %.

Pour plus d’informations sur l’ajout d’indicateurs à la page surveillance, voir [Comment : ajouter des indicateurs à la table indicateurs](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Cela peut prendre quelques instants pour vos données métriques apparaissent dans le portail Azure après avoir activé métriques de stockage. C’est parce que mesures horaires pour l’heure précédente ne sont pas affichées dans le portail Azure jusqu'à ce que l’heure actuelle écoulé. En outre, les indicateurs minutes ne sont pas affichés actuellement dans le portail Azure. Donc selon lorsque vous activez les mesures, peut prendre jusqu'à deux heures pour afficher les mesures de données.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilisez AzCopy pour copier les journaux de serveur vers un répertoire local

Stockage Azure écrit données du journal server dans des objets BLOB, si les mesures sont écrits dans les tables. Journal des objets BLOB sont disponibles dans connus `$logs` conteneur pour votre compte de stockage. Journal des objets BLOB sont nommés hiérarchiquement par année, mois, jour et les heures, afin des retrouver facilement l’intervalle de temps que vous souhaitez examiner. Par exemple, dans le `storagesample` compte, le conteneur pour les objets BLOB journal 01/02/2015, à partir de 8-9 am, est `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Les objets BLOB individuels dans ce conteneur sont nommés de manière séquentielle, commençant par `000000.log`.

Vous pouvez utiliser l’outil de ligne de commande AzCopy pour télécharger ces fichiers journaux côté serveur vers un emplacement de votre choix sur votre ordinateur local. Par exemple, vous pouvez utiliser la commande suivante pour télécharger les fichiers journaux pour les opérations d’objets blob ayant eu lieu à 2 janvier 2015 vers le dossier `C:\Temp\Logs\Server`; remplacer `<storageaccountname>` avec le nom de votre compte de stockage, et `<storageaccountkey>` avec votre clé d’accès compte :

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy est disponible au téléchargement sur la page [Téléchargements Azure](https://azure.microsoft.com/downloads/) . Pour plus d’informations sur l’utilisation de AzCopy, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

Pour plus d’informations sur les journaux côté serveur téléchargement, voir [Télécharger la journalisation de stockage des données de journal](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utiliser l’Analyseur de Message de Microsoft pour analyser les données du journal

Analyseur de Message de Microsoft est un outil de capture, l’affichage et l’analyse protocole le trafic, les événements et les autres messages système ou l’application dans les scénarios de diagnostics et de résolution des problèmes de messagerie. Analyseur de message également vous permet de chargement, agréger et analyser des données à partir de journal et suivi des fichiers enregistrés. Pour plus d’informations sur l’Analyseur de Message, consultez [Microsoft Message analyseur d’exploitation Guide](http://technet.microsoft.com/library/jj649776.aspx).

Analyseur de message inclut actifs pour le stockage Azure qui vous aident à analyser les journaux de réseau, client et serveur. Dans cette section, nous verrons comment utiliser ces outils pour résoudre le problème de faible succès pourcentage dans les journaux de stockage.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Téléchargez et installez l’Analyseur de Message et les ressources de stockage Azure

1. Télécharger [L’Analyseur de Message](http://www.microsoft.com/download/details.aspx?id=44226) à partir du Microsoft Download Center, puis exécutez le programme d’installation.
2. Démarrer l’Analyseur de Message.
3. Dans le menu **Outils** , sélectionnez **Gestionnaire des biens**. Dans la boîte de dialogue **Gestionnaire de biens** , sélectionnez **téléchargements**, puis filtrer sur le **Stockage Azure**. Vous verrez les ressources de stockage Azure, comme le montre l’image ci-dessous.
4. Cliquez sur **Synchroniser tous les éléments affichés** pour installer les ressources de stockage Azure. Les biens disponibles sont les suivantes :
    - **Stockage azure couleur règles :** Règles de couleur de stockage Azure vous permettent de vous permet de définir des filtres spéciales qui utilisent des styles de police, couleur et du texte pour mettre en évidence les messages qui contiennent des informations spécifiques dans une trace.
    - **Stockage azure graphiques :** Les graphiques stockage Azure sont graphiques prédéfinis qui server journal des données du graphique. Notez que pour utiliser des graphiques de stockage Azure pour le moment, vous pouvez uniquement charger le journal du serveur dans la grille d’analyse.
    - **Stockage azure analyseurs :** Les analyseurs du stockage Azure analysent le stockage Azure client, serveur et les journaux HTTP afin de les afficher dans la grille d’analyse.
    - **Filtres de stockage azure :** Filtres de stockage Azure sont des critères prédéfinis que vous pouvez utiliser pour vos données dans la grille d’analyse de la requête.
    - **Mises en page du mode de stockage azure :** Mises en page de vue stockage Azure sont mises en page de la colonne prédéfinie et regroupements dans la grille d’analyse.
4. Redémarrez l’Analyseur de Message une fois que vous avez installé les biens.

![Gestion des messages Analyseur de biens](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] Installez toutes les ressources de stockage Azure indiqués dans le cadre de ce didacticiel.

### <a name="import-your-log-files-into-message-analyzer"></a>Importer vos fichiers journaux dans l’Analyseur de Message

Vous pouvez importer tous vos fichiers journaux enregistré (réseau, côté client et côté serveur) dans une seule session dans l’Analyseur de Message de Microsoft pour l’analyse.

1. Dans le menu **fichier** dans l’Analyseur de Message de Microsoft, cliquez sur **Nouvelle Session**, puis cliquez sur **Session vide**. Dans la boîte de dialogue **Nouvelle Session** , entrez un nom pour votre session d’analyse. Dans le volet de **Détails de la Session** , cliquez sur le bouton de **fichiers** .
1. Pour charger les données de suivi réseau générées par l’Analyseur de Message, cliquez sur **Ajouter des fichiers**, accédez à l’emplacement où vous avez enregistré votre fichier .matp à partir de la session de suivi web, sélectionnez le fichier .matp et cliquez sur **Ouvrir**.
1. Pour charger les données du journal côté serveur, cliquez sur **Ajouter des fichiers**, accédez à l’emplacement où vous avez téléchargé vos journaux côté serveur, sélectionnez les fichiers journaux de la plage horaire que vous voulez analyser et cliquez sur **Ouvrir**. Puis, dans le volet de **Détails de la Session** , définissez la **Configuration du journal texte** déroulante pour chaque fichier journal côté serveur sur **AzureStorageLog** pour vous assurer que Microsoft Message Analyzer pouvez analyser le fichier journal correctement.
1. Pour charger les données du journal côté client, cliquez sur **Ajouter des fichiers**, naviguez jusqu'à l’emplacement où vous avez enregistré votre les journaux côté client, sélectionnez les fichiers journaux que vous voulez analyser et cliquez sur **Ouvrir**. Puis, dans le volet de **Détails de la Session** , définissez la **Configuration du journal texte** déroulante pour chaque fichier journal côté client sur **AzureStorageClientDotNetV4** pour vous assurer que Microsoft Message Analyzer pouvez analyser le fichier journal correctement.
1. Dans la boîte de dialogue **Nouvelle Session** charger et analyser les données du journal, cliquez sur **Démarrer** . Les données du journal affichent dans la grille d’analyse de l’Analyseur de Message.

L’image ci-dessous montre un exemple de session configuré avec les journaux de suivi réseau, client et serveur.

![Configurer la Session de l’Analyseur de Message](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Notez que l’Analyseur de Message charge les fichiers journaux en mémoire. Si vous avez un grand ensemble de données du journal, vous souhaiterez filtrer afin d’obtenir les meilleures performances de l’Analyseur de Message.

Tout d’abord, déterminer l’intervalle de temps qui vous intéresse et le laisser ce laps de temps aussi petite que possible. Dans de nombreux cas, vous devez passer en revue une période de minutes ou d’heures au maximum. Importer le plus petit ensemble de journaux répondant à vos besoins.

Si vous avez toujours une grande quantité de données du journal, vous souhaiterez spécifier une session de filtrer vos données de journal avant de le charger. Dans la zone de **Filtre de Session** , sélectionnez le bouton de la **bibliothèque** pour choisir un filtre prédéfini ; par exemple, choisissez **filtre heure Global I** parmi les filtres de stockage Azure pour un filtrage sur un intervalle de temps. Vous pouvez ensuite modifier les critères de filtre pour spécifier le début et fin horodatage pour l’intervalle que vous voulez voir. Vous pouvez également filtrer un code d’état particulier ; par exemple, vous pouvez choisir de charger uniquement les entrées de journal où le code d’état est 404.

Pour plus d’informations sur l’importation de données du journal dans l’Analyseur de Message de Microsoft, voir [Récupérer les données de Message](http://technet.microsoft.com/library/dn772437.aspx) sur TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utiliser l’ID de demande de client pour faire correspondre les données du fichier journal

La bibliothèque de Client de stockage Azure génère automatiquement un ID de demande de client unique pour chaque demande. Cette valeur est écrite dans le journal du client, le journal du serveur et la trace réseau, afin que vous puissiez l’utiliser pour faire correspondre des données entre trois journaux dans l’Analyseur de Message. Voir [ID de demande de Client](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) pour plus d’informations sur la demande de client identifiant.

Les sections suivantes décrivent comment utiliser les affichages de mise en page préconfiguré et personnalisés pour faire correspondre et regrouper des données en fonction de l’ID de demande de client.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Sélectionner une disposition de vue à afficher dans la grille d’analyse

Les ressources de stockage pour Analyseur de Message incluent des dispositions de mode de stockage Azure qui constituent les vues préconfigurés que vous pouvez utiliser pour afficher vos données avec regroupements utiles et des colonnes pour différents scénarios. Vous pouvez également créer des mises en page de l’affichage personnalisé et les enregistrer pour le réutiliser.

L’image ci-dessous montre le menu **Affichage** , disponible en sélectionnant **La disposition des vues** dans le ruban de barre d’outils. Les mises en page de l’affichage pour le stockage Azure sont regroupés sous le nœud de **Stockage Azure** dans le menu. Vous pouvez rechercher des `Azure Storage` dans la zone de recherche pour filtrer sur le stockage Azure permet d’afficher uniquement les dispositions. Vous pouvez également sélectionner l’étoile en regard d’une disposition de vues pour ajouter aux Favoris et les afficher dans la partie supérieure du menu.

![Afficher le menu mise en forme](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Commencez par sélectionner **groupées par ClientRequestID et le Module**. Cet affichage des groupes mise en page Enregistrer les données à partir de tous les journaux des trois tout d’abord par ID de demande de client, puis en fichier journal source (ou un **Module** dans l’Analyseur de Message). Dans cette vue, vous pouvez explorer les ID d’une demande de client particulier et afficher les données de tous les fichiers journaux trois pour cette requête client identifiant.

L’image ci-dessous montre ce mode appliquée aux données du journal exemple, un sous-ensemble de colonnes affichées. Vous pouvez voir que pour un ID de demande client particulier, la grille d’analyse affiche des données à partir du journal du client, le journal du serveur et la trace réseau.

![Afficher la présentation stockage Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Différents fichiers journaux ont des colonnes différentes, afin que les données de plusieurs fichiers journaux qui s’affiche dans la grille d’analyse, certaines colonnes ne contienne pas toutes les données d’une ligne donnée. Par exemple, dans l’image ci-dessus, les lignes de journal client ne pas affichent toutes les données pour **l’horodatage**, **TimeElapsed**, **Source**et les colonnes de **Destination** , car ces colonnes n’existent pas dans le journal du client, mais existent dans la trace réseau. De même, la colonne **d’horodatage** affiche des données d’horodatage à partir du journal de serveur, mais aucune donnée est affichée pour les colonnes **TimeElapsed**, **Source**et de **Destination** , qui ne font pas partie du journal du serveur.

Outre les mises en page de la vue stockage Azure, vous pouvez également définir et enregistrer vos propres dispositions d’affichage. Vous pouvez sélectionner d’autres champs de votre choix pour regrouper des données et enregistrer le regroupement dans le cadre de votre mise en page personnalisée également.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Appliquer des règles de couleur à la grille d’analyse

Les ressources de stockage incluent également les règles de couleur, qui offrent qu'un visuel signifie identifier les différents types d’erreurs dans la grille d’analyse. Les règles de couleur prédéfinie s’appliquent à des erreurs HTTP, afin qu’ils s’affichent uniquement pour le suivi de journal et de réseau de serveur.

Pour appliquer des règles de couleur, sélectionnez **Couleur de règles** à partir du ruban de barre d’outils. Vous verrez les règles de couleur de stockage Azure dans le menu. Dans le didacticiel, sélectionnez **Erreurs Client (StatusCode entre 400 et 499)**, comme le montre l’image ci-dessous.

![Afficher la présentation stockage Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Outre les règles de couleur de stockage Azure, vous pouvez également définir et enregistrer vos propres règles de couleur.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Groupe et filtrer des données de journal pour rechercher les erreurs 400-plage

Nous allons ensuite regrouper et filtrer les données du journal pour trouver toutes les erreurs dans la plage 400.

1. Localisez la colonne **StatusCode** dans la grille d’analyse, cliquez sur l’en-tête de colonne, puis sélectionnez **groupe**.
2. Groupe suivant, dans la colonne **ClientRequestId** . Vous verrez que les données dans la grille d’analyse sont maintenant organisées par code d’état et par ID client demande.
1. Afficher la fenêtre de l’outil de filtre d’affichage si elle n’est pas déjà affichée. Dans le ruban de barre d’outils, sélectionnez **Outil Windows**, puis **Filtre d’affichage**.
2. Pour filtrer les données du journal pour afficher uniquement les erreurs 400-plage, ajoutez les critères de filtre suivantes dans la fenêtre de **Filtre d’affichage** , puis cliquez sur **Appliquer**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

L’image ci-dessous montre les résultats de ce regroupement et un filtre. Développer le champ **ClientRequestID** sous le regroupement de code d’état 409, par exemple, montre une opération qui a donné lieu à ce code d’état.

![Afficher la présentation stockage Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Après avoir appliqué ce filtre, vous verrez que les lignes à partir du journal client sont exclus, comme client de journal n’inclut pas d’une colonne **StatusCode** . Dans un premier temps, nous allons examiner le serveur et les journaux de suivi réseau pour identifier les 404 erreurs et puis nous allons retourner dans le journal de client pour examiner les opérations du client qui conduit leur.

>[AZURE.NOTE] Vous pouvez filtrer la colonne **StatusCode** et toujours afficher les données de tous les journaux des trois, y compris le journal de client, si vous ajoutez une expression au filtre qui comporte les entrées de journal où le code d’état est null. Pour créer cette expression de filtre, utilisez :
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Ce filtre renvoie le toutes les lignes à partir du client journal et uniquement les lignes le journal de serveur et HTTP dont le code d’état est supérieur à 400. Si vous appliquez à la mise en page affichage regroupé par ID de demande de client et le module, vous pouvez effectuer une recherche ou rendez-vous directement via les entrées de journal pour trouver celles où tous les journaux des trois sont représentés.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrer les données du journal pour rechercher des 404 erreurs

Les ressources de stockage incluent des filtres prédéfinis que vous pouvez utiliser pour limiter les données du journal pour rechercher les erreurs et les tendances que vous recherchez. Ensuite, nous allons appliquer des filtres prédéfinis deux : une qui permet de filtrer le serveur et les journaux de suivi réseau pour 404 erreurs et qui permet de filtrer les données dans un intervalle de temps spécifié.

1. Afficher la fenêtre de l’outil de filtre d’affichage si elle n’est pas déjà affichée. Dans le ruban de barre d’outils, sélectionnez **Outil Windows**, puis **Filtre d’affichage**.
2. Dans la fenêtre de filtre d’affichage, sélectionnez **bibliothèque**, puis effectuez une recherche sur `Azure Storage` pour trouver le stockage Azure filtres. Sélectionnez le filtre pour **404 (introuvable) les messages dans tous les journaux**.
3. Afficher le menu de **bibliothèque** à nouveau, recherchez et sélectionnez le **Filtre période globale**.
4. Modifier les horodatages indiqués dans le filtre à la plage que vous souhaitez afficher. Cela vous aidera à affiner la plage de données à analyser.
5. Votre filtre doit apparaître comme dans l’exemple ci-dessous. Cliquez sur **Appliquer** pour appliquer le filtre à la grille d’analyse.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Afficher la présentation stockage Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analyser vos données de journal

À présent que vous avez groupées et filtrer vos données, vous pouvez examiner les détails des requêtes individuelles à l’origine de 404 erreurs. Dans la présentation actuelle de la vue, les données sont regroupées par ID de demande de client, puis par la source du journal. Étant donné que nous apportions filtrage sur les requêtes dont le champ StatusCode contient 404, nous verrons seulement le serveur et les données de trace réseau, pas les données du journal client.

L’image ci-dessous montre une requête spécifique à l’endroit où une opération obtenir Blob obtenue une erreur 404, car le blob n’existe pas. Notez que certaines colonnes ont été supprimés à partir de la vue standard pour afficher les données pertinentes.

![Serveur filtrée et les journaux de suivi réseau](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Ensuite, nous allons corrélation cet identifiant de demande de client avec les données du journal client pour connaître les actions que le client est en cours lors de l’erreur est survenue. Vous pouvez afficher une nouvelle vue de grille d’analyse pour cette session afficher les données du journal client, qui s’ouvre dans un deuxième onglet :

1. Tout d’abord, copiez la valeur du champ **ClientRequestId** dans le Presse-papiers. Vous pouvez le faire en sélectionnant une ligne, vous avez trouvé le champ **ClientRequestId** , avec le bouton droit sur la valeur des données et cliquer sur **Copier « ClientRequestId »**.
1. Dans le ruban de barre d’outils, sélectionnez **Nouvelle visionneuse**, puis sélectionnez Ouvrir un nouvel onglet de la **Grille d’analyse** . Le nouvel onglet affiche toutes les données dans vos fichiers journaux, sans regrouper, filtrer ou les règles de couleur.
2. Dans le ruban de barre d’outils, sélectionnez **Afficher la présentation**, puis sélectionnez **Toutes les colonnes de Client .NET** sous la section **Stockage Azure** . Cette disposition vue affiche les données à partir du client journal, ainsi que les journaux de suivi réseau et serveur. Par défaut, elle est triée sur la colonne **format** .
3. Ensuite, recherchez le journal du client pour l’ID de demande de client. Dans le ruban de barre d’outils, sélectionnez **Rechercher des Messages**, puis spécifier un filtre personnalisé pour l’ID de demande de client dans le champ **Rechercher** . Utilisez la syntaxe suivante pour le filtre, en spécifiant l’identificateur de votre propre requête client :

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Analyseur de message localise et sélectionne la première entrée de journal où les critères de recherche correspond à l’ID de demande de client. Dans le journal de client, il existe plusieurs entrées pour chaque ID de demande de client, et vous préférerez peut-être afin de les regrouper sur le champ **ClientRequestId** pour simplifier l’ensemble les voir. L’image ci-dessous montre tous les messages dans le journal de client pour le client spécifié ID de demande.

![Client journal montrant les 404 erreurs](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Utilise les données affichées dans les mises en page de vue dans ces deux onglets, vous pouvez analyser les données de demande pour déterminer ce qui peut avoir provoqué l’erreur. Vous pouvez également consulter demandes qui ont précédé celui-ci pour voir si un événement précédent peut ont permis l’erreur 404. Par exemple, vous pouvez consulter les entrées du journal client précédant cet identifiant de demande de client pour déterminer si le blob ont été supprimé, ou si l’erreur est due à l’application cliente appelant une API CreateIfNotExists sur un conteneur ou blob. Dans le journal de client, vous pouvez trouver des adresses de blob dans le champ **Description** ; dans le serveur et les journaux de suivi réseau, cette information apparaît dans le champ **Résumé** .

Une fois que vous connaissez l’adresse du blob obtenue l’erreur 404, vous pouvez approfondir. Si vous recherchez les entrées du journal d’autres messages associés aux opérations sur le même objet blob, vous pouvez vérifier si le client supprimé précédemment l’entité.

## <a name="analyze-other-types-of-storage-errors"></a>Analyser les autres types d’erreurs de stockage

À présent que vous êtes habitué à l’Analyseur de Message pour analyser vos données de journal, vous pouvez analyser les autres types d’erreurs avec l’affichage mises en page, les règles de la couleur et la recherche/filtrage. Les tableaux ci-dessous répertorie certains problèmes que vous pouvez rencontrer et les critères de filtre vous permet de les localiser. Pour plus d’informations sur la création de filtres et l’Analyseur de Message filtrage langue, voir [Filtrage des données de Message](http://technet.microsoft.com/library/jj819365.aspx).

|    Pour déterminer l’origine...                                                                                               |    Utiliser l’Expression de filtre...                                                                                                                                                                                                                                        |    Expression s’applique aux journaux (Client, serveur, réseau, tous les)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Retards inattendus dans une file d’attente de remise des messages                                                              |    AzureStorageClientDotNetV4.Description contient « Nouvel essai Échec de l’opération. »                                                                                                                                                                                |    Client                                                        |
|    Augmentation HTTP PercentThrottlingError                                                                       |    HTTP. Response.StatusCode == 500 & #124 ; & #124 ; HTTP. Response.StatusCode == 503                                                                                                                                                                                          |    Réseau                                                       |
|    Augmenter de PercentTimeoutError                                                                               |    HTTP. Response.StatusCode == 500                                                                                                                                                                                                                             |    Réseau                                                       |
|    Augmenter de PercentTimeoutError (tous)                                                                         |    * StatusCode == 500                                                                                                                                                                                                                                          |    Tous les                                                           |
|    Augmenter de PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Client                                                        |
|    Messages HTTP 403 (refusé)                                                                                 |    HTTP. Response.StatusCode == 403                                                                                                                                                                                                                             |    Réseau                                                       |
|    HTTP 404 (non trouvé) des messages                                                                                 |    HTTP. Response.StatusCode == 404                                                                                                                                                                                                                             |    Réseau                                                       |
|    404 (tous)                                                                                                     |    * StatusCode == 404                                                                                                                                                                                                                                          |    Tous les                                                           |
|    Partagé problème de l’autorisation d’accès Signature (sa)                                                             |    AzureStorageLog.RequestStatus == « SASAuthorizationError »                                                                                                                                                                                                     |    Réseau                                                       |
|    HTTP 409 (conflit) des messages                                                                                  |    HTTP. Response.StatusCode == 409                                                                                                                                                                                                                             |    Réseau                                                       |
|    409 (tous)                                                                                                     |    * StatusCode == 409                                                                                                                                                                                                                                          |    Tous les                                                           |
|    Entrées de journal PercentSuccess faible ou analytique avoir opérations statut transaction de ClientOtherErrors    |    AzureStorageLog.RequestStatus == « ClientOtherError »                                                                                                                                                                                                         |    Serveur                                                        |
|    Avertissement Nagle                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) et (AzureStorageLog.RequestPacketSize < 1460) et (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Serveur                                                        |
|    Intervalle de temps dans les journaux de serveur et du réseau                                                                    |    #Horodatage > = 2014-10-20T16:36:38 et #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Serveur, réseau                                               |
|    Intervalle de temps dans les journaux de serveur                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 et AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Serveur                                                        |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les scénarios de bout en bout dépannage dans le stockage Azure, reportez-vous aux ressources :

- [Surveiller, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
- [Stockage Analytique](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Analyser un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- [Microsoft Message analyseur d’exploitation Guide](http://technet.microsoft.com/library/jj649776.aspx)
