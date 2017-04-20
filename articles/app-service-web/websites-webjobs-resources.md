<properties 
    pageTitle="Ressources de documentation WebJobs Azure" 
    description="Ressources recommandées pour apprendre à utiliser Azure WebJobs et le Kit de développement WebJobs Azure." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Ressources de documentation WebJobs Azure

## <a name="overview"></a>Vue d’ensemble

Cette rubrique fournit des ressources de documentation sur l’utilisation Azure WebJobs et le Kit de développement WebJobs Azure. WebJobs Azure offrent un moyen facile pour exécuter des scripts ou des programmes en tant que processus d’arrière-plan dans le contexte d’une [application de Service web app, API application ou une application mobile](../app-service/app-service-value-prop-what-is.md). Vous pouvez télécharger et exécuter un fichier exécutable comme comme cmd, bat, exe (.NET), ps1, feuille, php, copier, js et jar. Ces programmes s’exécuter en tant que WebJobs sur un planning (cron) ou en permanence.

L’objectif du [Kit de développement logiciel WebJobs](websites-webjobs-resources.md) est pour simplifier le code que vous écrivez pour les tâches courantes qu’un WebJob peuvent effectuer, par exemple de traitement d’images, traitement en file d’attente, RSS d’agrégation, gestion des fichiers et envoi de messages électroniques. Le SDK WebJobs possède des fonctionnalités intégrées pour travailler avec le stockage Azure et Bus des services, pour la planification des tâches et gestion des erreurs et de nombreux autres scénarios courants. En outre, il est conçu pour être extensible, et il est d' [Ouvrir le référentiel source pour les extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Fonctions d’Azure](../azure-functions/functions-overview.md) (actuellement en preview) est basé sur une version du WebJobs SDK qui fonctionne avec c# script Node.js et d’autres langues. 

Création, le déploiement et gestion des WebJobs sont transparente avec des outils intégrés dans Visual Studio. Vous pouvez créer WebJobs à partir de modèles, publier et gérer (exécution/arrêter/moniteur/débogage) les. 

Le tableau de bord WebJobs dans le portail Azure fournit les fonctionnalités de gestion puissantes qui vous donnent un contrôle total sur l’exécution de WebJobs, y compris la possibilité d’appeler des fonctions individuelles dans WebJobs. Le tableau de bord affiche également la sortie de journalisation et de fonction exécutions. 

##<a name="getstarted"></a>Prise en main WebJobs et WebJobs SDK

