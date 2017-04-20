<properties
    pageTitle="Créer un Service en nuage Hello World pour Azure dans Éclipse"
    description="Apprenez à créer une application Hello World simple à l’aide de la boîte à outils Azure pour Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Créer un Service en nuage Hello World pour Azure dans Éclipse #

Les étapes suivantes vous montrent comment créer et déployer une application JSP base sur Azure à l’aide de la boîte à outils Azure pour Eclipse. Voici un exemple JSP pour simplifier, mais une procédure très similaire serait appropriée pour un servlet Java, autant que déploiement d’Azure est concernée.

L’application est semblable à ce qui suit :

![][ic600360]

## <a name="prerequisites"></a>Conditions préalables ##

* Un développeur Kit JDK (Java), v 1.7 ou version ultérieure.
* Eclipse IDE pour les développeurs EE Java, Indigo ou version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat, GlassFish, serveur d’applications JBoss, jetée ou IBM® WebSphere® Application Server liberté Core.
* Un abonnement Azure peut être posé à partir de <http://azure.microsoft.com/pricing/purchase-options/>.
* La boîte à outils Azure pour Eclipse. Pour plus d’informations, voir [la boîte à outils Azure pour Eclipse][].

## <a name="to-create-a-hello-world-application"></a>Pour créer une application Hello World ##

Tout d’abord, nous allons commencer avec la création d’un projet Java.

*  Démarrer Eclipse et dans le menu, cliquez sur **fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet Web dynamique**. (Si vous ne voyez pas **Project Web dynamique** répertoriées sous forme d’un projet disponibles après avoir cliqué sur **fichier**, **Nouveau**, puis procédez comme suit : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur **projet...**, développer **Web**, cliquez sur **Projet Web dynamique**et cliquez sur **suivant**.)
*  Dans le cadre de ce didacticiel, nommez le projet **MyHelloWorld**. (Vous assurer que vous utilisez ce nom, les étapes suivantes dans ce didacticiel vous attendre à votre fichier WAR d’être nommé MyHelloWorld). Votre écran sera semblable à ce qui suit : ![][ic589576]
* Cliquez sur **Terminer**.
* Dans mode d’Explorateur de projets de Eclipse, développez **MyHelloWorld**. Avec le bouton droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.
* Dans la boîte de dialogue **Nouveau fichier JSP** , nommez fichier **index.jsp**. Conserver le dossier parent que **MyHelloWorld/contenu Web**, comme illustré ci-dessous :  ![][ic659262]
* Dans la boîte de dialogue **Sélectionner un modèle de JSP** , à des fins de ce didacticiel, sélectionnez **Nouveau fichier JSP (html)** , puis cliquez sur **Terminer**.
* Lorsque le fichier index.jsp s’ouvre dans Eclipse, ajouter dans le texte afin d’afficher dynamiquement **Bonjour !** dans l’objet existant `<body>` élément. Votre mise à jour `<body>` contenu doit apparaître comme suit :
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Enregistrer index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Pour déployer votre application sur Azure, la méthode rapide et simple ##

Dès que vous avez une application de web Java prête à tester, vous pouvez utiliser le raccourci suivant à faire un essai directement sur le nuage Azure.

1. Dans l’Explorateur de projets de Eclipse, cliquez sur **MyHelloWorld**.
1. Dans la barre d’outils Eclipse, cliquez sur le bouton déroulant **Publier** , puis sur **Publier en tant que Service en nuage Azure**
    ![][publishDropdownButton]
1. Si vous publiez cette application Azure pour la première fois et que vous n’avez pas créé un projet de déploiement d’Azure pour cette application avant, un projet de déploiement d’Azure est automatiquement créée pour vous. Vous devriez voir l’invite suivante s’affiche, qui indique également le package JDK et serveur d’application qui sera déployée automatiquement pour exécuter votre application.
    ![][ic789598]

    Cette approche raccourci permet un moyen rapide et facile à tester votre application dans Azure sans avoir à configurer un serveur spécifique ou JDK est la différence entre les valeurs par défaut. Si vous êtes satisfait des valeurs par défaut, vous pouvez cliquer sur **OK** pour continuer avec les étapes suivantes.
    Toutefois, si vous voulez modifier le JDK ou serveur d’applications à utiliser pour votre application, vous pouvez le faire ultérieurement en modifiant le projet de déploiement d’Azure qui a été créé automatiquement, ou vous pouvez cliquer sur **Annuler** maintenant et lisez la **section de projets de déploiement sur Azure** de ce didacticiel.
