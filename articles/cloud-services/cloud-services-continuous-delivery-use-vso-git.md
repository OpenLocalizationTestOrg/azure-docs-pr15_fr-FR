<properties
    pageTitle="Remise continue avec Git et Visual Studio Team Services dans Azure | Microsoft Azure"
    description="Découvrez comment configurer vos projets d’équipe Visual Studio Team Services pour Git permet de créer et de déployer la fonctionnalité Web App dans Azure Application Service ou sur le cloud services automatiquement."
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Remise continue à Azure à l’aide de Visual Studio Team Services et Git

Vous pouvez utiliser les projets d’équipe Visual Studio Team Services pour héberger un référentiel Git pour votre code source, automatiquement créer et déployer des applications web Azure ou services en nuage chaque fois que vous appuyez sur une validation vers le référentiel.

Vous devez Visual Studio 2013 et le Kit de développement Azure installé. Si vous n’avez pas Visual Studio 2013, téléchargez-le en choisissant le lien de **mise en route gratuitement** en [www.visualstudio.com](http://www.visualstudio.com). Installez le Kit de développement Azure [ici](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Vous avez besoin d’un compte de Visual Studio Team Services pour effectuer ce didacticiel : vous pouvez [Ouvrir un compte Visual Studio Team Services gratuitement](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Pour configurer un service cloud pour créer et déployer sur Azure à l’aide de Visual Studio Team Services automatiquement, procédez comme suit.

## <a name="1-create-a-git-repository"></a>1 : créer un référentiel Git

1. Si vous n’avez pas un compte Visual Studio Team Services, vous pouvez en obtenir un [ici](http://go.microsoft.com/fwlink/?LinkId=397665). Lorsque vous créez votre projet d’équipe, choisissez Git comme votre système de contrôle de source. Suivez les instructions pour connecter Visual Studio à votre projet d’équipe.

2. Dans **L’Explorateur d’équipes**, cliquez sur le lien **cloner ce référentiel** .

    ![][3]

3. Spécifier l’emplacement de la copie locale, puis sur le bouton **Dupliquer** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2 : créer un projet et valider vers le référentiel

1. Dans **L’Explorateur d’équipes**, dans la section **Solutions** , cliquez sur le lien **Nouveau** pour créer un projet dans le magasin local.

    ![][4]

2. Vous pouvez déployer une application web ou un service cloud (Application Azure) en suivant les étapes décrites dans cette procédure pas à pas. Créer un nouveau projet Azure Cloud Service, ou un nouveau projet ASP.NET MVC. Assurez-vous que le projet cible .NET Framework 4 ou version ultérieure. Si vous créez un projet de service cloud, ajoutez un rôle web ASP.NET MVC et un rôle de collaborateur.
Si vous voulez créer une application web, sélectionnez le modèle de projet **Application Web ASP.NET** , puis **MVC**. Pour plus d’informations, voir [créer une application web ASP.NET dans le Service d’application Azure](../app-service-web/web-sites-dotnet-get-started.md) .

3. Ouvrir le menu contextuel de la solution, puis cliquez sur **Valider**.

    ![][7]

4. Si c’est la première fois que vous avez utilisé Git dans Visual Studio Team Services, vous devez fournir des informations pour vous identifier dans Git. Dans la zone de **Modifications en attente** de **L’Explorateur d’équipes**, entrez votre nom d’utilisateur et votre adresse électronique. Entrer un commentaire pour la validation, puis sélectionnez le bouton de **validation** .

    ![][8]

5. Notez les options pour inclure ou exclure des modifications spécifiques lorsque vous archivez. Si les modifications que vous souhaitez sont exclues, sélectionnez **Inclure tout**.

6. Vous avez maintenant validée les modifications dans votre copie locale du référentiel. Ensuite, synchroniser ces modifications avec le serveur en choisissant le lien de **synchronisation** .

## <a name="3-connect-the-project-to-azure"></a>3 : connecter le projet à Azure

1. À présent que vous disposez d’un référentiel de Git dans Visual Studio Team Services avec du code source qu’il contient, vous êtes prêt à vous connecter votre référentiel git à Azure.  Dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885), sélectionnez votre application web ou service cloud, ou créez-en une en sélectionnant l’icône + en bas à gauche et en choisissant **Service Cloud** ou **Web App** , puis **Création rapide**.

    ![][9]

3. Pour les services en nuage, cliquez sur le lien **configurer la publication avec Visual Studio Team Services** . Pour les applications web, cliquez sur le lien **configurer déploiement du contrôle de source** .

    ![][10]

2. Dans l’Assistant, tapez le nom de votre compte de Visual Studio Team Services dans la zone de texte et cliquez sur le lien **Autoriser maintenant** . Vous pourriez être invité à se connecter.

    ![][11]

3. Dans le menu contextuel de **Demande de connexion** , sélectionnez **Accepter** pour autoriser Azure pour configurer votre projet d’équipe dans Visual Studio Team Services.

    ![][12]

4. Une fois l’autorisation établie, vous voyez une liste déroulante qui contient vos projets d’équipe Visual Studio Team Services.  Sélectionnez le nom du projet d’équipe que vous avez créé lors des étapes précédentes, puis sélectionnez le bouton de coche de l’Assistant.

    ![][13]

    La prochaine fois que vous appuyez sur une validation dans votre référentiel, Visual Studio Team Services créer et déployer votre projet dans Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4 : déclencher une reconstruction et redéployez votre projet

1. Dans Visual Studio, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier `_Layout.cshtml` sous les vues\\dossier partagé dans un rôle web MVC.

    ![][17]

2. Modifier le texte de pied de page pour le site et enregistrez le fichier.

    ![][18]

3. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le nœud de solution, le nœud de projet ou le fichier que vous avez modifié et puis cliquez sur **Valider**.

4. Tapez un commentaire, puis cliquez sur **Valider**.

    ![][20]

5. Cliquez sur le lien de **synchronisation** .

    ![][38]

6. Cliquez sur le lien **Push** pour pousser votre valider vers le référentiel dans Visual Studio Team Services. (Vous pouvez également utiliser le bouton **synchroniser** pour copier votre validations vers le référentiel. La différence est que **synchronisation** extrait également les dernières modifications apportées à partir du référentiel.)

    ![][39]

7. Cliquez sur le bouton **accueil** pour revenir à la page d’accueil de **L’Explorateur d’équipes** .

    ![][21]

8. Choisissez **crée** pour afficher les versions en cours.

    ![][22]

    **Team Explorer** montre qu’une build a été déclenchée pour votre archivage.

    ![][23]

9. Pour afficher un journal détaillé que la génération progresse, double-cliquez sur le nom de la version en cours.

10. Tandis que la génération est en cours, consultez la définition de build qui a été créée lorsque vous avez utilisé l’Assistant pour créer un lien vers Azure.  Ouvrez le menu contextuel pour la définition de build, puis sélectionnez **Modifier la définition de Build**.

    ![][25]

11. Dans l’onglet **déclencheur** , vous verrez que la définition de build est configurée pour générer sur chaque archivage, par défaut. (Pour un service cloud, Visual Studio Team Services crée et déploie la branche maître sur l’environnement intermédiaire automatiquement. Vous devez toujours une étape manuel pour déployer sur le site en ligne. Pour une application web qui n’a pas environnement intermédiaire, il déploie la branche maître directement sur le site en ligne.

    ![][26]

1. Dans l’onglet **processus** , vous pouvez voir que l’environnement de déploiement est défini sur le nom de votre application web ou service cloud.

    ![][27]

1. Spécifier des valeurs pour les propriétés si vous souhaitez que les valeurs différentes de celles les valeurs par défaut. Les propriétés pour la publication Azure se trouvent dans la section **déploiement** , et vous devrez peut-être également définir des paramètres de MSBuild. Par exemple, dans un nuage projet de service, pour spécifier une configuration du service différent de « Nuage », définissez les paramètres de MSbuild sur `/p:TargetProfile=[YourProfile]` où *[YourProfile]* correspond à un fichier de configuration de service avec un nom tel que ServiceConfiguration. *YourProfile*.cscfg.

    Le tableau suivant montre les propriétés disponibles dans la section **déploiement** :

  	|Propriété|Valeur par défaut|
  	|---|---|
  	|Autoriser les certificats non approuvés|Si faux, les certificats SSL doivent être signés par une autorité racine.|
  	|Autoriser la mise à niveau|Permet le déploiement mettre à jour un déploiement existant au lieu de créer un nouvel identifiant. Conserve l’adresse IP.|
  	|Ne pas supprimer|Si vrai, ne pas remplacer un déploiement non lié existant (mise à niveau est autorisé).|
  	|Chemin d’accès aux paramètres de déploiement|Le chemin d’accès à votre fichier .pubxml pour une application web, par rapport au dossier racine de la mis en pension. Ignoré pour les services cloud.|
  	|Environnement de déploiement de SharePoint|La même que le nom du service.|
  	|Environnement de déploiement d’Azure|Le nom du service web app ou sur le cloud.|

1. À ce stade, votre build doit être terminée.

    ![][28]

1. Si vous double-cliquez sur le nom de génération, Visual Studio affiche un **Résumé de la Build**, y compris les résultats des tests de projets de test unitaire associé.

    ![][29]

1. Dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez afficher le déploiement associé sous l’onglet **déploiements** lorsque l’environnement intermédiaire est sélectionnée.

    ![][30]

1.  Accédez à l’URL de votre site. Pour une application web, cliquez simplement sur le bouton **Parcourir** dans le portail. Pour un service cloud, choisissez l’URL dans la section **Aperçu rapide** de la page de **tableau de bord** qui affiche l’environnement intermédiaire.

    Déploiements d’intégration continue pour les services en nuage sont publiés dans l’environnement intermédiaire par défaut. Vous pouvez le modifier en définissant la propriété **Environnement de Service Cloud substitution** en **Production**. Voici l’endroit où l’URL du site se trouve sur la page de tableau de bord du service cloud.

    ![][31]

    Un nouvel onglet de navigateur s’ouvre pour afficher votre site en cours d’exécution.

    ![][32]

1.  Si vous apportez d’autres modifications à votre projet, vous déclencheur plus crée, et vous s’accumuler plusieurs déploiements. L’option dernière est marquée comme étant actif.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5 : redéployez une version antérieure

Cette étape est facultative. Dans le portail Azure classique, sélectionnez un déploiement antérieur puis **redéployez** rembobiner votre site à un archivage antérieures. Notez que cette opération déclencher la lecture d’une nouvelle version dans TFS et crée une nouvelle entrée dans l’historique de votre déploiement.

![][34]

## <a name="6-change-the-production-deployment"></a>6 : modifier le déploiement de Production

Lorsque vous êtes prêt, vous pouvez promouvoir l’environnement intermédiaire pour l’environnement de Production en choisissant **intervertir** dans le portail classique Azure. L’environnement intermédiaire nouvellement déployé est convertie en Production, et l’environnement de Production précédente, le cas échéant, devient un environnement intermédiaire. Le déploiement Active peut différer la Production et mis en œuvre des environnements, mais l’historique de déploiement des dernières versions préliminaires est la même quelle que soit l’environnement.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7 : déploiement d’une branche de travail.

Lorsque vous utilisez Git, vous généralement apportez des modifications dans une branche de travail et intégrez dans la branche maître lorsque votre développement atteint un état terminé. Pendant la phase de développement d’un projet, vous souhaiterez créer et déployer la branche de travail dans Azure.

1. Dans **L’Explorateur d’équipes**, sélectionnez le bouton **accueil** , puis sélectionnez le bouton **Branches** .

    ![][40]

2. Cliquez sur le lien **Nouvelle branche** .

    ![][41]

3. Entrez le nom de la succursale, tels que « traitement » et choisissez **Créer une branche**. Cela crée une nouvelle branche locale.

    ![][42]

4. Publier la branche. Sélectionnez le nom de la succursale dans **branches non publiés**, puis sur **Publier**.

    ![][44]

6. Par défaut, seules les modifications à la branche maître déclenchent une build en continu. Pour configurer build en continu pour une branche de travail, cliquez sur la page **crée** dans **L’Explorateur d’équipes**, puis sélectionnez **Modifier la définition de Build**.

7. Ouvrez l’onglet **Paramètres de la Source** . Sous **contrôlés avec branches pour l’intégration continue et générer**, sélectionnez **Cliquez ici pour ajouter une nouvelle ligne**.

    ![][47]

8. Spécifiez la branche que vous avez créé, tel que Réf/têtes/travail.

    ![][48]

9. Effectuez une modification dans le code, ouvrez le menu contextuel pour le fichier modifié et puis cliquez sur **Valider**.

    ![][43]

10. Cliquez sur le lien **Validations non synchronisée** , puis sélectionnez le bouton **synchroniser** ou sur le lien **de transmission** pour copier les modifications à la copie de la branche de travail dans Visual Studio Team Services.

    ![][45]

11. Naviguez jusqu'à l’affichage **crée** et recherchez la génération déclenchant simplement vous utilisez pour la branche de travail.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir des conseils supplémentaires sur l’utilisation de Git avec Visual Studio Team Services, voir [développer et partager votre code dans Git à l’aide de Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) et pour plus d’informations sur l’utilisation d’un référentiel Git qui n’est pas géré par Visual Studio Team Services à publier sur Azure, voir [Déploiement continue à Azure Application Service](../app-service-web/app-service-continuous-deployment.md). Pour plus d’informations sur Visual Studio Team Services, consultez [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
