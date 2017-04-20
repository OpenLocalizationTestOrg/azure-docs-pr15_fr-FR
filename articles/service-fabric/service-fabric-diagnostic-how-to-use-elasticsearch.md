<properties
   pageTitle="À l’aide de Elasticsearch en tant que Service TISSU application trace magasin | Microsoft Azure"
   description="Décrit comment les applications de Service TISSU peuvent utiliser Elasticsearch et Kibana pour stocker, index et effectuer des recherches dans les traces application (journaux)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Utiliser Elasticsearch comme une trace de l’application Service TISSU stocker
## <a name="introduction"></a>Introduction
Cet article décrit comment les applications [Azure Service TISSU](https://azure.microsoft.com/documentation/services/service-fabric/) peuvent utiliser des **Elasticsearch** et **Kibana** pour le stockage de repérer l’application, l’indexation et la recherche. [Elasticsearch](https://www.elastic.co/guide/index.html) est un open source, distribué et scalable en temps réel recherche et analytique moteur qui n’est adapté pour cette tâche. Il peut être installé sur Windows et Linux machines virtuelles en cours d’exécution dans Microsoft Azure. Elasticsearch peut traiter efficacement *structurées* traces produites à l’aide de technologies d’assistance comme **Suivi d’événements pour Windows (ETW)**.

ETW est utilisé par runtime tissu de Service pour des informations de diagnostic source (traces). Il est recommandé pour les applications de Service tissu à trop de source de leurs informations de diagnostic. L’utilisation du même mécanisme permet de corrélation entre traces fournie par runtime et fourni par l’application et facilite la résolution des problèmes. Modèles de projet tissu de service dans Visual Studio incluent une API de journalisation (basée sur la classe .NET **source d’événement** ) qui émet suivis ETW par défaut. Pour obtenir une vue d’ensemble de traçage d’application tissu de Service à l’aide de ETW, consultez [surveillance et diagnostic services dans une configuration de développement ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Pour les traces s’afficheront dans Elasticsearch, ils doivent être capturé dans les nœuds du cluster Service tissu en temps réel (pendant l’exécution de l’application) et envoyé à un point de terminaison Elasticsearch. Il existe deux options principales pour capturer les repérer :

+ **Trace capture en cours**  
L’application, ou plus précisément les processus de service, sont responsable de l’envoi de données de diagnostic au magasin trace (Elasticsearch).

+ **Capture de suivi des messages hors de processus**  
Un agent séparé est capture traces du processus de service ou d’un processus et en les envoyant dans le magasin de suivi.

Ci-dessous, nous décrivent comment configurer Elasticsearch sur Azure, discuter des avantages et inconvénients pour les deux options de saisie et expliquent comment configurer un service de tissu de Service pour envoyer des données à Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Configurer la Elasticsearch sur Azure
La façon la plus simple pour configurer le service Elasticsearch sur Azure consiste à utiliser [**le Gestionnaire de ressources Azure modèles**](../azure-resource-manager/resource-group-overview.md). Un [modèle de démarrage rapide pour le Gestionnaire de ressources Azure pour Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) complète est disponible à partir du référentiel de modèles de démarrage rapide Azure. Ce modèle utilise des comptes de stockage distincte unités d’échelle (groupes de nœuds). Il peut également mise en service des clients et des nœuds de serveur avec des configurations différentes et distincts différents numéros de disques de données pièce jointes.

Ici, nous utilisons un autre modèle, appelé **ES multiposte** à partir du [référentiel Azure outils de diagnostic](https://github.com/Azure/azure-diagnostics-tools). Ce modèle est plus facile à utiliser, et qu’il crée un cluster Elasticsearch protégé par l’authentification de base HTTP. Avant de poursuivre, téléchargez le référentiel à partir de GitHub sur votre ordinateur (en clonant le référentiel ou télécharger un fichier zip). Le modèle ES multiposte se trouve dans le dossier du même nom.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Préparer un ordinateur pour exécuter les scripts d’installation Elasticsearch
Pour utiliser le modèle ES multiposte le plus simple consiste à utiliser un script PowerShell Azure fourni appelé `CreateElasticSearchCluster`. Pour utiliser ce script, vous devez installer les modules PowerShell et un outil appelé **openssl**. Ce dernier est nécessaire pour la création d’un code SSH pouvant être utilisées pour administrer votre cluster Elasticsearch à distance.

`CreateElasticSearchCluster`script est conçu pour faciliter leur utilisation avec le modèle ES multiposte à partir d’un ordinateur Windows. Il est possible d’utiliser le modèle sur un ordinateur non Windows, mais ce scénario est dépasse le cadre de cet article.

1. Si vous n’avez pas installé les déjà, installez [**PowerShell Azure modules**](http://aka.ms/webpi-azps). Lorsque vous y êtes invité, cliquez sur **exécuter**, puis **installer**. PowerShell Azure 1.3 ou version ultérieure est requise.

2. L’outil **openssl** est inclus dans la distribution de [**Git pour Windows**](http://www.git-scm.com/downloads). Si vous n’avez pas déjà fait, installez maintenant [Git pour Windows](http://www.git-scm.com/downloads) . (Les options d’installation par défaut sont OK).

3. En supposant que Git a été installé, mais ne pas inclus dans le chemin d’accès système, ouvrez une fenêtre de Microsoft Azure PowerShell et exécutez les commandes suivantes :

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Remplacer le `<Git installation folder>` avec l’emplacement Git sur votre ordinateur ; la valeur par défaut est **« C:\Program Files\Git »**. Notez le point-virgule au début de la première trajectoire.

4. Vérifiez que vous êtes connecté à Azure (via [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) applet de commande) et que vous avez sélectionné l’abonnement qui doit être utilisé pour créer votre cluster recherche élastique. Vous pouvez vérifier que l’abonnement correcte est sélectionnée à l’aide de `Get-AzureRmContext` et `Get-AzureRmSubscription` applets de commande.

5. Si vous n’avez pas déjà fait, modifiez le répertoire actuel vers le dossier ES multiposte.

### <a name="run-the-createelasticsearchcluster-script"></a>Exécuter le script CreateElasticSearchCluster
Avant d’exécuter le script, ouvrez le `azuredeploy-parameters.json` classer et vérifiez ou indiquez les valeurs pour les paramètres de script. Vous trouverez les paramètres suivants :

|Nom du paramètre           |Description|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Le nom qui est utilisé pour créer le nom DNS visible publiquement pour le cluster recherche élastique (en ajoutant le domaine région Azure sur le nom fourni). Par exemple, si cette valeur de paramètre est « myBigCluster » et la région Azure choisie est US ouest, le nom DNS qui en résulte pour le cluster est myBigCluster.westus.cloudapp.azure.com. <br /><br />Ce nom sert également un nom racine pour plusieurs objets associé au cluster élastique recherche, tels que les noms des nœuds de données.|
|adminUsername           |Le nom du compte d’administrateur de gestion de cluster recherche élastique (clés SSH correspondantes sont générées automatiquement).|
|dataNodeCount           |Le nombre de nœuds dans le cluster recherche élastique. La version actuelle du script ne distingue pas les nœuds de données et requête ; tous les nœuds de lire les deux rôles. Par défaut, 3 nœuds.|
|dataDiskSize            |La taille des données disques (Go) allouée pour chaque nœud de données. Chaque nœud reçoit 4 disques de données, en mode exclusif dédiés au service de recherche élastique.|
|région                  |Le nom de la région Azure où le cluster élastique recherche doit se trouver.|
|esUserName              |Le nom d’utilisateur de l’utilisateur qui est configuré pour avoir accès au cluster ES (soumis à l’authentification de base HTTP). Le mot de passe ne fait pas partie du fichier de paramètres et doit être fourni lorsque `CreateElasticSearchCluster` script est appelé.|
|vmSizeDataNodes         |La taille de la machine virtuelle Azure pour élastique rechercher les nœuds de cluster. Par défaut, Standard_D2.|

Vous êtes maintenant prêt à exécuter le script. Exécutez la commande suivante :

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

où 

|Nom du paramètre de script    |Description|
|-----------------------  |--------------------------|
|`<es-group-name>`        |Le nom du groupe de ressources Azure contenant toutes les ressources de cluster recherche élastique.|
|`<azure-region>`         |Le nom de la région Azure où le cluster recherche élastique doit être créé.|         
|`<es-password>`          |Le mot de passe pour l’utilisateur recherche élastique.|

>[AZURE.NOTE] Si vous obtenez une exception NullReferenceException à partir de l’applet de commande Test-AzureResourceGroup, vous avez oublié se connecter à Azure (`Add-AzureRmAccount`).

Si vous obtenez une erreur d’exécuter le script et que vous constatez que l’erreur a été provoquée par une valeur de paramètre modèle incorrecte, corrigez le fichier de paramètres et réexécuter le script avec un nom de groupe de ressources différents. Vous pouvez également réutiliser le même nom de groupe de ressources et que le script nettoyer l’ancien en ajoutant le `-RemoveExistingResourceGroup` paramètre à l’appel de script.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Résultat de l’exécution du script CreateElasticSearchCluster
Après avoir exécuté la `CreateElasticSearchCluster` script, les objets principales suivantes seront créés. Pour cet exemple, nous supposons que vous avez utilisé « myBigCluster » comme valeur de la `dnsNameForLoadBalancerIP` paramètre et que la zone où vous avez créé le cluster peut US ouest.

|Objet|Nom, l’emplacement et remarques|
|----------------------------------|----------------------------------|
|Code SSH pour l’administration distante |fichier myBigCluster.key (dans le répertoire à partir duquel la CreateElasticSearchCluster a été exécuté). <br /><br />Ce fichier clé peut être utilisé pour vous connecter au nœud d’administration et (via le nœud administrateur) pour des nœuds de données dans le cluster.|
|Nœud Admin                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />Une machine virtuelle dédiée pour l’administration distante Elasticsearch cluster--la seule qui autorise les connexions SSH externes. Il s’exécute sur le même réseau virtuel que tous les nœuds de cluster Elasticsearch, mais il ne s’exécute pas tous les services Elasticsearch.|
|Nœuds de données                        |myBigCluster1... myBigCluster*N* <br /><br />Nœuds de données qui exécutent les services Elasticsearch et Kibana. Vous pouvez vous connecter viaSSH à chaque nœud, mais uniquement via le nœud d’administration.|
|Cluster Elasticsearch             |http://myBigCluster.westus.cloudapp.Azure.com/es/ <br /><br />Point de terminaison principal pour le cluster Elasticsearch (Notez le suffixe /es). Il est protégé par l’authentification HTTP basique (les informations d’identification ont été les paramètres spécifiés esUserName/esPassword du modèle ES multiposte). Le cluster présente également la tête plug-in installée (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) pour l’administration cluster de base.|
|Service Kibana                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />Le service Kibana a été configurée pour afficher les données à partir du cluster Elasticsearch créé. Il est protégé par les mêmes informations d’authentification en tant que le cluster lui-même.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>Processus et hors de processus trace capture
Dans l’introduction, nous l’avons mentionné deux méthodes principales de la collecte de données de diagnostic : en cours et hors du processus. Chacun a forces et faiblesses.

La **trace capture en cours** présentent les avantages suivants :

1. *Déploiement et simplifier la configuration*

    * La configuration de la collecte de données de diagnostic est uniquement une partie de la configuration d’application. Il est facile de toujours conserver « synchronisé » avec le reste de l’application.

    * Configuration de l’application ou par service est facilement réalisable.

    * Absence de processus trace capture nécessite généralement un déploiement séparé et la configuration de l’agent de diagnostic, ce qui correspond à une tâche administrative supplémentaire et une source potentielle d’erreurs. La technologie agent particulier permet souvent qu’une seule instance de l’agent par machine virtuelle (nœud). Cela signifie que la configuration de la collection de la configuration des diagnostics est partagée par toutes les applications et services qui s’exécutent sur ce nœud.

2. *Flexibilité*

    * L’application peut envoyer les données à l’endroit où il a besoin accéder, dans la mesure où il est une bibliothèque de client qui prend en charge le système de stockage ciblé. Nouveau récepteurs peuvent être ajoutés selon les besoins.

    * Règles de capture, les filtres et agrégation de données complexes peuvent être implémentées.

    * Une trace hors de processus capture est souvent limité par les récepteurs de données qui prend en charge de l’agent. Certains agents sont extensibles.

3. *Accès aux données d’application interne et le contexte*

    * Le diagnostic sous-système en cours d’exécution dans le processus de service d’application peut accroître facilement les traces avec des informations contextuelles.

    * Dans l’approche hors du processus, les données doivent être envoyées à un agent via un mécanisme de communication entre les processus, tel que Event Tracing for Windows. Ce mécanisme peut imposer des limitations supplémentaires.

La **trace hors de processus capture** présente les avantages suivants :

1. *La possibilité de contrôler l’application et recueillir blocage vide*

    * Dans le processus trace capture peut être échoue si l’application ne démarre pas ou se bloque. Un agent indépendant a une meilleure chance de capturer les informations de dépannage cruciales.<br /><br />

2. *L’argument échéance, fiabilité et performances éprouvées*

    * Un agent développé par un éditeur de plateforme (par exemple, un agent Microsoft Azure Diagnostics) a été soumis à des tests rigoureux et la sécurisation de bataille.

    * Dans le processus trace capture, soins doivent être prises pour vous assurer que l’activité de l’envoi de données de diagnostic à partir d’un processus d’application ne pas interférer avec les tâches principales de l’application ou provoquer des problèmes de minutage ou les performances. Un agent séparément en cours d’exécution est moins sujet à ces problèmes et est conçu pour limiter son impact sur le système.

Il est possible de combiner et bénéficier de ces deux méthodes. En effet, il peut être la meilleure solution pour de nombreuses applications.

Ici, nous utilisons la **bibliothèque Microsoft.Diagnostic.Listeners** et le suivi dans le processus de capture pour envoyer des données à partir d’une application de Service tissu à un cluster Elasticsearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Utiliser la bibliothèque récepteurs pour envoyer des données de diagnostic à Elasticsearch
La bibliothèque Microsoft.Diagnostic.Listeners fait partie d’application de Service TISSU PartyCluster exemple. Utiliser :

1. Téléchargez [l’échantillon PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) à partir de GitHub.

2. Copiez les projets Microsoft.Diagnostics.Listeners et Microsoft.Diagnostics.Listeners.Fabric (dossiers entiers) à partir du répertoire exemple PartyCluster dans le dossier de solution de l’application qui est censée pour envoyer les données à Elasticsearch.

3. Ouvrez la solution cible, cliquez sur le nœud solution dans l’Explorateur de solutions, puis sélectionnez **Ajouter un projet existant**. Ajouter le projet Microsoft.Diagnostics.Listeners à la solution. Répétez les mêmes étapes pour le projet Microsoft.Diagnostics.Listeners.Fabric.

4. Ajouter une référence de projet à partir de vos projets de service pour les deux projets ajoutés. (Chaque service qu’il est supposé pour envoyer des données à Elasticsearch doit faire référence à Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric).

    ![Références de projet aux bibliothèques Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Disponibilité générales du tissu service release et package Microsoft.Diagnostics.Tracing Nuget
Les applications créées avec la version de disponibilité du Service TISSU général (2.0.135, publié le 31 mars 2016) ciblent **.NET Framework 4.5.2**. Cette version est la version la plus récente du .NET Framework pris en charge par Azure au moment de la version disponible. Malheureusement, cette version de l’infrastructure ne dispose pas certaines APIs EventListener nécessitant la bibliothèque Microsoft.Diagnostics.Listeners. Étant donné que la source d’événement (composant formant la base de la journalisation des API dans les applications TISSU) et EventListener sont étroitement, chaque projet qui utilise la bibliothèque Microsoft.Diagnostics.Listeners doit utiliser une autre implémentation de la source d’événement. Cette implémentation est fournie par le **package Microsoft.Diagnostics.Tracing Nuget**, créés par Microsoft. Le package est entièrement compatibles en amont avec source d’événement inclus dans le cadre, aucune modification de code ne doit être nécessaire que les modifications de l’espace de noms référencé.

Pour commencer à utiliser l’implémentation Microsoft.Diagnostics.Tracing de la classe source d’événement, procédez comme suit pour chaque projet de service qui a besoin d’envoyer des données à Elasticsearch :

1. Avec le bouton droit sur le projet de service, puis sélectionnez **Manage Nuget Packages**.

2. Basculer vers la source du package nuget.org (si ce n’est pas déjà fait) et recherchez «**Microsoft.Diagnostics.Tracing**».

3. Installer le `Microsoft.Diagnostics.Tracing.EventSource` package (et ses dépendances).

4. Ouvrez le fichier **ServiceEventSource.cs** ou **ActorEventSource.cs** dans votre projet de service et remplacez le `using System.Diagnostics.Tracing` directives au-dessus du fichier avec la `using Microsoft.Diagnostics.Tracing` directive.

Ces étapes ne sera pas nécessaires une fois que le **.NET Framework 4.6** est pris en charge par Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Configuration et l’instanciation récepteur Elasticsearch
La dernière étape pour envoyer des données de diagnostic à Elasticsearch consiste à créer une instance de `ElasticSearchListener` et configurez-le avec les données de connexion Elasticsearch. Que l’auditeur capture automatiquement tous les événements déclenchés via classes source d’événement définies dans le projet de service. Il doit être active pendant la durée de vie du service, est l’endroit idéal pour créer votre document dans le code de l’initialisation de service. Voici comment le code d’initialisation d’un service sans état peut ressembler une fois les modifications nécessaires (ajouts remarquer dans les commentaires en commençant par `****`) :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Données de connexion Elasticsearch doivent être placées dans une section distincte dans le fichier de configuration de service (**PackageRoot\Config\Settings.xml**). Le nom de la section doit correspondre à la valeur passée à la `FabricConfigurationProvider` constructeur, par exemple :

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Les valeurs de `serviceUri`, `userName` et `password` paramètres correspondent à l’adresse de point de terminaison Elasticsearch cluster, Elasticsearch nom d’utilisateur et mot de passe, respectivement. `indexNamePrefix`est le préfixe d’index Elasticsearch ; la bibliothèque Microsoft.Diagnostics.Listeners crée un index pour ses données quotidiennement.

### <a name="verification"></a>Vérification
Voilà ! À présent, chaque fois que le service est exécuté, il commence à envoyer traces au service Elasticsearch spécifié dans la configuration. Vous pouvez vérifier ceci en ouvrant le Kibana UI associé à l’instance de Elasticsearch cible. Dans notre exemple, l’adresse de page est http://myBigCluster.westus.cloudapp.azure.com/. Vérifiez qu’index avec le préfixe du nom choisi pour la `ElasticSearchListener` instance ont bien été créés et remplis avec des données.

![Événements d’application affichant Kibana PartyCluster][2]

## <a name="next-steps"></a>Étapes suivantes
- [Pour plus d’informations sur le diagnostic et un service Service tissu de surveillance](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
