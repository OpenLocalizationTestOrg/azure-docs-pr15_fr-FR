<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.5.1" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.5.1

Ce document contient les notes de publication pour le Kit de développement Azure pour .NET 2.5.1 version. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Notes de publication Azure SDK pour .NET 2.5.1

Voici des nouvelles fonctionnalités et les mises à jour dans le Kit de développement Azure pour .NET 2.5.1.

- Nouvelle features\scenarios liés aux **Extensions des outils Web**. 

    - Sites Web Azure Azure Application Service a été renommé. Pour plus d’informations, voir [Service d’application Azure et Services Azure existants](app-service-changes-existing-services.md).
    - Prise en charge des applications de l’API (Preview) Azure a été ajouté afin que les clients peuvent publier des projets ASP.NET comme API applications et utilisez l’Ajouter > mouvement Azure API application Client c# projets pour générer le code basé sur la structure de l’application API déployés. 
    - Le nœud sites Web dans l’Explorateur de serveurs a été déconseillé à la place le nœud de Service d’application Azure, qui contient la prise en charge de regroupement d’Azure API applications, les applications mobiles et les applications Web basées sur les groupe de ressources.
    - Prise en charge des applications Mobile (Preview) Azure a été ajouté afin que les clients peuvent créer des projets d’applications Mobile, ajouter des applications Mobile contrôleurs, publier les projets et à distance déboguer des applications.
    - Ajouter > mouvement Azure API application Client prend désormais en charge les fichiers Swagger JSON locaux, afin que les développeurs API Web peuvent utiliser NuGets tiers comme Swashbuckle pour générer Swagger ou créez-la manuellement. De cette façon, développeurs de client peuvent utiliser les fonctionnalités de génération de code pour utiliser un point de terminaison Swagger dans les projets c#. 
    - Web App et boîtes de dialogue publication API application ont été améliorées pour prendre en charge le concept de portail Azure de regroupement des ressources, et sélection/création de groupes de ressources Azure et application Service Plans sont représentées dans la nouvelle Web App et API App mise à disponibilité boîte de dialogue. 
    - Les nœuds API Application Server Explorer Azure fournissent des liens vers le lien approfondi API applications dans le portail Azure, ainsi que d’autres fonctionnalités telles que la diffusion en continu de journal et débogage à distance.

    Pour les problèmes connus et limitations actuelles dans Azure SDK .NET 2.5.1 [cette](app-service-release-notes.md#known_issues_2_5_1) section ci-dessous.


- Nouvelle features\scenarios liées aux **Outils HDInsight** dans Visual Studio sont activées dans cette version. 
    - Contrôle locales des scripts hive. Cliquez sur le bouton de script valider dans la barre d’outils pour déterminer s’il existe des erreurs dans votre script. 
    - Débogage des travaux Hive amélioré. Vous pouvez à présent déboguer Hive travaux en accédant à la journalisation des fils dans Visual Studio. Si votre application a des problèmes de performances, une solution fils journaux offrent des informations utiles...
    - (Version d’évaluation) Support de mot clé la saisie semi-automatique et IntelliSense pour Hive. Afin de créer des scripts Hive, HDInsight Tools pour Visual Studio ajouté support mot-clé la saisie semi-automatique et IntelliSense pour Hive.
    - Prise en charge vague. Vous pouvez désormais utiliser HDInsight Tools pour Visual Studio pour développer des topologies/becs verseurs/boulons vague en c#. Vous pouvez envoyer la topologie développée vers un cluster vague et afficher le statut de topologie/boulons/bec. Vous pouvez utiliser les journaux système et les journaux de client pour résoudre les problèmes de votre vague topologies/détaillée/becs verseurs. Vous pouvez également utiliser des ressources existantes JAVA dans vague d’HDInsight.
    
    Pour plus d’informations, voir [prise en main de l’utilisation HDInsight Hadoop Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Kit de développement Azure pour .NET 2.5.1 limitations et problèmes connus

- Applications API Azure apparaît sous la forme d’une cible de déploiement pour les applications mobiles. Applications Web doit être la destination uniquement pour les applications mobiles jusqu'à une version ultérieure. 
- API application Azure mise en service peut entraîner succès mais échouer de façon intermittente mettre à jour l’avancement dans la fenêtre de l’activité de Service d’application Azure. Solution de contournement consiste à vérifier l’état de l’application de l’API Azure nouveau dans le portail Azure. 
- Fichier > Nouveau projet > API application > expérience F5 génère une erreur HTTP, car il n’existe aucun default/index.html. Solution de contournement consiste à parcourir manuellement l’URL /api/values. 
- Icônes de l’Explorateur de serveurs apparaissent de façon intermittente, plane. Le redémarrage VS résout ce problème. 
- Si une exception est levée pendant la mise en service Web App ou API application (par exemple, les erreurs de quota dépassé ou nom de la passerelle Azure API application en double), les erreurs affichent du texte JSON brut. 
- Problèmes intermittents-création d’un projet lors de l’analyse de l’Application est activée au moment de la création de projet.
- Parfois, le code de Client de l’application API Azure généré il manque des espaces de noms, ils doivent être inclus manuellement (ou exportés automatiquement via des indications Visual Studio) pour le code afin de compiler. 
- Projets de l’application mobile doivent être publiées aux applications web, mais vous devez choisir un site que vous avez créé une application Mobile dans le portail Azure dans la mesure où les projets application Mobile requièrent une base de données. 
- La page de démarrage pour les applications mobiles utilise le terme « service mobile » au lieu de « applications mobiles » 
- Création d’un projet application mobile peut prendre jusqu'à une minute pour créer. 
- Au cours de l’API application mise en service (dans certains cas) une erreur revient à partir de l’API Azure reflétant que les autorisations pas peuvent être définies correctement, tandis que l’application API a été correctement configurée et est prête à être utilisée. Vous pouvez définir manuellement les autorisations à l’aide du portail Azure.
- Analyse de l’application n’est pas pris en charge sur des modèles d’API application et les modèles de l’application Mobile.
- Projets API application ne peuvent pas être utilisés en association avec des projets de Service Cloud.
- API projet modèles d’application sont uniquement disponibles en c#.
- Consommation API application via le menu contextuel « Ajouter un Client Azure API App » est uniquement prise en charge dans c#.

 
