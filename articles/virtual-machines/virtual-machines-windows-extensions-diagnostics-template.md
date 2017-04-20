<properties
    pageTitle="Créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide du modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Utiliser un modèle de gestionnaire de ressources Azure pour créer une nouvelle machine virtuelle Windows avec l’extension diagnostics de Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide du modèle de gestionnaire de ressources Azure

L’Extension de Diagnostics Azure propose l’analyse et les fonctions de diagnostic sur un Windows basé Azure machine virtuelle. Vous pouvez activer ces fonctionnalités sur l’ordinateur virtuel en incluant l’extension dans le cadre du modèle de gestionnaire de Ressources azure. Voir [Création Azure Gestionnaire de ressources modèles avec les Extensions machine virtuelle](virtual-machines-windows-extensions-authoring-templates.md) pour plus d’informations sur l’inclusion de toutes les extensions dans le cadre d’un modèle de machine virtuelle. Cet article décrit comment vous pouvez ajouter l’extension Azure Diagnostics à un modèle de machine virtuelle windows.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Ajouter l’extension Azure Diagnostics pour la définition de la ressource machine virtuelle 

Pour activer l’extension diagnostics sur une Machine virtuelle Windows que vous devez ajouter l’extension comme une ressource virtuelle dans le modèle de gestionnaire de ressources.

Pour un gestionnaire de ressources simple Machine virtuelle en fonction Ajouter la configuration de l’extension au tableau des *ressources* pour la Machine virtuelle : 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Une autre convention courantes est ajouter la configuration de l’extension au niveau du nœud de ressources racine du modèle au lieu de définir sous nœud de ressources de la machine virtuelle. Avec cette approche, vous devez spécifier explicitement une relation hiérarchique entre l’extension et la machine virtuelle avec les valeurs de *nom* et *type* . Par exemple : 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

L’extension est toujours associée à la machine virtuelle, vous pouvez directement définir directement sous le nœud de la ressource de la machine virtuelle ou définir au niveau de base et utiliser la dénomination hiérarchique à associer avec la machine virtuelle.

Machine virtuelle échelle jeux de la configuration des extensions spécifiée dans la propriété *extensionProfile* de la *VirtualMachineProfile*.
   
La propriété *publisher* avec la valeur de **Microsoft.Azure.Diagnostics** et la propriété de *type* avec la valeur de **IaaSDiagnostics** identifient l’extension Azure Diagnostics.

La valeur de la propriété *name* peut être utilisée pour faire référence à l’extension du groupe de ressources. La valeur spécifiquement **Microsoft.Insights.VMDiagnosticsSettings** activer l’identifier facilement par la Azure classique portail portail veiller à ce que les graphiques surveillance affichent correctement dans le portail classique Azure.

La *typeHandlerVersion* spécifie la version de l’extension que vous voulez utiliser. Définition de *autoUpgradeMinorVersion* version mineure **true** garantit que vous obtenez la dernière version mineure de l’extension est disponible. Il est recommandé de toujours définir *autoUpgradeMinorVersion* doit toujours être **true** afin que vous obtenez toujours à utiliser la dernière extension diagnostics disponibles avec toutes les nouvelles fonctionnalités et correctifs. 

