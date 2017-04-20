<properties
    pageTitle="Publier une application sur un cluster distant avec Visual Studio | Microsoft Azure"
    description="Découvrez comment publier une application à un cluster de tissu service distant à l’aide de Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Publier une application sur un cluster distant à l’aide de Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

L’extension Azure Service tissu pour Visual Studio fournit un moyen facile, répétitif et contenant un script pour publier une application à un cluster de tissu de Service.

## <a name="the-artifacts-required-for-publishing"></a>Les objets requis pour la publication

### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 déployer

Il s’agit d’un script PowerShell qui utilise un chemin d’accès du profil publier en tant que paramètre pour publier des applications de Service tissu. Étant donné que ce script fait partie de votre application, n’hésitez pas à modifier selon les besoins de votre application.

### <a name="publish-profiles"></a>Publier les profils

Un dossier dans le projet d’application de Service TISSU appelé **PublishProfiles** contient des fichiers XML qui stockent les informations essentielles pour la publication d’une application, tel que :

- Paramètres de connexion de service TISSU cluster
- Chemin d’accès à un fichier de paramètre d’application
- Paramètres de mise à niveau

Par défaut, votre application inclura deux des profils de publication : Local.xml et Cloud.xml. Vous pouvez ajouter d’autres profils par copier-coller parmi les fichiers par défaut.

### <a name="application-parameter-files"></a>Fichiers de paramètres d’application

Un dossier dans le projet d’application de Service TISSU appelé **ApplicationParameters** contient des fichiers XML pour les valeurs de paramètre manifeste d’application spécifié par l’utilisateur. Fichiers manifeste d’application peuvent être paramétrées afin que vous puissiez utiliser différentes valeurs pour les paramètres de déploiement. Pour plus d’informations sur le paramétrage de votre application, voir [gérer plusieurs environnements dans tissu de Service](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] Pour les services acteur, vous devez créer le projet essayant d’abord avant de modifier le fichier dans un éditeur ou via la boîte de dialogue Publier. C’est parce que la partie des fichiers de manifeste est généré lors de la génération.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Pour publier une application à l’aide de la boîte de dialogue Publier Service TISSU Application

Les étapes suivantes expliquent comment publier une application à l’aide de la boîte de dialogue **Publier Service TISSU Application** fournie par les outils de tissu Visual Studio Service.

1. Dans le menu contextuel du projet Application tissu de Service, choisissez **publier...** Pour afficher la boîte de dialogue **Publier Service TISSU Application** .

    ![La ** boîte de dialogue Publier Service TISSU Application **][0]

    Le fichier sélectionné dans la zone de liste déroulante **profil cible** est l’endroit où tous les paramètres, à l’exception de **manifeste versions**, sont enregistrés. Vous pouvez réutiliser un profil existant ou créez-en une en cliquant sur **< gérer les profils... >** dans la zone de liste déroulante **profil cible** . Lorsque vous choisissez un profil de publication, son contenu apparaît dans les champs correspondants de la boîte de dialogue. Pour enregistrer vos modifications à tout moment, cliquez sur le lien **Enregistrer le profil** .    

2. Dans la section **point de terminaison de connexion** , spécifiez le point de terminaison publication locale ou distante tissu de Service d’un cluster. Pour ajouter ou modifier le point de terminaison de connexion, cliquez sur la liste déroulante de **Point de terminaison de connexion** . La liste indique le cluster Service TISSU disponible extrémités de la connexion à laquelle vous pouvez publier en fonction de vos abonnements Azure. Notez que si vous n’êtes pas déjà connecté à Visual Studio, vous devrez faire.

    Utilisez la boîte de dialogue de sélection de cluster pour choisir à partir de l’ensemble des abonnements disponibles et clusters.

    ![La ** boîte de dialogue Sélectionnez Service TISSU Cluster **][1]

    >[AZURE.NOTE] Si vous voulez publier sur un point de terminaison arbitraire (par exemple, un cluster de fête), consultez la section **publication à un point de terminaison cluster arbitraire** ci-dessous.

    Une fois que vous choisissez un point de terminaison, Visual Studio valide la connexion au cluster Service TISSU sélectionné. Si le cluster n’est pas sécurisé, Visual Studio y connecter immédiatement. Toutefois, si le cluster est sécurisé, vous devez installer un certificat sur votre ordinateur local avant de poursuivre. Pour plus d’informations, voir [comment configurer la connexion sécurisée](service-fabric-visualstudio-configure-secure-connections.md) . Lorsque vous avez terminé, cliquez sur le bouton **OK** . Le cluster sélectionné apparaît dans la boîte de dialogue **Publier Service TISSU Application** .

