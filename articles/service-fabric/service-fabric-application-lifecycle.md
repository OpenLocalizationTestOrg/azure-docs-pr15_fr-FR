<properties
   pageTitle="Cycle de vie application en Service TISSU | Microsoft Azure"
   description="Décrit le développement, le déploiement, test, la mise à niveau, la conservation et suppression d’applications de Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>Cycle de vie application tissu de service
Comme avec d’autres plateformes, une application sur Azure Service TISSU généralement parcourt les phases suivantes : création, développement, test, déploiement, la mise à niveau, maintenance et suppression. Service TISSU fournit prise en charge exhaustive du cycle de vie application complète d’applications cloud, de développement jusqu’au déploiement, la gestion quotidienne et maintenance aux désaffecter éventuelle. Le modèle de service permet à plusieurs rôles différents participer indépendamment du cycle de vie d’application. Cet article fournit une vue d’ensemble des API et comment elles sont utilisées par les différents rôles dans l’ensemble les phases du cycle de vie application tissu de Service.

## <a name="service-model-roles"></a>Rôles de modèle de service
Les rôles de modèle de service sont :

- **Développeur de service**: développe services modulaires et génériques pouvant être réutilisées et utilisés dans plusieurs applications du même type ou de types différents. Par exemple, un service de file d’attente peut être utilisé pour la création d’une application tickets (support technique) ou une application de commerce (panier).

- **Développeur de l’application**: crée des applications en intégrant un ensemble de services pour répondre à certains scénarios ou configuration spécifiques. Par exemple, un site Web de commerce peut-être intégrer « JSON de Service frontal sans état », « Appel d’offres avec état Services » et « file d’attente avec état » pour créer une solution ENCHERE.

- **Administrateur de l’application**: décisions sur la configuration de l’application (en remplissant les paramètres de modèle de configuration), le déploiement (mappage aux ressources disponibles) et la qualité du service. Par exemple, un administrateur d’application décide de paramètres régionaux de langue (en anglais pour les États-Unis) ou en japonais pour le Japon, par exemple de l’application. Une autre application déployée peut avoir différents paramètres.

- **Opérateur**: déploie applications basées sur la configuration d’application et les exigences définies par l’administrateur d’application. Par exemple, un opérateur de dispositions et déploie l’application et garantit qu’il s’exécute dans Azure. Opérateurs de surveiller les informations de santé et les performances d’application et mettre à jour l’infrastructure physique selon vos besoins.


## <a name="develop"></a>Développer
1. Un *développeur service* développe des différents types de services à l’aide du modèle de programmation [Intervenants fiable](service-fabric-reliable-actors-introduction.md) ou [Services fiables](service-fabric-reliable-services-introduction.md) .
2. Un *développeur de service* décrit manière déclarative les types de service développé dans un fichier manifeste service composée d’un ou plusieurs modules de code, la configuration et données.
3. Un *développeur de l’application* crée alors une application à l’aide de différents types de service.
4. Manière déclarative, un *développeur de l’application* décrit le type d’application dans un manifeste d’application en référençant manifestes service des services constituants et correctement, en substituant et paramétrage différents paramètres de configuration et déploiement des services constituants.

Pour obtenir des exemples, voir [prise en main intervenants fiable](service-fabric-reliable-actors-get-started.md) et [prise en main des Services fiables](service-fabric-reliable-services-quick-start.md) .

## <a name="deploy"></a>Déployer
1. Un *administrateur de l’application* personnalise le type d’application à une application spécifique soit déployé sur un cluster de Service tissu en spécifiant les paramètres appropriés de l’élément **ApplicationType** dans le manifeste d’application.

