<properties
   pageTitle="Environnements de développement et de test | Microsoft Azure"
   description="Apprenez à utiliser le Gestionnaire de ressources Azure modèles pour accéder rapidement et systématiquement créer et supprimer de développement et environnements de test."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Environnements de développement et de test dans Microsoft Azure

Applications personnalisées sont généralement déployées sur plusieurs environnements de développement et tests avant le déploiement en production. Lorsque les environnements sont créés en local, ressources informatiques sont achetées ou alloués pour chaque environnement pour chaque application. Les environnements incluent souvent plusieurs ordinateurs physiques ou virtuels avec des configurations spécifiques qui sont déployées manuellement ou avec scripts d’automatisation complexes. Déploiements souvent prennent heures et entraînent configurations incohérentes dans des environnements.

## <a name="scenario"></a>Scénario ##

Lorsque vous configurez les environnements de développement et test dans Microsoft Azure, vous payez uniquement pour les ressources que vous utilisez.  Cet article explique comment rapidement et de manière cohérente à travers vous pouvez créer, mettre à jour et supprimer de développement et tester les environnements à l’aide du Gestionnaire de ressources Azure modèles et fichiers de paramètre, comme illustré ci-dessous.

![Scénario](./media/solution-dev-test-environments/scenario.png)

Trois environnements de test et développement sont présentés ci-dessus.  Chacune possède une application web et des ressources de base de données SQL, qui sont spécifiées dans un fichier de modèle.  Les noms de l’application et la base de données dans chaque environnement sont différentes et sont spécifiés dans les fichiers de paramètre unique pour chaque environnement.  

Si vous n’êtes pas familiarisé avec les concepts de gestionnaire de ressources Azure, il est recommandé de lire l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) avant la lecture de cet article.

Vous voudrez peut-être traitée tout d’abord les étapes décrites dans cet article tel qu’il figure sans lire un des articles référencées d’acquérir rapidement une expérience à l’aide de modèles Azure le Gestionnaire de ressources. Une fois que vous avez été une fois les étapes, vous pourrez obtenir des réponses à la plupart des questions qui a été créée votre premier temps via en essayant supplémentaires en suivant les étapes et en lisant les articles référencées.

## <a name="plan-azure-resource-use"></a>Planifier l’utilisation des ressources Azure
Une fois que vous avez une conception de niveau supérieur pour votre application, vous pouvez définir :

- Les ressources Azure votre application inclura. Vous pourriez générer votre application et déployer une application Web Azure avec une base de données SQL Azure.  Vous pouvez créer votre application dans des machines virtuelles à l’aide de PHP et MySQL ou IIS et SQL Server ou autres composants. L’article [Azure Application Service, les Services en nuage et comparaison de Machines virtuelles]( app-service-web/choose-web-site-cloud-service-vm.md) vous permet de choisir quelles ressources Azure, vous souhaiterez peut-être utiliser pour votre application.
- Quel niveau de service requis comme disponibilité, sécurité et échelle répondant à votre application.

## <a name="download-an-existing-template"></a>Télécharger un modèle existant
Un modèle de gestionnaire de ressources Azure définit toutes les ressources Azure qui utilise votre application. Plusieurs modèles déjà existent que vous pouvez déployer directement dans le portail Azure, ou télécharger, modifier et enregistrer dans un système de contrôle de code source avec votre code de l’application.  Suivez les étapes ci-dessous pour télécharger un modèle existant.

