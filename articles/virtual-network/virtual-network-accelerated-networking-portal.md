<properties 
   pageTitle="Accélérer la mise en réseau pour une machine virtuelle - portail | Microsoft Azure"
   description="Découvrez comment configurer accélérée de mise en réseau pour une machine virtuelle Azure à l’aide du portail Azure."
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
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Mise en réseau accélérée pour une machine virtuelle

> [AZURE.SELECTOR]
- [Portail Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Réseau accélérée permet de virtualisation d’e/s unique racine (SR IOV) pour une machine virtuelle (), considérablement améliorer ses performances réseau. Ce chemin d’accès High performance ignore l’hôte à partir de la datapath réduisant latence, gigue et l’utilisation de l’UC pour une utilisation avec les charges de travail réseau exigeants sur les types de machine virtuelle pris en charge. Cet article explique comment utiliser le portail Azure pour configurer l’accélération réseau dans le modèle de déploiement d’Azure le Gestionnaire de ressources. Vous pouvez également créer une machine virtuelle avec accélérée réseau à l’aide de PowerShell Azure. Pour savoir comment procéder, cliquez sur la zone PowerShell en haut de cet article.

L’image suivante montre la communication entre deux machines virtuelles (machine virtuelle) avec et sans accélérée de mise en réseau :

![Comparaison](./media/virtual-network-accelerated-networking-portal/image1.png)

Sans accélérée mise en réseau, tout le trafic réseau et déconnexion de la machine virtuelle doit parcourir l’hôte et le commutateur virtuel. Le commutateur virtuel offre toutes les stratégies, tels que les groupes de sécurité réseau, listes de contrôle d’accès, isolement et d’autres services de réseau virtualisé pour le trafic réseau. Pour plus d’informations, consultez l’article [virtualisation du réseau Hyper-V et le commutateur virtuel](https://technet.microsoft.com/library/jj945275.aspx) .

Avec accélérée de mise en réseau, le trafic réseau arrive à la carte réseau (carte réseau) et est ensuite transmis à la machine virtuelle. Toutes les stratégies de réseau le commutateur virtuel s’applique sans accélérée de mise en réseau sont décharger et appliqués au niveau du matériel. Application d’une stratégie au niveau du matériel permet de la carte réseau pour le trafic réseau transférer directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu'il appliqué dans l’hôte.

Les avantages de l’accélération réseau s’appliquent uniquement à la machine virtuelle qui il est activé sur. Pour de meilleurs résultats, il est idéal activer cette fonctionnalité sur au moins deux machines virtuelles connecté à la même VNet. Lors de la communication entre VNets ou connexion locale, cette fonctionnalité a un impact minime au latence globale.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Avantages

- **Une latence inférieure / supérieures paquets par seconde (pps) :** Supprimer le commutateur virtuel la datapath supprime le temps de paquets de l’hôte pour le traitement des stratégies et augmente le nombre de paquets qui peut être traité à l’intérieur de la machine virtuelle.
- **Réduites gigue :** Traitement switch virtuel dépend de la quantité de stratégie qui doit être appliqué et la charge de travail de l’UC qui effectue le traitement. Décharger l’application de stratégie pour le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, suppression de l’hôte de communication machine virtuelle et toutes les interruptions logiciel et les changements de contexte.
- **Réduite de l’UC :** Ignorer le commutateur virtuel dans l’hôte permet d’accéder à moins de l’UC pour traiter le trafic réseau.

## <a name="limitations"></a>Limitations

Les limitations suivantes lors de l’utilisation de cette fonctionnalité :
 
- **Création d’une interface de réseau :** Mise en réseau accélérée ne peut être activée pour une nouvelle interface réseau.  Il ne peut pas être activé sur une interface de réseau existante.
- **Création d’une machine virtuelle :** Une interface de réseau avec un réseau accélérée activé ne peut être attachée à une machine virtuelle lors de la création de la machine virtuelle. L’interface réseau ne peuvent pas être associé à une machine virtuelle existante.
- **Régions :** Proposée dans les régions ouest États-Unis et Ouest Europe Azure. L’ensemble des régions s’élargissent à l’avenir.
- **Système d’exploitation pris en charge :** Microsoft Windows Server 2012 R2 et Windows Server 2016 Technical Preview 5. Prise en charge Linux et Windows Server 2012 est bientôt ajouté.
- **Taille de mémoire virtuelle :** Standard_D15_v2 et Standard_DS15_v2 sont qu'uniquement pris en charge de tailles d’instance machine virtuelle. Pour plus d’informations, voir l’article [tailles machine virtuelle Windows](../virtual-machines/virtual-machines-windows-sizes.md) . Le jeu de tailles d’instance machine virtuelle pris en charge s’élargissent à l’avenir.

Modifications apportées à ces limitations seront annoncées via la page des [mises à jour de réseau virtuel Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Créer une machine virtuelle Windows avec mise en réseau accélérée

1. Inscrivez-vous à l’aperçu en envoyant un courrier électronique aux [Abonnements de mise en réseau accélérée](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) avec votre ID de l’abonnement et l’utilisation prévue. N’effectuez pas les étapes restantes jusqu'à une fois que vous recevez un message vous informant que vous avez été accepté dans l’aperçu.
2. Connectez-vous au portail Azure à http://portal.azure.com.
3. Créer une machine virtuelle en effectuant les étapes décrites dans l’article [créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) en sélectionnant les options suivantes :
    - Sélectionnez un système d’exploitation répertorié dans la section Limitations de cet article.
    - Sélectionnez un emplacement (région) répertorié dans la section Limitations de cet article.
    - Sélectionnez une taille de mémoire virtuelle répertoriée dans la section Limitations de cet article. Si un des formats pris en charge n’est pas répertorié, cliquez sur **Afficher tout** dans la carte de **Choisir une taille** pour sélectionner une taille d’une liste développée.
    - Dans la carte de **paramètres** , cliquez sur *activé* pour l' **accès réseau Accelerated**, comme le montre l’illustration suivante :

        ![Paramètres](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] L’option accélérée de mise en réseau n’est visible que si vous avez :
    >
    >- Été acceptée dans l’aperçu
    >- Sélectionné le système d’exploitation pris en charge, l’emplacement et taille machine virtuelle mentionné dans la section Limitations de cet article.

5. Une fois que la machine virtuelle est créée, téléchargez le [pilote accélérée de mise en réseau](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), vous connecter et se connecter à la machine virtuelle et exécuter le programme d’installation de pilote à l’intérieur de la machine virtuelle.
6. Cliquez sur le bouton Windows, puis cliquez sur **Gestionnaire de périphériques**. Vérifiez que la **Carte Mellanox ConnectX-3 virtuelle fonction Ethernet** apparaît sous l’option de **réseau** lorsqu’elle est développée, comme le montre l’illustration suivante :

    ![Gestionnaire de périphériques](./media/virtual-network-accelerated-networking-portal/image2.png)