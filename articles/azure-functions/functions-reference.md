<properties
    pageTitle="Référence du développeur fonctions Azure | Microsoft Azure"
    description="Comprendre les concepts de fonctions Azure et les composants qui sont communes à toutes les langues et les liaisons."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, cluster dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Référence du développeur fonctions Azure

Fonctions Azure partagent quelques concepts techniques core et des composants, quelle que soit la langue ou la liaison que vous utilisez. Avant de passer à la formation des détails spécifiques à une langue donnée ou d’une liaison, veillez à lire cette présentation s’applique à tous les éléments.

Cet article suppose que vous avez déjà lus [vue d’ensemble des fonctions Azure](functions-overview.md) et que vous connaissez les [concepts SDK WebJobs tels que déclencheurs, liaisons et le runtime JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Fonctions Azure est basé sur le WebJobs SDK. 


## <a name="function-code"></a>Code de la fonction

Une *fonction* est le principal concept dans les fonctions Azure. Vous écrivez du code pour une fonction dans une langue de votre choix et enregistrez les fichiers de code et un fichier de configuration dans le même dossier. Configuration est en JSON et le fichier est nommé `function.json`. Une variété de langues sont prises en charge et chacun d'entre eux possède une expérience légèrement différente optimisée pour travailler de façon optimale pour cette langue. 

La `function.json` fichier contient configuration spécifique à une fonction, y compris ses liaisons. Le runtime lit ce fichier pour déterminer quels événements déclencher désactiver de, les données à inclure lors de l’appel de la fonction et l’emplacement d’envoyer des données transmis à partir de la fonction elle-même. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Vous pouvez empêcher l’exécution de la fonction en cours d’exécution en définissant la `disabled` propriété `true`.

La `bindings` propriété est l’endroit où vous configurez déclencheurs et liaisons. Chaque liaison partage certains paramètres courants et certains paramètres qui sont spécifiques à un type de liaison. Chaque liaison requiert les paramètres suivants :

|Propriété|Valeurs/Types|Commentaires|
|---|-----|------|
|`type`|chaîne|Type de liaison. Par exemple, `queueTrigger`.
|`direction`|« in », « out »| Indique si la liaison est pour recevoir des données dans la fonction ou l’envoi de données à partir de la fonction.
| `name` | chaîne | Le nom qui sera utilisé pour les données liées dans la fonction. Pour c# il s’agit d’un nom d’argument ; pour JavaScript, il sera la clé dans une liste de valeurs/clé.

## <a name="function-app"></a>Application de fonction

Une application de la fonction est composée d’une ou plusieurs fonctions individuelles qui sont gérées par le Service d’application Azure. Toutes les fonctions dans une application de la fonction partagent le même plan de tarification, le déploiement continue et la version d’exécution. Fonctions écrites dans plusieurs langues peuvent partager la même application de fonction. Considérez une application de la fonction comme un moyen d’organiser et gérer les collectivement votre fonctions. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (exécution de scripts et hôte web)

Le runtime ou exécution de scripts, est l’hôte WebJobs SDK sous-jacente qui attend les événements, collecte et envoie des données et finalement exécute votre code. 

Pour faciliter la déclencheurs HTTP, vous trouverez également un hôte web qui est conçu pour réunir devant l’hôte de script dans les scénarios de production. Cela vous aide à isoler l’hôte de script du trafic frontal gérés par l’hôte web.

## <a name="folder-structure"></a>Structure de dossiers

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Lorsque vous définissez un projet pour le déploiement des fonctions à une application de la fonction dans le Service d’application Azure, vous pouvez considérer cette structure de dossiers comme code de votre site. Vous pouvez utiliser les outils existants tels que l’intégration continue et déploiement, ou scripts de déploiement personnalisés pour ce faire déployer installation du package heure ou transpilation de code.

>[AZURE.NOTE] La `wwwroot` dossier ici est l’endroit où vos fichiers seront obtenir déployés sur. Toutefois, vous ne devez pas inclure ce dossier dans les fichiers que vous déployez, qui risque d’entraîner avec `wwwroot\wwwroot`. En revanche, votre `host.json` dossiers de fichiers et fonction doivent être directement à la racine de ce que vous déployez.

## <a id="fileupdate"></a>Comment mettre à jour des fichiers d’application de fonction

L’éditeur de fonctions intégrée dans le portail Azure vous permet de mettre à jour le fichier *function.json* et le fichier de code pour une fonction. Pour télécharger ou mettre à jour d’autres fichiers tels que *package.json* ou *project.json* ou dépendances, vous devez utiliser d’autres méthodes de déploiement.

Fonction applications sont conçues service d’application afin que toutes les [options de déploiement disponibles aux applications web standard](../app-service-web/web-sites-deploy.md) sont disponibles pour les applications de fonction également. Voici quelques méthodes que vous pouvez utiliser pour télécharger ou mettre à jour des fichiers d’application de fonction. 

#### <a name="to-use-app-service-editor"></a>Pour utiliser l’éditeur de Service d’application

1. Dans le portail fonctions Azure, cliquez sur **paramètres de l’application fonction**.

2. Dans la section **Paramètres avancés** , cliquez sur **accéder aux paramètres de Service d’application**.

3. Sous **Outils de développement**, cliquez sur **Éditeur de l’application de Service** dans le Menu de l’application de volet de navigation.

4.  Cliquez sur **OK**.

    Une fois l’éditeur de Service application chargée, vous verrez les dossiers de fichiers et fonction *host.json* sous *wwwroot*. 

5. Ouvrir des fichiers à modifier, ou glisser-déplacer depuis votre ordinateur de développement pour télécharger des fichiers.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Utiliser point de terminaison de l’application de fonction SCM (Kudu)

1. Accédez à : `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Déboguer Console > CMD**.

3. Accédez à `D:\home\site\wwwroot\` pour mettre à jour *host.json* ou `D:\home\site\wwwroot\<function_name>` pour mettre à jour des fichiers d’une fonction.

4. Glisser-déplacer un fichier à télécharger dans le dossier approprié dans la grille de fichier. Il existe deux zones dans la grille de fichier dans lequel vous pouvez déplacer un fichier. Pour les fichiers *.zip* , une zone s’affiche avec l’étiquette « Glisser ici pour télécharger et décompresser le fichier ». Pour d’autres types de fichiers, de déroulement dans la grille de fichier, mais en dehors de la zone « unzip ».

#### <a name="to-use-ftp"></a>Utilisation de FTP

1. Suivez les instructions [ici](../app-service-web/web-sites-deploy.md#ftp) pour obtenir FTP configuré.

2. Lorsque vous êtes connecté au site application fonction, copier un fichier mis à jour *host.json* `/site/wwwroot` ou copier des fichiers fonction `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Utiliser le déploiement continue

Suivez les instructions dans la rubrique [déploiement continue pour les fonctions Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Exécution en parallèle

Lorsque plusieurs déclencheur se produisent plus vite qu’un seul thread fonction runtime peut les traiter, le runtime peut-être appeler la fonction plusieurs fois en parallèle.  Si une application de la fonction utilise le [Plan de Service dynamique](functions-scale.md#dynamic-service-plan), l’application de la fonction pourrait évoluer automatiquement.  Chaque instance de l’application de la fonction, si l’application s’exécute sur le Plan de Service dynamique ou un régulière [Plan de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), peut-être traiter des appels de fonctions simultanées en parallèle à l’aide de plusieurs threads.  Le nombre maximal d’appels de fonction simultanées dans chaque instance de l’application de fonction varie selon la taille de la mémoire de l’application de la fonction. 

## <a name="azure-functions-pulse"></a>Fonctions Azure Pulse  

Pulse est un flux d’événement en direct qui montre la fréquence d’exécution de votre fonction, ainsi que les réussites et les échecs. Vous pouvez également surveiller votre durée d’exécution moyenne. Nous allez ajouter davantage de fonctionnalités et de personnalisation lui au fil du temps. Vous pouvez accéder à la page **Pulse** à partir de l’onglet **analyse** .

## <a name="repositories"></a>Référentiels

Le code de fonctions Azure est ouvrir la source et stockées dans les référentiels GitHub :

* [Azure fonctions runtime](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portail de fonctions Azure](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modèles de fonctions Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensions Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Liaisons

Voici un tableau de toutes les liaisons pris en charge.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Signalisation des problèmes

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Référence du développeur Azure fonctions c#](functions-reference-csharp.md)
* [Référence du développeur Azure fonctions F #](functions-reference-fsharp.md)
* [Référence du développeur NodeJS fonctions Azure](functions-reference-node.md)
* [Liaisons et azure déclencheurs de fonctions](functions-triggers-bindings.md)
* [Fonctions azure : le voyage](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) sur le blog de l’équipe Azure Application Service. Historique de la façon dont les fonctions Azure a été développée.





