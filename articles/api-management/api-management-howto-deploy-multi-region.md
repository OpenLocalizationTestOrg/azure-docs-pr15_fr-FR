<properties
    pageTitle="Comment déployer une instance de service de gestion de l’API Azure sur plusieurs régions Azure"
    description="Découvrez comment déployer une instance de service de gestion de l’API Azure sur plusieurs régions Azure." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service de gestion de l’API Azure sur plusieurs régions Azure

Gestion des API prend en charge plusieurs région déploiement qui permet aux éditeurs de l’API distribuer un service de gestion des API unique sur un nombre quelconque de votre choix régions Azure. Cela permet de réduire demande latence perçue par les consommateurs API séparés géographiquement et améliore également la disponibilité du service si une région est hors connexion. 

Lorsqu’un service de gestion de l’API est initialement créé, il contient qu’une seule [unité][] et se trouve dans une seule région Azure désignée en tant que la zone primaire. Autres zones peuvent être ajoutés facilement via Azure classique portail. Serveur de passerelle de gestion des API est déployé sur chaque région et le trafic appel doivent être routé vers la passerelle le plus proche. Si une région est déconnectée, le trafic est redirigé automatiquement vers la passerelle suivante le plus proche. 

> [AZURE.IMPORTANT] Plusieurs région déploiement est disponible uniquement dans la couche **[Premium][]** .

## <a name="add-region"> </a>Déployer une instance de service de gestion de l’API dans une nouvelle région

Pour commencer, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Accédez à l’onglet **échelle du** portail classique Azure pour votre instance de service de gestion de l’API. 

![Onglet échelle][api-management-scale-service]

Pour déployer vers une nouvelle zone, cliquez sur la liste déroulante sous la région principale et sélectionnez une région dans la liste.

![Ajouter la région][api-management-add-region]

Une fois que la zone est sélectionnée, choisissez le nombre d’unités pour la nouvelle zone de la liste déroulante.

![Spécifier les unités][api-management-select-units]

Une fois que les zones de votre choix et les unités sont configurées, cliquez sur **Enregistrer**.

## <a name="remove-region"> </a>Supprimer une instance de service de gestion des API d’une région

Pour supprimer une instance de service de gestion de l’API d’une région, accédez à l’onglet **échelle du** portail classique Azure pour votre instance de service de gestion de l’API. 

![Onglet échelle][api-management-scale-service]

Cliquez sur le **X** à droite de la région souhaitée pour le supprimer.  

![Supprimer la région][api-management-remove-region]

Une fois que les zones de votre choix sont supprimés, cliquez sur **Enregistrer**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Prise en main avec la gestion des API Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unité]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