1. Dans la boîte de dialogue **publier sur Azure** :
    1. S’il n’y a aucun abonnement pour sélectionner dans la liste des **abonnements** encore, procédez comme suit pour importer des informations de votre abonnement :
        1. Cliquez sur **Importer depuis un fichier de paramètres de publication**.
        1. Dans la boîte de dialogue **Importer des informations abonnement** , cliquez sur **Télécharger le fichier paramètres de publication**. Si vous n’êtes pas encore connecté à votre compte Azure, vous invitera à se connecter. Puis vous serez invité à enregistrer une Azure publier le fichier de configuration. L’enregistrer sur votre ordinateur local.
        1. Toujours dans la boîte de dialogue **Importer des informations abonnement** , cliquez sur le bouton **Parcourir** , sélectionnez le fichier de paramètres de publication que vous avez enregistré localement dans l’étape précédente, puis cliquez sur **Ouvrir**. Votre écran doit ressembler à ce qui suit : ![][ic644267]
        1. Cliquez sur **OK**.
    1. Pour l' **abonnement**, sélectionnez l’abonnement que vous souhaitez utiliser pour votre déploiement.
    1. Pour le **compte de stockage**, sélectionnez le compte de stockage que vous souhaitez utiliser, ou cliquez sur **Nouveau** pour créer un nouveau compte de stockage.
    1. **Nom du Service**, sélectionnez le service en nuage que vous souhaitez utiliser, ou cliquez sur **Nouveau** pour créer un nouveau service cloud.
    1. Pour **Système d’exploitation cible**, sélectionnez la version du système d’exploitation que vous souhaitez utiliser pour votre déploiement.
    1. Pour un **environnement cible**, à des fins de ce didacticiel, sélectionnez **mise en attente**. (Lorsque vous êtes prêt à déployer sur votre site de production, vous devez y remédier en **Production**.)
    1. Facultatif : Assurez-vous que l’option **Remplacer déploiement précédent** est cochée si vous voulez que votre nouveau déploiement d’écraser automatiquement le déploiement précédent. Lorsque vous activez cette option, vous n'allez pas les problèmes expérience « 409 conflit » lors de la publication au même emplacement.
        Notez que la boîte de dialogue **publier sur Azure** contienne une section pour **l’Accès à distance**. Par défaut, accès à distance n’est pas activé et nous pas activez pour cet exemple. Pour activer l’accès à distance, vous devez entrer un nom d’utilisateur et mot de passe à utiliser lors de la connexion à distance. Pour plus d’informations sur l’accès à distance, voir [L’activation de l’accès à distance pour les déploiements Azure dans Eclipse][].
        Votre boîte de dialogue **publier sur Azure** sera semblable à ce qui suit : ![][ic719488]
1. Cliquez sur **Publier** pour publier dans l’environnement intermédiaire.
    Lorsque vous êtes invité à effectuer une génération complète, cliquez sur **Oui**. Cela peut prendre plusieurs minutes pour la première génération.
    Un **Journal d’activité Azure** s’affichera dans votre section Affichages Eclipse à onglets.
    ![][ic719489]
   Vous pouvez utiliser ce journal, ainsi que l’affichage de la **Console** , pour afficher la progression de votre déploiement. Alternative consiste à se connecter au [Portail de gestion Azure][], utilisez la section **Services Cloud** pour contrôler le statut.
1. Quand votre déploiement est déployé avec succès, le **Journal d’activité Azure** affiche un statut **publié**. Cliquez sur **Published**, comme illustré dans l’image suivante, votre navigateur s’ouvre une instance de votre déploiement.
    ![][ic719490]

Comme il s’agissait d’un déploiement dans un environnement intermédiaire, le nom DNS sera de la forme http://&lt;*guid*&gt;. cloudapp.net, programme d’et l’URL contient le nom DNS ainsi un suffixe pour votre application. Par exemple, http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (La partie **MyHelloWorld** respecte la casse). Vous pouvez également voir le nom DNS si vous cliquez sur le nom du déploiement dans le portail de gestion de plateforme Azure (dans la partie Cloud Services du portail de gestion).

Bien que cette étape par étape a été pour un déploiement dans l’environnement intermédiaire, un déploiement en production suit la même procédure, à l’exception de la boîte de dialogue **publier sur Azure** , sélectionnez **Production** au lieu de **mise en attente** pour l' **environnement cible**. Un déploiement en production se traduit par une URL basée sur le nom DNS de votre choix, au lieu d’un GUID que celui utilisé pour la mise en attente.

