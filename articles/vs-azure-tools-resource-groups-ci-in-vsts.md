<properties
    pageTitle="Intégration continue dans VS Team Services à l’aide de projets de groupe de ressources Azure | Microsoft Azure"
    description="Décrit comment configurer l’intégration continue dans Visual Studio Team Services à l’aide de projets de déploiement de groupe de ressources Azure dans Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Intégration continue dans Visual Studio Team Services à l’aide de projets de déploiement de groupe de ressources Azure

Pour déployer un modèle Azure, vous devez effectuer les tâches passent par les différentes étapes : Build, Test, copier vers Azure (également appelé « Reclassement ») et déployer un modèle.  Il existe deux façons d’effectuer cette opération dans Visual Studio Team Services (VS Team Services). Ces deux méthodes fournir le même résultat, sélectionnez celui qui convient le mieux à votre flux de travail.

-   Ajouter une seule étape à votre définition de build qui exécute le script PowerShell inclus dans le projet de déploiement de groupe de ressources Azure (déployer-AzureResourceGroup.ps1). Le script copie les objets et puis déploie le modèle.
-   Ajouter que plusieurs VS Team Services étapes de génération, chacun d'entre eux exécuter une tâche de la scène.

Cet article explique comment utiliser la première option (utilisez une définition de build pour exécuter le script PowerShell). L’un des avantages de cette option sont que le script utilisé par les développeurs dans Visual Studio est le même script utilisé par VS Team Services. Cette procédure suppose que vous disposez déjà d’un projet de déploiement Visual Studio archivé VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Copier des objets dans Azure 

Quel que soit le scénario, si vous avez les objets qui sont nécessaires pour le déploiement de modèle, vous devrez accorder l’accès Azure le Gestionnaire de ressources qui leur. Ces objets peuvent inclure des fichiers tels que :

-   Modèles imbriqués
-   DSC et les scripts de configuration
-   Fichiers binaires d’application

### <a name="nested-templates-and-configuration-scripts"></a>Les modèles imbriqués et les Scripts de Configuration
Lorsque vous utilisez les modèles fournis par Visual Studio (ou créé avec des extraits de Visual Studio), le script PowerShell stades non seulement les objets, il paramètre également l’URI pour les ressources pour les déploiements différents. Le script puis copie les objets dans un conteneur sécurisé dans Azure, crée un jeton associations de sécurité pour qu’ils contiennent, puis passe ces informations sur le déploiement du modèle. Voir [Création d’un déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx) pour en savoir plus sur les modèles imbriqués.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurer la déploiement continue dans VS Team Services

Pour appeler le script PowerShell dans VS Team Services, vous devez mettre à jour votre définition de build. En bref, les étapes sont : 

1.  Modifier la définition de build.
1.  Configurer l’autorisation Azure dans VS Team Services.
1.  Ajoutez une étape de génération de PowerShell Azure qui fait référence au script PowerShell dans le projet de déploiement de groupe de ressources Azure.
1.  Définissez la valeur du paramètre *- ArtifactsStagingDirectory* pour l’utiliser avec un projet créé dans VS Team Services.

### <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

Les étapes suivantes vous guidera dans les étapes nécessaires pour configurer le déploiement continue dans VS Team Services 

1.  Modifier la définition de votre build VS Team Services et ajoutez une étape de génération Azure PowerShell. Sélectionnez la définition de build sous la catégorie **des définitions de Build** , puis sélectionnez le lien **Modifier** .

    ![][0]

1.  Ajouter une nouvelle étape de génération **Azure PowerShell** pour la définition de build, puis **Ajouter créer étape...** bouton.

    ![][1]

1.  Sélectionnez la catégorie de **tâches de déploiement** , sélectionnez la tâche **Azure PowerShell** et puis cliquez sur le bouton **Ajouter** .

    ![][2]

1.  Sélectionnez l’étape de génération **Azure PowerShell** , puis renseignez ses valeurs.

    1.  Si vous disposez déjà d’un point de terminaison du service Azure ajouté à VS Team Services, sélectionnez l’abonnement dans la zone de liste déroulante **Abonnement Azure** , puis passez à la section suivante. 

        Si vous n’avez pas un point de terminaison du service Azure dans VS Team Services, vous devez en ajouter une. Cette section vous permet d’accéder à travers le processus. Si votre compte Azure utilise un compte Microsoft (par exemple, Hotmail), vous devez procéder comme suit pour obtenir une authentification Principal du Service.

    1.  Cliquez sur le lien **Gérer** en regard de l' **Abonnement Azure** déroulant de la zone de liste.

        ![][3]

    1. Dans la zone de liste déroulante **Nouveau point de terminaison de Service** , sélectionnez **Azure** .

        ![][4]

    1.  Dans la boîte de dialogue **Ajouter un abonnement Azure** , sélectionnez l’option **Principal du Service** .

        ![][5]

    1.  Ajouter des informations sur votre abonnement Azure à la boîte de dialogue **Ajouter un abonnement Azure** . Vous devez disposer des éléments suivants :
        -   Id de l’abonnement
        -   Nom de l’abonnement
        -   Id de Principal du service
        -   Clé Principal du service
        -   Id de client

    1.  Ajouter un nom de votre choix dans la zone de nom **d’abonnement** . Cette valeur s’affichent plus loin dans la liste VS Team Services déroulante **Abonnement Azure** . 

    1.  Si vous ne connaissez pas votre ID d’abonnement Azure, vous pouvez utiliser une des commandes suivantes pour obtenir.
        
        Pour les scripts PowerShell, utilisez :

        `Get-AzureRmSubscription`

        Pour Azure infrastructure du langage commun, utilisez :

        `azure account show`
    

    1.  Pour obtenir une identification Principal du Service, clé Principal du Service et l’ID de client, suivent la procédure de [créer un Active Directory application et principal du service à l’aide de portail](resource-group-create-service-principal-portal.md) ou [authentifier un principal de service avec le Gestionnaire de ressources Azure](resource-group-authenticate-service-principal.md).

    1.  Ajoutez les valeurs Principal du Service ID, clé Principal du Service et l’ID de client à la boîte de dialogue **Ajouter un abonnement Azure** , puis sélectionnez le bouton **OK** .

        Vous avez à présent une entité Service valide à utiliser pour exécuter le script PowerShell Azure.

