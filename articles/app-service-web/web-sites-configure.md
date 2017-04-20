<properties 
    pageTitle="Configurer des applications web dans le Service d’application Azure" 
    description="Comment configurer une application web dans les Services d’application Azure" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Configurer des applications web dans le Service d’application Azure #

Cette rubrique explique comment configurer une application web à l’aide du [Portail Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Paramètres de l’application

1. Dans le [Portail Azure], ouvrez la carte pour l’application web.
2. Cliquez sur **tous les paramètres**.
3. Cliquez sur **paramètres de l’Application**.

![Paramètres de l’application][configure01]

La carte de **paramètres de l’Application** dispose de paramètres regroupés sous différentes catégories.

### <a name="general-settings"></a>Paramètres généraux

**Versions framework**. Définir ces options si votre application utilise un de ces structures : 

- **.NET Framework**: définir la version du .NET framework. 
- **PHP**: définir la version PHP, ou **désactivé** pour désactiver PHP. 
- **Java**: sélectionnez la version de Java ou **désactivé** pour désactiver Java. Utilisez l’option **Web conteneur** pour choisir entre les versions Tomcat et jetée.
- **Python**: sélectionnez la version Python, ou **désactivé** pour désactiver les Python.

Pour des raisons techniques, l’activation de Java pour votre application désactive les options .NET, PHP et Python.

<a name="platform"></a>
**Plateforme**. Sélectionne si votre application web s’exécute dans un environnement 32 bits ou 64 bits. L’environnement 64 bits requiert mode Basic ou Standard. Libérez et modes partagé sont toujours exécutent dans un environnement 32 bits.

**Web Sockets**. La valeur **ON** pour activer le protocole WebSocket ; par exemple, si votre application web utilise [ASP.NET SignalR] ou [socket.io].

<a name="alwayson"></a>
**Toujours actif**. Par défaut, les applications web sont décharger s’ils sont inactifs pendant un certain temps. Le système vous permet de conserver des ressources. En mode Basic ou Standard, vous pouvez activer **Toujours sur** conserver l’application chargée tout le temps. Si votre application s’exécute travaux web continue, vous devez activer **Toujours sur**ou les tâches web peuvent ne pas fonctionner correctement.

**Gérées Version du Pipeline**. Définit le [mode pipeline]de IIS. Conservez la valeur intégré (par défaut) à moins d’avoir une application héritée nécessitant une version antérieure de IIS.

**Remplacez l’automatique**. Si vous activez permutation automatique pour un emplacement de déploiement, application Service échangez automatiquement l’application web en production lorsque vous appuyez sur une mise à jour de cet emplacement. Pour plus d’informations, voir [déploiement mis en œuvre des emplacements pour les applications web dans le Service d’application Azure] (web-sites-intermédiaire-publishing.md).

### <a name="debugging"></a>Le débogage

Le **débogage distant**. Active le débogage distant. Lorsque activé, vous pouvez utiliser le débogueur distant dans Visual Studio pour se connecter directement à votre application web. Le débogage distant restera activé pour 48 heures. 

### <a name="app-settings"></a>Paramètres de l’application

Cette section contient des paires nom/valeur qui vous web application va se charger au démarrage vers le haut. 

- Pour les applications .NET, ces paramètres sont injecte dans votre configuration .NET `AppSettings` en cours d’exécution, remplacement des paramètres existants. 

- Applications PHP, Python, Java et nœud peuvent accéder à ces paramètres en tant que variables d’environnement en cours d’exécution. Pour chaque paramètre d’application, deux variables d’environnement sont créés ; une avec le nom spécifié par l’entrée de paramètre d’application et l’autre avec un préfixe de APPSETTING_. Les deux contiennent la même valeur.

### <a name="connection-strings"></a>Chaînes de connexion

Chaînes de connexion pour les ressources liées. 

Pour les applications .NET, ces chaînes de connexion sont injecte dans votre configuration .NET `connectionStrings` paramètres en cours d’exécution, substitution entrées existantes où la clé est égale au nom de la base de données liée. 

Pour les applications PHP, Python, Java et nœud, ces paramètres peuvent être utilisés comme variables d’environnement en cours d’exécution, le préfixe avec le type de connexion. Les préfixes variable d’environnement sont les suivantes : 

- SQL Server :`SQLCONNSTR_`
- MySQL :`MYSQLCONNSTR_`
- Base de données SQL :`SQLAZURECONNSTR_`
- Personnalisé :`CUSTOMCONNSTR_`

Par exemple, si une chaîne de connexion MySql ont été nommée `connectionstring1`, il est accessible via la variable d’environnement `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documents par défaut

Le document par défaut est la page web qui s’affiche à l’URL racine pour un site Web.  Le premier fichier correspondant dans la liste est utilisé. 

Applications Web peuvent utiliser des modules itinéraire basée sur URL plutôt que fournit du contenu statique, auquel cas il n’existe aucun document par défaut en tant que tels.    

### <a name="handler-mappings"></a>Mappages de gestionnaires

Utilisez cette zone pour ajouter des processeurs de script personnalisé pour gérer les demandes d’extensions de fichier spécifiques. 

- **Numéro de poste**. Extension de fichier à être gérés, par exemple *.php ou handler.fcgi. 
- **Chemin d’accès du processeur de script**. Le chemin d’accès absolu du processeur script. Les demandes de fichiers qui correspondent à l’extension de fichier seront traités par le processeur de script. Utiliser le chemin d’accès `D:\home\site\wwwroot` pour faire référence au répertoire racine de votre application.
- **Arguments supplémentaires**. Arguments de ligne de commande facultatifs pour le processeur de script 


### <a name="virtual-applications-and-directories"></a>Annuaires et applications virtuelles 
 
Pour configurer les annuaires et applications virtuelles, spécifiez chaque répertoire virtuel et son chemin d’accès physique correspondant par rapport à la racine du site Web. Si vous le souhaitez, vous pouvez sélectionner la case à cocher de **l’Application** pour marquer un répertoire virtuel en tant qu’application.


## <a name="enabling-diagnostic-logs"></a>Activer les journaux de diagnostic

Pour activer les journaux de diagnostic :

1. Dans la carte pour votre application web, cliquez sur **tous les paramètres**.
2. Cliquez sur **les journaux de Diagnostic**. 

Options pour écrire les journaux de diagnostic à partir d’une application web qui prend en charge enregistrement : 

- **L’application enregistre**. Écrit des journaux d’application dans le système de fichiers. Journalisation s’étend sur une période de 12 heures. 

**Niveau**. Lors de l’application la journalisation est activée, cette option spécifie la quantité d’informations qui seront enregistré (erreur, avertissement, informations ou commentaires).

**Journalisation sur le serveur web**. Les journaux sont enregistrés dans le format de fichier journal étendu W3C. 

**Messages d’erreur détaillés**. Fichiers .htm des messages d’erreur détaillés enregistre. Les fichiers sont enregistrés sous /LogFiles/DetailedErrors. 

**Suivi des requêtes a échoué**. Journaux d’échec de demandes vers des fichiers XML. Les fichiers sont enregistrés sous /LogFiles/W3SVC*xxx*, où xxx est un identificateur unique. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Veillez à télécharger le fichier XSL, car il fournit des fonctionnalités de mise en forme et le contenu des fichiers XML de filtrage.

Pour afficher les fichiers journaux, vous devez créer des informations d’identification FTP, comme suit :

1. Dans la carte pour votre application web, cliquez sur **tous les paramètres**.
2. Cliquez sur **informations d’identification de déploiement**.
3. Entrez un nom d’utilisateur et mot de passe.
4. Cliquez sur **Enregistrer**.

![Définir les informations d’identification du déploiement][configure03]

Le nom d’utilisateur FTP complè est « app\username » dans lequel *l’application* est le nom de votre application web. Le nom d’utilisateur est répertorié dans la carte de l’application web, sous **Essentials**.  

![Informations d’identification du déploiement FTP][configure02]

## <a name="other-configuration-tasks"></a>Autres tâches de configuration

### <a name="ssl"></a>SSL 

En mode Basic ou Standard, vous pouvez télécharger des certificats SSL pour un domaine personnalisé. Pour plus d’informations, voir [Activer HTTPS pour une application web]. 

Pour afficher vos certificats téléchargés, cliquez sur **Tous les paramètres** > **domaines personnalisés et SSL**.

### <a name="domain-names"></a>Noms de domaine

Ajouter des noms de domaine personnalisé pour votre application web. Pour plus d’informations, voir [configurer un nom de domaine personnalisé pour une application web dans le Service d’application Azure].

Pour afficher vos noms de domaine, cliquez sur **Tous les paramètres** > **domaines personnalisés et SSL**.

### <a name="deployments"></a>Déploiements

- Configurer la déploiement continue. Reportez-vous [à l’aide de Git pour déployer des applications Web dans le Service d’application Azure](./web-sites-deploy.md).
- Emplacements de déploiement. Voir [déployer dans les environnements de mise en attente pour les applications Web dans le Service d’application Azure].


Pour afficher vos connecteurs de déploiement, cliquez sur **Tous les paramètres** > **emplacements de déploiement**.

### <a name="monitoring"></a>Surveillance des mots clés

En mode Basic ou Standard, vous pouvez tester la disponibilité des points de terminaison HTTP ou HTTPS, à partir d’emplacements distribués geo jusqu'à trois. Un test de surveillance échoue si le code de réponse HTTP est une erreur (4xx ou 5xx) ou la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible si les tests de surveillance imprimés à partir de tous les emplacements spécifiés. 

Pour plus d’informations, voir [Comment : surveiller l’état de point de terminaison web].

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application], où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer un nom de domaine personnalisé dans le Service d’application Azure]
- [Activer HTTPS pour une application dans le Service d’application Azure]
- [Mettre à l’échelle une application web dans le Service d’application Azure]
- [Concepts de base de surveillance pour les applications Web dans le Service d’application Azure]

<!-- URL List -->

[SignalR ASP.NET]: http://www.asp.net/signalr
[Portail Azure]: https://portal.azure.com/
[Configurer un nom de domaine personnalisé dans le Service d’application Azure]: ./web-sites-custom-domain-name.md
[Déployer dans les environnements de mise en attente pour les applications Web dans le Service d’application Azure]: ./web-sites-staged-publishing.md
[Activer HTTPS pour une application dans le Service d’application Azure]: ./web-sites-configure-ssl-certificate.md
[Comment : contrôler l’état de point de terminaison web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Concepts de base de surveillance pour les applications Web dans le Service d’application Azure]: ./web-sites-monitor.md
[mode pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Mettre à l’échelle une application web dans le Service d’application Azure]: ./web-sites-scale.md
[Socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Essayez de Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
