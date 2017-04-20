<properties
    pageTitle="Installer une forêt Active Directory sur un réseau virtuel Azure | Microsoft Azure"
    description="Didacticiel qui explique comment créer une nouvelle forêt Active Directory sur une machine virtuelle (machine virtuelle) sur un réseau virtuel Azure."
    services="active-directory, virtual-network"
    keywords="machine virtuelle Active directory, forêt active directory installer, vidéos azure AD "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure

Cette rubrique montre comment créer un environnement Active Directory Windows Server sur un réseau virtuel Azure sur une machine virtuelle () sur un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Dans ce cas, le réseau virtuel Azure n’est pas connecté à un réseau local.

Vous serez peut également être intéressé dans ces rubriques connexes :

- Pour une vidéo qui décrit ces étapes, voir [comment installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Vous pouvez éventuellement [configurer un site à VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) , puis des options installer une nouvelle forêt ou étendre une forêt local à un réseau virtuel Azure. Pour ces étapes, voir [installer un contrôleur de domaine réplica Active Directory dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Pour obtenir des instructions sur l’installation des Services de domaine Active Directory (AD DS) sur un réseau virtuel Azure conceptuelle, reportez-vous [aux instructions pour le déploiement d’Active Directory Windows Server Azure machines virtuelles en fonctionnement](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagramme de scénario

Dans ce scénario, les utilisateurs externes doivent accéder aux applications qui s’exécutent sur des serveurs à un domaine. Les ordinateurs virtuels qui s’exécutent les serveurs d’applications et les ordinateurs virtuels qui s’exécutent superflus sont installés installés dans leur propre service cloud dans un réseau virtuel Azure. Ils sont également inclus dans une disponibilité définies pour une meilleure tolérance.

![Forêt Active Directory sur un machines virtuelles réseau virtuel Azure ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>En quoi il diffère en local ?

Il n’est pas beaucoup de différences entre l’installation d’un contrôleur de domaine sur Azure et en local. Les principales différences sont répertoriées dans le tableau suivant.

Pour configurer...  | En local  | Réseau virtuel Azure
------------- | -------------  | ------------
**Adresse IP du contrôleur de domaine**  | Attribuer une adresse IP statique sur les propriétés de la carte réseau   | Exécuter l’applet de commande Set-AzureStaticVNetIP pour attribuer une adresse IP statique
**Résolution du client DNS**  | Définir par défaut et autre serveur DNS adresse du serveur sur le réseau propriétés de la carte de membres du domaine   | Définir l’adresse du serveur DNS dans les propriétés du réseau virtuel
**Stockage de base de données Active Directory**  | Vous pouvez également modifier l’emplacement de stockage par défaut de C:\  | Vous devez modifier l’emplacement de stockage par défaut à partir de C:\



## <a name="create-an-azure-virtual-network"></a>Créer un réseau virtuel Azure

1. Connectez-vous au portail classique Azure.
2. Créer un réseau virtuel. Cliquez sur **les réseaux** > **créer un réseau virtuel**. Utilisez les valeurs du tableau ci-dessous pour terminer l’Assistant.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Détails du réseau virtuel**  | <p>Nom : Entrez un nom pour votre réseau virtuel</p><p>Région : Choisir la région le plus proche</p>
    **DNS et VPN**  | <p>Laissez serveur DNS vide</p><p>Ne sélectionnez pas l’option VPN</p>
    **Espaces d’adressage réseau virtuel**  | <p>Nom du sous-réseau : entrez un nom pour votre sous-réseau</p><p>Adresse IP de début : <b>10.0.0.0</b></p><p>CIDR : <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Créer des ordinateurs virtuels pour exécuter le contrôleur de domaine et les rôles des serveurs DNS

Répétez les étapes suivantes pour créer des ordinateurs virtuels pour héberger le rôle de contrôleur de domaine selon vos besoins. Vous devez déployer au moins deux DC virtuel pour fournir une redondance et tolérance de panne. Si le réseau virtuel Azure inclut au moins deux DC qui est configurés de la même façon (autrement dit, ils sont les deux catalogues globaux, serveur DNS exécution, et aucune contient un rôle FSMO et ainsi de suite) puis placez les ordinateurs virtuels qui s’exécutent ceux DC dans une disponibilité définies pour une meilleure tolérance.

Pour créer les ordinateurs virtuels à l’aide de Windows PowerShell au lieu de l’interface utilisateur, voir [Utiliser Azure PowerShell pour créer et de préconfigurer Machines virtuelles fonctionnant sous Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Dans le portail classique, cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **De la galerie**. Utilisez les valeurs suivantes pour exécuter l’Assistant. Acceptez la valeur par défaut pour un paramètre, sauf si une autre valeur suggérée ou obligatoire.

    Sur cette page de l’Assistant...  | Spécifiez ces valeurs
    ------------- | -------------
    **Choisir une Image**  | Centre de données Windows Server 2012 R2
    **Configuration de Machine virtuelle**  | <p>Machine virtuelle Name : Tapez un nom d’étiquette unique (par exemple, AzureDC1).</p><p>Nouveau nom d’utilisateur : Tapez le nom d’un utilisateur. Cet utilisateur sera membre du groupe Administrateurs local sur l’ordinateur virtuel. Vous avez besoin de ce nom pour se connecter à la machine virtuelle pour la première fois. Le compte intégré nommé administrateur ne fonctionnera pas.</p><p>Nouveau mot de passe/confirmer : Tapez un mot de passe</p>
    **Configuration de Machine virtuelle**  | <p>Service cloud : Choisissez <b>créer un nouveau service cloud</b> pour la première machine virtuelle et sélectionnez ce nom de service cloud même lorsque vous créez davantage d’ordinateurs virtuels hébergeant le rôle de contrôleur de domaine.</p><p>Nom du DNS Service cloud : Attribuez un nom unique</p><p>Région/groupe affinité/réseau virtuelle : indiquez le nom de réseau virtuel (par exemple, WestUSVNet).</p><p>Compte de stockage : Choisissez <b>d’utiliser un compte de stockage généré automatiquement</b> pour la première machine virtuelle, puis sélectionnez ce nom de compte de stockage même lorsque vous créez davantage d’ordinateurs virtuels hébergeant le rôle de contrôleur de domaine.</p><p>Jeu de disponibilité : Cliquez sur <b>créer un jeu de disponibilité</b>.</p><p>Nom du jeu de disponibilité : tapez un nom pour la disponibilité défini lorsque vous créez la première machine virtuelle, puis sélectionnez que même nom lorsque vous créez davantage d’ordinateurs virtuels.</p>
    **Configuration de Machine virtuelle**  | <p>Sélectionnez <b>installer l’Agent de machine virtuelle</b> et toutes les extensions que vous avez besoin.</p>
2. Connectez un disque à chaque ordinateur virtuel qui exécutent le rôle de serveur contrôleur de domaine. Le disque supplémentaire est nécessaire pour stocker la base de données Active Directory, les journaux et SYSVOL. Spécifiez une taille pour le disque (par exemple, 10 Go) et laissez la **Préférence de Cache hôte** définie sur **Aucune**. Pour connaître la procédure, voir [l’insertion d’un disque de données pour une Machine virtuelle Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Une fois que vous êtes connectez à la machine virtuelle, ouvrez le **Gestionnaire de serveur** > **Services de stockage de fichiers et** créer un volume sur le disque à l’aide de NTFS.
4. Réserver une adresse IP statique pour les machines virtuelles qui seront exécutera le rôle de contrôleur de domaine. Pour réserver une adresse IP statique, téléchargez le programme d’installation de Microsoft Web plate-forme et [installez PowerShell Azure](../powershell-install-configure.md) et exécutez l’applet de commande Set-AzureStaticVNetIP. Par exemple :

    « Get-AzureVM - ServiceName AzureDC1-nommer AzureDC1 | Jeu-AzureStaticVNetIP - adresse IP 10.0.0.4 | Mise à jour AzureVM

Pour plus d’informations sur la définition d’une adresse IP statique, consultez [configurer une adresse IP statique interne d’un ordinateur virtuel](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Installer Windows Server Active Directory

Utiliser la même routine pour [installer AD DS](https://technet.microsoft.com/library/jj574166.aspx) que vous utilisez locaux (autrement dit, vous pouvez utiliser l’interface utilisateur, un fichier de réponses ou Windows PowerShell). Vous devez fournir les informations d’identification d’administrateur pour installer une nouvelle forêt. Pour spécifier l’emplacement de la base de données Active Directory, les journaux et SYSVOL, modifiez l’emplacement de stockage par défaut à partir du lecteur de système d’exploitation sur le disque des données supplémentaires que vous avez joint à la machine virtuelle.

Après que l’installation de contrôleur de domaine est terminée, vous reconnecter à la machine virtuelle et ouvrez une session sur le contrôleur de domaine. N’oubliez pas de spécifier des informations d’identification de domaine.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Réinitialiser le serveur DNS pour le réseau virtuel Azure

1. Réinitialisez le paramètre de redirecteurs DNS sur le nouveau serveur DNS/contrôleur de domaine.
  1. Dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS**.
  2. Dans le **Gestionnaire DNS**, double-cliquez sur le nom du serveur DNS, puis cliquez sur **Propriétés**.
  3. Sous l’onglet **redirecteurs** , cliquez sur l’adresse IP de le redirecteurs et cliquez sur **Modifier**.  Sélectionnez l’adresse IP, puis cliquez sur **Supprimer**.
  4. Cliquez sur **OK** pour fermer l’éditeur et **Ok** pour fermer les propriétés du serveur DNS.
2. Mettre à jour les paramètres de serveur DNS pour le réseau virtuel.
  1. Cliquez sur **Les réseaux virtuels** > Double-cliquez sur le réseau virtuel, vous avez créé > **configurer** > **serveurs DNS**, tapez le nom et le fondu d’un des ordinateurs virtuels qui s’exécute le rôle de serveur DNS/contrôleur de domaine, cliquez sur **Enregistrer**.
  2. Sélectionnez la machine virtuelle et cliquez sur **redémarrer** pour déclencher la machine virtuelle pour configurer les paramètres de résolution DNS avec l’adresse IP du nouveau serveur DNS.


## <a name="create-vms-for-domain-members"></a>Créer des ordinateurs virtuels pour les membres du domaine

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


## <a name="see-also"></a>Voir aussi

-  [Comment installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Instructions pour le déploiement d’Active Directory Windows Server sur des Machines virtuelles Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurer un réseau VPN Site à](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installer un contrôleur de domaine Active Directory réplica dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IaaS Pro informatique : notions de base (01) Machine virtuelle](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS Pro informatique : (05) réseaux virtuels création et la connectivité entre locaux](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)
-  [Comment installer et configurer PowerShell Azure](../powershell-install-configure.md)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure référence applet de commande](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Définir une adresse IP statique machine virtuelle Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Comment affecter des adresses IP statiques à machine virtuelle Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installer une nouvelle forêt Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introduction à Active Directory Domain Services (AD DS) virtualisation (niveau 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
