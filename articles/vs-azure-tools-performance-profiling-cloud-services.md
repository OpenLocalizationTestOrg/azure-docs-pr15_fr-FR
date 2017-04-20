<properties 
   pageTitle="Tester les performances d’un service cloud | Microsoft Azure"
   description="Tester les performances d’un service cloud à l’aide du Générateur de profils Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="testing-the-performance-of-a-cloud-service"></a>Tester les performances d’un service cloud 

##<a name="overview"></a>Vue d’ensemble

Vous pouvez tester les performances d’un service de nuage des façons suivantes :

- Utiliser les Diagnostics de Azure pour recueillir des informations sur les demandes et les connexions et passer en revue les statistiques de site qui montrent comment le service effectue un point de vue du client. Pour commencer à utiliser, voir [configuration diagnostics pour les Services en nuage Azure et Machines virtuelles]( http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Utilisez le Générateur de profils Visual Studio pour obtenir une analyse approfondie des aspects calculs de la façon dont le service s’exécute. Comme décrit dans cette rubrique, vous pouvez utiliser le Générateur de profils pour mesurer les performances comme un service s’exécute dans Azure. Pour plus d’informations sur l’utilisation du Générateur de profils pour mesurer les performances comme un service s’exécute localement dans un émulateur de calcul, voir [tester les performances d’une Azure Cloud Service localement dans le calcul émulateur à l’aide de du Générateur de profils Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## <a name="choosing-a-performance-testing-method"></a>Choix d’une méthode de test des performances

###<a name="use-azure-diagnostics-to-collect"></a>Utilisez Azure Diagnostics pour collecter :###

- Statistiques sur les pages web ou des services, tels que les demandes et les connexions.

- Statistiques sur les rôles, tels que la fréquence à laquelle un rôle est redémarré.

- Informations sur l’utilisation de la mémoire, telles que le pourcentage de temps utilisé par le nettoyage ou la mémoire définir global d’un rôle en cours d’exécution.

###<a name="use-the-visual-studio-profiler-to"></a>Utiliser le Générateur de profils Visual Studio pour :###

- Déterminer les fonctions les plus longue.

- Combien de temps prend chaque partie d’un programme calculs de mesure.

- Comparer des rapports de performances détaillées pour les deux versions d’un service.

- Analyser l’allocation de mémoire de manière plus détaillée le niveau d’allocation de mémoire individuels.

- Analyser les problèmes d’accès concurrentiels au code multithread.

Lorsque vous utilisez le Générateur de profils, vous pouvez collecter des données lorsqu’un service cloud s’exécute localement ou dans Azure.

###<a name="collect-profiling-data-locally-to"></a>Collecter les données de profilage localement à :###

- Tester les performances d’une partie d’un service cloud, tel que l’exécution du rôle de collaborateur spécifique, qui ne nécessite pas une charge simulée plausible.

- Tester les performances d’un service cloud dans isolement, sous contrôle.

- Tester les performances d’un service cloud avant de déployer vers Azure.

- Tester les performances d’un service cloud privé, sans affecter les déploiements existants.

- Tester les performances du service sans entraîner de frais pour l’exécution dans Azure.

###<a name="collect-profiling-data-in-azure-to"></a>Collecter les données de profilage dans Azure pour :###

- Tester les performances d’un service cloud en cas de charge simulé ou réel.

- Utilisez la méthode d’instrumentation de collecte des données de profilage, comme décrit plus loin dans cette rubrique.

- Tester les performances du service dans le même environnement que lorsque le service s’exécute en production.

En règle générale permet de simuler une charge pour tester des services cloud sous normal ou des conditions difficiles.

## <a name="profiling-a-cloud-service-in-azure"></a>Profil d’un service cloud dans Azure

Lorsque vous publiez votre service cloud dans Visual Studio, vous pouvez le service de profil et spécifiez les paramètres de profilage que vous fournissent les informations voulues. Une session de profilage est démarrée pour chaque instance d’un rôle. Pour plus d’informations sur la publication de votre service à partir de Visual Studio, voir [publication d’un Service Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Pour en savoir plus sur les performances profil dans Visual Studio, voir [Guide du débutant profil de performances](https://msdn.microsoft.com/library/azure/ms182372.aspx) et [L’analyse des performances des applications par à l’aide des outils de profil](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE] Vous pouvez activer IntelliTrace ou profil lorsque vous publiez votre service cloud. Vous ne pouvez pas activer les deux.

###<a name="profiler-collection-methods"></a>Méthodes de collection de sites du Générateur de profils

Vous pouvez utiliser des méthodes de collecte différent de profil utilisateur, en fonction de vos problèmes de performances :

- **Échantillonnage de l’UC** - cette méthode collecte les statistiques qui sont utiles pour l’analyse initiale des problèmes d’utilisation du processeur. Échantillonnage de l’UC est suggérée pour le démarrage de la plupart des enquêtes de performances. Il existe un faible impact sur l’application qui vous soyez profil lorsque vous collectez des données d’échantillonnage de l’UC.

- **Instrumentation** -cette méthode collecte des données de minutage détaillées sont utiles pour l’analyse approfondie et pour analyser les problèmes de performances d’entrée/sortie. La méthode d’instrumentation enregistre chaque entrée, sortie et appel de fonction des fonctions dans un module pendant un profil exécuter. Cette méthode est utile pour collecter les informations de minutage détaillées sur une section de votre code et comprendre l’impact des opérations d’entrée et de sortie sur les performances de l’application. Cette méthode est désactivée pour un ordinateur exécutant un système d’exploitation 32 bits. Cette option est disponible uniquement lorsque vous exécutez le service cloud dans Azure, pas localement dans l’émulateur cluster.

- **Allocation de mémoire .NET** - cette méthode collecte des données d’allocation de mémoire .NET Framework à l’aide de l’échantillon profil méthode. Les informations collectées incluent le numéro et la taille des objets alloués.

- **Accès concurrentiels au** - cette méthode collecte des données de conflit de ressources et processus et thread données d’exécution sont utiles pour analyser les applications multi-threads et plusieurs processues. La méthode de concurrence collecte des données pour chaque événement que bloque l’exécution de votre code, par exemple lorsqu’un thread attend verrouillé l’accès à une ressource d’application à libérer. Cette méthode est utile pour analyser les applications multithread.

- Vous pouvez également activer la **Couche Interaction profil**, qui fournit des informations supplémentaires sur les temps d’exécution de ADO.NET synchrone des appels dans les fonctions des applications multicouches communiquent avec une ou plusieurs bases de données. Vous pouvez collecter les données d’interaction de couche avec une des méthodes de profilage. Pour plus d’informations sur la couche interaction profil, voir [Couche Interactions afficher](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Configuration des paramètres de profilage

L’illustration suivante montre comment configurer vos paramètres profilage à partir de la boîte de dialogue Publier les applications Azure.

![Configurer les paramètres de profil](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] Pour activer la case à cocher **Activer le profil** , vous devez le Générateur de profils installé sur l’ordinateur local que vous utilisez pour publier votre service cloud. Par défaut, le Générateur de profils est installé lorsque vous installez Visual Studio.

### <a name="to-configure-profiling-settings"></a>Pour configurer les paramètres de profilage

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour votre projet Azure, puis sur **Publier**. Pour plus d’informations sur la publication d’un service cloud, voir [publication d’un nuage du service en utilisant les outils Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Dans la boîte de dialogue **Publier Azure Application** , choisissez l’onglet **Paramètres avancés** .

1. Pour activer le profil, activez la case à cocher **Activer profil** .

1. Pour configurer vos paramètres de profilage, cliquez sur le lien **paramètres** . La boîte de dialogue Paramètres de profil s’affiche.

1. Dans les cases d’option **quelle méthode de profil que vous voulez utiliser** , choisissez le type de profil que vous avez besoin.

1. Pour collecter les données de profilage couche interaction, activez la case à cocher **Activer le profil d’Interaction de couche** .

1. Pour enregistrer les paramètres, cliquez sur le bouton **OK** .

    Lorsque vous publiez cette application, ces paramètres sont utilisés pour créer la session de profilage pour chaque rôle.

## <a name="viewing-profiling-reports"></a>Affichage des rapports de profil

Une session de profilage est créée pour chaque instance d’un rôle dans votre service cloud. Pour afficher vos rapports profilage de chaque session à partir de Visual Studio, vous pouvez afficher la fenêtre Explorateur de serveurs, puis sélectionnez le nœud Azure calculer pour sélectionner une instance d’un rôle. Vous pouvez ensuite afficher le rapport profilage comme le montre l’illustration suivante.

![Affichage profil rapport à partir d’Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Pour afficher les rapports de profilage

1. Pour afficher la fenêtre Explorateur de serveurs dans Visual Studio, dans la barre de menus choisissez Affichage, Explorateur de serveurs.

1. Sélectionnez le nœud de calcul Azure, puis le nœud de déploiement d’Azure du service cloud que vous avez choisi de profil lorsque vous avez publié dans Visual Studio.

1. Pour afficher les rapports de profilage d’une instance, sélectionnez le rôle dans le service, ouvrir le menu contextuel d’une instance spécifique, puis **Afficher le rapport de profil**.

    Le rapport, un fichier .vsp, n’est pas téléchargé à partir d’Azure, et l’état du téléchargement s’affiche dans le journal d’activité Azure. Une fois le téléchargement terminé, le rapport profilage apparaît dans un onglet de l’éditeur Visual Studio nommé <Role name> _<Instance Number>_ <identifier>.vsp. Données de synthèse pour le rapport s’affiche.

1. Pour afficher les différents affichages du rapport, dans la liste Affichage actuel, choisissez le type d’affichage souhaité. Pour plus d’informations, voir [Vues de rapport outils de profil](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Étapes suivantes

[Services de Cloud débogage](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publication d’un Service Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

