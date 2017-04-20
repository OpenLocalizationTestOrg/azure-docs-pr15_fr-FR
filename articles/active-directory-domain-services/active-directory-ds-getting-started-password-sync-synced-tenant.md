<properties
    pageTitle="Les Services de domaine Active Directory Azure : Activer la synchronisation de mot de passe | Microsoft Azure"
    description="Prise en main Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Activer la synchronisation de mot de passe pour les Services de domaine Active Directory Azure
Dans tâches précédentes, vous avez activé Azure Active Directory Domain Services pour votre client Azure AD. La tâche suivante consiste à activer la synchronisation des mots de passe Azure Active Directory Domain Services. Une fois que la synchronisation d’informations d’identification a été configurée, les utilisateurs peuvent se connecter au domaine géré à l’aide de leurs informations d’identification d’entreprise.

Les étapes à suivre sont différentes selon que votre organisation possède une annonce Azure exclusivement le nuage, du client ou est configuré pour synchroniser avec votre annuaire local à l’aide d’Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Client AD Azure exclusivement le nuage](active-directory-ds-getting-started-password-sync.md)
- [Synchronisé client Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Étape 5 : Activer la synchronisation de mot de passe aux Services de domaine AAD pour un Azure synchronisé client AD
A synchronisé Azure client AD est configuré pour synchroniser avec un répertoire local de votre organisation à l’aide d’Azure AD Connect. Azure AD Connect n’est pas synchronisé NTLM et des informations d’identification Kerberos hachage à Azure Active Directory par défaut. Pour utiliser les Services de domaine Active Directory Azure, vous devez configurer Azure AD Connect pour synchroniser hachage d’informations d’identification requises pour l’authentification NTLM et Kerberos. Les étapes suivantes permettent la synchronisation des valeurs de hachage d’informations d’identification nécessaires à votre client Azure AD.


### <a name="install-or-update-azure-ad-connect"></a>Installer ou mettre à jour Azure AD Connect
Installer la dernière version recommandée d’Azure AD Connect sur ordinateur faisant partie d’un domaine. Si vous avez une instance existante de Azure AD Connect le programme d’installation, vous devez mettre à jour pour qu’il utilise la dernière version d’Azure AD Connect. Pour éviter les problèmes/bogues connus qui ont déjà été corrigés, assurez-vous que vous utilisez toujours la dernière version d’Azure AD Connect.

**[Téléchargement Azure AD se connecter](http://www.microsoft.com/download/details.aspx?id=47594)**

Recommandé version : **1.1.281.0** - publié sur 7 septembre 2016.

  > [AZURE.WARNING] Vous devez installer la dernière version recommandée d’Azure AD Connect pour activer les informations d’identification de mot de passe héritées (requises pour l’authentification NTLM et Kerberos) à synchroniser avec votre client Azure AD. Cette fonctionnalité n’est pas disponible dans les versions antérieures de Azure AD Connect ou avec l’outil de synchronisation d’annuaire existant.

Instructions d’installation pour Azure AD Connect sont disponibles dans l’article suivant : [prise en main d’Azure AD Connect](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Activer la synchronisation de NTLM et Kerberos hachage d’informations d’identification à Azure Active Directory
Exécuter le script PowerShell suivant sur chaque forêt Active Directory, de forcer la synchronisation de mot de passe et activer hachage d’informations d’identification de tous les utilisateurs locaux pour la synchronisation avec votre client Azure AD. Ce script active les valeurs de hachage d’informations d’identification requises pour l’authentification Kerberos/NTLM à synchroniser à votre client Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Selon la taille de votre annuaire (nombre d’utilisateurs, groupes etc.), la synchronisation de hachage d’informations d’identification à Azure Active Directory de temps. Les mots de passe sera utilisables sur le domaine géré Azure Active Directory Domain Services peu de temps après que les valeurs de hachage d’informations d’identification ont été synchronisées à Azure Active Directory.


<br>

## <a name="related-content"></a>Contenu associé

- [Activer la synchronisation de mot de passe aux Services de domaine AAD pour un Azure exclusivement le nuage, annuaire Active Directory](active-directory-ds-getting-started-password-sync.md)

- [Administrer un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)

- [Participer à une machine virtuelle Windows à un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)

- [Participer à une machine virtuelle Red chapeau Enterprise Linux à un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
