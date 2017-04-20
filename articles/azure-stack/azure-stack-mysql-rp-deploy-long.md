<properties
    pageTitle="Déployer le fournisseur de ressources MySQL sur pile Azure | Microsoft Azure"
    description="Procédure détaillée pour déployer le fournisseur de ressources MySQL sur Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>Déployer le fournisseur de ressources MySQL sur pile Azure à utiliser avec les applications Web

> [AZURE.NOTE] Les informations suivantes s’applique uniquement aux déploiements Azure pile TP1.

Utilisez cet article pour suivre les étapes détaillées pour la configuration du fournisseur de ressources MySQL sur la preuve Azure pile de démonstration afin que vous puissiez commencer [à l’aide de bases de données MySQL](azure-stack-mysql-rp-deploy-short.md) sur pile Azure, y compris à l’aide de MySQL en tant que le serveur principal pour les sites WordPress créé avec [Azure Web Apps](azure-stack-webapps-deploy.md).

## <a name="set-up-steps-before-you-deploy"></a>Procédure de configuration avant de déployer

Avant de déployer le fournisseur de ressources, vous devez :

- Avoir une image de Windows Server par défaut avec .NET 3.5
- Désactiver la sécurité renforcée d’Internet Explorer (IE)
- Installer la dernière version de PowerShell Azure

### <a name="create-an-image-of-windows-server-including-net-35"></a>Créer une image de Windows Server, y compris .NET 3.5

Vous pouvez ignorer cette étape si vous avez téléchargé les bits Azure pile après 2/23/2016, car l’image de Windows Server 2012 R2 base par défaut inclut .NET framework 3.5 dans ce téléchargement et versions ultérieures.

Si vous avez téléchargé avant 23/2/2016, vous devez créer un disque dur virtuel de Windows Server 2012 R2 centre de données avec image .NET 3.5 et jeu est en tant que l’image par défaut dans le référentiel Image plate-forme.

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>Désactiver Internet Explorer amélioré sécurité et activer les cookies

Pour déployer un fournisseur de ressources, exécutez l’environnement de l’écriture de script intégré PowerShell (ISE) en tant qu’administrateur, vous devez autoriser les cookies et JavaScript dans le profil d’Internet Explorer que vous permet de vous connecter à Azure Active Directory pour les administrateurs et utilisateurs se-ins.

**Pour désactiver Internet Explorer une sécurité améliorée :**

1. Se connecter à l’ordinateur Azure pile preuve de concept (du contact) en tant qu’AzureStack/administrateur et ouvrez le Gestionnaire de serveur.

2. Activer ou désactiver **La Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et utilisateurs.

3. Se connecter à la machine virtuelle **ClientVM.AzureStack.local** en tant qu’administrateur et ouvrez le Gestionnaire de serveur.

4. Activer ou désactiver **La Configuration de sécurité renforcée d’Internet Explorer** pour les administrateurs et utilisateurs.

**Pour activer les cookies :**

1. Dans l’écran Démarrer de Windows, cliquez sur **toutes les applications**et cliquez sur **Accessoires de Windows**, avec le bouton droit **d’Internet Explorer**, pointez sur **autres**, puis sélectionnez **Exécuter en tant qu’administrateur**.

2. Si vous y êtes invité, vérifiez la **sécurité recommandé d’utiliser**, puis cliquez sur **OK**.

3. Dans Internet Explorer, cliquez sur les **Outils (icône d’engrenage)** &gt; **options Internet** &gt; onglet **confidentialité** .

4. Cliquez sur **Avancé**vous assurer que les deux boutons **Accepter** sont activées et cliquez sur **OK**, puis cliquez à nouveau sur **OK** .

5. Fermez Internet Explorer et redémarrez PowerShell ISE en tant qu’administrateur.

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>Installer une version compatible Azure pile de PowerShell Azure

1. Désinstallez tout PowerShell Azure existant à partir de votre ordinateur Client virtuel.

2. Connectez-vous à l’ordinateur du contact pile Azure en tant qu’administrateur/AzureStack.

3. À l’aide de bureau à distance, connectez-vous à la machine virtuelle **ClientVM.AzureStack.local** en tant qu’administrateur.

4. Ouvrez le panneau de configuration, cliquez sur **désinstaller un programme** &gt; sur **Azure PowerShell** &gt; cliquez sur **désinstaller**.

