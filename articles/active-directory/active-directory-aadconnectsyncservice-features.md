<properties
    pageTitle="Fonctionnalités du service synchronisation Azure AD Connect et configuration | Microsoft Azure"
    description="Décrit les fonctionnalités de côté de service pour le service de synchronisation Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Fonctionnalités du service synchronisation Azure AD Connect

La fonctionnalité de synchronisation d’Azure AD Connect comporte deux éléments :

- Le composant local nommé **synchronisation Azure AD Connect**, également appelé **moteur de synchronisation**.
- Le service résidant dans Azure AD également connu sous le **service de synchronisation Azure AD Connect**

Cette rubrique explique comment utiliser les fonctionnalités suivantes du **service de synchronisation Azure AD Connect** et comment vous pouvez configurer à l’aide de Windows PowerShell.

Ces paramètres sont configurés par le [Module Azure Active Directory pour Windows PowerShell](http://aka.ms/aadposh). Téléchargez et installez séparément à partir d’Azure AD Connect. Les applets de commande présentées dans cette rubrique ont été introduites dans la [2016 mars relâchez (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Si vous n’avez pas les applets de commande présentées dans cette rubrique ou qu’ils ne produisent pas le même résultat, puis vérifiez que vous exécutez la version la plus récente.

Pour voir la configuration dans votre annuaire Azure AD, exécutez `Get-MsolDirSyncFeatures`.  
![Résultat Get-MsolDirSyncFeatures](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

La plupart de ces paramètres peuvent uniquement être modifiées par Azure AD Connect.

Les paramètres suivants peuvent être configurées par `Set-MsolDirSyncFeature`:

DirSyncFeature | Commentaire
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Permet à un attribut à être mis en quarantaine lorsqu’elle est un doublon d’un autre objet plutôt qu’ignore la totalité de l’objet pendant l’exportation.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Permet aux objets effectuer la jointure userPrincipalName en plus de l’adresse SMTP principale.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Permet au moteur de synchronisation mettre à jour l’attribut userPrincipalName pour les utilisateurs de (non fédéré) géré/sous licence.

Après avoir activé une fonctionnalité, il ne peut pas être désactivé à nouveau.

>[AZURE.NOTE] À partir de 24 août 2016 la fonctionnalité *la résilience attribut double* est activée par défaut pour Azure nouveau répertoires AD. Cette fonctionnalité est également être transférée et activée sur les répertoires créées avant cette date. Vous recevrez une notification par courrier électronique lorsque votre annuaire est prêt à obtenir cette fonctionnalité est activée.

Les paramètres suivants sont configurés par Azure AD Connect et ne peut pas être modifiées par `Set-MsolDirSyncFeature`:

DirSyncFeature | Commentaire
--- | ---
DeviceWriteback | [Azure AD Connect : L’activation d’écriture différée appareil](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Azure AD Connect synchronisation : extensions Directory](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Mise en œuvre la synchronisation de mot de passe avec synchronisation Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Aperçu : Écriture différée groupe](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Pas actuellement pris en charge.

## <a name="duplicate-attribute-resiliency"></a>Résilience attribut en double
Au lieu de basculer mise en service des objets avec UPN en double / proxyAddresses, l’attribut double est » mis en quarantaine » et une valeur temporaire est affectée. Lors de la résolution du conflit, le nom UPN temporaire est automatiquement remplacé par la valeur correcte. Ce problème peut être activé pour UPN et proxyAddress séparément. Pour plus d’informations, voir [synchronisation des identités et résilience attribut en double](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Correspondance de contours UserPrincipalName
Lorsque cette fonctionnalité est activée, bordures correspondance est activée pour UPN en plus de l' [adresse SMTP principale](https://support.microsoft.com/kb/2641663), qui est toujours activée. Correspondance de bordures est utilisée pour faire correspondre les utilisateurs cloud existants dans Azure Active Directory avec des utilisateurs locaux.

Si vous devez correspondance locale les comptes Active Directory avec des comptes existants créés dans le cloud et vous n’utilisez pas Exchange Online, puis cette fonctionnalité est utile. Dans ce scénario, vous ne généralement avez une raison pour définir l’attribut SMTP dans le cloud.

Cette fonctionnalité est sur par défaut pour nouvellement créée répertoires Azure AD. Vous pouvez voir si cette fonctionnalité est activée pour vous en exécutant :  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Si cette fonctionnalité n’est pas activée pour votre annuaire Azure AD, vous pouvez l’activer en exécutant :  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchroniser les mises à jour userPrincipalName
Traditionnellement, mises à jour de l’attribut UserPrincipalName au moyen du service de synchronisation locales a été bloqué, à moins que les deux conditions suivantes sont remplies :

- L’utilisateur est géré (non fédéré).
- L’utilisateur n’a pas été affecté à une licence.

Pour plus d’informations, voir [noms d’utilisateurs dans Office 365, Azure ou Intune ne correspondent pas le local UPN ou l’ID de connexion secondaire](https://support.microsoft.com/kb/2523192).

L’activation de cette fonctionnalité permet au moteur de synchronisation à jour userPrincipalName lorsqu’il est modifié en local et que vous utilisez la synchronisation de mot de passe. Si vous utilisez la fédération, cette fonctionnalité n’est pas pris en charge.

Cette fonctionnalité est sur par défaut pour nouvellement créée répertoires Azure AD. Vous pouvez voir si cette fonctionnalité est activée pour vous en exécutant :  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Si cette fonctionnalité n’est pas activée pour votre annuaire Azure AD, vous pouvez l’activer en exécutant :  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Après avoir activé cette fonctionnalité, les valeurs existantes userPrincipalName restera comme-est. Sur la prochaine modification de l’userPrincipalName attribut local, la synchronisation normale delta utilisateurs met à jour le nom UPN.  

## <a name="see-also"></a>Voir aussi

- [Azure AD Connect synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
