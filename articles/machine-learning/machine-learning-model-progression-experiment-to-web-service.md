<properties
    pageTitle="Comment un modèle d’apprentissage automatique progresse à partir d’une expérience d’un service Web operationalized | Microsoft Azure"
    description="Vue d’ensemble des mécanismes de la façon dont votre progresse de modèle d’apprentissage automatique Azure à partir d’un développement essayer auprès d’un service Web operationalized."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Comment un modèle d’apprentissage automatique progresse à partir d’une expérience d’un service Web operationalized

Azure Machine apprentissage Studio fournit une zone de dessin interactive qui vous permet de développer, exécuter, tester et parcourir un ***expérimenter*** représentant un modèle d’analyse prédictive. Il existe un large éventail de modules peut :

* Données d’entrée dans votre expérience
* Manipuler les données
* Former un modèle à l’aide des algorithmes d’apprentissage automatique
* Score du modèle
* Évaluer les résultats
* Valeurs finales de sortie

Une fois que vous êtes satisfait de votre expérience, vous pouvez le déployer comme un ***service Web de formation classique Azure ordinateur*** ou un ***service Web de formation nouveau Azure Machine*** afin que les utilisateurs peuvent envoyer de nouvelles données et recevoir les résultats.

Dans cet article, nous donnent une vue d’ensemble des mécanismes de la façon dont votre progresse de modèle d’apprentissage automatique à partir d’un développement essayer auprès d’un service Web operationalized.