>[AZURE.WARNING] À ce stade, vous avez déployé votre application Azure dans le cloud. Toutefois, avant de poursuivre, savoir qu’une application déployée, même si elle n’est pas en cours d’exécution, continueront à accumuler facturables pour votre abonnement. Par conséquent, il est très important que vous supprimez des déploiements indésirables de votre abonnement Azure.

## <a name="about-azure-deployment-projects"></a>À propos des projets de déploiement d’Azure ##

Pour déployer un ou plusieurs des applications Java vers Azure, un projet de déploiement d’Azure est nécessaire. Il joue le rôle de « package » qui doivent être incorporés dans afin d’être publié sur Azure vos applications.

Outre les informations sur vos applications, un projet de déploiement d’Azure contienne également des informations sur les autres composants clés de votre déploiement, surtout : le conteneur de serveur d’application pour exécuter votre application web dans et l’exécution de Java à exécuter sur. Azure prend en charge un large éventail de Java exécutions et vous pouvez choisir parmi les serveurs d’applications Java.

Bien que l’exemple utilisé ici est simplifiée à des fins pédagogiques, un projet de déploiement d’Azure peut contenir d’autres informations de configuration importantes qui vous permet de créer des services cloud presque arbitraire complexes, format SVG, hautement disponible, à plusieurs niveaux avec vos applications. Vous pouvez activer **affinité session (« sessions permanentes »)**, **rapidement la mise en cache**, **débogage distant**, **SSL décharger**, **pare-feu/port routage**, **accès à distance**et un nombre d’autres puissantes fonctionnalités.

Si vous avez terminé la section précédente de ce didacticiel ( » pour déployer votre application sur Azure, la méthode rapide et simple »), vous verrez maintenant un nouveau projet de déploiement d’Azure dans l’Explorateur de projets générées automatiquement pour vous et nommé «**MyHelloWorld_onAzure**».

Vous pouvez également avoir démarré ce didacticiel en créant tout d’abord un projet de déploiement d’Azure vide et en ajoutant vos applications vers celui-ci. Il est plus long processus, mais vous donne davantage de contrôle sur la configuration initiale à partir du début.

Pour créer un nouveau projet de déploiement d’Azure à partir de zéro, cliquez sur le bouton **Nouveau projet de déploiement d’Azure** ![][ic710876].

Quelle que soit que vous travaillez dans un projet de déploiement d’Azure déjà existant, ou créer un à partir de zéro, vous ne pouvez modifier ses paramètres de configuration et les composants, tels que le JDK ou le serveur d’applications, tout aussi facilement à tout moment.

Pour modifier le JDK, ou le serveur d’applications ou la liste de l’application dans un projet de déploiement d’Azure existant :

1. Développez le nœud de projet (par exemple, **MyHelloWorld_onAzure**) dans l’Explorateur de projets
2. Clic droit **WorkerRole1**
3. Développer le sous-menu **Azure** dans le menu contextuel
4. Cliquez sur **Configuration du serveur**

