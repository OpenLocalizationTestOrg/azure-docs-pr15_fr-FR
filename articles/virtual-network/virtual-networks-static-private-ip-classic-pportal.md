<properties 
   pageTitle="Comment définir une adresse IP privée statique en mode classique à l’aide du portail Azure | Microsoft Azure"
   description="Présentation des adresses IP privée statique et comment les gérer en mode classique à l’aide du portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Comment définir une adresse IP privée statique (classique) dans le portail Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [gérer une adresse IP statique privée dans le modèle de déploiement du Gestionnaire de ressources](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple ci-dessous vous attendez un environnement simple déjà créé. Si vous souhaitez exécuter les étapes qu’ils s’affichent dans ce document, commencez par créer l’environnement de test décrite dans [créer une vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée statique lorsque vous créez une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un VNet nommé *TestVNet* avec une adresse IP statique privée de *192.168.1.101*, procédez comme suit :

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **Calculer** > **Windows Server 2012 R2 centre de données**, notez que la liste **Sélectionner un modèle de déploiement** déjà affiche **classique**, puis cliquez sur **créer**.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Dans la carte **Créer machine virtuelle** , entrez le nom de la machine virtuelle doit être créé (*DNS01* dans notre scénario), le compte d’administrateur local et le mot de passe.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Cliquez sur **Configuration facultatif** > **réseau** > **Réseau virtuel**, puis cliquez sur **TestVNet**. Si **TestVNet** n’est pas disponible, vérifiez que vous utilisez l’emplacement *Central US* et que vous avez créé l’environnement de test décrite au début de cet article.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. Dans la carte **réseau** , vérifiez que l’actuellement sélectionné constitue *FrontEnd*, puis cliquez sur **adresses IP**sous **Attribution d’adresses IP** et cliquez sur **statique**, puis entrez *192.168.1.101* pour **l’Adresse IP** , comme indiqué ci-dessous.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Cliquez sur **OK** dans la carte **d’adresses IP** , puis cliquez sur **OK** dans la carte **réseau** , puis cliquez sur **OK** dans la carte de **configuration de la facultative** .
7. Dans la carte **Machine virtuelle créer** , cliquez sur **créer**. Avez-vous remarqué la vignette ci-dessous affichée dans votre tableau de bord.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment récupérer des informations statiques d’adresses IP privées pour une machine virtuelle

Pour afficher les informations d’adresse IP privées statiques de la machine virtuelle créée avec les étapes ci-dessus, exécutez les étapes décrites ci-dessous.

1. À partir du portail Azure Azure, cliquez sur **Rechercher tout** > **machines virtuelles (classique)** > **DNS01** > **tous les paramètres** > **adresses IP** et notez l’attribution d’adresses IP et d’adresses IP comme indiqué ci-dessous.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment supprimer une adresse IP privée d’une machine virtuelle
Pour supprimer l’adresse IP privée statique de la machine virtuelle créée ci-dessus, suivez les étapes ci-dessous.
    
1. À partir de la carte **d’adresses IP** ci-dessus, cliquez sur **dynamique** vers la droite d' **Attribution d’adresses IP**, puis cliquez sur **Enregistrer**, puis cliquez sur **Oui**.

    ![Créer des machines virtuelles portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Comment ajouter une adresse IP privée à un ordinateur virtuel existant
Pour ajouter une adresse IP privée statique de la machine virtuelle créée à l’aide de la procédure ci-dessus, procédez comme suit :

1. À partir de la carte **d’adresses IP** ci-dessus, cliquez sur **statique** à droite de **l’Attribution d’adresses IP**.
2. Tapez *192.168.1.101* pour l' **adresse IP**, puis cliquez sur **Enregistrer**, puis cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP public (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API REST de IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).