<properties 
    pageTitle="L’utilisation du Gestionnaire de connexion hybride | Microsoft Azure" 
    description="Installer et configurer le Gestionnaire de connexions hybride et se connecter aux connecteurs local dans les applications de logique" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Se connecter aux connecteurs locales à l’aide du Gestionnaire de connexions hybride

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2014-12-01-aperçu. Logique applications officielle (disponibilité générale) utilise une passerelle pour la connectivité locale. En savoir plus sur la nouvelle [passerelle](app-service-logic-gateway-connection.md) et [Logique applications disponible](https://azure.microsoft.com/documentation/services/logic-apps/).

Pour utiliser un système local, applications logique utilise le Gestionnaire de connexions hybride. Certains connecteurs peuvent se connecter à un système en local, telles que SQL Server, SAP, SharePoint et ainsi de suite. 

Le Gestionnaire de connexion hybride (HCM) est un clic : une fois les installer qui est installée sur un serveur IIS au sein de votre réseau, derrière le pare-feu. À l’aide d’un relais Bus des services Azure, HCM authentifie le système en local avec le lien dans Azure. 

> [AZURE.NOTE] Gestionnaire de connexions hybride est requis uniquement si vous vous connectez à une ressource locale derrière le pare-feu. Si vous vous connectez pas à un système local, vous ne devez le Gestionnaire de connexions hybride.

Pour commencer, vous devez :

- Azure Bus des services relais espace de noms sa chaîne de connexion. Consultez [Tarification Bus Service](https://azure.microsoft.com/pricing/details/service-bus/) pour déterminer le niveau inclut relais.
- En local connexion informations système, y compris le nom d’utilisateur et mot de passe. Par exemple, si vous vous connectez à un serveur SQL local, vous devez le compte de connexion SQL Server et le mot de passe.
- Informations de serveur local, y compris port numéro et nom de serveur. Par exemple, si vous vous connectez à un serveur SQL local, vous devez le nom de SQL Server et le numéro de port TCP.

## <a name="get-the-service-bus-connection-string"></a>Obtenir la chaîne de connexion de Service Bus

Dans le portail Azure, copier la racine de Service Bus chaîne de connexion associations de sécurité. Cette chaîne de connexion connecte votre lien Azure à votre système local. 

1. Dans le [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), sélectionnez votre espace de noms Bus des services, puis sélectionnez les **Informations de connexion**:

    ![][SB_ConnectInfo]

2. Copiez la chaîne de connexion associations de sécurité :

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Installer le Gestionnaire de connexions hybride

1. Dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), sélectionnez le lien que vous avez créé. Pour l’ouvrir, vous pouvez sélectionnez **Parcourir**, sélectionnez **Applications API**, puis votre lien ou API application. 
<br/><br/>
Dans **Connexion hybride**, le programme d’installation est **incomplète**:
<br/>
![][2] 

2. Sélectionnez **connexion hybride**. La chaîne de connexion Bus des services que vous avez précédemment entré est répertoriée.
3. Copiez la **chaîne de Configuration principal**:
<br/>
![][PrimaryConfigString]

4. Sous **Gestionnaire de connexions hybrides en local**, vous pouvez télécharger le Gestionnaire de connexion hybride ou installer directement à partir du portail. 
<br/><br/>
Pour installer directement à partir du portail, accédez à votre serveur IIS en local, accédez au portail, cliquez sur **télécharger et configurer**.
<br/><br/>
Pour télécharger le Gestionnaire de connexions hybride, accédez à votre serveur IIS en local et accédez à l' **application ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). L’installation démarre automatiquement afin que vous pouvez l’exécuter.

5. Dans la fenêtre **Paramètres récepteur** , entrez la **Chaîne de Configuration primaire** collé précédemment (à l’étape 3), puis sélectionnez **installer**.

Quand le programme d’installation est terminée, le suivant s’affiche :
<br/>
![][3] 

Maintenant lorsque vous naviguez vers le connecteur à nouveau, le statut de connexion hybride est **connecté**. Vous devrez peut-être fermer le connecteur et rouvrez celui-ci :
<br/>
![][4] 

> [AZURE.NOTE] Pour basculer vers la chaîne de connexion secondaire, exécutez à nouveau le programme d’installation hybride connexion et entrez la **Chaîne de Configuration secondaire**.


## <a name="tcp-ports-and-security"></a>Ports TCP et la sécurité

Lorsque vous créez une connexion hybrides, un site Web est créé sur votre serveur IIS local en local. Le serveur IIS peut se trouver dans une DMZ. La configuration requise ports TCP sur le serveur IIS est les suivantes :

Ports TCP | Pourquoi
--- | ---
 | Aucun ports TCP entrants ne sont requis.
9350 - 9354 | Ces ports sont utilisés pour la transmission des données. Le Gestionnaire de relais Bus des services détecte port 9350 pour déterminer si la connectivité TCP est disponible. Si elle est disponible, il suppose que port 9352 est également disponible. Le trafic de données dépasse port 9352. <br/><br/>Autoriser les connexions sortantes à ces ports.
5671 | Lorsque le port 9352 est utilisé pour le trafic de données, port 5671 est utilisée comme la le canal de contrôle. <br/><br/>Autoriser les connexions sortantes à ce port. 
80, 443 | Si les ports 9352 et 5671 ne sont pas utilisables, *puis* les ports 80 et 443 sont les ports secours utilisés pour la transmission des données et le canal de contrôle.<br/><br/>Autoriser les connexions sortantes à ces ports.
Port du système local | Sur le système local, ouvrez le port utilisé par le système. Par exemple, SQL Server utilise généralement le port 1433. Ouvrez ce port TCP.

[Hébergement derrière un pare-feu avec Bus des services](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Résolution des problèmes

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Résolution des problèmes en local

1. Sur le serveur IIS, vérifiez le rôle web IIS est installé et tous les services IIS sont démarrés.
2. Sur le serveur IIS, vérifiez le Gestionnaire de connexions hybride est installé et en cours d’exécution :
 - Dans le Gestionnaire IIS (inetmgr), le site Web ***MicrosoftAzureBizTalkHybridListener*** doit figurer et en cours d’exécution. 
 - Ce site Web utilise le ***HybridListenerAppPool*** qui s’exécute *emploient* le compte d’utilisateurs locaux intégrés. Ce pool d’applications doit également être démarré.
3. Sur le serveur IIS, vérifiez que le connecteur est installé et en cours d’exécution : 
 - Un site Web est créé pour votre lien. Par exemple, si vous avez créé un connecteur SQL, il est un site Web ***MicrosoftSqlConnector_nnn*** . Dans le Gestionnaire IIS (inetmgr), vérifiez ce site Web est répertorié et démarré. 
 - Ce site Web utilise son propre pool d’applications IIS nommé ***HybridAppPoolnnn***. Ce pool d’applications s’exécute *emploient* le compte d’utilisateurs locaux intégrés. Ce site Web et le pool d’applications doivent tous deux être démarré. 
 - Parcourir le connecteur local. Par exemple, si votre site Web de connecteur utilise le port 6569, accédez à http://localhost:6569. Un document par défaut n’est pas configuré pour une `HTTP Error 403.14 - Forbidden error` est attendue.
4. Dans votre pare-feu, vérifiez les Ports TCP répertoriés dans cette rubrique sont ouverts.
5. Examinez le système source ou cible :
 - Certains systèmes locaux nécessitent des fichiers de dépendance supplémentaires. Par exemple, si vous vous connectez à SAP en local, certains fichiers SAP supplémentaires doivent être installés sur le serveur IIS.
 - Vérifiez la connectivité au système avec le compte de connexion. Par exemple, le port TCP utilisé par le système doit être ouvert, comme le port 1433 pour SQL Server. Le compte de connexion que vous avez entré dans le portail Azure doit avoir accès au système.
6. Sur le serveur IIS, vérifiez les journaux d’événements pour rechercher des erreurs. 
7. Nettoyage de disque et réinstaller le Gestionnaire de connexions hybride : 
 - Dans IIS, supprimez manuellement le site Web de connecteur et son pool d’applications. 
 - Exécutez à nouveau le Gestionnaire de connexions hybride et confirmez que vous entrez la **Chaîne de Configuration principal** appropriée pour votre lien.



### <a name="in-the-azure-classic-portal"></a>Dans le portail classique Azure

1. Vérifiez que l’espace de noms de Service Bus a un état **actif** .
2. Lorsque vous créez le connecteur, entrez la chaîne de connexion de Service Bus sa. N’entrez pas la chaîne de connexion ACS.


## <a name="faq"></a>FAQ

**QUESTION**: il existe deux gestionnaires de connexion hybride. Quelle est la différence ? 

**Answer**: il est la technologie [Hybride connexions](../biztalk-services/integration-hybrid-connection-overview.md) qui est utilisée principalement par des applications Web (anciennement sites Web) et les applications Mobile (anciennement mobiles services) pour vous connecter à local. Ce gestionnaire de connexions hybride est dans sa propre [configuration](../biztalk-services/integration-hybrid-connection-create-manage.md) et utilise un BizTalk Service Azure (en coulisses). Il prend en charge les protocoles TCP et HTTP uniquement.

Avec les connecteurs Azure Application Service, nous avons également un gestionnaire de connexions hybride.  Ce gestionnaire de connexions hybride signifie n’utilise *pas* un BizTalk Azure du Service (en coulisses) et prend en charge plus que les protocoles TCP et HTTP. Voir les [liens et des API liste d’applications](app-service-logic-connectors-list.md).

Les deux utilisent Bus des services Azure pour vous connecter au système local.

**QUESTION**: lorsque je crée une application API personnalisée, puis-je utiliser le Gestionnaire de connexions application Service hybride pour vous connecter à en local ? 

**Réponse**: pas dans le sens traditionnel. Vous pouvez utiliser un connecteur intégré, configurez le Gestionnaire de connexions application Service hybrides pour vous connecter au système local. Ensuite, utilisez ce connecteur avec votre application API personnalisé, en utilisant une application logique. Pour l’instant, vous ne pouvez pas développer ou créer votre propre API application hybride (par exemple, le connecteur SQL ou fichier).

Si votre API personnalisé utilise un port TCP ou HTTP, vous pouvez utiliser des [Connexions hybride](../biztalk-services/integration-hybrid-connection-overview.md) et son gestionnaire de connexions hybride. Dans ce scénario, un BizTalk Service Azure est utilisé. [Se connecter à locale SQL Server à partir d’une application web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) peuvent vous aider.  


## <a name="read-more"></a>En savoir plus

[Surveiller des applications de votre logique](app-service-logic-monitor-your-logic-apps.md)<br/>
[Tarifs Bus du service](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
