<properties
    pageTitle="Utiliser un service web apprentissage automatique avec un modèle d’application web | Microsoft Azure"
    description="Utiliser un modèle d’application web dans Azure Marketplace pour utiliser un service web prédictive dans l’apprentissage automatique Azure."
    keywords="service Web, operationalization, API REST, machine apprentissage"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Utilisation d’un service web apprentissage automatique Azure avec un modèle d’application web

>[AZURE.NOTE] Cette rubrique décrit les techniques applicables à un service web classique. 

Une fois que vous avez développé votre modèle prédictive et déployé comme un service web Azure à l’aide de la Machine apprentissage Studio, ou à l’aide d’outils tels que R ou Python, vous pouvez accéder au modèle operationalized à l’aide d’une API REST.

Il existe plusieurs façons de consommer l’API REST et accéder au service web. Par exemple, vous pouvez écrire une application en c#, R ou Python à l’aide de l’exemple de code généré lorsque vous avez déployé le service web (disponible dans la Page aide de l’API dans le tableau de bord de service web dans Machine apprentissage Studio). Ou vous pouvez utiliser l’exemple de classeur Microsoft Excel créé à votre intention (également disponible dans le tableau de bord de service web dans Studio).

Mais le moyen le plus rapide et plus facile pour accéder à votre service web consiste à utiliser les modèles d’application Web disponibles sur le [Azure Marketplace des applications Web](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>L’ordinateur Azure apprentissage des modèles d’application Web

Les modèles d’application web disponibles sur le marché Azure peuvent créer une application web personnalisée capable de données d’entrée de votre service web et les résultats attendus. Il vous souhaitez est accorder l’accès de l’application web à votre service web et des données et le modèle fait le reste.

Deux modèles sont disponibles :

- [Modèle d’application Azure ML requête-réponse Service Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modèle d’application Azure ML lot l’exécution du Service Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Chaque modèle crée un exemple d’application ASP.NET, à l’aide de l’API URI et la clé pour votre service web et déploie comme un site web à Azure. Le modèle de réponse de demande de Service (enregistrements de ressource) crée une application web qui vous permet d’envoyer une seule ligne de données au service web pour obtenir un seul résultat. Le modèle de Service d’exécution par lots (BES) crée une application web qui vous permet d’envoyer le nombre de lignes de données pour obtenir les résultats multiples.

Aucun codage n’est requis pour utiliser ces modèles. Vous indiquez simplement l’API URI et la clé et le modèle crée l’application pour vous.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Comment utiliser le modèle de réponse de demande de Service (enregistrements de ressource)

Une fois que vous avez déployé votre service web, vous pouvez suivre les étapes ci-dessous pour utiliser le modèle d’application web enregistrements de ressource, comme indiqué dans le diagramme suivant.

![Procédure à suivre pour utiliser un modèle web enregistrements de ressources][image1]

1. Dans Machine apprentissage Studio, ouvrez l’onglet **Services Web** , puis le service web que vous souhaitez accéder. Copiez la clé répertoriée sous la **clé d’API** et enregistrez-le.

    ![Clé d’API][image3]

2. Ouvrez la Page aide de l’API de **Demande/réponse** . En haut de la page d’aide, sous **demander**, copiez la valeur **URI de demande** et enregistrez-le. Cette valeur ressemble à ceci :

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![URI de la demande][image4]

3. Accédez au [portail Azure](https://portal.azure.com), **connexion**, cliquez sur **Nouveau**, recherchez et sélectionnez **Azure ML réponse de demande de Service Web App**, puis cliquez sur **créer**. 

    - Donnez un nom unique à votre application web. L’URL de l’application web sera ce nom suivi `.azurewebsites.net.` par exemple,`http://carprediction.azurewebsites.net.`

    - Sélectionnez l’abonnement Azure et les services sous lequel s’exécute votre service web.

    - Cliquez sur **créer**.

    ![Créer dans le navigateur][image5]

4. Azure terminée déploiement de l’application web, cliquez sur l' **URL** dans la page de paramètres d’application web dans Azure ou entrez l’URL dans un navigateur web. Par exemple,`http://carprediction.azurewebsites.net.`

5. Lorsque l’application web s’exécute tout d’abord qu’il vous demandera **l’URL du billet API** et la **Clé de l’API**.
Entrez les valeurs que vous avez enregistré précédemment :
    - **Demande URI** à partir de la Page de l’aide de l’API de **l’URL du billet de l’API**
    - **Clé d’API** à partir du tableau de bord de service web pour la **Clé d’API**.

    Cliquez sur **Envoyer**.

    ![Entrez billet URI et clé d’API][image6]

6. L’application web affiche sa page de **Configuration d’application Web** avec les paramètres de service web actuels. Ici vous pouvez modifier les paramètres utilisés par l’application web.

    > [AZURE.NOTE] Modifier les paramètres ici les modifications uniquement pour cette application web. Il ne change pas les paramètres par défaut de votre service web. Par exemple, si vous modifiez la **Description** ici il ne modifiez la description qui s’affichée dans le tableau de bord de service web dans Machine apprentissage Studio.

    Lorsque vous avez terminé, cliquez sur **Enregistrer les modifications**, puis cliquez sur **Atteindre la Page d’accueil**.

7. Dans la page d’accueil, vous pouvez saisir les valeurs à envoyer à votre service web, cliquez sur **Envoyer**, et le résultat est renvoyé.

Si vous souhaitez revenir à la page de **Configuration** , accédez à la `setting.aspx` page de l’application web. Par exemple : `http://carprediction.azurewebsites.net/setting.aspx.` vous devrez entrer à nouveau la clé de l’API : vous avez besoin pour accéder à la page et mettre à jour les paramètres.

Vous pouvez arrêter, redémarrez ou supprimer l’application web dans le portail Azure comme une autre application web. Dans la mesure où elle s’exécute, vous pouvez accéder à l’adresse web accueil et entrez de nouvelles valeurs.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Comment utiliser le modèle de Service d’exécution par lots (BES)

Vous pouvez utiliser le modèle d’application web BES de la même façon que le modèle d’enregistrements de ressources, sauf que l’application web créé vous permettra d’envoyer plusieurs lignes de données et de recevoir plusieurs résultats.

Les résultats d’un service web de l’exécution du lot sont stockés dans un conteneur de stockage Azure ; les valeurs d’entrée peuvent provenir de stockage Azure ou un fichier local.
Par conséquent, vous devez un conteneur de stockage Azure pour contenir les résultats renvoyés par l’application web, et vous devez préparer vos données d’entrée.

![Procédure à suivre pour utiliser un modèle web BES][image2]

1. Suivez la procédure pour créer l’application web BES en ce qui concerne le modèle d’enregistrements, à l’exception :
    - Obtenir l' **URI demander** à partir de la Page aide de l’API de **L’exécution du lot** pour le service web.
    - Accédez au [Modèle d’application Azure ML lot de l’exécution du Service Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) pour ouvrir le modèle BES dans Azure Marketplace et cliquez sur **Créer dans le navigateur**.

2. Pour indiquer l’endroit où vous souhaitez que les résultats stockées, entrez les informations de conteneur de destination dans la page d’accueil de l’application web. Également indiquer l’endroit où l’application web accessible les valeurs d’entrée, soit dans un fichier local ou un conteneur de stockage Azure.
Cliquez sur **Envoyer**.

    ![Informations sur le stockage][image7]

L’application web affiche une page avec l’état de la tâche.
Lorsque la tâche est terminée, vous aurez l’emplacement des résultats dans le stockage blob Azure. Vous avez également la possibilité de télécharger les résultats dans un fichier local.

## <a name="for-more-information"></a>Pour plus d’informations

Pour en savoir plus sur...

- Création d’une expérience d’apprentissage automatique avec Machine d’apprentissage Studio, consultez [créer votre première expérience dans Azure Machine d’apprentissage Studio](machine-learning-create-experiment.md)

- comment déployer votre apprentissage automatique expérimenter comme un service web, voir [déployer un service web d’apprentissage Machine Azure](machine-learning-publish-a-machine-learning-web-service.md)

- autres méthodes pour accéder à votre service web, voir [comment utiliser un service web d’apprentissage automatique Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
