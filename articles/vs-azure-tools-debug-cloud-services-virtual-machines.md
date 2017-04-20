<properties 
    pageTitle="Débogage d’un service cloud Azure ou machine virtuelle dans Visual Studio | Microsoft Azure"
    description="Débogage d’un Service Cloud ou Machine virtuelle dans Visual Studio"
    services="visual-studio-online"
    documentationCenter="na"
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

# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Débogage d’un service cloud Azure ou machine virtuelle dans Visual Studio

Visual Studio vous offre plusieurs options pour le débogage des services cloud Azure et machines virtuelles.



## <a name="debug-your-cloud-service-on-your-local-computer"></a>Déboguer votre service cloud sur votre ordinateur local

Vous pouvez gagner du temps et argent à l’aide de la Azure calcul émulateur déboguer votre service cloud sur un ordinateur local. Par le débogage d’un service localement avant de déployer, vous pouvez améliorer la fiabilité et les performances sans payer de temps à gérer. Toutefois, certaines erreurs se produisent uniquement lorsque vous exécutez un service cloud dans Azure lui-même. Vous pouvez déboguer ces erreurs si vous activez le débogage distant lorsque vous publiez votre service, puis joignez le débogueur à une instance de rôle.

L’émulateur simule le service Azure calculer et est exécuté dans votre environnement local afin que vous pouvez tester et déboguer votre service cloud avant de déployer. L’émulateur gère le cycle de vie de vos instances de rôles et offre un accès aux ressources simulés, par exemple de stockage local. Lorsque vous déboguez ou exécutez votre service à partir de Visual Studio, il démarre automatiquement l’émulateur comme une application d’arrière-plan, puis déploie votre service à l’émulateur. Vous pouvez utiliser l’émulateur pour afficher votre service lorsqu’elle s’exécute dans l’environnement local. Vous pouvez exécuter la version complète ou la version de l’émulateur expresse. (La version de l’émulateur expresse commençant par 2.3 Azure, est la valeur par défaut.) Voir [émulateur à l’aide de Express pour exécuter et déboguer un Service Cloud localement](https://msdn.microsoft.com/library/dn339018.aspx).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Pour déboguer votre service cloud sur votre ordinateur local

1. Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** pour exécuter votre projet de service cloud Azure. Vous pouvez également appuyer sur F5. Vous voyez un message qui démarre l’émulateur de calcul. Lorsque l’émulateur démarre, l’icône de barre d’état système confirme l’accès.

    ![Émulateur Azure dans la barre d’état système](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

1. Afficher l’interface utilisateur pour l’émulateur cluster en ouvrant le menu contextuel de l’icône Azure dans la zone de notification et sélectionnez **Afficher l’interface utilisateur émulateur de calcul**.

    Le volet gauche de l’interface utilisateur affiche les services qui sont actuellement déployés sur l’émulateur cluster et les instances de rôle chaque service est en cours d’exécution. Vous pouvez choisir le service ou les rôles pour afficher le cycle de vie, journalisation et informations de diagnostic dans le volet droit. Si vous placez le focus dans la marge supérieure d’une fenêtre incluse, il se développe pour remplir le volet de droite.

1. Lancer l’application en sélectionnant des commandes dans le menu **Déboguer** et en définissant des points d’arrêt dans votre code. Pendant que vous parcourez l’application dans le débogueur, les volets sont mis à jour avec l’état actuel de l’application. Lorsque vous arrêtez le débogage, le déploiement des applications est supprimé. Si votre application inclut un rôle web et que vous avez défini la propriété d’action de démarrage pour démarrer le navigateur web, Visual Studio démarre votre application web dans le navigateur. Si vous modifiez le nombre d’instances d’un rôle dans la configuration du service, vous devez arrêter votre service cloud, puis redémarrez débogage afin que vous puissiez déboguer ces nouvelles instances de ce rôle.

    **Remarque :** Lorsque vous arrêtez en cours d’exécution ou débogage votre service, l’émulateur de calcul locale et émulateur de stockage ne sont pas arrêtés. Vous devez les arrêter explicitement à partir de la zone de notification.


## <a name="debug-a-cloud-service-in-azure"></a>Déboguer un service cloud dans Azure

Pour déboguer un service cloud depuis un ordinateur distant, vous devez activer cette fonctionnalité explicitement lorsque vous déployez votre service cloud afin que requis services (msvsmon.exe, par exemple) sont installés sur les ordinateurs virtuels qui s’exécutent vos instances de rôles. Si vous n’avez pas activer le débogage distant lorsque vous avez publié le service, vous devez publier à nouveau le service avec le débogage distant est activée.

Si vous activez le débogage distant d’un service cloud, il ne performances être dégradées ou payer des frais supplémentaires. N’utilisez pas le débogage distant sur un service de production, étant donné que les clients qui utilisent le service peuvent être affectées.

>[AZURE.NOTE] Lorsque vous publiez un service cloud à partir de Visual Studio, vous pouvez activer **IntelliTrace** pour tous les rôles dans ce service cibler .NET Framework 4 ou le .NET Framework 4.5. En utilisant **IntelliTrace**, vous pouvez examiner les événements qui se sont produites dans une instance de rôle par le passé et reproduire le contexte à ce stade. Voir [débogage d’un service cloud publiés avec IntelliTrace et Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) et [à l’aide de IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Pour activer le débogage à distance d’un service cloud

1. Ouvrir le menu contextuel pour le projet Azure, puis sur **Publier**.

1. Sélectionnez l’environnement **intermédiaire** et la configuration de **débogage** .

    Ce n'est qu’une indication. Vous pouvez choisir d’exécuter votre environnement de test dans un environnement de Production. Toutefois, vous pouvez affecter les utilisateurs si vous activez le débogage distant sur l’environnement de Production. Vous pouvez choisir la configuration Release, mais la configuration de débogage qui rend le débogage plus facilement.

    ![Cliquez sur la configuration de débogage](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

1. Suivez la procédure habituelle, mais activez la case à cocher **Activer le débogueur distant pour tous les rôles** sous l’onglet **Paramètres avancés** .

    ![Configuration de débogage](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Pour joindre le débogueur à un service cloud dans Azure

1. Dans l’Explorateur de serveurs, développez le nœud de votre service cloud.

1. Ouvrir le menu contextuel pour le rôle ou d’une instance de rôle auquel vous voulez joindre et sélectionnez **Joindre débogueur**.

    Si vous déboguez un rôle, le débogueur Visual Studio se connecte à chaque instance de ce rôle. Le débogueur s’arrête sur un point d’arrêt pour la première instance de rôle qui s’exécute cette ligne de code et remplit les conditions de ce point d’arrêt. Si vous déboguez une instance, l’attache débogueur à uniquement cette instance et s’arrête à un point d’arrêt uniquement lorsque cette instance spécifique s’exécute cette ligne de code et répondent aux conditions de point d’arrêt.

    ![Attacher débogueur](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

1. Une fois que le débogueur est attaché à une instance, débogage comme d’habitude. Le débogueur attache automatiquement au processus hôte approprié pour votre rôle. En fonction de ce que le rôle est, le débogueur attache w3wp.exe, WaWorkerHost.exe ou WaIISHost.exe. Pour vérifier le processus auquel le débogueur est attaché, développez le nœud instance dans l’Explorateur de serveurs. Pour plus d’informations sur les processus Azure, consultez [Architecture de rôle Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

    ![Sélectionnez la boîte de dialogue code type](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Pour identifier les processus auquel le débogueur est joint, ouvrez la boîte de dialogue processus par, dans la barre de menus, en choisissant Déboguer, Windows, processus. (Clavier : Ctrl + Alt + Z) Pour détacher un processus spécifique, ouvrir le menu contextuel, puis sélectionnez **Détacher le processus**. Ou, recherchez le nœud instance dans l’Explorateur de serveur, recherchez le processus, ouvrir le menu contextuel, puis sélectionnez **Détacher le processus**.

    ![Déboguer des processus](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

>[AZURE.WARNING] Éviter les taquets de temps aux points d’arrêt à distance débogage. Azure traite un processus qui est arrêté pendant plus de quelques minutes sous la forme ne répond pas et n’envoie le trafic à cette instance. Si vous cessez trop longtemps, msvsmon.exe détacher du processus.

Pour détacher le débogueur de tous les processus dans votre instance ou le rôle, ouvrir le menu contextuel pour le rôle ou l’instance que vous êtes déboguer, puis sur **Détacher débogueur**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Limitations de débogage à distance dans Azure

À partir d’Azure SDK 2.3, débogage distant présente les limitations suivantes.

- Avec le débogage distant activé, vous ne pouvez publier un service cloud dans lequel n’importe quel rôle comporte plus de 25 instances.

- Le débogueur utilise les ports 30400 à 30424, 31400 à 31424 et 32400 à 32424. Si vous essayez d’utiliser un de ces ports, vous ne pourrez publier votre service, et un des messages d’erreur suivants s’affichent dans le journal d’activité pour Azure : 

    - Erreur lors de la validation du fichier .cscfg par rapport au fichier .csdef. 
    La plage de port réservé 'plage' point de terminaison Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector du rôle 'rôle' chevauche une port déjà défini ou une plage.
    - Échoué de l’allocation. Réessayez plus tard, essayez de réduire la taille de mémoire virtuelle ou le nombre d’instances de rôle ou essayez de déploiement à une zone différente.


## <a name="debugging-azure-virtual-machines"></a>Débogage machines virtuelles Azure

Vous pouvez déboguer des programmes s’exécutant sur Azure machines virtuelles à l’aide de l’Explorateur de serveur dans Visual Studio. Lorsque vous activez le débogage distant sur une machine virtuelle Azure, Azure installe l’extension de débogage à distance sur l’ordinateur virtuel. Ensuite, vous pouvez attacher des processus sur l’ordinateur virtuel et déboguer comme d’habitude.

>[AZURE.NOTE] Machines virtuelles créées dans la pile Gestionnaire Azure ressource peut être déboguer à distance à l’aide de Cloud Explorer dans Visual Studio 2015. Pour plus d’informations, voir [Gestion des ressources Azure avec l’Explorateur de Cloud](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Déboguer une machine virtuelle Azure

1. Dans l’Explorateur de serveurs, développez le nœud Machines virtuelles et sélectionnez le nœud de la machine virtuelle que vous souhaitez déboguer.

1. Ouvrir le menu contextuel, puis sélectionnez **Activer le débogage**. Lorsque vous êtes invité si vous ne savez si vous voulez activer le débogage sur l’ordinateur virtuel, sélectionnez **Oui**.

    Azure installe l’extension de débogage à distance sur l’ordinateur virtuel pour activer le débogage.

    ![Machine virtuelle activer la commande de débogage](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Journal d’activité Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Après que l’extension de débogage à distance l’installation terminée, ouvrez le menu contextuel de la machine virtuelle et sélectionnez **Joindre débogueur...**

    Azure Obtient une liste de processus sur l’ordinateur virtuel et illustrées dans la boîte de dialogue processus attacher au.

    ![Ajouter la commande débogueur](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Dans la boîte de dialogue **Attacher au processus** , sélectionnez **Sélectionner** pour limiter la liste des résultats pour afficher uniquement les types de code que vous souhaitez déboguer. Vous pouvez déboguer le code 32 ou 64 bits géré, code natif ou les deux.

    ![Sélectionnez la boîte de dialogue code type](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Sélectionnez les processus que vous souhaitez déboguer sur l’ordinateur virtuel, puis sélectionnez **joindre**. Par exemple, vous pouvez choisir le processus w3wp.exe si vous souhaitez déboguer une application web sur l’ordinateur virtuel. Pour plus d’informations, voir [Déboguer un ou plusieurs processus dans Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) et [l’Architecture de rôle Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Créer un projet web et une machine virtuelle pour le débogage

Avant de publier votre projet Azure, vous peuvent être utiles à tester dans un environnement de contenus qui prend en charge le débogage et tester des scénarios, et l’endroit où vous pouvez installer test et analyse des programmes. Pour faire cela est déboguer à distance votre application sur un ordinateur virtuel.

Projets ASP.NET Visual Studio offrent une option pour créer une machine virtuelle pratique que vous pouvez utiliser pour tester l’application. La machine virtuelle inclut souvent nécessaire de points de terminaison tels que PowerShell et WebDeploy Bureau à distance.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Pour créer un projet web et une machine virtuelle pour le débogage

1. Dans Visual Studio, créez une nouvelle Application Web ASP.NET.

1. Dans la boîte de dialogue Nouveau projet ASP.NET, dans la section Azure, choisissez **Machine virtuelle** dans la zone de liste déroulante. Laissez la case à cocher **créer des ressources distantes** sélectionnée. Sélectionnez **OK** pour continuer.

    La boîte de dialogue **machine virtuelle de créer sur Azure** s’affiche.


    ![Créer la boîte de dialogue de projet web ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Remarque :** Vous êtes invité à se connecter à votre compte Azure si vous n’êtes pas déjà connecté.

1. Sélectionnez les différents paramètres de la machine virtuelle, puis sélectionnez **OK**. Pour plus d’informations, consultez [Machines virtuelles]( http://go.microsoft.com/fwlink/?LinkId=623033) .

    Le nom que vous entrez pour nom DNS sera le nom de la machine virtuelle. 

    ![Créer des machines virtuelles sur boîte de dialogue Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure crée la machine virtuelle, puis les dispositions et configure les points de terminaison, tels que le Bureau à distance et déploiement Web



1. Une fois que la machine virtuelle est entièrement configurée, sélectionnez nœud de la machine virtuelle dans l’Explorateur de serveur.

1. Ouvrir le menu contextuel, puis sélectionnez **Activer le débogage**. Lorsque vous êtes invité si vous ne savez si vous voulez activer le débogage sur l’ordinateur virtuel, sélectionnez **Oui**. 

    Azure installe l’extension de débogage à distance à la machine virtuelle pour activer le débogage.

    ![Machine virtuelle activer la commande de débogage](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Journal d’activité Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Publier votre projet en tant que sous forme de plan dans [Comment : déployer un Web Project à l’aide de publication en un clic dans Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Étant donné que vous souhaitez déboguer sur l’ordinateur virtuel, dans la page **paramètres** de l’Assistant **Publier le site Web** , sélectionnez **Déboguer** en tant que la configuration. Cela permet de garantir que les symboles de code sont disponibles pendant le débogage.

    ![Paramètres de publication](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

1. Dans les **Options de publication de fichier**, sélectionnez **Supprimer les fichiers supplémentaires à la destination** si le projet a déjà été déployé à un moment antérieur.

1. Une fois que le projet publie, dans le menu contextuel de la machine virtuelle dans l’Explorateur de serveur, sélectionnez **Joindre débogueur...**

    Azure Obtient une liste de processus sur l’ordinateur virtuel et illustrées dans la boîte de dialogue processus attacher au.

    ![Ajouter la commande débogueur](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Dans la boîte de dialogue **Attacher au processus** , sélectionnez **Sélectionner** pour limiter la liste des résultats pour afficher uniquement les types de code que vous souhaitez déboguer. Vous pouvez déboguer le code 32 ou 64 bits géré, code natif ou les deux.

    ![Sélectionnez la boîte de dialogue code type](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Sélectionnez les processus que vous souhaitez déboguer sur l’ordinateur virtuel, puis sélectionnez **joindre**. Par exemple, vous pouvez choisir le processus w3wp.exe si vous souhaitez déboguer une application web sur l’ordinateur virtuel. Pour plus d’informations, voir [Déboguer un ou plusieurs processus dans Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) .

## <a name="next-steps"></a>Étapes suivantes

- Utiliser **Intellitrace** pour collecter un journal d’appels et d’événements à partir d’un serveur de publication. Voir [débogage d’un Service Cloud publiés avec IntelliTrace et Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
- Utiliser les **Diagnostics de Azure** pour enregistrer des informations détaillées à partir de code qui s’exécutent dans des rôles, si les rôles sont exécutent dans l’environnement de développement ou dans Azure. Consultez [collecte de données à l’aide des Diagnostics Azure l’enregistrement](http://go.microsoft.com/fwlink/p/?LinkId=400450).
