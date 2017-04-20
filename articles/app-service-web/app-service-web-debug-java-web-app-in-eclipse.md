<properties 
    pageTitle="Déboguer une application Web Java sur Azure dans Eclipse | Microsoft Azure" 
    description="Ce didacticiel vous montre comment utiliser la boîte à outils Azure pour Eclipse pour déboguer une application Web Java s’exécutant sur Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Déboguer une application Web Java sur Azure dans Éclipse

Ce didacticiel montre comment déboguer une application Web Java s’exécutant sur Azure à l’aide de la [Boîte à outils Azure pour Eclipse]. Pour simplifier la démonstration, vous allez utiliser un exemple simple Java Server Page (JSP) pour ce didacticiel, mais la procédure est similaire pour un servlet Java lorsque vous effectuez le débogage Azure.

Lorsque vous avez terminé ce didacticiel, votre application ressemblera à l’illustration suivante lorsque vous effectuez le débogage dans Eclipse :

![][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un développeur Kit JDK (Java), v 1,8 ou version ultérieure.
* Eclipse IDE pour les développeurs EE Java, Indigo ou version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure proviennent de <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* La boîte à outils Azure pour Eclipse. Pour plus d’informations, voir [la boîte à outils Azure pour Eclipse].
* Un projet Web dynamiques créés et déployés sur Azure Application Service ; par exemple, voir [créer une application Web Hello World pour Azure dans Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Déboguer une application Web Java sur Azure

Pour effectuer ces étapes dans cette section, vous pouvez utiliser un projet Web dynamique existant auquel vous avez déjà déployé comme une application Web Java sur Azure, vous téléchargez un [Exemple de projet Web dynamique] et suivez les étapes décrites dans [créer une application Web Hello World pour Azure dans Eclipse] pour déployer sur Azure. 

1. Ouvrez Eclipse.

1. Configurer les délais d’expiration pour le débogage distant :

    1. Cliquez sur le menu **Windows** dans Eclipse, puis cliquez sur **Préférences**.
    1. Développez le nœud **Java** , puis sélectionnez **Déboguer**.
    1. Configurer les paramètres pour le **délai d’expiration de débogueur (ms)** et la **barre de lancement délai (millisecondes)** `120000`.

        ![][02]

    1. Cliquez sur **OK** pour fermer la boîte de dialogue **Préférences** .

1. En mode d’Explorateur de projets de Eclipse, le bouton droit sur le projet Web dynamique dont vous avez déployé sur Azure. Lorsque le menu contextuel s’affiche, sélectionnez **Déboguer en tant que**, puis cliquez sur **Azure Web App**.

    ![][03]

1. Si c’est la première fois que vous effectuez le débogage votre projet Web dynamique, la boîte de dialogue **Configurations déboguer** s’ouvre. Vous pouvez accepter les valeurs par défaut qui sont spécifiées par la boîte à outils sous l’onglet **se connecter** . Sous l’onglet **Source** , cliquez sur **Ajouter**, puis sur **projet Java**, sélectionnez **Projet Web dynamique**, puis cliquez sur **OK**. Une fois que vous avez effectué ces étapes, cliquez sur **Déboguer**.

    ![][04]

1. Lorsque vous êtes invité à **Activer le débogage distant dans l’application Web distante maintenant ?**, cliquez sur **OK**.

1. Lorsque vous êtes invité **votre application web est maintenant prête pour le débogage distant**, cliquez sur **OK**.

    ![][05]

1. Lorsque la boîte de dialogue **Configurations déboguer** réapparaît, cliquez sur **Déboguer**.

1. Invite de commande Windows ou shell Unix vous ouvrez et préparer la connexion nécessaires pour le débogage ; Vous devez patienter jusqu'à ce que la connexion à votre application Web Java remote réussit avant de poursuivre. Si vous utilisez Windows, il ressemblera à l’illustration suivante.

    ![][06]

1. Insérer un point d’arrêt dans votre page JSP, puis ouvrez l’URL de votre application Web Java dans un navigateur :

    1. Ouvrez **L’Explorateur Azure** dans Eclipse.
    1. Accéder aux **Applications Web** et l’application Web Java que vous souhaitez déboguer.
    1. Cliquez avec le bouton droit sur l’application Web, puis cliquez sur **Ouvrir dans le navigateur**.
    1. Éclipse êtes prêt à entrer en mode débogage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

Pour plus d’informations sur la création d’Azure Web Apps, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ressources Azure pour Éclipse]: ../azure-toolkit-for-eclipse.md
[Installez le Kit de ressources de Azure pour Éclipse]: ../azure-toolkit-for-eclipse-installation.md
[Créer une application Hello World Web pour Azure dans Éclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Exemple de projet Web dynamique]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png
