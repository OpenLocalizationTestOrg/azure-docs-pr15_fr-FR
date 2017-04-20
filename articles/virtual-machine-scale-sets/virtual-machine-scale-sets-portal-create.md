<properties
    pageTitle="Créer une échelle de Machine virtuelle définie à l’aide du portail Azure | Microsoft Azure"
    description="Déployer des jeux d’échelle à l’aide du portail Azure."
    keywords="machine virtuelle échelle jeux" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Créer une échelle de Machine virtuelle définie à l’aide du portail Azure

Ce didacticiel montre qu’il est facile pour créer un ensemble d’échelle de Machine virtuelle en quelques minutes, à l’aide du portail Azure. Si vous n’avez un abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Sélectionnez l’image de la machine virtuelle à partir de la place de marché

À partir du portail, vous pouvez facilement déployer une échelle définie avec CentOS, CoreOS, Debian, Open Suse, Red chapeau Enterprise Linux, SUSE Linux Enterprise Server, serveur Ubuntu ou images Windows Server.

Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur web. Cliquez sur `New`, recherchez `scale set`, puis sélectionnez le `Virtual machine scale set` entrée :

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Créer la machine virtuelle Linux

Vous pouvez désormais utiliser les paramètres par défaut et créer rapidement la machine virtuelle.

* Sur le `Basics` carte, entrez un nom pour l’ensemble d’échelle. Ce nom devient la base du nom de domaine complet de l’équilibrage de charge devant le jeu d’échelle, vérifiez que le nom est unique dans l’ensemble d’Azure.

* Sélectionnez votre système d’exploitation souhaité tapez, entrez votre nom d’utilisateur souhaité, puis sélectionnez le type d’authentification que vous préférez. Si vous choisissez un mot de passe, il doit être au moins 12 caractères long et répondre à trois des quatre types de caractères suivants : une minuscule, une majuscule, un chiffre et un caractère spécial. Consultez plus d’informations sur la [Configuration requise de nom d’utilisateur et mot de passe](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Si vous choisissez `SSH public key`, assurez-vous de ne coller dans votre clé publique, pas votre clé privée :

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Entrez votre nom de groupe de ressources souhaité et l’emplacement, puis cliquez sur `OK`.

* Sur le `Virtual machine scale set service settings` carte : entrez votre étiquette de nom de domaine de votre choix (la base du nom de domaine complet pour l’équilibrage de charge devant le jeu d’échelle). Cette étiquette doit être unique dans l’ensemble d’Azure.

* Sélectionnez votre image de disque système d’exploitation souhaité, le nombre d’instances et la taille de l’ordinateur.

* Activer ou désactiver l’échelle automatique et configurer si activé :

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Sur le `Summary` carte, lors de la validation est terminée, cliquez sur `OK`.

* Enfin, sur le `Purchase` carte, cliquez sur `Purchase` pour démarrer l’échelle définie le déploiement.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Se connecter à une machine virtuelle dans l’ensemble de l’échelle

Une fois que votre jeu échelle est déployé, accédez à la `Inbound NAT Rules` onglet de l’équilibrage de charge pour l’ensemble d’échelle :

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Vous pouvez vous connecter à chaque ordinateur virtuel dans l’échelle à l’aide de ces règles NAT. Par exemple, pour un ensemble d’échelle de Windows, s’il existe une règle NAT sur le port entrant 50000, vous pouvez se connecter à cet ordinateur via RDP sur `<load-balancer-ip-address>:50000`. Pour un ensemble d’échelle Linux, vous devez vous connecter à l’aide de la commande `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Étapes suivantes

Pour des informations sur le déploiement des jeux d’échelle depuis l’interface, consultez [cette documentation](./virtual-machine-scale-sets-cli-quick-create.md).

Pour des informations sur le déploiement des jeux d’échelle à partir de PowerShell, consultez [cette documentation](./virtual-machine-scale-sets-windows-create.md).

Pour des informations sur le déploiement des jeux d’échelle de Visual Studio, consultez [cette documentation](./virtual-machine-scale-sets-vs-create.md).

Pour des informations générales, consultez la [page Vue d’ensemble de la documentation pour échelle définit](./virtual-machine-scale-sets-overview.md).

Pour plus d’informations, consultez la [page d’accueil pour les jeux d’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

