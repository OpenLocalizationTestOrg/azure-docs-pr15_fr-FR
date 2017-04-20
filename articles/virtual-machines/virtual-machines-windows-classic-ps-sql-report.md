<properties 
    pageTitle="Utiliser PowerShell pour créer une machine virtuelle avec un serveur de rapports en Mode natif | Microsoft Azure"
    description="Cette rubrique décrit et vous guide dans le déploiement et la configuration d’un serveur de rapports SQL Server Reporting Services en mode natif dans une Machine virtuelle Azure. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Utiliser PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en Mode natif

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Cette rubrique décrit et vous guide dans le déploiement et la configuration d’un serveur de rapports SQL Server Reporting Services en mode natif dans une Machine virtuelle Azure. Les étapes décrites dans ce document utilisent une combinaison d’étapes manuelles pour créer la machine virtuelle et un script Windows PowerShell pour configurer Reporting Services sur l’ordinateur virtuel. Le script de configuration comprend l’ouverture d’un port de pare-feu pour HTTP ou HTTPs.

>[AZURE.NOTE] Si vous n’avez pas besoin de **HTTPS** sur le serveur de rapports, **ignorez l’étape 2**.
>
>Après avoir créé la machine virtuelle à l’étape 1, accédez à la section utiliser un script pour configurer le serveur de rapports et HTTP. Après avoir exécuté le script, le serveur de rapports est prêt à utiliser.

## <a name="prerequisites-and-assumptions"></a>Conditions préalables

