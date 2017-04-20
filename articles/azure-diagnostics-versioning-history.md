<properties
    pageTitle="Historique des Diagnostics de Windows Azure"
    description="Explication des modifications dans les différentes versions de diagnostics de Windows Azure comme livré avec différentes versions de Microsoft Azure SDK."
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Historique des Diagnostics Microsoft Azure

Vous débutez avec les Diagnostics de Windows Azure ? Voir [vue d’ensemble des Diagnostics de Windows Azure](azure-diagnostics.md).

Chaque version d’Azure SDK est généralement fourni avec une nouvelle version d’Azure Diagnostics. Le tableau ci-dessous décrit les versions Azure SDK et les Diagnostics de Azure liées à la version du Kit de développement logiciel.



Version SDK Azure | Version Diagnostics Azure | Modèle
--- | --- | ---
1.x      | 1.0 | plug-in
2.0 et 2.4| 1.0 | "
2,5      | 1.2 | extension
2.6      | 1.3 | "
2.7      | 1.4 | "
2,8      | 1,5 | "


La version la plus récente est de 1,5, ce qui est fourni avec Azure SDK 2,8. La version d’extension Diagnostics Azure qui est fourni avec le Kit de développement est utilisée uniquement pour les scénarios émulateur local. N’importe quelle application déployée utilise automatiquement la dernière version lors de l’exécution dans Azure, sans tenir compte de ce qui est équipée version du Kit de développement l’application. Toutefois, à moins d’installer la dernière version du SDK Azure, vous n’avez pas tous les outils pour vous aider à utilisent les nouvelles fonctionnalités.

Diverses fonctionnalités et modifications décrites ci-après.

## <a name="azure-sdk-28"></a>Kit de développement logiciel Azure 2,8
Azure SDK 2,8 ajouté la possibilité d’envoyer des données de diagnostic [Application perspectives](./application-insights/app-insights-cloudservices.md) , ce qui facilite la diagnostiquer les problèmes dans l’ensemble de votre application, ainsi que le niveau de l’infrastructure système et.

## <a name="azure-26-diagnostics-changes"></a>Modifications des diagnostics Azure 2.6

Pour les projets de Service de nuage Azure SDK 2.6 dans Visual Studio, les modifications suivantes ont été apportées. (Ces modifications s’appliquent également à ses versions ultérieures Azure SDK.)

- L’émulateur local prend désormais en charge les diagnostics. Cela signifie que vous pouvez collecter des données de diagnostics et vérifiez que votre application consiste à créer les traces droite pendant que vous êtes développer et tester dans Visual Studio. La chaîne de connexion `UseDevelopmentStorage=true` permet de collecte de données de diagnostic pendant que vous exécutez votre projet de service cloud dans Visual Studio à l’aide de l’émulateur de stockage Azure. Toutes les données de diagnostic sont collectées dans le compte de stockage (stockage de développement).

- La chaîne de connexion de compte diagnostics stockage (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) est stockée une nouvelle fois dans le fichier de configuration (.cscfg) service. Le compte de stockage diagnostics Azure SDK 2.5 a été spécifié dans le fichier diagnostics.wadcfgx.

Il existe certaines différences importantes entre comment la chaîne de connexion a travaillé dans Azure SDK 2.4 et antérieures et son fonctionnement dans Azure SDK 2.6 et versions ultérieures.

- Azure SDK 2.4 et les versions antérieures, la chaîne de connexion a été utilisée comme un runtime par le plug-in diagnostics pour obtenir les informations de compte de stockage pour transférer les journaux de diagnostics.

- Azure SDK 2.6 et versions ultérieures, la chaîne de connexion diagnostics est utilisée par Visual Studio pour configurer l’extension diagnostics avec les informations de compte de stockage approprié lors de la publication. La chaîne de connexion vous permet de définir des comptes de stockage différents pour les configurations de services différentes que Visual Studio utilisera lors de la publication. Toutefois, étant donné que le plug-in diagnostics n’est plus disponible (après avoir Azure SDK 2.5), le fichier .cscfg par lui-même ne peut pas activer l’Extension Diagnostics. Vous devez activer l’extension séparément par le biais d’outils tels que Visual Studio ou PowerShell.

- Pour simplifier le processus de configuration de l’extension diagnostics avec PowerShell, la sortie du package à partir de Visual Studio contienne également la configuration publique XML pour l’extension diagnostics pour chaque rôle. Visual Studio utilise la chaîne de connexion diagnostics pour renseigner les informations de compte de stockage présentes dans la configuration du publique. Les fichiers de configuration public sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics. <RoleName>. PubConfig.xml. Les déploiements PowerShell basé peuvent utiliser ce modèle pour mapper chaque configuration à un rôle.

