<properties
   pageTitle="Chiffrement de disque Azure pour Windows et Linux IaaS machines virtuelles | Microsoft Azure"
   description="Le document fournit une vue d’ensemble de Microsoft Azure disque chiffrement pour Windows et machines virtuelles IaaS Linux."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="krkhan"/>


#<a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Chiffrement de disque Azure pour Windows et Linux IaaS machines virtuelles

Microsoft Azure s’engage à garantir la confidentialité des données, souveraineté de données et active vous permettent de contrôler vos Azure hébergé une plage de données avancées des technologies d’assistance pour chiffrer, contrôler et gérer les clés de chiffrement, access d’audit & du contrôle de données. Cette souplesse clients Azure pour choisir la solution qui correspond le mieux à leurs besoins professionnels. Dans ce document, nous allons présenter vous à une nouvelle solution technologie « Azure chiffrement de disque pour Windows et de Linux IaaS machine virtuelle » pour protéger et protéger vos données afin de répondre à votre sécurité de l’organisation et les engagements de conformité. Le document fournit des indications détaillées sur la façon d’utiliser les fonctionnalités de chiffrement de disque Azure, y compris les scénarios pris en charge et que l’utilisateur rencontre.

**Remarque**: certaines recommandations contenues dans cet article, peuvent entraîner une augmentation des données, de réseau ou utilisation des ressources cluster résultant des coûts de licence ou abonnement supplémentaires.

## <a name="overview"></a>Vue d’ensemble

