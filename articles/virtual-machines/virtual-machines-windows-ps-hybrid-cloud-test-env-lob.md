<properties 
    pageTitle="Environnement de test d’application métier | Microsoft Azure" 
    description="Apprenez à créer une ligne sur le web, d’applications d’entreprise dans un environnement cloud hybride pour IT pro ou tests de développement." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurer une application métier basée sur le web dans un nuage hybride pour le test

Cette rubrique vous guide dans la création d’un environnement de cloud hybride simulé pour tester une ligne sur le web d’application métier hébergée dans Microsoft Azure. Voici la configuration qui en résulte.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Cette configuration se compose de :

- Un réseau simulé local hébergé dans Azure (la TestLab VNet).
- Réseau local entre virtuel hébergé dans Azure (TestVNET).
- Une connexion VPN VNet-VNet.
- Un serveur web métier, SQL server et contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration offre une base et le point de départ commun à partir de laquelle vous pouvez :

- Développer et tester des applications métiers hébergées sur Internet Information Services (IIS) par un serveur de base de données SQL Server 2014 principal dans Azure.
- Effectuez des tests de cette charge informatique hybride simulé sur le nuage.

Il existe trois phases principales configuration de cet environnement de test cloud hybride :

1.  Configurer l’environnement cloud hybride simulé.
2.  Configurer l’ordinateur SQL server (SQL1).
3.  Configurer le serveur métier (LOB1).