L’élément *paramètres* contient des propriétés de configuration pour l’extension qui peut être défini et relire à partir de l’extension (parfois appelée configuration publique). La propriété *xmlcfg* contient la configuration basé sur xml pour les journaux de diagnostics, compteurs etc. qui seront collectées par l’agent de diagnostics. Pour plus d’informations sur le schéma xml, consultez [Schéma de Configuration de Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) . Fréquent consiste à enregistrer la configuration xml réel comme une variable dans le modèle de gestionnaire de ressources Azure et puis effectuera et en base 64 coder les pour définir la valeur pour *xmlcfg*. Consultez la section sur les [variables de configuration des diagnostics](#diagnostics-configuration-variables) pour en savoir plus sur la façon de stocker le fichier xml dans des variables. La propriété *storageAccount* indique le nom du compte de stockage auquel diagnostics données seront transférées. 
 
Les propriétés dans *protectedSettings* (parfois appelée configuration privée) peuvent être définies, mais ne peuvent pas être lus précédent après avoir été défini. La nature écriture seule de *protectedSettings* permet de stocker des secrets comme la clé de compte de stockage où les données de diagnostic seront écrit.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Spécification de compte de stockage diagnostics comme paramètres 

L’extrait de json diagnostics extension ci-dessus suppose deux paramètres *existingdiagnosticsStorageAccountName* et *existingdiagnosticsStorageResourceGroup* pour spécifier le compte de stockage diagnostics l’emplacement de stockage des données de diagnostic. Spécification du compte de stockage diagnostics comme un paramètre rend facile de modifier le compte de stockage diagnostics travers différents environnements par exemple, vous souhaiterez peut-être utiliser un compte de stockage différents diagnostics pour tester et une autre pour votre déploiement en production.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

Il est conseillé de spécifier un compte de stockage diagnostics dans un groupe de ressources autre que le groupe de ressources pour la machine virtuelle. Un groupe de ressources peut être considéré comme une unité de déploiement avec sa propre durée de vie, une machine virtuelle peut être déployée et redéployée comme nouvelles mises à jour de configurations deviennent, mais vous pouvez continuer à stocker les données de diagnostic dans le même compte de stockage dans ces machine virtuelle des déploiements. Activation du compte de stockage dans une autre ressource active le compte de stockage accepter des données à partir de plusieurs déploiements machine virtuelle ce qui facilite la résolution des problèmes sur les différentes versions.

>[AZURE.NOTE] Si vous créez un modèle de machine virtuelle windows à partir de Visual Studio du compte de stockage par défaut peut être défini pour utiliser le même compte de stockage lequel la machine virtuelle disque dur virtuel est chargée. Il s’agit pour simplifier la configuration initiale de la machine virtuelle. Le modèle pour utiliser un compte de stockage différents qui peut être passé dans en tant que paramètre du nouveau facteur. 

## <a name="diagnostics-configuration-variables"></a>Variables de configuration des Diagnostics
 
L’extrait de json diagnostics extension ci-dessus définit une variable *d’ID de compte* pour simplifier l’obtention de la clé de compte de stockage pour le stockage de diagnostics :   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


La propriété *xmlcfg* pour l’extension diagnostics est définie à l’aide de plusieurs variables concaténées. Les valeurs de ces variables sont au format xml afin qu’ils doivent être d’échappement correctement lorsque vous définissez les variables json.

Le tableau suivant décrit le code de configuration de diagnostics xml qui collecte ces compteurs niveau système standard ainsi que des journaux des événements windows et les journaux de diagnostics infrastructure. Il a été d’échappement et correctement mis en forme pour que la configuration peut être collée directement dans la section variables de votre modèle. Voir le [Schéma de Configuration Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) pour obtenir un exemple humain plus lisible de fichier xml de la configuration.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

Le nœud métrique définition xml dans la configuration ci-dessus est un élément de configuration importantes comme il définit comment les compteurs de performance définis précédemment dans le code xml de nœud *PerformanceCounter* sont regroupées et stockées. 

> [AZURE.IMPORTANT] Les mesures de lecteur les graphiques de surveillance et les alertes dans le portail Azure.  Le nœud de **mesures** avec la *resourceID* et **MetricAggregation** doit être inclus dans la configuration de diagnostics pour votre machine virtuelle si vous voulez voir les données d’analyse machine virtuelle dans le portail Azure. 

Voici un exemple du code xml pour les définitions de mesures : 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

L’attribut *resourceID* identifie de façon unique la machine virtuelle dans votre abonnement. Veillez à utiliser les fonctions subscription() et resourceGroup() afin que le modèle met automatiquement à jour ces valeurs en fonction de l’abonnement et le groupe de ressources que vous effectuez le déploiement.

Si vous créez plusieurs Machines virtuelles d’une boucle vous devrez remplir la valeur *resourceID* avec une fonction copyIndex() pour différencier correctement chaque machine virtuelle individuel. La valeur *xmlCfg* peut être mis à jour pour prendre en charge cette comme suit :  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

La valeur MetricAggregation de *PT1H* et *PT1M* indiquer une agrégation par minute et une agrégation plus d’une heure.

## <a name="wadmetrics-tables-in-storage"></a>Tables de WADMetrics de stockage

La configuration de mesures ci-dessus générera des tables dans votre compte de stockage diagnostics avec les conventions suivantes :

- **WADMetrics** : préfixe Standard pour tous les tableaux WADMetrics
- **PT1H** ou **PT1M** : indique que la table contient agréger les données plus 1 heure ou une minute
- **P10D** : indique la table contient des données 10 jours à partir de la table de démarrage de collecte des données
- **V2S** : constante de chaîne
- **AAAAMMJJ** : la date à laquelle la table a démarré collecte des données

Exemple : *WADMetricsPT1HP10DV2S20151108* contient les données métriques agrégées plus d’une heure pour 10 jours en commençant à 11-Nov-2015    

Chaque table WADMetrics doit contenir les colonnes suivantes :

- **PartitionKey**: le partitionkey est construit selon la valeur *resourceID* pour identifier la ressource machine virtuelle. pour, par exemple : 002Fsubscriptions :<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : suit le format <Descending time tick>:<Performance Counter Name>. Le calcul de graduation heure décroissant est graduations temps max moins l’heure de début de la période d’agrégation. Par exemple Si la période d’échantillonnage démarré sur 10-Nov-2015 et 00:00Hrs UTC puis le calcul serait : DateTime.MaxValue.Ticks - (nouveau DateTime(2015,11,10,0,0,0,DateTimeKind.Utc). Graduations). Comme pour la mémoire la clé de ligne de compteur de performance octets disponibles ressemblera : 2519551871999999999__:005CMemory:005CAvailable:0020 octets
- **CounterName** : est le nom du compteur de performance. Cela correspond à la *counterSpecifier* définis dans le fichier xml config.
- **Maximale** : la valeur maximale du compteur de performance pendant la période d’agrégation.
- **Minimale** : la valeur minimale du compteur de performance pendant la période d’agrégation.
- **Total** : la somme de toutes les valeurs du compteur de performance signalées pendant la période d’agrégation.
- **Nb** : le nombre total de valeurs reportée pour le compteur de performance.
- **Moyenne** : la valeur moyenne (total/count) du compteur de performance pendant la période d’agrégation.


## <a name="next-steps"></a>Étapes suivantes

- Pour un modèle d’exemple complet d’une machine virtuelle Windows avec l’extension diagnostics voir [201-machine virtuelle-surveillance-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Déployez le modèle de gestionnaire de ressources à l’aide de [PowerShell Azure](virtual-machines-windows-ps-manage.md) ou [ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md)
- Pour plus d’informations sur les [modèles de programmation Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md)







