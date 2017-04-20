<properties
    pageTitle="Déploiement d’Azure pile du contact | Microsoft Azure"
    description="Découvrez comment préparer le contrôle du concept pile Azure et exécuter le script PowerShell pour déployer Azure pile du contact."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Déploiement d’Azure pile du contact
Pour déployer le contrôle du concept pile Azure, vous devez tout d’abord [préparer l’ordinateur de déploiement](#prepare-the-deployment-machine) , puis sur [exécuter le script de déploiement PowerShell](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Téléchargez et installez Microsoft Azure pile du contact TP2

Avant de commencer, vérifiez que vous au moins 85 Go d’espace.

1. Le téléchargement d’Azure pile du contact TP2 est constitué d’un fichier zip contenant les fichiers suivants 12, environ 20 Go au total :
    - 1 MicrosoftAzureStackPOC.EXE
2. Passez en revue les informations de l’Assistant Auto-extracteur et écran contrat de licence, puis sur **suivant**.
3. Passez en revue les informations de l’Assistant Extraction automatique et l’écran de la déclaration de confidentialité, puis sur **suivant**.
4. Sélectionnez la Destination pour les fichiers extraits, cliquez sur **suivant**.
    - La valeur par défaut est : <drive letter>:\<dossier actif > \Microsoft Azure pile du contact
5. Passez en revue les informations de l’Assistant Extraction automatique et l’écran de l’emplacement de Destination, puis cliquez sur **Extraire** pour extraire les fichiers de ThirdPartyLicenses.rtf et CloudBuilder.vhdx (~44.5 Go).

> [AZURE.NOTE] Une fois que vous avez extrait les fichiers, vous pouvez supprimer le fichier zip pour récupérer de l’espace sur l’ordinateur. Ou bien, vous pouvez déplacer le fichier zip vers un autre emplacement afin qu’if que vous deviez redéployez vous n’avez pas besoin de télécharger à nouveau les fichiers zip.

## <a name="prepare-the-deployment-machine"></a>Préparer l’ordinateur de déploiement

1. Vérifiez que vous pouvez physiquement se connecter à l’ordinateur de déploiement, ou ont accès à la console physique (par exemple, KVM). Vous devrez tel accès après le redémarrage de l’ordinateur de déploiement à l’étape 9.

2. Vérifiez que l’ordinateur de déploiement dispose de la [configuration minimale requise](azure-stack-deploy.md). Vous pouvez utiliser le [Vérificateur de déploiement d’Azure pile Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) pour confirmer vos besoins.

3. Ouvrez une session tant qu’administrateur du Local sur votre ordinateur et du contact.

4. Copiez le fichier CloudBuilder.vhdx dans C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Si vous choisissez de ne pas utiliser le script recommandé pour préparer votre ordinateur de hôte du contact (étapes 5 – étape 7), ne saisissez n’importe quelle touche licence à la page d’activation. Une version d’évaluation de Windows Server 2016 image est incluse, et entrer une clé de licence expiration provoque des messages d’avertissement.

5. Sur l’ordinateur du contact, exécutez le script PowerShell suivant pour télécharger les fichiers de prise en charge le protocole TP2 pile Azure :

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Ce script télécharge les fichiers de prise en charge le protocole TP2 pile Azure dans le dossier spécifié par le paramètre $LocalPath.
    
6. Ouvrez une console PowerShell élevée et placez-vous à l’endroit où vous avez copié les fichiers.
    - 11 MicrosoftAzureStackPOC-N.BIN (où N est 1-11)
7. Avec le bouton droit sur la MicrosoftAzureStackPOC.EXE > Exécuter en tant qu’administrateur.

8. Exécuter le script PrepareBootFromVHD.ps1. Ce script et les fichiers automatiques sont disponibles avec les autres scripts de prise en charge fournis avec cette version.
    Il existe cinq paramètres de ce script PowerShell :
    - CloudBuilderDiskPath (obligatoire) – le chemin d’accès à la CloudBuilder.vhdx sur l’hôte.
    - DriverPath (facultatif) – vous permet d’ajouter des pilotes supplémentaires pour l’hôte dans le disque dur virtuel.
    - (Facultatif) – ApplyUnattend spécifier ce paramètre commutateur pour automatiser la configuration du système d’exploitation. Si spécifié, l’utilisateur doit fournir AdminPassword pour configurer le système d’exploitation au démarrage (nécessite une condition d’accompagnement fichier unattend_NoKVM.xml).
    Si vous n’utilisez pas ce paramètre, le fichier unattend.xml générique est utilisé sans poursuivre la personnalisation. Vous devrez KVM personnalisation complète après le redémarrage.
    - AdminPassword (facultatif) – utilisé uniquement lorsque le paramètre ApplyUnattend est défini, requiert un minimum de six caractères.
    - VHDLanguage (facultatif) – spécifie la langue du disque dur virtuel, une valeur par défaut « en-us ».
    Le script présentée et contient des exemples d’utilisation, bien que l’utilisation de la plus courante est :
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Si vous exécutez cette commande exacte, vous devez entrer AdminPassword à l’invite.

9. Lorsque le script est terminé, vous devez confirmer le redémarrage. S’il existe d’autres personnes qui se connectent, cette commande échouera. Si la commande échoue, exécutez la commande suivante :`Restart-Computer -force` 

10. L’hôte redémarre dans le système d’exploitation de CloudBuilder.vhdx, où le déploiement continue.

> [AZURE.IMPORTANT] Pile Azure requiert l’accès à Internet, directement ou via un proxy transparent. Le déploiement du contact TP2 prend en charge exactement une carte réseau pour le réseau. Si vous avez plusieurs cartes réseau, assurez-vous que seul est activé (et tous les autres sont désactivés) avant d’exécuter le script de déploiement de la section suivante.

## <a name="run-the-powershell-deployment-script"></a>Exécuter le script de déploiement PowerShell

1. Ouvrez une session tant qu’administrateur du Local sur votre ordinateur et du contact. Utilisez les informations d’identification spécifiées dans les étapes précédentes.

2. Ouvrez une console PowerShell avec élévation de privilèges.

3. Dans PowerShell, exécutez la commande suivante :`cd C:\CloudDeployment\Configuration`

4. Exécutez la commande déployer :`.\InstallAzureStackPOC.ps1`

5. À l’invite **Entrez le mot de passe** , entrez un mot de passe et confirmez-le. Il s’agit du mot de passe pour tous les ordinateurs virtuels. Veillez à enregistrer.

6. Entrez les informations d’identification de votre compte Azure Active Directory. Cet utilisateur doit être l’administrateur Global dans le client de l’annuaire.

7. Le processus de déploiement peut prendre quelques heures, pendant lesquelles le système redémarre automatiquement une seule fois.

    > [AZURE.IMPORTANT] Si vous voulez suivre la progression du déploiement, connectez-vous en tant qu’azurestack\AzureStackAdmin. Si vous êtes connecté en tant qu’un administrateur local une fois que l’ordinateur est joint au domaine, vous ne verrez la progression du déploiement. Ne pas réexécuter déploiement, à la place connectez-vous en tant qu’azurestack\AzureStackAdmin valider qu’il s’exécute.

    Lorsque le déploiement a réussi, la console PowerShell affiche : **achevé : Action « Déploiement »**.

    Si le déploiement échoue, vous pouvez essayer [réexécuter](azure-stack-rerun-deploy.md). Vous pouvez aussi [redéployez](azure-stack-redeploy.md) à partir de zéro.

### <a name="deployment-script-examples"></a>Exemples de scripts de déploiement

Si votre identité AAD n’est associé à un répertoire AAD :

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Si votre identité AAD est associée supérieure à un seul AAD répertoire :

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Si votre environnement ne dispose pas DHCP activé, vous devez inclure les paramètres supplémentaires suivants à l’une des options ci-dessus (exemple d’utilisation fournis) :

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>Paramètres facultatifs InstallAzureStackPOC.ps1

| Paramètre | Obligatoire/facultatif | Description |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Facultatif | Définit le nom d’utilisateur Azure Active Directory et le mot de passe. Ces informations d’identification Azure peuvent être un ID d’organisation ou un Account Microsoft. Pour utiliser les informations d’identification Account Microsoft, omettez ce paramètre dans l’applet de commande. L’omission de ce paramètre vous demande la fenêtre contextuelle d’authentification Azure pendant le déploiement. Cela crée les jetons d’authentification et actualiser utilisés pendant le déploiement. |
| AADDirectoryTenantName | Obligatoire | Définit le répertoire client. Utilisez ce paramètre pour spécifier un répertoire spécifique dans lequel le compte DAS dispose des autorisations pour gérer plusieurs annuaires. Nom d’un client d’annuaire AAD au format complet <directoryName>. onmicrosoft.com. |
| AdminPassword | Obligatoire | Définit le compte d’administrateur local et tous les autres comptes d’utilisateurs sur tous les ordinateurs virtuels créés dans le cadre du déploiement du contact. Ce mot de passe doit correspondre le mot de passe administrateur local actif sur l’hôte. |
| AzureEnvironment | Facultatif | Sélectionnez l’environnement Azure avec lequel vous souhaitez enregistrer ce déploiement d’Azure pile. Les options incluent *Azure Public*, *Azure - Chine*, *Azure - gouvernement des États-Unis*. |
| EnvironmentDNS | Facultatif | Création d’un serveur DNS dans le cadre du déploiement pile Azure. Pour permettre aux ordinateurs à l’intérieur de la solution pour résoudre les noms en dehors de l’horodatage, fournissent votre serveur DNS infrastructure existant. Le serveur DNS dans horodatage transfère les demandes de résolution de noms inconnue à ce serveur. |
| NatIPv4Address | Requis pour la prise en charge DHCP NAT | Définit une adresse IP statique pour MAS BGPNAT01. N’utilisez ce paramètre si le DHCP ne pouvez pas attribuer une adresse IP à accéder à Internet. |
| NatIPv4DefaultGateway | Requis pour la prise en charge DHCP NAT | Définit la passerelle par défaut utilisée avec l’adresse IP statique pour MAS BGPNAT01. N’utilisez ce paramètre si le DHCP ne pouvez pas attribuer une adresse IP à accéder à Internet.  |
| NatIPv4Subnet | Requis pour la prise en charge DHCP NAT | Préfixe réseauIP utilisé pour DHCP prise en charge de NAT. N’utilisez ce paramètre si le DHCP ne pouvez pas attribuer une adresse IP à accéder à Internet.  |
| PublicVLan | Facultatif | Définit l’ID de réseau local virtuel. Utilisez ce paramètre uniquement si l’hôte et MAS BGPNAT01 doivent configurer les ID de réseau local virtuel pour accéder au réseau physique (et Internet). Par exemple,`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Réexécuter | Facultatif | Utilisez cet indicateur à nouveau le déploiement.  Toutes les entrées précédentes sont utilisée. Réenregistrer les données précédemment fournies ne sont pas pris en charge, car plusieurs valeurs uniques sont générées et utilisés pour le déploiement. |
| Serveurs de temps | Facultatif | Utilisez ce paramètre si vous avez besoin spécifier un serveur de temps spécifique. |

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à pile Azure](azure-stack-connect-azure-stack.md)
