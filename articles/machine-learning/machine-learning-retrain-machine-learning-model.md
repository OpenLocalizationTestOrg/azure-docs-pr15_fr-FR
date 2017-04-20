<properties
    pageTitle="Former une Machine apprentissage du modèle | Microsoft Azure"
    description="Découvrez comment faire pour former un modèle et mettre à jour le service Web pour utiliser le modèle que vous venez formé dans l’apprentissage automatique Azure."
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
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Former une Machine apprentissage du modèle

Dans le cadre du processus d’operationalization des modèles d’apprentissage machine dans Azure Machine d’apprentissage, votre modèle est formé et enregistré. Vous puis utilisez pour créer un service Web predicative. Le service Web puis peut être utilisé dans les sites web, les tableaux de bord et les applications mobiles. 

Les modèles que vous créez à l’aide d’apprentissage automatique ne sont généralement pas statiques. Nouvelles données devient disponibles ou lorsque le client de l’API a leurs propres données le modèle doit être s. 

Reconversion peut se produire fréquemment. Avec la fonctionnalité de programmation API reconversion, vous pouvez par programme former le modèle à l’aide de l’API reconversion et mettre à jour le service Web avec le modèle que vous venez formé. 

Ce document décrit le processus de reconversion et vous montre comment utiliser les API reconversion.

## <a name="why-retrain-defining-the-problem"></a>Pourquoi former : définissant le problème  

Dans le cadre de la machine formation processus d’apprentissage, un modèle est formé à l’aide d’un jeu de données. Les modèles que vous créez à l’aide d’apprentissage automatique ne sont généralement pas statiques. Nouvelles données devient disponibles ou lorsque le client de l’API a leurs propres données le modèle doit être s.

Dans ces scénarios, une API de programmation offre un moyen pratique pour permettre à vous ou au consommateur de votre API créer un client qui peut, de manière unique ou régulière, former le modèle à l’aide de leurs propres données. Ils peuvent évaluer les résultats de la reconversion et mettre à jour de l’API de service Web pour utiliser le modèle que vous venez formé.

>[AZURE.NOTE] Si vous avez une expérience de formation existant et le service du nouveau site Web, vous souhaiterez peut-être honorées extraire un service Web prédictive existant au lieu de la procédure pas à pas mentionnées dans la section suivante.

## <a name="end-to-end-workflow"></a>Flux de travail de bout en bout 

Le processus comprend les éléments suivants : A formation expérience et une expérience prédictive publiés comme un service Web. Pour activer la reconversion d’un modèle de formation, l’expérience de formation doit être publié sous forme d’un service Web avec la sortie d’un modèle de formation. Cela permet d’accès à l’API dans le modèle pour former. 

Les étapes suivantes s’appliquent aux services nouveau et Web classique :

Créer le service Web prédictive initial :

* Créer une expérience de formation
* Créer une expérience web prédictive
* Déployer un service web prédictive

Former le service Web :

* Expérience de formation de mise à jour afin de permettre aux
* Déployer le service web reconversion
* Utiliser le code de Service de l’exécution du lot pour former le modèle

Pour une présentation détaillée des étapes précédentes, voir [former Machine apprentissage modèles par programme](machine-learning-retrain-models-programmatically.md).

Si vous avez déployé un Service Web classique :

* Créer un point de terminaison du service Web prédictive
* Obtenir l’URL correctif et le code
* Utiliser l’URL correctif pour pointez sur le point de terminaison de nouveau sur le modèle de retrained 

Pour une présentation détaillée des étapes précédentes, voir [former un service Web classique](machine-learning-retrain-a-classic-web-service.md).

Si vous rencontrez des difficultés reconversion un service Web classique, voir [dépannage la reconversion d’un service Web classique Azure Machine formation](machine-learning-troubleshooting-retraining-models.md).

Si vous avez déployé un service de nouveau site Web :

* Connectez-vous à votre compte Azure le Gestionnaire de ressources
* Obtenir la définition du service Web
* Exporter la définition du Service Web en tant que JSON
* Mettre à jour la référence à la `ilearner` blob dans le JSON
* Importer le JSON dans une définition de Service Web
* Mise à jour du service Web avec la nouvelle définition du Service Web

Pour une présentation détaillée des étapes précédentes, voir [former un service de nouveau site Web en utilisant les applets de commande PowerShell de gestion de formation Machine](machine-learning-retrain-new-web-service-using-powershell.md).

Le processus de configuration de la reconversion d’un service Web classique implique les étapes suivantes :

![Présentation du processus de reconversion][1]

Le processus de configuration de la reconversion d’un service de nouveau site Web comprend les opérations suivantes :

![Présentation du processus de reconversion][7]

## <a name="other-resources"></a>Autres ressources

- [Modèles reconversion et mise à jour Azure Machine apprentissage avec Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Créer de nombreux modèles d’apprentissage automatique et web points de terminaison de service à partir d’une expérience à l’aide de PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- La vidéo [Agréés reconversion des API à l’aide de modèles](https://www.youtube.com/watch?v=wwjglA8xllg) vous montre comment former les modèles d’apprentissage automatique créés dans l’apprentissage automatique Azure à l’aide de la reconversion API et PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

