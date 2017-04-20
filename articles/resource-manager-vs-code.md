<properties
   pageTitle="Utilisation du Code VS avec le Gestionnaire de ressources modèles | Microsoft Azure"
   description="Montre comment configurer la Code Visual Studio pour créer des modèles Azure le Gestionnaire de ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Utilisation des modèles Azure du Gestionnaire de ressources dans le Code de Visual Studio

Azure modèles Gestionnaire de ressources sont des fichiers JSON qui décrivent une ressource et les dépendances connexes. Ces fichiers peuvent parfois être grandes et complexe prise en charge des outils est important. Code de Visual Studio est un éditeur de code nouveau, léger, open source et disponibilité sur plusieurs plateformes. Il prend en charge la création et modification des modèles de gestionnaire de ressources via une [nouvelle extension](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Code VS s’exécute partout et ne nécessite pas accès à Internet, sauf si vous souhaitez également déployer des modèles de votre gestionnaire de ressources.

Si vous n’avez pas déjà VS Code, vous pouvez l’installer en [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Installer l’extension du Gestionnaire de ressources

Pour travailler avec les modèles JSON dans Code VS, vous devez installer une extension. Les étapes suivantes téléchargement et installez la prise en charge pour les modèles de JSON Gestionnaire de ressources :

1. Barre de lancement VS Code 
2. Ouvrir rapide (Ctrl + P) 
3. Exécutez la commande suivante : 

        ext install azurerm-vscode-tools

4. Redémarrez VS Code lorsque vous êtes invité à activer l’extension. 

 Travail terminé !

## <a name="set-up-resource-manager-snippets"></a>Configurer le Gestionnaire de ressources extraits

Les étapes précédentes installé la prise en charge des outils, mais nous devons maintenant configurer Code VS pour utiliser des extraits de modèle JSON.

1. Copiez le contenu du fichier à partir du référentiel [azure-xplat-processeur-outils](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) dans votre Presse-papiers.
2. Barre de lancement VS Code 
3. Dans le Code VS, vous pouvez ouvrir le fichier d’extraits JSON en naviguant vers le **fichier** -> **Préférences** -> **Utilisateur extraits** -> **JSON**, ou en sélectionnant **F1** et en tapant **Préférences** jusqu'à ce que vous pouvez sélectionner **Préférences : extraits**.

    ![extraits de préférence](./media/resource-manager-vs-code/preferences-snippets.png)

    Parmi les options, sélectionnez **JSON**.

    ![Sélectionnez json](./media/resource-manager-vs-code/select-json.png)

4. Coller le contenu du fichier à l’étape 1 dans votre fichier extraits utilisateur devant le final «} » 
5. Vérifiez que le JSON ressemble OK et il n’existe aucune soulignements ondulés n’importe où. 
6. Enregistrez et fermez le fichier extraits utilisateur.

C’est tout ce qui est nécessaire pour commencer à utiliser les Gestionnaire de ressources extraits. Ensuite, nous allons présenter ce programme d’installation pour le test.

## <a name="work-with-template-in-vs-code"></a>Utiliser le modèle dans le Code VS

Pour commencer à travailler avec un modèle, le plus simple consiste à utiliser votre propre ou sélectionnez un des rapide démarrer modèles disponibles sur [Github](https://github.com/Azure/azure-quickstart-templates) . Vous pouvez facilement [Exporter un modèle](resource-manager-export-template.md) pour un de vos groupes de ressources via le portail. 

1. Si vous avez exporté un modèle à partir d’un groupe de ressources, ouvrez les fichiers extraits de Code VS.

    ![afficher les fichiers](./media/resource-manager-vs-code/show-files.png)

2. Ouvrez le fichier template.json afin que vous pouvez modifier et ajouter des ressources supplémentaires. Après le **« ressources » : [** appuyez sur ENTRÉE pour commencer une nouvelle ligne. Si vous tapez **processeur**, s’affiche avec une liste d’options. Ces options sont les extraits de modèle que vous avez installé. Il doit ressembler à ceci : 

    ![afficher des extraits de code](./media/resource-manager-vs-code/type-snippets.png)

3. Sélectionnez l’extrait de code que vous le souhaitez. Pour cet article, je suis en choisissant **processeur ip** pour créer une nouvelle adresse IP publique. Placez une virgule après le crochet fermant «} » de la ressource nouvellement créée pour vous assurer que votre modèle de syntaxe est valide.

     ![Ajouter une virgule](./media/resource-manager-vs-code/add-comma.png)

4. Code VS a IntelliSense intégré. Lorsque vous modifiez vos modèles, Code VS suggère des valeurs disponibles. Par exemple, pour ajouter une section de variables à votre modèle, ajoutez **» «** (deux guillemets), puis sélectionnez **Ctrl + Espace** entre les guillemets. S’affiche avec les options, y compris les **variables**.

    ![ajouter des variables](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense peut également suggérer des valeurs disponibles ou des fonctions. Pour définir une propriété sur une valeur de paramètre, créer une expression avec **« [] »** et **Ctrl + Espace**. Vous pouvez commencer à taper le nom d’une fonction. Sélectionnez **l’onglet** lorsque vous avez trouvé la fonction souhaitée.

    ![Ajouter un paramètre](./media/resource-manager-vs-code/select-parameters.png)

6. Sélectionnez à nouveau **Ctrl + Espace** au sein de la fonction pour afficher la liste des paramètres disponibles dans votre modèle.

    ![Ajouter un paramètre](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Si vous rencontrez des problèmes de validation de schéma dans votre modèle, vous verrez les soulignements ondulés familiers dans l’éditeur. Vous pouvez afficher la liste des erreurs et les avertissements en tapant **Ctrl + Maj + M** ou en sélectionnant les glyphes dans la barre d’état gauche inférieure.

    ![erreurs](./media/resource-manager-vs-code/errors.png)

    Validation de votre modèle peut vous aider à détecter les problèmes de syntaxe ; Toutefois, vous pouvez également voir des erreurs que vous pouvez ignorer. Dans certains cas, l’éditeur est comparaison de votre modèle à un schéma qui n’est pas à jour et par conséquent signale une erreur même si vous savez qu’elle est correcte. Par exemple, supposons une fonction a été récemment ajoutée au Gestionnaire de ressources, mais le schéma n’a pas été mis à jour. L’éditeur signale une erreur malgré le fait que la fonction fonctionne correctement pendant le déploiement.

    ![message d’erreur](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Déployer vos nouvelles ressources

Lorsque votre modèle est prêt, vous pouvez déployer les nouvelles ressources suivant les instructions ci-dessous : 

### <a name="windows"></a>Windows

1. Ouvrez une invite de commande PowerShell 
2. Type de connexion : 

        Login-AzureRmAccount 

3. Si vous avez plusieurs abonnements, obtenir une liste des abonnements avec :

        Get-AzureRmSubscription

    Puis sélectionnez l’abonnement à utiliser.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Mettre à jour les paramètres de votre fichier parameters.json
5. Exécuter le Deploy.ps1 pour déployer votre modèle dans Azure

### <a name="osxlinux"></a>OSX/Linux

1. Ouvrir une fenêtre terminal 
2. Type de connexion :

        azure login 

3. Si vous avez plusieurs abonnements, sélectionnez l’abonnement avec :

        azure account set <subscriptionNameOrId> 

4. Mettre à jour les paramètres dans le fichier parameters.json.
5. Pour déployer le modèle, exécutez :

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md).
- Pour en savoir plus sur les fonctions de modèle, voir [fonctions de modèle Azure le Gestionnaire de ressources](resource-group-template-functions.md).
- Pour plus d’exemples d’utilisation de Code de Visual Studio, voir [créer des applications de cloud avec Code Visual Studio](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) à partir de la connexion [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour des Démarrages rapides supplémentaires à partir de la démonstration HealthClinic.biz, consultez [Azure développeur outils Démarrages rapides](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