- La chaîne de connexion dans le fichier .cscfg est également utilisée par le portail Azure pour accéder aux données diagnostics afin qu’il peut apparaître dans l’onglet **analyse** . La chaîne de connexion est nécessaire pour configurer le service pour afficher les données d’analyse détaillées dans le portail.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migration des projets à Azure SDK 2.6 et versions ultérieures

Lors de la migration à partir d’Azure SDK 2.5 à Azure SDK 2.6 ou version ultérieure, si vous avez un compte de stockage diagnostics spécifié dans le fichier .wadcfgx, puis il restera il. Pour tirer parti de la flexibilité d’utilisation de stockage différents comptes pour les configurations de stockage différents, vous devrez ajouter manuellement la chaîne de connexion à votre projet. Si vous migrez un projet à partir d’Azure SDK 2.4 ou version antérieure vers Azure SDK 2.6, les chaînes de connexion diagnostics sont conservés. Toutefois, notez les modifications dans comment les chaînes de connexion sont traités dans Azure SDK 2.6 comme indiqué dans la section précédente.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Comment Visual Studio détermine le compte de stockage diagnostics

- Si une chaîne de connexion diagnostics spécifiée dans le fichier .cscfg, Visual Studio utilise pour configurer l’extension diagnostics lors de la publication et lors de la création de fichiers xml de configuration public lors de l’emballage.

- Si aucune chaîne de connexion diagnostics ne spécifié dans le fichier .cscfg, puis Visual Studio revient à l’aide du compte de stockage spécifié dans le fichier .wadcfgx pour configurer l’extension diagnostics lors de la publication et générer les fichiers xml public configuration lors de la compression.

- La chaîne de connexion diagnostics dans le fichier .cscfg est prioritaire sur le compte de stockage dans le fichier .wadcfgx. Si une chaîne de connexion diagnostics est spécifiée dans le fichier .cscfg, puis Visual Studio qui utilise et ignore le compte de stockage dans .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Que signifie les « mise à jour développement stockage chaînes de connexion... » case à cocher faire ?

La case à cocher pour les **chaînes de connexion de stockage de développement mise à jour pour les Diagnostics et de mise en cache des informations d’identification de compte de stockage Microsoft Azure lors de la publication de Microsoft Azure** vous donne un moyen pratique pour mettre à jour les chaînes de connexion de compte de stockage développement avec le compte de stockage Azure spécifié lors de la publication.

Par exemple, supposons que vous activez cette case à cocher et la chaîne de connexion diagnostics indique `UseDevelopmentStorage=true`. Lorsque vous publiez le projet sur Azure, Visual Studio met automatiquement à jour la chaîne de connexion diagnostics avec le compte de stockage que vous avez spécifié dans l’Assistant Publication. Toutefois, si un compte de stockage réel a été spécifié en tant que la chaîne de connexion diagnostics, ce compte est utilisé à la place.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Différences de fonctionnalités Diagnostics entre Azure SDK 2.4 et antérieur et Azure SDK 2,5 et versions ultérieur

Si vous mettez à niveau votre projet à partir d’Azure SDK 2.4 à Azure SDK 2.5 ou version ultérieure, vous devez garder à l’esprit les différences de fonctionnalité diagnostics suivantes.

- **API de configuration sont déconseillées** – configuration par programme de diagnostics n’est disponible dans Azure SDK 2.4 ou les versions antérieures, mais est déconseillée dans Azure SDK 2.5 et versions ultérieures. Si votre configuration diagnostics est actuellement définie dans le code, vous devez reconfigurer ces paramètres de toutes pièces dans le projet a été déplacée dans l’ordre des diagnostics pour continuer à travailler. Le fichier de configuration de diagnostics pour Azure SDK 2.4 est diagnostics.wadcfg et diagnostics.wadcfgx pour Azure SDK 2.5 et versions ultérieures.

- **Diagnostics pour les applications de service cloud peut uniquement être configuré au niveau du rôle et non au niveau de l’instance.**

- **Chaque fois que vous déployez votre application, la configuration de diagnostics est mis à jour** – cela peut entraîner des problèmes de disparités qui existent si vous modifiez votre configuration diagnostics à partir de l’Explorateur de serveurs, puis redéployez votre application.

- **Dans Azure SDK 2.5 et une version ultérieures, impact images complètes sont configurés dans le fichier de configuration diagnostics pas dans le code** – si vous disposez d’images de blocage configurés dans le code, vous devrez transférer manuellement la configuration de code vers le fichier de configuration, car le vidage de blocage ne sont pas transférées lors de la migration vers Azure SDK 2.6.
