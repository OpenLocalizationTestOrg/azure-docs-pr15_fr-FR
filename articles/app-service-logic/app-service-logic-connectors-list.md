<properties
    pageTitle="Liste des liens et des API applications disponibles | Service d’application Microsoft Azure"
    description="En savoir plus sur les connecteurs et les applications de l’API dans le Service d’application Azure"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Liste des liens et des applications de l’API à utiliser dans vos applications de logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version de schéma logique applications 2014-12-01-preview. Pour la version logique applications disponibilité générale, voir [Créer une liste des connecteurs](../connectors/apis-list.md).

En savoir plus sur tous les connecteurs disponibles et applications API créé par Microsoft à utiliser dans vos applications logique.

Pour les informations de tarification et une liste de ce qui est inclus avec chaque niveau de Service, voir [Azure Application Service tarifs](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Pour commencer avec les applications logique avant de vous inscrire pour un compte Azure, accédez à [Essayer logique d’application](https://tryappservice.azure.com/?appservice=logic). Vous pouvez immédiatement créer une application de logique starter courte dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="core-connectors"></a>Connecteurs Core
Le tableau suivant répertorie tous les connecteurs disponibles et applications API créé par Microsoft sont disponibles en tant que connecteurs principaux :

Nom | Description
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Utiliser Bing pour traduire du texte dans une autre langue.
[HTTP](app-service-logic-connector-http.md) | Le récepteur HTTP s’ouvre un point de terminaison qui se comporte comme un serveur HTTP et écoute les demandes entrantes HTTP ou HTTPS. L’action HTTP ne nécessite pas une application API et est pris en charge en mode natif au sein de la logique d’applications.
[Marge](app-service-logic-connector-slack.md) | Connectez-vous à la marge et publier des messages dans les canaux marges.


## <a name="enterprise-integration-connectors"></a>Connecteurs de l’intégration d’entreprise
Le tableau suivant répertorie tous les connecteurs disponibles et applications API créé par Microsoft disponible en tant que connecteurs de l’intégration d’entreprise :

Nom  | Description
------------- | -------------
[Règles de BizTalk](app-service-logic-use-biztalk-rules.md) | Utiliser des règles de BizTalk pour définir et contrôler la logique métier dans une organisation. Stratégies d’entreprise peuvent être mis à jour sans recompilation ou sans redéployez les applications associées.
[BizTalk XPath extraction](app-service-logic-xpath-extract.md) | Recherche et extrait les données à partir du contenu XML basé sur un XPath que vous choisissez.
[Connecteur DB2](app-service-logic-connector-db2.md) | Se connecte à un IBM DB2 base de données en local et sur un ordinateur virtuel Azure exécutant un système d’exploitation Windows. Commandes Informix Structured Query Language pouvez mapper opérations API Web et OData. <br/><br/>Aucun déclencheur. Les actions incluent la table sélectionnez Insertion, mise à jour, supprimer et instruction personnalisée<br/><br/>Ce connecteur inclut également le Client Microsoft pour DRDA pour vous connecter à un serveur Informix via un réseau TCP/IP.
[Fichier](app-service-logic-connector-file.md) | À l’aide de ce connecteur, vous pouvez vous connecter au système de fichiers locaux ou réseau et tâches de fichier différent complète, y compris téléchargement, suppression, la liste de fichiers et plus.
[Informix](app-service-logic-connector-informix.md) | Se connecte à une base de données IBM Informix, en local et sur un ordinateur virtuel Azure exécutant un système d’exploitation Windows. Commandes Informix Structured Query Language pouvez mapper opérations API Web et OData.<br/><br/>Aucun déclencheur. Les actions incluent la table sélectionnez Insertion, mise à jour, supprimer et instruction personnalisée.<br/><br/>Lorsque vous utilisez en local, VPN ou Azure ExpressRoute peut être utilisé. Ce connecteur inclut également un Client Microsoft pour DRDA pour vous connecter à un serveur Informix via un réseau TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se connecte à locale SQL Server ou une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer les entrées d’une table de base de données SQL.
MQ | Se connecte au serveur MQ WebSphere d’IBM version 8, en local et sur un ordinateur virtuel Azure exécutant un système d’exploitation Windows. Lorsque vous utilisez en local, VPN ou Azure ExpressRoute peut être utilisé. Le connecteur inclut également le Client Microsoft pour MQ.<br/><br/>Aucun déclencheur. Aucune action.<br/><br/>**Remarque** Actuellement ne peuvent pas être utilisés avec les applications logique.

## <a name="connectors-as-triggers"></a>Connecteurs en tant que déclencheurs
Plusieurs connecteurs fournissent des déclencheurs pour applications logique. Ces déclencheurs sont de deux types :

1. Sondage déclencheurs : Ces déclencheurs interrogent votre service à une fréquence spécifiée pour vérifier les nouvelles données. Lorsque les nouvelles données ne soient disponibles, une nouvelle instance de votre application logique s’exécute avec les données comme entrée. Pour empêcher les mêmes données d’être utilisé à plusieurs reprises, le déclencheur peut nettoyage des données qui a été lire et transmises à l’application logique. Exemples de ces liens sont fichier et le stockage Azure SQL.
2. Déclencheurs push : Ces déclencheurs écoutent des données sur un point de terminaison ou un événement se produise. Ensuite, déclencher la lecture d’une nouvelle instance d’une application logique. Exemples de ces liens sont récepteur HTTP et Twitter.

## <a name="connectors-as-actions"></a>Connecteurs comme des Actions
Connecteurs peuvent également être utilisés comme des actions au sein de votre application logique. Actions sont utiles pour rechercher des données de l’application logique qui peut ensuite utilisé lors de l’exécution. Par exemple, vous devrez peut-être rechercher des données à partir d’une base de données SQL pour plus d’informations sur un client lors du traitement d’une commande. Ou bien, vous devrez peut-être écrire, mettre à jour ou supprimer des données dans une destination. Pour cela les actions fournies par les connecteurs. Actions établir une correspondance entre les opérations dans les applications de l’API (telle que définie par leurs métadonnées Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Créer vos propres connecteurs et applications API
[Connecteurs et référence des applications API](http://aka.ms/appservicesconnectorreference)  
[Azure déclencheurs d’application API de Service d’application](../app-service-api/app-service-api-dotnet-triggers.md)  
[Référence de l’application logique](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Plus d’informations sur les liens et des API applications
[Quels sont les connecteurs et les applications de l’API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[L’utilisation du Gestionnaire de connexion hybride dans le Service d’application Azure](app-service-logic-hybrid-connection-manager.md)  
[Gérer et analyser vos applications API et les connecteurs intégrés](app-service-logic-monitor-your-connectors.md)