- **Abonnement Azure**: vérifier le nombre de cœurs disponibles dans votre abonnement Azure. Si vous créez la taille de mémoire virtuelle recommandée de **A3**, vous devez cœurs disponibles **4** . Si vous utilisez une taille de mémoire virtuelle de **A2**, vous devez cœurs disponibles **2** .
    
    - Pour vérifier la limite de base de votre abonnement, dans le portail Azure classique, cliquez sur paramètres dans le volet gauche, puis cliquez sur l’utilisation du menu supérieur.
    
    - Pour augmenter le quota de base, contactez [Le Support Azure](https://azure.microsoft.com/support/options/). Pour plus d’informations de taille de la mémoire virtuelle, voir [Tailles Machine virtuelle pour Azure](virtual-machines-linux-sizes.md).

- **Écriture de scripts Windows PowerShell**: la rubrique suppose que vous avez une connaissance de base de Windows PowerShell. Pour plus d’informations sur l’utilisation de Windows PowerShell, voir les rubriques suivantes :

    - [Démarrage de Windows PowerShell sur Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Prise en main de Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Étape 1 : Configurer une Machine virtuelle Azure

1. Accédez au portail classique Azure.

1. Dans le volet gauche, cliquez sur **Machines virtuelles** .

    ![machines virtuelles Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Cliquez sur **Nouveau**.

    ![bouton Nouveau](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Cliquez sur **à partir de la galerie**.

    ![nouvelle machine virtuelle à partir de la galerie](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. **SQL Server 2014 RTM Standard – Windows Server 2012 R2** , puis cliquez sur la flèche pour continuer.

    ![suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Si vous devez les données de Reporting Services par l’effort fonctionnalité d’abonnement, sélectionnez **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Pour plus d’informations sur les éditions de SQL Server et les fonctionnalités prises en charge, voir [Fonctionnalités prises en charge par les éditions de SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Dans la page **configuration de machine virtuelle** , modifiez les champs suivants :
                                    
    - S’il existe plus d’une **DATE de publication de VERSION**, sélectionnez la version la plus récente.
    
    - **Nom de la Machine virtuelle**: nom de l’ordinateur sert également dans la page de configuration suivante le nom de Cloud Service DNS par défaut. Le nom DNS doit être unique au sein du service Azure. Envisagez de configurer la machine virtuelle avec un nom de l’ordinateur qui décrit l’utilisation de la machine virtuelle. Par exemple ssrsnativecloud.
    
    - **Niveau**: Standard
    
    - **Taille : A3** est la taille de mémoire virtuelle recommandée pour les charges de travail de SQL Server. Si une machine virtuelle est utilisée uniquement comme un serveur de rapports, une taille de mémoire virtuelle de A2 est suffisante, à moins que le serveur de rapports des expériences d’une grande charge de travail. Machine virtuelle tarifs d’informations, voir [Machines virtuelles tarifs](https://azure.microsoft.com/pricing/details/virtual-machines/).
    
    - **Nom du nouvel utilisateur**: le nom que vous fournissez est créé en tant qu’administrateur sur l’ordinateur virtuel.
    
    - **Nouveau mot de passe** et **Confirmer**. Ce mot de passe est utilisé pour le nouveau compte d’administrateur et il est recommandé de qu'utiliser un mot de passe.
    
    - Cliquez sur **suivant**. ![suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Dans la page suivante, modifiez les champs suivants :

    - **Service Cloud**: sélectionnez **créer un nouveau Service Cloud**.
    
    - **Nom DNS du Service cloud**: il s’agit du nom DNS public du Service Cloud associé à la machine virtuelle. Le nom par défaut est le nom que vous avez tapé le nom de la machine virtuelle. Si dans les étapes suivantes de la rubrique, vous créez un certificat SSL fiable et, le nom DNS est utilisé pour la valeur de la «**délivré à**» du certificat.
    
    - **Région/affinité groupe/virtuelle réseau**: choisir le plus proche de vos utilisateurs finaux la région.
    
    - **Compte de stockage**: utilisez un compte de stockage générée automatiquement.
    
    - **Définie disponibilité**: aucune.
    
    - **Points de terminaison** Conserver les points de terminaison de **Bureau à distance** et **PowerShell** , puis ajoutez point de terminaison HTTP ou HTTPS, selon votre environnement.

        - **HTTP**: les ports publiques et privées par défaut sont **80**. Notez que si vous utilisez un port privé différent de 80, modifiez **$HTTPport = 80** dans le script http.

        - **HTTPS**: les ports publiques et privées par défaut sont **443**. Meilleure pratique de sécurité consiste à modifier le port privé et de configurer votre pare-feu et le serveur de rapports pour utiliser le port privé. Pour plus d’informations sur les points de terminaison, voir [comment définir la Communication avec une Machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md). Notez que si vous utilisez un port autre que 443, modifiez le paramètre **$HTTPsport = 443** dans le script HTTPS.
    
    - Cliquez sur Suivant. ![suivant](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Sur la dernière page de l’Assistant, conservez la valeur par défaut **installer l’agent machine virtuelle** sélectionnée. Les étapes décrites dans cette rubrique n’utilisent pas l’agent machine virtuelle, mais si vous envisagez de conserver cet ordinateur virtuel, l’agent de machine virtuelle et extensions vous permettra il améliorer CM.  Pour plus d’informations sur l’agent machine virtuelle, voir [Agent machine virtuelle et Extensions-partie 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Un de l’annonce extensions sont installées par défaut en cours d’exécution est l’extension « BGINFO » qui s’affiche sur le bureau machine virtuelle, les informations système telles que l’adresse IP interne et espace disque.

1. Cliquez sur terminé. ![Bien](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. L' **état** de la machine virtuelle affiche sous la forme **début (Provisioning)** au cours du processus de mise en service et affiche ensuite comme **en cours d’exécution** lorsque la machine virtuelle est généré et vous êtes prêt à utiliser.

## <a name="step-2-create-a-server-certificate"></a>Étape 2 : Créer un certificat de serveur

>[AZURE.NOTE] Si vous n’avez pas besoin de HTTPS sur le serveur de rapports, vous pouvez **Ignorer l’étape 2** et accédez à la section **utiliser un script pour configurer le serveur de rapports et HTTP**. Utilisez le script HTTP pour configurer rapidement le serveur de rapports et le serveur de rapports sera prêt à utiliser.

Pour pouvoir utiliser HTTPS sur l’ordinateur virtuel, vous avez besoin d’un certificat SSL fiable. Selon votre scénario, vous pouvez utiliser une des deux méthodes suivantes :

- Un certificat SSL valide émis par une autorité de Certification (CA) et approuvés par Microsoft. Les certificats racine Autorité de certification sont requises pour être distribué via le programme de certificat racine Microsoft. Pour plus d’informations sur ce programme, voir [Windows et le programme de certificat racine pour 8 Windows Phone SSL (autorités de certification membre)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) et [Présentation du programme de certificat racine Microsoft](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Un certificat auto-signé. Certificats auto-signé ne sont pas recommandés pour les environnements de production.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Pour utiliser un certificat créé par une autorité de certification approuvée (CA)

1. **Demander un certificat de serveur pour le site Web à partir d’une autorité de certification**. 

    Vous pouvez utiliser l’Assistant certificat de serveur Web pour générer un fichier de demande de certificat (Certreq.txt) que vous envoyez à une autorité de certification ou pour générer une demande d’une autorité de certification en ligne. Par exemple, Services de certificats Microsoft dans Windows Server 2012. En fonction du niveau d’assurance d’identification offerte par votre certificat de serveur, il est plusieurs jours à plusieurs mois pour l’autorité de certification approuver la demande et vous envoyer un fichier de certificat. 

    Pour plus d’informations sur la demande d’un certificat de serveur, voir les rubriques suivantes : 
    
    - Utilisez [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Outils de sécurité pour administrer Windows Server 2012.

    [Outils de sécurité pour administrer Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] Le champ **délivré à** de ce certificat SSL doit être identique au **Nom du Cloud Service DNS** utilisé pour la nouvelle machine virtuelle.

1. **Installer le certificat de serveur sur le serveur Web**. Dans ce cas, le serveur Web est la machine virtuelle qui héberge le serveur de rapports et le site Web est créé dans les étapes suivantes lorsque vous configurez Reporting Services. Pour plus d’informations sur l’installation du certificat de serveur sur le serveur Web à l’aide du composant logiciel enfichable Certificats, consultez [installer un certificat de serveur](https://technet.microsoft.com/library/cc740068).
    
    Si vous souhaitez utiliser le script inclus dans cette rubrique, pour configurer le serveur de rapports, la valeur des certificats **empreinte numérique** est requis en tant que paramètre du script. Consultez la section suivante pour plus d’informations sur l’obtention de l’empreinte numérique du certificat.

1. Attribuer le certificat de serveur à serveur de rapports. L’affectation a été effectuée dans la section suivante lorsque vous configurez le serveur de rapports.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Utiliser le certificat d’auto-signature Machines virtuelles

Un certificat auto-signé créé sur l’ordinateur virtuel lors de la machine virtuelle a été mis en service. Le certificat a le même nom que le nom VM DNS. Afin d’éviter les erreurs de certificat, il est nécessaire que le certificat est fiable sur l’ordinateur virtuel lui-même, ainsi que par tous les utilisateurs du site.

1. Pour faire confiance à l’autorité de certification racine du certificat sur l’ordinateur Local virtuel, ajoutez le certificat aux **Autorités de Certification racine de confiance**. Voici un résumé des étapes requises. Pour plus d’informations sur la façon d’approuver l’autorité de certification, consultez [installer un certificat de serveur](https://technet.microsoft.com/library/cc740068).

    1. À partir du portail classique Azure, sélectionnez la machine virtuelle, puis cliquez sur se connecter. Selon la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.
    
        ![se connecter à azure machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom d’utilisateur machine virtuelle, le nom d’utilisateur et le mot de passe que vous avez configuré lors de la création de la machine virtuelle. 
    
        Par exemple, dans l’image suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur est **testuser**.
        
        ![nom de connexion prédéfini comprenant machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Exécutez mmc.exe. Pour plus d’informations, voir [Comment : afficher les certificats avec le composant logiciel enfichable](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. Dans le menu **fichier** de console, ajoutez le composant logiciel enfichable **certificats** , sélectionnez **Compte d’ordinateur** lorsque vous y êtes invité, puis cliquez sur **suivant**.
    
    1. Sélectionnez **Ordinateur Local** pour gérer, puis cliquez sur **Terminer**.
    
    1. Cliquez sur **Ok** , puis développez le **certificats - personnel** nœuds, puis cliquez sur **certificats**. Le certificat étant nommé d’après le nom de la machine virtuelle DNS et se termine par **cloudapp.net**. Le bouton droit sur le certificat et cliquez sur **Copier**.
    
    1. Développez le nœud **Autorités de Certification racine de confiance** , puis cliquez sur **certificats** , puis sur **Coller**.
    
    1. Pour valider, double-cliquez sur le nom du certificat sous **Autorités de Certification racine de confiance** et vérifiez qu’il n’y a aucune erreur et que vous consultez votre certificat. Si vous souhaitez utiliser le script HTTPS inclus dans cette rubrique, pour configurer le serveur de rapports, la valeur des certificats **empreinte numérique** est requis en tant que paramètre du script. **Pour obtenir la valeur de l’empreinte numérique**, procédez ainsi. Vous trouverez également un échantillon de PowerShell pour récupérer l’empreinte numérique dans la section [utiliser un script pour configurer le serveur de rapports et HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Double-cliquez sur le nom du certificat, par exemple ssrsnativecloud.cloudapp.net.
        
        1. Cliquez sur l’onglet **Détails** .
        
        1. Cliquez sur **Aperçu**. La valeur de l’empreinte numérique s’affiche dans le champ Détails, par exemple a6 08 3c df f9 0 b f7 e3 7c 25 ed a4 ed 7e CA 91 9c 2c toujours 2f.
        
        1. Copier l’empreinte numérique et enregistrez la valeur pour rappel ultérieur ou modifier le script maintenant.
        
        1. (*) Avant d’exécuter le script, supprimez les espaces entre les paires de valeurs. Par exemple l’empreinte numérique avant de noter est désormais a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Attribuer le certificat de serveur à serveur de rapports. L’affectation a été effectuée dans la section suivante lorsque vous configurez le serveur de rapports.

Si vous utilisez un certificat SSL auto-signé, le nom du certificat correspond déjà le nom d’hôte de la machine virtuelle. Par conséquent, le DNS de l’ordinateur est déjà inscrit globalement et sont accessibles à partir de n’importe quel client.

## <a name="step-3-configure-the-report-server"></a>Étape 3 : Configurer le serveur de rapports

Cette section vous guide dans la configuration de la machine virtuelle comme un serveur de rapports en mode natif Reporting Services. Vous pouvez utiliser une des méthodes suivantes pour configurer le serveur de rapports :

- Utilisez le script pour configurer le serveur de rapports

- Utilisez le Gestionnaire de Configuration pour configurer le serveur de rapports.

Pour obtenir la procédure détaillée, voir la section [se connecter à la Machine virtuelle et démarrer le Gestionnaire de Configuration de Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Note d’authentification :** L’authentification Windows est la méthode d’authentification recommandée ni l’authentification de Reporting Services par défaut. Seuls les utilisateurs qui sont configurés sur la machine virtuelle peuvent accéder Reporting Services et attribuées aux rôles Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Utiliser un script pour configurer le serveur de rapports et HTTP

Pour utiliser le script Windows PowerShell pour configurer le serveur de rapports, procédez comme suit. La configuration inclut HTTP, HTTPS pas :

1. À partir du portail classique Azure, sélectionnez la machine virtuelle, puis cliquez sur se connecter. Selon la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.

    ![se connecter à azure machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom d’utilisateur machine virtuelle, le nom d’utilisateur et le mot de passe que vous avez configuré lors de la création de la machine virtuelle. 

    Par exemple, dans l’image suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur est **testuser**.
    
    ![nom de connexion prédéfini comprenant machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Sur la machine virtuelle, ouvrez **Windows PowerShell ISE** avec des privilèges d’administrateur. PowerShell ISE est installé par défaut sur Windows server 2012. Il est recommandé de qu'utiliser l’ISE au lieu d’une fenêtre de Windows PowerShell standard afin que vous pouvez coller le script dans l’ISE, modifiez le script et puis exécuter le script.

1. Dans Windows PowerShell ISE, cliquez sur le menu **affichage** , puis sur **Afficher le volet de Script**.

1. Copiez le script suivant et collez le script dans le volet de script Windows PowerShell ISE.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Si vous avez créé la machine virtuelle avec un port HTTP différent de 80, modifiez le paramètre $HTTPport = 80.

1. Le script est déjà configuré pour Reporting Services. Si vous souhaitez exécuter le script pour Reporting Services, modifiez la partie de la version du chemin d’accès à l’espace de noms « 11 », dans l’instruction Get-WmiObject.

1. Exécuter le script.

**Validation**: pour vérifier que les fonctionnalités du serveur de rapports de base fonctionne, consultez la section [vérifier la configuration](#verify-the-configuration) plus loin dans cette rubrique.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Utiliser un script pour configurer le serveur de rapports et HTTPS

Pour utiliser Windows PowerShell pour configurer le serveur de rapports, procédez comme suit. La configuration inclut pas HTTP, HTTPS.

1. À partir du portail classique Azure, sélectionnez la machine virtuelle, puis cliquez sur se connecter. Selon la configuration de votre navigateur, vous pouvez être invité à enregistrer un fichier .rdp pour la connexion à la machine virtuelle.

    ![se connecter à azure machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilisez le nom d’utilisateur machine virtuelle, le nom d’utilisateur et le mot de passe que vous avez configuré lors de la création de la machine virtuelle. 

    Par exemple, dans l’image suivante, le nom de la machine virtuelle est **ssrsnativecloud** et le nom d’utilisateur est **testuser**.

    ![nom de connexion prédéfini comprenant machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Sur la machine virtuelle, ouvrez **Windows PowerShell ISE** avec des privilèges d’administrateur. PowerShell ISE est installé par défaut sur Windows server 2012. Il est recommandé de qu'utiliser l’ISE au lieu d’une fenêtre de Windows PowerShell standard afin que vous pouvez coller le script dans l’ISE, modifiez le script et puis exécuter le script.

1. Pour activer l’exécution de scripts, exécutez la commande Windows PowerShell suivante :

        Set-ExecutionPolicy RemoteSigned

    Vous pouvez ensuite exécuter la commande suivante pour vérifier la stratégie :

        Get-ExecutionPolicy

1. Dans **Windows PowerShell ISE**, cliquez sur le menu **affichage** , puis sur **Afficher le volet de Script**.

1. Copiez le script suivant et collez-le dans la fenêtre de script Windows PowerShell ISE.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Modifiez le paramètre **$certificatehash** dans le script :

    - Il s’agit d’un paramètre **obligatoire** . Si vous n’avez pas enregistré la valeur du certificat des étapes précédentes, utilisez une des méthodes suivantes pour copier la valeur de hachage du certificat de l’empreinte numérique certificats. :

        Sur la machine virtuelle, ouvrez Windows PowerShell ISE et exécutez la commande suivante :

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        Le résultat est semblable à ce qui suit. Si le script renvoie une ligne vide, la machine virtuelle ne pas disposer d’un certificat configuré par exemple, consultez la section [pour utiliser le certificat d’auto-signature Machines virtuelles](#to-use-the-virtual-machines-self-signed-certificate).
    
    OR
    
    - Sur l’ordinateur virtuel exécutez mmc.exe, puis ajoutez le composant logiciel enfichable **certificats** .
    
    - Sous le nœud **Autorités de certification racine de confiance** , double-cliquez sur le nom de votre certificat. Si vous utilisez le certificat auto-signé de la machine virtuelle, le certificat étant nommé d’après le nom de la machine virtuelle DNS et se termine par **cloudapp.net**.
    
    - Cliquez sur l’onglet **Détails** .
    
    - Cliquez sur **Aperçu**. La valeur de l’empreinte numérique s’affiche dans le champ Détails, par exemple af 11 60 b6 4 b 28 8 d 89 0 a 82 12 ff 6 b a9 c3 66 4 f 31 90 48
    
    - **Avant d’exécuter le script**, supprimer les espaces entre les paires de valeurs. Par exemple af1160b64b288d890a8212ff6ba9c3664f319048

1. Modifier le paramètre **$httpsport** : 

    - Si vous avez utilisé le port 443 du point de terminaison HTTPS, il est inutile mettre à jour ce paramètre dans le script. Sinon, utilisez la valeur de port que vous avez sélectionné lorsque vous avez configuré le point de terminaison privé HTTPS sur l’ordinateur virtuel.

1. Modifier le paramètre **$DNSName** : 

    - Le script est configuré pour un certificat avec des caractères génériques $DNSName = « + ». Si vous le faites sans que vous souhaitez configurer pour une liaison de certificat génériques, commentez $DNSName = » + « et activer la ligne suivante, la référence $DNSNAme complète, ## $DNSName="$server.cloudapp.net ».

        Modifier la valeur $DNSName si vous ne souhaitez pas utiliser le nom DNS de la machine virtuelle pour Reporting Services. Si vous utilisez le paramètre, le certificat doit également utiliser ce nom et que vous enregistrez le nom globalement sur un serveur DNS.

1. Le script est déjà configuré pour Reporting Services. Si vous souhaitez exécuter le script pour Reporting Services, modifiez la partie de la version du chemin d’accès à l’espace de noms « 11 », dans l’instruction Get-WmiObject.

1. Exécuter le script.

**Validation**: pour vérifier que les fonctionnalités du serveur de rapports de base fonctionne, consultez la section [vérifier la configuration](#verify-the-connection) plus loin dans cette rubrique. Pour vérifier le certificat liaison ouvrez une invite de commande avec des privilèges d’administrateur, puis exécutez la commande suivante :

    netsh http show sslcert

Le résultat doit inclure les éléments suivants :

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utilisez le Gestionnaire de Configuration pour configurer le serveur de rapports

Si vous ne souhaitez pas exécuter le script PowerShell pour configurer le serveur de rapports, suivez les étapes décrites dans cette section pour utiliser le Gestionnaire de configuration de mode natif Reporting Services pour configurer le serveur de rapports.

1. À partir du portail classique Azure, sélectionnez la machine virtuelle, puis cliquez sur se connecter. Utilisez le nom d’utilisateur et mot de passe que vous avez configuré lors de la création de la machine virtuelle.

    ![se connecter à azure machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Exécuter Windows update et installer les mises à jour de la machine virtuelle. Si un redémarrage de la machine virtuelle est requis, redémarrez la machine virtuelle et vous reconnecter à la machine virtuelle à partir du portail classique Azure.

1. Dans le menu Démarrer de la machine virtuelle, tapez **Reporting Services** et ouvrez le **Gestionnaire de Configuration de Reporting Services**.

1. Laissez les valeurs par défaut pour le **Nom du serveur** et **Instance de serveur de rapports**. Cliquez sur **se connecter**.

1. Dans le volet gauche, cliquez sur **URL du Service Web**.

1. Par défaut, RS est configuré pour le port HTTP 80 avec IP « Toutes les affectations ». Pour ajouter HTTPS :

    1. Dans le **Certificat SSL**: sélectionnez le certificat que vous souhaitez utiliser, par exemple [nom de la machine virtuelle]. cloudapp.net. Si aucun certificat n’est répertoriés, reportez-vous à la section **étape 2 : créer un certificat de serveur** pour plus d’informations sur l’installation et approuver le certificat sur l’ordinateur virtuel.
    
    1. Sous **Port SSL**: choisissez 443. Si vous avez configuré le point de terminaison HTTPS privé dans la machine virtuelle avec un autre port privé, utilisez cette valeur ici.
    
    1. Cliquez sur **Appliquer** et attendez que l’opération se termine.

1. Dans le volet gauche, cliquez sur **base de données**.

    1. Cliquez sur **Modifier Databas**e.
    
    1. Cliquez sur **créer une nouvelle base de données de serveur de rapport** , puis sur **suivant**.
    
    1. Conservez la valeur par défaut **Le nom de serveur**: nom de la machine virtuelle et laissez la valeur par défaut **Type d’authentification** en tant qu' **Utilisateur actuel** – **Sécurité intégrée**. Cliquez sur **suivant**.
    
    1. Laissez le **Nom de la base de données** en tant que **ReportServer** par défaut et cliquez sur **suivant**.
    
    1. Laissez le **Type d’authentification** comme **Informations d’identification de Service** par défaut et cliquez sur **suivant**.
    
    1. Dans la page de **Résumé** , cliquez sur **suivant** .
    
    1. Une fois la configuration terminée, cliquez sur **Terminer**.

1. Dans le volet gauche, cliquez sur **Gestionnaire de rapports**. Conservez la valeur par défaut **Répertoire virtuel** sous forme de **rapports** et cliquez sur **Appliquer**.

1. Cliquez sur **Quitter** pour fermer le Gestionnaire de Configuration de Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Étape 4 : Ouvrir le Port pare-feu Windows

>[AZURE.NOTE] Si vous avez utilisé un des scripts pour configurer le serveur de rapports, vous pouvez ignorer cette section. Le script inclus une étape pour ouvrir le port du pare-feu. La valeur par défaut a été ports 80 pour HTTP et 443 pour HTTPS.

Pour vous connecter à distance au Gestionnaire de rapports ou le serveur de rapports sur l’ordinateur virtuel, un point de terminaison TCP est requis sur l’ordinateur virtuel. Il est nécessaire pour ouvrir le même port dans le pare-feu de l’ordinateur. Le point de terminaison créée lors de la machine virtuelle a été mis en service.

Cette section fournit des informations de base ouvrir le port du pare-feu. Pour plus d’informations, voir [configurer un pare-feu pour accéder au serveur de rapport](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Si vous avez utilisé le script pour configurer le serveur de rapports, vous pouvez ignorer cette section. Le script inclus une étape pour ouvrir le port du pare-feu.

Si vous avez configuré un port privé pour HTTPS autre que 443, modifier le script suivant en conséquence. Pour ouvrir le port **443** sur le pare-feu Windows, procédez comme suit :

1. Ouvrez une fenêtre Windows PowerShell avec des privilèges d’administrateur.

1. Si vous avez utilisé un port autre que 443 lorsque vous avez configuré le point de terminaison HTTPS sur l’ordinateur virtuel, mettez à jour le port dans la commande suivante, puis exécutez la commande :

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Lorsque la commande se termine, **Ok** s’affiche dans l’invite de commandes.

Pour vérifier que le port est ouvert, ouvrez une fenêtre de Windows PowerShell et exécutez la commande suivante :

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Vérifier la configuration

Pour vérifier que les fonctionnalités du serveur de rapports de base fonctionne, ouvrez votre navigateur avec des privilèges d’administrateur et naviguez jusqu'à suivant ad rapport Gestionnaire de report server URL :

- Sur la machine virtuelle, accédez à l’URL du serveur de rapports :

        http://localhost/reportserver

- Sur la machine virtuelle, accédez à l’URL du Gestionnaire de rapports :

        http://localhost/Reports

- Depuis votre ordinateur local, recherchez le Gestionnaire de rapport **à distance** sur l’ordinateur virtuel. Mettre à jour le nom DNS dans l’exemple suivant, le cas échéant. Lorsque vous y êtes invité pour un mot de passe, utilisez les informations d’identification d’administrateur que vous avez créé lors de la machine virtuelle a été mis en service. Le nom d’utilisateur se trouve dans la [domaine]\[nom d’utilisateur] format, dans laquelle le domaine est le nom d’ordinateur machine virtuelle, par exemple ssrsnativecloud\testuser. Si vous n’utilisez pas HTTP**S**, supprimez le **s** dans l’URL. Consultez la section suivante pour plus d’informations sur la création d’autres utilisateurs sur ordinateur virtuel.

        https://ssrsnativecloud.cloudapp.net/Reports

- Depuis votre ordinateur local, naviguez jusqu'à l’URL du serveur distant rapport. Mettre à jour le nom DNS dans l’exemple suivant, le cas échéant. Si vous n’utilisez pas HTTPS, supprimez la lettre s dans l’URL.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Créer des utilisateurs et attribuer des rôles

Après avoir configuré et vérifiez le serveur de rapports, une tâche administrative courante consiste à créer un ou plusieurs utilisateurs et attribuer des rôles Reporting Services. Pour plus d’informations, voir les rubriques suivantes :

- [Créer un compte d’utilisateur local](https://technet.microsoft.com/library/cc770642.aspx)

- [Utilisateur accorder l’accès à un serveur de rapports (Gestionnaire de rapports)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Créer et gérer des attributions de rôle](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Pour créer et publier des rapports sur la Machine virtuelle Azure

Le tableau suivant résume quelques-unes des options disponibles pour publier des rapports existants à partir d’un ordinateur local pour le serveur de rapports hébergé sur la Machine virtuelle de Microsoft Azure :

- **Script RS.exe**: utilisation RS.exe script pour copier des éléments de rapport à partir d’et serveur de rapport à votre Machine virtuelle de Microsoft Azure. Pour plus d’informations, consultez la section « Mode natif en mode natif – Machine virtuelle Microsoft Azure » dans [l’exemple Reporting Services rs.exe Script pour migrer le contenu entre les serveurs de rapports](https://msdn.microsoft.com/library/dn531017.aspx).

- **Le Générateur de rapports**: la machine virtuelle inclut le clic-une fois la version de Microsoft SQL Server le Générateur de rapports. Pour démarrer rapport Générateur la première fois sur l’ordinateur virtuel :

    1. Démarrez votre navigateur avec des privilèges d’administrateur.
    
    1. Recherchez le Gestionnaire de rapports sur l’ordinateur virtuel, puis cliquez sur **Le Générateur de rapports** dans le ruban.

    Pour plus d’informations, voir [installation, désinstallation et prenant en charge le Générateur de rapports](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools : machine virtuelle**: Si vous avez créé la machine virtuelle SQL Server 2012, puis SQL Server Data Tools est installé sur l’ordinateur virtuel et pouvant être utilisées pour créer des **Projets de serveur de rapports** et des rapports sur l’ordinateur virtuel. SQL Server Data Tools peuvent publier les rapports sur le serveur de rapports sur l’ordinateur virtuel.
    
    Si vous avez créé la machine virtuelle SQL Server 2014, vous pouvez installer SQL Server données Outils - BI pour visual Studio. Pour plus d’informations, voir les rubriques suivantes :

    - [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

    - [Outils de données SQL Server et SQL Server Business Intelligence (BI SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools : distant**: sur votre ordinateur local, créez un projet de Reporting Services dans SQL Server Data Tools contenant des rapports Reporting Services. Configurer le projet pour vous connecter à l’URL du service web.

    ![Propriétés du projet ssdt pour projet SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Utiliser un script**: script permet de copier le contenu du serveur rapport. Pour plus d’informations, voir [exemple Reporting Services rs.exe Script pour migrer le contenu entre les serveurs de rapports](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Réduire les coûts si vous n’utilisez pas la machine virtuelle

>[AZURE.NOTE] Pour réduire les frais pour vos Machines virtuelles Azure lorsqu’elles pas en cours d’utilisation, arrêtez la machine virtuelle à partir du portail classique Azure. Si vous utilisez les options d’alimentation Windows à l’intérieur d’une machine virtuelle pour arrêter la machine virtuelle, vous êtes chargé toujours la même quantité de la machine virtuelle. Pour réduire les frais, vous devez arrêter la machine virtuelle dans le portail classique Azure. Si vous n’avez plus besoin de la machine virtuelle, n’oubliez pas de supprimer la machine virtuelle et les fichiers .vhd associé pour éviter des frais de stockage. Pour plus d’informations, voir la section FAQ située [Machines virtuelles tarifs détails](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Plus d’informations

### <a name="resources"></a>Ressources

- Pour un contenu similaire lié à un seul déploiement de SQL Server Business Intelligence et SharePoint 2013, voir [Utiliser Windows PowerShell pour créer une machine virtuelle Azure avec SQL Server BI et SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Pour un contenu similaire lié à un déploiement sur plusieurs serveurs de SQL Server Business Intelligence et SharePoint 2013, voir [Déployer SQL Server à la décision dans le Machines virtuelles Azure](https://msdn.microsoft.com/library/dn321998.aspx).

- Pour plus d’informations pour les déploiements de Business Intelligence dans le Machines virtuelles Azure SQL Server, voir [SQL Server Business Intelligence dans le Machines virtuelles Azure](virtual-machines-windows-classic-ps-sql-bi.md).

- Pour plus d’informations sur les coûts d’Azure calculer des frais, voir l’onglet Machines virtuelles de [Calculatrice tarification Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Contenu de la Communauté

- Pour obtenir des instructions étape par étape la création d’un serveur de rapports en mode natif des Services de création de rapports sans utiliser de script, consultez [Hébergement SQL Reporting Services sur le Machine virtuelle Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Liens vers d’autres ressources pour SQL Server dans machines virtuelles Azure

[SQL Server sur vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md)
