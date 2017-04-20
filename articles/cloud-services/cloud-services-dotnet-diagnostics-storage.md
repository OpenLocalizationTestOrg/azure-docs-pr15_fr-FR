<properties
    pageTitle="Stocker et affichage des données de Diagnostic dans stockage Azure | Microsoft Azure"
    description="Obtenir les diagnostics de Windows Azure données dans le stockage Azure et l’afficher"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Stocker et afficher des données de diagnostic dans le stockage Azure

Données de diagnostic ne sont pas stockées définitivement sauf si transférez-le à l’émulateur de stockage Microsoft Azure ou au stockage Azure. Une fois dans le stockage, il peut être affiché avec l’un des nombreux outils disponibles.

## <a name="specify-a-storage-account"></a>Spécifier un compte de stockage

Vous spécifiez le compte de stockage que vous souhaitez utiliser dans le fichier ServiceConfiguration.cscfg. Les informations de compte sont définies comme une chaîne de connexion dans un paramètre de configuration. L’exemple suivant illustre la chaîne de connexion par défaut créée pour un nouveau projet de Service Cloud dans Visual Studio :


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Vous pouvez modifier cette chaîne de connexion pour fournir des informations de compte pour un compte de stockage Azure.

Selon le type de données de diagnostic qui sont collectées, Diagnostics Azure utilise le service d’objets Blob ou le service de tableau. Le tableau suivant montre les sources de données sont conservées et leur format.

|Source de données|Format de stockage|
|---|---|
|Journaux d’Azure|Table|
|Journaux IIS 7.0|Objets BLOB|
|Journaux d’infrastructure Diagnostics Azure|Table|
|Impossible de demander le suivi des journaux|Objets BLOB|
|Journaux d’événements Windows|Table|
|Compteurs de performance|Table|
|Vidage sur incident|Objets BLOB|
|Journaux d’erreur personnalisée|Objets BLOB|

## <a name="transfer-diagnostic-data"></a>Transférer des données de diagnostic

Pour SDK 2.5 et versions ultérieures, la demande de transfert de données de diagnostic peut se produire par le biais du fichier de configuration. Vous pouvez transférer des données de diagnostic à intervalles réguliers, comme indiqué dans la configuration.

Pour SDK 2.4 précédent et vous pouvez demander à transférer les données de diagnostic via le fichier de configuration ainsi que par programme. Cette méthode autorise également vous permettent d’effectuer des transferts à la demande.


>[AZURE.IMPORTANT] Lorsque vous transférez des données de diagnostic à un compte de stockage Azure, vous subir des coûts pour les ressources de stockage utilisé par vos données de Diagnostics.

## <a name="store-diagnostic-data"></a>Stocker les données de diagnostic

Données du journal sont stockées dans le stockage Blob ou Table avec les noms suivants :

**Tables**

- **WadLogsTable** - journaux écrits en code à l’aide de que l’auditeur trace.

- **WADDiagnosticInfrastructureLogsTable** - Analyseur de diagnostics et de configuration est modifié.

- **WADDirectoriesTable** – répertoires le diagnostic surveille.  Cela inclut les journaux IIS, IIS ne peut pas les journaux de requêtes et des répertoires personnalisés.  L’emplacement du fichier journal blob est spécifié dans le champ conteneur et le nom de l’objet blob est dans le champ RelativePath.  Le champ AbsolutePath indique l’emplacement et le nom du fichier tel qu’il était sur l’ordinateur virtuel Azure.

- **WADPerformanceCountersTable** – compteurs de Performance.

- **WADWindowsEventLogsTable** – les journaux d’événements Windows.

**Objets BLOB**

- **conteneur de contrôle wad** – (uniquement pour le Kit de développement logiciel 2.4 et précédent) contient les fichiers de configuration XML qui détermine les diagnostics de Windows Azure.

- Ouvre une session **wad-iis-failedreqlogfiles** – contient les informations de demande de IIS a échoué.

- **wad-iis-fichiers journaux** – comprend des informations sur IIS se connecte.

- **« personnalisée »** – un conteneur personnalisé basé sur la configuration des répertoires qui sont analysés par l’Analyseur de diagnostic.  Le nom de ce conteneur blob est spécifié dans WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Outils pour afficher les données de diagnostic
Plusieurs outils sont disponibles pour afficher les données une fois qu’il est transféré vers le stockage. Par exemple :

- Explorateur de serveurs dans Visual Studio - si vous avez installé les outils Azure pour Microsoft Visual Studio, vous pouvez utiliser le nœud de stockage Azure dans l’Explorateur de serveur à afficher en lecture seule blob et tableau de données à partir de vos comptes de stockage Azure. Vous pouvez afficher des données à partir de votre compte émulateur stockage local et également à partir des comptes de stockage que vous avez créé pour Azure. Pour plus d’informations, voir [la navigation et gestion du stockage des ressources avec l’Explorateur de serveur](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Explorateur de stockage de Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui vous permet de travailler facilement avec les données de stockage Azure sous Windows, OSX et Linux.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclut le Gestionnaire de Diagnostics Azure qui vous permet d’afficher, de télécharger et de gérer les données de diagnostic collectées par les applications en cours d’exécution sur Azure.


## <a name="next-steps"></a>Étapes suivantes

[Suivre le flux dans une application de Services de Cloud avec Diagnostics Azure](cloud-services-dotnet-diagnostics-trace-flow.md)
