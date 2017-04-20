
<properties
   pageTitle="Créer un Internet facing équilibrage de charge dans le modèle de déploiement classique à l’aide du portail classique Azure | Microsoft Azure"
   description="Apprenez à créer un Internet facing équilibrage de charge dans le modèle de déploiement classique à l’aide du portail classique Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Créer un Internet facing équilibrage de charge (classique) dans le portail classique Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [découvrir comment créer un Internet facing équilibrage de charge à l’aide du Gestionnaire de ressources Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurer un équilibrage de charge via Internet pour les machines virtuelles

Pour charger le trafic réseau solde à partir d’Internet machines virtuelles d’un service cloud, vous devez créer un ensemble d’équilibrage de charge. Cette procédure suppose que vous avez déjà créé les machines virtuelles et qu’ils sont tous dans un même service cloud.

**Pour configurer un ensemble d’équilibrage de charge pour les machines virtuelles**

1. Dans le portail Azure classique, cliquez sur **Machines virtuelles**, puis cliquez sur le nom d’un ordinateur virtuel dans l’ensemble d’équilibrage de charge.

2. Cliquez sur les **points de terminaison**, puis cliquez sur **Ajouter**.

3. Dans la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur la flèche droite.

4. Dans la page **spécifier les détails du point de terminaison** :

    * Dans la zone **nom**, tapez un nom pour le point de terminaison ou sélectionnez le nom dans la liste des points de terminaison prédéfinis pour les protocoles courantes.
    * Dans la zone **protocole**, sélectionnez le protocole requis par le type du point de terminaison TCP ou UDP, selon vos besoins.
    * Dans **les ports Public et privé**, tapez les numéros de port que vous souhaitez que la machine virtuelle à utiliser, selon vos besoins. Vous pouvez utiliser le port privé et les règles de pare-feu sur l’ordinateur virtuel pour rediriger le trafic de façon appropriée pour votre application. Le port privé peut être identique à celui public. Par exemple, pour un point de terminaison pour le trafic web (HTTP), vous pourriez affecter le port 80 au port publique et privé.

5. Sélectionnez **créer un jeu d’équilibrage de charge**, puis cliquez sur la flèche droite.

6. Dans la page **configurer l’ensemble d’équilibrage de charge** , tapez un nom pour l’ensemble d’équilibrage de charge et puis assigner les valeurs pour le comportement de sonde de l’équilibrage de charge Azure. L’équilibrage de charge utilise sondes pour déterminer si les ordinateurs virtuels dans l’ensemble d’équilibrage de charge sont disponibles pour recevoir le trafic entrant.

7. Activez la case à cocher pour créer le point de terminaison équilibrage de charge. Vous verrez **Oui** dans la colonne **nom du jeu d’équilibrage de charge** de la page **points de terminaison** de la machine virtuelle.

8. Dans le portail, cliquez sur **Machines virtuelles**et cliquez sur le nom d’un ordinateur virtuel supplémentaire dans l’ensemble d’équilibrage de charge, cliquez sur les **points de terminaison**, puis cliquez sur **Ajouter**.

9. Dans la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur le **point de terminaison d’ajouter un jeu d’équilibrage de charge existant**, sélectionnez le nom de l’ensemble d’équilibrage de charge et puis cliquez sur la flèche droite.

10. Dans la page **spécifier les détails du point de terminaison** , tapez un nom pour le point de terminaison, puis cliquez sur la coche.

Pour les ordinateurs virtuels supplémentaires dans le jeu d’équilibrage de charge, répétez les étapes 8-10.



## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un programme d’équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

