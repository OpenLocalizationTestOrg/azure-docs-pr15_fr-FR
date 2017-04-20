<properties 
    pageTitle="Importer et exporter des données dans le Cache Redis Azure | Microsoft Azure" 
    description="Découvrez comment importer et exporter des données vers et depuis le stockage blob avec vos instances de Cache Redis Azure premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importer et exporter des données dans le Cache Redis Azure

Importation/exportation est une opération de gestion des données Azure Redis Cache, qui vous permet d’importer des données dans le Cache Redis Azure ou exporter des données à partir d’Azure Redis Cache par importer et exporter un instantané de base de données de Cache Redis (relationnelle) à partir d’un cache premium vers un blob de page dans un compte de stockage Azure. Importer/Exporter vous permet de migrer entre différentes instances de Cache Redis Azure ou remplir le cache de données avant des utiliser.

Cet article fournit un guide pour l’importation et exportation de données avec Azure Redis Cache et fournit les réponses aux questions fréquemment posées.

>[AZURE.IMPORTANT] Importation/exportation est en mode Aperçu avant et est disponible uniquement pour le cache de [niveau premium](cache-premium-tier-intro.md) .

## <a name="import"></a>Importation

Importation peut servir à transférer Redis compatible relationnelle ou les fichiers à partir de n’importe quel serveur Redis en cours d’exécution dans n’importe quel nuage ou l’environnement, y compris Redis s’exécutant sur n’importe quel fournisseur de nuage tel que Amazon Web Services et d’autres personnes, Windows ou Linux. Importer des données est un moyen facile de créer un cache de données prédéfinie. Pendant le processus d’importation, Azure Redis Cache charge les fichiers relationnelle à partir du stockage Azure en mémoire, puis insère les touches dans le cache.

