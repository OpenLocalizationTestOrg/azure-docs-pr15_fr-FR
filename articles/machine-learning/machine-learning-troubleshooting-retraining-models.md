<properties
    pageTitle="Résolution des problèmes de la Retraining d’un service Web classique Azure Machine formation | Microsoft Azure"
    description="Identifier et corriger rencontré problèmes courants lorsque vous soyez reconversion du modèle d’un Service Web de Azure Machine apprentissage."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Résolution des problèmes de la reconversion d’un service Web classique Azure Machine formation

## <a name="retraining-overview"></a>Vue d’ensemble reconversion

Lorsque vous déployez une expérience prédictive comme un service web score il s’agit d’un modèle statique. Nouvelles données devient disponibles ou lorsque le client de l’API a leurs propres données, le modèle doit être s. 

Pour une procédure pas à pas complète du processus de reconversion d’un service Web classique, consultez [Former Machine apprentissage modèles par programme](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>Processus de reconversion

Lorsque vous avez besoin former le service Web, vous devez ajouter quelques éléments supplémentaires :

* Un service Web déployé à partir de l’expérience de formation. L’expérience doit avoir un module de **Sortie du Service Web** attaché à la sortie du module **Train modèle** .  

    ![Joindre la sortie du service web au modèle de train.][image1]

* Un point de terminaison ajouté à votre service Web score.  Vous pouvez ajouter le point de terminaison par programme à l’aide de l’exemple de code référencée dans les modèles de former les apprentissage automatique par programme rubrique ou via le portail classique Azure.

Vous pouvez utiliser ensuite le code exemple c# à partir de la page de l’aide de l’API du Service Web de formation pour former le modèle. Une fois que vous avez évalué les résultats et que vous conviennent, vous mettez à jour le modèle formé notation de service web en utilisant le nouveau point de terminaison que vous avez ajouté.

Avec tous les éléments en place, les principales étapes à suivre pour former le modèle sont les suivantes :

1.  Appeler le Service Web de formation : l’appel est au lot de l’exécution de Service (BES), pas la demande de réponse Service (enregistrements de ressource). Vous pouvez utiliser le code c# exemple sur la page de l’aide de l’API pour passer l’appel. 
2.  Recherchez les valeurs pour les *BaseLocation*, *RelativeLocation*et *SasBlobToken*: ces valeurs sont renvoyées dans les résultats de votre appel du service Web de formation. 
      ![Affiche le résultat de l’échantillon reconversion et les valeurs BaseLocation, RelativeLocation et SasBlobToken.][image6]
3.  Mettre à jour le point de terminaison ajouté à partir du service web score avec le nouveau modèle formé : à l’aide de l’exemple de code fourni dans les modèles de former les apprentissage automatique par programme, mettre à jour le nouveau point de terminaison vous ajoutés au modèle de score avec le modèle que vous venez formé à partir du Service Web de formation.

## <a name="common-obstacles"></a>Obstacles courants

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Vérifier si vous avez l’URL exacte correctif

L’URL correctif que vous utilisez doit être celle associée à la nouvelle extrémité score que vous avez ajouté au service Web score. Il existe un certain nombre de méthodes pour obtenir l’URL correctif :

**Option 1 : par programmation**

Pour obtenir l’URL correctif correcte :

1.  Exécuter l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2.  À partir du résultat de AddEndpoint, rechercher la valeur *HelpLocation* et copiez l’URL.

    ![HelpLocation dans le résultat de l’échantillon addEndpoint.][image2]

3.  Collez l’URL dans un navigateur pour accéder à une page qui fournit des liens d’aide pour le service Web.
4.  Cliquez sur le lien de **Mise à jour de ressources** pour ouvrir la page aide correctif.

**Option 2 : Utiliser le portail classique Azure**

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2.  Ouvrez l’onglet apprentissage automatique. 
     ![Onglet incliné machine.][image4]
3.  Cliquez sur le nom de votre espace de travail, puis les **Services Web**.
4.  Cliquez sur le service Web score que vous travaillez. (Si vous n’avez pas modifié le nom par défaut du service web, elle se termine par [score Exp.].)
5.  Cliquez sur **Ajouter un point de terminaison**.
6.  Après que le point de terminaison est ajoutée, cliquez sur le nom du point de terminaison. Cliquez ensuite sur **Mise à jour de ressources** pour ouvrir la page aide correction.

>[AZURE.NOTE] Si vous avez ajouté le point de terminaison du service Web de formation au lieu du Service Web prédictive, vous recevrez l’erreur suivante lorsque vous cliquez sur le lien de **Mise à jour de ressources** : nous sommes désolés, mais cette fonctionnalité n’est pas prise en charge ou disponible dans ce contexte. Ce Service Web a pas être mise à jour de ressources. Nous excusons pour les désagréments et que vous travaillez sur l’amélioration de ce flux de travail.

![Nouveau tableau de bord de point de terminaison.][image3]

La page d’aide correctif contient l’URL correctif, vous devez utiliser et fournit des exemples de code que vous pouvez utiliser pour appeler.

![Correctif URL.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Vérifiez que vous mettez à jour le point de terminaison score correct

* Correctif pas le Service Web de formation : l’opération correctif doit être effectuée sur le service Web score.
* Correctif pas le point de terminaison par défaut sur service Web : l’opération correctif doit être effectuée sur la nouvelle notation service point de terminaison Web que vous avez ajouté.

Vous pouvez vérifier le service Web auquel le point de terminaison est activé en visitant le portail classique Azure. 

>[AZURE.NOTE] N’oubliez pas que vous ajoutez le point de terminaison du service Web prédictive, pas le Service Web de formation. Si vous avez correctement déployé une formation et un Service Web prédictive, normalement deux services Web distinctes répertoriés. Le Service Web prédictive doit se terminer par « [exp prédictive.] ».

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2.  Ouvrez l’onglet apprentissage automatique. 
     ![Espace de travail formation l’interface utilisateur de l’ordinateur.][image4]
3.  Sélectionnez votre espace de travail.
4.  Cliquez sur **Services Web**.
5.  Sélectionnez votre Service Web prédictive.
6.  Vérifiez que votre nouveau point de terminaison a été ajouté au service Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Vérifier l’espace de travail figurant dans votre service web pour vous assurer qu’il apparaît dans la zone correcte

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2.  Sélectionnez apprentissage automatique dans le menu.
      ![Région d’apprentissage machine l’interface utilisateur.][image4]
3.  Vérifiez l’emplacement de votre espace de travail.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png