* [Présentation des WebJobs Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [WebJobs Azure sont Isard et vous devez commencer à l’aise immédiatement !](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Billet de Blog par recherche de Troie).
* [Fonctionnalités WebJobs Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Quel est le WebJobs SDK](websites-dotnet-webjobs-sdk.md)
* [Conseils de travaux d’arrière-plan par Microsoft modèles et pratiques](/documentation/articles/best-practices-background-jobs/)
* [Annonce de la 1.1.0 RTM du Kit de développement Microsoft Azure WebJobs](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Prise en main du Kit de développement WebJobs Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Comment utiliser le stockage Azure file d’attente avec WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [L’utilisation de stockage d’objets blob Azure avec WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [L’utilisation de stockage de table Azure avec WebJobs SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Comment utiliser Bus des services Azure avec WebJobs SDK](websites-dotnet-webjobs-sdk-service-bus.md)
* [Comment utiliser WebHooks avec WebJobs SDK, avec exemples pour GitHub, IFTTT et HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK aide-mémoire (téléchargement PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentation présentée sous de WebJobs paramètres GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Vidéos
    * [WebJobs et le Kit de développement WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Série de vidéos WebJobs Azure sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Présentation de WebJobs outils pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Outils WebJobs et le débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Azure WebJobs mise à jour avec Pranav Rastogi - extensibilité de version 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consultez également les sections suivantes sur [WebJobs déploiement](#deploy) et [de test et de débogage WebJobs](#debug).

##<a name="deploy"></a>Déploiement WebJobs

* [Comment déployer Azure WebJobs à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Comment déployer WebJobs à l’aide du portail Azure](web-sites-create-web-jobs.md)
* [L’activation de remise continue ou de ligne de commande de WebJobs Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [GIT déployer une application console .NET Azure à l’aide de WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Déployer un WebJob F # Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Déploiement des services personnalisés en tant que Webjobs Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vidéos
    * [Présentation de WebJobs outils pour Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Outils WebJobs et le débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Planification WebJobs

* [La boîte de dialogue WebJob Azure ajouter](websites-dotnet-deploy-webjobs.md#configure)
* [Créer un WebJob planifiée dans le portail Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Connecter une tâche du planificateur à un WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Planification Azure WebJobs avec les expressions cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Planification des fonctions WebJob individuelles à l’aide de la TimerTrigger SDK WebJobs](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Test et débogage WebJobs

* [Nouveau développeur et débogage des fonctionnalités pour Azure WebJobs dans Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Afficher le tableau de bord WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Comment écrire des journaux en utilisant WebJobs SDK et les afficher dans le tableau de bord](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs débogage à distance](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Auteur ce blob ?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Code d’hébergement interactif dans le Cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Ajout de suivi à WebJobs Azure](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Surveiller, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vidéos
    * [Outils WebJobs et le débogage distant](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Mise à l’échelle WebJobs

* [Mise à l’échelle de votre Application Web avec des sites Web Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure application Service : créer l’architecture des applications Web prêt pour les entreprises de grande échelle](https://channel9.msdn.com/Events/Build/2014/3-626). Décrit l’échelle des applications web avec WebJobs, y compris le SDK WebJobs.
* Vidéos
    * [Mise à l’échelle WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Ressources WebJobs supplémentaires

* [Billet de blog WebJobs disponibilité générale Azure par Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Exécution des travaux de Powershell Web Azure service d’application](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Recevoir une notification lorsque votre Azure déclenchée WebJobs terminée](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Stratégie de rétention de sauvegarde d’application Web simple avec WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Applications Web azure et Services de Cloud lente à la première requête](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Montre comment utiliser WebJobs afin de simuler la fonctionnalité AlwaysOn qui est disponible uniquement pour le niveau de tarification Standard.
* [Arrêt WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Arrêt approprié pour WebJobs SDK, voir [arrêt](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatiser les sauvegardes avec Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vidéos
    * [Azure vidéos WebJobs par Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Série de vidéos WebJobs Azure sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Ressources WebJobs SDK supplémentaires

* [Notes de mise à jour du Kit de développement logiciel WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [WebJobs Kit de développement de code source](https://github.com/Azure/azure-webjobs-sdk)
* [Kit de développement logiciel WebJobs extensions de code source](https://github.com/Azure/azure-webjobs-sdk-extensions), avec [guide détaillé pour le modèle d’extensibilité](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Code source du Script du Kit de développement logiciel WebJobs](https://github.com/Azure/azure-webjobs-sdk-script/) (utilisé pour les [Fonctions Azure](../azure-functions/functions-overview.md))
* [WebJob télécharger des fichiers FREB au stockage Azure en utilisant WebJobs SDK](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hébergement webjobs Azure en dehors d’Azure, avec les avantages de journalisation à partir d’un Azure hébergé webjob](http://bypassion.dk/?p=510)
* [Création d’un outil d’importation de données avec Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Vue d’ensemble des fonctions Azure](../azure-functions/functions-overview.md)
* Vidéos
    * [Série de vidéos WebJobs Azure sur Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Exemples d’applications WebJob

* [Exemples d’application fournis par l’équipe WebJobs sur GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Dans le navigateur Azure simple avec WebJobs Backend en utilisant WebJobs SDK](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Montre comment utiliser WebJobs planifiées et événementielles. Consultez le blog [reconstruisant le SiteMonitR à l’aide Azure WebJobs SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog Azure](/blog)
* [Blog d’Amit Apple](http://blog.amitapple.com/). Se concentre sur WebJobs (pas le Kit de développement).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtenir de l’aide avec WebJobs

* [StackOverflow pour WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow pour le Kit de développement WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow pour les fonctions Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Forum Azure et ASP.NET](http://forums.asp.net/1247.aspx)
* [Azure forum application Service Web Apps](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site Web applications utilisateur vocale Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Utilisez le hashtag #AzureWebJobs.
* [Signaler un problème ou bogue WebJobs](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

