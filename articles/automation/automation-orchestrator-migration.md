<properties
   pageTitle="Migration d’Orchestrator à Automation Azure | Microsoft Azure"
   description="Décrit comment migrer des modules procédures opérationnelles et l’intégration de System Center Orchestrator à Automation Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migration d’Orchestrator vers Azure Automation (version bêta)

Procédures opérationnelles dans [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) sont basées sur les activités de packs d’intégration qui sont créés spécialement pour Orchestrator procédures opérationnelles dans Azure Automation sont basés sur Windows PowerShell.  [Graphique procédures opérationnelles](automation-runbook-types.md#graphical-runbooks) dans Azure Automation ont une apparence semblable aux procédures opérationnelles Orchestrator avec leurs activités représentant des applets de commande PowerShell, procédures opérationnelles enfant et actifs.

Le [Kit de Migration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclut des outils pour vous aider lors de la conversion des procédures opérationnelles d’Orchestrator à Automation Azure.  En plus de convertir les procédures opérationnelles eux-mêmes, vous devez convertir les modules de l’intégration avec les activités qui utilisent les procédures opérationnelles modules d’intégration avec les applets de commande Windows PowerShell.  

Voici la procédure de base pour la conversion des procédures opérationnelles Orchestrator d’automatisation Azure.  Chacune de ces étapes est décrite en détail dans les sections ci-dessous.

1.  Téléchargez le [Kit de Migration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) qui contient les outils et les modules mentionnés dans cet article.
2.  Importer des [activités Standard Module](#standard-activities-module) dans Automation Azure.  Cela inclut les versions converties d’activités Orchestrator standards qui peuvent être utilisées par les procédures opérationnelles converti.
3.  Importer des [Modules d’intégration System Center Orchestrator](#system-center-orchestrator-integration-modules) dans Azure Automation pour ces packs d’intégration utilisées par les procédures opérationnelles qui accèdent à System Center.
4.  Convertir des packs d’intégration tiers et personnalisés à l’aide du [Convertisseur du Pack de l’intégration](#integration-pack-converter) et y importer Automation Azure.
5.  Convertir des procédures opérationnelles Orchestrator à l’aide du [Convertisseur Runbook](#runbook-converter) et installez dans Azure Automation.
6.  Créer manuellement des biens Orchestrator requis dans Azure Automation dans la mesure où le convertisseur Runbook ne convertit pas ces ressources.
7.  Configurer un [Environnement hybride Runbook collaborateur](#hybrid-runbook-worker) dans votre centre de données locales pour exécuter les procédures opérationnelles converti accédera ressources locales.

## <a name="service-management-automation"></a>Service gestion Automation

[Service gestion Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) stocke et exécute des procédures opérationnelles dans votre centre de données locales comme Orchestrator, et qu’il utilise les modules d’intégration même qu’Azure Automation. Le [Convertisseur Runbook](#runbook-converter) convertit procédures opérationnelles Orchestrator procédures opérationnelles graphique mais qui ne sont pas prises en charge dans SMA.  Vous pouvez toujours installer le [Module activités Standard](#standard-activities-module) et les [Modules d’intégration System Center Orchestrator](#system-center-orchestrator-integration-modules) dans SMA, mais vous devez manuellement [la réécriture des procédures opérationnelles](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Employé consultant Runbook hybride

Procédures opérationnelles dans Orchestrator sont stockés sur un serveur de base de données et exécuter sur des serveurs runbook, à la fois dans votre centre de données locales.  Procédures opérationnelles dans Azure Automation sont stockées dans le cloud Azure et peuvent s’exécuter dans votre centre de données locales à l’aide d’un [Environnement hybride Runbook concerné](automation-hybrid-runbook-worker.md).  Voici comment vous exécuterez généralement procédures opérationnelles converti à partir d’Orchestrator dans la mesure où ils sont conçus pour s’exécuter sur des serveurs locaux.

## <a name="integration-pack-converter"></a>Convertisseur du Pack de l’intégration

Le convertisseur du Pack d’intégration packs d’intégration qui ont été créés à l’aide de [Boîte à outils d’intégration Orchestrator (OIT)](http://technet.microsoft.com/library/hh855853.aspx) pour les modules d’intégration en fonction de Windows PowerShell qui peuvent être importées dans Azure Automation ou Service gestion Automation.  

Lorsque vous exécutez le convertisseur Pack intégration, vous sont présentées avec un Assistant qui vous permet de sélectionner un fichier d’intégration pack (.oip).  L’Assistant répertorie les activités incluses dans ce pack intégration ensuite et vous pouvez sélectionner qui vont être migré.  Lorsque vous exécutez l’Assistant, il crée un module d’intégration qui inclut une applet de commande correspondant pour chacune des activités dans le module d’intégration d’origine.


### <a name="parameters"></a>Paramètres

Les propriétés d’une activité dans le module intégration sont converties en paramètres de l’applet de commande correspondante dans le module d’intégration.  Applets de commande Windows PowerShell ont un jeu de [paramètres communs](http://technet.microsoft.com/library/hh847884.aspx) qui peuvent être utilisés avec toutes les applets de commande.  Par exemple, le détaillée paramètre - entraîne une applet de commande copier des informations détaillées sur son fonctionnement.  Aucune applet de commande ne peut comporter un paramètre avec le même nom qu’un paramètre commun.  Si une activité a une propriété portant le même nom qu’un paramètre commun, l’Assistant vous invite à fournir un autre nom pour le paramètre.

### <a name="monitor-activities"></a>Surveillance des activités

Moniteur procédures opérationnelles dans Orchestrator commencent avec une [surveiller l’activité](http://technet.microsoft.com/library/hh403827.aspx) et exécuter en continu en attente d’être appelé par un événement particulier.  Automatisation Azure ne reconnaît pas les procédures opérationnelles moniteur, afin que toutes les activités moniteur dans le module intégration ne seront pas converties.  En revanche, une applet de commande espace réservé est créé dans le module d’intégration de l’activité moniteur.  Cette applet de commande n’a aucune fonctionnalité, mais elle permet de n’importe quel runbook converti qui utilise doit être installé.  Cette procédure opérationnelle ne sera pas en mesure d’exécuter dans Azure Automation, mais elle peut être installée afin que vous pouvez le modifier.

### <a name="integration-packs-that-cannot-be-converted"></a>Packs d’intégration qui ne peut pas être convertis

Impossible de convertir packs d’intégration qui n’ont pas été créés avec OIT avec le convertisseur Pack intégration. Il existe également certains packs d’intégration fournis par Microsoft qui actuellement ne peut pas être convertie avec cet outil.  Versions converties de ces packs d’intégration ont été [fourni pour le téléchargement](#system-center-orchestrator-integration-modules) afin qu’ils peuvent être installés dans Azure Automation ou Service gestion Automation.


## <a name="standard-activities-module"></a>Module d’activités standard

Orchestrator comprend un ensemble d' [activités standards](http://technet.microsoft.com/library/hh403832.aspx) qui ne figurent pas dans un module d’intégration mais sont utilisées par les nombreuses procédures opérationnelles.  Le module activités Standard est un module d’intégration qui inclut une applet de commande équivalente pour chacune de ces activités.  Vous devez installer le module d’intégration dans Azure automatisation avant d’importer les procédures opérationnelles convertie qui utilisent une activité standard.

Outre les procédures opérationnelles convertie de prise en charge, les applets de commande dans le module activités standard peuvent être utilisées par une personne connaissant Orchestrator permettant de créer de nouvelles procédures opérationnelles dans Azure Automation.  Tandis que les fonctionnalités de toutes les activités standards peuvent être exécutées avec les applets de commande, ils peuvent fonctionner différemment.  Les applets de commande dans le module activités standard converti sera sont les mêmes que leurs activités correspondantes et utilisez les mêmes paramètres.  Cela peut aider l’auteur du runbook Orchestrator existant dans leur transition vers Azure Automation procédures opérationnelles.

## <a name="system-center-orchestrator-integration-modules"></a>Modules d’intégration System Center Orchestrator

Microsoft fournit des [packs d’intégration](http://technet.microsoft.com/library/hh295851.aspx) pour la création de procédures opérationnelles pour automatiser des composants de System Center et d’autres produits.  Certaines de ces packs d’intégration sont actuellement basés sur OIT mais que vous ne pouvez pas actuellement converties en modules d’intégration en raison de problèmes connus.  [Modules d’intégration System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) inclut des versions converties de ces packs d’intégration qui peuvent être importés dans Azure Automation et automatisation de gestion des services.  

Par la version RTM de cet outil, les versions mises à jour de packs d’intégration en fonction de OIT pouvant être converties avec le convertisseur Pack intégration seront publiées.  Conseils seront également fournies pour vous aider à convertir les procédures opérationnelles à l’aide des activités de packs d’intégration non basés sur OIT.

## <a name="runbook-converter"></a>Convertisseur Runbook

Le convertisseur Runbook convertit procédures opérationnelles Orchestrator [procédures opérationnelles graphiques](automation-runbook-types.md#graph-runbooks) qui peuvent être importés dans Azure Automation.  

Convertisseur Runbook est implémenté comme un module PowerShell avec une applet de commande appelé **ConvertFrom SCORunbook** qui effectue la conversion.  Lorsque vous installez l’outil, il crée un raccourci vers une session PowerShell qui charge l’applet de commande.   

Voici la procédure de base pour convertir un runbook Orchestrator et importez-le dans Azure Automation.  Les sections suivantes fournissent des détails supplémentaires sur à l’aide de l’outil et l’utilisation de procédures opérationnelles converti.

1. Exporter une ou plusieurs procédures opérationnelles de Orchestrator.
2. Obtenir des modules d’intégration pour toutes les activités de la procédure opérationnelle.
3. Convertir les procédures Orchestrator opérationnelles dans le fichier exporté.
4. Passez en revue les informations dans les journaux pour valider la conversion et pour déterminer les tâches manuelles requis.
4. Importer des procédures opérationnelles converti dans Azure Automation.
5. Créer les éléments requis dans Azure Automation.
6. Modifier la runbook dans Azure Automation pour modifier les activités requises.

### <a name="using-runbook-converter"></a>À l’aide du convertisseur Runbook

La syntaxe de **ConvertFrom SCORunbook** est la suivante :

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - chemin d’accès au fichier d’exportation contenant les procédures opérationnelles à convertir.
- Module - virgule liste délimitée par des modules d’intégration contenant des activités dans les procédures opérationnelles.
- OutputFolder - chemin d’accès au dossier pour créer converti procédures opérationnelles graphique. 


La commande suivante convertit les procédures opérationnelles dans un fichier d’exportation appelé **MyRunbooks.ois_export**.  Ces procédures opérationnelles utilisent packs d’intégration Active Directory et du Gestionnaire de Protection des données.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>Fichiers journaux

Le convertisseur Runbook créera les fichiers journaux suivants dans le même emplacement que le runbook converti.  Si les fichiers existent déjà, ils seront remplacées avec les informations de la dernière conversion.

| Fichier | Contenu |
|:---|:---|
| Convertisseur Runbook - Progress.log | Procédure détaillée de la conversion incluant des informations pour chaque activité correctement convertie et avertissement pour chaque activité ne pas convertie. |
| Convertisseur Runbook - Summary.log  | Résumé de la dernière conversion, y compris les messages d’avertissement et suivi des tâches que vous devez effectuer telles que la création d’une variable requise pour la runbook converti.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportation des procédures opérationnelles des Orchestrator

Le convertisseur Runbook fonctionne avec un fichier d’exportation de Orchestrator qui contient une ou plusieurs procédures opérationnelles.  Il crée un runbook Azure Automation correspondant pour chaque runbook Orchestrator dans le fichier d’exportation.  

Pour exporter un runbook Orchestrator, double-cliquez sur le nom de la procédure opérationnelle dans le concepteur Runbook et sélectionnez **Exporter**.  Pour exporter tous les procédures opérationnelles dans un dossier, double-cliquez sur le nom du dossier, puis sélectionnez **Exporter**.


### <a name="runbook-activities"></a>Activités Runbook

Le convertisseur Runbook convertit chaque activité dans le runbook Orchestrator à une activité correspondante dans Azure Automation.  Pour ces activités qui ne peut pas être converties, une activité de l’espace réservé est créée dans la procédure opérationnelle avec texte d’avertissement.  Après avoir importé le runbook converti dans Azure Automation, vous devez remplacer un de ces activités avec les activités valides qui exécutent les fonctions requises.

Toutes les activités Orchestrator dans le [Module activités Standard](#standard-activities-module) seront converties.  Il existe certaines activités Orchestrator standards qui mais ne sont pas dans ce module et ne sont pas converties.  Par exemple, **Envoyer un événement de plate-forme** n’a aucune Automation Azure équivalente dans la mesure où l’événement est spécifique à Orchestrator.

[Surveillance des activités](https://technet.microsoft.com/library/hh403827.aspx) ne sont pas convertis car il n’existe aucun équivalent leur dans Azure Automation.  L’exception des activités moniteur dans [converti packs d’intégration](#integration-pack-converter) qui sera convertie en l’activité de l’espace réservé.

Toute activité à partir d’un [converti pack intégration](#integration-pack-converter) sera convertie si vous spécifiez le chemin d’accès pour le module d’intégration avec le paramètre de **modules** .  Pour les Packs d’intégration système Centre, vous pouvez utiliser les [Modules d’intégration System Center Orchestrator](#system-center-orchestrator-integration-modules).


### <a name="orchestrator-resources"></a>Ressources Orchestrator

Le convertisseur Runbook convertit uniquement les procédures opérationnelles, pas d’autres ressources Orchestrator tels que des compteurs, des variables ou des connexions.  Compteurs ne sont pas pris en charge dans Azure Automation.  Variables et les connexions sont prises en charge, mais vous devez les créer manuellement.  Les fichiers journaux seront vous informe si la runbook requiert ces ressources et spécifier des ressources correspondantes dont vous avez besoin pour créer dans Azure Automation pour la runbook converti fonctionner correctement.

Par exemple, un runbook peut utiliser une variable pour remplir une valeur particulière dans une activité.  La procédure opérationnelle converti sera convertir l’activité et spécifiez un variable bien dans Azure Automation portant le même nom que la variable Orchestrator.  Ceci est indiqué dans le fichier du **Convertisseur Runbook - Summary.log** créé après la conversion.  Vous devrez créer manuellement ce bien variable dans Azure automatisation avant d’utiliser la procédure opérationnelle. 

 
### <a name="input-parameters"></a>Paramètres d’entrée

Procédures opérationnelles dans Orchestrator acceptent des paramètres d’entrée avec l’activité de **l’Initialisation de données** .  Si la procédure opérationnelle en cours de conversion comprend cette activité, un [paramètre d’entrée](automation-graphical-authoring-intro.md#runbook-input-and-output) dans la procédure opérationnelle Azure Automation est créé pour chaque paramètre de l’activité.  Une activité de [contrôle de Script de flux de travail](automation-graphical-authoring-intro.md#activities) est créée dans la procédure opérationnelle converti qui extrait et retourne chaque paramètre.  Toutes les activités dans la procédure opérationnelle qui utilisent un paramètre d’entrée font référence à la sortie de cette activité.

La raison pour laquelle que cette stratégie est utilisée consiste à mieux refléter la fonctionnalité dans le runbook Orchestrator.  Activités de nouvelles procédures opérationnelles graphique doivent faire référence directement pour entrer les paramètres à l’aide d’une source de données d’entrée de procédure opérationnelle.


### <a name="invoke-runbook-activity"></a>Appeler Runbook activité

Procédures opérationnelles dans Orchestrator précédez l’activité **Runbook appeler** d’autres procédures opérationnelles. Si la runbook en cours de conversion inclut cette activité et que l’option **attendre la fin** est définie, une activité runbook est créée pour lui dans la procédure opérationnelle converti.  Si l’option **attendre la fin** n’est pas définie, une activité de Script de flux de travail est créée par **AzureAutomationRunbook de démarrer** pour démarrer la procédure opérationnelle.  Après avoir importé le runbook converti dans Azure Automation, vous devez modifier cette activité avec les informations indiquées dans l’activité.




## <a name="related-articles"></a>Articles connexes

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service gestion Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [Employé consultant Runbook hybride](automation-hybrid-runbook-worker.md)
- [Activités Standard Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
- [Téléchargez System Center Orchestrator Migration boîte à outils](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