5. [Téléchargez la dernière PowerShell Azure qui prend en charge Azure pile](http://aka.ms/azstackpsh) et installez-le.

    Après avoir installé PowerShell, vous pouvez exécuter cette vérification script PowerShell pour vous assurer que vous pouvez vous connecter à votre instance Azure pile (une page web de connexion doit apparaître).

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>Démarrage du déploiement de fournisseur de ressources PowerShell

1. Connexion Bureau à distance du contact Azure pile à clientVm.AzureStack.Local et connectez-vous en tant qu’azurestack\\azurestackuser.

2. [Télécharger les fichiers binaires MySQL RP](http://aka.ms/masmysqlrp) fichier et extraire à d\\MySQLRP.

3. Exécuter le d\\MySQLRP\\Bootstrap.cmd fichier en tant qu’administrateur (azurestack\administrator).

    Cette action ouvre le fichier Bootstrap.ps1 dans PowerShell ISE.

4. Windows PowerShell ISE issue de chargement, cliquez sur le bouton « lecture » ou appuyez sur F5.

    Deux onglets principaux charge, chacun d’eux contenant tous les scripts et les fichiers que vous devez déployer votre fournisseur de ressources MySQL.

## <a name="prepare-prerequisites"></a>Préparer les conditions préalables

Cliquez sur l’onglet **Préparer les conditions préalables** pour :

- Créer des certificats requis
- Télécharger les fichiers binaires MySQL à votre pile d’Azure
- Télécharger des objets à un compte de stockage sur Azure pile
- Publier des éléments de la galerie

### <a name="create-the-required-certificates"></a>Créer les certificats requis
Ce script **Nouveau SslCert.ps1** ajoute le \_. Certificat SSL AzureStack.local.pfx à la d\\MySQLRP\\conditions préalables\\BlobStorage\\dossier conteneur. Le certificat permet de sécuriser la communication entre le fournisseur de ressources et de l’instance locale du Gestionnaire de ressources Azure.

1. Sous l’onglet principal **Conditions préalables pour les préparer** , cliquez sur l’onglet **Nouveau SslCert.ps1** et l’exécuter.

2. Dans l’invite qui s’affiche, tapez un mot de passe PFX protège la clé privée et **Prenez note de ce mot de passe**. Vous en aurez besoin ultérieurement.

### <a name="download-mysql-binaries-to-your-azure-stack"></a>Télécharger les fichiers binaires MySQL à votre pile d’Azure

1. Sélectionnez l’onglet **MySqlServer.ps1 de téléchargement** et l’exécuter.
2. Lorsque vous y êtes invité, cliquez sur **Oui** dans la boîte de dialogue de confirmation pour accepter le CLUF.

    Cette commande ajoute deux fichiers zip vers le dossier D:\MySql\Prerequisites\BlobStorage\Container.

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>Télécharger tous les objets dans un compte de stockage sur Azure pile

1. Cliquez sur l’onglet **Téléchargement-Microsoft.MySql-RP.ps1** et l’exécuter.

2. Dans la boîte de dialogue Windows PowerShell de demande d’informations d’identification, tapez les informations d’identification de pile Azure service administrateur.

3. Lorsque vous êtes invité à l’ID de client Azure Active Directory, tapez votre nom de domaine complet du client Azure Active Directory : par exemple, microsoftazurestack.onmicrosoft.com.

    Une fenêtre contextuelle vous invite à fournir les informations d’identification.

    > [AZURE.TIP] Si le menu contextuel ne s’affiche pas, vous que soit n’avez pas désactivé Internet Explorer amélioré sécurité pour activer JavaScript sur cet ordinateur et l’utilisateur, ou vous n’avez pas accepté les cookies dans Internet Explorer. Voir [configurer les étapes avant de déployer](#set-up-steps-before-you-deploy).

4. Tapez vos informations d’identification d’administrateur de Service pile Azure, puis sur **Se connecter**.

### <a name="publish-gallery-items-for-later-resource-creation"></a>Publier des éléments de la galerie pour la création de ressources une version ultérieure

Sélectionnez l’onglet **Publier GalleryPackages.ps1** et l’exécuter. Ce script ajoute deux éléments marketplace à marketplace du portail Azure pile du contact que vous pouvez utiliser pour déployer des ressources de base de données sous forme d’éléments marketplace.

## <a name="deploy-the-mysql-resource-provider-vm"></a>Déployer le fournisseur de ressources MySQL machine virtuelle

À présent que vous avez préparé le contrôle du concept pile Azure avec les certificats nécessaires et les éléments marketplace, vous pouvez déployer un fournisseur de ressources de SQL Server. Cliquez sur l’onglet **fournisseur déployer MySQL** pour :

   - Indiquez les valeurs dans un fichier JSON qui fait référence au processus de déploiement
   - Déployer le fournisseur de ressources
   - Mettre à jour le DNS local
   - Enregistrer la carte de fournisseur de ressources SQL Server

### <a name="provide-values-in-the-json-file"></a>Indiquez les valeurs dans le fichier JSON

Cliquez sur **Microsoft.MySqlprovider.Parameters.JSON**. Ce fichier comprend les paramètres que le modèle de gestionnaire de ressources Azure doit déployer correctement à pile Azure.

1. Remplissez les paramètres dans le fichier JSON **vide** :

    - Vérifiez que vous fournissez la **adminusername** et **adminpassword** pour le fournisseur de ressources MySQL machine virtuelle.

    - Vérifiez que vous fournissez le mot de passe pour le paramètre **SetupPfxPassword** que vous avez apportées à une note de l’étape [préparer composants requis](#prepare-prerequisites) .

    - Vérifiez que vous fournissez paramètres **basicAuthUserName** et **basicAuthPassword** . **Prenez note des valeurs suivantes.** Vous aurez besoin ultérieurement pour enregistrer le fournisseur de ressources.

2. Cliquez sur **Enregistrer**.

### <a name="deploy-the-resource-provider"></a>Déployer le fournisseur de ressources

1. Cliquez sur l’onglet **déployer-Microsoft.Mysql-provider.PS1** et exécuter le script.
2. Tapez le nom de votre client dans Azure Active Directory lorsque vous y êtes invité.
3. Dans la fenêtre contextuelle, envoyez vos informations d’identification d’administration de pile Azure service.

La mise en œuvre complète peut prendre entre 15 et 45 minutes sur certains validation des conceptions pile Azure très utilisées.

### <a name="update-the-local-dns"></a>Mettre à jour le DNS local

1. Cliquez sur l’onglet **Registre-Microsoft.MySQL-fqdn.ps1** et exécuter le script.
2. Lorsque vous êtes invité de l’ID de client Azure Active Directory, entrez votre nom de domaine complet du client Azure Active Directory : par exemple, **microsoftazurestack.onmicrosoft.com**.

### <a name="register-the-sql-rp-resource-provider"></a>Enregistrer le fournisseur de ressources RP SQL

1. Cliquez sur l’onglet **Registre-Microsoft.My-provider.ps1** et exécuter le script.

2. Lorsque vous êtes invité vos informations d’identification, utilisez ce que vous avez noté que les paramètres **basicAuthUserName** et **basicAuthPassword** .

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Vérifier le déploiement à l’aide du portail de pile Azure

1. Se déconnecter de le ClientVM et vous reconnecter en tant que **AzureStack\User**.

2. Sur le bureau, cliquez sur **Le portail Azure pile du contact** et se connecter au portail en tant que l’administrateur de service.

3. Vérifiez que le déploiement a réussi. Cliquez sur **Parcourir** &gt; **Groupes de ressources**, cliquez sur le groupe de ressources que vous avez utilisé (valeur par défaut est **MySQLRP**), puis vérifiez que le composant essentials de la cuillère (moitié supérieure) lit **déploiement a réussi**.


4. Vérifiez que l’enregistrement a réussi. Cliquez sur **Parcourir** &gt; **fournisseurs de ressources**, puis recherchez les **MySQL Local**.

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Créer votre première base de données MySQL pour tester votre déploiement

1. Connectez-vous au portail Azure pile du contact en tant qu’administrateur de service.

2. Cliquez sur le **+** bouton &gt; **personnalisé** &gt; **serveur MySQL et bases de données**.

3. Renseignez le formulaire avec des détails de la base de données.

    **Notez le « nom du serveur « vous entrez.** La chaîne de connexion pour votre base de données inclut le nom du serveur « » dans le cadre du nom d’utilisateur : par exemple, ** "user@ <ServerName>»**. Vous devrez entrer un nom d’utilisateur dans ce format lorsque vous vous connectez à la base de données : par exemple, lorsque vous déployez un site web MySQL en utilisant le fournisseur de ressources de Site Web Azure


## <a name="next-steps"></a>Étapes suivantes

Essayez d’autres [services PaaS](azure-stack-tools-paas-services.md) tels que le [fournisseur de ressources de SQL Server](azure-stack-sql-rp-deploy-short.md) et le [fournisseur de ressources des applications Web](azure-stack-webapps-deploy.md).
