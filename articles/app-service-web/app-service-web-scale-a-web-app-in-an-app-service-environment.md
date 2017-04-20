<properties 
    pageTitle="Comment adapter une application dans un environnement de Service d’application" 
    description="Mise à l’échelle d’une application dans un environnement de Service d’application" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Mise à l’échelle des applications dans un environnement de Service d’application #

Dans le Service d’application Azure, il existe trois actions en règle générale, que vous pouvez mettre à l’échelle :

- plan de tarification
- taille de travail 
- nombre d’instances.

Dans un ASE, il est inutile pour sélectionner ou modifier le plan de tarification.  En termes de fonctionnalités, il est déjà à une prime fonctionnalité niveau de prix.  

En ce qui concerne les tailles de travail, l’administrateur ASE peut affecter la taille de la ressource cluster à utiliser pour chaque pool de travail.  Que signifie que vous pouvez avoir collaborateur regroupement 1 avec P4 calcule ressources et collaborateur Pool 2 avec les ressources de cluster P1, si vous le souhaitez.  Ils n’ont pas à être dans l’ordre de taille.  Pour informations autour de la taille et leur prix voir le document ici [Azure Application Service tarifs][AppServicePricing].  Cela laisse les options d’échelle pour les applications web et programmes de Service d’application dans un environnement de Service d’application pour être :

- sélection du pool collaborateur
- nombre d’instances

Modification de des éléments s’effectue via l’interface utilisateur appropriée pour votre ASE hébergé Plans de Service d’application.  

![][1]

Vous ne pouvez pas évoluer votre ASP dépasse le nombre de ressources de cluster disponibles dans la liste de travail figurant dans vos pages ASP.  Si vous devez calculer des ressources dans cette liste de travail, vous devez obtenir votre administrateur ASE pour les ajouter.  Pour plus d’informations autour de reconfigurer votre ASE consultez les informations ici : [Comment faire pour configurer un environnement de Service d’application][HowtoConfigureASE].  Vous souhaiterez peut-être également tirer parti des fonctionnalités ASE échelle automatique pour ajouter la capacité en fonction de planification ou indicateurs.  Pour obtenir plus d’informations sur la configuration d’échelle pour le ASE environnement lui-même voir [comment configurer échelle automatique pour un environnement de Service d’application][ASEAutoscale].

Vous pouvez créer plusieurs application offre de service à l’aide de ressources de calcul à partir des pools travail différents, ou vous pouvez utiliser le même pool de travail.  Ressources de calcul par exemple si vous disposez de ressources cluster disponible (10) 1 du Pool de travail, vous pouvez choisir de créer un plan de services d’application à l’aide de ressources de calcul (6) et planifier un deuxième service d’application qui utilise (4).

### <a name="scaling-the-number-of-instances"></a>Le nombre d’instances de mise à l’échelle ###

Lorsque vous créez votre application web dans un environnement de Service d’application elle commence par 1 instance.  Vous pouvez ensuite évoluer vers des instances supplémentaires pour fournir des ressources de cluster supplémentaires pour votre application.   

Si votre ASE a une capacité suffisante c’est assez simple.  Vous accédez à votre Plan de Service de l’application qui contient les sites que vous souhaitez évoluer et sélectionnez échelle.  Cette action ouvre l’interface utilisateur de l’endroit où vous pouvez définir l’échelle de vos pages ASP manuellement ou configurer des règles d’échelle pour votre ASP.  Sur une échelle de manuellement votre application valeur simplement ***échelle par*** ***un nombre d’instances entrer manuellement***.  À partir de là faites glisser le curseur à la quantité souhaitée ou entrez dans la zone à côté du curseur.  

![][2] 

Les règles d’échelle pour une page ASP dans ASE fonctionnent de la même manière normalement.  Vous pouvez sélectionner ***Pourcentage processeur*** sous ***échelle par*** et créer des règles d’échelle pour votre ASP selon un pourcentage de l’UC ou vous pouvez créer des règles plus complexes à l’aide de ***règles de planification et de performances***.  Pour afficher plus d’informations sur la configuration d’échelle utiliser le guide ici [adapter une application dans le Service d’application Azure][AppScale]. 


### <a name="worker-pool-selection"></a>Sélection du Pool collaborateur ###

Comme indiqué précédemment, la sélection du pool collaborateur est accessible à partir de l’interface utilisateur ASP.  Ouvrez la carte pour ASP que vous souhaitez mettre à l’échelle, puis sélectionnez pool de travail.  Vous verrez tous les pools de travail que vous avez configurée dans votre environnement de Service d’application.  Si vous avez pool de travail qu’un seul puis seuls apparaissent l’une pool répertoriés.  Pour modifier le pool de travail votre ASP est dans, vous sélectionnez simplement le pool de travail que vous souhaitez que votre Plan de Service de l’application pour atteindre.  

![][3]

Avant de passer votre ASP pool d’un travail à l’autre, il est important pour vous assurer que vous aurez capacité adéquate pour votre ASP.  Dans la liste des pools collaborateur, non seulement le nom du pool collaborateur n’est répertorié mais vous pouvez également voir combien travailleurs sont disponibles dans ce pool de travail.  Assurez-vous qu’il existe suffisamment d’instances contenant votre Plan de Service d’application.  Si vous avez besoin plus calculer des ressources dans la liste de travail que vous souhaitez déplacer vers, puis obtenir votre administrateur ASE pour les ajouter.  

> [AZURE.NOTE] Déplacement de qu'une page ASP pool d’un travail entraînera froid démarre des applications dans que ASP.  Cela peut entraîner des demandes d’exécution lentement que votre application est froid démarré sur les nouvelles ressources cluster.  Le démarrage à froid vous pouvez éviter à l’aide de l' [application à chaud capacité] [ AppWarmup] dans le Service d’application Azure.  Le module de l’initialisation de l’Application décrit dans l’article fonctionne également pour les démarrages à froid, car le processus d’initialisation est également appelé lorsqu’applications sont froid démarré sur cluster de nouvelles ressources. 

## <a name="getting-started"></a>Prise en main

Pour commencer avec les environnements de Service d’application, voir [Comment en créer une application Service environnement][HowtoCreateASE]

Pour plus d’informations sur la plateforme Azure Application Service, voir [Service d’application Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
