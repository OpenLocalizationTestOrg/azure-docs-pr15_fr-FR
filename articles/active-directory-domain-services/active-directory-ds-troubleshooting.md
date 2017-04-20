<properties
    pageTitle="Azure Active Directory Domain Services : Guide de dépannage | Microsoft Azure"
    description="Guide de dépannage pour les Services de domaine Active Directory Azure"
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
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Services de domaine Active Directory Azure - guide de dépannage
Cet article fournit des conseils de dépannage pour les problèmes que vous pouvez rencontrer lors de la configuration ou de l’administration des Services de domaine Azure Active Directory (AD).


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Vous ne pouvez pas activer Azure Active Directory Domain Services pour votre annuaire Azure AD
Cette section vous permet de résoudre les erreurs lorsque vous essayez d’activer Azure Active Directory Domain Services pour votre annuaire et échec ou obtient basculé vers « Désactivé ».

Choisissez les étapes de dépannage qui correspondent au message d’erreur que vous rencontrez.

|**Message d’erreur**|**Résolution**|
|---|:---|
|*Le nom contoso100.com est déjà en cours d’utilisation sur ce réseau. Spécifiez un nom qui n’est pas en cours d’utilisation.*|[Conflit de noms de domaine dans le réseau virtuel](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*Les Services de domaine n’a pas peuvent être activées dans ce client Azure AD. Le service ne dispose pas des autorisations appropriées pour l’application appelée « Azure Active Directory Domain Services Sync ». Supprimer l’application appelée « Azure Active Directory Domain Services Sync » et puis essayez d’activer les Services de domaine pour votre client Azure AD.*|[Les Services de domaine ne dispose pas des autorisations appropriées pour l’application de synchronisation des Services de domaine Active Directory Azure](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*Les Services de domaine n’a pas peuvent être activées dans ce client Azure AD. L’application de Services de domaine de votre client Azure AD n’a pas les autorisations requises pour activer les Services de domaine. Supprimer l’application avec la d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificateur application et puis essayez d’activer les Services de domaine pour votre client Azure AD.*|[L’application de Services de domaine n’est pas configurée correctement dans votre client](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*Les Services de domaine n’a pas peuvent être activées dans ce client Azure AD. L’application Microsoft Azure Active Directory est désactivée dans votre client Azure AD. Activer l’application avec la 00000002-0000-0000-c000-000000000000 identificateur application et puis essayez d’activer les Services de domaine pour votre client Azure AD.*|[L’application Microsoft Graph est désactivée dans votre client Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Conflit de noms de domaine
**Message d’erreur :**

*Le nom contoso100.com est déjà en cours d’utilisation sur ce réseau. Spécifiez un nom qui n’est pas en cours d’utilisation.*

**Mise à jour :**

Vérifiez que vous n’avez pas un domaine existant portant le même nom de domaine disponible sur ce réseau virtuel. Par exemple, supposons que vous avez un domaine appelé « contoso.com » déjà disponibles sur le réseau virtuel sélectionné. Plus tard, vous essayez d’activer un domaine géré Azure Active Directory Domain Services portant le même nom de domaine (c'est-à-dire, « contoso.com ») sur ce réseau virtuel. Vous rencontrez une erreur lorsque vous tentez d’activer les Services de domaine Active Directory Azure.

Cela est dû à des conflits de noms pour le nom de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré Azure Active Directory Domain Services. Vous pouvez également mettre hors service le domaine existant, puis passez à activer les Services de domaine Active Directory Azure.


### <a name="inadequate-permissions"></a>Autorisations insuffisantes
**Message d’erreur :**

*Les Services de domaine n’a pas peuvent être activées dans ce client Azure AD. Le service ne dispose pas des autorisations appropriées pour l’application appelée « Azure Active Directory Domain Services Sync ». Supprimer l’application appelée « Azure Active Directory Domain Services Sync » et puis essayez d’activer les Services de domaine pour votre client Azure AD.*

**Mise à jour :**

Vérifier s’il existe une application avec le nom « Azure Active Directory Domain Services Sync » dans votre annuaire Azure AD. Si cette application existe, supprimez-la et puis réactivez Azure Active Directory Domain Services.

Procédez comme suit pour vérifier la présence de l’application et de suppression, si l’application existe :

  1. Accédez au **portail classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Sélectionnez le nœud **Active Directory** dans le volet gauche.
  3. Sélectionnez le client Azure AD (répertoire) pour laquelle vous voulez activer les Services de domaine Active Directory Azure.
  4. Accédez à l’onglet **Applications** .
  5. Sélectionnez l’option **Mon entreprise possède des Applications** dans la liste déroulante.
  6. Vérifier une application appelée **synchronisation des Services de domaine Active Directory Azure**. Si l’application existe, passez à supprimer.
  7. Une fois que vous avez supprimé l’application, essayez d’activer les Services de domaine Active Directory Azure une nouvelle fois.


### <a name="invalid-configuration"></a>Configuration non valide
**Message d’erreur :**

*Les Services de domaine n’a pas peuvent être activées dans ce client Azure AD. L’application de Services de domaine de votre client Azure AD n’a pas les autorisations requises pour activer les Services de domaine. Supprimer l’application avec la d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificateur application et puis essayez d’activer les Services de domaine pour votre client Azure AD.*

**Mise à jour :**

Vérifiez si vous avez une application avec le nom « AzureActiveDirectoryDomainControllerServices » (avec un identificateur d’application de d87dcbc6-a371-462e-88e3-28ad15ec4e64) dans votre annuaire Azure AD. Si cette application existe, vous devez supprimer et puis réactivez Azure Active Directory Domain Services.

Utiliser le script PowerShell suivant pour rechercher l’application et supprimez-le.

> [AZURE.NOTE] Ce script utilise des applets de commande **Azure AD PowerShell version 2** . Pour une liste complète de toutes les applets de commande disponibles et pour télécharger le module, consultez la [documentation de référence AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph désactivé
**Message d’erreur :**

Les Services de domaine n’a pas peuvent être activées dans ce client Azure AD. L’application Microsoft Azure Active Directory est désactivée dans votre client Azure AD. Activer l’application avec la 00000002-0000-0000-c000-000000000000 identificateur application et puis essayez d’activer les Services de domaine pour votre client Azure AD.

**Mise à jour :**

Vérifiez si vous avez désactivé une application avec l’identificateur 00000002-0000-0000-c000-type "000000000000". Cette application est l’application Microsoft Azure AD et offre un accès API graphique à votre client Azure AD. Azure Active Directory Domain Services doit être activé pour synchroniser votre client Azure AD pour votre domaine géré de cette application.

Pour corriger cette erreur, activer cette application et puis essayez d’activer les Services de domaine pour votre client Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Les utilisateurs ne peuvent pas se connecter au domaine géré Azure Active Directory Domain Services
Si un ou plusieurs utilisateurs dans votre client Azure AD ne parvenez pas à se connecter au domaine géré nouvellement créé, effectuez les étapes de résolution des problèmes suivantes :

- **Se connecter à l’aide du format UPN :** Essayez de vous connecter en utilisant le format UPN (par exemple, 'joeuser@contoso.com') au lieu du format SAMAccountName (« CONTOSO\joeuser »). SAMAccountName peut être généré automatiquement pour les utilisateurs dont le préfixe UPN est trop long ou est identique à un autre utilisateur sur le domaine géré. Le format UPN est garanti unique au sein d’un client Azure AD.

> [AZURE.NOTE] Nous vous recommandons d’utiliser au format UPN connecter pour le domaine géré Azure Active Directory Domain Services.

- Assurez-vous d’avoir [activé la synchronisation de mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.

- **Comptes externes :** Assurez-vous que le compte d’utilisateur concerné n’est pas un compte externe dans le client Azure AD. Exemples de comptes externes comptes Microsoft (par exemple, 'joe@live.com') ou comptes d’utilisateurs à partir d’un utilisateur externe répertoire Azure AD. Dans la mesure où Azure Active Directory Domain Services n’a pas les informations d’identification pour ces comptes d’utilisateur, ces utilisateurs ne peuvent pas se connecter au domaine géré.

- **Synchronisé comptes :** Si les comptes d’utilisateurs affectés sont synchronisés à partir d’un répertoire local, vérifiez que :
    - Vous avez déployé ou mis à jour vers la [version plus récente recommandée d’Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Vous avez configuré Azure AD Connect pour [effectuer une synchronisation complète](active-directory-ds-getting-started-password-sync.md).

    - Selon la taille de votre annuaire, il peut prendre un certain temps pour les comptes et des informations d’identification hachage soit disponible dans Azure Active Directory Domain Services. Vérifiez que vous attendez suffisamment longtemps avant de réessayer d’authentification (selon la taille de votre répertoire - quelques heures par jour ou deux pour annuaires volumineux).

    - Si le problème persiste après avoir vérifié les étapes précédentes, essayez de redémarrer le Service Microsoft Azure AD Sync. À partir de votre ordinateur de synchronisation, lancer une invite de commande et exécutez les commandes suivantes :
      1. arrêter NET « Microsoft Azure AD Sync »
      2. démarrage NET « Microsoft Azure AD Sync »

- **Comptes exclusivement le nuage**: si le compte d’utilisateur concerné est un compte d’utilisateur exclusivement le nuage, assurez-vous que l’utilisateur a changé son mot de passe une fois que vous avez activé Azure Active Directory Domain Services. Cette étape permet d’afficher les informations d’identification hachage requis pour les Services de domaine Active Directory Azure à générer.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Les utilisateurs supprimés de votre client Azure AD ne sont pas supprimées de votre domaine géré
Azure AD vous protège contre la suppression accidentelle d’objets utilisateur. Lorsque vous supprimez un compte d’utilisateur de votre client Azure AD, l’objet utilisateur correspondant est déplacé vers la Corbeille. Lorsque cette opération de suppression est synchronisée avec votre domaine géré, cela provoque le compte d’utilisateur correspondant à marquer désactivé. Cette fonctionnalité permet de récupérer ou restaurer le compte d’utilisateur ultérieurement.

Pour supprimer entièrement le compte d’utilisateur de votre domaine géré, supprimer définitivement l’utilisateur de votre client Azure AD. Utiliser l’applet de commande PowerShell supprimer-MsolUser avec la '-RemoveFromRecycleBin' option, comme décrit dans cet [article MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Nous contacter
Contactez l’équipe du produit Azure Active Directory Domain Services à [partager des commentaires ou un support technique] (actif-répertoire-ds-contact-us.md).
