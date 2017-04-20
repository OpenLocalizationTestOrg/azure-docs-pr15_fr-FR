<properties
   pageTitle="Résoudre les problèmes de Services de Cloud à l’aide de perspectives Application | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes de service cloud à l’aide d’Application perspectives pour traiter les données à partir d’Azure Diagnostics."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Résoudre les problèmes de Services de Cloud à l’aide d’analyse de l’Application

Avec l’extension diagnostics [Azure SDK 2,8](https://azure.microsoft.com/downloads/) et Azure 1,5 vous pouvez maintenant envoyer vos données Azure Diagnostics pour votre Service Cloud directement aux analyses de l’Application. Ouvre une session ETW les différents types de journaux collectées par Diagnostics Azure, y compris les journaux d’applications, les journaux d’événements windows, et compteurs maintenant pouvant être envoyés analyse des applications et de visualisation dans le portail d’Application Insights l’interface utilisateur. Lorsqu’il est utilisé ainsi que le Kit de développement de perspectives Application vous pouvez désormais obtenir des informations sur les indicateurs et journaux provenant de votre application, ainsi que les données de niveau système et de l’infrastructure en provenance de Diagnostics Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurer les Diagnostics Azure pour envoyer des données analyse d’Application

Suivez ces étapes pour votre projet de service cloud pour envoyer des données Azure Diagnostics analyse de l’Application de configuration.

1) Dans l’Explorateur de Visual Studio avec le bouton droit sur un rôle et sélectionnez **Propriétés** pour ouvrir le Concepteur de rôles

![Propriétés du rôle Solution Explorer][1]

2) Dans le Concepteur de rôle sous la section diagnostics, activez la case à cocher pour **Envoyer des données de diagnostics analyse des applications**

![Concepteur de rôles envoyer des données de diagnostics analyse des applications][2]

3) Dans la boîte de dialogue qui s’affiche, sélectionnez la ressource de perspectives d’Application que vous voulez envoyer les données diagnostics de Windows Azure à. La boîte de dialogue vous permet de sélectionner une ressource Insights Application existante à partir de votre abonnement ou spécifier manuellement une clé d’instrumentation pour une ressource d’analyse de l’Application. Si vous ne disposez d’une ressource Application Insights vous pouvez créer sur en cliquant sur le lien **créer une nouvelle ressource** qui ouvre une fenêtre de navigateur au portail classique Azure où vous pouvez créer une ressource de perspectives d’Application. Pour plus d’informations sur la création d’une ressource d’analyse de l’Application, voir [créer une nouvelle ressource Application perspectives](../application-insights/app-insights-create-new-resource.md)

![Sélectionnez la ressource perspectives d’application][3]

4) Une fois que vous avez ajouté la ressource Application perspectives, la touche instrumentation pour cette ressource est stockée comme un paramètre de configuration de service avec le nom **APPINSIGHTS_INSTRUMENTATIONKEY**. Vous pouvez modifier ce paramètre de configuration pour chaque environnement ou de configuration du service en sélectionnant une configuration différente dans la liste déroulante configuration de Service vers le bas et spécifiez une nouvelle clé instrumentation pour cette configuration.

![Sélectionnez configuration du service][4]

Le paramètre de configuration **APPINSIGHTS_INSTRUMENTATIONKEY** est utilisé par Visual Studio pour configurer l’extension diagnostics avec les informations de ressource Application Insights appropriées lors de la publication. Le paramètre de configuration est un moyen pratique de définition de clés instrumentation différentes pour les configurations de services différente. Visual Studio traduire ce paramètre et insérez-le dans la configuration d’extension de diagnostics public lors de la publication. Pour simplifier le processus de configuration de l’extension diagnostics avec PowerShell, le package également la sortie à partir de Visual Studio contient la configuration publique XML avec l’instrumentation Insights Application appropriée clé inclus. Les fichiers de configuration public sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics. <RoleName>. PubConfig.xml. Les déploiements PowerShell basé peuvent utiliser ce modèle pour mapper chaque configuration à un rôle.

5) L’activation de l' **Envoyer des données de diagnostics Application analyse** configure automatiquement les diagnostics de Windows Azure pour envoyer tous les compteurs de performance et les journaux de niveau d’erreur qui sont collectées par l’agent de diagnostics de Windows Azure analyse de l’Application. Si vous voulez continuer à configurer les données envoyées analyse de l’Application, vous devez modifier manuellement le fichier *diagnostics.wadcfgx* pour chaque rôle. Voir [Configurer les Diagnostics Azure pour envoyer des données analyse des applications](../azure-diagnostics-configure-applicationinsights.md) pour en savoir plus sur Mettre à jour manuellement la configuration.

Une fois que le Service Cloud est configuré pour envoyer des données Azure diagnostics analyse application que vous pouvez le déployez dans Azure comme vous le feriez normalement en prenant soin de l’extension diagnostics de Windows Azure est activée. Voir [publication d’un Service Cloud à l’aide de Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Affichage des données dans l’Application Insights diagnostics de Windows Azure
La télémétrie diagnostic Azure s’affichent dans la ressource Insights Application configurée pour votre service cloud.

Voici comment les diagnostics de Windows Azure différents enregistrer les types carte concepts d’analyse de l’Application :  

-  Compteurs de performance sont affichées dans les indicateurs personnalisés dans perspectives d’Application
-  Les journaux d’événements Windows sont affichés sous forme de Traces et des événements personnalisés dans perspectives d’Application
-  Journaux des applications, ETW journaux et les journaux de Diagnostics Infrastructure apparaissent sous forme de Traces dans perspectives Application.

Pour afficher les données de diagnostics de Windows Azure dans perspectives d’Application :

- [Explorer les mesures](../application-insights/app-insights-metrics-explorer.md) permet de visualiser les compteurs de performance personnalisés ou les comptages des différents types d’événements de journal des événements de windows.

![Indicateurs personnalisés dans l’Explorateur de mesures][5]

- Utiliser [la recherche](../application-insights/app-insights-diagnostic-search.md) pour rechercher dans les journaux de suivi différents envoyés par Azure Diagnostics. Pour exemple si vous avez une exception non gérée dans un rôle qui a provoqué le rôle à se bloquer et Corbeille ces informations serait apparaissant dans le canal *d’Application* du *journal des événements Windows*. Vous pouvez utiliser la fonctionnalité de recherche pour examiner l’erreur du journal des événements Windows et obtenir la trace de pile complète pour l’exception de ce qui vous permet de trouver la cause du problème.

![Recherche Traces][6]

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter l’Application Insights SDK à votre service cloud](../application-insights/app-insights-cloudservices.md) pour envoyer des données sur les requêtes, exceptions, dépendances et tout télémétrie personnalisé à partir de votre application. Combiné avec les Diagnostics Azure vous pouvez obtenir une vue complète de vos applications et du système dans la même ressource Application un aperçu des données.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