Chiffrement de disque Azure est une nouvelle fonctionnalité qui vous permet de chiffrer vos disques machine virtuelle Windows et Linux IaaS. Chiffrement de disque Azure s’appuie sur la fonctionnalité [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard du secteur de Windows et la fonctionnalité [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume pour le système d’exploitation et les disques de données. La solution est intégrée à l’archivage sécurisé [Azure clé](https://azure.microsoft.com/documentation/services/key-vault/) pour vous aider à contrôler et de gérer les clés de chiffrement de disque et secrets dans votre abonnement de l’archivage sécurisé clés, tout en garantissant que toutes les données dans les disques machine virtuelle sont chiffrées au reste dans votre espace de stockage Azure.

Chiffrement de disque Azure pour Windows et Linux IaaS machines virtuelles fait désormais partie **Officielle** dans toutes les régions publiques Azure pour les ordinateurs virtuels Standard et machines virtuelles avec stockage premium.

### <a name="encryption-scenarios"></a>Scénarios de chiffrement

La solution de chiffrement de disque Azure prend en charge les scénarios client suivants :

- Activer le chiffrement de nouvelles IaaS machines virtuelles créé à partir de disque dur virtuel préalable chiffré et clés de chiffrement
- Activer le chiffrement de nouvelles IaaS machines virtuelles créé à partir des images Galerie Azure
- Activer le chiffrement de machines virtuelles IaaS existantes en cours d’exécution dans Azure
- Désactiver le chiffrement de machines virtuelles IaaS Windows
- Désactiver le chiffrement de lecteurs de données pour les machines virtuelles IaaS Linux

La solution prend en charge les éléments suivants pour les machines virtuelles IaaS lorsque activée dans Microsoft Azure :

- Intégration avec l’archivage sécurisé clé Azure
- Couche standard machines virtuelles - [A, D, DS, G, GS etc. série IaaS machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
- Activer le chiffrement sous Windows et Linux IaaS machines virtuelles
- Désactiver le chiffrement de lecteurs du système d’exploitation et de données pour les machines virtuelles IaaS Windows
- Désactiver le chiffrement de lecteurs de données pour les machines virtuelles IaaS Linux
- Activer le chiffrement de machines virtuelles IaaS exécutant le système d’exploitation Windows Client
- Activer le chiffrement sur les volumes avec les chemins de montage
- Activer le chiffrement de machines virtuelles Linux configuré avec RAID logiciel système 
- Activer le chiffrement de machines virtuelles Windows configuré avec des espaces de stockage
- Toutes les régions publiques Azure sont prises en charge

La solution ne prend pas en charge les scénarios suivants, fonctionnalités et technologie dans la version :

- Machines virtuelles IaaS couche de base
- Désactiver le chiffrement de lecteur du système d’exploitation pour les machines virtuelles IaaS Linux
- Machines virtuelles IaaS créés à l’aide de la méthode de création classique machine virtuelle
- Intégration à votre Service de gestion de clés en local
- Windows Server 2016 Technical Preview n’est pas pris en charge dans cette version
- Azure fichiers (partage de fichiers Azure), du système de fichiers réseau (NFS), les volumes dynamiques, machines virtuelles Windows configuré avec les systèmes RAID logiciel


### <a name="encryption-features"></a>Fonctionnalités de chiffrement

Lorsque vous activez et déployez le chiffrement des disques Azure pour les machines virtuelles IaaS Azure, les fonctionnalités suivantes sont activées, selon la configuration fournie :

- Chiffrement du volume du système d’exploitation pour protéger le volume de démarrage au reste dans le stockage de client
- Chiffrement des données en volume/s pour protéger les volumes de données au reste dans le stockage de client
- Désactiver le chiffrement de lecteurs du système d’exploitation et de données pour les machines virtuelles IaaS Windows
- Désactiver le chiffrement de lecteurs de données pour les machines virtuelles IaaS Linux
- Protection de clés de chiffrement et secrets dans le client abonnement de l’archivage sécurisé clés Azure
- Rapports d’état de chiffrement de IaaS VM chiffré
- Suppression des paramètres de configuration de chiffrement de disque à partir de la machine virtuelle IaaS

Le chiffrement de disque Azure pour IaaS VMS pour Windows et Linux solution inclut l’extension de chiffrement de disque pour Windows, extension de chiffrement de disque pour Linux, les applets de commande PowerShell disque chiffrement, les applets de commande de disque chiffrement infrastructure du langage commun et les modèles de disque chiffrement Azure le Gestionnaire de ressources. La solution de chiffrement Azure disque est pris en charge sur machines virtuelles IaaS fonctionnant sous Windows ou système d’exploitation Linux. Pour plus d’informations sur les systèmes d’exploitation pris en charge, voir la section conditions préalables ci-dessous.

**Remarque**: il est sans frais supplémentaires pour le chiffrement des disques machine virtuelle avec chiffrement de disque Azure.

### <a name="value-proposition"></a>Proposition de valeur

La solution de gestion de chiffrement de disque Azure Active les besoins professionnels suivants dans le cloud :

-   IaaS VM sont-ils sécurisés inactives à l’aide de la technologie de chiffrement standard aux exigences de conformité et de sécurité de l’organisation adresse.
-   Démarrage de IaaS VM sous client contrôlée clés et les stratégies, et ils peuvent auditer leur utilisation dans l’archivage sécurisé clé.


### <a name="encryption-workflow"></a>Chiffrement de flux de travail

Les étapes de haut niveau nécessaires pour activer le chiffrement de disque pour Windows et de Linux VM sont :

1. Client choisit un scénario de chiffrement dans les scénarios de chiffrement ci-dessus.
2. Client opte pour l’activation de chiffrement des disques via le modèle de gestionnaire de ressources de chiffrement de disque Azure ou les applets de commande PS ou les commande infrastructure du langage commun et spécifie la configuration de chiffrement

    - Pour le scénario de disque dur virtuel client chiffré, les chargements client le disque dur virtuel chiffré à leur stockage compte et chiffrement matériel de clé à leur clé vault et fournissent la configuration de chiffrement pour activer le chiffrement dans un nouveau VM IaaS
    - Pour la nouvelle machine virtuelle créé à partir de la galerie Azure et machines virtuelles existant en cours d’exécution dans Azure, le client fournir la configuration de chiffrement pour activer le chiffrement de la IaaS VM

3. Client accorde l’accès à la plateforme Azure pour lire le matériel de clé de chiffrement (BitLocker chiffrement clés pour les systèmes Windows et mot de passe Linux) à partir de leur l’archivage sécurisé clé pour activer le chiffrement de la IaaS VM
4. Le client fournir une identité de l’application Azure AD pour écrire la clé de chiffrement dans leur l’archivage sécurisé clé pour activer le chiffrement de la VM IaaS pour scénarios mentionnées dans #2 ci-dessus.
5.  Azure met à jour le modèle de service machine virtuelle avec chiffrement et la configuration de l’archivage sécurisé clés et dispositions chiffrées machine virtuelle pour le client

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Flux de travail déchiffrement

Les étapes de haut niveau nécessaires pour désactiver le chiffrement de disque pour de IaaS VM sont :

1. Le client choisit désactiver le chiffrement (déchiffrement) sur un VM IaaS en cours d’exécution dans Azure via le modèle de gestionnaire de ressources de chiffrement de disque Azure ou les applets de commande PS et spécifie la configuration de déchiffrement.
2. L’étape de chiffrement désactiver désactive le chiffrement du volume du système d’exploitation ou des données ou les deux sur l’ordinateur en cours d’exécution Windows IaaS virtuel. Toutefois OS désactivation disque chiffrement pour Linux n'est pas pris en charge comme indiqué dans la documentation ci-dessus. L’étape désactiver est autorisé uniquement pour les lecteurs de données sur machines virtuelles Linux. 
4. Azure met à jour le modèle de service machine virtuelle et le IaaS VM est marquée déchiffrée. Le contenu de la machine virtuelle est chiffré pas plus inactives.
5. L’opération de chiffrement désactivation ne supprime pas l’archivage sécurisé clés client et le matériel de clé de chiffrement,-clés de chiffrement BitLocker pour Windows ou Linux mot de passe.

## <a name="prerequisites"></a>Conditions préalables

Voici les conditions préalables pour activer le chiffrement de disque Azure sur Azure IaaS machines virtuelles pour les scénarios pris en charge entourées dans la section vue d’ensemble

- Utilisateur doit avoir un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions pris en charge
- Chiffrement de disque Azure est pris en charge sur le Windows serveur suivant SKU – Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Windows Server 2016 Technical Preview n’est pas pris en charge dans cette version.
- Le chiffrement des disques Azure est pris en charge sur le Windows client suivant SKU – Windows 8 clients et Windows 10.

**Remarque**: pour Windows Server 2008 R2, .net framework 4.5 doit être installé avant d’activer le chiffrement dans Azure. Vous pouvez l’installer à partir de Windows update en installant la mise à jour facultative « Microsoft .NET Framework 4.5.2 pour Windows Server 2008 R2 x 64 systèmes ([KB2901983](https://support.microsoft.com/kb/2901983)) »

- Chiffrement de disque Azure est pris en charge sur le Linux serveur suivant références SKU - Ubuntu, CentOS, SUSE et SUSE Linux Enterprise Server (SLES) et rouge chapeau Enterprise Linux.

**Remarque**: le chiffrement des disques Linux du système d’exploitation est pris en charge sur les CentOS de répartitions - 7.2 RHEL, Linux 7.2, Ubuntu 16.04 suivants

- Toutes les ressources (Ex : l’archivage sécurisé clé, le stockage du compte, machine virtuelle, etc.) doit appartenir à la même région Azure et l’abonnement.

**Remarque**: le chiffrement des disques Azure nécessite que l’archivage sécurisé clé et les ordinateurs virtuels résident dans la même région Azure. Les configurer dans la zone distincte va entraîner l’échec de l’activation de la fonctionnalité de chiffrement disque Azure.

- Pour installer et configurer l’archivage sécurisé de clé Azure pour l’utilisation du chiffrement disque Azure, consultez la section **paramètre et configuration de l’archivage sécurisé de clé Azure pour l’utilisation du chiffrement disque Azure** dans la section *conditions préalables* de cet article.
- Pour installer et configurer application Azure AD dans Azure Active directory pour l’utilisation du chiffrement disque Azure, consultez la section **configuration de l’Application AD Azure dans Azure Active Directory** dans la section *conditions préalables* de cet article.
- Pour installer et configurer la stratégie d’accès de l’archivage sécurisé clé pour l’Application AD Azure, reportez-vous à la section **stratégie d’accès de l’archivage sécurisé de clé de paramètre pour l’Application AD Azure** dans la section *conditions préalables* de cet article.
- Pour préparer un disque dur virtuel Windows préalable chiffré, consultez la section **préparer un disque dur virtuel Windows préalable chiffrés** à l’appendice de cet article.
- Pour préparer un VHD Linux préalablement chiffré, consultez la section **préparer un disque dur virtuel Linux préalable chiffrés** à l’appendice de cet article.
- Plateforme Azure doit avoir accès aux clés de chiffrement ou secrets dans le client l’archivage sécurisé de clé Azure afin de les rendre disponibles pour la machine virtuelle pour démarrer et déchiffrer le volume machine virtuelle du système d’exploitation. Pour accorder des autorisations pour la plateforme Azure pour accéder à la clé de l’archivage sécurisé customer, **enabledForDiskEncryption** propriété doit être définie sur l’archivage sécurisé clé pour cette exigence. Reportez-vous à la section **paramètre et configuration de l’archivage sécurisé de clé Azure pour l’utilisation du chiffrement disque Azure** dans l’appendice de cet article pour plus d’informations.
- L’archivage sécurisé clé secret et le chiffrement de clé clé (KEK) URL doivent être créée. Azure applique cette restriction de contrôle de version. Voir exemples pour secret valide et URL KEK ci-dessous :
    - Exemple d’URL secrète valide :   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - Exemple de valide KRK KEK :   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Chiffrement de disque Azure ne reconnaît pas les numéros de port spécifiées dans le cadre de l’archivage sécurisé clé secret et URL KEK. Voir les exemples ci-dessous de l’URL de l’archivage sécurisé clé pris en charge :
    - URL de l’archivage sécurisé clé non acceptées   *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - URL de l’archivage sécurisé clés acceptée :   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Pour activer le chiffrement de disque Azure fonctionnalité, les ordinateurs virtuels IaaS doit répondre aux exigences de configuration de point de terminaison réseau suivantes : 
    - La IaaS VM doit être en mesure de vous connecter au point de terminaison Azure Active Directory \[Login.windows.net\] afin d’obtenir un jeton pour vous connecter à l’archivage sécurisé clé Azure
    - La IaaS VM doit être en mesure de vous connecter à un point de terminaison de l’archivage sécurisé de clé Azure pour écrire les perte des clés dans l’archivage sécurisé clés client
    - La IaaS VM doit être en mesure de vous connecter à un point de terminaison de stockage Azure qui héberge le référentiel extension Azure et le compte de stockage Azure qui héberge ces fichiers

**Remarque :** Si votre stratégie de sécurité limite l’accès à partir d’Azure machines virtuelles à Internet, vous pouvez résoudre l’URI ci-dessus vers lequel vous avez besoin de connectivité et configurez une règle spécifique pour autoriser la connectivité sortante pour les adresses IP.

- Utilisez la dernière version de version Azure PowerShell SDK pour configurer le chiffrement des disques Azure. Téléchargez la dernière version de [relâcher la touche PowerShell Azure](https://github.com/Azure/azure-powershell/releases)

**Remarque :** Chiffrement de disque Azure n’est pas pris en charge sur [Azure PowerShell SDK version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Si vous recevez un message d’erreur relatifs à l’utilisation de PowerShell Azure 1.1.0, consultez l’article [Azure disque chiffrement d’erreur de contenu lié à Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

- Pour exécuter une des commandes Azure infrastructure du langage commun et associer à votre abonnement Azure, vous devez d’abord installer version Azure infrastructure du langage commun :
    - Pour installer Azure infrastructure du langage commun et associer à votre abonnement Azure, Découvrez [comment installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md)
    - À l’aide de l’infrastructure du langage commun Azure pour Mac, Linux et Windows avec le Gestionnaire de ressources Azure, voir [ici](azure-cli-arm-commands.md)
- Solution de chiffrement disque Azure utilisez protection de clé externe BitLocker pour les machines virtuelles Windows IaaS. Si vos ordinateurs virtuels sont joint au domaine, n’introduisez pas les stratégies de groupe qui mettent en œuvre des logiciels de protection plateforme sécurisée. Consultez [cet article](https://technet.microsoft.com/library/ee706521) pour plus d’informations sur la stratégie de groupe pour « Autoriser BitLocker sans un module de plateforme sécurisée compatible ».
- Le script prérequis de PowerShell disque Azure chiffrement pour créer l’application Azure AD, créer l’archivage sécurisé clé nouveau ou l’archivage sécurisé clés existants le programme d’installation et activer le chiffrement se trouve [ici](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

#### <a name="setup-the-azure-ad-application-in-azure-active-directory"></a>Configuration de l’Application Azure AD dans Azure Active Directory

Lorsque le chiffrement doit être activée pour une machine virtuelle en cours d’exécution dans Azure, le chiffrement des disques Azure génère et écrit les clés de chiffrement dans votre l’archivage sécurisé clé. Gestion des clés de chiffrement dans l’archivage sécurisé clé doivent l’authentification Azure Active Directory.

À cet effet, une application Azure AD doit être créé. La procédure détaillée pour l’enregistrement d’une application sont accessibles ici, dans la section « Obtenir une identité pour l’Application » dans ce [billet de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx).  Ce billet contienne également un nombre d’exemples utiles sur mise en service et la configuration de votre l’archivage sécurisé clé. À des fins d’authentification, l’authentification basée sur secret client ou l’authentification basée sur certificat Azure AD client peut être utilisée.

##### <a name="client-secret-based-authentication-for-azure-ad"></a>Authentification pour Azure AD basée sur le secret client

Les sections suivantes présentent les étapes nécessaires pour configurer une client secrète l’authentification basée sur pour Azure AD.

##### <a name="create-a-new-azure-ad-app-using-azure-powershell"></a>Créer une nouvelle application Azure AD à l’aide de PowerShell Azure

Utiliser l’applet de commande PowerShell ci-dessous pour créer une nouvelle application Azure AD :

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Remarque :** $azureAdApplication.ApplicationId est l’identifiant du client Azure AD et $aadClientSecret est le nom d’utilisateur que vous devez utiliser ultérieurement pour activer MDE client. Vous devez sauvegarder le secret client Azure AD correctement.


##### <a name="provisioning-the-azure-ad-client-id-and-secret-from-the-azure-classic-deployment-model-portal"></a>Mise en service de l’ID de client Azure AD et le code secret à partir du modèle de déploiement Azure classique portail

Azure AD Client ID et le code secret peut également être configurée en utilisant le modèle de déploiement Azure classique portail en https://manage.windowsazure.com, suivez les étapes ci-dessous pour effectuer cette tâche :

1.Cliquez sur l’onglet Active Directory comme illustré ci-dessous :

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2.Cliquez sur Ajouter Application et tapez le nom de l’application comme indiqué ci-dessous :

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Cliquez sur la flèche et configurer les propriétés de l’application comme indiqué ci-dessous :

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Cliquez sur la coche située dans le coin inférieur gauche pour terminer. Page de configuration de l’application apparaît. Notez que l’ID de Client AD Azure se trouve dans la partie inférieure de la page, comme illustré ci-dessous.

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5 enregistrer le code secret client Azure AD par clic sur le bouton Enregistrer. Cliquez sur Enregistrer bouton et notez le code secret à partir de la zone de texte touches, il s’agit du secret client Azure AD. Vous devez sauvegarder le secret client Azure AD correctement.

![Chiffrement de disque Azure](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


**Remarque :** ce flux ci-dessus n’est pas pris en charge dans le portail.

##### <a name="use-an-existing-app"></a>Utiliser une application existante

Pour exécuter les commandes ci-dessous, vous devez le module Azure Active Directory PowerShell, qui peut être obtenu à partir [d’ici](https://technet.microsoft.com/library/jj151815.aspx).

**Remarque :** les commandes ci-dessous doivent être exécutées à partir d’une nouvelle fenêtre PowerShell. N’utilisez pas Azure PowerShell ou dans la fenêtre Gestionnaire de ressources Azure pour exécuter ces commandes. La raison de cette recommandation est parce que ces applets de commande dans le module de MSOnline ou Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Authentification par certificat pour Azure AD

> [AZURE.NOTE] Authentification par certificat AAD n’est actuellement pas pris en charge sur les ordinateurs virtuels Linux.

Les sections suivantes présentent les étapes nécessaires pour configurer une authentification par certificat pour Azure AD.

##### <a name="create-a-new-azure-ad-app"></a>Créer une nouvelle application Azure AD

Exécuter les applets de commande PowerShell ci-dessous pour créer une nouvelle application Azure AD :

**Remarque :** Remplacer `yourpassword` chaîne en dessous de votre mot de passe sécurisé et protéger le mot de passe.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Une fois que vous avez terminé cette étape, télécharger un fichier .pfx sur l’archivage sécurisé clé et activer la stratégie d’accès nécessaire pour déployer ce certificat sur un ordinateur virtuel.

##### <a name="use-an-existing-azure-ad-app"></a>Utiliser une application Azure AD existante
Si vous configurez l’authentification par certificat pour une application existante, utilisez les applets de commande PowerShell ci-dessous. Vérifiez que les exécuter à partir d’une nouvelle fenêtre PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Une fois que vous avez terminé cette étape, télécharger un fichier .pfx sur l’archivage sécurisé clé et activer la stratégie d’accès nécessaire pour déployer ce certificat sur un ordinateur virtuel.

##### <a name="upload-a-pfx-file-to-key-vault"></a>Télécharger un fichier PFX sur l’archivage sécurisé clé
Vous pouvez lire ce [billet de blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) explication de détails sur le fonctionnement de ce processus. Les applets de commande PowerShell ci-dessous existe cependant, il que vous suffit de cette tâche. Vérifiez que les exécuter à partir d’Azure PowerShell console :

**Remarque :** Remplacer `yourpassword` chaîne en dessous de votre mot de passe sécurisé et protéger le mot de passe.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-key-vault-to-an-existing-vm"></a>Déployer un certificat dans l’archivage sécurisé clé sur un ordinateur virtuel existant
Après avoir terminé de télécharger le PFX, suivez les étapes ci-dessous pour déployer un certificat dans l’archivage sécurisé clé sur un ordinateur virtuel existant :

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### <a name="setting-key-vault-access-policy-for-the-azure-ad-application"></a>Configuration de stratégie d’accès de l’archivage sécurisé clé pour l’Application AD Azure

Votre application Azure AD doit des droits relatifs à l’accès aux clés ou secrets dans l’archivage sécurisé. Utiliser l’applet de commande [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) pour accorder des autorisations pour l’application, à l’aide de l’Id de Client (qui a été généré lors de l’application a été inscrite) en tant que la valeur du paramètre – nom principal de service. Vous pouvez lire [ce billet de blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) pour obtenir des exemples sur ce point. Ci-dessous, vous disposez également d’un exemple montrant comment effectuer cette tâche via PowerShell :

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**Remarque**: le chiffrement des disques Azure, vous devez configurer les stratégies d’accès suivantes à votre Application cliente AAD - autorisations « WrapKey » et « Set »

## <a name="terminology"></a>Terminologie

Utilisez le tableau terminologie comme référence pour comprendre certains des termes couramment utilisés par cette technologie :

| Terminologie           | Définition                                                                                                                                                                                                                                   |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD                   | Azure AD est [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Azure AD compte est requis pour l’authentification, le stockage et l’extraction secrets de l’archivage sécurisé clé.                                                                                                        |
| Archivage de sécurisé de clés Azure [AKV] | L’archivage sécurisé clé Azure est un service de gestion de clés de chiffrement basé sur validées FIPS Modules matériels de sécurité pour protéger vos clés de chiffrement et secrets sensibles en toute sécurité., reportez-vous à la documentation de [L’archivage sécurisé clé](https://azure.microsoft.com/services/key-vault/) pour plus d’informations.          |
| PROCESSEUR                   | Gestionnaire de ressources Azure                                                                                                                                                                                                                       |
| BitLocker             | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est qu'un secteur reconnus technologie de chiffrement de volume Windows utilisée pour activer le chiffrement de disque dans Windows IaaS machines virtuelles                                                                                                                  |
| BEK                   | Clés de chiffrement BitLocker sont utilisées pour chiffrer le volume de démarrage du système d’exploitation et les volumes de données. Les clés BitLocker sont divulgation d’informations dans l’archivage sécurisé clés Azure du client en tant que secrets.                                                                              |
| INFRASTRUCTURE DU LANGAGE COMMUN                   | [Interface de ligne de commande Azure](../xplat-cli-install.md)                                                                                                                                                                                                                 |
| DM-Crypt              | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) est sous-système chiffrement basé sur Linux transparente disque utilisé pour activer le chiffrement de disque dans Linux IaaS machines virtuelles                                                                                                                           |
| KEK                   | Clé de chiffrement est la clé asymétrique (RSA 2048) permet de protéger ou renvoyer à la ligne le mot de passe si vous le souhaitez. Vous pouvez fournir une touche protégées par HSM ou logiciel protégé. Pour plus d’informations, reportez-vous à la documentation de [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/) pour plus d’informations |
| PS applets de commande            | [Applets de commande PowerShell Azure](powershell-install-configure.md)                                                                                                                                                                                                                                           |

### <a name="setting-and-configuring-azure-key-vault-for-azure-disk-encryption-usage"></a>Utilisation de chiffrement des disques paramètre et configuration de l’archivage sécurisé de clé Azure Azure

Chiffrement de disque Azure protège les clés de chiffrement de disque et secrets dans votre l’archivage sécurisé de clé Azure. Suivez les étapes sur chacun d’eux des sections ci-dessous à la configuration de l’archivage sécurisé clé pour l’utilisation du chiffrement disque Azure.

#### <a name="create-a-new-key-vault"></a>Créer un archivage sécurisé clé nouveau
Pour créer un nouveau l’archivage sécurisé clé, utilisez une des options ci-dessous :

- Utiliser le modèle de gestionnaire de ressources « 101-créer-KeyVault » situé [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Utiliser PowerShell Azure [applets de commande de l’archivage sécurisé clé](https://msdn.microsoft.com/library/dn868052.aspx).
- Utiliser le portail de gestionnaire de ressources Azure.

**Remarque :** Si vous avez déjà une configuration de l’archivage sécurisé clé pour votre abonnement, passez à la section suivante.

![Archivage sécurisé clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="provisioning-a-key-encryption-key-optional"></a>Mise en service d’une clé de chiffrement (facultatif)

Si vous souhaitez utiliser une clé de chiffrement de clé (KEK) pour un niveau de sécurité supplémentaire pour placer les clés de chiffrement BitLocker, vous devez ajouter un KEK à votre l’archivage sécurisé clé à utiliser dans le processus de configuration.  Utiliser l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) pour créer une nouvelle clé de chiffrement de clé dans l’archivage sécurisé clé. Vous pouvez également importer KEK à partir de votre gestionnaire de clés locaux HSM. Pour plus d’informations, voir [l’archivage sécurisé clé documentation](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

La KEK peut être ajouté dans le Gestionnaire de ressources Azure portail également à l’aide de Azure clé l’archivage sécurisé expérience utilisateur.

![Archivage sécurisé clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions-to-allow-the-azure-platform-access-to-the-keys-and-secrets"></a>Définir des autorisations de l’archivage sécurisé clé pour permettre le niveau d’accès aux clés et secrets plateforme Azure

La plateforme Azure doit avoir accès aux clés de chiffrement ou secrets dans votre l’archivage sécurisé de clé Azure afin de les rendre disponibles pour la machine virtuelle pour démarrer et déchiffrer les volumes. Pour accorder des autorisations pour la plateforme Azure pour qu’il puisse accéder l’archivage sécurisé clé, la propriété *enabledForDiskEncryption* doit être définie sur l’archivage sécurisé clé. Vous pouvez définir la propriété enabledForDiskEncryption sur votre l’archivage sécurisé clé à l’aide de l’applet de commande PS de l’archivage sécurisé clé :

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Vous pouvez également définir la propriété *enabledForDiskEncryption* en visitant https://resources.azure.com. Vous devez définir la propriété *enabledForDiskEncryption* sur votre l’archivage sécurisé clé comme mentionné précédemment. Dans le cas contraire le déploiement échouera.

Vous pouvez configurer les stratégies d’accès pour votre application AAD à partir de l’expérience utilisateur l’archivage sécurisé clé :

![Archivage sécurisé clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Archivage sécurisé clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Vérifiez que l’archivage sécurisé clé est activé pour le chiffrement de disque dans « Stratégies d’accès avancé » :

![Archivage sécurisé clé Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scénarios de déploiement de chiffrement de disque et de l’expérience utilisateur

Il existe de nombreux scénarios que vous pouvez activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections qui suivent couvre de façon plus détaillée ces scénarios.

### <a name="enable-encryption-on-new-iaas-vms-created-from-the-azure-gallery"></a>Activer le chiffrement de nouveau IaaS VM créé à partir de la galerie d’Azure

Chiffrement de disque peut être activé sur nouvel ordinateur Windows IaaS virtuel à partir d’Azure galerie dans Azure à l’aide du modèle de gestionnaire de ressources publiés [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration du chiffrement d’entrée dans la carte de paramètres, cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, conditions juridiques et groupe contrat et cliquez sur le bouton Créer pour activer le chiffrement dans un nouveau VM IaaS.

**Remarque :** Ce modèle crée une nouvelle chiffré Windows machine virtuelle à l’aide de l’image de la galerie Windows Server 2012.

Chiffrement de disque peut être activé sur un nouvel ordinateur IaaS RedHat Linux 7.2 virtuel avec un tableau de RAID-0 200 Go à l’aide de [ce](https://aka.ms/fde-rhel) modèle de gestionnaire de ressources. Une fois que le modèle est déployé, vérifiez l’état de chiffrement machine virtuelle à l’aide du `Get-AzureRmVmDiskEncryptionStatus` applet de commande comme décrit dans la section «[lecteur le chiffrement du système d’exploitation sur une machine virtuelle Linux en cours d’exécution](#encrypting-os-drive-on-a-running-linux-vm)». Lorsque l’ordinateur retourne état `VMRestartPending`, redémarrez l’ordinateur virtuel.

Vous pouvez afficher les détails de paramètres de modèle Gestionnaire de ressources pour la nouvelle machine virtuelle à partir de scénario galerie Azure à l’aide des ID Azure AD Client dans le tableau ci-dessous :

| Paramètre                        | Description|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName                 | Nom d’utilisateur de la machine virtuelle                                                                                                                           |
| adminPassword                 | Mot de passe utilisateur d’administration de la machine virtuelle                                                                                                                       |
| newStorageAccountName         | Nom du compte de stockage pour stocker le système d’exploitation et données disques durs virtuels                                                                                                             |
| vmSize                        | Taille de la machine virtuelle. Pour l’instant, seules les séries Standard A, D et G sont pris en charge                                                                                          |
| virtualNetworkName            | Nom de la VNet à laquelle la VM NIC doit appartenir.                                                                                                            |
| subnetName                    | Nom du sous-réseau dans le vNet auquel doit appartenir le VM NIC                                                                                               |
| AADClientID                   | ID de client de l’application Azure AD disposant des autorisations pour écrire des secrets dans l’archivage sécurisé clé                                                                                       |
| AADClientSecret               | Secret client de l’application Azure AD disposant des autorisations pour écrire des secrets dans l’archivage sécurisé clé                                                                                   |
| keyVaultURL                   | URL de la clé de l’archivage sécurisé à quel BitLocker clé doit être téléchargé vers. Vous pouvez accéder à l’aide de l’applet de commande : (Get-AzureRmKeyVault - VaultName-ResourceGroupName). VaultURI |
| keyEncryptionKeyURL           | URL de la clé de chiffrement qui est utilisé pour chiffrer la clé générée BitLocker. Cette étape est facultative.                                                               |
| keyVaultResourceGroup         | Groupe de ressources de l’archivage sécurisé clé                                                               |
| vmName                        | Nom de la machine virtuelle sur quel chiffrement opération doit être effectuée


**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez apporter vos propres KEK à divulgation d’informations supplémentaire de la clé de chiffrement de données (mot de passe secret) dans l’archivage sécurisé clé.

### <a name="enable-encryption-on-new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Activer le chiffrement de nouveau IaaS VM créé à partir de clés client chiffrées dur et de chiffrement

Dans ce scénario, vous pouvez activer le chiffrement en utilisant le Gestionnaire de ressources du modèle, les applets de commande PowerShell ou les commandes de l’infrastructure du langage commun. Les sections ci-dessous montre de façon plus détaillée du modèle de gestionnaire de ressources et de commandes de l’infrastructure du langage commun.

Suivez les instructions de l’une de ces sections de préparation préalable chiffrés images pouvant être utilisées dans Azure. Une fois que l’image est créée, les étapes décrites dans la section suivante peuvent servir à créer une machine virtuelle Azure chiffré.

- [Préparation d’un disque dur virtuel Windows préalable chiffré](#preparing-a-pre-encrypted-windows-vhd)
- [Préparer un VHD Linux préalablement chiffré](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-resource-manager-template"></a>À l’aide du Gestionnaire de ressources du modèle

Chiffrement de disque peut être activé sur client chiffrées disque dur virtuel en utilisant le modèle de gestionnaire de ressources publiés [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration du chiffrement d’entrée dans la carte de paramètres, cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, conditions juridiques et groupe contrat et cliquez sur le bouton Créer pour activer le chiffrement de nouveau IaaS VM.

Les détails de paramètres de modèle Gestionnaire de ressources pour le scénario de disque dur virtuel client chiffré sont décrites dans le tableau ci-dessous :

| Paramètre                        | Description|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nom du compte de stockage pour stocker chiffré OS disque dur virtuel. Ce compte de stockage doit ont déjà été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle                                                     |
| osVhdUri              | Disque dur virtuel URI de système d’exploitation à partir du compte de stockage                                                                                                                                                                                      |
| osType                | Type de produit du système d’exploitation (Windows/Linux)                                                                                                                                                                                         |
| virtualNetworkName    | Nom de la VNet à laquelle la VM NIC doit appartenir. Cela doit déjà créée dans le même groupe de ressources et le même emplacement que la machine virtuelle                                                                     |
| subnetName            | Nom du sous-réseau dans le vNet auquel doit appartenir le VM NIC                                                                                                                                                     |
| vmSize                | Taille de la machine virtuelle. Pour l’instant, seules les séries Standard A, D et G sont pris en charge                                                                                                                                                |
| keyVaultResourceID    | ResourceID identifiant la ressource de l’archivage sécurisé clés dans processeur. Vous pouvez accéder à l’aide de l’applet de commande PowerShell : (Get-AzureRmKeyVault - VaultName &lt;yourKeyVaultName&gt; - ResourceGroupName &lt;yourResourceGroupName&gt;). ResourceId |
| keyVaultSecretUrl     | URL de la clé de chiffrement de disque mis en service dans l’archivage sécurisé clé                                                                                                                                                                |
| keyVaultKekUrl        | URL de la clé de chiffrement est pour chiffrer la clé de chiffrement de disque générée                                                                                                                                       |
| vmName               | Nom de la machine virtuelle IaaS   |


#### <a name="using-powershell-cmdlets"></a>À l’aide des applets de commande PowerShell

Chiffrement de disque peut être activé sur client chiffrées disque dur virtuel à l’aide des applets de commande PS publiés [ici](https://msdn.microsoft.com/library/azure/mt603746.aspx).  

#### <a name="using-cli-commands"></a>À l’aide des commandes de l’infrastructure du langage commun

Suivez les étapes ci-dessous pour activer le chiffrement de disque pour ce scénario à l’aide des commandes de l’infrastructure du langage commun :

1. Définir les stratégies d’accès de l’archivage sécurisé clé :
    - Définir l’indicateur 'EnabledForDiskEncryption' :`azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
    - Définir des autorisations pour l’application Azure AD pour écrire des secrets dans KeyVault :`azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]`
2. Pour activer le chiffrement sur un ordinateur virtuel existant/en cours d’exécution, tapez :  *machine virtuelle azure enable-disque-chiffrement--groupe de ressources <resourceGroupName> --nom <vmName> id de client--aad <aadClientId> secret--aad-client <aadClientSecret> --url du archivage sécurisé clé disque chiffrement <keyVaultURL> --id du archivage sécurisé clé disque chiffrement <keyVaultResourceId> *
3. Obtenir l’état de chiffrement : *« machine virtuelle azure afficher-disque-chiffrement-status--groupe de ressources <resourceGroupName> --nom <vmName> --json «*
4. Pour activer le chiffrement sur une nouvelle machine virtuelle du client chiffrées disque dur virtuel, utilisez les paramètres avec la commande « Créer machine virtuelle azure » ci-dessous :
    - disque-chiffrement-clé-l’archivage sécurisé-id < disque-chiffrement-clé-l’archivage sécurisé-id >
    - disque-chiffrement-clé-url < disque-chiffrement-clé-url >
    - clé de chiffrement id--clé-l’archivage sécurisé-< clé de chiffrement id--clé-l’archivage sécurisé->
    - clé de chiffrement-clé-url < clé-chiffrement-clé-url >


### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Activer le chiffrement d’existant ou en machine virtuelle IaaS Windows Azure

Dans ce scénario, vous pouvez activer le chiffrement en utilisant le Gestionnaire de ressources du modèle, les applets de commande PowerShell ou les commandes de l’infrastructure du langage commun. Les sections ci-dessous montre de façon plus détaillée comment l’activer à l’aide du Gestionnaire de ressources du modèle et les commandes de l’infrastructure du langage commun.

#### <a name="using-resource-manager-template"></a>À l’aide du Gestionnaire de ressources du modèle

Chiffrement de disque peut être activé sur existantes/en cours d’exécution IaaS Windows machine virtuelle dans Azure à l’aide du modèle de gestionnaire de ressources publiés [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration du chiffrement d’entrée dans la carte de paramètres, cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, conditions juridiques et groupe contrat et cliquez sur le bouton Créer pour activer le chiffrement d’existantes/en cours d’exécution IaaS VM.

Les détails de paramètres de modèle de gestionnaire de ressources pour le scénario de machine virtuelle existant/en cours d’exécution à l’aide des ID Azure AD Client sont disponibles dans le tableau ci-dessous :

| Paramètre                 | Description|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | ID de client de l’application Azure AD disposant des autorisations pour écrire des secrets dans l’archivage sécurisé clé                                                                                                                                              |
| AADClientSecret         | Secret client de l’application Azure AD disposant des autorisations pour écrire des secrets dans l’archivage sécurisé clé                                                                                                                                          |
| keyVaultName | Nom de la clé de l’archivage sécurisé à quel BitLocker clé doit être téléchargé vers. Vous pouvez accéder à l’aide de l’applet de commande : (Get-AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | URL de la clé de chiffrement qui est utilisé pour chiffrer la clé générée BitLocker. Cette étape est facultative si vous sélectionnez `nokek` dans la liste déroulante UseExistingKek. Si vous sélectionnez `kek` dans la liste déroulante UseExistingKek, vous devez entrer la valeur keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Type du volume sur le chiffrement opération est exécutée. Les valeurs valides sont « OS », « Données », « Tout »                                                                                                                     |
| sequenceVersion         | Version de séquence de l’opération BitLocker. Incrémenter ce numéro de version chaque fois qu’une opération de chiffrement disque est effectuée sur la même machine virtuelle                                                                             |
| vmName                 | Nom de la machine virtuelle sur quel chiffrement opération doit être effectuée


**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez apporter vos propres KEK à divulgation d’informations supplémentaire de la clé de chiffrement de données (secret de chiffrement BitLocker) dans l’archivage sécurisé clé.

#### <a name="using-powershell-cmdlets"></a>À l’aide des applets de commande PowerShell

Reportez-vous à la **Azure Explorer le chiffrement des disques avec Azure PowerShell** blog billet [partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et [partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) pour plus d’informations sur la façon d’activer le chiffrement à l’aide de chiffrement de disque Azure à l’aide des applets de commande PS.

#### <a name="using-cli-commands"></a>À l’aide des commandes de l’infrastructure du langage commun

Suivez les étapes ci-dessous pour activer le chiffrement sur existantes/en cours d’exécution machine virtuelle Windows IaaS dans Azure à l’aide des commandes de l’infrastructure du langage commun :

1. Définir les stratégies d’accès de l’archivage sécurisé clé :
    - Définir 'EnabledForDiskEncryption' indicateur : » set-stratégie keyvault azure--nom de l’archivage sécurisé <keyVaultName> --activé-de-disque-chiffrement true »
    - Définir des autorisations à application Azure AD pour écrire des secrets dans KeyVault : » set-stratégie keyvault azure--nom de l’archivage sécurisé <keyVaultName> --nom principal de service <aadClientID> --autorisations doivent être appliquées aux clés [\"tous les\"]--autorisations doivent être appliquées aux secrets [\"tous les\"] »
2. Pour activer le chiffrement sur un ordinateur virtuel existant/en cours d’exécution, tapez :  *machine virtuelle azure enable-disque-chiffrement--groupe de ressources <resourceGroupName> --nom <vmName> id de client--aad <aadClientId> secret--aad-client <aadClientSecret> --url du archivage sécurisé clé disque chiffrement <keyVaultURL> --id du archivage sécurisé clé disque chiffrement <keyVaultResourceId> *
3. Obtenir l’état de chiffrement : *« machine virtuelle azure afficher-disque-chiffrement-status--groupe de ressources <resourceGroupName> --nom <vmName> --json «*
4. Pour activer le chiffrement sur une nouvelle machine virtuelle du client chiffrées disque dur virtuel, utilisez les paramètres avec la commande « Créer machine virtuelle azure » ci-dessous :
    - disque-chiffrement-clé-l’archivage sécurisé-id < disque-chiffrement-clé-l’archivage sécurisé-id >
    - disque-chiffrement-clé-url < disque-chiffrement-clé-url >
    - clé de chiffrement id--clé-l’archivage sécurisé-< clé de chiffrement id--clé-l’archivage sécurisé->
    - clé de chiffrement-clé-url < clé-chiffrement-clé-url >


### <a name="enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure"></a>Activer le chiffrement d’existant ou en cours d’exécution machine virtuelle Linux IaaS dans Azure

Chiffrement de disque peut être activé sur existantes/en cours d’exécution IaaS Linux machine virtuelle dans Azure à l’aide du modèle de gestionnaire de ressources publiés [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration du chiffrement d’entrée dans la carte de paramètres, cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, conditions juridiques et groupe contrat et cliquez sur le bouton Créer pour activer le chiffrement d’existantes/en cours d’exécution IaaS VM.

Les détails de paramètres de modèle de gestionnaire de ressources pour le scénario de machine virtuelle existant/en cours d’exécution à l’aide des ID Azure AD Client sont décrites dans le tableau ci-dessous :

| Paramètre                 | Description|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | ID de client de l’application Azure AD disposant des autorisations pour écrire des secrets dans l’archivage sécurisé clé                                                                                                                                              |
| AADClientSecret         | Secret client de l’application Azure AD disposant des autorisations pour écrire des secrets dans l’archivage sécurisé clé                                                                                                                                          |
| keyVaultName | Nom de la clé de l’archivage sécurisé à quel BitLocker clé doit être téléchargé vers. Vous pouvez accéder à l’aide de l’applet de commande : (Get-AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | URL de la clé de chiffrement qui est utilisé pour chiffrer la clé générée BitLocker. Cette étape est facultative si vous sélectionnez « nokek » dans la liste déroulante UseExistingKek. Si vous sélectionnez « kek » dans la liste déroulante UseExistingKek, vous devez entrer la valeur keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Type du volume sur le chiffrement opération est exécutée. Les valeurs prises en charge valides sont « OS » / « Tout » (pour RHEL 7.2, 7.2 CentOS & 16.04 Ubuntu) et « Données » pour tous les autres distros.                                                                                                                      |
| sequenceVersion         | Version de séquence de l’opération BitLocker. Incrémenter ce numéro de version chaque fois qu’une opération de chiffrement disque est effectuée sur la même machine virtuelle                                                                             |
| vmName                 | Nom de la machine virtuelle sur quel chiffrement opération doit être effectuée
| mot de passe              | Tapez un mot de passe fort comme la clé de chiffrement de données                                                                                                                                                                       |                                                                                                                                                                                                                                                      

**Remarque :** KeyEncryptionKeyURL est un paramètre facultatif. Vous pouvez apporter vos propres KEK à divulgation d’informations supplémentaire de la clé de chiffrement de données (mot de passe secret) dans l’archivage sécurisé clé.

#### <a name="cli-commands"></a>Commandes de l’infrastructure du langage commun

Chiffrement de disque peut être activé sur client chiffrées disque dur virtuel à l’aide de la commande de l’infrastructure du langage commun installée à partir [d’ici](../xplat-cli-install.md). Suivez les étapes ci-dessous pour activer le chiffrement sur existantes/en cours d’exécution machine virtuelle Linux IaaS dans Azure à l’aide des commandes de l’infrastructure du langage commun :

1. Définir les stratégies d’accès de l’archivage sécurisé clé :
    - Définir 'EnabledForDiskEncryption' indicateur : » set-stratégie keyvault azure--nom de l’archivage sécurisé <keyVaultName> --activé-de-disque-chiffrement true »
    - Définir des autorisations à application Azure AD pour écrire des secrets dans KeyVault : » set-stratégie keyvault azure--nom de l’archivage sécurisé <keyVaultName> --nom principal de service <aadClientID> --autorisations doivent être appliquées aux clés [\"tous les\"]--autorisations doivent être appliquées aux secrets [\"tous les\"] »
2. Pour activer le chiffrement sur un ordinateur virtuel existant/en cours d’exécution, tapez :  *machine virtuelle azure enable-disque-chiffrement--groupe de ressources <resourceGroupName> --nom <vmName> id de client--aad <aadClientId> secret--aad-client <aadClientSecret> --url du archivage sécurisé clé disque chiffrement <keyVaultURL> --id du archivage sécurisé clé disque chiffrement <keyVaultResourceId> *
3. Obtenir l’état de chiffrement : « machine virtuelle azure afficher-disque-chiffrement-status--groupe de ressources <resourceGroupName> --nom <vmName> --json »
4. Pour activer le chiffrement sur une nouvelle machine virtuelle du client chiffrées disque dur virtuel, utilisez les paramètres avec la commande « Créer machine virtuelle azure » ci-dessous.
    - *disque-chiffrement-clé-l’archivage sécurisé-id < disque-chiffrement-clé-l’archivage sécurisé-id >*
    - *disque-chiffrement-clé-url < disque-chiffrement-clé-url >*
    - *clé de chiffrement id--clé-l’archivage sécurisé-< clé de chiffrement id--clé-l’archivage sécurisé->*
    - *clé de chiffrement-clé-url < clé-chiffrement-clé-url >*

### <a name="get-encryption-status-of-an-encrypted-iaas-vm"></a>Obtenir l’état de chiffrement d’un VM IaaS chiffré

Vous pouvez obtenir l’état de chiffrement à l’aide du portail Azure le Gestionnaire de ressources, des [applets de commande PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) ou des commandes de l’infrastructure du langage commun. Les sections ci-dessous montre comment utiliser le portail Azure et les commandes de l’infrastructure du langage commun pour obtenir l’état de chiffrement.

#### <a name="get-encryption-status-of-an-encrypted-windows-vm-using-azure-resource-manager-portal"></a>Obtenir l’état de chiffrement d’un ordinateur de virtuel Windows chiffrés à l’aide du Gestionnaire de ressources Azure portail

Vous pouvez obtenir l’état de chiffrement de IaaS VM depuis le Gestionnaire de ressources Azure portal. Se connecter au portail Azure à https://portal.azure.com/, cliquez sur lien machines virtuelles dans le menu de gauche pour afficher une vue récapitulative des machines virtuelles dans votre abonnement. Vous pouvez filtrer l’affichage machines virtuelles en sélectionnant le nom de l’abonnement dans le menu déroulant abonnement. Cliquez sur colonnes situées en haut du menu page machines virtuelles. Sélectionnez colonne chiffrement de disque dans la carte de colonne choisir et cliquez sur Mettre à jour. Vous devriez voir la colonne de chiffrement disque affichant l’état de chiffrement « Activé » ou « Non activée » pour chaque machine virtuelle comme indiqué dans l’illustration ci-dessous.

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-encryption-status-of-an-encrypted-windowslinux-iaas-vm-using-disk-encryption-ps-cmdlet"></a>Obtenir l’état de chiffrement d’une chiffré (Windows/Linux) IaaS VM à l’aide de chiffrement des disques applet de commande PS
Vous pouvez obtenir l’état de chiffrement de IaaS VM à partir de l’applet de commande disque chiffrement PS « Get-AzureRmVMDiskEncryptionStatus ». Pour obtenir les paramètres de chiffrement pour votre machine virtuelle, tapez dans votre session PowerShell Azure :

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Le résultat de Get-AzureRmVMDiskEncryptionStatus peut être vérifiée pour le chiffrement clé URL.
    
    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey
    
    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Les valeurs des paramètres OSVolumeEncrypted et DataVolumesEncrypted sont définies sur « Chiffré » montrant que les deux volumes sont chiffrés à l’aide de chiffrement des disques Azure. Reportez-vous à la **Azure Explorer le chiffrement des disques avec Azure PowerShell** blog billet [partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et [partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) pour plus d’informations sur la façon d’activer le chiffrement à l’aide de chiffrement de disque Azure à l’aide des applets de commande PS.

**Remarque**: sur machines virtuelles de Linux, la `Get-AzureRmVMDiskEncryptionStatus` applet de commande prend minutes 3 et 4 pour signaler l’état de chiffrement.

#### <a name="get-encryption-status-of-the-iaas-vm-from-disk-encryption-cli-command"></a>Obtenir l’état de chiffrement de IaaS VM de chiffrement des disques commande infrastructure du langage commun

Vous pouvez obtenir l’état de chiffrement de IaaS VM à partir de la commande infrastructure du langage commun *machine virtuelle azure afficher-disque-chiffrement-status*le chiffrement des disques. Pour obtenir les paramètres de chiffrement pour votre machine virtuelle, tapez votre session Azure infrastructure du langage commun :

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Désactiver le chiffrement exécutant Windows IaaS machine virtuelle

Vous pouvez désactiver le chiffrement sur un Windows ou Linux IaaS machine virtuelle en cours d’exécution via le modèle de gestionnaire de ressources de chiffrement de disque Azure ou les applets de commande PS et spécifie la configuration de déchiffrement.


##### <a name="windows-vm"></a>Machine virtuelle Windows

L’étape de chiffrement désactiver désactive le chiffrement du volume du système d’exploitation ou des données ou les deux sur l’ordinateur en cours d’exécution Windows IaaS virtuel. Vous ne pouvez pas désactiver le volume du système d’exploitation et laissez le volume de données chiffré. Lors de l’étape de chiffrement désactiver est effectué, modèle de déploiement d’Azure classique met à jour le modèle de service machine virtuelle et la machine virtuelle IaaS Windows est marquée déchiffrée. Le contenu de la machine virtuelle est chiffré pas plus inactives. Le chiffrement désactiver ne supprime pas l’archivage sécurisé clés client et le matériel de clé de chiffrement, qui est le chiffrement BitLocker clés pour Windows et mot de passe Linux. 

##### <a name="linux-vm"></a>Machine virtuelle Linux

L’étape de chiffrement désactiver désactive le chiffrement du volume de données sur l’ordinateur en cours d’exécution Linux IaaS virtuel

**Remarque**: la désactivation de chiffrement sur disque du système d’exploitation n’est pas autorisée sur machines virtuelles Linux.

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-resource-manager-template"></a>Désactiver le chiffrement d’existantes/en cours d’exécution IaaS VM dans Azure à l’aide du Gestionnaire de ressources du modèle

Chiffrement de disque peut être désactivé sur exécutez Windows IaaS virtuelle en utilisant le modèle de gestionnaire de ressources publiés [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Cliquez sur le bouton « Déployer sur Azure » sur le modèle de démarrage rapide Azure, configuration déchiffrement d’entrée dans la carte de paramètres, cliquez sur OK. Sélectionnez l’abonnement, groupe de ressources, ressources emplacement, conditions juridiques et groupe contrat et cliquez sur le bouton Créer pour activer le chiffrement dans un nouveau VM IaaS.

Pour Linux VM, [ce](https://aka.ms/decrypt-linuxvm) modèle peut être utilisé pour désactiver le chiffrement.

Détails paramètres du Gestionnaire de ressources modèle permettant de désactiver le chiffrement de IaaS VM en cours d’exécution :

| vmName         | Nom de la machine virtuelle sur quel chiffrement opération doit être effectuée                                                                                                                                                                       |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| volumeType     | Type du volume sur le déchiffrement opération est exécutée. Les valeurs valides sont « OS », « Données », « Tout ». **Remarque :** Vous ne pouvez pas désactiver le chiffrement sur l’exécution du volume de système d’exploitation Windows IaaS machine virtuelle/démarrage sans désactiver le chiffrement de volume « Données ». **Remarque**: la désactivation de chiffrement sur disque du système d’exploitation n’est pas autorisée sur machines virtuelles Linux. |
| sequenceVersion | Version de séquence de l’opération BitLocker. Incrémenter ce numéro de version chaque fois qu’une opération de déchiffrement disque est effectuée sur la même machine virtuelle                                                                                          |

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-ps-cmdlet"></a>Désactiver le chiffrement d’existantes/en cours d’exécution IaaS VM dans Azure à l’aide de la cmdlet PS

Pour désactiver à l’aide de l’applet de commande PS, applet de commande [Disable AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) désactive le chiffrement sur une infrastructure comme une machine virtuelle service (IaaS). Cette applet de commande prend en charge Windows et machines virtuelles Linux. Cette applet de commande installe une extension sur l’ordinateur virtuel pour désactiver le chiffrement. Si le paramètre de nom n’est pas spécifié, une extension avec le nom par défaut « AzureDiskEncryption pour Windows machines virtuelles » est créée. 

Sur les ordinateurs virtuels Linux, l’extension « AzureDiskEncryptionForLinux » est utilisée.

**Remarque**: cette applet de commande redémarre la machine virtuelle. 

## <a name="appendix"></a>Appendice

### <a name="connect-to-your-subscription"></a>Se connecter à votre abonnement

Veillez à consulter la section *conditions préalables* dans ce document avant de poursuivre. Après avoir vérifié que toutes les conditions préalables ont été remplies, suivez les étapes ci-dessous pour vous connecter à votre abonnement :

1.Cliquez démarrer une session PowerShell Azure et se connecter à votre compte Azure avec la commande suivante :

    Login-AzureRmAccount

2.Cliquez si vous avez plusieurs abonnements et que vous souhaitez spécifier un spécifique à utiliser, tapez ce qui suit pour afficher les abonnements pour votre compte :

    Get-AzureRmSubscription

3 pour spécifier l’abonnement que vous voulez utiliser, tapez :

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4.la pour vérifier l’abonnement configuré est correct, tapez :

    Get-AzureRmSubscription

5 pour confirmer que les applets de commande de chiffrement des disques Azure sont installés, tapez :

    Get-command *diskencryption*

6 vous devriez voir la sous sortie confirmation de l’installation Azure disque chiffrement PowerShell :

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### <a name="preparing-a-pre-encrypted-windows-vhd"></a>Préparation d’un disque dur virtuel Windows préalable chiffré
Les sections suivantes sont nécessaires pour préparer un disque dur virtuel Windows préalable chiffré déploiement comme un disque dur virtuel chiffré dans Azure IaaS. Les étapes sont utilisées pour préparer et démarrer une nouvelle windows machine virtuelle (disque dur virtuel) sur Hyper-V ou Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Mettre à jour la stratégie de groupe pour autoriser les non-plateforme sécurisée pour la protection du système d’exploitation
Vous devez configurer le paramètre de stratégie de groupe BitLocker appelé lecteur BitLocker, situé sous stratégie ordinateur Local \Computer administration\Composants Windows. Modifier ce paramètre à : *Lecteurs de système d’exploitation - exiger une authentification supplémentaire au démarrage - Autoriser BitLocker sans un module de plateforme sécurisée compatible* comme indiqué dans l’illustration ci-dessous :

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Installer les composants de fonctionnalité BitLocker
Pour Windows Server 2012 et ci-dessus utilisent l’en dessous de la commande :

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Pour utiliser Windows Server 2008 R2 l’en dessous de la commande :

    ServerManagerCmd -install BitLockers

#### <a name="prepare-os-volume-for-bitlocker-using-bdehdcfg"></a>Préparer le volume du système d’exploitation à l’aide de BitLocker`bdehdcfg`

Exécutez la commande ci-dessous pour compresser la partition du système d’exploitation et préparer la machine BitLocker.

    bdehdcfg -target c: shrink -quiet

#### <a name="using-bitlocker-to-protect-the-os-volume"></a>Utilisation de BitLocker pour protéger le volume du système d’exploitation
Utiliser la [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) commande pour activer le chiffrement sur le volume de démarrage à l’aide d’un logiciel de protection de clé externe et placez la clé externe (fichier .bek) sur le disque externe ou le volume. Chiffrement sera activé sur le volume système/de démarrage après le redémarrage suivant.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Remarque :** La machine virtuelle doit être préparé avec un disque dur virtuel/ressources de données distinct pour obtenir la clé externe à l’aide de BitLocker.

#### <a name="encrypting-os-drive-on-a-running-linux-vm"></a>Chiffrer lecteur du système d’exploitation sur une VM Linux en cours d’exécution

Chiffrement du lecteur du système d’exploitation sur une VM Linux en cours d’exécution est pris en charge sur les distros suivants :

- RHEL 7.2
- CentOS 7.2
- Ubuntu 16.04

Conditions préalables pour le chiffrement des disques du système d’exploitation :

- Machine virtuelle doit être créé à partir de l’image de la galerie Azure dans le Gestionnaire de ressources Azure portal.
- Machine virtuelle Azure au moins 4 Go de RAM (recommandé la taille est 7 Go).
- (Pour RHEL et CentOS) SELinux doit être [désactivé](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) sur la machine virtuelle. La machine virtuelle doit être redémarrée au moins une fois après avoir désactivé SELinux.


##### <a name="steps"></a>Étapes

1.Cliquez créer une machine virtuelle à l’aide de la distros spécifiés ci-dessus.

7.2 CentOS, chiffrement de disque du système d’exploitation est pris en charge via une image particulière. Pour utiliser cette image, spécifiez « 7.2n » en tant que la référence (SKU) lors de la création de la machine virtuelle :

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2.Cliquez configurer la machine virtuelle selon vos besoins. Si vous envisagez de chiffrer toutes les données lecteurs doivent être/etc/fstab spécifiée et monter dans les lecteurs (OS + données).

> [AZURE.NOTE] Vous devez utiliser UUID =... pour spécifier les données lecteurs dans/etc/fstab au lieu de spécifier le nom du périphérique bloc, par exemple, /dev/sdb1. Pendant le chiffrement l’ordre des lecteurs remplacera sur l’ordinateur virtuel. Si votre ordinateur virtuel s’appuie sur un ordre spécifique de périphériques de bloc que celui-ci ne pourra pas les monter après le chiffrement.

3 sessions SSH déconnexion.

4.la pour chiffrer le système d’exploitation, spécifiez volumeType comme « tout » ou « OS » lors de [l’activation de chiffrement](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

> [AZURE.NOTE] Tous les processus d’espace utilisateur qui n’exécutent pas en tant que `systemd` services doivent être supprimés avec un `SIGKILL`. La machine virtuelle doit être redémarrée. Veuillez planifier temps d’arrêt de la machine virtuelle lors de l’activation de système d’exploitation disque chiffrement sur un ordinateur virtuel en cours d’exécution.

5 régulièrement surveiller l’avancement de chiffrement en suivant les instructions fournies dans la [section suivante](#monitoring-os-encryption-progress).

6 une fois que Get-AzureRmVmDiskEncryptionStatus affiche « VMRestartPending », redémarrez votre ordinateur virtuel par l’enregistrement rouvrez-le ou via le portail/PowerShell/CLI.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

Il est recommandé d’enregistrer les [diagnostics de démarrage](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) de la machine virtuelle *avant* le redémarrage.

#### <a name="monitoring-os-encryption-progress"></a>Contrôle de la progression de chiffrement du système d’exploitation

Il existe trois manières pour surveiller l’avancement de chiffrement du système d’exploitation.

1.Cliquez utiliser l’applet de commande Get-AzureRmVmDiskEncryptionStatus et vérifiez que le champ ProgressMessage : 
 
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

Une fois que la machine virtuelle atteint « Du système d’exploitation disque chiffrement démarré » prendra plus ou moins 40 à 50 minutes sur un stockage Premium sauvegardée machine virtuelle.

En raison de [problème #388](https://github.com/Azure/WALinuxAgent/issues/388) dans WALinuxAgent, `OsVolumeEncrypted` et `DataVolumesEncrypted` apparaissent sous forme de `Unknown` dans certaines distros. Avec WALinuxAgent version 2.1.5 et au-dessus seront corrigée automatiquement. Au cas où vous voyez `Unknown` dans le résultat, vous pouvez vérifier état de chiffrement de disque à l’aide Azure ressource Viewer.

Accédez à la [Visionneuse de ressources Azure](https://resources.azure.com/), puis développez cette hiérarchie dans le volet sélection à gauche :

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

Dans la InstanceView, faites défiler vers le bas pour afficher l’état de chiffrement de vos lecteurs.

![Vue d’Instance de machine virtuelle](./media/azure-security-disk-encryption/vm-instanceview.png)

2.Cliquez examiner [diagnostics démarrage](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). Messages d’extension MDE doivent être précédés de `[AzureDiskEncryption]`.

3 ouverture de session à la machine virtuelle via SSH et en le journal de l’extension à partir de

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

Il n’est pas recommandé pour vous connecter à la machine virtuelle alors que le chiffrement du système d’exploitation est en cours. Par conséquent, les journaux doivent être copiés uniquement lorsque les deux autres méthodes ont échoué.

#### <a name="preparing-a-pre-encrypted-linux-vhd"></a>Préparer un VHD Linux préalablement chiffré

##### <a name="ubuntu-16"></a>16 Ubuntu

###### <a name="configure-encryption-during-distro-install"></a>Configurer le chiffrement lors de l’installation distro

1 Sélectionnez « Configurer les volumes chiffrés » lorsque partition disques.

![Programme d’installation Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2.Cliquez créer un lecteur de démarrage séparée qui ne doit pas être chiffré. Chiffrer votre lecteur racine.

![Programme d’installation Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3.dans fournir un mot de passe. Il s’agit du mot de passe que vous allez télécharger dans KeyVault.

![Programme d’installation Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4.la fin partition.

![Programme d’installation Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5 lors du démarrage de la machine virtuelle, vous êtes invité pour un mot de passe. Utilisez le mot de passe que vous avez indiquées dans l’étape 3.

![Programme d’installation Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6 préparer machine virtuelle pour le téléchargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). La dernière étape (annulation de la machine virtuelle) ne sont pas exécutées encore.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurer le chiffrement pour l’utiliser avec Azure

1.Cliquez créer un fichier sous /usr/local/sbin/azure_crypt_key.sh, avec le contenu dans le script ci-dessous. Soyez attentif aux KeyFileName, car il s’agit du nom du fichier de mot de passe par Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2.Cliquez modifier la configuration crypt dans */etc/crypttab*. Il doit ressembler à ceci :

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3 Si vous modifiez *azure_crypt_key.sh* dans Windows et copiés Linux, n’oubliez pas d’exécuter */usr/local/sbin/azure_crypt_key.sh dos2unix*.

4.la ajouter des autorisations exécutables au script :

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4.la modifier */etc/initramfs-tools/modules* en ajoutant des lignes :

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5 `update-initramfs -u -k all` pour mettre à jour l’initramfs pour rendre la `keyscript` prennent effet.
6 vous pouvez maintenant deprovision la machine virtuelle.

![Programme d’installation Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7 passez à l’étape suivante et [télécharger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2

###### <a name="configure-encryption-during-distro-install"></a>Configurer le chiffrement lors de l’installation distro

1 Sélectionnez « Chiffrer le Volume de groupe » lorsque partition disques. Fournir un mot de passe. Il s’agit du mot de passe que vous allez télécharger dans KeyVault.

![Configuration openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2.Cliquez démarrer la machine virtuelle à l’aide de votre mot de passe.

![Configuration openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3.dans préparer machine virtuelle pour le téléchargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). La dernière étape (annulation de la machine virtuelle) ne sont pas exécutées encore.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurer le chiffrement pour l’utiliser avec Azure

1.Cliquez modifier la /etc/dracut.conf et ajoutez la ligne suivante :

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2.Cliquez commentez ces lignes à la fin du fichier « / usr/lib/dracut/modules.d/90crypt/module-setup.sh » :

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3 ajouter la ligne suivante au début du fichier « / usr/lib/dracut/modules.d/90crypt/parse-crypt.sh »

    DRACUT_SYSTEMD=0

et remplacez toutes les occurrences de

    if [ -z "$DRACUT_SYSTEMD" ]; then

À

    if [ 1 ]; then

4.la modifier /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et l’ajouter une fois le « périphérique # LUKS ouvrir »

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5 le « / usr/sbin/dracut - f - v » pour mettre à jour l’initrd.

6 vous pouvez maintenant deprovision la machine virtuelle et [télécharger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### <a name="centos-7"></a>CentOS 7

###### <a name="configure-encryption-during-distro-install"></a>Configurer le chiffrement lors de l’installation distro

1.Cliquez sélectionnez « chiffrer mes données » lors de la partition disques.

![Programme d’installation centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2.Cliquez Vérifiez que « Chiffrer » est sélectionnée pour partition racine.

![Programme d’installation centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3.dans fournir un mot de passe. Il s’agit du mot de passe que vous allez télécharger dans KeyVault.

![Programme d’installation centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4.la lors du démarrage de la machine virtuelle, vous êtes invité pour un mot de passe. Utilisez le mot de passe que vous avez indiquées dans l’étape 3.

![Programme d’installation centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5 préparer machine virtuelle pour le téléchargement dans Azure à l’aide de [ces instructions](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). La dernière étape (annulation de la machine virtuelle) ne sont pas exécutées encore.

6 vous pouvez maintenant deprovision la machine virtuelle et [télécharger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurer le chiffrement pour l’utiliser avec Azure

1.Cliquez modifier la /etc/dracut.conf et ajoutez la ligne suivante :

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2.Cliquez commentez ces lignes à la fin du fichier « / usr/lib/dracut/modules.d/90crypt/module-setup.sh » :

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3 ajouter la ligne suivante au début du fichier « / usr/lib/dracut/modules.d/90crypt/parse-crypt.sh »

    DRACUT_SYSTEMD=0

et remplacez toutes les occurrences de

    if [ -z "$DRACUT_SYSTEMD" ]; then

À

    if [ 1 ]; then

4.la modifier /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et l’ajouter une fois le « périphérique # LUKS ouvrir »

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5 le « / usr/sbin/dracut - f - v » pour mettre à jour l’initrd.

![Programme d’installation centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Télécharger le disque dur virtuel chiffré à un compte de stockage Azure
Une fois le chiffrement valeur_nominale DM Crypt BitLocker le chiffrement est activé, le disque dur virtuel chiffré local doit être téléchargés vers votre compte de stockage.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-disk-encryption-secret-for-the-pre-encrypted-vm-to-key-vault"></a>Télécharger secret de chiffrement de disque pour la machine virtuelle préalable chiffrée pour l’archivage sécurisé clé
Le code secret de chiffrement de disque obtenu précédemment doit être téléchargé au code secret utilisé dans l’archivage sécurisé clé. L’archivage sécurisé clé doit disposer des autorisations activées pour votre client AAD ainsi que le chiffrement des disques.


    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Code secret de chiffrement de disque ne pas été chiffré avec un KEK
[Jeu-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) permet de fournir le code secret dans l’archivage sécurisé clé. Dans le cas d’une machine virtuelle Windows, le fichier bek est codé sous forme de chaîne en base 64 et puis téléchargé dans l’archivage sécurisé clé à l’aide de l’applet de commande Set-AzureKeyVaultSecret. Pour Linux, le mot de passe est codée sous forme de chaîne en base 64 et puis téléchargé dans l’archivage sécurisé clé. En outre, vérifiez que les balises suivantes sont définis lors de la création du secret dans l’archivage sécurisé clé.

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

La `$secretUrl` doit être utilisé dans l’étape suivante pour [fixer le disque du système d’exploitation sans utiliser KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Code secret de chiffrement de disque chiffrée avec un KEK

Le code secret peut éventuellement être chiffré avec une clé de chiffrement avant de télécharger à l’archivage sécurisé clé. Utiliser l' habillage [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) pour tout d’abord chiffrer le mot de passe à l’aide de la clé de chiffrement. Le résultat de cette opération de retour à la ligne est une chaîne d’URL codé en base 64 qui est ensuite téléchargée au code secret à l’aide de l’applet de commande [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) .

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

La `$KeyEncryptionKey` et `$secretUrl` doivent être utilisés dans l’étape suivante pour [fixer le disque du système d’exploitation à l’aide de KEK](#using-a-kek).

### <a name="specify-secret-url-when-attaching-os-disk"></a>Spécifier des URL secrète lorsque vous joignez disque du système d’exploitation

#### <a name="without-using-a-kek"></a>Sans utiliser un KEK

Lors de l’attache du disque du système d’exploitation, `$secretUrl` doit être passé. L’URL a été générée dans la section [« code secret chiffrement disque pas chiffré avec un KEK »](#disk-encryption-secret-not-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>À l’aide d’un KEK

Lors de l’attache du disque du système d’exploitation, `$KeyEncryptionKey` et `$secretUrl` doivent être passés. L’URL a été générée dans la section [« code secret chiffrement disque chiffrées avec un KEK »](#disk-encryption-secret-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Téléchargez ce Guide
Vous pouvez télécharger ce guide dans la [Galerie TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## <a name="for-more-information"></a>Pour plus d’informations
[Explorer le chiffrement des disques Azure avec PowerShell Azure](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explorer le chiffrement des disques Azure avec Azure PowerShell - partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