Quel que soit ait ces étapes de configuration serveur en modifiant un projet de déploiement d’Azure existant comme illustré ci-dessus, ou créer un nouveau à partir de zéro, vous verrez le même type de boîtes de dialogue et vous pouvez configurer votre JDK, les composants de serveur et d’application. Pour plus d’informations comment modifier les paramètres dans les boîtes de dialogue, par exemple pour modifier le JDK, le serveur d’applications et ajouter ou supprimer des applications dans un déploiement, voir l’article [Propriétés configuration du serveur][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Windows uniquement : pour déployer votre application sur l’émulateur cluster ##

>[AZURE.NOTE] L’émulateur Azure est uniquement disponible sur Windows. Ignorer cette section si vous utilisez un système d’exploitation différent de Windows.

Si vous avez créé un nouveau projet de déploiement d’Azure suivant la procédure décrite plus haut, c'est-à-dire implicitement, en le publiant votre application Azure, le JDK et application serveurs ont été configurés pour le nuage, mais pas pour émulation locale. Pour préparer votre projet test dans l’émulateur local, procédez comme suit :

1. Dans l’Explorateur de projets de Eclipse, cliquez sur **MyHelloWorld_onAzure**.
1. Un clic droit sur **WorkerRole1**.
1. Développez le sous-menu **Azure** dans le menu contextuel.
1. Cliquez sur **Configuration du serveur**.
1. Sous l’onglet **JDK** , vérifiez si la boîte à outils a préconfigurée par défaut JDK local pour vous. Dans le cas contraire, ou si vous voulez modifier les paramètres par défaut supposés, vérifiez que la case à cocher **utiliser le JDK à partir de ce chemin d’accès de fichier pour le test localement** est activée et l’emplacement d’installation JDK que vous voulez utiliser est spécifié. Si vous voulez le modifier, cliquez sur le bouton **Parcourir** et l’utilisation du contrôle de navigation, sélectionnez l’emplacement du répertoire du JDK à utiliser.
1. Cliquez sur l’onglet **serveur** .
1. Dans la zone de texte **chemin d’accès du serveur Local** dans la partie inférieure de la boîte de dialogue, entrez le chemin d’accès d’un serveur installé localement correspondant au type et le numéro de version principale du serveur sélectionné en haut de la boîte de dialogue, sous la case à cocher **déployer un serveur de ce type** . Si vous voulez utiliser un autre type ou une version principale du serveur d’applications, modifiez d’abord la sélection sous cette case à cocher.
1. Cliquez sur **OK**.
1. Dans la barre d’outils Eclipse, cliquez sur le bouton **exécuter dans Azure émulateur** , ![][ic710879]. Si le bouton **exécuter dans Azure émulateur** n’est pas activé, vérifiez que **MyHelloWorld_onAzure** est sélectionné dans l’Explorateur de projets de Eclipse et vérifiez qu’Explorateur de projets de Eclipse a le focus en tant que la fenêtre active. Cette opération tout d’abord démarrer une version complète de votre projet, puis lancer votre application web Java dans l’émulateur cluster. (Notez que selon les caractéristiques de performances de votre ordinateur, la première génération peut prendre entre quelques secondes à quelques minutes, mais générations ultérieures obtenez plus rapidement.) Une fois la première étape de génération terminée, vous demandera par Windows contrôle (compte utilisateur) pour permettre à cette commande apporter des modifications à votre ordinateur. Cliquez sur **Oui**.

>[AZURE.IMPORTANT] Si vous ne voyez pas l’invite de compte d’utilisateur, vérifiez la barre des tâches Windows pour l’icône de compte d’utilisateur et cliquez sur tout d’abord. Parfois le compte d’utilisateur invite ne s’affiche pas dans une fenêtre de niveau supérieur, mais n’est visible uniquement sous forme d’une icône de barre des tâches.

1. Examinez les résultats de l’émulateur cluster l’interface utilisateur pour déterminer il s'existe des problèmes avec votre projet. Selon le contenu de votre déploiement, il peut prendre quelques minutes pour que votre application pour qu’il démarre entièrement au sein de l’émulateur de calcul.
1. Démarrez votre navigateur et utiliser l’URL `http://localhost:8080/MyHelloWorld` comme adresse (la `MyHelloWorld` partie de l’URL respecte la casse). Vous devriez voir votre application MyHelloWorld (le résultat de index.jsp), similaire à l’image suivante : ![][ic589579]

Lorsque vous êtes prêt à arrêter votre application de s’exécuter dans l’émulateur de calcul, dans la barre d’outils Eclipse, cliquez sur le bouton **Réinitialiser Azure émulateur** , ![][ic710880].

## <a name="to-delete-your-deployment"></a>Pour supprimer votre déploiement ##

Pour supprimer votre déploiement au sein de la boîte à outils Azure pour Eclipse, vérifiez que **MyHelloWorld_onAzure** est sélectionné dans l’Explorateur de projets de Eclipse, assurez-vous que l’Explorateur de projets Eclipse contient la fenêtre active concentrer, puis cliquez sur le bouton **Annuler la publication** , ![][ic710883], dans la barre d’outils Eclipse. (Vous pourriez faire la même opération en cliquant **MyHelloWorld_onAzure** dans l’Explorateur de projets de Eclipse, en cliquant sur **Azure** et puis en cliquant sur **Annuler le déploiement d’Azure Cloud**). Cela affiche la boîte de dialogue **Annuler la publication d’un projet Azure** .

![][ic719491]

Sélectionnez le service d’abonnement et nuage qui contient votre déploiement, sélectionnez le déploiement que vous voulez supprimer, puis cliquez sur **Annuler la publication**.

(Alternative à l’utilisation de la boîte à outils pour supprimer le déploiement consiste à utiliser la section **Cloud Services** du portail de gestion Azure : accédez à votre déploiement, sélectionnez-le, puis cliquez sur le bouton **Supprimer** . Cela arrêter et avant de supprimer le déploiement. Si vous souhaitez uniquement interrompre le déploiement et supprimez pas, cliquez sur le bouton **Arrêter** au lieu du bouton **Supprimer** , mais comme indiqué ci-dessus, si vous ne supprimez pas le déploiement, frais facturables continueront à accumuler pour votre déploiement, même s’il est arrêté).

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

[Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse][]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Activer l’accès à distance pour les déploiements Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés de configuration de serveur]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