2. Un *opérateur* télécharge le package d’application sur le magasin d’image cluster à l’aide de la [méthode **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou l' [applet de commande **Copier ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). Le package d’application contient le manifeste d’application et de la collection de packages de service. Service TISSU déploie des applications à partir du package d’application stockées dans le magasin d’image, qui peut être un magasin d’objets blob Azure ou le service système tissu de Service.

3. L' *opérateur* configure ensuite le type d’application dans le cluster cible à partir du package d’application chargés à l’aide de la [méthode **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), l' [applet de commande **Registre ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou l' [opération de **mise en service une Application** reste](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Après la mise en service de l’application, un *opérateur* démarre l’application avec les paramètres fournis par l' *administrateur de l’application* à l’aide de la [méthode **CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), l' [applet de commande **New-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125913.aspx)ou l' [opération reste **Créer une Application** ](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Une fois que l’application a été déployée, un *opérateur* utilise la [méthode **CreateServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), l' [applet de commande **New-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt125874.aspx)ou l' [opération de **Création de Service** reste](https://msdn.microsoft.com/library/azure/dn707657.aspx) pour créer des instances de service pour l’application basée sur les types de service disponibles.

6. L’application est actif dans le cluster tissu de Service.

Pour obtenir des exemples, voir [déployer une application](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Après le déploiement sur le cluster de développement local ou un cluster de test, un *développeur de service* s’exécute le scénario de test basculement intégrés en utilisant les classes [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) et [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) ou l' [applet de commande **Appeler ServiceFabricFailoverTestScenario** ](https://msdn.microsoft.com/library/azure/mt644783.aspx). Le scénario de test basculement s’exécute un service spécifié par le biais des transitions importantes et basculement pour vous assurer qu’il est toujours disponible et qu’il fonctionne.

2. Le *développeur du service* exécute ensuite le scénario de test chaos intégrés en utilisant les classes [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) et [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) ou l' [applet de commande **Appeler ServiceFabricChaosTestScenario** ](https://msdn.microsoft.com/library/azure/mt644774.aspx). Le scénario de test chaos provoque aléatoirement plusieurs nœud package de code et des erreurs de copie dans le cluster.

3. *Développeur du service de* [communication de service à service tests](service-fabric-testability-scenarios-service-communication.md) en créant des scénarios de test qui transfèrent le principales réplicas autour du cluster.

Pour plus d’informations, voir [Présentation du Service d’analyse de défaillance](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Mise à niveau
1. Un *développeur de service* met à jour les services de l’application instanciée constituants et/ou résout bogues et fournit une nouvelle version du manifeste de service.

2. Un *développeur de l’application* substitue et paramètre les paramètres de configuration et le déploiement des services cohérentes et fournit une nouvelle version du manifeste d’application. Développeur de l’application puis incorpore les nouvelles versions des manifestes service dans l’application et fournit une nouvelle version du type d’application dans un package d’application mis à jour.

3. Un *administrateur de l’application* incorpore la nouvelle version du type d’application dans l’application cible en mettant à jour les paramètres appropriés.

5. Un *opérateur* télécharge le package d’application mis à jour vers le magasin d’image cluster à l’aide de la [méthode **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou l' [applet de commande **Copier ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). Le package d’application contient le manifeste d’application et de la collection de packages de service.

6. Un *opérateur* dispositions la nouvelle version de l’application dans le cluster cible à l’aide de la [méthode **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), l' [applet de commande **Registre ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou l' [opération de **mise en service une Application** reste](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Un *opérateur* met à jour l’application cible pour la nouvelle version à l’aide de la [méthode **UpgradeApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), l' [applet de commande **Démarrer ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125975.aspx)ou l' [opération reste **mettre à niveau une Application** ](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Un *opérateur* vérifie l’avancement de la mise à niveau à l’aide de la [méthode **GetApplicationUpgradeProgressAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), l' [applet de commande **Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx)ou l' [opération de **Progression de mise à niveau l’Application obtention** reste](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Le cas échéant, l' *opérateur* modifie et applique à nouveau les paramètres de la mise à niveau application active à l’aide de la [méthode **UpdateApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), l' [applet de commande de **Mise à jour ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt126030.aspx)ou l' [opération de **Mise à jour une Application mise à niveau** le reste](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Le cas échéant, l' *opérateur* annule la mise à niveau application active à l’aide de la [méthode **RollbackApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), l' [applet de commande **Démarrer ServiceFabricApplicationRollback** ](https://msdn.microsoft.com/library/azure/mt125833.aspx)ou l' [opération reste **Annulation Application mise à niveau** ](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Service TISSU met à jour l’application cible en cours d’exécution dans le cluster sans perdre la disponibilité d’une de ses services constituants.

Reportez-vous à l' [Application mise à niveau didacticiel](service-fabric-application-upgrade-tutorial.md) pour obtenir des exemples.

## <a name="maintain"></a>Mettre à jour
1. Pour les mises à niveau du système d’exploitation et correctifs, Service TISSU interfaces avec l’infrastructure Azure afin de garantir la disponibilité de toutes les applications en cours d’exécution dans le cluster.

2. Pour les mises à niveau et correctifs vers la plateforme tissu de Service, Service TISSU met à niveau lui-même sans perdre sa disponibilité de toutes les applications en cours d’exécution sur le cluster.

3. Un *administrateur de l’application* approuve l’ajout ou la suppression des nœuds à partir d’un cluster après l’analyse des données d’utilisation capacité historiques et prévisionnelles futures.

4. Un *opérateur* ajoute et supprime les nœuds spécifiés par l' *administrateur de l’application*.

5. Lorsque nouveaux nœuds sont ajoutés à ou nœuds existants sont supprimés du cluster, Service TISSU automatiquement équilibre la charge de l’exécution des applications sur tous les nœuds du cluster pour optimiser les performances.

## <a name="remove"></a>Supprimer
1. Un *opérateur* peut supprimer une instance spécifique d’un service en cours d’exécution dans le cluster sans supprimer l’ensemble de l’application à l’aide de la [méthode **DeleteServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), l' [applet de commande **Supprimer ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt126033.aspx)ou l' [opération de **Service de suppression de** reste](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Un *opérateur* pouvez également supprimer une instance d’application et tous ses services à l’aide de la [méthode **DeleteApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), l' [applet de commande **Supprimer ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125914.aspx)ou l' [opération reste **Supprimer une Application** ](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Une fois les applications et services ont été arrêtés, l' *opérateur* peut annule la configuration du type d’application à l’aide de la [méthode **UnprovisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), l' [applet de commande **Unregister ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125885.aspx)ou l' [opération reste de **la mise hors service une Application** ](https://msdn.microsoft.com/library/azure/dn707671.aspx). Annuler déploiement le type d’application ne supprime pas le package d’application à partir de la ImageStore. Vous devez supprimer manuellement le package d’application.

4. Un *opérateur* supprime le package d’application de la ImageStore à l’aide de la [méthode **RemoveApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) ou l' [applet de commande **Supprimer ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Pour obtenir des exemples, voir [déployer une application](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement de test et la gestion des applications de Service tissu et des services, voir :

- [Intervenants fiables](service-fabric-reliable-actors-introduction.md)
- [Services fiables](service-fabric-reliable-services-introduction.md)
- [Déployer une application](service-fabric-deploy-remove-applications.md)
- [Mise à niveau de l’application](service-fabric-application-upgrade.md)
- [Vue d’ensemble de la capacité de test](service-fabric-testability-overview.md)
- [Exemple de cycle de vie d’une application basée sur le reste](service-fabric-rest-based-application-lifecycle-sample.md)
