<properties
    pageTitle="Installer IIS sur votre ordinateur virtuel Windows première | Microsoft Azure"
    description="Tester votre première machine virtuelle Windows en installant IIS et ouvrir le port 80 à l’aide du portail Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Essayer l’installation d’un rôle sur votre ordinateur Windows virtuel
    
Une fois que votre première machine virtuelle (machine virtuelle) haut et en cours d’exécution, vous pouvez déplacer sur pour l’installation des logiciels et services. Pour ce didacticiel, nous allons utiliser le Gestionnaire de serveur de la machine virtuelle de Windows Server pour installer IIS. Ensuite, nous allons créer un groupe de sécurité réseau (NSG) à l’aide du portail Azure pour ouvrir le port 80 pour le trafic IIS. 

Si vous n’avez pas encore créé votre première machine virtuelle, vous devez revenir à [créer votre première machine virtuelle de Windows dans le portail Azure](virtual-machines-windows-hero-tutorial.md) avant de poursuivre ce didacticiel.

## <a name="make-sure-the-vm-is-running"></a>Vérifiez que la machine virtuelle est en cours d’exécution

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Dans le menu concentrateur, cliquez sur **Machines virtuelles**. Sélectionnez la machine virtuelle dans la liste.
3. Si le statut est défini sur **arrêté (Deallocated)**, cliquez sur le bouton **Démarrer** dans la carte **Essentials** de la machine virtuelle. Si le statut est **en cours d’exécution**, vous pouvez déplacer sur à l’étape suivante.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Se connecter à la machine virtuelle et se connecter

1.  Dans le menu concentrateur, cliquez sur **Machines virtuelles**. Sélectionnez la machine virtuelle dans la liste.

