<properties
    pageTitle="Remise continue avec Visual Studio Team Services dans Azure | Microsoft Azure"
    description="Découvrez comment configurer vos projets d’équipe Visual Studio Team Services pour créer et déployer la fonctionnalité Web App dans Azure Application Service ou sur le cloud services automatiquement."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Remise continue à Azure à l’aide de Visual Studio Team Services

Vous pouvez configurer vos projets d’équipe Visual Studio Team Services pour créer et déployer des applications web Azure ou services en nuage automatiquement.  (Pour plus d’informations sur la façon de configurer une build en continu et déployer le système à l’aide d’un *local* Team Foundation Server, voir [Remise continue pour les Services de Cloud dans Azure](cloud-services-dotnet-continuous-delivery.md)).

Ce didacticiel suppose que vous avez Visual Studio 2013 et le Kit de développement Azure installé. Si vous n’avez pas Visual Studio 2013, téléchargez-le en choisissant le lien de **mise en route gratuitement** en [www.visualstudio.com](http://www.visualstudio.com). Installez le Kit de développement Azure [ici](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Vous avez besoin d’un compte de Visual Studio Team Services pour effectuer ce didacticiel : vous pouvez [Ouvrir un compte Visual Studio Team Services gratuitement](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Pour configurer un service cloud pour créer et déployer sur Azure à l’aide de Visual Studio Team Services automatiquement, procédez comme suit.

## <a name="1-create-a-team-project"></a>1 : créer un projet d’équipe

Suivez les instructions [ici](http://go.microsoft.com/fwlink/?LinkId=512980) pour créer votre projet d’équipe et liez-le à Visual Studio. Cette procédure suppose que vous utilisez Team Foundation Version contrôle (TFVC) en tant que votre solution de contrôle de source. Si vous souhaitez utiliser Git contrôle de version, voir [la version Git de cette procédure](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2 : archivage d’un projet au contrôle de source

1. Dans Visual Studio, ouvrez la solution que vous voulez déployer ou créez-en une.
Vous pouvez déployer une application web ou un service cloud (Application Azure) en suivant les étapes décrites dans cette procédure pas à pas.
Si vous voulez créer une nouvelle solution, créez un nouveau projet Azure Cloud Service, ou un nouveau projet ASP.NET MVC. Vérifiez que le projet cible .NET Framework 4 ou 4.5 et si vous créez un projet de service cloud, ajoutez un rôle web ASP.NET MVC et un rôle de collaborateur, puis sélectionnez application Internet pour le rôle web. Lorsque vous y êtes invité, choisissez **Application Internet**.
Si vous voulez créer une application web, sélectionnez le modèle de projet Application Web ASP.NET, puis MVC. Voir [créer une application web ASP.NET dans le Service d’application Azure](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Visual Studio Team Services ne prennent en charge que les déploiements CI de Visual Studio Web Applications pour le moment. Projets de Site Web sortent du cadre.

1. Ouvrez le menu contextuel de la solution, puis sélectionnez **Ajouter la Solution au contrôle de code Source**.

    ![][5]

1. Accepter ou modifier les paramètres par défaut et cliquez sur le bouton **OK** . Une fois le processus terminé, les icônes de contrôle de code source apparaissent dans **L’Explorateur**.

    ![][6]

1. Ouvrez le menu contextuel de la solution, puis sélectionnez **Archiver**.

    ![][7]

1. Dans la zone de **Modifications en attente** de **L’Explorateur d’équipes**, tapez un commentaire pour l’archivage et cliquez sur le bouton **Archiver** .

    ![][8]

    Notez les options pour inclure ou exclure des modifications spécifiques lorsque vous archivez. Si les modifications souhaitées sont exclues, cliquez sur le lien **Inclure tous les** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3 : connecter le projet à Azure

1. À présent que vous disposez d’un projet d’équipe VS Team Services avec du code source, vous êtes prêt à vous connecter votre projet d’équipe à Azure.  Dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885), sélectionnez votre application web ou service cloud, ou créez-en une en choisissant le **+** icône située en bas à gauche et en choisissant **Service Cloud** ou **Web App** , puis **Création rapide**. Cliquez sur le lien **configurer la publication avec Visual Studio Team Services** .

    ![][10]

1. Dans l’Assistant, tapez le nom de votre compte de Visual Studio Team Services dans la zone de texte, puis cliquez sur le lien **Autoriser maintenant** . Vous pourriez être invité à se connecter.

    ![][11]

1. Dans le menu contextuel de **Demande de connexion** , cliquez sur le bouton **Accepter** pour autoriser Azure pour configurer votre projet d’équipe dans VS Team Services.

    ![][12]

1. Lorsque l’autorisation a réussi, vous voyez une liste déroulante contenant une liste de vos projets d’équipe Visual Studio Team Services. Sélectionnez le nom du projet d’équipe que vous avez créé lors des étapes précédentes, puis le bouton l’Assistant coche.

    ![][13]

1. Une fois que votre projet est lié, vous verrez des instructions pour l’archivage de modifications apportées à votre projet d’équipe Visual Studio Team Services.  Sur votre prochaine archivage, Visual Studio Team Services sera créez et déployez votre projet à Azure.  Essayez ceci en cliquant sur le lien **Archiver de Visual Studio** , puis le lien de la **Barre de lancement de Visual Studio** (ou le bouton **Visual Studio** équivalent en bas de l’écran portail).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4 : déclencher une reconstruction et redéployez votre projet

1. Dans Visual Studio **Team Explorer**, cliquez sur le lien **Source Control Explorer** .

    ![][15]

1. Accédez à votre fichier de solution et ouvrez-le.

    ![][16]

1. Dans l' **Explorateur de solutions**, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier `_Layout.cshtml` sous les vues\\dossier partagé dans un rôle web MVC.

    ![][17]

1. Modifier le logo du site et cliquez sur **Ctrl + S** pour enregistrer le fichier.

    ![][18]

1. Dans **L’Explorateur d’équipes**, cliquez sur le lien **Modifications en attente** .

    ![][19]

1. Entrez un commentaire, puis sur le bouton **Archiver** .

    ![][20]

1. Cliquez sur le bouton **accueil** pour revenir à la page d’accueil de **L’Explorateur d’équipes** .

    ![][21]

1. Cliquez sur le lien **crée** pour afficher les versions en cours.

    ![][22]

    **L’Explorateur d’équipes** montre qu’une build a été déclenchée pour votre archivage.

    ![][23]

1. Double-cliquez sur le nom de la version en cours pour afficher un journal détaillé que la génération progresse.

    ![][24]

1. Tandis que la génération est en cours, consultez la définition de build qui a été créée lorsque vous avez lié TFS à Azure à l’aide de l’Assistant.  Ouvrez le menu contextuel pour la définition de build, puis sélectionnez **Modifier la définition de Build**.

    ![][25]

    Dans l’onglet **déclencheur** , vous verrez que la définition de build est configurée pour générer sur chaque archivage par défaut.

    ![][26]

    Dans l’onglet **processus** , vous pouvez voir que l’environnement de déploiement est défini sur le nom de votre application web ou service cloud. Si vous travaillez avec des applications web, les propriétés qui qu'apparaissent sera différentes de celui illustré ici.

    ![][27]

1. Spécifier des valeurs pour les propriétés si vous souhaitez que les valeurs différentes de celles les valeurs par défaut. Les propriétés pour la publication Azure sont dans la section **déploiement** .

    Le tableau suivant montre les propriétés disponibles dans la section **déploiement** :

  	|Propriété|Valeur par défaut|
  	|---|---|
  	|Autoriser les certificats non approuvés|Si faux, les certificats SSL doivent être signés par une autorité racine.|
  	|Autoriser la mise à niveau|Permet le déploiement mettre à jour un déploiement existant au lieu de créer un nouvel identifiant. Conserve l’adresse IP.|
  	|Ne pas supprimer|Si vrai, ne pas remplacer un déploiement non lié existant (mise à niveau est autorisé).|
  	|Chemin d’accès aux paramètres de déploiement|Le chemin d’accès à votre fichier .pubxml pour une application web, par rapport au dossier racine de la mis en pension. Ignoré pour les services cloud.|
  	|Environnement de déploiement de SharePoint|La même que le nom du service.|
  	|Environnement de déploiement d’Azure|Le nom du service web app ou sur le cloud.|

1. Si vous utilisez plusieurs configurations de service (fichiers .cscfg), vous pouvez spécifier la configuration du service souhaité dans le paramètre **Générer, options avancées, MSBuild arguments** . Par exemple, pour utiliser ServiceConfiguration.Test.cscfg, définissez arguments MSBuild ligne option `/p:TargetProfile=Test`.

    ![][38]

    À ce stade, votre build doit être terminée.

    ![][28]

1. Si vous double-cliquez sur le nom de génération, Visual Studio affiche un **Résumé de la Build**, y compris les résultats des tests de projets de test unitaire associé.

    ![][29]

1. Dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez afficher le déploiement associé sous l’onglet **déploiements** lorsque l’environnement intermédiaire est sélectionnée.

    ![][30]

1.  Accédez à l’URL de votre site. Pour une application web, cliquez simplement sur le bouton **Parcourir** dans la barre de commandes. Pour un service cloud, choisissez l’URL dans la section **Aperçu rapide** de la page de **tableau de bord** qui affiche l’environnement intermédiaire d’un service cloud. Déploiements d’intégration continue pour les services en nuage sont publiés dans l’environnement intermédiaire par défaut. Vous pouvez le modifier en définissant la propriété **Environnement de Service Cloud substitution** en **Production**. Cette capture d’écran indique l’endroit où l’URL du site sur la page de tableau de bord du service cloud.

    ![][31]

    Un nouvel onglet de navigateur s’ouvre pour afficher votre site en cours d’exécution.

    ![][32]

    Pour les services en nuage, si vous apportez d’autres modifications à votre projet, vous déclencheur crée plus et vous s’accumuler plusieurs déploiements. L’option dernière marqué comme étant actif.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5 : redéployez une version antérieure

Cette étape s’applique aux services cloud et est facultative. Dans le portail Azure classique, sélectionnez un déploiement antérieur, puis sélectionnez le bouton **redéployez** rembobiner votre site à un archivage antérieures.  Notez que cette opération déclencher la lecture d’une nouvelle version dans TFS et crée une nouvelle entrée dans l’historique de votre déploiement.

![][34]

## <a name="6-change-the-production-deployment"></a>6 : modifier le déploiement de Production

Cette étape s’applique uniquement aux services en nuage, pas les applications web. Lorsque vous êtes prêt, vous pouvez promouvoir l’environnement intermédiaire pour l’environnement de production en cliquant sur le bouton **intervertir** dans le portail classique Azure. L’environnement intermédiaire nouvellement déployé est convertie en Production, et l’environnement de Production précédente, le cas échéant, devient un environnement intermédiaire. Le déploiement Active peut différer la Production et mis en œuvre des environnements, mais l’historique de déploiement des dernières versions préliminaires est la même quelle que soit l’environnement.

![][35]

## <a name="7-run-unit-tests"></a>7 : exécuter des tests unitaires

Cette étape s’applique uniquement aux applications web, services en nuage pas. Pour placer un portail de qualité dans votre déploiement, vous pouvez exécuter les tests unitaires et si elles échouent, vous pouvez arrêter le déploiement.

1.  Dans Visual Studio, ajoutez un projet de test unitaire.

    ![][39]

1.  Ajoutez des références de projet au projet que vous souhaitez tester.

    ![][40]

1.  Ajouter quelques tests unitaires. Pour commencer, essayez un test factice passera toujours.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Modifier la définition de build, choisissez l’onglet **processus** , puis développez le nœud de **Test** .

1.  Définissez la **faire échouer la build en cas d’échec de test** sur True. Cela signifie que le déploiement ne se produire, à moins que les tests ont réussi.

    ![][41]

1.  File d’attente une nouvelle build.

    ![][42]

    ![][43]

1. Tandis que la version est cours, vérifiez la progression.

    ![][44]

    ![][45]

1. Lorsque la génération est terminée, vérifiez les résultats.

    ![][46]

    ![][47]

1.  Essayez de créer un test échoue. Ajouter un nouveau test en copiant la première miniature, renommez-la et commentez la ligne de code qui indique NotImplementedException est une exception attendue.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Archiver la modification dans la file d’attente une nouvelle build.

    ![][48]

1. Afficher les résultats des tests pour afficher des détails sur le problème.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les tests d’unités dans Visual Studio Team Services, voir [exécuter les tests d’unité dans votre build](http://go.microsoft.com/fwlink/p/?LinkId=510474). Si vous utilisez Git, voir [partager votre code dans Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) et [déploiement continue à Azure Application Service](../app-service-web/app-service-continuous-deployment.md).  Pour plus d’informations sur Visual Studio Team Services, consultez [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
