<properties
    pageTitle="Prise en main l’archivage sécurisé de clé Azure | Microsoft Azure"
    description="Utilisez ce didacticiel pour vous aider à commencer à utiliser l’archivage sécurisé de clé Azure pour créer un conteneur renforcé dans Azure, stocker et gérer des clés de chiffrement et secrets dans Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Prise en main l’archivage sécurisé de clé Azure #
L’archivage sécurisé clé Azure est disponible dans la plupart des régions. Pour plus d’informations, voir la [clé de l’archivage sécurisé tarifs page](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction  
Utilisez ce didacticiel pour vous aider à commencer à utiliser l’archivage sécurisé de clé Azure pour créer un conteneur renforcé (un archivage sécurisé) dans Azure, stocker et gérer des clés de chiffrement et secrets dans Azure. Il vous guide dans le processus de l’utilisation de PowerShell Azure pour créer un archivage sécurisé qui contient une clé ou un mot de passe que vous pouvez ensuite utiliser avec une application Azure. Il vous montre ensuite comment une application peut utiliser cette touche ou le mot de passe.

**Durée estimée d’exécution :** 20 minutes

>[AZURE.NOTE]  Ce didacticiel n’inclut pas les instructions pour l’écriture de l’application Azure que l’une des étapes comprend, à savoir comment autoriser une application pour utiliser une clé ou un code secret dans l’archivage sécurisé clé.
>
>Ce didacticiel utilise Azure PowerShell. Pour obtenir des instructions de l’Interface de ligne de commande de disponibilité sur plusieurs plateformes, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).