Cette charge de travail nécessite un abonnement Azure. Si vous avez souscrit un abonnement MSDN ou Visual Studio, voir [bancaire mensuel Azure pour Visual Studio abonnés](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Pour obtenir un exemple d’une application métier hébergée dans Azure de production, consultez le plan d’architecture **des applications métier** en [diagrammes d’Architecture logiciel Microsoft et les projets](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Phase 1 : Configurer l’environnement cloud hybride simulé

Créer la [simulé à l’environnement de test cloud hybride](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Étant donné que cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau du réseau d’entreprise, vous pouvez le fermer pour l’instant.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Phase 2 : Configurer l’ordinateur SQL server (SQL1)

À partir du portail Azure, démarrez l’ordinateur DC2 si nécessaire.

Ensuite, créez une machine virtuelle pour SQL1 avec ces commandes à une invite de commande PowerShell Azure sur votre ordinateur local. Avant d’exécuter ces commandes, renseignez les valeurs de variables et de supprimer les caractères < et >.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Utiliser le portail Azure pour vous connecter à SQL1 utilisant le compte d’administrateur local de SQL1.

Ensuite, configurez les règles du pare-feu Windows pour autoriser les tests de connectivité de base et le trafic de SQL Server. À partir d’une invite de commande Windows PowerShell au niveau de l’administrateur sur le serveur SQL1, exécutez ces commandes.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

La commande ping doit entraîner quatre réponses correctes s’affichent à partir de l’adresse IP 192.168.0.4.

Ensuite, ajoutez le disque de données supplémentaires sur le serveur SQL1 en tant que nouveau volume par la lettre f.

1.  Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de stockage et de fichier**, puis cliquez sur **disques**.
2.  Dans le volet contenu, dans le groupe de **disques** , cliquez sur **le disque 2** (avec la **Partition** définie sur **inconnu**).
3.  Cliquez sur **tâches**, puis cliquez sur **Nouveau Volume**.
4.  Dans le champ avant que vous commencer des pages de l’Assistant Nouveau Volume, cliquez sur **suivant**.
5.  Dans la page Sélectionnez le serveur et disque, cliquez sur **disque 2**, puis cliquez sur **suivant**. Lorsque vous y êtes invité, cliquez sur **OK**.
6.  Dans la zone spécifier la taille de la page de volume, cliquez sur **suivant**.
7.  Dans l’attribuer à une page de lecteur lettre ou un dossier, cliquez sur **suivant**.
8.  Dans la page Paramètres du système sélectionnez le fichier, cliquez sur **suivant**.
9.  Dans la page de sélections confirmer, cliquez sur **créer**.
10. Lorsque vous avez terminé, cliquez sur **Fermer**.

Exécuter ces commandes à l’invite de commande Windows PowerShell sur SQL1 :

    md f:\Data
    md f:\Log
    md f:\Backup

Ensuite, joindre SQL1 au domaine Active Directory CORP Windows Server avec les commandes suivantes à l’invite de Windows PowerShell SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Utiliser le compte CORP\User1 lorsque vous êtes invité à fournir les informations d’identification du compte de domaine pour la commande **Ajouter à un ordinateur** .

Après avoir redémarré, utiliser le portail Azure pour vous connecter à SQL1 *avec le compte d’administrateur local de SQL1*.

Ensuite, configurez SQL Server 2014 pour utiliser le lecteur f pour les nouvelles bases de données et des autorisations de compte d’utilisateur.

1.  À partir de l’écran Démarrer, tapez **Gestion de SQL Server**, puis cliquez sur **SQL Server 2014 Management Studio**.
2.  Dans **se connecter au serveur**, cliquez sur **se connecter**.
3.  Dans le volet d’arborescence Explorateur d’objets, avec le bouton droit **SQL1**, puis cliquez sur **Propriétés**.
4.  Dans la fenêtre **Propriétés du serveur** , cliquez sur **Paramètres de base de données**.
5.  Recherchez l' **emplacement par défaut de base de données** , définissez les valeurs suivantes : 
    - Pour les **données**, tapez le chemin d’accès **f:\Data**.
    - **Journal**, tapez le chemin d’accès **f:\Log**.
    - Pour la **sauvegarde**, tapez le chemin d’accès **f:\Backup**.
    - Remarque : Uniquement les nouvelles bases de données utilisent ces emplacements.
6.  Cliquez sur **OK** pour fermer la fenêtre.
7.  Dans le volet d’arborescence **Explorateur d’objets** , ouvrez **sécurité**.
8.  Cliquez sur **connexions** , puis sur **Nouvelle connexion**.
9.  Dans la zone **nom de connexion**, tapez **CORP\User1**.
10. Dans la page **Rôles de serveur** , cliquez sur **sysadmin**, puis cliquez sur **OK**.
11. Fermez Microsoft SQL Server Management Studio.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Phase 3 : Configurer le serveur métier (LOB1)

Tout d’abord, créez une machine virtuelle pour LOB1 avec les commandes suivantes à l’invite de commande PowerShell Azure sur votre ordinateur local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, utilisez le portail Azure pour vous connecter à LOB1 avec les informations d’identification de compte d’administrateur local de LOB1.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de connectivité de base. À partir d’une invite de commande Windows PowerShell au niveau de l’administrateur sur LOB1, exécutez ces commandes.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

La commande ping doit entraîner quatre réponses correctes s’affichent à partir de l’adresse IP 192.168.0.4.

Joindre ensuite LOB1 au domaine Active Directory CORP avec ces commandes à l’invite de Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Utiliser le compte CORP\User1 lorsque vous êtes invité à fournir les informations d’identification du compte de domaine pour la commande **Ajouter à un ordinateur** .

Après avoir redémarré, utiliser le portail Azure pour vous connecter à LOB1 avec le compte CORP\User1 et le mot de passe.

Ensuite, configurez LOB1 pour IIS et tester l’accès à partir de CLIENT1.

1.  Dans le Gestionnaire de serveur, cliquez sur **Ajouter des rôles et les fonctionnalités**.
2.  Dans la page **avant de commencer** , cliquez sur **suivant**.
3.  Dans la page **Sélectionnez le type d’installation** , cliquez sur **suivant**.
4.  Dans la page **Sélectionner le serveur de destination** , cliquez sur **suivant**.
5.  Dans la page **rôles de serveur** , cliquez sur **Le serveur Web (IIS)** dans la liste des **rôles**.
6.  Lorsque vous y êtes invité, cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **suivant**.
7.  Dans la page **Sélectionner des fonctionnalités** , cliquez sur **suivant**.
8.  Dans la page **Web Server (IIS)** , cliquez sur **suivant**.
9.  Dans la page **Sélectionnez les services de rôle** , activez ou désactivez les cases à cocher pour les services que vous avez besoin pour tester votre application métier, puis sur **suivant**.
10. Dans la page **Confirmer sélections pour l’installation** , cliquez sur **installer**.
11. Attendre la fin de l’installation des composants, puis sur **Fermer**.
12. À partir du portail Azure, connectez-vous à l’ordinateur CLIENT1 avec les informations d’identification du compte CORP\User1 et puis démarrez Internet Explorer.
13. Dans la barre d’adresses, tapez **http://lob1/** et appuyez sur ENTRÉE. Vous devez voir la page de web IIS 8 par défaut.

Il s’agit de votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Cet environnement est maintenant prêt à déployer votre application web basée sur LOB1 et tester les fonctionnalités à partir de CLIENT1 sur le sous-réseau du réseau d’entreprise.

## <a name="next-step"></a>Étape suivante

- Ajouter un nouvel ordinateur virtuel à l’aide du [portail Azure](virtual-machines-windows-hero-tutorial.md).
