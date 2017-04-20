<properties
    pageTitle="Comment déployer un Service Web sur plusieurs régions | Microsoft Azure"
    description="Étapes pour déployer des régions (copier) un Service Web à d’autres."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Comment déployer un Service Web sur plusieurs régions

Les nouveaux Services Web Azure permettent de facilement déployer un service web sur plusieurs zones sans recourir à plusieurs abonnements ou des espaces de travail. 

Tarifs est région spécifique, par conséquent, vous devez définir un plan de facturation pour chaque région dans lequel vous allez déployer le service web.

## <a name="to-create-a-plan-in-another-region"></a>Pour créer un plan dans une autre zone

1. Connectez-vous à [Microsoft Azure Machine d’apprentissage Services Web](https://services.azureml.net/).
2. Cliquez sur l’option de menu **Plans** .
3. Dans les offres sur Afficher la page, cliquez sur **Nouveau**.
4. Dans le menu déroulant **abonnement** , sélectionnez l’abonnement dans lequel la nouvelle offre résideront.
5. Dans la liste déroulante **région** , sélectionnez une zone pour la nouvelle offre. Les Options du Plan pour la zone sélectionnée s’affichent dans la section **Options du Plan** de la page.
6. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le plan. Plus d’informations sur les groupes de ressources, consultez [ressources Azure gérer via le portail](../azure-portal/resource-group-portal.md).
7. Dans la zone **Nom de Plan** , tapez le nom du plan.
8. Sous **Options de l’offre**, cliquez sur le niveau de facturation pour la nouvelle offre.
9. Cliquez sur **créer**.


## <a name="deploying-the-web-service-to-another-region"></a>Déploiement du service web dans une autre région

1. Cliquez sur l’option de menu **Services Web** .
2. Sélectionnez le Service Web vous déployez dans une nouvelle région.
3. Cliquez sur **Copier**.
4. Dans la zone **Nom du Service Web**, tapez un nouveau nom pour le service web.
5. Dans la zone **description du service Web**, tapez une description pour le service web.
6. Dans le menu déroulant **abonnement** , sélectionnez l’abonnement dans lesquelles le nouveau service web résideront.
7. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le service web. Plus d’informations sur les groupes de ressources, consultez [ressources Azure gérer via le portail](../azure-portal/resource-group-portal.md).
8. Dans la liste déroulante **région** , sélectionnez la zone dans laquelle vous voulez déployer le service web.
9. Dans la liste déroulante **compte de stockage** , sélectionnez un compte de stockage dans lequel stocker le service web.
10. Dans la liste déroulante **Plan de prix** , sélectionnez un plan dans la région sélectionnée à l’étape 8.
11. Cliquez sur **Copier**.

