<properties
    pageTitle="Sécuriser votre l’archivage sécurisé clé | Microsoft Azure"
    description="Gérer les autorisations d’accès de l’archivage sécurisé clé pour la gestion des chambres fortes et clés et secrets. Modèle d’autorisation et d’authentification pour l’archivage sécurisé clé et comment sécuriser votre l’archivage sécurisé clé"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>Sécuriser votre l’archivage sécurisé clé

L’archivage sécurisé clé Azure est un service de nuage qui met à l’abri des clés de chiffrement et codes secrets (par exemple, les certificats, chaînes de connexion, les mots de passe) pour vos applications cloud. Dans la mesure où ces données sont sensibles et critiques, que vous souhaitez sécuriser l’accès à votre clés chambres fortes afin que seuls les autorisés applications et les utilisateurs peuvent accéder à l’archivage sécurisé clé. Cet article fournit une vue d’ensemble du modèle d’accès aux clés de l’archivage sécurisé, explique authentification et autorisation et explique comment sécuriser l’accès à l’archivage sécurisé clé pour vos applications cloud avec un exemple.

## <a name="overview"></a>Vue d’ensemble

Accès à un archivage sécurisé clé est contrôlée via deux interfaces distinctes : plan de gestion et plan de données. Pour les deux plans d’autorisation et d’authentification appropriée est nécessaire pour un appelant (un utilisateur ou une application) peut accéder à l’archivage sécurisé clé. Authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que l’appelant est autorisé à exécuter.

Authentification plan de gestion et plan de données utilisent Azure Active Directory. Pour l’autorisation, plan de gestion des utilise le contrôle d’accès basé sur un rôle (RBAC) tandis que plan de données utilise la stratégie d’accès de l’archivage sécurisé clés.

Voici un bref aperçu des thèmes abordés :

