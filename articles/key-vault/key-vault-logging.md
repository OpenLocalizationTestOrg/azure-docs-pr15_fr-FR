<properties
    pageTitle="Journalisation de l’archivage sécurisé clés Azure | Microsoft Azure"
    description="Utilisez ce didacticiel pour vous aider à commencer à utiliser l’archivage sécurisé de clé Azure journalisation."
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
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Journalisation de l’archivage sécurisé clés Azure #
L’archivage sécurisé clé Azure est disponible dans la plupart des régions. Pour plus d’informations, voir la [clé de l’archivage sécurisé tarifs page](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction  
Après avoir créé un ou plusieurs chambres fortes clés, vous souhaiterez probablement contrôler quand et comment votre clés chambres fortes sont accessibles et par qui. Pour cela, vous pouvez activer l’enregistrement pour l’archivage sécurisé clé, qui enregistre les informations dans un compte de stockage Azure que vous spécifiez. Un nouveau conteneur nommé **perspectives-journaux-auditevent** est automatiquement créé pour votre compte de stockage spécifié, et vous pouvez utiliser ce même compte de stockage pour la collecte des journaux pour plusieurs chambres fortes clés.

Vous pouvez accéder à vos informations de journalisation au maximum, 10 minutes après la clé vault opération. Dans la plupart des cas, il sera plus rapide que cela.  C’est à vous permet de gérer vos journaux dans votre compte de stockage :

- Méthodes de contrôle d’accès Azure standard permet de Sécurisez vos journaux en limitant y accéder.
- Supprimer les journaux à conserver dans votre compte de stockage.

Utilisez ce didacticiel pour vous aider à commencer à utiliser l’archivage sécurisé de clé Azure journalisation pour créer votre compte de stockage, activez la journalisation et interpréter les informations de journalisation collectées.  


>[AZURE.NOTE]  Ce didacticiel n’inclut pas les instructions pour la création de chambres fortes clés, des clés ou secrets. Pour obtenir ces informations, voir [prise en main l’archivage sécurisé de clé Azure](key-vault-get-started.md). Ou, pour obtenir des instructions de l’Interface de ligne de commande de disponibilité sur plusieurs plateformes, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).
>
>Pour l’instant, vous ne pouvez pas configurer l’archivage sécurisé de clé Azure dans le portail Azure. Au lieu de cela, suivez ces instructions PowerShell Azure.

Les journaux que vous pouvez collecter terminées peuvent être visualisées à l’aide d’analytique journal à partir de la Suite de gestion des opérations. Pour plus d’informations, voir [l’archivage sécurisé clé Azure (Preview) solution dans journal Analytique](../log-analytics/log-analytics-azure-key-vault.md).