Pour plus d’informations générales sur l’archivage sécurisé de clé Azure, consultez [Quel est l’archivage sécurisé de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez disposer des éléments suivants :

- Un abonnement à Microsoft Azure. Si vous n’avez pas une, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Azure PowerShell, **version minimale de 1.1.0**. Pour installer PowerShell Azure et associer à votre abonnement Azure, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version, à partir de la console PowerShell Azure, tapez `(Get-Module azure -ListAvailable).Version`. Lorsque vous avez Azure PowerShell version 0.9.1 via 0.9.8 installé, vous pouvez toujours utiliser ce didacticiel avec certaines modifications mineures. Par exemple, vous devez utiliser le `Switch-AzureMode AzureResourceManager` commande et parmi les commandes de l’archivage sécurisé de clé Azure ont été modifiés. Pour obtenir la liste des applets de commande l’archivage sécurisé clé pour les versions 0.9.1 via 0.9.8, voir [Applets de commande Azure clé l’archivage sécurisé](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Une application qui sera configurée pour utiliser la clé ou le mot de passe que vous créez dans ce didacticiel. Un exemple d’application est disponible à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Pour plus d’informations, voir le fichier Lisez-moi accompagne.


Ce didacticiel est conçu pour les débutants Azure PowerShell, mais elle suppose que vous comprenez les concepts de base, telles que les modules, les applets de commande et les sessions. Pour plus d’informations, voir [prise en main de Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Pour obtenir une aide détaillée sur une applet de commande que vous voyez dans ce didacticiel, utilisez l’applet de commande **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide de l’applet de commande de **Connexion AzureRmAccount** , tapez :

    Get-Help Login-AzureRmAccount -Detailed

Vous pouvez également consulter les didacticiels suivants pour vous familiariser avec le Gestionnaire de ressources Azure dans Azure PowerShell :

- [Comment installer et configurer PowerShell Azure](../powershell-install-configure.md)
- [À l’aide de PowerShell Azure avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Se connecter à vos abonnements ##

Démarrer une session PowerShell Azure et se connecter à votre compte Azure avec la commande suivante :  

    Login-AzureRmAccount 

Notez que si vous utilisez une instance spécifique d’utilisation Azure, par exemple, Azure pour le gouvernement, le - paramètre environnement avec cette commande. Par exemple :`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Dans la fenêtre du navigateur contextuel, entrez votre nom d’utilisateur compte Azure et le mot de passe. PowerShell Azure Obtient tous les abonnements qui sont associés à ce compte et par défaut, utilise la première.

Si vous avez plusieurs abonnements et que vous souhaitez spécifier un spécifique à utiliser pour l’archivage sécurisé de clé Azure, tapez ce qui suit pour afficher les abonnements pour votre compte :

    Get-AzureRmSubscription

Ensuite, pour indiquer l’abonnement à utiliser, tapez :

    Set-AzureRmContext -SubscriptionId <subscription ID>

Pour plus d’informations sur la configuration Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Créer un nouveau groupe de ressources ##

Lorsque vous utilisez le Gestionnaire de ressources Azure, toutes les ressources associées sont créés à l’intérieur d’un groupe de ressources. Nous allons créer un nouveau groupe de ressources nommé **ContosoResourceGroup** pour ce didacticiel :

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Créer un archivage sécurisé clé ##

Utiliser l’applet de commande [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) pour créer un archivage sécurisé clé. Cette applet de commande a trois paramètres obligatoires : un **nom de groupe de ressources**, un **nom de l’archivage sécurisé clés**et l' **emplacement géographique**.

Par exemple, si vous utilisez le nom de l’archivage sécurisé de **ContosoKeyVault**, le nom du groupe de ressources de **ContosoResourceGroup**et l’emplacement de l' **Asie de l’est**, tapez :

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Le résultat de cette applet de commande affiche les propriétés de l’archivage sécurisé clé que vous venez de créer. Les deux propriétés les plus importantes sont :

- **Nom de l’archivage sécurisé**: dans l’exemple, il s’agit de **ContosoKeyVault**. Vous allez utiliser ce nom pour d’autres applets de commande de l’archivage sécurisé clé.
- **URI de l’archivage sécurisé**: dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net/. Applications qui utilisent votre l’archivage sécurisé via son API REST doivent utiliser cet URI.

Votre compte Azure est maintenant autorisé à effectuer les opérations sur cette archivage sécurisé clé. Personne d’autre n’est encore.

>[AZURE.NOTE]  Si vous voyez l’erreur **l’abonnement n’est pas enregistré pour utiliser l’espace de noms « Microsoft.KeyVault »** lorsque vous essayez de créer votre l’archivage sécurisé clé nouveau, puis exécutez `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` puis exécutez à nouveau votre commande New-AzureRmKeyVault. Pour plus d’informations, voir [Registre AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Ajouter une clé ou un code secret à l’archivage sécurisé clé ##

Si vous souhaitez que l’archivage sécurisé de clé Azure pour créer une clé de logiciel protégé pour vous, utilisez l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) et tapez les informations suivantes :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Toutefois, si vous avez une clé de logiciel protégé existante dans un. Fichier PFX enregistré sur votre disque C:\ dans un fichier nommé softkey.pfx que vous voulez télécharger dans l’archivage sécurisé Azure clé, tapez la commande suivante pour définir la variable **securepfxpwd** un mot de passe de **123** pour le. Fichier PFX :

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Tapez la commande suivante pour importer la clé à partir de la. Fichier PFX, qui permet de protéger la clé par le logiciel dans le service de l’archivage sécurisé clé :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Vous pouvez désormais faire référence à cette clé que vous avez créé ou téléchargé dans l’archivage sécurisé Azure clé, à l’aide de son URI. **Https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** permet de toujours télécharger la version actuelle, puis **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** permet d’obtenir cette version spécifique.  

Pour afficher l’URI pour cette clé, tapez :

    $Key.key.kid

Pour ajouter un code secret à l’archivage sécurisé, qui est un mot de passe nommé SQLPassword et a la valeur Pa$ $w0rd et l’archivage sécurisé de clé Azure, tout d’abord convertir la valeur de Pa$ $w0rd une chaîne sécurisée en tapant ce qui suit :

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Ensuite, tapez les informations suivantes :

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Vous pouvez désormais faire référence à ce mot de passe que vous avez ajouté à l’archivage sécurisé Azure clé, à l’aide de son URI. **Https://ContosoVault.vault.azure.net/secrets/SQLPassword** permet de toujours télécharger la version actuelle, puis **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** permet d’obtenir cette version spécifique.

Pour afficher l’URI de ce mot de passe, tapez :

    $secret.Id

Examinons la clé ou le mot de passe que vous venez de créer :

- Pour afficher votre clé, tapez :`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Pour afficher votre code secret, tapez :`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Votre clé et l’archivage sécurisé clé ou le secret est désormais prêt pour les applications à utiliser. Vous devez autoriser les applications à utiliser ces.  

## <a id="register"></a>Enregistrer une application avec Azure Active Directory ##

Cette étape est généralement être effectuée par un développeur, sur un autre ordinateur. Il n’est pas spécifique à l’archivage sécurisé de clé Azure, mais il est inclus ici pour plus de précision.


>[AZURE.IMPORTANT] Pour terminer le didacticiel, votre compte, l’archivage sécurisé et l’application que vous inscrivez dans cette étape doivent être tous dans le même répertoire Azure.

Les applications qui utilisent un archivage sécurisé clé doivent s’authentifier à l’aide d’un jeton d’Azure Active Directory. Pour ce faire, le propriétaire de l’application doit d’abord inscrire l’application dans leur Azure Active Directory. À la fin de l’enregistrement, le propriétaire de l’application récupère les valeurs suivantes :


- Un **ID de l’Application** (également appelé ID de Client) et une **clé d’authentification** (également connu sous le secret partagé). L’application doit présenter ces deux valeurs à Azure Active Directory, afin d’obtenir un jeton. Comment l’application est configurée pour cela dépend de l’application. Pour l’application d’exemple de l’archivage sécurisé clé, le propriétaire de l’application définit les valeurs suivantes dans le fichier app.config.

Pour enregistrer l’application dans Azure Active Directory :

1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur **Active Directory**et sélectionnez le répertoire dans lequel enregistré votre application. <br> <br> **Remarque :** Vous devez sélectionner le même répertoire qui contient l’abonnement Azure avec lequel vous avez créé votre l’archivage sécurisé clé. Si vous ne savez pas quel répertoire cela est, cliquez sur **paramètres**, identifier l’abonnement auquel vous avez créé votre l’archivage sécurisé clé et notez le nom de l’annuaire affichée dans la dernière colonne.

3. Cliquez sur **APPLICATIONS**. Si aucun applications n’ont été ajoutées à votre répertoire, cette page affiche uniquement le lien **Ajouter une application** . Cliquez sur le lien, ou par ailleurs, vous pouvez cliquer sur **Ajouter** dans la barre de commandes.
4.  Dans l’Assistant **Ajouter une APPLICATION** , sur le **que voulez-vous faire ?** page, cliquez sur **Ajouter une application de développe de mon organisation**.
5.  Dans la page **dites-nous relatives à votre application** , spécifiez un nom pour votre application et sélectionnez **WEB APPLICATION et/ou API WEB** (par défaut). Cliquez sur l’icône **suivante** .
6.  Dans la page **Propriétés de l’application** , spécifiez **l’URL de session** et **Application ID URI** pour votre application web. Si votre application n’a pas ces valeurs, vous pouvez les faire pour cette étape (par exemple, vous pouvez spécifier http://test1.contoso.com pour les deux zones). S’il existe ces sites n’a pas d’importance. Important est que l’application URI ID pour chaque application est différente pour chaque application dans votre annuaire. Le répertoire utilise cette chaîne pour identifier votre application.
7.  Cliquez sur l’icône **terminé** pour enregistrer vos modifications dans l’Assistant.
8.  Dans la page de **Démarrage rapide** , cliquez sur **configurer**.
9.  Faites défiler jusqu'à la section **clés** , sélectionnez la durée, puis cliquez sur **Enregistrer**. La page actualise et affiche désormais une valeur de clé. Vous devez configurer votre application avec cette valeur de clé et la valeur **Réf CLIENT** . (Les instructions pour cette configuration sont spécifiques à l’application).
10. Copiez la valeur d’ID client à partir de cette page, vous allez utiliser dans l’étape suivante pour définir les autorisations sur votre l’archivage sécurisé.

## <a id="authorize"></a>Autorisez l’application à utiliser la clé ou le code secret ##

Pour autoriser l’application pour accéder à la clé ou secret dans l’archivage sécurisé, utilisez l’applet de commande  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) .

Par exemple, si votre nom de l’archivage sécurisé est **ContosoKeyVault** et l’application que vous souhaitez autoriser possède un ID de client de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, et que vous voulez autoriser l’application à déchiffrer et connectez-vous avec touches dans votre l’archivage sécurisé, exécutez la commande suivante :


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si vous souhaitez autoriser cette application même à lire secrets dans votre l’archivage sécurisé, exécutez la commande suivante :


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Si vous souhaitez utiliser un module de sécurité matériel (HSM) ##

Pour assurance ajouté, vous pouvez importer ou générer des clés dans les modules de sécurité matérielle (HSM) qui ne laissez jamais la limite HSM. Les modules HSM sont FIPS 140-2 niveau 2 validées. Si cette exigence ne s’applique pas à vous, ignorez cette section et accédez à [Supprimer l’archivage sécurisé clé et clés et secrets associés](#delete).

Pour créer ces touches protégées par HSM, vous devez utiliser le [niveau de service Azure clé l’archivage sécurisé Premium pour prendre en charge des clés protégées par HSM](https://azure.microsoft.com/pricing/free-trial/). En outre, notez que cette fonctionnalité n’est pas disponible pour la Chine Azure.


Lorsque vous créez l’archivage sécurisé clé, ajoutez le paramètre **- Référence (SKU)** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Vous pouvez ajouter des clés protégées par un logiciel (comme indiqué précédemment) et protégées par HSM à cette archivage sécurisé clé. Pour créer une clé protégées par HSM, définissez la **-Destination** paramètre « HSM » :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un. Fichier PFX sur votre ordinateur. Cette commande permet d’importer la clé dans HSM dans le service de l’archivage sécurisé clé :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


La commande suivante importe un « fournir votre propre clé » package (BYOK). Ce scénario vous permet de générer votre clé dans votre HSM local et transférer vers HSM dans le service de l’archivage sécurisé clé, sans la clé de quitter la limite HSM :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Pour obtenir des instructions plus détaillées sur la façon de générer ce package BYOK, voir [comment générer et transfert protégées par HSM clés pour l’archivage sécurisé de clé Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Supprimer l’archivage sécurisé clé et des clés associées et secrets ##

Si vous n’avez plus besoin l’archivage sécurisé clé et la clé ou le secret qu’il contient, vous pouvez supprimer l’archivage sécurisé clé à l’aide de l’applet de commande [Supprimer AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) :

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Ou bien, vous pouvez supprimer un groupe de ressources Azure entière, qui inclut l’archivage sécurisé clé et autres ressources que vous avez inclus dans ce groupe :

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Autres applets de commande PowerShell Azure ##

Autres commandes qui peuvent être utiles pour la gestion de l’archivage sécurisé de Azure clé :

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Cette commande permet d’obtenir un affichage de toutes les clés et propriétés sélectionnées tabulaire.
- `$Keys[0]`: Cette commande affiche une liste complète des propriétés de la clé spécifiée
- `Get-AzureKeyVaultSecret`: Cette commande répertorie un affichage tabulaire de tous les noms secrets et propriétés sélectionnées.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exemple comment supprimer une clé spécifique.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exemple comment supprimer un secret spécifique.


## <a id="next"></a>Étapes suivantes ##

Pour un didacticiel suivi qui utilise l’archivage sécurisé de clé Azure dans une application web, voir [L’archivage sécurisé utilisation Azure clé à partir d’une Application Web](key-vault-use-from-web-application.md).

Pour voir comment votre l’archivage sécurisé clé est utilisé, consultez [Enregistrement de Azure clé l’archivage sécurisé](key-vault-logging.md).

Pour obtenir la liste des dernières applets de commande PowerShell Azure pour l’archivage sécurisé de clé Azure, voir [Applets de commande Azure clé l’archivage sécurisé](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Pour les références de programmation, voir [guide du développeur de l’archivage sécurisé de clé Azure](key-vault-developers-guide.md).
