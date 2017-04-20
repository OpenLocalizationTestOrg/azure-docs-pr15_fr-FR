<properties 
    pageTitle="Créer une application Hello World Web pour Azure dans Eclipse | Microsoft Azure" 
    description="Ce didacticiel montre comment utiliser la boîte à outils Azure pour Eclipse pour créer une application Web Hello World pour Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Créer une application Hello World Web pour Azure dans Éclipse

Ce didacticiel montre comment créer et déployer une application Hello World simple à Azure comme une application Web à l’aide de la [Boîte à outils Azure pour Eclipse]. Voici un exemple JSP base pour simplifier, mais une procédure très similaire serait appropriée pour un servlet Java, autant que déploiement d’Azure est concernée.

Lorsque vous avez terminé ce didacticiel, votre application ressemblera à l’illustration suivante lorsque vous l’affichez dans un navigateur web :

![Aperçu de Hello World application][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un développeur Kit JDK (Java), v 1,8 ou version ultérieure.
* Eclipse IDE pour les développeurs EE Java, Luna ou version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure proviennent de <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* La boîte à outils Azure pour Eclipse. Pour plus d’informations, voir [la boîte à outils Azure pour Eclipse].

## <a name="to-create-a-hello-world-application"></a>Pour créer une Application du monde Hello

Tout d’abord, nous allons commencer avec la création d’un projet Java.

1. Démarrer Eclipse et dans le menu, cliquez sur **fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet Web dynamique**. (Si vous ne voyez pas **Project Web dynamique** répertoriées sous forme d’un projet disponibles après avoir cliqué sur **fichier** et **Nouveau**, puis procédez comme suit : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur **projet...**, développer **Web**, cliquez sur **Projet Web dynamique**et cliquez sur **suivant**.)

1. Dans le cadre de ce didacticiel, nommez le projet **MyWebApp**. Votre écran sera semblable à ce qui suit :

    ![Création d’un projet Web dynamique][02]

1. Cliquez sur **Terminer**.

1. Dans mode d’Explorateur de projets de Eclipse, développez **MyWebApp**. Avec le bouton droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.

1. Dans la boîte de dialogue **Nouveau fichier JSP** , nom de fichier **index.jsp**, conserver le dossier parent que **MyWebApp/contenu Web**, puis cliquez sur **suivant**.

1. Dans la boîte de dialogue **Sélectionner un modèle de JSP** à des fins de ce didacticiel, sélectionnez **Nouveau fichier JSP (html)**et puis cliquez sur **Terminer**.

1. Lorsque votre fichier index.jsp s’ouvre dans Eclipse, ajoutez dans le texte afin d’afficher dynamiquement **Bonjour !** dans l’objet existant `<body>` élément. Votre mise à jour `<body>` contenu doit ressembler à l’exemple suivant :

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Enregistrer index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Pour déployer votre Application à un conteneur de l’application Web Azure

Il existe plusieurs manières par lequel vous pouvez déployer une application web Java Azure. Ce didacticiel décrit l’une des plus simples : votre application sera déployée sur un conteneur Azure Web App - aucun type de projet spécial ni des outils supplémentaires ne sont nécessaires. JDK et les logiciels de conteneur web seront fournies pour vous par Azure, il est inutile pour télécharger votre propre ; Il vous suffit de votre application Web Java. Par conséquent, la publication de votre application prendra secondes, pas minutes.

1. Dans l’Explorateur de projets de Eclipse, menu contextuel **MyWebApp**.

1. Dans le menu contextuel, sélectionnez **Azure**, puis cliquez sur **Publier en tant que Azure Web App...**

    ![Publier comme Azure dans le navigateur][03]
   
    Par ailleurs, avec votre projet d’application web est sélectionnée dans l’Explorateur de projets, vous pouvez cliquez sur le bouton déroulant **Publier** dans la barre d’outils et sélectionnez **Publier en tant que Azure Web App** à partir de cet emplacement :
   
    ![Publier comme Azure dans le navigateur][14]
   
1. Si vous n’avez pas déjà connecté à Azure de Eclipse, vous devrez vous connecter à votre compte Azure :

    ![Boîte de dialogue Azure se connecter][04]
   
    Si vous avez plusieurs comptes Azure, certaines des invites pendant le processus de connexion peuvent apparaître plusieurs fois, même s’ils semblent être identiques. Dans ce cas, continuez à suivre les instructions de connexion.

1. Une fois que vous avez correctement connecté à votre compte Azure, la boîte de dialogue **Gérer les abonnements** affiche une liste des abonnements qui sont associés à vos informations d’identification. S’il existe plusieurs abonnements répertoriés et que vous voulez utiliser uniquement un sous-ensemble spécifique d'entre eux, vous pouvez éventuellement désélectionner l’option de celles que vous ne souhaitez pas utiliser. Lorsque vous avez sélectionné vos abonnements, cliquez sur **Fermer**.

    ![Gérer la boîte de dialogue abonnements][05]
   
1. Lorsque la boîte de dialogue **déployer au conteneur de l’application Web Azure** s’affiche, elle affiche tous les conteneurs de l’application Web que vous avez créée précédemment. Si vous n’avez pas créé tous les conteneurs, la liste sera vide.

    ![Déployer dans la boîte de dialogue Azure Web application conteneur][06]
   
1. Si vous n’avez pas créé un conteneur d’application Web Azure avant, ou si vous voulez publier votre application sur un nouveau conteneur, procédez comme suit. Dans le cas contraire, sélectionnez un conteneur d’application Web existant, puis passez à l’étape 7 ci-dessous.

    1. Cliquez sur **nouveau...**

        ![Déployer dans la boîte de dialogue Azure Web application conteneur][15]

    1. La boîte de dialogue **Nouveau Web application conteneur** s’affiche :

        ![Boîte de dialogue nouveau conteneur d’application Web][07a]

    1. Entrez un **nom DNS** pour le conteneur de votre application Web ; Cela constituera l’étiquette DNS feuille de l’URL hôte pour votre application web dans Azure. (Notez que le nom doit être disponibles et sont conformes aux exigences Azure Web App).

    1. Dans le menu déroulant **Conteneur Web** , sélectionnez le logiciel approprié pour votre application.

        Pour l’instant, vous pouvez choisir de 8 Tomcat, Tomcat 7 ou 9 jetée. Une distribution récente du logiciel sélectionné est fournie par Azure, et il est exécuté sur une distribution récente JDK 8 créée par Oracle et fournie par Azure.

    1. Dans le menu déroulant **abonnement** , sélectionnez l’abonnement que vous souhaitez utiliser pour ce déploiement.

    1. Dans le menu déroulant **Groupe de ressources** , sélectionnez le groupe de ressources dont vous souhaitez associer votre application Web. (Groupes de Ressources azure vous autorise à regrouper les ressources associées afin que, par exemple, ils peuvent être supprimés ensemble.)

        Vous pouvez sélectionner un groupe de ressources existant (si vous rencontrez) et passer à l’étape g ci-dessous, ou utilisez la procédure suivante pour créer un nouveau groupe de ressources qui suit :

        * Cliquez sur **nouveau...**

        * La boîte de dialogue **Nouveau groupe de ressources** s’affiche :

            ![Boîte de dialogue Nouveau groupe de ressources][08]

        * Dans la la zone de texte **nom** , attribuez un nom à votre nouveau groupe de ressources.

        * Dans la le menu déroulant **région** , sélectionnez les données Azure appropriées centrer emplacement pour votre groupe de ressources.

        * FACULTATIF : Par défaut, une distribution récente de Java 8 sera déployée par Azure automatiquement sur le conteneur de votre application web en tant que votre machine virtuelle Java. Toutefois, vous pouvez spécifier une autre version et distribution de la machine virtuelle Java si votre application Web exige. Pour spécifier le JDK pour votre application Web, cliquez sur l’onglet **JDK** et sélectionnez une des options suivantes :

            * **Déployer la valeur par défaut JDK offerte par Azure Web Apps service**: cette option déployez une distribution récente de Java 8.

            * **Déployer un 3e partie JDK disponible sur Azure**: cette option vous permet de choisir dans la liste des kits JDK qui sont fournis par Microsoft Azure.

            * **Déployer mon propre JDK à partir de cet emplacement de téléchargement**: cette option vous permet de spécifier votre propre distribution JDK, qui doit être empaquetée en tant que fichier ZIP et téléchargée dans un emplacement de téléchargement accessible au public ou un compte de stockage Azure pour lequel vous avez accès.

            ![Boîte de dialogue nouveau conteneur d’application Web][07b]

    * Cliquez sur **OK**.

    1. Le menu déroulant **Plan de Service d’application** répertorie les plans de services d’application qui sont associées dans le groupe de ressources que vous avez sélectionné. (Offres de Service d’application spécifier des informations telles que l’emplacement de votre application Web, le niveau de tarification et la taille d’instance cluster. Un Plan de Service application unique peut être utilisé pour plusieurs applications Web, c’est pourquoi, il est conservé séparément à partir d’un déploiement de l’application Web spécifique.)

        Vous pouvez sélectionner un existant application Plan de Service (si vous rencontrez) et passer à l’étape h ci-dessous, ou utilisez la procédure suivante pour créer un nouveau Plan de Service de l’application qui suit :

        * Cliquez sur **nouveau...**

        * La boîte de dialogue **Nouvelle application Service Plan** s’affiche :

            ![Boîte de dialogue Nouveau Plan de Service d’application][09]

        * Dans la la zone de texte **nom** , attribuez un nom à votre nouveau Plan de Service de l’application.

        * Dans la le menu déroulant **emplacement** , sélectionnez les données Azure appropriées centrer emplacement pour le plan.

        * Dans du menu déroulant **Couche tarifs** , sélectionnez les tarifs appropriée pour le plan. Aux fins de test, vous pouvez choisir **libre**.

        * Dans la le menu déroulant **Taille d’Instance** , sélectionnez taille de l’instance appropriée pour le plan. Aux fins de test, vous pouvez choisir **petite**.

    1. Une fois que vous avez terminé la procédure ci-dessus, la boîte de dialogue Nouveau Web application conteneur doit ressembler à l’illustration suivante :

        ![Boîte de dialogue nouveau conteneur d’application Web][10]

    1. Cliquez sur **OK** pour terminer la création de votre nouveau conteneur dans le navigateur.

        Patientez quelques secondes pour la liste des conteneurs Web App pour être actualisé et le conteneur de votre application web nouvellement créé doit maintenant être sélectionné dans la liste.

1. Vous êtes maintenant prêt à effectuer le déploiement initial de votre application Web vers Azure :

    ![Déployer dans la boîte de dialogue Azure Web application conteneur][11]

    Cliquez sur **OK** pour déployer votre application Java dans le conteneur sélectionné dans le navigateur.

    Par défaut, votre application sera déployée comme un sous-répertoire du serveur d’application. Si vous souhaitez qu’il déployée en tant que l’application racine, cochez la case **déployer à racine** avant de cliquer sur **OK**.

1. Ensuite, vous devez voir l’affichage du **Journal d’activité Azure** , qui indique l’état du déploiement de votre application Web.

    ![Journal d’activité Azure][12]

    Le processus de déploiement de votre application Web vers Azure doit prendre seulement quelques secondes. Lorsque votre prêt application, vous verrez un lien nommé **publié** dans la colonne **statut** . Lorsque vous cliquez sur le lien, il vous accédez alors à la page d’accueil de votre application Web déployée.

## <a name="updating-your-web-app"></a>Mise à jour de votre application Web

Mise à jour un existant en cours d’exécution Azure Web App est un processus simple et rapide, et que vous avez deux possibilités pour mettre à jour :

* Vous pouvez mettre à jour le déploiement d’une application Web Java existant.
* Vous pouvez publier une application Java supplémentaire dans le conteneur application Web.

Dans les deux cas, le processus est identique et ne prend que quelques secondes :

1. Dans l’Explorateur de projets Eclipse, cliquez sur l’application Java que vous voulez mettre à jour ou ajouter à un conteneur d’application Web existant.

1. Lorsque le menu contextuel s’affiche, sélectionnez **Azure** , puis sur **Publier en tant que Azure Web App...**

1. Dans la mesure où vous êtes déjà connecté précédemment, vous verrez une liste de vos conteneurs application Web existantes. Sélectionnez celui que vous souhaitez publier ou publier à nouveau votre application Java et cliquez sur **OK**.

Quelques secondes plus tard, l’affichage du **Journal d’activité Azure** s’affichent votre déploiement mis à jour comme étant **publiée** et vous ne pourrez pas vérifier votre application mise à jour dans un navigateur web.

## <a name="stopping-an-existing-web-app"></a>Arrêt d’une application Web existante

Pour arrêter un Azure Web App conteneur existant, (y compris toutes les applications Java déployées qu’il contient), vous pouvez utiliser le mode **Explorateur Azure** .

Si le mode **Explorateur Azure** n’est pas déjà ouvert, vous pouvez ouvrir en cliquant sur puis menu **fenêtre** en Eclipse, puis cliquez sur **Afficher la vue**, puis **autre...**, puis **Azure**, puis cliquez sur **Explorer Azure**. Si vous n’êtes pas déjà connecté, il vous invite à le faire.

Le mode **Explorateur Azure** qui s’affiche, utilisez, procédez comme suit pour arrêter votre application Web : 

1. Développez le nœud **Azure** .

1. Développez le nœud **Applications Web** . 

1. Clic droit sur l’application Web souhaitée.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Arrêter**.

    ![Arrêt d’une application Web existante][13]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les boîtes à outils Azure pour Java IDEs, consultez les liens suivants :

- [Kit de ressources Azure pour Éclipse]
  - [Installez le Kit de ressources de Azure pour Éclipse]
  - *Créer une application Hello World Web pour Azure dans Éclipse (Cet Article)*
  - [Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]
- [Kit de ressources Azure pour IntelliJ]
  - [Installez le Kit de ressources de Azure pour IntelliJ]
  - [Créer une application Hello World Web pour Azure dans IntelliJ]
  - [Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

Pour plus d’informations sur la création d’Azure Web Apps, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de ressources Azure pour Éclipse]: ../azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application Hello World Web pour Azure dans IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installez le Kit de ressources de Azure pour Éclipse]: ../azure-toolkit-for-eclipse-installation.md
[Installez le Kit de ressources de Azure pour IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png
