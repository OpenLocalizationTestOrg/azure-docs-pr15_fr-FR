<properties
    pageTitle="Le débogage des Applications Azure dans Éclipse"
    description="En savoir plus sur les Applications Azure débogage à l’aide de la boîte à outils Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Le débogage des Applications Azure dans Éclipse #

À l’aide de la boîte à outils Azure pour Eclipse, vous pouvez déboguer vos applications si elles sont en cours d’exécution dans Azure ou dans l’émulateur cluster si vous utilisez Windows en tant que votre système d’exploitation. L’image suivante montre les propriétés de **débogage** boîte de dialogue utilisée pour activer le débogage distant :

![][ic719504]

Ce didacticiel suppose que vous disposez déjà d’une application qui a été créée et est familiarisé avec l’émulateur cluster et du déploiement à Azure.

Nous allons utiliser l’application à partir du didacticiel [à l’aide de la bibliothèque d’exécution Service Azure dans JSP][] comme point de départ pour cette rubrique. Avant de commencer, créez cette application si vous ne le n'avez pas déjà fait.

## <a name="to-debug-your-application-while-running-in-azure"></a>Déboguer votre application lors de l’exécution dans Azure ##

>[AZURE.WARNING] Prise en charge actuelle de la boîte à outils pour le débogage Java distant est destiné principalement pour les déploiements en cours d’exécution dans l’émulateur cluster Azure. Étant donné que la connexion de débogage n’est pas sécurisée, vous n’activez pas le débogage distant dans les déploiements de production. Si vous devez déboguer une application en cours d’exécution dans le cloud Azure, utilisez un déploiement intermédiaire, mais savoir que tout accès non autorisé à votre session de débogage est possible si quelqu'un connaît l’adresse IP de votre déploiement cloud, même s’il s’agit d’un déploiement intermédiaire.

1. Générez votre projet pour le test dans l’émulateur : Explorateur dans Eclipse de projets, avec le bouton droit **MyAzureProject**, cliquez sur **Propriétés**, cliquez sur **Azure**et définissez **Générer** au **déploiement vers le cloud**.
1. Reconstruire votre projet : dans le menu Eclipse, cliquez sur **projet**, puis cliquez sur **Créer toutes les**.
1. Déployez votre application sur *les intermédiaires* dans Azure.
    >[AZURE.IMPORTANT] Comme indiqué ci-dessus, il est vivement recommandé que vous déboguez dans l’émulateur cluster dans la plupart des cas, puis déboguez dans l’environnement intermédiaire uniquement si le débogage supplémentaire est nécessaire. Il est recommandé de ne pas déboguer dans l’environnement de production.
1. Une fois que votre déploiement est prêt dans Azure, obtenir le nom DNS pour le déploiement à partir du [Portail de gestion Azure][]. Un déploiement intermédiaire a un nom DNS dans le formulaire de http://*&lt;guid&gt;*. cloudapp.net, où * &lt;guid&gt; * est une valeur GUID affectée par Azure.
1. Dans l’Explorateur de projets de Eclipse, avec le bouton droit **WorkerRole1**et cliquez sur **Azure**, puis cliquez sur **débogage**.
1. Dans la boîte de dialogue **Propriétés de débogage WorkerRole1** :
    1. Vérifier **Activer le débogage distant pour ce rôle.**
    1. De **point de terminaison de saisie à utiliser**, utilisez **débogage (public : 8090, 8090 : privé)**.
    1. Assurez-vous de **Que Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion débogueur** n’est pas cochée.
        >[AZURE.IMPORTANT] L’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion débogueur** est destinée pour avancé débogage des scénarios dans l’émulateur cluster uniquement (pas pour les déploiements dans le cloud). Si l’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion débogueur** est utilisée, il sera suspendre des processus de démarrage du serveur jusqu'à ce que le débogueur Eclipse est connecté à sa machine virtuelle Java. Pendant que vous pouvez utiliser cette option pour une session de débogage à l’aide de l’émulateur cluster, ne l’utilisez pas pour une session de débogage dans un déploiement de cloud. Échec de l’initialisation d’un serveur a lieu dans une tâche de démarrage Azure, et le cloud Azure ne font pas points de terminaison publics disponible jusqu'à ce que le démarrage est terminée. Par conséquent, un processus de démarrage sera termine pas correctement si cette option est activée dans un déploiement cloud, parce qu’il ne sera pas en mesure de recevoir une connexion à partir d’un client Eclipse externe.
1. Cliquez sur **créer des Configurations de débogage**.
1. Dans la boîte de dialogue **Configuration de débogage Azure** :
    1. Pour **project Java déboguer**, sélectionnez le projet **MyHelloWorld** .
    1. Pour **configurer le débogage pour**, cochez **Azure cloud (mise en attente)**.
    1. Vérifiez que **Azure calculer émulateur** n’est pas cochée.
    1. Pour **hôte**, entrez le nom DNS de votre déploiement intermédiaire, mais sans le précédent **http://**. Par exemple (utiliser votre GUID à la place le GUID illustré ici) : **4e616d65-6f6e-6 d 65-6973-526f62657274.cloudapp.net**
