<properties 
    pageTitle="Permet de déployer des ressources Azure Azure portal | Microsoft Azure" 
    description="Utiliser Azure portail et gérer les ressources Azure pour déployer vos ressources." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Déployer des ressources avec les modèles de gestionnaire de ressources et Azure portal

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure infrastructure du langage commun](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique indique comment utiliser le [portail Azure](https://portal.azure.com) avec [Le Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour déployer vos ressources Azure. Pour en savoir plus sur la gestion de vos ressources, consultez [ressources Azure gérer via le portail](./azure-portal/resource-group-portal.md).

Pour l’instant, pas chaque service prend en charge le portail ou le Gestionnaire de ressources. Pour ces services, vous devez utiliser le [portail classique](https://manage.windowsazure.com). Pour l’état de chaque service, voir [graphique Azure disponibilité du portail](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Créer le groupe de ressources

1. Pour créer un groupe de ressources vide, sélectionnez **Nouveau** > **gestion** > **Groupe de ressources**.

    ![créer le groupe de ressources vide](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Lui donner un nom et un emplacement et, si nécessaire, sélectionnez un abonnement. Vous devez fournir un emplacement pour le groupe de ressources, car le groupe de ressources stocke les métadonnées relatives aux ressources. Pour des raisons de conformité, vous souhaiterez spécifier l’emplacement de stockage que les métadonnées. En règle générale, nous vous recommandons de spécifier un emplacement dans lequel se trouve la plupart de vos ressources. À l’aide de la même emplacement peut simplifier un modèle.

    ![définir les valeurs de groupe](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Déployer des ressources à partir de Marketplace

Après avoir créé un groupe de ressources, vous pouvez déployer les ressources qui lui depuis le site Marketplace. Le marché propose des solutions prédéfinies pour les scénarios courants.

1. Pour commencer un déploiement, sélectionnez **Nouveau** et le type de ressource que vous voulez déployer. Ensuite, recherchez la version de la ressource que vous voulez déployer.

    ![déployer des ressources](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Si vous ne voyez pas la solution particulière que vous voulez déployer, vous pouvez rechercher le marché de celui-ci.

    ![Recherchez sur marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

3. Selon le type de ressource sélectionnée, vous avez une collection de propriétés à définir avant le déploiement. Ces options ne sont pas indiquées ici, comme elles varient en fonction de type de ressource. Pour tous les types, vous devez sélectionner un groupe de ressources de destination. L’image suivante montre comment créer une application web et le déployez dans le groupe de ressources que vous avez créé.

    ![créer le groupe de ressources](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Par ailleurs, vous pouvez décider de créer un groupe de ressources lors du déploiement de vos ressources. Sélectionnez **à nouveau** , puis nommez le groupe de ressources.

    ![créer le nouveau groupe de ressources](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Votre déploiement commence. Le déploiement peut prendre quelques minutes. Lorsque le déploiement est terminée, vous voyez une notification.

    ![afficher la notification](./media/resource-group-template-deploy-portal/view-notification.png)

5. Après le déploiement de vos ressources, vous pouvez ajouter davantage de ressources au groupe de ressources à l’aide de la commande **Ajouter** dans la carte de groupe de ressources.

    ![ajouter des ressources](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Déployer des ressources à partir du modèle personnalisé

Si vous voulez effectuer un déploiement sans utiliser un des modèles sur le marché, vous pouvez créer un modèle personnalisé qui définit l’infrastructure de votre solution. Pour en savoir plus sur la création de modèles, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md).

1. Pour déployer un modèle personnalisé à partir du portail, sélectionnez **Nouveau**, puis démarrer la recherche pour le **Modèle de déploiement** jusqu'à ce que vous pouvez le sélectionner dans les options.

    ![déploiement de modèle de recherche](./media/resource-group-template-deploy-portal/search-template.png)

2. Sélectionnez **Déploiement de modèle** à partir des ressources disponibles.

    ![Sélectionnez déploiement de modèle](./media/resource-group-template-deploy-portal/select-template.png)

3. Après avoir lancé le déploiement de modèle, ouvrez le modèle vide est disponible pour la personnalisation.

    ![créer le modèle](./media/resource-group-template-deploy-portal/show-custom-template.png)

    Dans l’éditeur, ajoutez la syntaxe JSON qui définit les ressources que vous voulez déployer. Sélectionnez **Enregistrer** lorsque vous avez terminé. Pour des instructions sur l’écriture de la syntaxe de la JSON, voir [Présentation du modèle Gestionnaire de ressources](resource-manager-template-walkthrough.md).

    ![modifier le modèle](./media/resource-group-template-deploy-portal/edit-template.png)

4. Ou bien, vous pouvez sélectionner un modèle existant à partir des [modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/). Ces modèles sont fournis par la Communauté. Ils comprennent les nombreux scénarios courants et une personne peut avoir ajouté un modèle similaire à ce que vous voulez déployer. Vous pouvez rechercher les modèles pour rechercher un élément qui correspond à votre scénario.

    ![Sélectionnez le modèle de démarrage rapide](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Vous pouvez afficher le modèle sélectionné dans l’éditeur.

5. Après avoir fourni toutes les autres valeurs, sélectionnez **créer** le modèle de déploiement. 

    ![modèle de déploiement](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Déployer des ressources à partir d’un modèle enregistré à votre compte

Le portail permet d’enregistrer un modèle à votre compte Azure et redéployez ultérieurement. Pour plus d’informations sur l’utilisation de ces enregistré modèles, de [prise en main des modèles privés sur le portail Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Pour rechercher vos modèles enregistrés, sélectionnez **Parcourir** > **modèles**.

    ![Parcourir les modèles](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Dans la liste des modèles enregistrés dans votre compte, sélectionnez celle que vous souhaitez travailler.

    ![modèles enregistrés](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Sélectionnez **déployer** à redéployez ce modèle enregistré.

    ![déployez le modèle enregistré](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour afficher les journaux d’audit, voir [Auditer opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour résoudre les erreurs de déploiement, voir [déploiements de groupe de ressources de résolution des problèmes avec Azure portail](resource-manager-troubleshoot-deployments-portal.md).
- Pour récupérer un modèle à partir d’un déploiement ou un groupe de ressources, voir [Exporter le Gestionnaire de ressources Azure modèle à partir de ressources existantes](resource-manager-export-template.md).





