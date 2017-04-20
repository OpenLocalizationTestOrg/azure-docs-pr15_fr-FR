<properties
    pageTitle="Ne peut pas RDP pour une machine virtuelle Azure | Microsoft Azure"
    description="Résoudre les problèmes lorsque vous ne pouvez pas vous connecter à votre machine virtuelle Windows dans Azure à l’aide de bureau à distance"
    keywords="Erreur du Bureau à distance, erreur de connexion Bureau à distance, ne peut pas se connecter à la machine virtuelle, dépannage de bureau à distance"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Résoudre les problèmes de connexions de bureau à distance à une machine virtuelle Azure

La connexion de protocole RDP (Remote Desktop) à votre basés sur Windows Azure machine virtuelle (machine virtuelle) peut échouer pour diverses raisons, ce qui vous laisse Impossible d’accéder à votre ordinateur virtuel. Le problème peut être avec le service de bureau à distance sur la machine virtuelle, la connexion réseau ou le client de bureau à distance sur votre ordinateur hôte. Cet article vous guide tout au long de certaines des méthodes plus courantes pour résoudre les problèmes de connexion RDP. 

Si vous avez besoin d’une aide supplémentaire à tout moment dans cet article, vous pouvez contacter experts Azure dans [les forums de débordement de pile Azure MSDN](https://azure.microsoft.com/support/forums/). Par ailleurs, vous pouvez créer un incident de support Azure. Atteindre le [site de support technique Azure](https://azure.microsoft.com/support/options/) , puis sélectionnez **Prise en charge**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Étapes de dépannage rapides
Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle :

1. Réinitialiser la configuration de bureau à distance.
2. Vérifier le groupe de sécurité du réseau règles / Cloud Services points de terminaison.
3. Passez en revue les journaux de console de machine virtuelle.
4. Vérifier l’état de la ressource virtuelle.
5. Réinitialiser votre mot de passe machine virtuelle.
6. Redémarrez votre ordinateur virtuel.
7. Redéployez votre machine virtuelle.

Poursuivre la lecture si vous avez besoin des procédures plus détaillées et des explications.

> [AZURE.TIP] Si le bouton **se connecter** pour votre machine virtuelle est grisé dans le portail et que vous n’êtes pas connecté à Azure via une connexion [Express itinéraire](../expressroute/expressroute-introduction.md) ou [VPN de Site à Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , vous devez créer et affecter votre machine virtuelle une adresse IP publique avant de pouvoir utiliser RDP. Vous pouvez en savoir plus sur les [adresses IP publiques dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Méthodes pour résoudre les problèmes RDP
Vous pouvez résoudre les machines virtuelles créées à l’aide du modèle de déploiement du Gestionnaire de ressources à l’aide d’une des méthodes suivantes :

- [Portail azure](#using-the-azure-portal) - correctement si vous avez besoin pour le réinitialiser rapidement les informations d’identification utilisateur ou de configuration RDP et que vous n’avez pas installé les outils Azure.
- [Azure PowerShell](#using-azure-powershell) - si vous êtes à l’aise avec une invite PowerShell, rapidement réinitialiser les RDP configuration utilisateur informations d’identification à l’aide des applets de commande PowerShell Azure.

Étapes de résolution des problèmes de machines virtuelles créées à l’aide du [modèle de déploiement classique](#troubleshoot-vms-created-using-the-classic-deployment-model)sont également disponibles.


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Résoudre les problèmes à l’aide du portail Azure
Après chaque étape de résolution des problèmes, essayez de vous reconnecter à votre ordinateur virtuel. Si vous ne pouvez toujours pas vous connecter, essayez l’étape suivante.

1. **Réinitialiser votre connexion RDP**. Cette procédure de dépannage réinitialise la configuration de RDP lorsque les connexions à distance sont désactivées ou les règles de pare-feu Windows bloquent RDP, par exemple.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet paramètres à la section **prise en charge + résolution des problèmes** en bas de la liste. Cliquez sur le bouton **Réinitialiser votre mot de passe** . Définir le **Mode** de **Réinitialiser la configuration uniquement** , puis sur le bouton **mettre à jour** :

    ![Réinitialiser la configuration de RDP dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Règles de vérifier le groupe de sécurité du réseau**. Cette procédure de dépannage vérifie que vous disposez d’une règle dans votre groupe de sécurité de réseau pour autoriser le trafic RDP. Le port par défaut pour RDP est le port TCP 3389. Une règle pour autoriser le trafic RDP ne peut-être pas être créée automatiquement lorsque vous créez votre machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Cliquez sur les **interfaces réseau** à partir du volet Paramètres.

    ![Afficher les interfaces de réseau pour une machine virtuelle portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Sélectionnez votre interface de réseau dans la liste (il s’agit généralement qu’un seul) :

    ![Sélectionnez l’interface réseau dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Sélectionnez le **groupe de sécurité réseau** pour afficher le groupe de sécurité réseau associé à votre interface réseau :

    ![Sélectionnez le groupe de sécurité réseau dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Vérifiez qu’il existe une règle de trafic entrant autorise le trafic RDP sur le port TCP 3389. L’exemple suivant montre une règle de sécurité valide qui autorise le trafic RDP. Vous pouvez voir `Service` et `Action` sont correctement configurés :

    ![Vérifier la règle RDP NSG dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Si vous ne disposez pas d’une règle qui autorise le trafic RDP, [créer une règle de groupe de sécurité réseau](virtual-machines-windows-nsg-quickstart-portal.md). Autoriser le port TCP 3389.

3. **Diagnostics démarrage machine virtuelle révision**. Cette procédure de dépannage passe en revue les journaux de console machine virtuelle pour déterminer si la machine virtuelle est signalé un problème. Machines virtuelles pas en totalité ont diagnostics démarrage activé, pour cette procédure de dépannage peut être facultative.
    
    Étapes de dépannage spécifiques n’entrent pas dans le cadre de cet article, mais peuvent signaler un problème plus large qui affecte la connectivité RDP. Pour plus d’informations sur la révision de la console journaux et capture d’écran de la machine virtuelle, voir [Démarrage de diagnostic pour les machines virtuelles](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Vérifier l’état d’intégrité de la ressource machine virtuelle**. Cette procédure de dépannage vérifie il n’existe aucun problème connu avec la plateforme Azure qui peuvent avoir un impact sur la connectivité de la machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet paramètres à la section **prise en charge + résolution des problèmes** en bas de la liste. Cliquez sur le bouton **santé des ressources** . Une machine virtuelle correcte signale comme étant **disponibles**:

    ![Vérifier le fonctionnement de ressources machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Réinitialiser les informations d’identification utilisateur**. Cette procédure de dépannage réinitialise le mot de passe sur un compte d’administrateur local lorsque vous n’êtes pas sûr ou que vous avez oublié les informations d’identification.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet paramètres à la section **prise en charge + résolution des problèmes** en bas de la liste. Cliquez sur le bouton **Réinitialiser votre mot de passe** . Vérifiez que le **Mode** est défini pour **Réinitialiser votre mot de passe** , puis entrez votre nom d’utilisateur et un mot de passe. Pour finir, cliquez sur le bouton **mettre à jour** :

    ![Réinitialiser les informations d’identification utilisateur dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Redémarrez votre ordinateur virtuel**. Cette procédure de dépannage peut corriger les problèmes sous-jacent affectée la machine virtuelle elle-même.

    Sélectionnez votre machine virtuelle dans le portail Azure, puis cliquez sur l’onglet **vue d’ensemble** . Cliquez sur le bouton **redémarrer** :

    ![Redémarrez la machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Redéployez votre machine virtuelle**. Cette procédure de dépannage redéploie votre machine virtuelle à un autre hôte dans Azure pour corriger les problèmes réseau ou n’importe quelle plate-forme sous-jacente.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet paramètres à la section **prise en charge + résolution des problèmes** en bas de la liste. Cliquez sur le bouton **redéployez** , puis cliquez sur **redéployez**:

    ![Redéployez la machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Une fois cette opération terminée, les données de disque éphémères sont perdues et adresses IP dynamiques qui sont associées à la machine virtuelle sont mis à jour.

Si vous rencontrez toujours des problèmes RDP, vous pouvez [Ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou en savoir [plus détaillées concepts dépannage RDP et les étapes](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Résoudre les problèmes à l’aide de PowerShell Azure
Si vous n’avez pas déjà fait, [installer et configurer la dernière PowerShell Azure](../powershell-install-configure.md).

Les exemples suivants utilisent des variables tel que `myResourceGroup`, `myVM`, et `myVMAccessExtension`. Remplacez ces noms de variables et les emplacements par vos propres valeurs.

> [AZURE.NOTE] Vous réinitialiser les informations d’identification utilisateur et la configuration de RDP à l’aide de l’applet de commande PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) . Dans les exemples suivants, `myVMAccessExtension` est un nom que vous spécifiez dans le cadre du processus. Si vous avez déjà travaillé avec la VMAccessAgent, vous pouvez obtenir le nom de l’extension existante à l’aide de `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` pour vérifier les propriétés de la machine virtuelle. Pour afficher le nom, consultez la section « Extensions » de la sortie.

Après chaque étape de résolution des problèmes, essayez de vous reconnecter à votre ordinateur virtuel. Si vous ne pouvez toujours pas vous connecter, essayez l’étape suivante.

1. **Réinitialiser votre connexion RDP**. Cette procédure de dépannage réinitialise la configuration de RDP lorsque les connexions à distance sont désactivées ou les règles de pare-feu Windows bloquent RDP, par exemple.

    L’exemple suivant réinitialise la connexion RDP sur un ordinateur virtuel nommé `myVM` dans les `WestUS` emplacement dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Règles de vérifier le groupe de sécurité du réseau**. Cette procédure de dépannage vérifie que vous disposez d’une règle dans votre groupe de sécurité de réseau pour autoriser le trafic RDP. Le port par défaut pour RDP est le port TCP 3389. Une règle pour autoriser le trafic RDP ne peut-être pas être créée automatiquement lorsque vous créez votre machine virtuelle.

    Tout d’abord, attribuez toutes les données de configuration pour votre groupe de sécurité réseau pour le `$rules` variable. L’exemple suivant obtienne des informations sur le groupe de sécurité réseau nommé `myNetworkSecurityGroup` dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    À présent, afficher les règles qui sont configurés pour ce groupe de sécurité réseau. Vérifier l’existence d’une règle pour autoriser le port TCP 3389 pour les connexions entrantes comme suit :

    ```powershell
    $rules.SecurityRules
    ```

    L’exemple suivant montre une règle de sécurité valide qui autorise le trafic RDP. Vous pouvez voir `Protocol`, `DestinationPortRange`, `Access`, et `Direction` sont correctement configurés :

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Si vous ne disposez pas d’une règle qui autorise le trafic RDP, [créer une règle de groupe de sécurité réseau](virtual-machines-windows-nsg-quickstart-powershell.md). Autoriser le port TCP 3389.

3. **Réinitialiser les informations d’identification utilisateur**. Cette procédure de dépannage réinitialise le mot de passe du compte d’administrateur local que vous spécifiez lorsque vous n’êtes pas sûr d’ou que vous avez oublié, les informations d’identification.

    Tout d’abord, spécifiez le nom d’utilisateur et un mot de passe en affectant des informations d’identification pour le `$cred` variable comme suit :

    ```powershell
    $cred=Get-Credential
    ```

    À présent, mettez à jour les informations d’identification sur votre ordinateur virtuel. L’exemple suivant met à jour les informations d’identification sur un ordinateur virtuel nommé `myVM` dans les `WestUS` emplacement dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Redémarrez votre ordinateur virtuel**. Cette procédure de dépannage peut corriger les problèmes sous-jacent affectée la machine virtuelle elle-même.

    L’exemple suivant redémarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Redéployez votre machine virtuelle**. Cette procédure de dépannage redéploie votre machine virtuelle à un autre hôte dans Azure pour corriger les problèmes réseau ou n’importe quelle plate-forme sous-jacente.

    L’exemple suivant redéploie la machine virtuelle nommée `myVM` dans les `WestUS` emplacement dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Si vous rencontrez toujours des problèmes RDP, vous pouvez [Ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou en savoir [plus détaillées concepts dépannage RDP et les étapes](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Résoudre les problèmes de machines virtuelles créées à l’aide du modèle de déploiement classique

Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

1. **Réinitialiser votre connexion RDP**. Cette procédure de dépannage réinitialise la configuration de RDP lorsque les connexions à distance sont désactivées ou les règles de pare-feu Windows bloquent RDP, par exemple.

    Sélectionnez votre machine virtuelle dans le portail Azure. Cliquez sur le **... Plus** , puis cliquez sur **Réinitialiser l’accès à distance**:

    ![Réinitialiser la configuration de RDP dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Vérifier les Services en nuage de points de terminaison**. Cette procédure de dépannage vérifie que vous disposez des points de terminaison de vos Services Cloud pour autoriser le trafic RDP. Le port par défaut pour RDP est le port TCP 3389. Une règle pour autoriser le trafic RDP ne peut-être pas être créée automatiquement lorsque vous créez votre machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Cliquez sur le bouton **points de terminaison** pour afficher les points de terminaison déjà configurées pour votre ordinateur virtuel. Vérifiez que les points de terminaison existent qui autorisent le trafic RDP sur le port TCP 3389.
    
    L’exemple suivant illustre les points de terminaison valides autorisent le trafic RDP :

    ![Vérifiez les points de terminaison de Services de Cloud dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Si vous ne disposez pas d’un point de terminaison qui autorise le trafic RDP, [créer un point de terminaison de Services Cloud](virtual-machines-windows-classic-setup-endpoints.md). Laisser TCP privé Port3389.

3. **Diagnostics démarrage machine virtuelle révision**. Cette procédure de dépannage passe en revue les journaux de console machine virtuelle pour déterminer si la machine virtuelle est signalé un problème. Machines virtuelles pas en totalité ont diagnostics démarrage activé, pour cette procédure de dépannage peut être facultative.
    
    Étapes de dépannage spécifiques n’entrent pas dans le cadre de cet article, mais peuvent signaler un problème plus large qui affecte la connectivité RDP. Pour plus d’informations sur la révision de la console journaux et capture d’écran de la machine virtuelle, voir [Démarrage de diagnostic pour les machines virtuelles](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Vérifier l’état d’intégrité de la ressource machine virtuelle**. Cette procédure de dépannage vérifie il n’existe aucun problème connu avec la plateforme Azure qui peuvent avoir un impact sur la connectivité de la machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet paramètres à la section **prise en charge + résolution des problèmes** en bas de la liste. Cliquez sur le bouton **Santé des ressources** . Une machine virtuelle correcte signale comme étant **disponibles**:

    ![Vérifier le fonctionnement de ressources machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Réinitialiser les informations d’identification utilisateur**. Cette procédure de dépannage réinitialise le mot de passe du compte d’administrateur local que vous spécifiez lorsque vous n’êtes pas sûr ou que vous avez oublié les informations d’identification.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet paramètres à la section **prise en charge + résolution des problèmes** en bas de la liste. Cliquez sur le bouton **Réinitialiser votre mot de passe** . Entrez votre nom d’utilisateur et un mot de passe. Pour finir, cliquez sur le bouton **Enregistrer** :

    ![Réinitialiser les informations d’identification utilisateur dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Redémarrez votre ordinateur virtuel**. Cette procédure de dépannage peut corriger les problèmes sous-jacent affectée la machine virtuelle elle-même.

    Sélectionnez votre machine virtuelle dans le portail Azure, puis cliquez sur l’onglet **vue d’ensemble** . Cliquez sur le bouton **redémarrer** :

    ![Redémarrez la machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Si vous rencontrez toujours des problèmes RDP, vous pouvez [Ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou en savoir [plus détaillées concepts dépannage RDP et les étapes](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Résoudre les erreurs de RDP spécifiques
Vous pouvez rencontrer un message d’erreur lorsque vous tentez de vous connecter à votre ordinateur virtuel via RDP. Messages d’erreur les plus courants sont les suivantes :

- [La session distante a été déconnectée car il n’existe aucun serveur de licences bureau à distance fournir une licence](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Bureau à distance ne peut pas trouver l’ordinateur « nom »](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Erreur de [une authentification. Impossible de contacter l’autorité de sécurité locale](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Erreur de sécurité de Windows : vos informations d’identification n’a pas fonctionné](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [Cet ordinateur ne peut pas se connecter à l’ordinateur distant](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Ressources supplémentaires
Si aucune de ces erreurs s’est produite et vous ne pouvez toujours pas vous connecter à la machine virtuelle via le Bureau à distance, lisez le détaillées [guide de dépannage pour le Bureau à distance](virtual-machines-windows-detailed-troubleshoot-rdp.md).

- [Package de diagnostics Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Étapes de l’accès à des applications qui s’exécutent sur un ordinateur virtuel de dépannage, voir [résoudre les problèmes l’accès à l’application en cours d’exécution sur un ordinateur virtuel Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Si vous rencontrez des problèmes à l’aide de SSH (Secure Shell) pour vous connecter à une VM Linux dans Azure, voir [résoudre les connexions SSH pour une machine virtuelle Linux dans Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
