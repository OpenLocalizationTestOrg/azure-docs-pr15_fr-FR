<properties
    pageTitle="Quel est le Kit de développement .NET Azure"
    description="Découvrez ce qui est inclus dans le Kit de développement .NET Azure."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>Quel est le Kit de développement Azure pour .NET ?

## <a name="overview"></a>Vue d’ensemble

Le Kit de développement Azure pour .NET est un ensemble d’outils de Visual Studio, outils de ligne de commande, fichiers binaires runtime et les bibliothèques client pour vous aider à développement, tester et déploiement des applications qui s’exécutent dans Azure. Cet article décrit ce que vous obtenez lorsque vous installez le Kit de développement. Vous pouvez télécharger le Kit de développement de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/).

Le Kit de développement Azure pour .NET comprend également [bibliothèques clientes pour consommation de services Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Ces bibliothèques sont installés séparément à l’aide de NuGet.

##<a id="included"></a>Ce qui est inclus dans le Kit de développement Azure pour .NET

Le Kit de développement Azure pour .NET installe les produits suivants :

- [Édition de communauté Visual Studio 2015](#vwd)
- [Émulateur de stockage de Microsoft Azure](#stgemulator)
- [Outils de stockage de Microsoft Azure](#stgtools)
- [Outils de création de Microsoft Azure](#auth)
- [Émulateur Microsoft Azure](#emulator)
- [HDInsight Tools pour Visual Studio et Microsoft Hive pilote ODBC](#hdinsight)
- [Bibliothèques Microsoft Azure pour .NET](#libraries)
- [Kit de développement de l’application Mobile Microsoft Azure](#mobile)
- [Microsoft Azure PowerShell](#ps)
- [Outils de Microsoft Azure pour Microsoft Visual Studio](#tools)
- [Microsoft ASP.NET et outils Web pour Visual Studio](#wte)
- [Outils de Microsoft Azure données Lake pour Visual Studio](#datalake)

###<a id="vwd"></a>Édition de communauté Visual Studio 2015

Si vous n’avez pas Visual Studio sur votre ordinateur, le Kit de développement s’installe [Visual Studio Communauté édition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Émulateur de stockage de Microsoft Azure

L' [Émulateur de stockage Azure](http://msdn.microsoft.com/library/hh403989.aspx) utilise une instance de SQL Server et le système de fichiers local pour simuler le stockage Azure (files d’attente, les tableaux, des objets BLOB), afin que vous puissiez tester localement.

###<a id="stgtools"></a>Outils de stockage de Microsoft Azure

Cette procédure installe [AzCopy](http://aka.ms/AzCopy), un outil de ligne de commande permet de transférer des données vers et depuis un compte de stockage Azure.

###<a id="auth"></a>Outils de création de Microsoft Azure

Cela inclut les éléments suivants :

* L' [outil de ligne de commande CSPack](http://msdn.microsoft.com/library/gg432988.aspx) pour la création de packages de déploiement.
* l' [outil de ligne de commande CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) pour chiffrer les mots de passe utilisés pour accéder aux instances de rôle du service cloud via une connexion Bureau à distance.
* Fichiers binaires Runtime que projets de service en nuage nécessitent de communiquer avec leur environnement d’exécution et des diagnostics. Ces fichiers binaires ne sont pas disponibles dans les packages NuGet.

###<a id="emulator"></a>Émulateur Microsoft Azure

L' [Émulateur Azure](http://msdn.microsoft.com/library/dn339018.aspx) simule l’environnement de service cloud afin que vous pouvez tester des projets de service cloud localement sur votre ordinateur avant de les déployer vers Azure.

###<a id="hdinsight"></a>HDInsight Tools pour Visual Studio et pilote ODBC Hive Microsoft

Outils HDInsight dans l’Explorateur de serveur permettent de naviguer dans les bases de données Hive et comptes de stockage liées pour les clusters HDInsight, créer des tables et créer et soumettre des requêtes Hive. Pour plus d’informations, voir [prise en main de l’utilisation HDInsight Hadoop Tools pour Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="libraries"></a>Bibliothèques Microsoft Azure pour .NET

Cela inclut les éléments suivants :

* Packages NuGet pour le stockage Azure, Bus de Service et la mise en cache qui sont stockées sur votre ordinateur afin que Visual Studio peut créer cloud nouveaux projets de service tout en mode hors connexion.
* Visual Studio plug-in qui permet aux projets de [Cache dans le rôle](http://msdn.microsoft.com/library/dn386103.aspx) d’exécutés localement dans Visual Studio.

###<a id="mobile"></a>Kit de développement de l’application Mobile Microsoft Azure

Outils d’utilisation des [Applications de Azure Application Service Mobile](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>Microsoft Azure PowerShell

PowerShell Azure vous permet [d’automatiser la création de l’environnement Azure et de déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Outils de Microsoft Azure pour Microsoft Visual Studio

Cela vous permet de travailler avec les ressources Azure, principalement des Services Cloud et des Machines virtuelles :

* [Créer, ouvrir et publier des projets de service cloud](cloud-services/cloud-services-dotnet-get-started.md).
* [Créer des packages de déploiement pour les projets de service cloud](http://msdn.microsoft.com/library/ff683672.aspx).
* [Créer des Machines virtuelles Azure lors de la création de nouveaux projets web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Des scripts PowerShell créer lors de la création de nouvelles machines virtuelles](http://msdn.microsoft.com/library/dn642480.aspx).
* [Afficher et gérer les paramètres de projet de service cloud dans windows propriétés de projet Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Afficher et gérer [les services en nuage](http://msdn.microsoft.com/library/ff683675.aspx), [machines virtuelles](http://msdn.microsoft.com/library/jj131259.aspx)et [Bus des services](http://msdn.microsoft.com/library/jj149828.aspx) dans l’Explorateur de serveur.
* [Exécuter en mode de débogage à distance pour les services en nuage et machines virtuelles](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatiser les ressources de mise en service à l’aide de projets de déploiement de groupe de ressources Azure](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Outils de Service d’application Microsoft pour Visual Studio

Cela vous permet de travailler avec des sites Web Azure :

* [Publier les projets web aux sites Web Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Publier des projets d’application console à Azure WebJobs](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Ressources du site Web créer Azure et base de données SQL lors de la création d’un nouveau projet web ou lors de la publication d’un projet web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [PowerShell créer les scripts de déploiement lors de la création de nouveaux sites Web](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gérer et résoudre les problèmes des sites Web Azure dans l’Explorateur de serveurs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Exécuter en mode de débogage à distance des sites Web et WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] Vous n’êtes pas obligé d’installer le Kit de développement Azure pour .NET utiliser ces fonctionnalités ; ils sont également inclus dans les mises à jour de Visual Studio.

##<a id="datalake"></a>Outils de Microsoft Azure données Lake pour Visual Studio

Pour plus d’informations, voir [didacticiel : développer les scripts U-SQL à l’aide de Lake Data Tools pour Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>Ce qui n’a pas inclus lorsque vous installez le Kit de développement Azure pour .NET

Il existe plusieurs choses que vous souhaiterez pour le développement d’Azure qui ne sont pas inclus lorsque vous installez le Kit de développement. Les plus importantes sont les suivantes :

* [Bibliothèques du client](http://go.microsoft.com/fwlink/?LinkId=510472).

    Le Kit de développement Azure inclut des bibliothèques de client pour l’utilisation des services Azure, mais pas tous les sont installés lorsque vous installez le Kit de développement. Si votre application a besoin d’une bibliothèque de client qui ne s’installe pas le Kit de développement, vous pouvez l’obtenir auprès [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Si votre application utilise une bibliothèque de client le Kit de développement installe-t-il, il est judicieux de mettre à jour avec la version actuelle, sur NuGet.org.

    **Copies locales des bibliothèques de clients.** Le Kit de développement Azure pour .NET copie sur votre ordinateur les packages NuGet pour certaines bibliothèques client Azure, tels que stockage Bus de Service et la mise en cache. Ces bibliothèques client sont automatiquement inclus dans les nouveaux projets de service cloud, afin des packages NuGet locales activer Visual Studio pour créer des projets même si vous n’êtes pas connecté à Internet. Bibliothèques client sont généralement mis à jour plus fréquemment que de nouvelles versions SDK sont publiées, afin que les bibliothèques du client sur NuGet.org sont souvent plus récentes que celles que vous offre le Kit de développement.

    **Modèles de projets qui incluent les bibliothèques clientes.** Modèles de projets [Azure Cloud Service](cloud-services/cloud-services-dotnet-get-started.md) et Azure Application Service [Applications Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) incluent automatiquement certaines bibliothèques client. Pour d’autres bibliothèques ou d’autres modèles, installez les [packages NuGet de bibliothèque client](http://go.microsoft.com/fwlink/?LinkId=510472) dont vous avez besoin.

* [Modèles de projet applications Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Modèles d’applications mobiles sont disponibles uniquement dans Visual Studio 2013 mise à jour 2 et versions ultérieures. Ils ne sont pas disponibles dans Visual Studio 2012 ou les versions antérieures et non dans Visual Studio 2013 mise à jour 1 ou une version antérieure, même si vous installez le Kit de développement Azure pour .NET.

##<a id="faq"></a>Forum aux Questions

- [De nombreuses fonctionnalités Azure sont déjà présentes dans Visual Studio. Ai-je besoin installer le Kit de développement Azure pour .NET ?](#azinvs)
- [Je veux une bibliothèque de client. Dois-je installer le Kit de développement Azure pour .NET obtenir ?](#clientlib)
- [Où puis-je trouver les anciennes versions du Kit de développement Azure pour .NET ?](#olderversions)
- [Quelle est la stratégie du cycle de vie pour les versions du Kit de développement Azure pour .NET ?](#lifecycle)
- [Quelles versions du système d’exploitation invité est le Kit de développement Azure pour .NET compatible avec ?](#guestos)
- [Comment désinstaller le Kit de développement Azure pour .NET ?](#uninstall)

###<a id="azinvs"></a>De nombreuses fonctionnalités Azure sont déjà présentes dans Visual Studio. Ai-je besoin installer le Kit de développement Azure pour .NET ?

Il est recommandé d’installer le Kit de développement si vous souhaitez développer pour Azure à l’aide des outils les plus récents. Si vous préférez installer pas le Kit de développement, vous pouvez le faire si les conditions suivantes sont remplies :

* Vous avez installé dernière [Mise à jour de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Vous développez uniquement pour les sites Web Azure ou les Services mobiles, pas pour les services en nuage ou Machines virtuelles.
* Votre application n’utilise pas de stockage, ou qu’il utilise le stockage, mais vous n’avez pas besoin l’émulateur de stockage ou de l’outil AzCopy.

###<a id="clientlib"></a>Je veux une bibliothèque de client. Dois-je installer le Kit de développement Azure pour .NET obtenir ?

Le Kit de développement installe des bibliothèques de client uniquement afin que vous puissiez créer cloud projets de service même si vous n’êtes pas connecté à Internet. Bibliothèques de client en cours sont disponibles dans NuGet packages sur [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Pour plus d’informations, voir [ce qui n’a pas inclus lorsque vous installez le Kit de développement Azure pour .NET](#notincluded) précédemment dans ce document.

###<a id="olderversions"></a>Où puis-je trouver les anciennes versions du Kit de développement Azure pour .NET ?

Pour les versions antérieures, voir que le [Kit de développement logiciel Azure pour .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) page de téléchargement.

###<a id="lifecycle"></a>Quelle est la stratégie du cycle de vie pour les versions du Kit de développement Azure pour .NET ?

Voir [politique de Support Microsoft Azure Cloud Services](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Quelles versions du système d’exploitation invité est le Kit de développement Azure pour .NET compatible avec ?

Voir [les versions du système d’exploitation invité Azure et matrice de compatibilité SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Comment désinstaller le Kit de développement Azure pour .NET ?

Chaque élément figurant dans cet article sous [ce qui est inclus dans le Kit de développement Azure pour .NET](#included) est un programme dans la liste des programmes installés du Panneau de configuration Windows **programmes et fonctionnalités**.  Il n’existe aucun moyen pour les désinstaller en tant que groupe ; Vous devez désinstaller chaque programme individuellement.

Lorsque vous avez le Kit de développement Azure pour .NET est déjà installé, et vous installez une nouvelle version, il n’est généralement aucun nécessaire de désinstaller l’ancien. Dans la plupart des cas, l’installation du Kit de développement logiciel met à jour un programme existant au lieu d’ajouter un nouveau et quitter l’ancien.

Toutefois, si vous voulez supprimer les fichiers restants non plus à vos besoins d’une version antérieure, désinstaller uniquement les programmes qui spécifient le numéro de version et les désinstaller uniquement s’il existe du même programme avec une version plus récente. Par exemple, après mise à jour à partir de 2,5 vers 2.6 que vous voyiez versions 2.5 et 2.6 de « Microsoft Azure Tools pour Microsoft Visual Studio 2013 », et vous pouvez désinstaller la version 2,5. Mais vous pouvez voir uniquement la version 2,5 des « Outils de création de Microsoft Azure » et il n’est pas sûr que désinstaller.

Notez que les numéros de version dans les titres de programme indiqués dans les **programmes et fonctionnalités** peuvent être confusion.  Par exemple, SDK version 2.6 inclut « Microsoft Azure Mobile application SDK 1.0 » si vous installez le Kit de développement pour Visual Studio 2013 et « Microsoft Azure Mobile application SDK 2.0 » pour Visual Studio 2015 ; la version dans ce cas n’est pas la version SDK mais un indicateur dont la version de Visual Studio le programme s’applique à.

Cet article ne répertorie pas tous les programmes que chaque version antérieure du Kit de développement Azure incluse ; Il existe d’autres programmes que vous pouvez désinstaller les versions antérieures SDK, car les versions antérieures SDK incluaient parfois programmes qui ont été omis dans une version ultérieure. Par exemple, version 2.5 s’installe « Azure ressource Gestionnaire outils pour Visual Studio », mais qui n’est pas dans la liste de cet article, car il ne s’affiche plus comme un programme distinct dans les **programmes et fonctionnalités**.  Cet article répertorie uniquement les programmes qui sont inclus dans le Kit de développement Azure pour .NET version 2.6.

> **Remarque :** Certains des programmes qui inclut le Kit de développement peuvent également être installés séparément dans d’autres contextes et peuvent être nécessaire même si vous n’avez pas besoin du Kit de développement complète. La même peut être vraie pour les programmes qui ont été installés par les versions antérieures SDK mais qui ont été omis dans une version ultérieure SDK. Lorsque vous désinstallez des programmes, en veillant à n’éviter de supprimer quelque chose qui est toujours nécessaire sur votre ordinateur.



##<a id="versions"></a>Versions

Pour déterminer quelle version actuelle ou télécharger des versions antérieures, consultez la page [Azure SDK pour l’historique de Version .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Ressources

Pour télécharger le Kit de développement Azure active pour .NET ou une bibliothèque de client, consultez la [page de téléchargements Azure](https://azure.microsoft.com/downloads/).

Pour le Kit de développement Azure pour le code source .NET, y compris les bibliothèques de client, voir [GitHub.com/Azure](https://github.com/azure/).

Pour la documentation de référence sur la bibliothèque client Azure, voir [Informations de référence .NET Azure](https://azure.microsoft.com/documentation/api/).
