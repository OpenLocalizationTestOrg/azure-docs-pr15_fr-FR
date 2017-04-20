<properties 
    pageTitle="Utiliser Azure d’apprentissage automatique paramètres de Service Web | Microsoft Azure" 
    description="Comment utiliser les paramètres de Service Web Azure Machine apprentissage pour modifier le comportement de votre modèle lorsque vous accédez au service web." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>Utiliser Azure d’apprentissage automatique paramètres de Service Web

Un service web d’apprentissage automatique Azure est créé par une expérience qui contient des modules avec configurables paramètres de publication. Dans certains cas, vous souhaitiez modifier le comportement de module pendant l’exécution du service web. *Paramètres de Service Web* permettent d’effectuer cette tâche. 

Un exemple courant est la configuration d' [Importer des données] [ reader] module afin que l’utilisateur du service web publié peut spécifier une autre source de données lors de l’accès au service web. Ou en configurant les [Exporter les données] [ writer] module afin que vous pouvez spécifier une destination différente. D’autres exemples incluent la modification du nombre de bits de la [Fonctionnalité hachage] [ feature-hashing] module ou le nombre de fonctionnalités désirées pour la [sélection de fonctionnalité basée sur le filtre] [ filter-based-feature-selection] module. 

Vous pouvez définir des paramètres de Service Web et associez-les à un ou plusieurs paramètres module dans votre expérience, et vous pouvez spécifier si elles sont obligatoires ou facultatives. L’utilisateur du service web peut ensuite fournir des valeurs pour ces paramètres lorsqu’ils appellent le service web. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>Comment configurer et utiliser des paramètres de Service Web

Vous définissez un paramètre de Service Web en cliquant sur l’icône en regard du paramètre d’un module et en sélectionnant « Définir en tant que paramètre du service web ». Cela crée un nouveau paramètre du Service Web et connecte à ce paramètre de module. Puis, lorsque le service web est accessible, l’utilisateur peut spécifier une valeur pour le paramètre du Service Web et il est appliqué au paramètre module.

Une fois que vous définissez un paramètre de Service Web, il est disponible pour tout autre paramètre module à l’expérience. Si vous définissez un paramètre de Service Web associé à un paramètre pour un module, vous pouvez utiliser ce paramètre du Service Web même pour tout autre module, dans la mesure où le paramètre attend le même type de valeur. Par exemple, si le paramètre du Service Web est une valeur numérique, puis il peut uniquement être utilisé pour les paramètres de module qu’attendez une valeur numérique. Lorsque l’utilisateur définit une valeur pour le paramètre du Service Web, elle est appliquée à tous les paramètres de module associé.

Vous pouvez décider s’il faut fournir une valeur par défaut pour le paramètre de Service Web. Dans ce cas, le paramètre est facultatif pour l’utilisateur du service web. Si vous ne fournissez pas une valeur par défaut, l’utilisateur est nécessaire pour entrer une valeur lorsque le service web est accessible.

La documentation de l’API de service web inclut des informations pour l’utilisateur du service web sur la façon de spécifier le paramètre de Service Web par programme lors de l’accès au service web.

>[AZURE.NOTE] La documentation de l’API d’un service web classique est fournie via le lien de **page d’aide de l’API** dans le **tableau de bord** dans Machine apprentissage Studio du service web. La documentation de l’API pour un nouveau service web est fournie via le portail [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) dans les pages **consommer** et **API Swagger** pour votre service web.


##<a name="example"></a>Exemple

Par exemple, supposons que nous avons une expérience avec un [Exporter les données] [ writer] module qui envoie des informations au stockage d’objets blob Azure. Nous allons définir un paramètre de Service Web nommé « Blob chemin d’accès » qui permet à l’utilisateur de service web changer le chemin d’accès au stockage blob lorsque le service est accessible.

1.  Machine apprentissage Studio, cliquez sur [Exporter les données] [ writer] module pour le sélectionner. Ses propriétés sont affichées dans le volet de propriétés à droite de la zone de dessin expérience.

2.  Spécifier le type de stockage :

    - Sous **Spécifiez destination de données**, sélectionnez « Stockage d’objets Blob Azure ».
    - Sous **Spécifiez le type d’authentification**, sélectionnez « Compte ».
    - Entrez les informations de compte pour le stockage blob Azure. 
    <p />

3.  Cliquez sur l’icône à droite du **chemin d’accès au blob commençant par le paramètre de conteneur**. Il ressemble à ceci :

    ![Icône de paramètre du Service Web][icon]

    Sélectionnez « Définir en tant que paramètre du service web ».

    Une entrée est ajoutée sous l’onglet **Paramètres de Service Web** en bas du volet de propriétés avec le nom « Chemin d’accès au blob commençant par conteneur ». Il s’agit du paramètre du Service Web qui est désormais associé à ce [Exporter les données] [ writer] paramètre module.

4.  Pour renommer le paramètre du Service Web, cliquez sur le nom, entrez « Blob chemin d’accès », puis appuyez sur **entrée** . 
 
5.  Pour fournir une valeur par défaut pour le paramètre du Service Web, cliquez sur l’icône à droite du nom, sélectionnez « Fournir la valeur par défaut », entrez une valeur (par exemple, « container1/output1.csv ») et appuyez sur **entrée** .

    ![Paramètre du Service Web][parameter]

6.  Cliquez sur **exécuter**. 

7.  Cliquez sur **Déployer un Service Web** et sélectionnez **Déployer du Service Web [classique]** ou **Déployer du Service Web [nouveau]** pour déployer le service web.

L’utilisateur du service web pouvez à présent spécifier une nouvelle destination pour les [Exporter les données] [ writer] module lors de l’accès au service web.

##<a name="more-information"></a>Plus d’informations

Pour obtenir un exemple plus détaillé, consultez l’entrée de [Paramètres de Service Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) dans le [Blog de formation Machine](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Pour plus d’informations sur l’accès à un service web Machine d’apprentissage, voir [comment utiliser une machine publiée service web de formation](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
