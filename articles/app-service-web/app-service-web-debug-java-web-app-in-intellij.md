<properties 
    pageTitle="Déboguer une application Web Java sur Azure dans IntelliJ | Microsoft Azure" 
    description="Ce didacticiel montre comment utiliser la boîte à outils Azure pour IntelliJ pour déboguer une application Web Java s’exécutant sur Azure." 
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

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Déboguer une application Web Java sur Azure dans IntelliJ

Ce didacticiel montre comment déboguer une application Web Java s’exécutant sur Azure à l’aide de la [Boîte à outils Azure pour IntelliJ]. Pour simplifier la démonstration, vous allez utiliser un exemple simple Java Server Page (JSP) pour ce didacticiel, mais la procédure est similaire pour un servlet Java lorsque vous effectuez le débogage Azure.

Lorsque vous avez terminé ce didacticiel, votre application ressemblera à l’illustration suivante lorsque vous effectuez le débogage dans IntelliJ :

![][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un développeur Kit JDK (Java), v 1,8 ou version ultérieure.
* Édition intégrale idée de IntelliJ. Cela peut être téléchargé à partir de <https://www.jetbrains.com/idea/download/index.html>.
* Distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure proviennent de <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* La boîte à outils Azure pour IntelliJ. Pour plus d’informations, voir [la boîte à outils Azure pour IntelliJ].
* Un projet Web dynamiques créés et déployés sur Azure Application Service ; par exemple, voir [créer une application Web Hello World pour Azure dans IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Déboguer une application Web Java sur Azure

Pour effectuer ces étapes dans cette section, vous pouvez utiliser un projet Web dynamique existant auquel vous avez déjà déployé comme une application Web Java sur Azure, vous téléchargez un [Exemple de projet Web dynamique] et suivez les étapes décrites dans [créer une application Web Hello World pour Azure dans IntelliJ] pour déployer sur Azure. 

1. Ouvrez le projet Java Web App dont vous avez déployé sur Azure dans IntelliJ.

1. Cliquez sur le menu **exécuter** , puis cliquez sur **Modifier les Configurations...**.

    ![][02]

1. Lorsque la boîte de dialogue **Exécuter/Déboguer Configurations** s’ouvre : 

    1. Sélectionnez **Azure dans le navigateur**.
    1. Cliquez sur **+** pour ajouter une nouvelle configuration.
    1. Attribuez un **nom** pour la configuration.
    1. Accepter les valeurs par défaut restantes qui vous sont proposés par le Kit de ressources de Azure, puis cliquez sur **OK**.

        ![][03]

1. Sélectionnez la configuration de débogage Azure dans le navigateur que vous venez de créer dans le coin supérieur droit du menu, cliquez sur **Déboguer**

    ![][04]

1. Lorsque vous êtes invité à **Activer le débogage distant dans l’application Web distante maintenant ?**, cliquez sur **OK**.

1. Lorsque vous êtes invité **votre application web est maintenant prête pour le débogage distant**, cliquez sur **OK**.

    ![][05]

1. Sélectionnez la configuration de débogage Azure dans le navigateur que vous venez de créer dans le coin supérieur droit du menu, puis cliquez sur **Déboguer**.

1. Invite de commande Windows ou shell Unix vous ouvrez et préparer la connexion nécessaires pour le débogage ; Vous devez patienter jusqu'à ce que la connexion à votre application Web Java remote réussit avant de poursuivre. Si vous utilisez Windows, il ressemblera à l’illustration suivante :

    ![][06]

1. Insérer un point d’arrêt dans votre page JSP, puis ouvrez l’URL de votre application Web Java dans un navigateur :

    1. Ouvrez **L’Explorateur Azure** dans IntelliJ.
    1. Accéder aux **Applications Web** et l’application Web Java que vous souhaitez déboguer.
    1. Cliquez avec le bouton droit sur l’application Web, puis cliquez sur **Ouvrir dans le navigateur**.
    1. IntelliJ êtes prêt à entrer en mode débogage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

Pour plus d’informations sur la création d’Azure Web Apps, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ressources Azure pour IntelliJ]: ../azure-toolkit-for-intellij.md
[Installez le Kit de ressources de Azure pour IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Créer une application Hello World Web pour Azure dans IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Exemple de projet Web dynamique]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
