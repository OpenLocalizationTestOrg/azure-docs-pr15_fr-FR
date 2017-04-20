<properties
    pageTitle="Installez le Kit de ressources de Azure pour Eclipse | Microsoft Azure"
    description="Découvrez comment installer le Kit de ressources Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>Installez le Kit de ressources de Azure pour Éclipse

La boîte à outils Azure pour Eclipse fournit des modèles et les fonctionnalités qui vous permettent de facilement créer, développer, tester et déployer des applications Azure à l’aide de l’environnement de développement Eclipse. La boîte à outils Azure pour Eclipse est un projet Open Source dont le code source est disponible sous licence du site de projet sur GitHub à l’adresse suivante :

<https://github.com/Microsoft/Azure-Tools-for-Java>

Les étapes suivantes vous montrent comment installer le Kit de ressources Azure pour Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Pour installer le Kit de ressources Azure pour Éclipse

1. Démarrez Eclipse.

1. Lorsque Eclipse s’ouvre, cliquez sur le menu **aide** , puis cliquez sur **Installer de nouveaux logiciels**, comme le montre l’illustration suivante.

    ![Installez le Kit de ressources de Azure pour Éclipse][01]

1. Dans la boîte de dialogue **Logiciels disponibles** , dans la zone de texte **compatibles avec** , tapez **http://dl.microsoft.com/eclipse** suivie de la touche **entrée** .

1. Dans le volet **nom** , cochez **Azure le Kit de ressources pour Eclipse**et décochez **Contact tous les sites au cours de mettre à jour installer pour rechercher des logiciels requis**. Votre écran doit ressembler à ce qui suit :

    ![Installez le Kit de ressources de Azure pour Éclipse][02]

1. Si vous développez le **Jeu d’outils Azure pour Eclipse**, vous verrez les éléments suivants :

    * **Plug-in d’application perspectives pour Java**: ce composant vous permet d’utiliser les services de journalisation et l’analyse de télémétrie pour vos applications et les instances de serveur d’Azure.
    * **Filtre de Services Azure accès contrôle**: ce composant prend en charge l’authentification des utilisateurs de l’application avec ACS Azure, l’activation de scénarios d’authentification unique et externalisation logique d’identité de l’application.
    * **Plug-in courantes Azure**: ce composant fournit les fonctionnalités communes nécessaires par d’autres composants du Kit de ressources.
    * **Explorer Azure pour Eclipse**: ce composant fournit les fonctionnalités communes nécessaires par d’autres composants du Kit de ressources.
    * **Plug-in Azure pour Eclipse avec Java**: ce composant prend en charge pour le développement de projets qui vous permettent de créer, tester et déployer des applications Java pour le cloud Microsoft Azure dans Eclipse et via la ligne de commande.
    * **Plug-in d’applications Web Azure avec Java**: ce composant prend en charge pour le déploiement des applications web Java conteneurs de Microsoft Azure Web App.
    * **Microsoft JDBC pilote 4.2 pour SQL Server**: ce composant fournit des API JDBC pour SQL Server et base de données SQL Microsoft Azure pour Java Platform Enterprise Edition 8.
    * **Package pour les bibliothèques de Client Apache Qpid pour JMS**: ce composant fournit le composant client JMS du projet Apache Qpid pour permettre à votre application utiliser la messagerie AMQP dans Microsoft Azure.
    * **Package Microsoft Azure des bibliothèques pour Java**: ce composant fournit des API pour accéder aux services Microsoft Azure, tels que le stockage, bus des services, service d’exécution, etc..

1. Cliquez sur **suivant**. (Si vous rencontrez des retards inhabituels lors de l’installation de la boîte à outils, assurez-vous que **tous les sites au cours de mettre à jour de Contact installer pour rechercher des logiciels requis** est désactivée.)

1. Dans la boîte de dialogue **Détails de l’installation** , cliquez sur **suivant**.

    ![Passez en revue les détails de l’Installation][03]

1. Dans la boîte de dialogue **Consulter les licences** , passez en revue les termes du contrat de licence. Si vous acceptez les termes du contrat de licence, cliquez sur **J’accepte les termes du contrat de licence** , puis sur **Terminer**. (Les étapes restantes part du principe que vous acceptez les termes du contrat de licence. Si vous n’acceptez pas les termes du contrat de licence, quittez le processus d’installation.)

    ![Licences de révision][04]

    Éclipse télécharger et installer les packages requises.

    ![Progression de l’installation][05]

1. Si vous êtes invité à redémarrer Eclipse pour terminer l’installation, cliquez sur **Oui**.

    ![Redémarrez l’invite de commandes][06]

## <a name="see-also"></a>Voir aussi

Pour plus d’informations sur les boîtes à outils Azure pour Java IDEs, consultez les liens suivants :

- [Kit de ressources Azure pour Éclipse]
  - *Installez le Kit de ressources de Azure pour Éclipse (Cet Article)*
  - [Créer une application Hello World Web pour Azure dans Éclipse]
  - [Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]
- [Kit de ressources Azure pour IntelliJ]
  - [Installez le Kit de ressources de Azure pour IntelliJ]
  - [Créer une application Hello World Web pour Azure dans IntelliJ]
  - [Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

<!-- URL List -->

[Kit de ressources Azure pour Éclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application Hello World Web pour Azure dans Éclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application Hello World Web pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installez le Kit de ressources de Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