Pour plus d’informations générales sur l’archivage sécurisé de clé Azure, consultez [Quel est l’archivage sécurisé de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez disposer des éléments suivants :

- Un existant clés l’archivage sécurisé que vous utilisez.  
- Azure PowerShell, **version minimale de 1.0.1**. Pour installer PowerShell Azure et associer à votre abonnement Azure, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version, à partir de la console PowerShell Azure, tapez `(Get-Module azure -ListAvailable).Version`.  
- Espace de stockage suffisant sur Azure pour les journaux de l’archivage sécurisé clé.


## <a id="connect"></a>Se connecter à vos abonnements ##

Démarrer une session PowerShell Azure et se connecter à votre compte Azure avec la commande suivante :  

    Login-AzureRmAccount

Dans la fenêtre du navigateur contextuel, entrez votre nom d’utilisateur compte Azure et le mot de passe. PowerShell Azure peuvent accéder à tous les abonnements qui sont associés à ce compte et par défaut, utilise la première.

Si vous avez plusieurs abonnements, vous devrez peut-être spécifier un spécifique ayant servi à créer votre l’archivage sécurisé de clé Azure. Tapez ce qui suit pour afficher les abonnements pour votre compte :

    Get-AzureRmSubscription

Ensuite, pour indiquer l’abonnement associée à votre l’archivage sécurisé clé que se connectent, tapez :

    Set-AzureRmContext -SubscriptionId <subscription ID>

Pour plus d’informations sur la configuration Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Créer un nouveau compte de stockage pour vos journaux ##

Bien que vous pouvez utiliser un compte de stockage existant pour les journaux, nous allons créer un nouveau compte de stockage sera consacré à l’archivage sécurisé clé journaux. Pour faciliter la tâche pour quand nous devons spécifier au plus tard, nous allons stocker les détails dans une variable nommée **sa**.

Pour faciliter la gestion des supplémentaire, nous utiliserons également le même groupe de ressources que celle qui contient notre l’archivage sécurisé clé. Dans le [didacticiel de mise en route](key-vault-get-started.md), ce groupe de ressources est nommé **ContosoResourceGroup** et nous allons continuer à utiliser l’emplacement de l’Asie de l’est. Remplacez ces valeurs pour vos propres, selon le cas :

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Si vous décidez d’utiliser un compte de stockage existant, il doit utiliser le même abonnement comme votre l’archivage sécurisé clé et il doit utiliser le modèle de déploiement du Gestionnaire de ressources, plutôt que le modèle de déploiement classique.

## <a id="identify"></a>Identifier l’archivage sécurisé clé pour les journaux ##

Dans notre didacticiel mise en route mise en route, notre nom de l’archivage sécurisé clés était **ContosoKeyVault**, afin que nous allons continuer à utiliser ce nom et stocker les détails dans une variable nommée **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Activer la journalisation ##

Pour activer la journalisation pour l’archivage sécurisé clé, nous allons utiliser l’applet de commande Set-AzureRmDiagnosticSetting, ainsi que les variables que nous avons créé pour notre nouveau compte de stockage et notre l’archivage sécurisé clé. Nous allons également définir la **-activé** marquer sur **$true** et définir la catégorie à AuditEvent (la seule catégorie pour l’ouverture de l’archivage sécurisé clé), :


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

Le résultat pour cela inclut :

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Cela permet de confirmer que l’enregistrement est désormais activé pour votre l’archivage sécurisé clé, l’enregistrement des informations à votre compte de stockage.

Vous pouvez également vous pouvez également définir une stratégie de rétention pour les journaux, tels que les anciens journaux sont automatiquement supprimés. Par exemple, définir une stratégie de rétention à l’aide de marque **- RetentionEnabled** pour **$true** et valeur **- RetentionInDays** paramètre **90** afin que les journaux antérieurs à 90 jours sont automatiquement supprimés.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Ce qui est enregistré :

- Toutes les demandes d’API REST authentifiés sont enregistrés, qui inclut les requêtes a échoué en raison d’autorisations d’accès, erreurs système ou des requêtes incorrectes.
- Opérations sur la touche vault lui-même, qui inclut la création, suppression, stratégies d’accès de l’archivage sécurisé clés de paramètre, et mise à jour des attributs de l’archivage sécurisé clés tels que des balises.
- Opérations sur les touches et secrets dans l’archivage sécurisé clé, qui inclut la création, la modification ou suppression des ces touches ou secrets ; opérations, telles que signe, vérifier, chiffrer, déchiffrer, renvoyer à la ligne et renvoi clés, récupérer secrets, liste touches et secrets ainsi que leurs versions.
- Demandes non authentifiés qui génèrent une réponse 401. Par exemple, les requêtes qui n’ont pas un jeton porteur ou sont incorrects ou obsolètes ont un jeton non valide.  


## <a id="access"></a>Accéder à vos journaux ##

Journaux de l’archivage sécurisé clés sont stockés dans le conteneur de **perspectives-journaux-auditevent** dans le compte de stockage que vous avez fourni. Pour répertorier tous les objets BLOB dans ce conteneur, tapez :

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Le résultat ressemblera doit ressembler à ceci :

**Conteneur Uri : https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Nom**

**----**

**resourceId = / abonnements/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fournisseurs/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId = / abonnements/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fournisseurs/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

** resourceId = / abonnements/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fournisseurs/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Comme vous pouvez le constater à partir de ce résultat, les objets BLOB suivent une convention d’appellation : **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Les valeurs de date et heure utilisent UTC.

Étant donné que le même compte de stockage peut être utilisé pour collecter les journaux de plusieurs ressources, l’ID de ressource complète dans le nom d’objets blob est très utile pour accéder ou télécharger uniquement les objets BLOB dont vous avez besoin. Mais avant cela, nous aborderons tout d’abord comment télécharger tous les objets BLOB.

Tout d’abord, créez un dossier pour télécharger les objets BLOB. Par exemple :

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Puis obtenir une liste de tous les objets BLOB :  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Canal cette liste via 'Get-AzureStorageBlobContent » pour télécharger les objets BLOB dans notre dossier de destination :

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Lorsque vous exécutez cette commande deuxième, la **/** délimiteur dans les noms d’objets blob créer une structure de dossiers complète sous le dossier de destination, et cette structure sera utilisée pour télécharger et stocker les objets BLOB en tant que fichiers.

Pour télécharger sélectivement des objets BLOB, utiliser des caractères génériques. Par exemple :

- Si vous avez plusieurs chambres fortes clés et que vous souhaitez télécharger les journaux de l’archivage sécurisé clé seul, nommé CONTOSOKEYVAULT3 :

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Si vous avez plusieurs groupes de ressources et que vous souhaitez télécharger les journaux de groupe d’une ressources, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Si vous voulez télécharger tous les journaux pour le mois de janvier 2016, utilisez `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Vous êtes maintenant prêt à démarrer consultant Nouveautés dans les journaux. Mais avant de passer à qui, deux paramètres supplémentaires pour Get-AzureRmDiagnosticSetting vous devrez peut-être savoir :

- Demander l’état des paramètres de diagnostic pour la ressource de l’archivage sécurisé clé :`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Pour désactiver la journalisation pour la ressource de l’archivage sécurisé clé :`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpréter les journaux de l’archivage sécurisé clé ##

Objets BLOB individuels stockés en tant que texte, mis en forme comme un objet blob JSON. Il s’agit d’un exemple d’entrée de journal exécution `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


Le tableau suivant répertorie les noms de champs et les descriptions.


| Nom de champ        | Description |
| ------------- |-------------|
| heure      | Date et heure (UTC).|
| resourceId      | ID de Azure ressource Gestionnaire de ressources. Pour les journaux de l’archivage sécurisé clé, il s’agit toujours l’ID de ressource clé l’archivage sécurisé.|
| NomOpération      | Nom de l’opération, comme indiqué dans le tableau suivant.|
| operationVersion      | Il s’agit de la version de l’API REST demandée par le client.|
| catégorie      | Pour les journaux de l’archivage sécurisé clé, AuditEvent est la valeur unique et disponible.|
| resultType      | Résultat de la demande de l’API REST.|
| resultSignature      | État HTTP.|
| resultDescription     | Description supplémentaire sur le résultat, lorsqu’elles sont disponibles.|
| durationMs      | Durée que nécessaire pour répondre à la demande de l’API REST, en millisecondes. Cela n’inclut pas la latence du réseau, afin que le temps de que vous mesurez côté client ne peut pas correspondent à cette fois.|
| callerIpAddress      | Adresse IP du client qui a effectué la demande.|
| ID de corrélation      | GUID facultatif qui peut s’écouler le client pour faire correspondre les journaux côté client avec les journaux côté service (touche l’archivage sécurisé).|
| identité      | Identité à partir du jeton qui a été présentée lors de la demande de l’API REST. Il s’agit généralement un « utilisateurs », « principal de service » ou une combinaison « utilisateur + identificateur » comme dans les cas d’une requête qui résulte de l’une applet de commande PowerShell Azure.|
| propriétés      | Ce champ contient des informations différentes en fonction de l’opération (NomOpération). Dans la plupart des cas, contient des informations sur le client (chaîne agent utilisateur transmise par le client), l’exacte API REST URI de la demande et le code d’état HTTP. En outre, quand un objet est renvoyé à la suite d’une demande (par exemple, KeyCreate ou VaultGet) il contiendra également la clé URI (tel que « id »), l’archivage sécurisé URI ou URI Secret.|




Les valeurs de champ **NomOpération** sont disponibles au format ObjectVerb. Par exemple :

- Toutes les opérations de l’archivage sécurisé clés ont la ' l’archivage sécurisé`<action>`' mettre en forme, telles que `VaultGet` et `VaultCreate`.

- Toutes les opérations clées ont la « clé`<action>`' mettre en forme, telles que `KeySign` et `KeyList`.

- Toutes les opérations secrètes ont la « Secret`<action>`' mettre en forme, telles que `SecretGet` et `SecretListVersions`.

Le tableau suivant répertorie les NomOpération et commande API REST correspondante.

| NomOpération        | Commande de l’API REST |
| ------------- |-------------|
| Authentification      | Via le point de terminaison Azure Active Directory|
| VaultGet      | [Obtenir des informations sur un archivage sécurisé clé](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Créer ou mettre à jour un archivage sécurisé clé](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Supprimer un archivage sécurisé clé](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Mettre à jour un archivage sécurisé clé](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Liste toutes les chambres fortes clés dans un groupe de ressources](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Créer une clé](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Obtenir des informations sur une clé](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importer une clé dans une archivage sécurisé](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Une clé de sauvegarde](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Supprimer une clé](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Restaurer une clé](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Signer avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Vérifier avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Renvoyer à la ligne d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Renvoi d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Chiffrer avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Déchiffrer avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Mettre à jour une clé](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Répertorier les clés dans un archivage sécurisé](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [Répertorier les versions d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Créer un code secret](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Obtenir secret](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Mettre à jour un code secret](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Supprimer un code secret](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Secrets de liste dans un archivage sécurisé](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Liste des versions d’un code secret](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Étapes suivantes ##

Pour un didacticiel qui utilise l’archivage sécurisé de clé Azure dans une application web, voir [L’archivage sécurisé utilisation Azure clé à partir d’une Application Web](key-vault-use-from-web-application.md).

Pour les références de programmation, voir [guide du développeur de l’archivage sécurisé de clé Azure](key-vault-developers-guide.md).

Pour une liste des applets de commande PowerShell Azure 1.0 pour l’archivage sécurisé de clé Azure, voir [Applets de commande Azure clé l’archivage sécurisé](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Pour un didacticiel sur la rotation des clés et journal d’audit avec l’archivage sécurisé de clé Azure, voir [comment le programme d’installation l’archivage sécurisé clé avec rotation des clés de bout en bout et d’audit](key-vault-key-rotation-log-monitoring.md).