3. Dans la carte de la machine virtuelle, cliquez sur **se connecter**. Cela crée et télécharge un fichier Remote Desktop Protocol (fichier .rdp) qui ressemble à un raccourci pour vous connecter à votre ordinateur. Vous souhaiterez peut-être enregistrer le fichier sur votre bureau pour y accéder facilement. **Ouvrir** ce fichier pour vous connecter à votre ordinateur virtuel.

    ![Capture d’écran du portail Azure montrant comment se connecter à votre machine virtuelle](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Vous obtenez un message d’avertissement qui la .rdp provient d’un éditeur inconnu. Ceci est normal. Dans la fenêtre de bureau à distance, cliquez sur **se connecter** pour continuer.

    ![Capture d’écran d’un avertissement concernant un éditeur inconnu](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Dans la fenêtre de la sécurité de Windows, tapez le nom d’utilisateur et mot de passe du compte local que vous avez créé quand vous avez créé la machine virtuelle. Le nom d’utilisateur est entré comme *vmname*& #92 ; *nom d’utilisateur*, puis cliquez sur **OK**.

    ![Capture d’écran de l’entrée de la machine virtuelle nom, nom d’utilisateur et mot de passe](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Vous obtenez un message d’avertissement que le certificat ne peut pas être vérifié. Ceci est normal. Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer une session.

    ![Capture d’écran montrant un message à propos de vérifier l’identité de la machine virtuelle](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Si vous rencontrez des difficultés lorsque vous tentez de vous connecter, consultez [résoudre les problèmes de bureau à distance connexions à un Windows Azure Machine virtuelle](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Installer IIS sur votre ordinateur virtuel

À présent que vous êtes connecté à la machine virtuelle, nous allons installer un rôle serveur afin que vous pouvez effectuer des tests plus.

1. Ouvrez le **Gestionnaire de serveur** si elle n’est pas déjà ouvert. Cliquez sur le menu **Démarrer** , puis cliquez sur **Gestionnaire de serveur**.
2. Dans le **Gestionnaire de serveur**, sélectionnez **Serveur Local** dans le volet gauche. 
3. Dans le menu, sélectionnez **Gérer** > **Ajouter des rôles et fonctionnalités**.
4. Dans l’Assistant de fonctionnalités, dans la page **Type d’Installation** , ajouter des rôles et choisissez une **installation basée sur une fonctionnalité ou rôle**, puis cliquez sur **suivant**.

    ![Capture d’écran montrant l’onglet Ajouter des rôles et les fonctionnalités de l’Assistant pour le Type d’Installation](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Sélectionnez la machine virtuelle à partir du pool de serveur, puis cliquez sur **suivant**.
6. Dans la page **Rôles de serveur** , sélectionnez **Serveur Web (IIS)**.

    ![Capture d’écran montrant l’onglet Ajouter des rôles et les fonctionnalités de l’Assistant pour les rôles de serveur](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. Dans le menu contextuel sur l’ajout de fonctionnalités nécessaires pour IIS, vérifiez que **les outils d’administration** est sélectionné, puis sur **Ajouter des fonctionnalités**. Fermeture de la fenêtre contextuelle, cliquez sur **suivant** dans l’Assistant.

    ![Capture d’écran montrant contextuel à confirmer l’ajout du rôle IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Dans la page fonctionnalités, cliquez sur **suivant**.
9. Dans la page **Rôle de serveur Web (IIS)** , cliquez sur **suivant**. 
10. Dans la page **Des Services de rôle** , cliquez sur **suivant**. 
11. Dans la page de **Confirmation** , cliquez sur **installer**. 
12. Une fois l’installation terminée, cliquez sur **Fermer** dans l’Assistant.



## <a name="open-port-80"></a>Ouvrez le port 80 

Afin que votre ordinateur virtuel accepter le trafic entrant sur le port 80, vous devez ajouter une règle de trafic entrant au groupe de sécurité réseau. 

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Machines **virtuelles** sélectionnez la machine virtuelle que vous avez créé.
3. Dans les paramètres de machines virtuelles, sélectionnez **interfaces réseau** , puis sélectionnez l’interface réseau existant.

    ![Capture d’écran montrant le paramètre machine virtuelle pour les interfaces réseau](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. Dans **Essentials** pour l’interface réseau, cliquez sur le **groupe de sécurité réseau**.

    ![Capture d’écran montrant la section Essentials pour l’interface réseau](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. Dans la carte **Essentials** pour le NSG, vous devez disposer d’une règle de trafic entrant de par défaut existant pour **rdp autoriser par défaut** qui permet de vous connecter à la machine virtuelle. Vous allez ajouter une autre règle de trafic entrant pour autoriser le trafic IIS. Cliquez sur **règle de sécurité entrante**.

    ![Capture d’écran montrant la section Essentials pour le NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. En **entrant des règles de sécurité**, cliquez sur **Ajouter**.

    ![Capture d’écran montrant le bouton Ajouter une règle de sécurité](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. En **entrant des règles de sécurité**, cliquez sur **Ajouter**. Tapez **80** dans la plage de ports et assurez-vous que **Autoriser** est sélectionnée. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Capture d’écran montrant le bouton Ajouter une règle de sécurité](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Pour plus d’informations sur NSGs, règles entrants et sortants, voir [Autoriser l’accès externe à votre ordinateur virtuel à l’aide du portail Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Se connecter au site Web IIS par défaut

1. Dans le portail Azure, cliquez sur **machines virtuelles** , puis sur votre ordinateur virtuel.
2. Dans la carte **Essentials** , copiez votre **adresse IP publique**.

    ![Capture d’écran montrant où trouver l’adresse IP de votre machine virtuelle](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Ouvrez un navigateur et dans la barre d’adresses, tapez votre adresse IP publique à ceci : http://<publicIPaddress> et appuyez sur **entrée** pour accéder à cette adresse.
3. Votre navigateur doit ouvrir la page de web IIS par défaut. Il ressemble à ceci :

    ![Capture d’écran montrant l’aperçu de la page IIS par défaut dans un navigateur](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez également expérimenter [en joignant un disque de données](virtual-machines-windows-attach-disk-portal.md) à votre machine virtuelle. Données disques offrent davantage de stockage pour votre ordinateur virtuel.