>[AZURE.NOTE] Il existe d’autres façons de développer et déployer des modèles de formation des machines, mais cet article se concentre sur l’utilisation de Machine d’apprentissage Studio. Pour plus d’informations créer un service Web prédictif classique avec R, voir le billet de blog [créer et déployer prédictive Web applications à l’aide de RStudio et Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Tandis que Azure Machine apprentissage Studio est conçu pour vous aider à développer et déployer un *modèle d’analyse prédictive*, il est possible d’utiliser Studio pour développer une expérience qui n’inclut pas un modèle d’analyse prédictive. Par exemple, une expérience peut-être entrer des données, manipuler et puis générer les résultats. Comme une expérience d’analyse prédictive, vous pouvez déployer cette expérience non prédictive comme un service Web, mais il est un processus simple, car l’expérience n’est pas formation ou notation un modèle d’apprentissage automatique. S’il n’est pas classique à utiliser Studio de cette façon, nous allons l’inclure dans la discussion afin que nous pouvons donner une explication complète du fonctionne de Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Développer et déployer un service Web prédictif

Voici les étapes qui une solution classique suit lorsque vous développez et déployez à l’aide de la Machine apprentissage Studio :

![Flux de déploiement](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figure 1 : étapes d’un modèle d’analyse prédictive courant*

### <a name="the-training-experiment"></a>L’expérience de formation

***formation essayer*** est la phase initiale de développement de votre service Web dans Machine apprentissage Studio. L’objectif de l’expérience de formation consiste à vous offre un emplacement pour développer, tester, une itération et finalement former une machine apprentissage du modèle. Vous pouvez même former plusieurs modèles simultanément que vous recherchez la meilleure solution, mais une fois que vous avez terminé essayant vous permet de sélectionner un seul formé graphiquement et éliminer le reste de l’expérience. Pour obtenir un exemple de développement d’une analyse prédictive expérimenter, voir [Création d’une solution analytique prédictive pour l’analyse de risque de crédit dans l’apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>L’expérience prédictive

Une fois que vous avez un modèle formé dans votre expérience de formation, cliquez sur **Définir le serviceWeb** , puis sélectionnez **Service Web prédictive** dans Machine apprentissage Studio pour lancer le processus de conversion de votre expérience de formation pour une ***expérience prédictive***. L’objectif de l’expérience prédictive consiste à utiliser votre modèle formé pour évaluer les nouvelles données, dans le but de devenir finalement operationalized comme un service Web Azure.

Cette conversion est effectuée pour les étapes suivantes :

-   Convertir l’ensemble des modules utilisés pour la formation dans un module unique et l’enregistrer comme modèle formé

-   Éliminer les modules superflus ne pas liés aux scores

-   Ajouter des ports d’entrée et de sortie utilisées par le service Web éventuelle

Il peut y avoir plusieurs modifications que vous souhaitez apporter à préparer votre expérience prédictive déployer comme un service Web. Par exemple, si vous souhaitez que le service Web uniquement un sous-ensemble des résultats de sortie, vous pouvez ajouter un module filtrage avant le port de sortie.

Dans ce processus de conversion, l’expérience de formation n’est pas supprimé. Lorsque le processus est terminé, vous avez deux onglets dans Studio : une pour l’expérience de formation et l’autre pour l’expérience prédictive. Ainsi, vous pouvez apporter des modifications à l’expérience de formation avant de déployer votre service Web et reconstruire l’expérience prédictive. Ou vous pouvez enregistrer une copie de l’expérience de formation pour commencer une autre ligne d’expérience.

>[AZURE.NOTE] Lorsque vous cliquez sur **Service Web prédictive** vous démarrez un processus automatique pour convertir votre expérience de formation pour une expérience prédictive et cela fonctionne bien dans la plupart des cas. Si votre expérience de formation est complexe (par exemple, si vous avez plusieurs chemins d’accès pour la formation qui vous joindre), vous souhaiterez effectuer cette conversion manuellement. Pour plus d’informations, voir [convertir une expérience de formation apprentissage automatique pour une expérience prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### <a name="the-web-service"></a>Le service Web

Une fois que vous êtes satisfait que votre expérience prédictive est prêt, vous pouvez déployer votre service comme soit un service Web classique ou un service Web nouveau basé sur le Gestionnaire de ressources Azure. Pour mettre votre modèle en déployant comme un *service Web de formation Machine classique*, cliquez sur **Déployer un Service Web** et sélectionnez **Déployer du Service Web [classique]**. Pour déployer en tant que *service Web de formation nouvel ordinateur*, cliquez sur **Déployer un Service Web** et sélectionnez **Déployer du Service Web [nouveau]**. Les utilisateurs peuvent maintenant envoyer des données à votre modèle à l’aide du service Web API REST et recevoir précédent les résultats. Pour plus d’informations, voir [comment consommer un service Web de formation Azure Machine qui a été déployé à partir d’un ordinateur expérience d’apprentissage](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Le cas non classique : création d’un service Web non prédictive

Si votre expérience ne pas former un modèle d’analyse prédictive, puis que vous n’avez pas besoin pour créer une expérience de formation et une expérience score - il y a une expérience, et vous pouvez le déployer comme un service Web. Machine apprentissage Studio détecte si votre expérience contient un modèle de prévision en analysant les modules que vous avez déjà utilisé.

Une fois que vous avez itération relative à votre expérience et que vous êtes satisfait :

1.  Cliquez sur **Définir le serviceWeb** et sélectionnez **Reconversion un Service Web** - nœuds d’entrée et de sortie sont automatiquement ajoutés

2.  Cliquez sur **exécuter**

3. Cliquez sur **Déployer un Service Web** et sélectionnez **Déployer du Service Web [classique]** ou un **Service Web déployer [nouveau]** selon l’environnement auquel vous voulez déployer.

Votre service Web est désormais déployée et vous pouvez accéder et gérer comme un service Web prédictif.

## <a name="updating-your-web-service"></a>Mise à jour de votre service Web

Maintenant que vous avez déployé votre expérience comme un service Web, que se passe-t-il si vous devez mettre à jour ?

Cela dépend de ce que vous devez mettre à jour :

**Vous souhaitez modifier les entrées ou sorties, ou que vous souhaitez modifier la façon dont le service Web manipule des données**

Si vous n’êtes pas modifier le modèle, mais changez simplement la façon dont le service Web gère les données, vous pouvez modifier l’expérience prédictive et cliquez sur **Déployer un Service Web** puis sélectionnez à nouveau **ServiceWeb déployer [classique]** ou un **Service Web déployer [nouveau]** . Le service Web est arrêté, l’expérience prédictive mis à jour est déployée et redémarrage du service Web.

Voici un exemple : supposons que votre expérience prédictive renvoie toute la ligne de données d’entrée avec le résultat prévue. Vous pouvez décider que vous souhaitez que le service Web simplement retourner le résultat. Ainsi, vous pouvez ajouter un module de **Colonnes de projet** à l’expérience prédictive, juste avant le port de sortie, pour exclure les colonnes autre que le résultat. Lorsque vous cliquez sur **Déployer un Service Web** et sélectionnez à nouveau **ServiceWeb déployer [classique]** ou un **Service Web déployer [nouveau]** , le service Web est mis à jour.

**Vous souhaitez former le modèle avec les nouvelles données**

Si vous souhaitez conserver votre apprentissage modèle de l’ordinateur, mais que vous voulez former avec de nouvelles données, vous avez deux possibilités :

1.  **Former le modèle pendant l’exécution du service Web** - si vous souhaitez former votre modèle lors de la prévision service Web est en cours d’exécution, ce faire, vous pouvez apporter quelques modifications à l’expérience de formation pour le convertir en une ***expérience reconversion***, puis vous pouvez le déployer comme un **service *web reconversion* **. Pour obtenir des instructions sur la façon de procéder, voir [former Machine apprentissage modèles par programme](machine-learning-retrain-models-programmatically.md).

2.  **Revenez à l’expérience de formation d’origine et utiliser les données de formation différentes pour développer votre modèle** - votre expérience prédictive est lié au service Web, mais l’expérience de formation n’est pas directement lié de cette manière. Si vous modifiez l’expérience de formation d’origine et cliquez sur **Définir le serviceWeb**, il crée un *Nouveau* prédictive expérimenter qui, lors du déploiement, crée un *Nouveau* service Web. Il ne simplement mettre à jour le service Web d’origine.

    Si vous avez besoin modifier l’expérience de formation, ouvrez-le et cliquez sur **Enregistrer sous** pour faire une copie. Cela toucher l’expérience de formation d’origine, vous exercer prédictive et service Web. Vous pouvez désormais créer un nouveau service Web avec vos modifications. Une fois que vous avez déployé le nouveau service Web, que vous pouvez ensuite décider si vous souhaitez arrêter le service Web précédent ou afin qu’il fonctionne avec une nouvelle.

**Vous voulez exercer un autre modèle**

Si vous souhaitez apporter des modifications à votre expérience prédictive d’origine, telles que la sélection d’un algorithme d’apprentissage autre ordinateur, essayez d’une méthode de formation différents, etc., puis vous devez suivre la deuxième procédure décrite ci-dessus pour former votre modèle : Ouvrez l’expérience de formation, cliquez sur **Enregistrer sous** pour faire une copie, puis démarrez le nouveau chemin d’accès de développement de votre modèle , création de l’expérience prédictive et déploiement du service web. Cela créera un nouveau site Web service soient ou non à celui d’origine - vous pouvez choisir celui ou les deux, continue de s’exécuter.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processus de développement et expérience, voir les articles suivants :

-   conversion de l’expérience - [convertir une expérience de formation apprentissage automatique pour une expérience prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   déploiement du service Web - [déployer un service web d’apprentissage automatique Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   reconversion du modèle - [former Machine apprentissage modèles par programme](machine-learning-retrain-models-programmatically.md)

Pour obtenir des exemples de l’ensemble du processus, voir :

-   [Didacticiel d’apprentissage machine : créer votre première expérience dans Azure Machine d’apprentissage Studio](machine-learning-create-experiment.md)

-   [Procédure pas à pas : Développer une solution analytique prédictive pour l’analyse de risque de crédit dans l’apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md)