<properties
    pageTitle="Créer une application web dans un environnement de Service d’application"
    description="Apprenez à créer des applications web et application offres de service, dans un environnement de Service d’application"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Créer une application web dans un environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment créer des applications web et les plans de services d’application dans un [Environnement de Service d’application](app-service-app-service-environment-intro.md) (ASE). 

> [AZURE.NOTE] Si vous voulez savoir comment créer une application web mais que vous n’avez pas besoin de le faire dans un environnement de Service d’application, voir [créer une application web .NET](web-sites-dotnet-get-started.md) ou parmi les didacticiels liés pour d’autres langues et les cadres.

## <a name="prerequisites"></a>Conditions préalables

Ce didacticiel suppose que vous avez créé un environnement de Service d’application. Si vous n’avez pas terminé, voir [créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Créer une application web

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Nouveau > Web + Mobile > Web App**. 

    ![][1]

2. Sélectionnez votre abonnement.  

    Si vous avez plusieurs abonnements n’oubliez pas que pour créer une application dans votre environnement de Service d’application, vous devez utiliser le même abonnement que vous avez utilisé lors de la création de l’environnement. 

3. Sélectionner ou créer un groupe de ressources.

    *Groupes de ressources* vous permettent de gérer les ressources Azure connexes comme une unité et sont utiles lors de l’établissement *d’un contrôle d’accès basé sur un rôle* règles pour vos applications. Pour plus d’informations, reportez-vous à [gestion de vos ressources Azure][ResourceGroups]. 

4. Sélectionnez ou créez un plan de services d’application.

    *Plans de services d’application* sont gérés ensembles d’applications web.  En règle générale, lorsque vous sélectionnez tarifs, le prix pratiqué est appliqué à l’offre de Service d’application plutôt qu’aux applications individuelles. Dans un ASE payer pour les instances cluster allouées à la ASE plutôt que de que vous avez indiquée avec votre ASP.  Pour accroître le nombre d’occurrences d’une application web évoluer les instances de votre Service d’application plan et qu’il affecte tous des applications web dans ce plan.  Certaines fonctionnalités telles que les emplacements de site ou de l’intégration VNET comporter restrictions de quantité au sein de l’offre.  Pour plus d’informations, voir [vue d’ensemble des offres de Service d’application Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    Vous pouvez identifier les offres de Service d’application dans votre ASE en recherchant dans l’emplacement indiqué sous le nom du plan.  

    ![][5]

    Si vous souhaitez utiliser un plan de services d’application qui existe déjà dans votre environnement de Service d’application, sélectionnez ce plan. Si vous voulez créer une nouvelle offre de Service d’application, consultez la section suivante de ce didacticiel, [créer un plan de services d’application dans un environnement de Service d’application](#createplan).

5. Entrez le nom de votre application web, puis cliquez sur **créer**. 

    Si votre ASE utilise un VIP externes l’URL d’une application dans ASE est : [*nom du site*]. [*nom de votre environnement de Service d’application*]. p.azurewebsites.net au lieu de [*nom du site*]. azurewebsites.net
    
    Si votre ASE utilise un VIP internes, l’URL d’une application de cette est ASE : [*nom du site*]. [*sous-domaine spécifié lors de la création de ASE*]   
    Après avoir sélectionné vos pages ASP lors de la création de ASE que vous verrez le sous-domaine mettre à jour en dessous de **nom**

## <a name="createplan"></a>Créer un plan de services d’application

Lorsque vous créez un plan de services d’application dans un environnement de Service d’application, des choix de travail sont différentes qu’il ne sont a aucune travailleurs partagés dans ASE.  Les travailleurs que vous devez utiliser sont ceux qui ont été allouées à la ASE par l’administrateur.  Cela signifie que pour créer une nouvelle offre, vous devez avoir plusieurs travailleurs réparties au pool de votre travail ASE que le nombre total d’instances sur tous vos plans déjà dans ce pool de travail.  Si vous n’avez pas suffisamment travailleurs dans votre pool de travail ASE pour créer votre planning, vous devez utiliser avec votre administrateur ASE pour obtenir les ajouter.

Une autre différence avec les offres de Service d’application hébergé par un environnement de Service d’application est l’absence de sélection tarification.  Lorsque vous avez un environnement de Service d’application vous payez des ressources cluster utilisées par le système et n’avez pas de frais ajoutés pour les plans dans cet environnement.  En règle générale, lorsque vous créez un plan de services d’application que vous sélectionnez un plan de tarification qui détermine votre facturation.  Un environnement de Service d’application est essentiellement un emplacement privé dans lequel vous pouvez créer du contenu.  Vous payez pour l’environnement et non à héberger votre contenu.

Suivez les instructions ci-dessous montrent comment créer un plan de services d’application pendant que vous créez une application web comme indiqué dans la section précédente du didacticiel.

1. Cliquez sur **Créer un nouveau** dans la sélection d’un plan l’interface utilisateur et attribuez un nom à votre plan comme vous le feriez normalement en dehors ASE.

2. Sélectionnez ASE que vous souhaitez utiliser dans le sélecteur de votre emplacement.

    Un environnement de Service d’application étant essentiellement un emplacement de déploiement privé, il s’affiche sous emplacement. 

    ![][2]

    Après la sélection de ASE dans le sélecteur d’emplacement, la création de plan de Service d’application interface utilisateur met à jour.  L’emplacement affiche désormais le nom du système ASE et la région il participe, et le sélecteur de plan tarification est remplacé par un sélecteur de pool de travail.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Sélection d’un pool de travail

En règle générale, dans le Service d’application Azure ou en dehors d’un environnement de Service d’application, il existe 3 tailles cluster qui sont disponibles avec la sélection d’un plan de prix dédié.  De la même manière, pour une ASE vous pouvez définir jusqu'à 3 pools des travailleurs et spécifier la taille de cluster qui est utilisée pour ce pool de travail.  Ce que cela signifie pour les clients de l’ASE est qu’au lieu de sélection d’un plan de tarification avec une taille de cluster pour votre plan de services d’application, sélectionnez ce qui est appelé un *pool de travail*.  

La sélection du pool employé consultant l’interface utilisateur indique la taille de cluster utilisée pour ce pool de travail sous le nom.  La quantité disponible fait référence à calcule combien instances sont disponibles pour une utilisation dans ce pool.  Le pool total peut avoir plusieurs instances que ce nombre, mais cette valeur fait référence à simplement combien n’est pas en cours d’utilisation.  Si vous avez besoin ajuster votre environnement de Service d’application pour ajouter des ressources de calcul plus voir [configuration de votre environnement de Service d’application](app-service-web-configure-an-app-service-environment.md).

![][4]

Dans cet exemple, vous voyez uniquement deux pools de travail disponibles. C’est parce que l’administrateur ASE alloué uniquement hosts dans ces pools deux concerné.  La troisième serait apparaissent lorsqu’il y a machines virtuelles alloués dedans.  

## <a name="after-web-app-creation"></a>Après la création d’une application web

Il existe quelques considérations pour exécuter des applications web et gérer les plans de services d’application dans ASE qui doivent être prises en compte.  

Comme indiqué précédemment, le propriétaire de l’ASE est responsable de la taille du système et par conséquent sont également chargés de s’assurer qu’il existe une capacité suffisante pour héberger les offres de Service de l’application souhaitées. S’il n’y a aucune la disposition des employés, vous ne serez pas en mesure de créer votre plan de services d’application.  C’est également la valeur True pour la mise à l’échelle des votre application web.  Si vous avez besoin de plusieurs instances que c’est obtenir votre administrateur d’environnement de Service d’application pour ajouter d’autres travailleurs.

Après avoir créé votre application web et le plan de services d’application, il est recommandé de mettre à l’échelle vers le haut.  Dans un ASE vous devez toujours avoir au moins 2 instances de votre plan de services d’application pour fournir la tolérance de panne pour vos applications.  Mise à l’échelle d’un plan de services d’application dans ASE est identique à normal via le plan de services d’application l’interface utilisateur.  Pour plus d’informations sur la mise à l’échelle, [comment adapter une application web dans un environnement de Service d’application](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
