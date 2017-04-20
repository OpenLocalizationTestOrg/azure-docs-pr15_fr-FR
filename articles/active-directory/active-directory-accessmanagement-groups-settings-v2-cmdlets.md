<properties
    pageTitle="Azure Active Directory PowerShell preview cmdlets pour la gestion de groupe dans Active Directory Azure | Microsoft Azure"
    description="Cette page fournit des exemples de PowerShell pour vous aider à gérer vos groupes dans Azure Active Directory"
    keywords="Azure AD, Azure Active Directory, PowerShell, gestion des groupes, groupe"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Azure Active Directory preview cmdlets pour la gestion de groupe

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-groups-create-azure-portal.md)
- [Portail classique Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Le document suivant fournit des exemples de l’utilisation de PowerShell pour gérer vos groupes dans Azure Active Directory (AD Azure).  Il fournit également des informations sur la façon de configurer le module preview Azure AD PowerShell. Vous devez tout d’abord, [Téléchargez le module Azure Active Directory PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>L’installation du module Azure Active Directory PowerShell

Pour installer le module preview AzureAD PowerShell, utilisez les commandes suivantes :

    PS C:\Windows\system32> install-module azureadpreview

Pour vérifier que le module Aperçu a été installé, utilisez la commande suivante :

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Maintenant, vous pouvez commencer à utiliser les applets de commande dans le module. Pour une description complète des applets de commande dans le module AzureAD Preview, reportez-vous à la [documentation de référence en ligne](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Connexion à l’annuaire
Avant de pouvoir commencer à gérer des groupes à l’aide des applets de commande Azure AD PowerShell (version préliminaire), vous devez vous connecter à votre session PowerShell à l’annuaire que vous voulez gérer. Pour ce faire, utilisez la commande suivante :

    PS C:\Windows\system32> Connect-AzureAD -Force

L’applet de commande vous invite à entrer les informations d’identification que vous souhaitez utiliser pour accéder à votre répertoire. Dans cet exemple, nous utilisons karen@drumkit.onmicrosoft.com pour accéder au répertoire de démonstration. L’applet de commande renverra une confirmation pour afficher que la session a été correctement connectée à votre annuaire :

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Maintenant, vous pouvez commencer à utiliser les applets de commande Aperçu AzureAD pour gérer les groupes dans votre annuaire.

## <a name="retrieving-groups"></a>Récupération des groupes
Pour récupérer des groupes existants à partir de votre annuaire, vous pouvez utiliser l’applet de commande Get-AzureADGroups. Pour extraire tous les groupes dans l’annuaire, utilisez l’applet de commande sans paramètres :

    PS C:\Windows\system32> get-azureadgroup

L’applet de commande renvoie tous les groupes dans l’annuaire connecté.

Vous pouvez utiliser le paramètre - objectID pour récupérer un groupe spécifique pour laquelle vous spécifiez objectID du groupe :

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

L’applet de commande retournera désormais le groupe dont objectID correspond à la valeur du paramètre que vous avez entré :

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Vous pouvez rechercher un groupe spécifique à l’aide du - paramètre de filtre. Ce paramètre prend une clause de filtre ODATA et renvoie tous les groupes qui correspondent au filtre, comme dans l’exemple suivant :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Notez que les applets de commande AzureAD PowerShell mise en œuvre la norme de requête OData, vous pouvez trouver plus d’informations [ici](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Création de groupes
Pour créer un nouveau groupe dans votre annuaire, utilisez l’applet de commande New-AzureADGroup. Cette applet de commande permet de créer un nouveau groupe de sécurité appelé « Marketing » :

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Mise à jour des groupes
Pour mettre à jour un groupe existant, utilisez l’applet de commande Set-AzureADGroup. Dans cet exemple, nous allons modification de la propriété DisplayName du groupe « Administrateurs Intune. » Tout d’abord, nous sommes recherche du groupe à l’aide de l’applet de commande Get-AzureADGroup et filtrer à l’aide de l’attribut DisplayName :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Ensuite, nous passons la propriété Description pour la nouvelle valeur « Intune appareil administrateurs » :

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Si nous avons trouvé le groupe nouveau, nous voyons maintenant que la propriété Description est mis à jour pour refléter la nouvelle valeur :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Supprimer des groupes
Pour supprimer des groupes à partir de votre annuaire, utilisez l’applet de commande Supprimer AzureADGroup comme suit :

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gestion des membres de groupes
Si vous devez ajouter de nouveaux membres à un groupe, utilisez l’applet de commande Add-AzureADGroupMember. Cette commande ajoute un membre au groupe Administrateurs Intune que nous avons utilisé dans l’exemple précédent :

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Le paramètre - ObjectId est ObjectID du groupe à laquelle nous voulons ajouter un membre, et le RefObjectId - est ObjectID de l’utilisateur que vous souhaitez ajouter en tant que membre au groupe.

Pour obtenir les membres d’un groupe existants, utilisez l’applet de commande Get-AzureADGroupMember, comme dans cet exemple :

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Pour supprimer le membre que nous précédemment ajoutés au groupe, utilisez l’applet de commande Supprimer AzureADGroupMember, comme est illustré ici :

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Pour vérifier les appartenances d’un utilisateur, utilisez l’applet de commande Sélectionner AzureADGroupIdsUserIsMemberOf. Cette applet de commande prend comme paramètres ObjectId de l’utilisateur pour lequel vérifier l’appartenance aux groupes et la liste des groupes pour lesquelles vous pouvez vérifier les appartenances. La liste des groupes doit être fourni sous la forme d’une variable complexe de type « Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck », donc tout d’abord devons créons une variable avec ce type :

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Ensuite, nous fournir des valeurs pour les GroupID vérifier dans l’attribut « Les GroupID » de cette variable complexe :

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

À présent, si nous voulons vérifier l’appartenance d’un utilisateur avec des 72cd4bbd-2594-40a2-935c-016f3cfeeeea ObjectID contre les groupes dans $g, nous devons utiliser :

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


La valeur renvoyée est une liste de groupes dont cet utilisateur est membre. Vous pouvez également appliquer cette méthode pour vérifier l’appartenance aux Contacts, des groupes ou des identités de Service pour une liste de groupes, à l’aide de sélectionner AzureADGroupIdsContactIsMemberOf, sélectionnez AzureADGroupIdsGroupIsMemberOf ou sélectionnez AzureADGroupIdsServicePrincipalIsMemberOf donnée

## <a name="managing-owners-of-groups"></a>Gérer les propriétaires de groupes
Pour ajouter des propriétaires à un groupe, utilisez l’applet de commande Add-AzureADGroupOwner :

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Le paramètre - ObjectId est ObjectID du groupe à laquelle nous voulons ajouter un propriétaire, et la RefObjectId - est ObjectID de l’utilisateur que vous souhaitez ajouter en tant que propriétaire du groupe.

Pour récupérer les propriétaires d’un groupe, utilisez le Get-AzureADGroupOwner :

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

L’applet de commande renvoie la liste des propriétaires pour le groupe spécifié :

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Si vous voulez supprimer un propriétaire d’un groupe, utilisez Supprimer AzureADGroupOwner :

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez trouver plus Azure Active Directory PowerShell documentation sur [Azure Active Directory applets de commande](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Gestion de l’accès aux ressources des groupes de Azure Active Directory](active-directory-manage-groups.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
