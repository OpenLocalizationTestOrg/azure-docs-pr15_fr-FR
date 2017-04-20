<properties
    pageTitle="Convertir une expérience de formation apprentissage automatique pour une expérience prédictive | Microsoft Azure"
    description="Comment convertir une expérience de formation apprentissage automatique, utilisée pour votre modèle prédictive analytique, pour une expérience prédictive qui peut être déployée comme un service web de formation."
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
    ms.date="08/19/2016"
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Convertir une expérience de formation apprentissage automatique pour une expérience prédictive

Azure apprentissage automatique vous permet de créer, tester et déployer des solutions prédictive analytique.

Une fois que vous avez créé et itération sur une *formation expérimenter* former votre modèle prédictive analytique, et vous êtes prêt à utiliser pour évaluer les nouvelles données, vous devez préparer et rationaliser votre expérience d’évaluation. Vous pouvez ensuite déployer cette *expérience prédictive* comme un service web Azure afin que les utilisateurs peuvent envoyer des données à votre modèle et recevoir des prédictions de votre modèle.

En convertissant une expérience prédictive, vous obtenez votre modèle formé prête à être déployée comme un service web. Les utilisateurs du service web vous envoie des données d’entrée à votre modèle et votre modèle de renvoyer les résultats de la prévision. Donc lorsque vous convertissez une expérience prédictive vous souhaiterez n’oubliez pas que comment votre modèle devant être utilisé par d’autres personnes.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Le processus de conversion d’une expérience de formation pour une expérience prédictive implique trois étapes :

1.  Enregistrer le modèle d’apprentissage automatique que vous avez reçu une formation et remplacer la machine algorithme et le [Modèle de Train] d’apprentissage[ train-model] modules avec votre modèle formé enregistré.
2.  Découper l’expérience pour que les modules requis pour le score. Une expérience de formation comprend un certain nombre de modules qui sont nécessaires pour la formation mais ne sont pas nécessaires une fois que le modèle est formé et prêt à utiliser pour le score.
3.  Définir l’emplacement dans votre expérience vous acceptera les données à partir de l’utilisateur du service web, et quelles données seront renvoyées.

## <a name="set-up-web-service-button"></a>Bouton vers le haut le Service Web

Une fois que vous avez exécuté votre expérience (bouton**exécuter** en bas de la zone de dessin expérience), le bouton **Définir le serviceWeb** (sélectionnez l’option de **Service Web prédictive** ) effectue à votre place les trois étapes de la conversion de votre expérience de formation pour une expérience prédictive :

1.  Enregistre votre modèle formé comme un module dans la section **Modèles formé** de la palette de module (à gauche de la zone de dessin expérience), puis remplace l’ordinateur d’apprentissage algorithme et le [Modèle de Train] [ train-model] modules avec le modèle formé enregistré.
2.  Il supprime les modules qui ne sont pas clairement nécessaires. Dans notre exemple, cela inclut les [Données fractionnées][split], 2ème [Score modèle][score-model]et le [Modèle évaluer] [ evaluate-model] modules.
3.  Il crée l’entrée du service Web et modules de sortie et ajoute les emplacements par défaut dans votre expérience.

Par exemple, l’expérience suivant apprend un modèle d’arbre de décision augmentée deux cours à l’aide des exemples de données recensement :

![Expérience de formation][figure1]

Les modules dans cette expérience des fonctions fondamentalement quatre différentes :

![Fonctions du module][figure2]

Lorsque vous convertissez cette expérience de formation pour une expérience prédictive, certaines de ces modules ne sont plus utiles ou qu’ils ont un objectif différent :

- **Données** - les données dans cet exemple de dataset n’est pas utilisée pendant la notation : l’utilisateur du service web fournit les données à noter. Toutefois, les métadonnées à partir de ce groupe de données, tels que des types de données sont utilisées par le modèle formé. Vous devez garder le jeu de données à l’expérience prédictive afin qu’il puisse fournir ces métadonnées.

- **Préparation** - selon les données qui seront soumises à score, ces modules peuvent ou peuvent ne pas être nécessaires pour traiter les données entrantes.

    Par exemple, dans cet exemple, le jeu de données d’exemple peut-être avoir des valeurs manquantes et il inclut des colonnes qui ne sont pas nécessaires pour former le modèle. Si un [Nettoyage de données manquants] [ clean-missing-data] module a été fourni pour le contrat avec les valeurs manquantes et une [Sélection des colonnes dans le jeu de données] [ select-columns] module a été fourni pour exclure les colonnes supplémentaires à partir du flux de données. Si vous savez que les données qui seront envoyées pour notation via le service web n’ont pas de valeurs manquantes, puis vous pouvez supprimer les [Données manquants nettoyer] [ clean-missing-data] module. Cependant, étant donné que la [Sélection des colonnes dans le jeu de données] [ select-columns] module permet de définir l’ensemble de fonctionnalités est un score, ce module doit rester.