1. Cliquez sur **OK** pour fermer la boîte de dialogue **Configuration de débogage Azure** .
1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de débogage WorkerRole1** .
1. Si vous n’avez pas un point d’arrêt déjà défini dans index.jsp, définissez-la :
    1. Au sein de l’Explorateur de projets de Eclipse, développez **MyHelloWorld**, développez le **contenu Web**, puis double-cliquez sur **index.jsp**.
    1. Dans index.jsp, avec le bouton droit dans la barre bleue à gauche de votre code Java et cliquez sur les **Points d’arrêt bascule**, comme illustré ci-dessous : ![][ic551537]
1. Dans le menu de Eclipse, cliquez sur **exécuter** , puis sur **Configurations déboguer**.
1. Dans la boîte de dialogue **Configurations déboguer** , développez **Application Java distante** dans le volet gauche, sélectionnez **Azure Cloud (WorkerRole1)**, puis cliquez sur **Déboguer**.
1. Dans votre navigateur, exécutez l’application intermédiaire, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, en substituant le GUID de votre nom de DNS pour * &lt;guid&gt;*. Si vous y êtes invité par une boîte de dialogue **Confirmer le commutateur Perspective** , cliquez sur **Oui**. Votre session de débogage doit s’exécuter maintenant à la ligne de code où le point d’arrêt a été défini.

>[AZURE.NOTE] Si vous tentez de démarrer une télécommande débogage connexion à un déploiement qui comporte plusieurs instances de rôle en cours d’exécution, vous ne pouvez pas contrôler actuellement quelle instance le débogueur sera initialement connecté à, comme l’équilibrage de charge Azure sélectionnent une instance de manière aléatoire. Une fois que vous êtes connecté à cette instance, cependant, vous pouvez continuer la même instance de débogage. Remarque en outre, s’il existe une période d’inactivité de plus de 4 minutes (par exemple, lorsque vous êtes arrêté à un point d’arrêt trop longtemps), Azure peut fermer la connexion.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Déboguer une instance de rôle spécifique dans un déploiement de plusieurs instances ##

Lorsque votre déploiement est en cours d’exécution dans le cloud, vous seront probablement exécutées il plusieurs cluster, ou rôle, instances. Cela vous permet de tirer parti de garantie de disponibilité Azure 99,95 % et à l’échelle votre application.

Dans ces scénarios, vous devrez peut-être à distance déboguer votre application Java dans une instance de rôle spécifique. Toutefois, si vous activez uniquement un standard d’entrée point de terminaison pour le débogage, l’équilibrage de charge Azure rend pratiquement impossible de vous connecter le débogueur sur une instance de rôle spécifique. Il s’au lieu de cela vous connecte à une instance de rôle elle choisit de manière aléatoire.

Il s’agit du type de scénario où tirer parti des points de terminaison d’entrée instance facilitera pour pouvoir déboguer une instance de rôle spécifique.

Supposons que vous envisagez d’exécuter jusqu'à 5 instances de rôle de votre déploiement. À l’aide de la page de propriétés de **points de terminaison de** la boîte de dialogue de propriétés de rôle, créez un point de terminaison instance d’entrée et affectez-le à une plage de ports publics, au lieu d’un numéro de port unique. Par exemple, dans la zone d’entrée de **port Public** , spécifiez **81 85**.

Une fois que vous déployez votre application avec ce point de terminaison instance, Azure sera affecter un numéro de port unique à partir de la plage à chacune des instances de rôle. Ensuite, afin de déterminer quelle instance est-il attribué le numéro du port, vous pouvez utiliser la variable d’environnement**_PUBLICPORT** *InstanceEndpointName*(où *InstanceEndpointName* est le nom que vous avez affecté lorsque vous avez créé le point de terminaison instance) configuré automatiquement par le Kit de ressources dans votre déploiement (par exemple, en renvoyant sa valeur dans le pied de page d’une page Web, afin de vous pouvez le lire lorsque vous naviguez vers celui-ci).

Une fois que vous savez quel numéro de port public cette instance a été affectée, vous pouvez le faire référence dans la configuration de débogage dans Eclipse, par apposer au nom d’hôte de votre service. Vous activez le débogueur Eclipse pour vous connecter à cette instance spécifique et pas toutes les autres instances.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Windows uniquement : déboguer votre application lors de l’exécution dans l’émulateur cluster ##

>[AZURE.NOTE] L’émulateur Azure est uniquement disponible sur Windows. Ignorer cette section si vous utilisez un système d’exploitation différent de Windows. 

