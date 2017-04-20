<properties
    pageTitle="Le débogage d’une application web Node.js dans le Service d’application Azure"
    description="Découvrez comment déboguer une application web Node.js dans le Service d’application Azure."
    tags="azure-portal"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Le débogage d’une application web Node.js dans le Service d’application Azure

Azure fournit des diagnostics intégrés pour vous aider à déboguer des applications Node.js hébergées dans [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. Dans cet article, vous allez apprendre comment activer la journalisation des stdout et stderr, afficher des informations d’erreur dans le navigateur et comment télécharger et afficher les fichiers journaux.

Diagnostics pour les applications Node.js hébergées sur Azure est fourni par [IISNode]. Tandis que cet article décrit les paramètres les plus courants pour recueillir des informations de diagnostic, il ne fournit pas une référence complète pour l’utilisation de IISNode. Pour plus d’informations sur l’utilisation de IISNode, reportez-vous au [Fichier Lisezmoi IISNode] sur GitHub.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Activer la journalisation

Par défaut, une application web application Service capture uniquement les informations de diagnostic sur les déploiements, tels que lorsque vous déployez une application web à l’aide de Git. Ces informations sont utiles si vous rencontrez des problèmes lors du déploiement, telle que l’échec pendant l’installation d’un module référencé dans **package.json**, ou si vous utilisez un script de déploiement personnalisé.

Pour activer la journalisation de flux stdout et stderr, vous devez créer un fichier **IISNode.yml** à la racine de votre application Node.js et ajoutez les éléments suivants :

    loggingEnabled: true

Cela active la journalisation des stderr et stdout à partir de votre application Node.js.

Le fichier de **IISNode.yml** peut également être utilisé pour contrôler si les erreurs liées à conviviales ou développeur sont renvoyées au navigateur lorsqu’une erreur se produit. Pour activer les erreurs de développeur, ajoutez la ligne suivante vers le fichier **IISNode.yml** :

    devErrorsEnabled: true

Une fois que cette option est activée, IISNode renvoie la dernière 64 Ko des informations envoyées à stderr au lieu d’une erreur conviviale telles que « une erreur interne s’est produite ».

> [AZURE.NOTE] DevErrorsEnabled est utile lorsque diagnostiquer les problèmes pendant le développement, l’activation dans un environnement de production peut entraîner des erreurs de développement envoyés aux utilisateurs finaux.

Si le fichier **IISNode.yml** n’existe pas déjà dans votre application, vous devez redémarrer votre application web après la publication de l’application mis à jour. Si vous changez simplement les paramètres dans un fichier **IISNode.yml** existant qui a déjà été publié, aucun redémarrage n’est requis.

> [AZURE.NOTE] Si votre application web a été créée en utilisant les outils de ligne de commande Azure ou les applets de commande PowerShell Azure, un fichier **IISNode.yml** par défaut est automatiquement créé.

Pour redémarrer l’application web, sélectionnez l’application web dans le [Portail Azure](https://portal.azure.com), puis cliquez sur le bouton **redémarrer** :

![Redémarrez bouton][restart-button]

Si les outils de ligne de commande Azure sont installés dans votre environnement de développement, vous pouvez utiliser la commande suivante pour redémarrer l’application web :

    azure site restart [sitename]

> [AZURE.NOTE] Tandis que loggingEnabled et devErrorsEnabled sont les plus couramment utilisés IISNode.yml les options de configuration pour capturer les informations de diagnostic, IISNode.yml peut servir à configurer un nombre d’options pour votre environnement d’hébergement. Pour une liste complète des options de configuration, consultez le fichier [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>L’accès aux journaux

Les journaux de diagnostic sont accessibles de trois façons ; À l’aide du protocole FTP (File Transfer), le téléchargement d’une archive Zip, ou en tant que live mis à jour de flux de données du journal (également appelé une queue). Télécharger l’archive Zip des fichiers journaux ou afficher le flux live nécessitent les outils de ligne de commande Azure. Elles peuvent être installées à l’aide de la commande suivante :

    npm install azure-cli -g

Une fois installée, les outils sont accessibles à l’aide de la commande « azure ». Les outils de ligne de commande doivent d’abord être configurés pour utiliser votre abonnement Azure. Pour plus d’informations sur la façon d’effectuer cette tâche, voir la **comment télécharger et importer les paramètres de publication** section de l’article [comment utiliser les outils de ligne de commande Azure](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Pour accéder aux informations de diagnostic via FTP, visitez le [Portail Azure](https://portal.azure.com), sélectionnez votre application web, puis le **tableau de bord**. Dans la section **liens rapides** , les liens **Envoie via FTP les journaux de DIAGNOSTIC** et **Les journaux de DIAGNOSTIC FTP** fournissent l’accès aux journaux à l’aide du protocole FTP.

> [AZURE.NOTE] Si vous n’avez pas déjà configuré nom d’utilisateur et mot de passe FTP ou le déploiement, vous pouvez le faire à partir de la page de gestion de **démarrage rapide** en sélectionnant **configurer les informations d’identification de déploiement**.

L’URL du serveur FTP renvoyés dans le tableau de bord est pour le répertoire de **fichiers journaux** , qui contient les sous-répertoires suivants :

* [Méthode de déploiement](web-sites-deploy.md) - si vous utilisez une méthode de déploiement tels que Git, un répertoire du même nom sera créé et contiendra relatives aux déploiements.

* nodejs - informations Stdout et stderr capturés à partir de toutes les instances de votre application (lorsque loggingEnabled est true).

###<a name="zip-archive"></a>Archive ZIP

Pour télécharger une archive Zip des journaux de diagnostic, utilisez la commande suivante à partir des outils de ligne de commande Azure :

    azure site log download [sitename]

Cela va télécharger un **diagnostics.zip** dans le répertoire actif. Cette archive contient l’arborescence suivante :

* déploiements - journal des informations sur les déploiements de votre application

* Fichiers journaux

    * [Méthode de déploiement](web-sites-deploy.md) - si vous utilisez une méthode de déploiement tels que Git, un répertoire du même nom sera créé et contiendra relatives aux déploiements.

    * nodejs - informations Stdout et stderr capturés à partir de toutes les instances de votre application (lorsque loggingEnabled est true).

###<a name="live-stream-tail"></a>Flux en direct (fin)

Pour afficher un flux en direct des informations de journal de diagnostic, utilisez la commande suivante à partir des outils de ligne de commande Azure :

    azure site log tail [sitename]

Cette opération retourne un flux d’événements, qui sont mises à jour qu’ils se produisent sur le serveur. Ce flux renverra les informations relatives au déploiement ainsi que des informations stdout et stderr (lorsque loggingEnabled est true).

<a id="nextsteps"></a>
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment activer et accéder aux informations de diagnostic pour Azure. Ces informations est utiles dans les problèmes de présentation qui se produisent avec votre application, elle peut pointer vers un problème avec un module que vous utilisez ou que la version de Node.js utilisé par l’application de Service Web Apps est différente de celui utilisé dans votre environnement de déploiement.

Pour plus d’informations dans utiliser des modules sur Azure, voir [Utilisation des Modules Node.js avec les Applications Azure](../nodejs-use-node-modules-azure-apps.md).

Pour plus d’informations sur la spécification d’une version Node.js pour votre application, voir [spécification d’une version Node.js dans une application Azure].

Pour plus d’informations, voir également le [Centre de développement Node.js](/develop/nodejs/).

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

[IISNode]: https://github.com/tjanczuk/iisnode
[Lisez-moi IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Spécifier une version Node.js dans une application Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
