<properties
    pageTitle="Dépannage Bureau à distance détaillées | Microsoft Azure"
    description="Passez en revue des étapes de résolution détaillées des erreurs de bureau à distance dans lequel vous ne parvenez pas à un machines virtuelles Windows dans Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="Impossible de se connecter au Bureau à distance, résoudre les problèmes de bureau à distance, Bureau à distance ne peut pas se connecter, des erreurs de bureau à distance, dépannage de bureau à distance, des problèmes de bureau à distance
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Étapes de dépannage détaillées pour les problèmes de connexion Bureau à distance aux ordinateurs virtuels Windows Azure

Cet article fournit des étapes de dépannage détaillées pour diagnostiquer et réparer les erreurs de bureau à distance complexes pour les ordinateurs exécutant Windows Azure virtuelle.

> [AZURE.IMPORTANT] Pour éviter les erreurs de bureau à distance les plus courantes, veillez à lire [l’article de résolution des problèmes base Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md) avant de poursuivre.

Vous pouvez rencontrer un message d’erreur de bureau à distance qui ne ressemble à un des messages d’erreur spécifiques décrites dans [le guide de dépannage de bureau à distance simple](virtual-machines-windows-troubleshoot-rdp-connection.md). Procédez comme suit pour déterminer pourquoi le client de bureau à distance (RDP) est impossible de se connecter au service RDP sur l’ordinateur virtuel Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous avez besoin d’une aide supplémentaire à tout moment dans cet article, vous pouvez contacter experts Azure sur [la Azure MSDN et les forums de débordement de pile](https://azure.microsoft.com/support/forums/). Par ailleurs, vous pouvez également créer un incident de support Azure. Accédez au [site d’assistance Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un Support**. Pour plus d’informations sur l’utilisation de prise en charge Azure, lisez le [FAQ de Microsoft Azure prise en charge](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Composants d’une connexion Bureau à distance

Les composants suivants sont impliqués dans une connexion RDP :

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Avant de poursuivre, il peut être utile de mentalement passez en revue ce qui a changé depuis la dernière connexion Bureau à distance à la machine virtuelle. Par exemple :

- L’adresse IP de l’ordinateur virtuel ou le service en nuage contenant la machine virtuelle (également appelée l’adresse IP virtuelle [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) a changé. L’échec RDP peut être, car le cache de client DNS a toujours l' *ancienne adresse IP* enregistrée pour le nom DNS. Vider le cache de client DNS et réessayez de vous connecter la machine virtuelle. Ou essayez de vous connecter directement avec la nouvelle VIP.
- Vous utilisez une application tierce pour gérer vos connexions de bureau à distance au lieu d’utiliser la connexion générée par le portail Azure. Vérifiez que la configuration d’application inclut le port TCP correct pour le trafic de bureau à distance. Vous pouvez vérifier ce port pour une machine virtuelle classique dans le [portail Azure](https://portal.azure.com), en cliquant sur paramètres de la machine virtuelle > points de terminaison.


## <a name="preliminary-steps"></a>Étapes préliminaires

Avant de procéder à la résolution détaillée des problèmes,

- Vérifiez l’état de la machine virtuelle dans le portail Azure classique ou le portail Azure pour des problèmes évidentes.
- Suivez les [étapes de réparation rapide des erreurs RDP courantes dans le guide de dépannage de base](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Essayez de vous reconnecter à la machine virtuelle via le Bureau à distance après ces étapes.


## <a name="detailed-troubleshooting-steps"></a>Étapes de résolution détaillées

Le client de bureau à distance peut ne pas être en mesure d’atteindre le service de bureau à distance sur l’ordinateur virtuel Azure en raison de problèmes en sources suivantes :

- [Ordinateur client de bureau à distance](#source-1-remote-desktop-client-computer)
- [Appareil de bord intranet organisation](#source-2-organization-intranet-edge-device)
- [Point de terminaison de service en nuage et accéder à la liste de contrôle (et)](#source-3-cloud-service-endpoint-and-acl)
- [Groupes de sécurité réseau](#source-4-network-security-groups)
- [Basé sur Windows Azure virtuelle](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Source 1 : Ordinateur client de bureau à distance

Vérifiez que votre ordinateur peut établir des connexions de bureau à distance vers un autre local, ordinateur fonctionnant sous Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Si vous ne pouvez pas, vérifiez les paramètres suivants sur votre ordinateur :

- Paramètre de pare-feu local bloque le trafic de bureau à distance.
- Localement installé le logiciel client proxy qui empêche connexions Bureau à distance.
- Localement installé un logiciel qui empêche connexions Bureau à distance de surveillance.
- Autres types de logiciels de sécurité qui surveillent le trafic ou autoriser/interdire certains types de trafic qui empêche connexions Bureau à distance.

Dans ce cas, désactivez le logiciel temporairement et essayez de vous connecter à un ordinateur local via le Bureau à distance. Si vous pouvez déterminer la cause réelle de cette façon, travailler avec votre administrateur réseau pour corriger les paramètres du logiciel pour autoriser les connexions de bureau à distance.

## <a name="source-2-organization-intranet-edge-device"></a>Source 2 : Dispositif de bord intranet organisation

Vérifiez qu’un ordinateur directement connecté à Internet peut établir des connexions de bureau à distance à votre machine virtuelle Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Si vous ne disposez pas d’un ordinateur est connecté directement à Internet, créer et tester avec un nouvel ordinateur virtuel Azure dans un service cloud ou de groupe de ressources. Pour plus d’informations, voir [créer une machine virtuelle exécutant Windows dans Azure](virtual-machines-windows-hero-tutorial.md). Vous pouvez supprimer la machine virtuelle et le groupe de ressources ou le service cloud, après le test.

Si vous pouvez créer une connexion Bureau à distance avec un ordinateur directement connecté à Internet, vérifiez votre appareil organisation intranet bord pour :

- Un pare-feu bloque les connexions HTTPS à Internet.
- Un serveur proxy empêchant les connexions Bureau à distance.
- Détection ou logiciels en cours d’exécution sur les périphériques de votre réseau bord qui empêche connexions Bureau à distance d’analyse réseau.

Travailler avec votre administrateur réseau pour corriger les paramètres de votre organisation intranet bord pour autoriser les connexions de bureau à distance basée sur HTTPS à Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Source 3 : Point de terminaison de service Cloud et et

Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, vérifiez une autre machine virtuelle Azure qui se trouve dans le même service cloud ou le réseau virtuel pouvez connexions Bureau à distance à votre ordinateur virtuel Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Pour une machine virtuelle créée dans le Gestionnaire de ressources, passez à le [Source 4 : groupes de sécurité réseau](#source-4-network-security-groups).

Si vous n’avez pas d’une autre machine virtuelle dans le même service cloud ou le réseau virtuel, créez-en un. Suivez les étapes décrites dans [créer une machine virtuelle exécutant Windows dans Azure](virtual-machines-windows-hero-tutorial.md). Supprimer la machine virtuelle test une fois le test est terminé.

Si vous pouvez vous connecter via le Bureau à distance à une machine virtuelle dans le même service cloud ou le réseau virtuel, vérifiez ces paramètres :

- La configuration de point de terminaison pour le trafic de bureau à distance sur la machine virtuelle cible : le port TCP privé du point de terminaison doit correspondre au port TCP sur lequel le service de bureau à distance de la machine virtuelle est à l’écoute (valeur par défaut est 3389).
- L’et du point de terminaison du trafic Bureau à distance sur la machine virtuelle cible : utilisateurs permettent de spécifier autorisé ou refusé le trafic entrant à partir d’Internet en fonction de son adresse IP source. Utilisateurs incorrectement configurés peuvent empêcher le trafic entrant Bureau à distance au point de terminaison. Vérifiez vos utilisateurs pour vous assurer que le trafic entrant à partir de votre publics adresses IP de votre serveur proxy ou autres serveur edge est autorisée. Pour plus d’informations, voir [qu’est une liste de contrôle (accès) de réseau ?](../virtual-network/virtual-networks-acl.md)

Pour vérifier si le point de terminaison est la source du problème, supprimez le point de terminaison actuel et créer un autre, en choisissant un port aléatoire dans la plage 49152 à 65535 pour le numéro de port externe. Pour plus d’informations, voir [comment configurer une machine virtuelle des points de terminaison](virtual-machines-windows-classic-setup-endpoints.md).

## <a name="source-4-network-security-groups"></a>Source 4 : Groupes de sécurité de réseau

Groupes de sécurité réseau autoriser un contrôle plus précis du trafic entrant et sortant autorisé. Vous pouvez créer des règles sous-réseaux et cloud services dans un réseau virtuel Azure. Vérifiez vos règles de groupe de sécurité réseau pour vous assurer que le trafic de bureau à distance à partir d’Internet est autorisé :

- Dans le portail Azure, sélectionnez votre machine virtuelle
- Cliquez sur **tous les paramètres** | **interfaces réseau** et sélectionnez votre interface réseau.
- Cliquez sur **tous les paramètres** | **groupe de sécurité réseau** et sélectionnez votre groupe de sécurité réseau.
- Cliquez sur **tous les paramètres** | **les règles de sécurité entrant** et assurez-vous que vous disposez d’une règle qui permet de RDP sur le port TCP 3389.
    - Si vous ne disposez pas d’une règle, cliquez sur **Ajouter** pour créer une règle. Entrez **TCP** pour le protocole, puis **3389** de la plage de port de destination.
    - Assurez-vous que l’action est définie sur **Autoriser** et cliquez sur OK pour enregistrer votre nouvelle règle de trafic entrant.


Pour plus d’informations, voir [qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Source 5 : Basés sur Windows Azure virtuelle

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Utilisez le [package d’outils de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour voir si le problème provient de la machine virtuelle Azure lui-même. Si ce package diagnostics ne parvient pas à résoudre le problème de **connectivité RDP pour une machine virtuelle Azure (redémarrage obligatoire)** , suivez les instructions de [cet article](virtual-machines-windows-reset-rdp.md). Cet article réinitialise le service de bureau à distance sur l’ordinateur virtuel :

- Activer la règle par défaut de pare-feu Windows « Bureau à distance » (le port TCP 3389).
- Activer des connexions de bureau à distance en définissant la valeur de Registre HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections sur 0.

Réessayez de vous connecter à partir de votre ordinateur. Si vous ne parvenez toujours pas à vous connecter via le Bureau à distance, vérifier les problèmes suivants :

- Le service de bureau à distance ne fonctionne pas sur la machine virtuelle cible.
- Le service de bureau à distance n’écoute pas sur le port TCP 3389.
- Pare-feu Windows ou un autre pare-feu local utilise une règle sortante qui empêche le trafic de bureau à distance.
- Détection ou logiciels en cours d’exécution sur l’ordinateur virtuel Azure d’analyse réseau empêche connexions Bureau à distance.

Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, vous pouvez utiliser une session PowerShell Azure à distance à la machine virtuelle Azure. Tout d’abord, vous devez installer un certificat pour d’hébergement cloud service la machine virtuelle. Accédez à [Configurer accès distant sécurisé PowerShell pour le Machines virtuelles Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) et téléchargez le fichier de script **InstallWinRMCertAzureVM.ps1** sur votre ordinateur local.

Ensuite, installez PowerShell Azure si vous n’avez pas déjà. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Ensuite, ouvrez une invite de commandes PowerShell Azure et modifier le dossier actif à l’emplacement du fichier de script **InstallWinRMCertAzureVM.ps1** . Pour exécuter un script PowerShell Azure, vous devez définir la stratégie d’exécution correct. Exécutez la commande **Get-ExecutionPolicy** pour déterminer le niveau de stratégie actuel. Pour plus d’informations sur la définition du niveau approprié, voir [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Ensuite, renseignez nom de votre abonnement Azure, le nom du service cloud et le nom de votre machine virtuelle (suppression de la < et > caractères), puis exécutez ces commandes.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Vous pouvez obtenir le nom de l’abonnement correct de la propriété _SubscriptionName_ de l’affichage de la commande **Get-AzureSubscription** . Vous pouvez obtenir le nom du service cloud pour la machine virtuelle à partir de la colonne _ServiceName%_ dans l’affichage de la commande **Get-AzureVM** .

Vérifiez si vous avez le nouveau certificat. Ouvrir un composant logiciel enfichable Certificats pour l’utilisateur actuel et consultez le dossier **Authorities\Certificates de Certification racine de confiance** . Vous devez voir un certificat avec le nom DNS de votre service cloud dans la colonne délivré à (exemple : cloudservice4testing.cloudapp.net).

Ensuite, démarrer une session PowerShell Azure distante à l’aide de ces commandes.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Après avoir entré les informations d’identification d’administrateur valide, vous devriez voir doit ressembler à l’invite Azure PowerShell suivante :

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La première partie de cette invite est le nom de votre service cloud qui contient la cible machine virtuelle, qui peut être différent de « cloudservice4testing.cloudapp.net ». Vous pouvez maintenant émettre Azure PowerShell commandes pour ce service cloud examiner les problèmes mentionnés et corrigez la configuration.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Pour corriger manuellement les Services Bureau à distance écoute le port TCP

Si vous ne pouvez pas exécuter le [package Outils de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour le problème de **connectivité RDP pour une machine virtuelle Azure (redémarrage obligatoire)** , à l’invite de session distante Azure PowerShell, exécutez cette commande.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propriété de numéro de port affiche le numéro de port actuel. Si nécessaire, modifiez le Bureau à distance port précédent numéro en sa valeur par défaut (3389) à l’aide de cette commande.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Vérifiez que le port a été modifié 3389 à l’aide de cette commande.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Quitter la session PowerShell Azure à distance en utilisant cette commande.

    Exit-PSSession

Vérifiez que le point de terminaison de bureau à distance pour la machine virtuelle Azure utilise également le port TCP 3398 comme son port interne. Redémarrez la machine virtuelle Azure et essayez à nouveau la connexion Bureau à distance.


## <a name="additional-resources"></a>Ressources supplémentaires

[Package de diagnostics Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Comment réinitialiser un mot de passe ou le service de bureau à distance pour machines virtuelles Windows](virtual-machines-windows-reset-rdp.md)

[Comment installer et configurer PowerShell Azure](../powershell-install-configure.md)

[Résoudre les problèmes de connexions SSH (Secure Shell) à une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Résoudre les problèmes d’accès à une application en cours d’exécution sur un ordinateur virtuel Azure](virtual-machines-linux-troubleshoot-app-connection.md)
