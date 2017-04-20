<properties
    pageTitle="Installer un contrôleur de domaine Active Directory réplica dans Azure | Microsoft Azure"
    description="Didacticiel qui explique comment installer un contrôleur de domaine à partir d’une forêt Active Directory local sur une machine virtuelle Azure."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installer un contrôleur de domaine Active Directory réplica dans un réseau virtuel Azure

Cette rubrique indique comment installer un contrôleur de domaine supplémentaire (également appelé réplica DC) pour un domaine Active Directory local sur des machines virtuelles Azure (machines virtuelles) dans un réseau virtuel Azure.

Vous serez peut également être intéressé dans ces rubriques connexes :

-  Vous pouvez éventuellement installer une nouvelle forêt Active Directory sur un réseau virtuel Azure. Pour ces étapes, voir [installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Pour obtenir des instructions sur l’installation des Services de domaine Active Directory (AD DS) sur un réseau virtuel Azure conceptuelle, reportez-vous [aux instructions pour le déploiement d’Active Directory Windows Server Azure machines virtuelles en fonctionnement](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Diagramme de scénario

Dans ce scénario, les utilisateurs externes doivent accéder aux applications qui s’exécutent sur des serveurs à un domaine. Les ordinateurs virtuels qui s’exécutent sur les serveurs d’applications et les DC réplica sont installés dans un réseau virtuel Azure. Le réseau virtuel pouvant être connecté au réseau local par une connexion [à un site à VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) , comme indiqué dans le diagramme suivant, ou vous pouvez utiliser [ExpressRoute](../expressroute/expressroute-locations-providers.md) pour une connexion plus rapide.

Les serveurs d’applications et les DC sont déployés dans les services cloud distincte pour distribuer le traitement des cluster et [ensembles de disponibilité](../virtual-machines/virtual-machines-windows-manage-availability.md) d’une meilleure tolérance.
DC répliquent entre eux et avec DC local à l’aide de la réplication Active Directory. Aucun outil de synchronisation n’est nécessaires.

![Contrôleur de domaine Active Directory diagramme pf réplica un vnet Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Créer un site Active Directory pour le réseau virtuel Azure

Il est conseillé de créer un site dans Active Directory qui représente la zone du réseau correspondant au réseau virtuel. Qui permet d’optimiser l’authentification, la réplication et autres opérations de localisation du contrôleur de domaine. Les étapes suivantes expliquent comment créer un site et pour plus d’informations, voir [Ajouter un nouveau Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Ouvrez Sites et Services Active Directory : **Gestionnaire de serveur** > **Outils** > **Sites et Services Active Directory**.
2. Créer un site pour représenter la zone où vous avez créé un réseau virtuel Azure : cliquez sur **Sites** > **Action** > **Nouveau site** > tapez le nom du nouveau site, tel que Azure nous ouest > Sélectionner un lien de site > **OK**.
3. Créer un sous-réseau et associer le nouveau site : double-cliquez sur **Sites** > avec le bouton droit **sous-réseaux** > **nouveau sous-réseau** > tapez la plage d’adresses IP du réseau virtuel (par exemple, 10.1.0.0/16 dans le diagramme de scénario) > sélectionnez le nouveau site Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Créer un réseau virtuel Azure

1. Dans le [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Services de réseau** > **Réseau virtuel** > **Personnalisé créer** et utiliser ce qui suit les valeurs pour terminer l’Assistant.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Détails du réseau virtuel**  | <p>Nom : Tapez un nom pour le réseau virtuel, par exemple WestUSVNet.</p><p>Région : Choisir la région le plus proche.</p>
    **Connectivité VPN et DNS**  | <p>Serveurs DNS : Spécifiez le nom et l’adresse IP d’un ou plusieurs serveurs DNS locaux.</p><p>Connectivité : Sélectionnez **configurer un réseau privé virtuel de site à**.</p><p>Réseau local : spécifier un réseau local.</p><p>Si vous utilisez ExpressRoute au lieu d’un réseau privé virtuel, consultez [configurer une connexion ExpressRoute via un fournisseur d’Exchange](../expressroute/expressroute-locations-providers.md).</p>
    **Connectivité à un site**  | <p>Nom : Tapez un nom pour le réseau local.</p><p>Adresse IP du périphérique VPN : spécifiez l’adresse IP du périphérique qui se connecteront au réseau virtuel. Le périphérique VPN ne peut pas être situé derrière un NAT.</p><p>Adresse : Indiquez les plages d’adresses pour votre réseau local (par exemple, 192.168.0.0/16 dans le diagramme de scénario).</p>
    **Espaces d’adressage réseau virtuel**  | <p>Espace d’adressage : Spécifiez la plage d’adresses IP pour les machines virtuelles que vous voulez exécuter dans le réseau virtuel Azure (par exemple, 10.1.0.0/16 dans le diagramme de scénario). Cette plage d’adresses ne peut pas recouvrir les plages d’adresses du réseau local.</p><p>Sous-réseaux : Spécifiez un nom et une adresse pour un sous-réseau pour les serveurs d’applications (par exemple Frontend, 10.1.1.0/24) et du contrôleur de domaine (par exemple serveur principal, 10.1.2.0/24).</p><p>Cliquez sur **Ajouter un sous-réseau passerelle**.</p>

2. Ensuite, vous devez configurer la passerelle réseau virtuel pour créer une connexion VPN de site à sécurisée. Voir [configurer une passerelle réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) pour les instructions.
3. Créer la connexion VPN site à site entre le réseau virtuel et un périphérique VPN en local. Voir [configurer une passerelle réseau virtuel](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) pour les instructions.


## <a name="create-azure-vms-for-the-dc-roles"></a>Créer des ordinateurs virtuels Azure pour les rôles contrôleur de domaine

Répétez les étapes suivantes pour créer des ordinateurs virtuels pour héberger le rôle de contrôleur de domaine selon vos besoins. Vous devez déployer au moins deux DC virtuel pour fournir une redondance et tolérance de panne. Si le réseau virtuel Azure inclut au moins deux DC qui est configurés de la même façon (autrement dit, ils sont les deux catalogues globaux, serveur DNS exécution, et aucune contient un rôle FSMO et ainsi de suite) puis placez les ordinateurs virtuels qui s’exécutent ceux DC dans une disponibilité définies pour une meilleure tolérance.
Pour créer les ordinateurs virtuels à l’aide de Windows PowerShell au lieu de l’interface utilisateur, voir [Utiliser Azure PowerShell pour créer et de préconfigurer Machines virtuelles fonctionnant sous Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Dans le [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **De la galerie**. Utilisez les valeurs suivantes pour exécuter l’Assistant. Acceptez la valeur par défaut pour un paramètre, sauf si une autre valeur suggérée ou obligatoire.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Choisir une Image**  | Centre de données Windows Server 2012 R2
    **Configuration de Machine virtuelle**  | <p>Machine virtuelle Name : Tapez un nom d’étiquette unique (par exemple, AzureDC1).</p><p>Nouveau nom d’utilisateur : Tapez le nom d’un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur l’ordinateur virtuel. Vous avez besoin de ce nom pour se connecter à la machine virtuelle pour la première fois. Le compte intégré nommé administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmer : Tapez un mot de passe</p>
    **Configuration de Machine virtuelle**  | <p>Service cloud : Choisissez <b>créer un nouveau service cloud</b> pour la première machine virtuelle et sélectionnez ce nom de service cloud même lorsque vous créez davantage d’ordinateurs virtuels hébergeant le rôle de contrôleur de domaine.</p><p>Nom du DNS Service cloud : Attribuez un nom unique</p><p>Région/groupe affinité/réseau virtuelle : indiquez le nom de réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez <b>d’utiliser un compte de stockage généré automatiquement</b> pour la première machine virtuelle, puis sélectionnez ce nom de compte de stockage même lorsque vous créez davantage d’ordinateurs virtuels hébergeant le rôle de contrôleur de domaine.</p><p>Jeu de disponibilité : Cliquez sur <b>créer un jeu de disponibilité</b>.</p><p>Nom du jeu de disponibilité : tapez un nom pour la disponibilité défini lorsque vous créez la première machine virtuelle, puis sélectionnez que même nom lorsque vous créez davantage d’ordinateurs virtuels.</p>
    **Configuration de Machine virtuelle**  | <p>Sélectionnez <b>installer l’Agent de machine virtuelle</b> et toutes les extensions que vous avez besoin.</p>
2. Connectez un disque à chaque machine virtuelle qui exécutera le rôle de serveur contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple, 10 Go) et laissez la **Préférence de Cache hôte** définie sur **Aucune**. Pour connaître la procédure, voir [l’insertion d’un disque de données pour une Machine virtuelle Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Une fois que vous êtes connectez à la machine virtuelle, ouvrez le **Gestionnaire de serveur** > **Services de stockage de fichiers et** créer un volume sur ce disque à l’aide de NTFS.
4. Réserver une adresse IP statique pour les machines virtuelles qui seront exécutera le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez le programme d’installation de Microsoft Web plateforme et [installez PowerShell Azure](../powershell-install-configure.md) et exécutez l’applet de commande Set-AzureStaticVNetIP. Par exemple :

    « Get-AzureVM - ServiceName AzureDC1-nommer AzureDC1 | Jeu-AzureStaticVNetIP - adresse IP 10.0.0.4 | Mise à jour AzureVM

Pour plus d’informations sur la définition d’une adresse IP statique, consultez [configurer une adresse IP statique interne d’un ordinateur virtuel](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installez les services AD DS sur machines virtuelles Azure

Se connecter à une machine virtuelle et vérifiez que vous êtes connecté sur la connexion de réseau privé virtuel ou ExpressRoute site aux ressources sur votre réseau local. Puis installez les services AD DS sur les ordinateurs virtuels Azure. Vous pouvez utiliser la même procédure que vous utilisez pour installer un contrôleur de domaine supplémentaire sur votre réseau local (Windows PowerShell, l’interface utilisateur ou un fichier de réponses). Lorsque vous installez les services AD DS, vérifiez que vous spécifiez le nouveau volume à l’emplacement de la base de données Active Directory, les journaux et SYSVOL. Si vous avez besoin d’un rappel sur l’installation de AD DS, voir [Installer Active Directory Domain Services (niveau 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [installer un contrôleur de domaine réplica Windows Server 2012 dans un domaine existant (niveau 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigurer serveur DNS pour le réseau virtuel

1. Dans le [portail classique Azure](https://manage.windowsazure.com), cliquez sur le nom du réseau virtuel, puis cliquez sur l’onglet **configurer** [reconfigurer les adresses IP de serveur DNS pour votre réseau virtuel](../virtual-network/virtual-networks-manage-dns-in-vnet.md) à utiliser les adresses IP affectées à la réplique DC au lieu des adresses IP d’un serveurs DNS locaux.

2. Pour vous assurer que toutes les réplica machines virtuelles contrôleur de domaine sur le réseau virtuel sont configurés avec à utiliser les serveurs DNS sur le réseau virtuel, cliquez sur **Machines virtuelles**et cliquez sur la colonne statut pour chaque machine virtuelle, puis cliquez sur **redémarrer**. Attendez que la machine virtuelle indique **en cours d’exécution** avant d’essayer de vous connecter à celle-ci.

## <a name="create-vms-for-application-servers"></a>Créer des machines virtuelles pour les serveurs d’applications

1. Répétez les étapes suivantes pour créer des machines virtuelles à exécuter en tant que serveurs d’applications. Acceptez la valeur par défaut pour un paramètre, sauf si une autre valeur suggérée ou obligatoire.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Choisir une Image**  | Centre de données Windows Server 2012 R2
    **Configuration de Machine virtuelle**  | <p>Machine virtuelle Name : Tapez un nom d’étiquette unique (par exemple, AppServer1).</p><p>Nouveau nom d’utilisateur : Tapez le nom d’un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur l’ordinateur virtuel. Vous avez besoin de ce nom pour se connecter à la machine virtuelle pour la première fois. Le compte intégré nommé administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmer : Tapez un mot de passe</p>
    **Configuration de Machine virtuelle**  | <p>Service cloud : Choisissez **créer un nouveau service cloud** pour la première machine virtuelle et sélectionnez ce nom de service cloud même lorsque vous créez plusieurs machines virtuelles qui hébergent l’application.</p><p>Nom du DNS Service cloud : Attribuez un nom unique</p><p>Région/groupe affinité/réseau virtuelle : indiquez le nom de réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez **d’utiliser un compte de stockage généré automatiquement** pour la première machine virtuelle, puis sélectionnez ce nom de compte de stockage même lorsque vous créez plusieurs machines virtuelles qui hébergent l’application.</p><p>Jeu de disponibilité : Cliquez sur **créer un jeu de disponibilité**.</p><p>Nom du jeu de disponibilité : tapez un nom pour la disponibilité défini lorsque vous créez la première machine virtuelle, puis sélectionnez que même nom lorsque vous créez davantage d’ordinateurs virtuels.</p>
    **Configuration de Machine virtuelle**  | <p>Sélectionnez <b>installer l’Agent de machine virtuelle</b> et toutes les extensions que vous avez besoin.</p>

2. Une fois que chaque machine virtuelle est mis en service, se connecter et joindre au domaine. Dans le **Gestionnaire de serveur**, cliquez sur **Le serveur Local** > **groupe de travail** > **Change...** puis sélectionnez **domaine** et tapez le nom de votre domaine local. Fournir des informations d’identification d’un utilisateur de domaine et redémarrez la machine virtuelle pour terminer la jointure domaine.

Pour créer les ordinateurs virtuels à l’aide de Windows PowerShell au lieu de l’interface utilisateur, voir [Utiliser Azure PowerShell pour créer et de préconfigurer Machines virtuelles fonctionnant sous Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Pour plus d’informations sur l’utilisation de Windows PowerShell, voir [Prise en main applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) et [Azure applet de commande référence](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires

-  [Instructions pour le déploiement d’Active Directory Windows Server sur des Machines virtuelles Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Comment télécharger existants superflus locaux Hyper-V dans Azure à l’aide de PowerShell Azure](http://support.microsoft.com/kb/2904015)
-  [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IaaS Pro informatique : notions de base (01) Machine virtuelle](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS Pro informatique : (05) réseaux virtuels création et connectivité entre locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Applets de commande de gestion Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