- **Train** - une fois que le modèle a été exemple avec succès, vous enregistrer en tant que modèle formé unique module. Vous remplacez puis ces modules individuels par le modèle formé enregistré.

- Dans cet exemple, le module de fractionnement **score** - est utilisé pour diviser le flux de données en un jeu de données de test et formation. Dans l’expérience prédictive Ceci n’est pas nécessaire et peut être supprimé. De même, le 2ème [Score modèle] [ score-model] module et le [Modèle d’évaluer] [ evaluate-model] module sont utilisées pour comparer les résultats à partir des données de test, afin que ces modules ne sont également pas nécessaires à l’expérience prédictive. Le [Modèle de Score] restant[ score-model] module, cependant, est nécessaire pour retourner le résultat score via le service web.

Voici comment notre exemple apparaît après avoir cliqué sur **Définir le serviceWeb**:

![Expérience prédictive converti][figure3]

Cela peut être suffisant pour préparer votre expérience à déployer comme un service web. Toutefois, vous souhaiterez peut-être effectuer des tâches supplémentaires spécifiques à votre expérience.

### <a name="adjust-input-and-output-modules"></a>Ajuster les modules d’entrée et de sortie

Dans votre expérience de formation, vous utilisez un jeu de données de formation et puis avez-vous un traitement pour obtenir les données dans un formulaire qui l’algorithme d’apprentissage machine nécessaires. Si les données que vous souhaitez recevoir via le service web n’auront pas besoin ce traitement, vous pouvez déplacer le **module d’entrée du service Web** sur un autre nœud dans votre expérience.

Par exemple, par défaut **La valeur d’un Service Web** place le module de **Saisie de service Web** en haut de votre flux de données, comme dans l’illustration ci-dessus. Toutefois, si les données d’entrée n’auront pas besoin ce traitement, puis vous pouvez positionner manuellement l' **entrée du service Web** au-delà les modules de traitement des données :

![Déplacer l’entrée de service web][figure4]

Les données d’entrée fournies par le biais du service web passera désormais directement dans le module Score modèle sans tout prétraitement.

De même, par défaut, **Définissez le serviceWeb** place le module de sortie de service Web en bas de votre flux de données. Dans cet exemple, le service web renverra à l’utilisateur la sortie du [Modèle de Score] [ score-model] module qui comprend le vecteur complète des données d’entrée, ainsi que le système d’évaluation des résultats.

Cependant, si vous préférez renvoyer différencier - par exemple, uniquement les résultats de la notation et pas le vecteur entière de données d’entrée -, vous peuvent insérer une [Sélection des colonnes dans le jeu de données] [ select-columns] module exclure toutes les colonnes sauf les résultats de la notation. Vous déplacez ensuite le module de **sortie de service Web** vers le résultat de la [Sélection des colonnes dans le jeu de données] [ select-columns] module :

![Déplacement de la sortie du service web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Ajouter ou supprimer des modules de traitement des données supplémentaires

S’il existe plus de modules dans votre expérience dont vous savez ne seront pas nécessaires au cours de la notation, lesquels peuvent être supprimés. Par exemple, étant donné que nous avons déplacé le module de **Saisie de service Web** à un point après les modules de traitement des données, nous pouvons supprimer les [Données manquants nettoyer] [ clean-missing-data] module à partir de l’expérience prédictive.

Notre expérience prédictive se présente désormais ainsi :

![Suppression de module supplémentaire][figure6]

### <a name="add-optional-web-service-parameters"></a>Ajoutez des paramètres de Service Web facultatifs

Dans certains cas, vous souhaiterez permettent à l’utilisateur de votre service web modifier le comportement des modules lorsque le service est accessible. *Paramètres de Service Web* permettent de faire cela.

Un exemple courant est la configuration d' [Importer des données] [ import-data] module afin que l’utilisateur du service web déployé peut spécifier une autre source de données lors de l’accès au service web. Ou en configurant les [Exporter les données] [ export-data] module afin que vous pouvez spécifier une destination différente.

Vous pouvez définir des paramètres de Service Web et les associer à un ou plusieurs paramètres de module, et vous pouvez spécifier si elles sont obligatoires ou facultatives. L’utilisateur du service web peut ensuite fournir des valeurs pour ces paramètres lorsque le service est accessible et les actions de module seront modifiées en conséquence.

Pour plus d’informations sur les paramètres de Service Web, voir [Paramètres de Service Web d’à l’aide de Azure Machine apprentissage ] [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Déployer l’expérience prédictive comme un service web

À présent que l’expérience prédictive a été suffisamment préparé, vous pouvez le déployer comme un service web Azure. Utilisation du service web, les utilisateurs peuvent envoyer des données à votre modèle et le modèle renverra ses prédictions.

Pour plus d’informations sur le processus de déploiement complet, voir [déploiement d’un service web d’apprentissage automatique Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