1. Parcourir les modèles existants dans le référentiel GitHub [Modèles de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates/) . Dans la liste, vous verrez un dossier «[201-web-application--base de données sql](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)». Étant donné que de nombreuses applications personnalisées incluent une application web et la base de données SQL, ce modèle est utilisé comme exemple dans la suite de cet article pour vous aider à comprendre comment utiliser des modèles. Il est dépasse le cadre de cet article à bien expliquer tout ce modèle crée et configure, mais si vous prévoyez d’utiliser pour créer des environnements réels de votre organisation, vous souhaiterez comprenez en lisant l’article de la [mise en service une application web avec une base de données SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Remarque : cet article a été écrites pour la version de décembre 2015 du modèle [201-web-application--base de données sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . Les liens ci-dessous qui pointent sur un modèle et fichiers de paramètre à cette version du modèle.
2. Cliquez sur le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) dans le dossier 201-web-application--base de données sql pour afficher son contenu. C’est le fichier de modèle Azure le Gestionnaire de ressources. 
3. Dans le mode d’affichage, cliquez sur le bouton «[premières](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)». 
4. Avec la souris, sélectionner le contenu de ce fichier et les enregistrer sur votre ordinateur en tant que fichier nommé « TestApp1-Template.json. » 
5. Examinez le contenu du modèle et notez les éléments suivants :
 - Section de **ressources** : cette section définit les types de ressources Azure créés par ce modèle. Parmi les autres types de ressources, ce modèle crée des ressources [Azure Web App](app-service-web/app-service-web-overview.md) et [Base de données SQL Azure](sql-database/sql-database-technical-overview.md) . Si vous préférez exécuter et gérer les serveurs web et SQL dans des machines virtuelles, vous pouvez utiliser les modèles «[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)» ou «[feu-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)», mais les instructions fournies dans cet article sont basés sur le modèle [201-web-application--base de données sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - Section **paramètres** : cette section définit les paramètres de chaque ressource peut être configuré avec. Certains paramètres spécifiés dans le modèle ont des propriétés de « ValeurParDéfaut », tandis que d’autres personnes n’est pas le cas. Lorsque vous déployez Azure ressources avec un modèle, vous devez fournir des valeurs pour tous les paramètres qui n’ont pas de propriétés ValeurParDéfaut spécifiées dans le modèle.  Si vous ne fournissez pas de valeurs pour les paramètres des propriétés ValeurParDéfaut, la valeur spécifiée pour le paramètre ValeurParDéfaut dans le modèle est utilisée.

Un modèle définit les ressources Azure sont créées et les paramètres de chaque ressource peut être configuré avec. Vous pouvez en savoir plus sur les modèles et pour la création de votre propre en lisant l’article de [meilleures pratiques pour la conception de modèles de Azure le Gestionnaire de ressources](best-practices-resource-manager-design-templates.md) .

## <a name="download-and-customize-an-existing-parameter-file"></a>Télécharger et personnaliser un fichier de paramètre existant

Bien que vous souhaiterez probablement le *même* Azure ressources créées dans chaque environnement, vous souhaiterez probablement la configuration des ressources à être *différents* dans chaque environnement.  Il s’agit de fichiers paramètre interviennent. Créer des fichiers de paramètre qui contiennent des valeurs uniques dans chaque environnement en effectuant les étapes ci-dessous.   

1. Afficher le contenu du fichier [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) dans le dossier 201-web-application--base de données sql. Il s’agit du fichier de paramètres pour le fichier de modèle que vous avez enregistrés dans la section précédente. 
2. Dans le mode d’affichage, cliquez sur le bouton «[premières](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)». 
3. Avec la souris, sélectionner le contenu de ce fichier et les enregistrer dans trois fichiers distincts sur votre ordinateur avec les noms suivants :
 - TestApp1-paramètres-Development.json
 - TestApp1-paramètres-Test.json
 - TestApp1-paramètres-pré-Production.json

3. À l’aide d’un texte ou un éditeur de JSON, modifiez le fichier de paramètres d’environnement de développement que vous avez créé à l’étape 3, remplacer les valeurs indiquées à droite des valeurs de paramètre dans le fichier avec les *valeurs* indiquées à droite des **paramètres** ci-dessous : 
 - **nom du site**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *États-Unis centre*
 - **nom du serveur**: *testapp1devsrv*
 - **serverLocation**: *États-Unis centre*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Remplacer par votre mot de passe*
 - **databaseName**: *testapp1devdb*

4. À l’aide d’un texte ou un éditeur de JSON, modifier le fichier de paramètres d’environnement Test créée à l’étape 3, remplacer les valeurs répertoriées à droite des valeurs de paramètre dans le fichier avec les *valeurs* répertoriées à droite des **paramètres** ci-dessous :
 - **nom du site**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *États-Unis centre*
 - **nom du serveur**: *testapp1testsrv*
 - **serverLocation**: *États-Unis centre*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Remplacer par votre mot de passe*
 - **databaseName**: *testapp1testdb*

5. À l’aide d’un texte ou un éditeur de JSON, modifiez le fichier de paramètre de Production avant que vous avez créé à l’étape 3.  Remplacez tout le contenu du fichier quel est ci-dessous :

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

Dans le fichier de paramètres de Production Pre ci-dessus, les paramètres de **référence (SKU)** et **requestedServiceObjectiveName** ont été *ajoutées*, tandis qu’ils n’ont pas été ajoutés dans les fichiers de paramètres de développement et de Test. C’est parce que les valeurs par défaut spécifiés pour ces paramètres dans le modèle et dans les environnements de développement et de Test, les valeurs par défaut sont utilisées, mais dans la version de pré-mise Production valeurs d’environnement de ceux définis par défaut pour ces paramètres sont utilisés.

La raison pour laquelle les valeurs de ceux définis par défaut sont utilisées pour ces paramètres dans l’environnement de Production avant consiste à tester les valeurs de ces paramètres vous préférerez pour votre environnement de Production afin qu’ils peuvent également être testés.  Ces paramètres tous les mettre en relation Azure [plans d’hébergement Web App](https://azure.microsoft.com/pricing/details/app-service/), ou **référence (SKU)** et Azure [SQL de base de données](https://azure.microsoft.com/pricing/details/sql-database/)ou **requestedServiceObjectiveName** qui sont utilisés par l’application.  Références SKU autre et les noms de service objectif ont des coûts différents et des fonctionnalités et prend en charge des mesures au niveau de service différent.

Le tableau ci-dessous répertorie les valeurs par défaut pour ces paramètres spécifiés dans le modèle et les valeurs qui sont utilisés au lieu des valeurs par défaut dans le fichier de paramètres de Production antérieur.

| Paramètre | Valeur par défaut | Valeur de paramètre de fichier |
|---|---|---|
| **référence (SKU)** | Gratuit | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Créer des environnements
Toutes les ressources Azure doivent être créées au sein d’un [Groupe de ressources Azure](azure-resource-manager/resource-group-overview.md). Groupes de ressources permettent de regrouper des ressources Azure afin qu’ils peuvent être gérés collectivement.  [Autorisations](./active-directory/role-based-access-built-in-roles.md) peuvent être affectées aux groupes de ressources telles que des personnes spécifiques au sein de votre organisation peuvent créer, modifier, supprimer ou afficher, ainsi que les ressources qu’ils contiennent.  Alertes et des informations de facturation pour les ressources dans le groupe de ressources peuvent être affichés dans le [Portail Azure](https://portal.azure.com). Groupes de ressources sont créés dans une [région](https://azure.microsoft.com/regions/)Azure.  Dans cet article, toutes les ressources sont créées dans la région États-Unis centre. Lorsque vous commencez à créer des environnements réels, vous devez choisir la région qui correspond le mieux à vos besoins. 

Créer des groupes de ressources pour chaque environnement à l’aide d’une des méthodes suivantes.  Toutes les méthodes obtiendrez le même résultat.

###<a name="azure-command-line-interface-cli"></a>Azure Interface de ligne de commande (CLI)

Vérifiez que vous avez l’infrastructure du langage commun [installé](xplat-cli-install.md) sur des options un Windows, OS X, ou Linux ordinateur et que vous avez [connecté](xplat-cli-connect.md) votre [compte Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé un compte professionnel ou scolaire) à votre abonnement Azure. À partir de la ligne de commande infrastructure du langage commun, tapez la commande suivante pour créer le groupe de ressources pour l’environnement de développement.

    azure group create "TestApp1-Development" "Central US"

Si elle a réussi, la commande renverra les éléments suivants :

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Pour créer le groupe de ressources pour l’environnement de Test, tapez la commande suivante :

    azure group create "TestApp1-Test" "Central US"

Pour créer le groupe de ressources pour l’environnement de Production avant, tapez la commande suivante :

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Vérifiez que vous avez Azure PowerShell 1.01 ou version ultérieure installé sur un ordinateur Windows et avez connecté votre [compte Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé un compte professionnel ou scolaire) à votre abonnement comme indiqué dans l’article [comment installer et configurer Azure PowerShell](powershell-install-configure.md) . À partir d’une invite de commande PowerShell, tapez la commande suivante pour créer le groupe de ressources pour l’environnement de développement.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Si elle a réussi, la commande renverra les éléments suivants :

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Pour créer le groupe de ressources pour l’environnement de Test, tapez la commande suivante :

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Pour créer le groupe de ressources pour l’environnement de Production avant, tapez la commande suivante :

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé un professionnel ou scolaire). Cliquez sur Nouveau--Gestion--> groupe de ressources et entrez « TestApp1-développement » dans la zone de nom de groupe de ressources votre abonnement et sélectionnez « Nous centrale » dans la zone emplacement de groupe ressource comme le montre l’image ci-dessous.
   ![Portail](./media/solution-dev-test-environments/rgcreate.png)
2. Cliquez sur le bouton Créer pour créer le groupe de ressources.
3. Cliquez sur Parcourir, faites défiler la liste aux groupes de ressources et cliquez sur groupes de ressources, comme illustré ci-dessous.
   ![Portail](./media/solution-dev-test-environments/rgbrowse.png) 
4. Après avoir cliqué sur groupes de ressources, vous verrez la carte de groupes de ressources avec votre nouveau groupe de ressources.
   ![Portail](./media/solution-dev-test-environments/rgview.png)
5. Créer le Test TestApp1 et des groupes de ressources de TestApp1-pré-Production la même façon que vous avez créé le groupe de ressources de développement de TestApp1 ci-dessus.

##<a name="deploy-resources-to-environments"></a>Déployer des ressources dans les environnements

Déployer les ressources Azure aux groupes de ressources pour chaque environnement en utilisant le fichier de modèle pour la solution et les fichiers de paramètres pour chaque environnement à l’aide d’une des méthodes suivantes.  Ces deux méthodes obtiendrez le même résultat.

###<a name="azure-command-line-interface-cli"></a>Azure Interface de ligne de commande (CLI)

À partir de la ligne de commande infrastructure du langage commun, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources que vous avez créé pour l’environnement de développement remplaçant [path] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Lorsque vous voyez un message « En attente de déploiement Terminer » pour quelques minutes, la commande renvoie les éléments suivants si elle a réussi :

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Si la commande n’aboutit pas, résoudre des messages d’erreur et essayez à nouveau.  Problèmes courants utilisent des valeurs de paramètre qui ne respectent pas les contraintes d’appellation ressource Azure. Vous trouverez d’autres conseils de dépannage dans l’article [déploiements de groupe de ressources de résolution des problèmes dans Azure](./resource-manager-troubleshoot-deployments-cli.md) .

À partir de la ligne de commande infrastructure du langage commun, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources que pour l’environnement de Test, vous avez créé remplaçant [path] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

À partir de la ligne de commande infrastructure du langage commun, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources que vous avez créé pour l’environnement de Production avant remplaçant [path] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

À partir d’une invite de commandes Azure PowerShell (version 1,01 ou une version ultérieure), tapez la commande ci-dessous pour déployer les ressources au groupe de ressources que vous avez créé pour l’environnement de développement remplaçant [path] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Lorsque vous voyez un curseur clignote pendant quelques minutes, la commande renvoie les éléments suivants si elle a réussi :

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Si la commande n’aboutit pas, résoudre des messages d’erreur et essayez à nouveau.  Problèmes courants utilisent des valeurs de paramètre qui ne respectent pas les contraintes d’appellation ressource Azure. Vous trouverez d’autres conseils de dépannage dans l’article [déploiements de groupe de ressources de résolution des problèmes dans Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources que pour l’environnement de Test, vous avez créé remplaçant [path] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources que vous avez créé pour l’environnement de Production avant remplaçant [path] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Les fichiers de modèle et de paramètre peuvent être gérées et mise à jour avec le code de votre application dans un système de contrôle de source.  Vous pouvez également enregistrer les commandes ci-dessus pour les fichiers de script et enregistrez-les avec votre code également.

> [AZURE.NOTE] Vous pouvez déployer le modèle sur Azure directement en cliquant sur le bouton « Déployer sur Azure » dans l’article de la [mise en service une application Web avec une base de données SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) .  Cela peut s’avérer utile pour en savoir plus sur les modèles, mais cela ne pas vous permettent de modifier, de version et enregistrer vos valeurs de modèle et de paramètre avec votre code de l’application, afin que les détails supplémentaires sur cette méthode ne sont pas traitées dans cet article.

## <a name="maintain-environments"></a>Mettre à jour les environnements mixtes
Pendant le développement, la configuration de ressources Azure dans les différents environnements peut-être être modifiée incohérente intentionnellement ou accidentellement.  Cela peut entraîner la résolution des problèmes inutiles et résolution des problèmes pendant le cycle de développement d’applications.

1. Modifier les environnements en ouvrant le [portail Azure](https://portal.azure.com). 
2. Connectez-vous à celui-ci avec le même compte que vous permettant d’effectuer les étapes ci-dessus. 
3. Comme indiqué dans l’image ci-dessous, cliquez sur Parcourir--> groupes de ressources (vous devrez peut-être faire défiler vers le bas pour afficher les groupes de ressources).
   ![Portail](./media/solution-dev-test-environments/rgbrowse.png)
4. Après avoir cliqué sur groupes de ressources dans l’image ci-dessus, vous verrez la carte de groupes de ressources et les groupes de trois ressources que vous avez créé dans une étape précédente, comme le montre l’image ci-dessous. Cliquez sur le groupe de ressources TestApp1 développement et vous verrez la carte qui répertorie les ressources créés par le modèle dans le déploiement de groupe de ressources TestApp1 développement que vous avez effectuées dans une étape précédente.  Supprimez la ressource TestApp1DevApp Web App en cliquant sur TestApp1DevApp dans la carte de groupe de ressources de développement de TestApp1, puis en cliquant sur Supprimer dans la carte de l’application Web TestApp1DevApp.
   ![Portail](./media/solution-dev-test-environments/portal2.png)
5. Cliquez sur « Oui » lorsque le portail vous demande d’indiquer si vous êtes sûr de que vouloir supprimer la ressource. Fermer la carte de groupe de ressources de développement de TestApp1, ouvrant de nouveau seront affichera il sans l’application Web que vous venez de supprimer.  Le contenu du groupe de ressources est désormais différent de ce qu’ils doivent être. Vous pouvez tester davantage en supprimant plusieurs ressources à partir de plusieurs groupes de ressources ou même modifier les paramètres de configuration de certaines des ressources. Au lieu d’utiliser le portail Azure pour supprimer une ressource à partir d’un groupe de ressources, vous pouvez utiliser la commande PowerShell [AzureResource de supprimer](https://msdn.microsoft.com/library/azure/dn757676.aspx) ou de la commande « Supprimer ressource azure » depuis l’interface pour accomplir la même tâche ou.
6. Pour intégrer l’ensemble des ressources et configuration devant pour figurer dans le groupe de ressources à l’état qu’ils doivent être, redéployez les environnements aux groupes de ressources utilisant les mêmes commandes que vous avez utilisé dans la section [ressources de déploiement environnements](#deploy-resources-to-environments) , mais remplacez « Deployment1 » par « Deployment2 ».
7.  Comme indiqué dans la section Résumé de la cuillère TestApp1 développement dans l’image affichée à l’étape 4, vous verrez que l’application Web que vous avez supprimé dans le portail dans l’étape précédente existe à nouveau, comme toutes les autres ressources vous avez choisi à supprimer. Si vous avez modifié la configuration d’une des ressources, vous pouvez également vérifier qu’ils ont été définies revenir aux valeurs dans les fichiers de paramètres trop. Un des avantages du déploiement de vos environnements avec les modèles de gestionnaire de ressources Azure est que vous pouvez facilement déployez de nouveau les environnements revenir à un état connu à tout moment.
8. Si vous cliquez sur le texte sous « Déploiement » dans l’image ci-dessous, vous verrez une carte qui affiche l’historique de déploiement du groupe de ressources.  Dans la mesure où vous avez utilisé le nom « Deployment1 » pour le premier déploiement et « Deployment2 » pour le déploiement deuxième, vous aurez deux entrées.  Cliquez sur un déploiement pour afficher une carte qui affiche les résultats pour chaque déploiement.
  ![Portail](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Supprimer des environnements
Une fois que vous avez terminé l’utilisation d’un environnement, vous souhaiterez supprimez-le afin que vous ne subir frais d’utilisation des ressources Azure que vous n’utilisez plus.  La suppression des environnements est encore plus facile que de les créer.  Des étapes précédentes, les groupes de ressources Azure individuelles ont été créés pour chaque environnement et puis ressources ont été déployés dans les groupes de ressources. 

Supprimer les environnements à l’aide d’une des méthodes suivantes.  Toutes les méthodes obtiendrez le même résultat.

### <a name="azure-cli"></a>Azure infrastructure du langage commun

À partir d’une invite infrastructure du langage commun, tapez les informations suivantes :

    azure group delete "TestApp1-Development"

Lorsque vous y êtes invité, entrez y et appuyez sur ENTRÉE pour supprimer l’environnement de développement et toutes ses ressources. Après quelques minutes, la commande renvoie les éléments suivants :

    info:    group delete command OK

À partir d’une invite infrastructure du langage commun, tapez la commande suivante pour supprimer les environnements restants :

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

À partir d’une invite de commandes Azure PowerShell (version 1,01 ou une version ultérieure), tapez la commande suivante pour supprimer le groupe de ressources et tout son contenu.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Lorsque vous y êtes invité si vous êtes sûr que vous voulez supprimer le groupe de ressources, entrez y, suivi de la touche ENTRÉE.

Tapez la commande suivante pour supprimer les environnements restants :

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Portail Azure

1. Dans le portail Azure, accédez aux groupes de ressources que lors des étapes précédentes. 
2. Sélectionnez le groupe de ressources de développement de TestApp1, puis sur Supprimer dans la carte de groupe de ressources de développement de TestApp1. Une nouvelle carte s’affichent. Entrez le nom de groupe de ressources, puis cliquez sur le bouton Supprimer.
![Portail](./media/solution-dev-test-environments/rgdelete.png)
3. Supprimer le Test TestApp1 et des groupes de ressources de TestApp1-pré-Production la même façon que vous avez supprimé le groupe de ressources de développement de TestApp1.

Quelle que soit la méthode que vous utilisez, les groupes de ressources et toutes les ressources qu'ils contenus n’existe plus et vous serez n’est plus subir facturation dépenses pour les ressources.  

Pour réduire la Azure ressource l’utilisation des dépenses pendant le développement d’applications [Automation Azure](automation/automation-intro.md) permet de planifier des travaux qui :

- Arrêter de machines virtuelles à la fin de chaque jour et les redémarrer au début de chaque jour.
- Supprimer des environnements entiers à la fin de chaque jour et recréez-les au début de chaque jour.
 
À présent que vous avez rencontré il est facile pour créer, mettre à jour et supprimer de développement et tester les environnements, vous pouvez en savoir plus sur ce que vous avez simplement faire a davantage expérimenter les étapes ci-dessus et lire les références contenues dans cet article.

## <a name="next-steps"></a>Étapes suivantes

- [Déléguer le contrôle administratif](./active-directory/role-based-access-control-configure.md) à différentes ressources dans chaque environnement en affectant Microsoft Azure AD groupes ou des utilisateurs à des rôles spécifiques qui ont la possibilité d’effectuer un sous-ensemble des opérations sur les ressources Azure.
- [Affecter des indicateurs](resource-group-using-tags.md) aux groupes de ressources pour chaque environnement et/ou des ressources. Vous pouvez ajouter une balise « Environnement » à vos groupes de ressources et définissez sa valeur pour qu’elles correspondent aux noms de votre environnement. Balises peuvent être particulièrement utiles lorsque vous avez besoin organiser des ressources pour facturation ou la gestion.
- Surveiller les alertes et facturation pour les ressources de groupe de ressources dans le [portail Azure](https://portal.azure.com).
