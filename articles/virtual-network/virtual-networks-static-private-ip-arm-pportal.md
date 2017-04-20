<properties 
   pageTitle="Comment définir une adresse IP privée statique en mode processeur à l’aide du portail Azure | Microsoft Azure"
   description="Présentation des adresses IP privée (DIP) et comment les gérer en mode processeur à l’aide du portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Comment définir une adresse IP privée dans le portail Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [gérer l’adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple ci-dessous vous attendez un environnement simple déjà créé. Si vous souhaitez exécuter les étapes qu’ils s’affichent dans ce document, commencez par créer l’environnement de test décrite dans [créer une vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Comment créer une machine virtuelle pour tester les adresses IP privées statiques

Vous ne pouvez pas définir une adresse IP privée statique lors de la création d’un ordinateur virtuel dans le mode de déploiement du Gestionnaire de ressources à l’aide du portail Azure. Vous devez d’abord créer la machine virtuelle, quantité définie son adresse IP privée d’être statique.

Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un VNet nommé *TestVNet*, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **Calculer** > **Windows Server 2012 R2 centre de données**, notez que la liste **Sélectionner un modèle de déploiement** déjà affiche **Le Gestionnaire de ressources**, puis cliquez sur **créer**, comme illustré dans l’illustration suivante.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Dans la carte **Concepts de base** , entrez le nom de la machine virtuelle doit être créé (*DNS01* dans notre scénario), le compte d’administrateur local et le mot de passe, comme illustré dans l’illustration suivante.

    ![Carte de concepts de base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Vérifiez que l' **emplacement** sélectionné est des *États-Unis centre*, puis cliquez sur **Select existantes** sous **groupe de ressources**, puis cliquez de nouveau **groupe de ressources** puis cliquez sur *TestRG*et puis cliquez sur **OK**.

    ![Carte de concepts de base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Dans la carte de **Choisir une taille** , sélectionnez **Standard A1**, puis cliquez sur **Sélectionner**.

    ![Cliquez sur une carte de taille](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. Dans la carte de **paramètres** , vérifiez que les propriétés suivantes sont définies sont définis avec les valeurs suivantes, puis cliquez sur **OK**.

    -**Compte de stockage**: *vnetstorage*
    - **Réseau**: *TestVNet*
    - **Sous-réseau**: *FrontEnd*

    ![Cliquez sur une carte de taille](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. Dans la carte de **Résumé** , cliquez sur **OK**. Avez-vous remarqué la vignette ci-dessous affichée dans votre tableau de bord.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment récupérer des informations statiques d’adresses IP privées pour une machine virtuelle

Pour afficher les informations d’adresse IP privées statiques de la machine virtuelle créée avec les étapes ci-dessus, exécutez les étapes décrites ci-dessous.

1. À partir du portail Azure Azure, cliquez sur **Rechercher tout** > **machines virtuelles** > **DNS01** > **tous les paramètres** > **interfaces réseau** , puis cliquez sur l’interface réseau uniquement répertoriées.

    ![Déploiement de mosaïque machine virtuelle](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Dans la carte **interface réseau** , cliquez sur **tous les paramètres** > **adresses IP** et notez les valeurs **d’une affectation** et **adresse IP** .

    ![Déploiement de mosaïque machine virtuelle](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment ajouter une adresse IP privée à un ordinateur virtuel existant
Pour ajouter une adresse IP privée statique de la machine virtuelle créée à l’aide de la procédure ci-dessus, procédez comme suit :

1. À partir de la carte **d’adresses IP** ci-dessus, cliquez sur **statique** sous **affectation**.
2. Tapez *192.168.1.101* pour **l’adresse IP**, puis cliquez sur **Enregistrer**.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Si, après avoir cliqué sur **Enregistrer** remarqué que l’affectation est toujours définie sur **dynamique**, cela signifie que l’adresse IP que vous avez tapé est déjà en cours d’utilisation. Essayez une autre adresse IP.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment supprimer une adresse IP privée d’une machine virtuelle
Pour supprimer l’adresse IP privée statique de la machine virtuelle créée ci-dessus, suivez la procédure ci-dessous.
    
1. À partir de la carte **d’adresses IP** ci-dessus, cliquez sur **dynamique** sous **affectation**, puis cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP public (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API REST de IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).