1. Générez votre projet pour le test dans l’émulateur : Explorateur dans Eclipse de projets, avec le bouton droit **MyAzureProject**, cliquez sur **Propriétés**, cliquez sur **Azure**et définissez **Générer pour** test **dans émulateur**.
1. Reconstruire votre projet : dans le menu Eclipse, cliquez sur **projet**, puis cliquez sur **Créer toutes les**.
1. Dans l’Explorateur de projets de Eclipse, avec le bouton droit **WorkerRole1**et cliquez sur **Azure**, puis cliquez sur **débogage**.
1. Dans la boîte de dialogue **Propriétés de débogage WorkerRole1** :
    1. Vérifier **Activer le débogage distant pour ce rôle.**
    1. De **point de terminaison de saisie à utiliser**, utilisez le point de terminaison par défaut généré automatiquement par le Kit de ressources, répertoriée sous forme de **débogage (public : 8090, 8090 : privé)**.
    1. Vérifiez que l’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion débogueur** n’est pas cochée.
        >[AZURE.IMPORTANT] L’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion débogueur** est destinée pour avancé débogage des scénarios dans l’émulateur cluster uniquement (pas pour les déploiements dans le cloud). Si l’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion débogueur** est utilisée, il sera suspendre des processus de démarrage du serveur jusqu'à ce que le débogueur Eclipse est connecté à sa machine virtuelle Java. Pendant que vous pouvez utiliser cette option pour une session de débogage à l’aide de l’émulateur cluster, ne l’utilisez pas pour une session de débogage dans un déploiement de cloud. Échec de l’initialisation d’un serveur a lieu dans une tâche de démarrage Azure, et le cloud Azure ne font pas points de terminaison publics disponible jusqu'à ce que le démarrage est terminée. Par conséquent, un processus de démarrage sera termine pas correctement si cette option est activée dans un déploiement cloud, parce qu’il ne sera pas en mesure de recevoir une connexion à partir d’un client Eclipse externe.
1. Cliquez sur **créer des Configurations de débogage**.
1. Dans la boîte de dialogue **Configuration de débogage Azure** :
    1. Pour **project Java déboguer**, sélectionnez le projet **MyHelloWorld** .
    1. Pour **configurer le débogage pour**, consultez **Azure calculer émulateur**.
1. Cliquez sur **OK** pour fermer la boîte de dialogue **Configuration de débogage Azure** .
1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de débogage WorkerRole1** .
1. Définir un point d’arrêt dans index.jsp :
    1. Au sein de l’Explorateur de projets de Eclipse, développez **MyHelloWorld**, développez le **contenu Web**, puis double-cliquez sur **index.jsp**.
    1. Dans index.jsp, avec le bouton droit dans la barre bleue à gauche de votre code Java et cliquez sur les **Points d’arrêt bascule**, comme illustré ci-dessous : ![][ic551537]

       Un point d’arrêt est défini si vous voyez une icône de point d’arrêt dans la barre bleue à gauche de votre code Java.
1. Démarrez l’application dans l’émulateur de calcul en cliquant sur le bouton **exécuter dans Azure émulateur** dans la barre d’outils Azure.
1. Dans le menu de Eclipse, cliquez sur **exécuter** , puis sur **Configurations déboguer**.
1. Dans la boîte de dialogue **Configurations déboguer** , développez **Application Java distante** dans le volet gauche, sélectionnez **Émulateur Azure (WorkerRole1)**, puis cliquez sur **Déboguer**.
1. Une fois l’émulateur cluster indique que votre application est exécutée, au sein de votre navigateur, exécutez **Http://localhost : 8080/MyHelloWorld**.
    Si vous y êtes invité par une boîte de dialogue **Confirmer le commutateur Perspective** , cliquez sur **Oui**.
    Votre session de débogage doit s’exécuter maintenant à la ligne de code où le point d’arrêt a été défini.

Cela vous montrer comment déboguer dans l’émulateur cluster ; la section suivante vous montre comment déboguer une application déployée vers Azure.

## <a name="debugging-notes"></a>Débogage des Notes ##

* Après le débogage, vous pouvez changer la perspective de **Déboguer** pour **Java** via en cliquant sur le menu de Eclipse, en cliquant sur **fenêtre**, **Perspective ouvert**, sélectionnez la perspective que vous voulez utiliser.
* Pour activer le débogage distant dans GlassFish, n’utilisez pas la fonctionnalité de configuration de débogage à distance du Kit de ressources Azure pour Eclipse. À la place configurer GlassFish manuellement. En raison de la manière que glassfish traite les options Java prédéfinies dans les variables d’environnement, fonctionnalité de configuration de débogage à distance de la boîte à outils ne fonctionne pas correctement avec GlassFish. Si la fonctionnalité de configuration de débogage à distance de la boîte à outils est activée, il peut empêcher GlassFish de démarrer.

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Création d’une Application World Hello pour Azure dans Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[À l’aide de la bibliothèque d’exécution Service Azure dans JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
