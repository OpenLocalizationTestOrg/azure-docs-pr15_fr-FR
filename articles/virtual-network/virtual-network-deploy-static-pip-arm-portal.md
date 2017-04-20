<properties 
   pageTitle="Déployer un ordinateur virtuel avec une adresse IP statique public à l’aide du portail Azure dans le Gestionnaire de ressources | Microsoft Azure"
   description="Découvrez comment déployer des machines virtuelles avec une adresse IP statique publique à l’aide du portail de zure dans le Gestionnaire de ressources"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Déployer un ordinateur virtuel avec une adresse IP statique public à l’aide du portail Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Créer une machine virtuelle avec une adresse IP publique statique 

Pour créer une machine virtuelle avec une adresse IP statique publique dans le portail Azure, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez au [portail Azure](https://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Dans le coin supérieur gauche du portail, cliquez sur **Nouveau**>>**Calculer**>**Windows Server 2012 R2 centre de données**.
3. Dans la liste **Sélectionner un modèle de déploiement** , sélectionnez **Le Gestionnaire de ressources** et cliquez sur **créer**.
4. Dans la carte **Concepts de base** , entrez les informations de machine virtuelle comme indiqué ci-dessous, puis cliquez sur **OK**.

    ![Portail Azure - concepts de base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Dans la carte de **Choisir une taille** , cliquez sur **Standard A1** comme indiqué ci-dessous, puis cliquez sur **Sélectionner**.

    ![Portail Azure - choisir une taille](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Dans la carte de **paramètres** , cliquez sur **adresse IP publique**, puis dans la carte de **créer une adresse IP publique** , sous **affectation**, cliquez sur **statique** comme illustré ci-dessous. Puis sur **OK**.

    ![Portail Azure - créer adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Dans la carte de **paramètres** , cliquez sur **OK**.
8. Passez en revue la carte de **Résumé** , comme indiqué ci-dessous, puis cliquez sur **OK**.

    ![Portail Azure - créer adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Avez-vous remarqué la nouvelle mosaïque dans votre tableau de bord.

    ![Portail Azure - créer adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Une fois que la machine virtuelle est créée, la carte de **paramètres** s’affiche comme indiqué ci-dessous

    ![Portail Azure - créer adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)