1.  Modifier la définition de build, puis sélectionnez l’étape de génération **Azure PowerShell** . Sélectionnez l’abonnement dans la zone de liste déroulante **Abonnement Azure** . (Si l’abonnement n’apparaît pas, cliquez sur le bouton **Actualiser** suivant le lien **Gérer** .) 

    ![][8]

1.  Fournir un chemin d’accès au script PowerShell déployer AzureResourceGroup.ps1. Pour ce faire, cliquez sur le bouton points de suspension (...) en regard de la zone **Chemin d’accès de Script** , recherchez le script PowerShell déployer AzureResourceGroup.ps1 dans le dossier **Scripts** de votre projet, sélectionnez-le, puis cliquez sur le bouton **OK** . 

    ![][9]

1. Après avoir sélectionné le script, mettez à jour le chemin d’accès au script afin qu’elle s’exécute à partir de la Build.StagingDirectory (le répertoire *ArtifactsLocation* est égale à). Vous pouvez le faire en ajoutant « $(Build.StagingDirectory)/ « au début du chemin du script.

    ![][10]

1.  Dans la zone **Arguments de Script** , entrez les paramètres suivants (en une seule ligne). Lorsque vous exécutez le script dans Visual Studio, vous pouvez voir comment VS utilise les paramètres dans la fenêtre de **sortie** . Vous pouvez utiliser ceci comme point de départ pour définir les valeurs de paramètre dans votre étape de génération.

  	| Paramètre | Description|
  	|---|---|
  	| -ResourceGroupLocation           | La valeur de géolocalisation où se trouve, par exemple **eastus** ou **« US Extrême-Orient »**le groupe de ressources. (Ajouter des guillemets simples s’il existe un espace dans le nom). Pour plus d’informations, consultez [Azure régions](https://azure.microsoft.com/en-us/regions/) .|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | Le nom du groupe de ressources utilisé pour ce déploiement.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Ce paramètre, le cas échéant, spécifie qu’objets doivent être téléchargés vers Azure à partir du système local. Vous ne devez définir ce commutateur si votre déploiement modèle requiert des objets supplémentaires à l’étape en utilisant le script PowerShell (par exemple, les scripts de configuration ou les modèles imbriqués).                                                                                                                                                                 |
  	| -StorageAccountName              | Le nom du compte de stockage utilisé pour les objets de la scène pour ce déploiement. Ce paramètre est requis uniquement si vous copiez des objets à Azure. Ce compte de stockage ne sera pas automatiquement créé par le déploiement, il doit déjà exister.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | Le nom du groupe de ressources associé au compte de stockage. Ce paramètre est requis uniquement si vous copiez des objets à Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | Le chemin d’accès au fichier de modèle dans le projet de déploiement de groupe de ressources Azure. Pour améliorer la flexibilité, utilisez un chemin d’accès pour ce paramètre est par rapport à l’emplacement du script PowerShell au lieu d’un chemin d’accès absolu.|
  	| -TemplateParametersFile          | Le chemin d’accès au fichier de paramètres dans le projet de déploiement de groupe de ressources Azure. Pour améliorer la flexibilité, utilisez un chemin d’accès pour ce paramètre est par rapport à l’emplacement du script PowerShell au lieu d’un chemin d’accès absolu.|
  	| -ArtifactStagingDirectory        | Ce paramètre vous permet de la session PowerShell script connaître le dossier à partir duquel les fichiers binaires du projet doivent être copiés. Cette valeur remplace la valeur par défaut utilisée par le script PowerShell. Pour les Services d’équipe VS utilisez, définissez la valeur : $(Build.StagingDirectory) - ArtifactStagingDirectory                                                                                                                                                                                              |

    Voici un exemple d’arguments script (ligne incorrecte pour une meilleure lisibilité) :

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Lorsque vous avez terminé, la zone **Arguments de Script** doit ressembler à ceci.

    ![][11]

1.  Après avoir ajouté que tous les éléments requis pour la session PowerShell Azure étape de génération, cliquez sur la **file d’attente** générer bouton pour générer le projet. L’écran **créer** montre la sortie à partir du script PowerShell.

## <a name="next-steps"></a>Étapes suivantes

Lisez la [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure et groupes de ressources Azure.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
