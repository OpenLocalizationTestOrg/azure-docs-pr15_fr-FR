<properties 
    pageTitle="Résolution des problèmes d’aucune donnée - Application perspectives pour .NET" 
    description="Données dans Visual Studio Application Insights n’apparaît ne pas ? Essayez ici." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Résolution des problèmes d’aucune donnée - Application perspectives pour .NET

## <a name="some-of-my-telemetry-is-missing"></a>Certains de mes télémétrie est manquant

*Dans l’Application Insights, je ne vois une fraction des événements qui sont générés par mon application.*

* Si vous voyez toujours la même fraction, il est probable adaptive [échantillonnages](app-insights-sampling.md). Pour confirmer cela, ouvrez recherche (à partir de la carte de la vue d’ensemble) et observez une instance d’une demande ou tout autre événement. Cliquez sur « … » pour obtenir des détails complète de la propriété en bas de la section Propriétés. Si requête Count > 1, puis échantillonnages sont opérationnel. 
* Dans le cas contraire, il est possible que vous êtes en appuyant sur une [limite de taux de données](app-insights-pricing.md#limits-summary) pour votre plan de tarification. Ces limites sont appliquées par minute.

## <a name="no-data-from-my-server"></a>Pas de données à partir de mon serveur

*J’ai installé mon application sur mon serveur web, et maintenant je ne vois pas les télémétrie à partir de celui-ci. La réinitialisation a fonctionné OK sur mon ordinateur de développement.*

* Probablement un problème de pare-feu. [Définir des exceptions de pare-feu pour avoir un aperçu Application envoyer des données](app-insights-ip-addresses.md).

*J’ai [installé le moniteur d’état](app-insights-monitor-performance-live-website-now.md) sur mon serveur web pour surveiller des applications existantes. Je ne vois pas les résultats.*

* Voir [dépannage moniteur d’état](app-insights-monitor-performance-live-website-now.md#troubleshooting). 


## <a name="q01"></a>Aucune option « Ajouter des perspectives Application » dans Visual Studio

*Lorsque je crée un nouveau projet dans Visual Studio, ou lorsque je clic droit sur un projet existant dans l’Explorateur de solutions, je ne vois pas les options d’analyse de l’Application.*

+ Pas tous les types de projet .NET sont pris en charge par les outils. Projets Web et WCF sont pris en charge. Pour d’autres types de projets tels que des applications de bureau ou services, vous pouvez toujours [ajouter manuellement une Application Insights SDK à votre projet](app-insights-windows-desktop.md).
+ Vérifiez que vous avez [mise à jour de Visual Studio 2013 3 ou version ultérieure](http://go.microsoft.com/fwlink/?LinkId=397827). Il est préinstallé avec les outils d’analyse des applications.
+ Sélectionnez **Outils**, **Extensions et mises à jour** et vérifiez que les **Outils d’analyse des applications** est installé et activé. Si c’est le cas, cliquez sur **mises à jour** pour voir si une mise à jour est disponible.
+ Ouvrez la boîte de dialogue Nouveau projet, puis sélectionnez application Web ASP.NET. Si vous voyez l’option d’analyse de l’Application, puis les outils sont installés. Dans le cas contraire, essayez de désinstaller puis réinstaller les outils d’analyse des applications.


## <a name="q02"></a>Échec de l’ajout de perspectives d’Application

*Lorsque je crée un nouveau projet web, ou lorsque j’essaie d’ajouter des perspectives d’Application à un projet existant, je vois un message d’erreur.*

Causes possibles :

* Échec de la communication avec le portail d’analyse de l’Application ; ou
* Il existe un problème avec votre compte Azure ;
* Vous avez uniquement [accès en lecture à l’abonnement ou le groupe dans lequel vous tentez de créer la nouvelle ressource](app-insights-resources-roles-access-control.md).

Correctif :

+ Vérifiez que vous avez fourni les informations d’identification de connexion pour le compte Azure vers la droite. 
+ Dans le navigateur, vérifiez que vous avez accès au [portail Azure](https://portal.azure.com). Ouvrir les paramètres et s’il existe aucune restriction.
+ [Analyse des applications ajouter à votre projet](app-insights-asp-net.md): dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet et cliquez sur « Ajouter l’Application perspectives. »
+ Si elle ne fonctionne toujours pas, suivez la [procédure manuelle](app-insights-windows-services.md) pour ajouter une ressource dans le portail et ajoutez le Kit de développement à votre projet. 

## <a name="emptykey"></a>J’obtiens une erreur « clé Instrumentation ne peut pas être vide »

Il semblerait qu’un problème est survenu pendant que vous installez Application Insights ou peut-être une carte de journalisation.

Dans l’Explorateur de solutions, avec le bouton droit `ApplicationInsights.config` et sélectionnez **Configurer Application perspectives**. Vous recevrez une boîte de dialogue qui vous invite à se connecter à Azure et créez une ressource d’Application perspectives, ou utiliser une existante.


##<a name="NuGetBuild"></a>« Packages NuGet disparaissent « sur mon serveur de génération

*Tout crée OK lorsque je suis débogage sur mon ordinateur de développement, mais j’obtiens une erreur NuGet sur le serveur de génération.*

Voir [Restaurer les Package NuGet](http://docs.nuget.org/Consume/Package-Restore) et [Restaurer de Package automatique](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Commande du menu manquantes pour ouvrir des perspectives d’Application à partir de Visual Studio

*Lorsque j’ai un clic droit mon projet l’Explorateur de solutions, je ne vois pas des commandes d’analyse de l’Application, ou je ne vois pas une commande Ouvrir Application perspectives.*

Causes possibles :

* Si vous avez créé la ressource Application Insights manuellement, ou si le projet est d’un type qui n’est pas pris en charge par les outils d’analyse de l’Application.
* Les outils d’analyse des applications sont désactivées dans votre Visual Studio.
* Votre Visual Studio est antérieur à la mise à jour 2013 3.

Correctif :

* Vérifiez que votre version de Visual Studio est mise à jour 2013 3 ou version ultérieure.
* Sélectionnez **Outils**, **Extensions et mises à jour** et vérifiez que les **Outils d’analyse des applications** est installé et activé. Si c’est le cas, cliquez sur **mises à jour** pour voir si une mise à jour est disponible.
* Clic droit sur votre projet dans l’Explorateur de solutions. Si vous voyez la commande **Configurer Application perspectives**, utilisez-le pour connecter votre projet à la ressource dans le service d’analyse de l’Application.


Dans le cas contraire, votre type de projet n’est pas directement pris en charge par les outils d’analyse de l’Application. Pour afficher votre télémétrie, connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur analyse de l’Application dans la barre de navigation gauche, puis sélectionnez votre application.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>« Accès refusé » lors de l’ouverture des perspectives d’Application à partir de Visual Studio

*La commande « Ouvrir Application Insights » me permet d’accéder au portail Azure, mais j’obtiens une erreur « accès refusé ».*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

La connexion de Microsoft vous avez utilisé dans votre navigateur par défaut n’a pas accès à [la ressource qui a été créée lors de l’analyse de l’Application a été ajouté à cette application](app-insights-asp-net.md). Il existe deux raisons susceptibles de : 

* Vous avez plusieurs comptes Microsoft - peut-être un professionnel et un compte Microsoft personnel ? La connexion que vous avez utilisé dans votre navigateur par défaut a été pour un compte différent de celui qui a accès à [Ajouter l’Application Insights au projet](app-insights-asp-net.md). 

 * Correctif : Cliquez sur votre nom en haut à droite de la fenêtre du navigateur et se déconnecter. Puis connectez-vous avec le compte qui a accès. Dans la barre de navigation gauche, cliquez sur Application Insights puis sélectionnez votre application.

* Une autre personne ajoutée Application Insights au projet, et il a oublié de vous accorder l' [accès au groupe de ressources](app-insights-resources-roles-access-control.md) dans lequel il a été créé. 

 * Correctif : S’ils utilisés un compte professionnel, ils peuvent vous ajouter à l’équipe ; ou qu’ils peuvent vous accordez l’accès individuel au groupe de ressources.



## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>« Bien introuvable » lors de l’ouverture des perspectives d’Application à partir de Visual Studio

*La commande « Ouvrir Application Insights » me permet d’accéder au portail Azure, mais j’obtiens une erreur « biens introuvable ».*

Causes possibles :

* La ressource Application perspectives pour votre application a été supprimée ; ou
* La clé d’instrumentation a été définie ou modifiée dans ApplicationInsights.config en modifiant directement, sans mettre à jour le fichier de projet. 

La clé d’instrumentation dans les contrôles ApplicationInsights.config où la télémétrie est envoyé. Une ligne dans le fichier de projet contrôle ressource qui s’ouvre lorsque vous utilisez la commande dans Visual Studio. 

Correctif :

* Dans l’Explorateur de solutions, droit sur le projet et choisissez Insights d’Application, configurez Application perspectives. Dans la boîte de dialogue, vous pouvez choisir Envoyer télémétrie à une ressource existante ou créez-en une. Ou :
* Ouvrez la ressource directement. Connectez-vous au [portail Azure](https://portal.azure.com)et cliquez sur analyse de l’Application dans la barre de navigation gauche, puis sélectionnez votre application.



## <a name="where-do-i-find-my-telemetry"></a>Où trouver mon télémétrie ?

*J’ai connecté au [portail Microsoft Azure](https://portal.azure.com)et je recherche le tableau de bord personnel Azure. Donc où puis-je trouver mes données d’analyse de l’Application ?*

* Dans la barre de navigation gauche, cliquez sur analyse de l’Application, puis votre nom de l’application. Si vous n’avez il tous les projets, vous devez [Ajouter ou configurer des perspectives d’Application dans votre projet web](app-insights-asp-net.md).

    Vous verrez alors certains graphiques résumés. Vous pouvez cliquer sur leur pour afficher plus en détail.

* Dans Visual Studio, tandis que vous êtes déboguer votre application, cliquez sur le bouton analyse de l’Application.


## <a name="q03"></a>Aucune donnée du serveur (ou pas du tout de données)

*J’ai exécuté mon application et puis ouvert le service d’Application Insights dans Microsoft Azure, mais tous les graphiques affichent « Découvrez comment collecter... » ou « N’est pas configuré. »* Ou, *uniquement les données utilisateur et en mode Page, mais aucune donnée du serveur.*

+ Exécuter votre application en mode débogage dans Visual Studio (F5). Utiliser l’application afin de générer certaines télémétrie. Vérifiez que vous pouvez voir les événements enregistrés dans la fenêtre de sortie de Visual Studio. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ Dans le portail d’analyse de l’Application, ouvrez [Diagnostics de recherche](app-insights-diagnostic-search.md). Données généralement apparaissent ici tout d’abord.
+ Cliquez sur le bouton Actualiser. La carte s’actualise régulièrement, mais vous pouvez le faire également manuellement. L’intervalle d’actualisation est plus longue pour les plages de temps plus grandes.
+ Vérifier que les clés instrumentation correspondent. Dans la carte principale pour votre application dans le portail d’analyse de l’Application, dans le menu déroulant **Essentials** , consultez **clé Instrumentation**. Puis, dans votre projet dans Visual Studio, ouvrez ApplicationInsights.config et recherchez le `<instrumentationkey>`. Vérifiez que les deux clés sont égales. Dans le cas contraire :
 + Dans le portail, cliquez sur analyse des applications et recherchez la ressource application avec la touche droite ; ou
 + Dans l’Explorateur de solutions Visual Studio, droit sur le projet et choisissez Insights d’Application, configurer. Réinitialiser l’application pour envoyer de télémétrie à la ressource de droite.
 + Si vous ne trouvez pas les touches correspondantes, vérifiez que vous utilisez les mêmes informations de connexion en tant que dans Visual Studio au portail.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ Dans le [tableau de bord personnel Microsoft Azure](https://portal.azure.com), recherchez-le sur la carte de l’état du Service. S’il existe quelques indices alertes, patientez jusqu'à ce qu’ils ont renvoyé aux OK puis fermez et rouvrez votre carte application Application perspectives.
+ Vérifiez également [notre blog état](http://blogs.msdn.com/b/applicationinsights-status/).
+ Vous n’avez écrit de code pour le [Kit de développement logiciel côté serveur](app-insights-api-custom-events-metrics.md) susceptibles de changer la clé d’instrumentation dans `TelemetryClient` instances ou en `TelemetryContext`? Ou vous n’avez écrit un [filtre ou échantillonnage configuration](app-insights-api-filtering-sampling.md) qui peuvent être filtrage arrière trop ?
+ Si vous avez modifié ApplicationInsights.config, vérifiez attentivement la configuration de [TelemetryInitializers et TelemetryProcessors](app-insights-api-filtering-sampling.md). Un type incorrectement nommé ou le paramètre peut entraîner le Kit de développement n’envoyer aucune donnée.

## <a name="q04"></a>Aucune donnée sur l’utilisation des affichages de Page, les navigateurs,

*Je vois des données dans les temps réponse du serveur et des demandes de serveur, mais pas de données dans le temps de chargement de mode Page ou dans les navigateur ou utilisation des cartes.*

Les données proviennent des scripts dans les pages web. 

+ Si vous avez ajouté des perspectives d’Application à un projet web existant, [que vous devez ajouter manuellement les scripts](app-insights-javascript.md).
+ Vérifiez que Internet Explorer n’affiche pas votre site en mode de compatibilité.
+ Utiliser la fonctionnalité de navigateur déboguer (F12 dans certains navigateurs, puis choisissez réseau) pour vérifier que les données sont envoyées à `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Aucune donnée dépendance ou une exception

Voir [télémétrie de dépendance](app-insights-asp-net-dependencies.md) et [télémétrie exception](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Aucune donnée de performance

Les données de performance (processeur, taux IO et ainsi de suite) est disponible pour les [services web Java](app-insights-java-collectd.md), [les applications de bureau de Windows](app-insights-windows-desktop.md), [IIS web applications et services si vous installez moniteur d’état](app-insights-monitor-performance-live-website-now.md)et [Azure Cloud Services](app-insights-azure.md). vous la trouverez sous Paramètres, serveurs.

Il n’est pas disponible pour les sites Web Azure.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Aucune donnée (serveur) depuis que j’ai publié l’application à mon serveur

+ Vérifiez que vous avez copié réellement tous les Microsoft. DLL ApplicationInsights sur le serveur, ainsi que de Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ Dans votre pare-feu, vous devrez peut-être [ouvrir certains ports TCP](app-insights-ip-addresses.md#data-access-api).
+ Si vous devez utiliser un proxy pour envoyer se déconnecter de votre réseau d’entreprise, définir [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) dans Web.config
+ Windows Server 2008 : Vérifiez que vous avez installé les mises à jour suivants : [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).


## <a name="i-used-to-see-data-but-it-has-stopped"></a>J’ai utilisé pour afficher des données, mais il est arrêté

* Consultez le [blog de l’état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous avez atteint votre quota mensuel de points de données ? Ouvrir les paramètres/Quota et tarification pour déterminer. Dans ce cas, vous pouvez mettre à niveau votre plan ou payer pour une capacité supplémentaire. Consultez les [tarifs jeu](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="i-dont-see-all-the-data-im-expecting"></a>Je ne vois pas toutes les données que je suis attendu.

Si votre application envoie un grand nombre de données et que vous utilisez le Kit de développement du perspectives d’Application pour ASP.NET version 2.0.0-beta3 ou version ultérieure, la fonctionnalité [d’échantillonnage adapté](app-insights-sampling.md) peut-être fonctionner et envoyer uniquement un pourcentage de votre télémétrie. 

Vous pouvez le désactiver, mais cela n’est pas recommandé. Échantillonnages sont conçu pour que télémétrie connexe est correctement transmis, fins de diagnostic. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Données géographiques incorrect de télémétrie d’utilisateur

La ville, région et des dimensions pays proviennent des adresses IP et ne sont pas toujours précises.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exception « méthode non trouvé » sur l’exécution en Azure Cloud Services

Que vous créez pour .NET 4.6 ? 4.6 n’est pas pris en charge dans les rôles d’Azure Cloud Services automatiquement. [Installer 4.6 chaque rôle](../cloud-services/cloud-services-dotnet-install-dotnet.md) avant l’exécution de votre application.

## <a name="still-not-working"></a>Ne fonctionne toujours ne pas...

* [Forum de perspectives d’application](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