3. Dans la zone de liste déroulante du **Fichier de paramètres d’Application** , accédez à un fichier de paramètres d’application. Un fichier de paramètres d’application conserve des valeurs spécifiées par l’utilisateur pour les paramètres dans le fichier manifeste d’application. Pour ajouter ou modifier un paramètre, cliquez sur le bouton **Modifier** . Entrez ou modifiez la valeur du paramètre dans la grille de **paramètres** . Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer** .

    ![La ** boîte de dialogue Modifier les paramètres **][2]

4. Utiliser la case à cocher **mettre à niveau de l’Application** pour spécifier si cela publier action est une mise à niveau. Mise à niveau publier actions diffèrent normal publier des actions. Pour obtenir une liste des différences entre, voir [Mettre à niveau de Service TISSU Application](service-fabric-application-upgrade.md) . Pour configurer les paramètres de mise à niveau, cliquez sur le lien **Configurer les paramètres de mise à niveau** . L’éditeur de paramètres de mise à niveau s’affiche. Voir la rubrique [configurer la mise à niveau d’une application de Service TISSU](service-fabric-visualstudio-configure-upgrade.md) pour en savoir plus sur les paramètres de mise à niveau.

5. Choisissez le **manifeste Versions...** bouton pour afficher la boîte de dialogue **Modifier les Versions** . Vous devez mettre à jour des applications et les versions de service pour une mise à niveau se déroule. Voir [application Service TISSU didacticiel de mise à niveau](service-fabric-application-upgrade-tutorial.md) pour découvrir comment service manifestes d’application et versions impact un processus de mise à niveau.

    ![La ** boîte de dialogue Modifier les Versions **][3]

    Si l’application et les versions de service utilisent le contrôle de version sémantique comme 1.0.0 ou des valeurs numériques au format 1.0.0.0, sélectionnez l’option **mettre à jour automatiquement les applications et les versions de service** . Lorsque vous choisissez cette option, le service et les numéros de version d’application sont automatiquement mis à jour chaque fois qu’un code, config ou données version du package sont mis à jour. Si vous préférez modifier manuellement les versions, désactivez la case à cocher pour désactiver cette fonctionnalité.

    >[AZURE.NOTE] Pour toutes les entrées de package apparaissent pour un projet d’un acteur, tout d’abord créer le projet pour générer les entrées dans les fichiers manifeste de Service.

6. Lorsque vous avez terminé spécifiant tous les paramètres nécessaires, cliquez sur le bouton **Publier** pour publier votre application sur le cluster Service TISSU sélectionné. Les paramètres que vous avez spécifié sont appliquées au processus de publication.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publier sur un point de terminaison cluster arbitraire (y compris clusters partie)

L’expérience de publication de Visual Studio est optimisé pour la publication sur clusters distants associé à un de vos abonnements Azure. Toutefois, il est possible sur Publier sur les points de terminaison arbitraires (par exemple, TISSU Service tiers clusters) en modifiant directement le profil de publication XML. Comme décrit ci-dessus, deux publier profils sont fournis par défaut--**Local.xml** et **Cloud.xml**--mais n’hésitez pas à créer des profils supplémentaires pour les différents environnements. Par exemple, vous souhaiterez peut-être créer un profil pour la publication sur clusters fête, nommée **Party.xml**.

Si vous vous connectez à un cluster non sécurisé, tout ce qui est nécessaire est le point de terminaison de connexion cluster, tel que `partycluster1.eastus.cloudapp.azure.com:19000`. Dans ce cas, le point de terminaison de connexion dans le profil de publication serait ressembler à ceci :

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Si vous vous connectez à un cluster sécurisé, vous devez également fournir les détails du certificat client à partir du magasin local devant être utilisé pour l’authentification. Pour plus d’informations, voir [configuration connexion sécurisée vers un cluster de tissu de Service](service-fabric-visualstudio-configure-secure-connections.md).

  Une fois que votre profil de publication a été configurée, vous pouvez la référence dans la boîte de dialogue Publier comme illustré ci-dessous.

  ![Publier le nouveau profil dans la boîte de dialogue Publier][4]

  Notez que dans ce cas, le nouveau profil de publication pointe vers un des fichiers de paramètre de l’application par défaut. Cette option si vous souhaitez publier la même configuration d’application à un nombre d’environnements. En revanche, dans les cas où vous souhaitez avoir des configurations différentes pour chaque environnement dans lequel vous voulez publier sur, il serait judicieux de créer un fichier de paramètres d’application correspondante.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment automatiser le processus de publication dans un environnement d’intégration continu, voir [configurer l’intégration continue tissu de Service](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