[Authentification à l’aide d’Azure Active Directory](#authentication-using-azure-active-direcrory) - cette section explique comment un appelant authentifie avec Azure Active Directory pour accéder à un archivage sécurisé clé via le plan de gestion et plan de données. 

[Plan de gestion et plan de données](#management-plane-and-data-plane) - plan de gestion et plan de données sont deux plans d’access utilisées pour accéder à votre l’archivage sécurisé clé. Chaque plan access prend en charge des opérations spécifiques. Cette section décrit les points de terminaison access, opérations pris en charge et accéder à la méthode de contrôle utilisée par chaque plan. 

[Contrôle d’accès de plan de gestion des](#management-plane-access-control) - dans cette section, que nous allons aborder permettant d’accéder aux opérations de plan de gestion à l’aide de contrôle d’accès basé sur un rôle.

[Contrôle d’accès de plan de données](#data-plane-access-control) - cette section décrit comment utiliser la stratégie d’accès de l’archivage sécurisé clés pour contrôler l’accès aux données plan.

[Exemple](#example) : cet exemple décrit comment configurer le contrôle d’accès pour votre l’archivage sécurisé clé permettre à trois différentes équipes (équipe de sécurité, les développeurs/opérateurs et auditeurs) pour effectuer des tâches spécifiques pour développer, gérer et surveiller une application dans Azure.


## <a name="authentication-using-azure-active-directory"></a>Authentification à l’aide d’Azure Active Directory

Lorsque vous créez un archivage sécurisé clé dans un abonnement Azure, il est automatiquement associée au client d’Azure Active Directory de l’abonnement. Tous les appelants (utilisateurs et applications) doivent être enregistrés dans ce client à accéder à cette archivage sécurisé clé. Une application ou un utilisateur doit s’authentifier avec Azure Active Directory pour accéder à l’archivage sécurisé clé. Cela s’applique aux deux Gestion plan et plan d’accès au. Dans les deux cas, une application peut accéder à l’archivage sécurisé clé de deux façons :

-  **utilisateur + access application** - cela est généralement pour les applications qui accèdent à l’archivage sécurisé clé au nom d’un utilisateur connecté. PowerShell Azure, portail Azure quelques exemples de ce type d’accès. Il existe deux façons pour accorder l’accès aux utilisateurs : eux consiste à accorder l’accès aux utilisateurs afin qu’ils puissent accéder l’archivage sécurisé clé à partir de n’importe quelle application et l’autre consiste à accorder un accès utilisateur à l’archivage sécurisé clé uniquement lorsqu’ils utilisent une application spécifique (appelée identité composée). 
-  **accès application seule** - pour les applications qui s’exécutent les services de processus, etc. travaux d’arrière-plan. Identité de l’application a accès à l’archivage sécurisé clé.

Dans les deux types d’applications, l’application authentifie avec Azure Active Directory à l’aide de la [prise en charge des méthodes d’authentification](../active-directory/active-directory-authentication-scenarios.md) et acquisition par un jeton. Méthode d’authentification utilisée dépend du type d’application. Ensuite l’application utilise ce jeton et envoie API REST demande l’archivage sécurisé clé. En cas d’accès de plan de gestion des demandes sont routés via le Gestionnaire de ressources Azure le point de terminaison. Lorsque vous accédez à plan de données, la parle applications directement à une clé vault point de terminaison. Afficher plus de détails sur le [flux d’authentification entier](../active-directory/active-directory-protocols-oauth-code.md). 

Le nom de la ressource pour laquelle l’application demande un jeton est différent selon que l’application accède au plan de gestion des ou plan de données. Par conséquent, le nom de la ressource est soit gestion plan ou données plan point de terminaison décrite dans le tableau dans une section ultérieure, selon l’environnement Azure.

Ayant un mécanisme unique pour l’authentification à la gestion et plan de données a ses propres avantages :

- Organisations centralement contrôler l’accès à tous les chambres fortes clés dans leur organisation
- Si un utilisateur quitte, ils instantanément perdent l’accès à tous les chambres fortes clés dans l’organisation
- Les organisations peuvent personnaliser l’authentification via les options dans Azure Active Directory (par exemple, ce qui permet l’authentification multifacteur pour renforcer la sécurité)

## <a name="management-plane-and-data-plane"></a>Plan de gestion et plan de données

L’archivage sécurisé clé Azure est un service Azure disponible via le modèle de déploiement d’Azure le Gestionnaire de ressources. Lorsque vous créez un archivage sécurisé clé, vous obtenez un conteneur virtuel dans lequel vous pouvez créer d’autres objets, tels que les clés, secrets et certificats. Vous accéder à votre l’archivage sécurisé clé à l’aide de plan de gestion et plan de données pour effectuer des opérations spécifiques. Interface de gestion de plan est utilisé pour gérer votre l’archivage sécurisé clé lui-même, telles que la création, suppression, mise à jour des attributs de l’archivage sécurisé clés et définir les stratégies d’accès pour le plan de données. Interface de plan de données est utilisée pour ajouter, supprimer, modifier et utilisez les touches, secrets et certificats stockés dans votre l’archivage sécurisé clé.

Les interfaces de plan Gestion plan et les données sont accessibles via différents points de terminaison (voir tableau). La deuxième colonne du tableau décrit les noms DNS pour ces points de terminaison dans différents environnements Azure. La troisième colonne décrit les opérations que vous pouvez effectuer à partir de chaque plan d’accès. Chaque plan d’accès est également équipé de son propre mécanisme de contrôle d’accès : de contrôle d’accès de plan de gestion des est défini à l’aide de contrôle d’accès Azure Resource Manager Role-Based (RBAC), tandis que vous de contrôle d’accès de plan de données est défini à l’aide de la stratégie d’accès de l’archivage sécurisé clés.

| Plan d’accès | Points de terminaison Access | Opérations | Mécanisme de contrôle d’accès|
|--------------|------------------|--------------------|--------|
| Plan de gestion|**Général :**<br> Management.Azure.com:443<br><br> **Chine Azure :**<br> Management.chinacloudapi.CN:443<br><br> **Azure gouvernement des États-Unis :**<br> Management.usgovcloudapi.NET:443<br><br> **Allemagne Azure :**<br> Management.microsoftazure.de:443 | Archivage sécurisé clé créer/en lecture/mise à jour/supprimer <br> Définir les stratégies d’accès pour l’archivage sécurisé clé<br>Définir des balises pour l’archivage sécurisé clé | Ressource Azure Gestionnaire accès basé sur un rôle contrôle d' |
| Plan de données | **Général :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.azure.net:443<br><br> **Chine Azure :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.azure.cn:443<br><br> **Azure gouvernement des États-Unis :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.usgovcloudapi.net:443<br><br> **Allemagne Azure :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.microsoftazure.de:443 | Pour les clés : Déchiffrer, chiffrer, UnwrapKey, WrapKey, vérifier, se, obtenir, List, mettre à jour, créer, importer, supprimer, sauvegarder, restaurer<br><br> Pour secrets : obtenez, liste, jeu, supprimer | Stratégie d’accès de l’archivage sécurisé clés|

La gestion des données et plan plan accéder aux contrôles travailler de manière indépendante. Par exemple, si vous voulez accorder l’accès d’une application pour utiliser les touches dans un archivage sécurisé clé, vous ne devez accorder des autorisations d’accès de plan données à l’aide de stratégies d’accès de l’archivage sécurisé clés et aucun accès de plan de gestion des est nécessaire pour cette application. En revanche, si vous souhaitez qu’un utilisateur pour pouvoir lire les balises et les propriétés de l’archivage sécurisé, mais a pas accès aux clés, secrets ou certificats, vous pouvez accorder cet utilisateur, '' un accès en lecture à l’aide de RBAC et aucun accès au plan de données n’est requis.

## <a name="management-plane-access-control"></a>Contrôle d’accès de plan de gestion

Le plan de gestion des se compose des opérations qui affectent l’archivage sécurisé clé lui-même. Par exemple, vous pouvez créer ou supprimer un archivage sécurisé clé. Vous pouvez obtenir une liste de chambres fortes dans un abonnement. Vous pouvez récupérer les propriétés de l’archivage sécurisé clé (par exemple, de référence (SKU), balises) et définir l’archivage sécurisé clé les stratégies d’accès qui contrôlent les utilisateurs et les applications pouvant accéder aux clés et secrets dans l’archivage sécurisé clé. Contrôle d’accès de plan de gestion des utilise RBAC. Voir la liste complète des opérations de l’archivage sécurisé clés qui peuvent être effectuées via le plan de gestion dans la table qui précède la section. 

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur un rôle (RBAC)
Chaque abonnement Azure a un Azure Active Directory. Les utilisateurs, des groupes et des applications à partir de ce répertoire peuvent être autorisées à accéder pour gérer les ressources de l’abonnement Azure qui utilisent le modèle de déploiement d’Azure le Gestionnaire de ressources. Ce type de contrôle d’accès est appelé contrôle d’accès basé sur un rôle (RBAC). Pour gérer cette access, vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [Outils Azure infrastructure du langage commun](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou les [API de reste Azure Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Avec le modèle Azure le Gestionnaire de ressources, vous créez votre l’archivage sécurisé clé dans une ressource groupe et contrôler l’accès au plan de gestion de cette archivage sécurisé clé à l’aide de Azure Active Directory. Par exemple, vous pouvez accorder aux utilisateurs ou un groupe d’administrer chambres fortes clés dans un groupe de ressources spécifique.

Vous pouvez accorder l’accès à des utilisateurs, groupes et les applications sur une étendue spécifique en attribuant des rôles RBAC appropriés. Par exemple, pour accorder l’accès à un utilisateur pour gérer les clés chambres fortes, vous devez attribuer un rôle prédéfini « clé l’archivage sécurisé collaboration » à cet utilisateur à une étendue spécifique. L’étendue dans ce cas serait un abonnement, un groupe de ressources ou simplement un spécifique clés l’archivage sécurisé. Un rôle attribué au niveau de l’abonnement s’applique à tous les groupes de ressources et des ressources au sein de cet abonnement. Un rôle attribué au niveau de groupe de ressources s’applique à toutes les ressources dans ce groupe de ressources. Un rôle attribué pour une ressource spécifique s’applique uniquement à cette ressource. Il existe plusieurs rôles prédéfinis (voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md)), et si les rôles prédéfinis ne s’intègrent pas vos besoins vous pouvez également définir vos propres rôles.

>[AZURE.IMPORTANT] Notez que si un utilisateur a collaboration autorisations (RBAC) à un plan de gestion de clés de l’archivage sécurisé, lui s’accordent l’accès aux plan de données, en définissant l’archivage sécurisé clés stratégie d’accès, qui contrôle l’accès au plan de données. Par conséquent, il est recommandé à contrôler étroitement qui accèdent « Collaborateur » à votre clés chambres fortes pour vous assurer seulement les personnes autorisées peuvent consulter et gérer votre clés chambres fortes, les clés, secrets et certificats.

## <a name="data-plane-access-control"></a>Contrôle d’accès de plan de données

Le plan de données de l’archivage sécurisé clés se compose des opérations qui affectent les objets dans une archivage sécurisé clé, tels que clés secrets et certificats.  Cela inclut clé opérations comme créent, importer, les opérations de chiffrement mise à jour, la liste, sauvegarde et restauration clés, tels que se, vérifier, chiffrer, déchiffrer, renvoyer à la ligne, renvoi et définir des balises et autres attributs pour les clés. De même, pour secrets qu'il inclut, obtenez, définissez, liste, supprimer.

Accès aux données plan est accordée en définissant les stratégies d’accès pour un archivage sécurisé clé. Un utilisateur, groupe ou une application doit disposer des autorisations de collaboration (RBAC) pour le plan de gestion pour un archivage sécurisé clé pouvoir définir les stratégies d’accès pour que l’archivage sécurisé clé. Un utilisateur, un groupe ou une application peut être autorisée à accéder pour effectuer des opérations spécifiques pour les clés ou secrets dans un archivage sécurisé clé. l’archivage sécurisé clé prend en charge jusqu'à 16 entrées de stratégie d’accès pour un archivage sécurisé clé. Créer un groupe de sécurité Azure Active Directory et ajouter des utilisateurs à ce groupe pour accorder l’accès aux données plan à plusieurs utilisateurs à un archivage sécurisé clé.

### <a name="key-vault-access-policies"></a>archivage sécurisé clé stratégies d’accès

stratégies d’accès de l’archivage sécurisé clés accordent des autorisations aux clés, secrets et certificats séparément. Par exemple, vous pouvez donner un accès utilisateur aux clés uniquement, mais pas des autorisations pour secrets. Toutefois, les autorisations d’accès aux clés ou secrets ou des certificats sont au niveau de l’archivage sécurisé. En d’autres termes, stratégie d’accès de l’archivage sécurisé clés ne reconnaît pas les autorisations au niveau objet. Vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [Outils Azure infrastructure du langage commun](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou [l’archivage sécurisé clé API REST de gestion de](https://msdn.microsoft.com/library/azure/mt620024.aspx) pour définir les stratégies d’accès pour un archivage sécurisé clé.

>[AZURE.IMPORTANT] Notez que les stratégies d’accès de l’archivage sécurisé clés s’appliquent au niveau de l’archivage sécurisé. Par exemple, lorsqu’un utilisateur est autorisé à créer et supprimer des touches, qu’elle puisse effectuer ces opérations sur toutes les clés dans ce archivage sécurisé clé.

## <a name="example"></a>Exemple

Supposons que vous développez une application qui utilise un certificat SSL, stockage Azure pour le stockage de données et utilise une clé RSA 2048 bits pour les opérations de connexion. Supposons que cette application est exécutée dans une machine virtuelle (ou un ensemble d’échelle de machine virtuelle). Vous pouvez utiliser un archivage sécurisé clé pour stocker les secrets application et utiliser l’archivage sécurisé clé pour stocker le certificat de démarrage qui est utilisé par l’application pour vous authentifier avec Azure Active Directory.

Par conséquent, Voici un résumé de toutes les clés et secrets stocké dans un archivage sécurisé clé.

- **Certificat SSL** - utilisé pour SSL
- **Clé de stockage** - utilisée pour accéder au compte de stockage
- **Clé RSA 2048 bits** - utilisé pour les opérations de connexion
- **Certificat démarrage** - en utilisé pour authentifier à Azure Active Directory, pour accéder à l’archivage sécurisé clé pour extraire la clé de stockage et utiliser la clé RSA pour la signature.

Maintenant nous allons répondre aux personnes qui sont gestion, le déploiement et l’audit de cette application. Nous allons utiliser trois rôles dans cet exemple.

- **Équipe de sécurité** - il s’agit généralement de votre service informatique de « office du responsable de la sécurité (sécurité directeur) » ou équivalent, responsable de la conservation correcte de secrets tels que des certificats SSL, clés RSA utilisés pour la signature, chaînes de connexion pour les bases de données, les clés de compte de stockage.
- **Les développeurs/opérateurs** - il s’agit des personnes qui développement cette application, puis le déployer dans Azure. En règle générale, ils ne font pas partie de l’équipe de sécurité et par conséquent, ils ne doivent pas avoir accès aux données sensibles, telles que des certificats SSL, clés RSA, mais l’application qu'ils déploient doit avoir accès aux utilisateurs.
- **Les auditeurs** - il s’agit généralement d’un autre jeu de personnes, isolés dans les développeurs et général votre service informatique. Leur responsabilité consiste à examiner les conditions d’utilisation et la maintenance des certificats, les clés, etc. et assurer le respect des normes de sécurité des données. 

Il existe un rôle plus se trouvant en dehors de l’étendue de cette application, mais ici pertinent à mentionner, et qui serait l’abonnement (ou le groupe de ressources) administrateur. Administrateur d’abonnement configure les autorisations d’accès initial de l’équipe de sécurité. Ici nous part du principe que l’administrateur d’abonnement a accordé l’accès à l’équipe de sécurité à un groupe de ressources dans les toutes les ressources nécessaires pour résident de cette application.

Maintenant voyons quelles actions chaque rôle effectue dans le cadre de cette application.

- **Équipe de sécurité**
    - Créer des clés chambres fortes
    - Active le clé vault journalisation
    - Ajouter des touches/secrets
    - Créer une sauvegarde des clés de sinistre
    - Définir la stratégie d’accès de l’archivage sécurisé clés pour accorder des autorisations aux utilisateurs et aux applications pour effectuer les opérations
    - Régulièrement pellicule clés/secrets
- **Les développeurs/opérateurs**
    - Obtenir des références au démarrage et des certificats SSL (empreintes), clé de stockage (secret URI) et la clé (clé URI) à partir de l’équipe de sécurité de signature
    - Développer et déployer des applications qui accède à des clés et secrets par programme
- **Auditeurs**
    - Journaux de l’utilisation de révision pour confirmer les conditions d’utilisation clé/secret et respect des normes de sécurité des données

Maintenant voyons quelles autorisations d’accès à l’archivage sécurisé clé requis par chaque rôle (et l’application) pour effectuer les tâches affectées. 

| Rôle d’utilisateur    | Autorisations de plan de gestion | Autorisations de plan de données |
|--------------|------------------------------|------------------------|
|Équipe de sécurité|archivage sécurisé clé collaborateur|Clés : sauvegarder, créer, supprimer, obtenir, importer, list, restaurer <br> Secrets : toutes les|
|Les développeurs/opérateur| l’archivage sécurisé clé déploiement d’autorisation, afin que les ordinateurs virtuels qu’ils déploient pouvant récupérer secrets de l’archivage sécurisé clé | Aucun |
|Auditeurs| Aucun | Clés : liste<br>Secrets : liste|
|Application| Aucun | Touches : signe<br>Secrets : obtenir |

>[AZURE.NOTE] Les auditeurs besoin liste d’autorisation pour les clés et secrets afin qu’ils peuvent inspecter les attributs des clés et des codes secrets ne sont pas émis dans les journaux, tels que des indicateurs, dates d’activation et d’expiration.

Outre les autorisations pour l’archivage sécurisé clé, les trois rôles doivent également accéder à d’autres ressources. Par exemple, pour pouvoir déployer des machines virtuelles (ou Web applications etc.). Les développeurs/opérateurs doivent également accéder de « Collaborateur » à ces types de ressources. Les auditeurs besoin d’y accéder en lecture du compte de stockage où se trouvent les journaux de l’archivage sécurisé clés.

Étant donné que la sécurisation de l’accès à votre l’archivage sécurisé clé est l’objectif de cet article, nous uniquement illustrer les différentes sections relatives à cette rubrique et ignorer des détails concernant le déploiement des certificats, l’accès à des clés et secrets par programme etc.. Ces informations sont déjà traitées ailleurs. Déploiement de certificats stockés dans l’archivage sécurisé clé aux machines virtuelles est couvertes dans un [billet de blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)et [exemples de code](https://www.microsoft.com/download/details.aspx?id=45343) disponibles qui illustre comment utiliser le certificat démarrage s’authentifier à Azure AD pour accéder à l’archivage sécurisé clé.

La plupart des autorisations d’accès peut être accordée à l’aide du portail Azure, mais pour accorder des autorisations granulaires vous devrez peut-être utiliser PowerShell Azure (ou Azure infrastructure du langage commun) pour obtenir le résultat souhaité. 

Part du principe les extraits de PowerShell suivants :

- L’administrateur Azure Active Directory a créé des groupes de sécurité qui représentent les trois rôles, à savoir Contoso sécurité d’équipe, Contoso application Devops, Contoso application Auditors. L’administrateur a également ajouté des utilisateurs aux groupes qu'elles appartiennent.

- **ContosoAppRG** est le groupe de ressources où se trouvent toutes les ressources. **contosologstorage** est où se trouvent les journaux. 

- L’archivage sécurisé clés **ContosoKeyVault** et stockage compte utilisé pour l’archivage sécurisé clés journaux **contosologstorage** doit être au même endroit Azure


Tout d’abord l’administrateur d’abonnement affecte « clé de l’archivage sécurisé collaboration » et les rôles ' utilisateur accès administrateur ' à l’équipe de sécurité. Ainsi, l’équipe de sécurité gérer l’accès à d’autres ressources et gérer des clés chambres fortes dans le groupe de ressources ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Le script suivant illustre comment l’équipe de sécurité peut créer une archivage sécurisé clé, le programme d’installation journalisation et définir des autorisations d’accès pour les autres rôles et l’application. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

Le rôle personnalisé défini, est uniquement être assignée à l’abonnement dans lequel le groupe de ressources ContosoAppRG est créé. Si les mêmes rôles personnalisés doivent être utilisés pour d’autres projets dans les autres abonnements, il est étendue peut avoir plusieurs abonnements ajoutés.

L’attribution de rôle personnalisés pour les développeurs/opérateurs pour l’autorisation « déployer/action » est inclus dans l’étendue du groupe de ressources. Ainsi seulement les ordinateurs virtuels créés dans le groupe de ressources 'ContosoAppRG' obtenez les secrets (certificat SSL et certificat démarrage). Les ordinateurs virtuels qu’un membre de l’équipe de développement/opérations crée dans un autre groupe de ressources ne sera pas en mesure d’obtenir ces secrets même si elles auraient les URI secrète.

Cet exemple illustre un scénario simple. Scénarios réels peuvent être plus complexes et vous devrez peut-être ajuster les autorisations pour votre l’archivage sécurisé clé selon vos besoins. Par exemple, dans notre exemple, nous part du principe que l’équipe sécurité fournira la clé et références confiant (MU et empreintes) que l’équipe les développeurs/opérateurs devez référencer dans leurs applications. Par conséquent, ils ne doivent accorder aux développeurs/opérateurs tout accès de plan de données. Notez également que cet exemple se concentre sur la sécurisation de votre l’archivage sécurisé clé. Similaire il convient de sécuriser [vos ordinateurs virtuels](https://azure.microsoft.com/services/virtual-machines/security/), [comptes de stockage](../storage/storage-security-guide.md) et autres ressources Azure trop.

>[AZURE.NOTE] Remarque : Cet exemple illustre l’archivage sécurisé comment les accès sera verrouillée vers le bas en production. Le développeur doit avoir leur propre abonnement ou resourcegroup lequel ils disposent des autorisations complètes à gérer leur chambres fortes, machines virtuelles et votre compte de stockage où ils développent l’application.


## <a name="resources"></a>Ressources

-   [Contrôle d’accès basé sur un rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    Cet article explique le contrôle d’accès basé sur Azure Active Directory rôle et son fonctionnement.

-   [RBAC : Intégré rôles](../active-directory/role-based-access-built-in-roles.md)

    Cet article décrit tous les rôles intégrés disponibles dans la section RBAC.

-   [Présentation de déploiement du Gestionnaire de ressources et déploiement classique](../resource-manager-deployment-model.md)

    Cet article décrit le déploiement du Gestionnaire de ressources et les modèles de déploiement classique et explique les avantages de l’utilisation de groupes Gestionnaire de ressources et des ressources

-    [Gérer le contrôle d’accès basé sur un rôle avec PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md)

     Cet article explique comment gérer le contrôle d’accès basé sur un rôle avec PowerShell Azure

-   [Gestion de contrôle d’accès basé sur un rôle avec l’API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Cet article vous explique comment utiliser l’API REST pour gérer RBAC.

-   [Contrôle d’accès basé sur un rôle pour Microsoft Azure à partir de Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Voici un lien vers une vidéo sur Channel 9 de la conférence Ignite MS 2015. Dans cette session, ils parlent accéder aux fonctionnalités de gestion et création de rapports dans Azure et d’Explorer les meilleures pratiques concernant la sécurisation de l’accès aux abonnements Azure à l’aide d’Azure Active Directory.

-   [Autoriser l’accès aux applications web à l’aide de OAuth 2.0 et Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    Cet article décrit le déroulement complet du jeton 2.0 pour l’authentification avec Azure Active Directory.

-   [archivage sécurisé clé des API REST de gestion](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Ce document est la référence de l’API REST gérer votre l’archivage sécurisé clé par programme, y compris la définition de stratégie d’accès de l’archivage sécurisé clés.

-   [archivage sécurisé clé API REST](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    Créer un lien vers l’archivage sécurisé clé documentation de référence API REST.

-   [Contrôle d’accès clés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Créer un lien vers la documentation de référence du contrôle clé access.

-   [Contrôle d’accès secret](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Créer un lien vers la documentation de référence du contrôle clé access.

-   [Définir](https://msdn.microsoft.com/library/mt603625.aspx) et [Supprimer des](https://msdn.microsoft.com/library/mt619427.aspx) stratégie d’accès l’archivage sécurisé clé à l’aide de PowerShell

    Liens vers la documentation pour les applets de commande PowerShell gérer la stratégie d’accès de l’archivage sécurisé clés.

## <a name="next-steps"></a>Étapes suivantes

Pour un didacticiel mise en route lors de l’obtention d’un administrateur, voir [Prise en main l’archivage sécurisé clé Azure](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation pour l’archivage sécurisé clé, consultez [enregistrement de l’archivage sécurisé clé Azure](key-vault-logging.md).

Pour plus d’informations sur l’utilisation de clés et secrets avec l’archivage sécurisé clé Azure, voir [à propos des touches et Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Si vous avez des questions sur l’archivage sécurisé clé, visitez [l’archivage de sécurisé de clés Azure Forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