>[AZURE.NOTE] Avant de commencer l’opération d’importation, vérifiez que votre base de données Redis (relationnelle) ou les fichiers sont téléchargés dans des objets BLOB de page dans le stockage Azure, dans la même région et abonnement comme votre instance Azure Redis Cache. Pour plus d’informations, voir [prise en main stockage d’objets Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md). Si vous avez exporté votre fichier relationnelle à l’aide de la fonctionnalité [d’Exportation de Cache Redis Azure](#export) , votre fichier relationnelle est déjà stocké dans un blob de page et est prêt pour l’importation.

1. Pour importer un ou plusieurs cache exporté des objets BLOB, [accédez à votre cache](cache-configure.md#configure-redis-cache-settings) dans le portail Azure et cliquez sur **Importer des données** à partir de la carte de **paramètres** de l’instance du cache.

    ![Importer des données][cache-import-data]

2. Cliquez sur **Choisir Blob(s)** et sélectionnez le compte de stockage contenant les données à importer.

    ![Sélectionnez le compte de stockage][cache-import-choose-storage-account]

3. Cliquez sur le conteneur qui contient les données à importer.

    ![Choisir un conteneur][cache-import-choose-container]

4. Sélectionnez un ou plusieurs objets BLOB pour importer en cliquant sur la zone à gauche du nom du blob, puis cliquez sur **Sélectionner**.

    ![Choisissez des objets BLOB][cache-import-choose-blobs]

5. Cliquez sur **Importer** pour commencer le processus d’importation.

    >[AZURE.IMPORTANT] Le cache n’est pas accessible par les clients de cache pendant le processus d’importation, et toutes les données existantes dans le cache sont supprimées.

    ![Importation][cache-import-blobs]

    Vous pouvez surveiller l’avancement de l’opération d’importation en suivant les notifications à partir du portail Azure ou en consultant les événements dans l' [audit se connecter](cache-configure.md#support-amp-troubleshooting-settings).

    ![Importer la progression][cache-import-data-import-complete] 


## <a name="export"></a>Exporter

Exporter vous permet d’exporter les données stockées dans le Cache de Redis Azure à Redis compatible relationnelle ou les fichiers. Vous pouvez utiliser cette fonctionnalité pour déplacer des données à partir d’une instance de Cache Redis Azure vers un autre ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur l’ordinateur virtuel qui héberge l’instance du serveur Azure Redis Cache et le fichier est téléchargé sur le compte de stockage désigné. Une fois l’opération d’exportation terminée avec soit un état de réussite ou l’échec, le fichier temporaire est supprimé.

1. Pour exporter le contenu du cache de stockage, [accédez à votre cache](cache-configure.md#configure-redis-cache-settings) dans le portail Azure actif et cliquez sur **Exporter les données** à partir de la carte de **paramètres** de l’instance du cache.

    ![Choisir un conteneur de stockage][cache-export-data-choose-storage-container]

2. Cliquez sur **Choisir un conteneur de stockage** et sélectionnez le compte de stockage de votre choix. Le compte de stockage doit être placé dans le même abonnement et région en tant que le cache de résolution.

    >[AZURE.IMPORTANT] Importer/exporter fonctionne avec des objets BLOB de page, qui sont prises en charge par classique et comptes de stockage processeur, mais ne sont pas pris en charge par [Blob comptes de stockage](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) pour le moment.

    ![Compte de stockage][cache-export-data-choose-account]

3. Choisissez le conteneur blob souhaité et cliquez sur **Sélectionner**. Pour utiliser nouveau un conteneur, cliquez sur **Ajouter un conteneur** pour l’ajouter en premier, puis sélectionnez-la dans la liste.

    ![Choisir un conteneur de stockage][cache-export-data-container]

4. Tapez un **préfixe de nom d’objets Blob** et cliquez sur **Exporter** pour démarrer le processus d’exportation. Le préfixe du nom blob est utilisé pour préfixer les noms des fichiers générés par cette opération d’exportation.

    ![Exporter][cache-export-data]

    Vous pouvez surveiller l’avancement de l’opération d’exportation en suivant les notifications à partir du portail Azure ou en consultant les événements dans l' [audit se connecter](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Cache reste disponibles pour une utilisation pendant le processus d’exportation.


## <a name="importexport-faq"></a>Forum aux questions sur Importer/exporter

Cette section contient des questions fréquemment posées sur la fonctionnalité Importer/exporter.

-   [Quelle tarification niveaux peut utiliser importer/exporter ?](#what-pricing-tiers-can-use-importexport)
-   [Puis-je importer des données à partir de n’importe quel serveur Redis ?](#can-i-import-data-from-any-redis-server)
-   [Mon cache sera disponible lors d’une opération d’importation/exportation ?](#will-my-cache-be-available-during-an-importexport-operation)
-   [Puis-je utiliser importer/exporter avec cluster Redis ?](#can-i-use-importexport-with-redis-cluster)
-   [Comment importer/exporter fonctionne-t-il avec un paramètre personnalisé de la bases de données ?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [Comment importer/exporter est différent de persistance Redis ?](#how-is-importexport-different-from-redis-persistence)
-   [Puis-je automatiser à l’aide de PowerShell, infrastructure du langage commun ou autres clients de gestion importer/exporter ?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [J’ai reçu une erreur de délai pendant mon opération d’importation/exportation. Que signifie ?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [Je reçois un message d’erreur lors de l’exportation de mes données au stockage d’objets Blob Azure. Que s'est-il passé ?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>Quelle tarification niveaux peut utiliser importer/exporter ?

Importer/exporter est disponible uniquement dans la prime tarifs niveau.

### <a name="can-i-import-data-from-any-redis-server"></a>Puis-je importer des données à partir de n’importe quel serveur Redis ?

Oui, en plus de l’importation de données exportées à partir d’instances de Cache Redis Azure, vous pouvez importer des fichiers relationnelle à partir de n’importe quel serveur Redis en cours d’exécution dans n’importe quel nuage ou l’environnement, tels que Linux, Windows, ou fournisseurs tels que Amazon Web Services en nuage. Pour ce faire, téléchargez le fichier relationnelle à partir du serveur Redis souhaité dans un blob de page dans un compte de stockage Azure et puis importez-les dans votre instance de Cache Redis Azure premium. Par exemple, vous souhaitez exporter les données à partir du cache de production et l’importer dans un cache utilisé dans le cadre d’un environnement intermédiaire pour tester ou de migration. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>Mon cache sera disponible lors d’une opération d’importation/exportation ?

-   **Exporter** - cache reste disponibles et vous pouvez continuer à utiliser le cache pendant une opération d’exportation.
-   **Importer** - cache ne sont plus disponibles lorsque démarre une opération d’importation et sont disponibles pour une utilisation lorsque l’opération d’importation est terminée.


### <a name="can-i-use-importexport-with-redis-cluster"></a>Puis-je utiliser importer/exporter avec cluster Redis ?

Oui, et vous pouvez importer/exporter entre un cache groupé et un cache non groupé. Depuis Redis cluster [prend uniquement en charge la base de données 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), toutes les données de bases de données différent de 0 ne seront pas importées. Lorsque le cache groupé les données sont importées, les clés sont redistribués parmi au milieu des fragments du cluster. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Comment importer/exporter fonctionne-t-il avec un paramètre personnalisé de la bases de données ?

Certains niveaux tarification avoir différentes [limites de bases de données](cache-configure.md#databases), il y a quelques considérations lors de l’importation si vous avez configuré une valeur personnalisée pour la `databases` définition lors de la création du cache.

-   Lors de l’importation à un niveau de tarification avec un inférieur `databases` limite que le niveau à partir duquel vous avez exporté :
    -   Si vous utilisez le numéro par défaut de `databases` qui ne correspond à 16 pour tous les niveaux de prix, aucune donnée n’est perdue.
    -   Si vous utilisez un nombre personnalisé de `databases` qui se situe dans les limites de la couche vers lequel vous importez, aucune donnée n’est perdue.
    -   Si vos données exportées contenaient des données dans une base de données qui dépasse les limites de la nouvelle couche, les données à partir de ces bases de données plus élevés ne sont pas importées.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Comment importer/exporter est différent de persistance Redis ?

Azure persistance Redis Cache permet de conserver les données stockées dans Redis au stockage Azure. Lors de la persistance est configuré, Azure Redis Cache persiste un instantané du cache Redis dans un format binaire Redis sur le disque basé sur une fréquence de sauvegarde configurable. Si un sinistre se produit qui désactive le cache principal et réplica, les données du cache sont restaurées automatiquement à l’aide de l’instantané le plus récent. Pour plus d’informations, voir [comment configurer permanence des données pour un Cache de Redis Premium Azure](cache-how-to-premium-persistence.md).

Importation / exportation vous permet de transférer des données dans ou exporter à partir d’Azure Redis Cache. Il ne pas configurer la sauvegarde et restauration à l’aide de persistance Redis.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Puis-je automatiser à l’aide de PowerShell, infrastructure du langage commun ou autres clients de gestion importer/exporter ?

Oui, pour PowerShell instructions voir [pour importer un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) et [pour exporter un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>J’ai reçu une erreur de délai pendant mon opération d’importation/exportation. Que signifie ?

S’il vous restez sur la carte de **données importer** ou **exporter des données** pendant plus de 15 minutes avant de lancer l’opération, vous recevrez un message d’erreur semblable au suivant.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Pour résoudre ce problème, démarrer l’importation ou exportation avant 15 minutes écoulée.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Je reçois un message d’erreur lors de l’exportation de mes données au stockage d’objets Blob Azure. Que s'est-il passé ?

Importer/exporter ne fonctionne qu’avec les fichiers relationnelle stockés en tant que BLOB de page. Autres types d’objets blob ne sont pas prises en charge pour l’instant, y compris les comptes de stockage blob avec niveaux d’accès rapide et pouvant être ajoutées.


## <a name="next-steps"></a>Étapes suivantes
Découvrez comment utiliser d’autres fonctionnalités de cache premium.

-   [Introduction à la couche Azure Redis Cache Premium](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








