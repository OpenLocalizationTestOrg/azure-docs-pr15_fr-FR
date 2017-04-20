<properties
   pageTitle="Créer un programme d’équilibrage de charge interne dans le Gestionnaire de ressources à l’aide du portail Azure | Microsoft Azure"
   description="Apprenez à créer un programme d’équilibrage de charge interne dans le Gestionnaire de ressources à l’aide du portail Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Créer un programme d’équilibrage de charge interne dans le portail Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Créer un équilibrage de charge interne à l’aide du portail Azure

Procédez comme suit pour créer un programme d’équilibrage de charge interne à partir du portail Azure.

1. Ouvrez un navigateur, accédez au [portail Azure](http://portal.azure.com), puis connectez-vous avec votre compte Azure.
2. Dans le côté supérieur gauche de l’écran, cliquez sur **Nouveau** > **réseau** > **équilibrage de charge**.
3. Dans la carte **créer l’équilibrage de charge** , entrez un **nom** pour votre équilibrage de charge.
4. Sous **modèle**, cliquez sur **interne**.
5. Cliquez sur **réseau virtuel**, puis sélectionnez le réseau virtuel dans lequel vous voulez créer l’équilibrage de charge.

    >[AZURE.NOTE] Si vous ne voyez pas le réseau virtuel que vous voulez utiliser, vérifiez l' **emplacement** que vous utilisez pour l’équilibrage de charge et modifiez en conséquence.

6. Cliquez sur **sous-réseau**, puis sélectionnez le sous-réseau où vous voulez créer l’équilibrage de charge.
7. Sous **Attribution d’adresses IP**, cliquez sur **dynamique** ou **statique**, selon que vous voulez l’adresse IP de l’équilibrage de charge être corrigé (statique) ou non.

    >[AZURE.NOTE] Si vous choisissez d’utiliser une adresse IP statique, vous devrez fournir une adresse pour l’équilibrage de charge.

8. Sous **groupe de ressources** Indiquez le nom d’un nouveau groupe de ressources pour l’équilibrage de charge, ou cliquez sur **Sélectionnez existante** , sélectionnez un groupe de ressources existant.
9. Cliquez sur **créer**.

## <a name="configure-load-balancing-rules"></a>Configurer des règles d’équilibrage de charge

Après la création d’équilibrage de charge, accédez à la ressource équilibrage de charge afin de le configurer.
Vous devez commencer par configurer un pool d’adresses principale et une sonde avant de configurer une règle d’équilibrage de charge.

### <a name="step-1-configure-a-back-end-pool"></a>Étape 1 : Configurer un pool principale

1. Dans le portail Azure, cliquez sur **Parcourir** > **programmes d’équilibrage de charge**, puis cliquez sur l’équilibrage de charge que vous avez créé précédemment.
2. Dans la carte de **paramètres** , cliquez sur **pools d’arrière-plan**.
3. Dans la carte **pools d’adresses principal** , cliquez sur **Ajouter**.
4. Dans la carte **Ajouter principal pool** , entrez un **nom** pour le regroupement de serveur principal, puis cliquez sur **OK**.

### <a name="step-2-configure-a-probe"></a>Étape 2 : Configurer une sonde

1. Dans le portail Azure, cliquez sur **Parcourir** > **programmes d’équilibrage de charge**, puis cliquez sur l’équilibrage de charge que vous avez créé précédemment.
2. Dans la carte de **paramètres** , cliquez sur **teste**.
3. Dans la carte **teste** , cliquez sur **Ajouter**.
4. Dans la carte **sonde ajouter** , entrez un **nom** pour la sonde.
5. Sous **protocole**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications TCP basé).
6. Sous **Port**, spécifiez le port à utiliser lorsque vous accédez à la sonde.
7. Sous **chemin d’accès** (pour les tests de HTTP uniquement), spécifiez le chemin d’accès à utiliser comme une sonde.
8. Sous **intervalle de** spécifier la fréquence à laquelle sonde l’application.
9. Sous **seuil incorrecte**, spécifiez les tentatives combien doivent échouer avant que la machine virtuelle principal est marquée comme incorrecte.
10. Cliquez sur **OK** pour créer la sonde.

### <a name="step-3-configure-load-balancing-rules"></a>Étape 3 : Configurer les règles d’équilibrage de charge

1. Dans le portail Azure, cliquez sur **Parcourir** > **programmes d’équilibrage de charge**, puis cliquez sur l’équilibrage de charge que vous avez créé précédemment.
2. Dans la carte de **paramètres** , cliquez sur **règles de l’équilibrage de charge**.
3. Dans la carte **l’équilibrage de charge règles** , cliquez sur **Ajouter**.
4. Dans la carte **Ajouter charger équilibrage de la règle** , entrez un **nom** pour la règle.
5. Sous **protocole**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications TCP basé).
6. Sous **Port**, spécifiez que les clients port se connectent dans l’équilibrage de charge.
7. Sous **port principal**, spécifiez le port à utiliser dans le pool principal (en règle générale, le port d’équilibrage de charge et le port principal sont identiques).
8. Sous **serveur principal pool**, sélectionnez le pool principal que vous avez créé précédemment.
9. Sous **persistance d’une Session**, sélectionnez les modalités de sessions à conserver.
10. Sous **délai d’inactivité (en minutes)**, spécifiez le délai d’inactivité.
11. Sous **IP flottante (serveur direct retour)**, cliquez sur **désactivé** ou **activé**.
12. Cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)