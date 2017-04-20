<properties
   pageTitle="Déploiement d’un Service Web"
   description="Le flux de travail de déploiement d’un processeur en fonction de service web"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Déployer un nouveau service web

Microsoft Azure Machine d’apprentissage maintenant fournit des services web basés sur [Le Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) permettant de nouvelles options de l’offre de facturation et déployez votre service web dans plusieurs régions.

Le flux de travail général pour déployer un service web à l’aide de Microsoft Azure Machine Learning Web Services est la suivante :

* Créer une expérience prédictive
* déployer
* configurer son nom
* plan de facturation
* tester
* consommer.

La figure suivante illustre le flux de travail.

![Flux de travail de déploiement de service Web][1]
 
## <a name="deploy-web-service-from-studio"></a>Déploiement de service web à partir de Studio 

Déployer une expérience en tant que nouveau service web. Connectez-vous à la Studio apprentissage Machine, puis créer un nouveau service web prédictive. 

**Remarque**: Si vous avez déjà déployé une expérience comme un service web classique vous ne pouvez pas le déployer comme un nouveau service web.
 
Cliquez sur **exécuter** en bas de la zone de dessin expérience, puis sur **Déployer un Service Web** et **Déployer du Service Web [nouveau]**. La page de déploiement du Gestionnaire de Service Web de formation Machine s’ouvre.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Gestionnaire de Service Web machine formation déployer expérience Page
Dans la page déployer expérience, entrez un nom pour le service web.
Sélectionnez un plan de tarification. Si vous avez un plan de tarification existant, que vous pouvez le sélectionner, sinon vous devez créer une nouvelle offre de prix pour le service. 

1.  Dans le **Plan de prix** de liste déroulante, sélectionnez un plan existant ou l’option **Sélectionner nouvelle offre** .
2.  Dans la zone **Nom du Plan**, tapez un nom qui identifie l’offre sur votre facture.
3.  Sélectionnez une des **Niveaux de planification mensuelle**. Notez que la valeur par défaut des niveaux de plan pour les plans pour votre région par défaut et votre service web est déployée dans cette zone.

Cliquez sur la page de démarrage rapide pour votre service web ouvre et **déployer** .

## <a name="quickstart-page"></a>Page de démarrage rapide
La page de démarrage rapide de service web vous donne accès et consulter des instructions sur les tâches les plus courantes que vous effectuerez après la création d’un nouveau service web. À partir de là, vous pouvez facilement accéder la page de **Test** et la page de **consommer** .

## <a name="testing-your-web-service"></a>Tester votre service web

Dans la page de démarrage rapide, cliquez sur service web de Test sous tâches courantes.   

Pour tester le service web comme une réponse de demande de Service (a) :

* Dans la barre de menus, cliquez sur **tester** .
* Cliquez sur **Requête-réponse**.
* Entrez les valeurs appropriées pour les colonnes d’entrée de votre expérience.
* Cliquez sur Tester la **Requête-réponse**.

Résultat affiche sur le côté droit de la page.

Pour tester un service web lot l’exécution du Service (BES), vous allez utiliser un fichier CSV :

* Dans la barre de menus, cliquez sur **tester** .
* Cliquez sur **commandes**.
* Sous votre entrée, cliquez sur Parcourir et accédez à votre fichier de données d’exemple.
* Cliquez sur **tester**.

L’état de votre test s’affiche sous **Lots de Test**.

## <a name="consuming-your-web-service"></a>Utilisation de votre Service Web

Lorsque déployé comme un service web, expériences d’apprentissage automatique Azure fournissent une API REST qui peuvent être utilisés par une vaste gamme de périphériques et plates-formes. C’est parce que la simple API REST accepte et répond avec JSON mis en forme des messages. Le portail d’apprentissage automatique Azure fournit un code qui peut être utilisé pour appeler le service web dans R, c# et Python.
 
Dans la page de consommation, vous pouvez trouver :

* La clé d’API et URI pour l’utilisation du service web dans les applications.
* Modèles d’application web et Excel pour démarrer votre consommation.
* Exemples de code dans c#, python et R pour vous aider à démarrer.

Pour plus d’informations sur l’utilisation des services web, voir [comment utiliser un service web Azure apprentissage automatique qui a été déployé à partir d’un ordinateur expérience d’apprentissage](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des services web, voir :

[L’utilisation d’un service web d’apprentissage automatique Azure qui a été déployé à partir d’un ordinateur expérience d’apprentissage](machine-learning-consume-web-services.md)

[Azure Machine Learning Web Services : Déploiement et consommation](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
