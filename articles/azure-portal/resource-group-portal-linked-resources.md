<properties 
    pageTitle="Ressources connexes et liées dans la galerie de mosaïque" 
    description="Obtenir des informations sur les ressources connexes et liées qui sont affichent dans la galerie vignette du portail preview Azure." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Ressources connexes et liées dans la galerie de mosaïque

La galerie vignette vous permet de rechercher des vignettes pour une ressource particulière et faites-les glisser vers votre carte en cours. À l’aide de la galerie de mosaïque, vous pouvez créer des vues de gestion qui s’étalent sur ressources. Pour n’importe quelle ressource spécifié, les ressources associées comprennent toutes les ressources qui partagent le même groupe de ressources que la ressource et toutes les ressources qui fournissent des liens vers ou à partir de la ressource.

## <a name="linked-resources-in-azure-resource-manager"></a>Ressources liées dans le Gestionnaire de ressources Azure

L’attache est une fonctionnalité du Gestionnaire de ressources Azure.  Il vous permet de déclarer des relations entre les ressources même si elles ne résident pas dans le même groupe de ressources. Liaison n’a aucun impact sur l’exécution de vos ressources, aucun impact sur la facturation et aucun impact sur l’accès basé sur un rôle.  Il est simplement un mécanisme que vous pouvez utiliser pour représenter les relations afin que les outils tels que la galerie vignette peut fournir une gestion riche expérience.  Vos outils peuvent inspecter les liens en utilisant les liens API et fournir ainsi que des expériences de gestion des relations personnalisé. 

## <a name="how-do-i-link-my-resources"></a>Comment lier mes ressources ?

Lorsque vous créez des ressources via le portail ou en déployant un modèle via PowerShell Azure ou Azure infrastructure du langage commun, des liens sont automatiquement créés pour certaines ressources dépendantes. Vous pouvez également programmer lier des ressources à l’aide de l' [Liées API REST de ressources](https://msdn.microsoft.com/library/azure/mt238499.aspx) ou déclarer les relations dans le modèle. Pour une description complète de l’utilisation des ressources liées, consultez [ressources de liaison dans le Gestionnaire de ressources Azure](../resource-group-link-resources.md).

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez besoin d’une introduction à la rédaction de modèles Azure le Gestionnaire de ressources, voir [modèles de création](../resource-group-authoring-templates.md).
- Pour aller plus en détail la création de liens entre les ressources, consultez [ressources de liaison dans le Gestionnaire de ressources Azure](../resource-group-link-resources.md).
- Pour savoir plus sur l’utilisation des groupes de ressources via le portail d’aperçu, voir [l’aide du portail d’aperçu Azure pour gérer vos ressources Azure](resource-group-portal.md).
