<properties
    pageTitle="Azure AD Connect synchronisation : attributs synchronisés à Azure Active Directory | Microsoft Azure"
    description="Répertorie les attributs qui sont synchronisés avec Azure Active Directory."
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
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect synchronisation : attributs synchronisés à Azure Active Directory
Cette rubrique répertorie les attributs qui sont synchronisés par synchronisation Azure AD Connect.  
Les attributs sont regroupées par le connexe Azure AD application.

## <a name="attributes-to-synchronize"></a>Attributs à synchroniser
Une question courante est *Quelle est la liste des attributs minimales à synchroniser*. Par défaut et recommandé est pour conserver les attributs par défaut pour une liste d’adresses globale complète (liste d’adresses globale) peut être créé dans le cloud et obtenir toutes les fonctionnalités dans Office 365 les charges de travail. Dans certains cas, il existe des attributs de votre organisation ne souhaite pas synchronisé dans le cloud dans la mesure où ces attributs contiennent sensible ou informations d’identification personnelle (informations d’identification personnelle) données, comme dans cet exemple :  
![attributs incorrectes](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

Dans ce cas, commencer avec la liste des attributs dans cette rubrique et d’identifier les attributs qui peut contenir des données sensibles ou informations d’identification personnelle et ne peut pas être synchronisés. Désactivez les attributs lors de l’installation à l’aide [d’application Azure AD et le filtrage de l’attribut](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Lorsque désélectionner les attributs, vous devez être prudent et uniquement désélectionnez ces attributs absolument pas possibles de synchroniser. Vous désélectionnez autres attributs peut-être avoir un impact négatif sur les fonctionnalités.

## <a name="office-365-proplus"></a>Office 365 ProPlus

| Nom de l’attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| Définit si un compte est activé.|
| NC| X|  |
| nom complet| X|  |
| objectSID| X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| pwdLastSet| X| propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| sourceAnchor| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| usageLocation| X| propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X| Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|

## <a name="exchange-online"></a>Exchange Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| Assistant| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| NC| X|  | X|  |
| co-création| X| X|  |  |
| société| X| X|  |  |
| code pays| X| X|  |  |
| département| X| X|  |  |
| Description| X| X| X|  |
| nom complet| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| Prénom| X| X|  |  |
| TélPersonnel| X| X|  |  |
| informations| X| X| X| Cet attribut n’est pas actuellement utilisé pour les groupes.|
| Initiales| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Gestionnaire| X| X|  |  |
| membre|  |  | X|  |
| Mobile| X| X|  |  |
| msDS-HABSeniorityIndex| X| X| X|  |
| msDS-PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| forme msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Cet attribut n’est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Cet attribut n’est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Cet attribut n’est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Cet attribut n’est actuellement pas utilisé par Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Cet attribut n’est actuellement pas utilisé par Exchange Online. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg-IsOrganizational|  |  | X|  |
| objectSID| X|  | X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| récepteur de radiomessagerie| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Dérivée de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titre| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| NC| X|  | X|  |
| co-création| X| X|  |  |
| société| X| X|  |  |
| code pays| X| X|  |  |
| département| X| X|  |  |
| Description| X| X| X|  |
| nom complet| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| Prénom| X| X|  |  |
| hideDLMembership|  |  | X|  |
| TélPersonnel| X| X|  |  |
| informations| X| X| X|  |
| initiales| X| X|  |  |
| ipPhone de l'| X| X|  |  |
| l| X| X|  |  |
| courrier| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gestionnaire| X| X|  |  |
| membre|  |  | X|  |
| middleName| X| X|  |  |
| Mobile| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| récepteur de radiomessagerie| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Dérivée de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titre| X| X|  |  |
| unauthOrig| X| X| X|  |
| URL| X| X|  |  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| c| X| X|  |  |
| NC| X|  | X|  |
| co-création| X| X|  |  |
| société| X| X|  |  |
| département| X| X|  |  |
| Description| X| X| X|  |
| nom complet| X| X| X|  |
| facsimiletelephonenumber| X| X| X|  |
| Prénom| X| X|  |  |
| TélPersonnel| X| X|  |  |
| ipPhone de l'| X| X|  |  |
| l| X| X|  |  |
| courrier| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gestionnaire| X| X|  |  |
| membre|  |  | X|  |
| Mobile| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| msRTCSIP-ApplicationOptions| X|  |  |  |
| msRTCSIP-DeploymentLocator| X| X|  |  |
| msRTCSIP-ligne| X| X|  |  |
| msRTCSIP-OptionFlags| X| X|  |  |
| msRTCSIP-OwnerUrn| X|  |  |  |
| msRTCSIP-PrimaryUserAddress| X| X|  |  |
| msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| securityEnabled|  |  | X| Dérivée de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titre| X| X|  |  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| NC| X|  | X| Nom ou l’alias courantes. La plupart du temps le préfixe de valeur [mail].|
| nom complet| X| X| X| Une chaîne qui représente le nom affiché souvent comme nom convivial (prénom nom).|
| courrier| X| X| X| adresse de messagerie complète.|
| membre|  |  | X|  |
| objectSID| X|  | X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| proxyAddresses| X| X| X| propriété mécanique. Utilisé par Azure AD. Contient toutes les adresses de messagerie secondaire pour l’utilisateur.|
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis.|
| securityEnabled|  |  | X| Dérivée de groupType.|
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X|  |  | Cet UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|

## <a name="intune"></a>Intune

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| c| X| X|  |  |
| NC| X|  | X|  |
| Description| X| X| X|  |
| nom complet| X| X| X|  |
| courrier| X| X| X|  |
| mailNickname| X| X| X|  |
| membre|  |  | X|  |
| objectSID| X|  | X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| securityEnabled|  |  | X| Dérivée de groupType|
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|

## <a name="dynamics-crm"></a>Dynamics CRM

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| c| X| X|  |  |
| NC| X|  | X|  |
| co-création| X| X|  |  |
| société| X| X|  |  |
| code pays| X| X|  |  |
| Description| X| X| X|  |
| nom complet| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| Prénom| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| Gestionnaire| X| X|  |  |
| membre|  |  | X|  |
| Mobile| X| X|  |  |
| objectSID| X|  | X| propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| physicalDeliveryOfficeName| X| X|  |  |
| code postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| securityEnabled|  |  | X| Dérivée de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| numéro de téléphone| X| X|  |  |
| titre| X| X|  |  |
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|

## <a name="3rd-party-applications"></a>3e applications tierces
Ce groupe est un ensemble d’attributs utilisés comme les attributs minimales nécessaires pour une application ou la charge de travail générique. Il peut être utilisé pour une charge de travail ne figurant ne pas dans une autre section ou pour une application non Microsoft. Il est utilisé explicitement pour les éléments suivants :

- Yammer (seul utilisateur consommée)
- [Scénarios de collaboration hybride-entreprises (B2B) entre organigramme offertes par des ressources comme SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Ce groupe est un ensemble d’attributs qui peut être utilisé si le répertoire Azure AD n’est pas utilisé pour prendre en charge d’Office 365, Dynamics ou Intune. Il comporte un ensemble réduit d’attributs principaux.

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Définit si un compte est activé.|
| NC| X|  | X|  |
| nom complet| X| X| X|  |
| Prénom| X| X|  |  |
| courrier| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| membre|  |  | X|  |
| objectSID| X|  |  | propriété mécanique. Identificateur de l’utilisateur AD permet de gérer la synchronisation entre Azure AD et AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriété mécanique. Permet de savoir quand invalider jetons déjà émis. Utilisé par la synchronisation de mot de passe et fédération.|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriété mécanique. Identificateur d’immuable à mettre à jour la relation entre ajoute et Azure AD.|
| usageLocation| X|  |  | propriété mécanique. Pays de l’utilisateur. Utilisé pour les affectations de licence.|
| userPrincipalName| X|  |  | Nom UPN est l’ID de connexion pour l’utilisateur. Valeur souvent identique au [mail].|

## <a name="windows-10"></a>Windows 10
Un computer(device) à un domaine Windows 10 synchronise certains attributs à Azure Active Directory. Pour plus d’informations sur les scénarios, voir [se connecter à un domaine appareils à Azure Active Directory pour Windows 10 des expériences](active-directory-azureadjoin-devices-group-policy.md). Les attributs toujours synchroniser et Windows 10 n’apparaît pas sous la forme d’une application, que vous pouvez annuler la sélection. Un ordinateur à un domaine Windows 10 est identifié en demandant l’userCertificate attribut remplie.

| Nom de l’attribut| APPAREIL| Commentaire |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valeur codé en dur pour les ordinateurs à un domaine. |
| nom complet | X| |
| MS-DS-CreatorSID | X| Également appelé registeredOwnerReference.|
| GUID d’objet | X| Également appelé ID de périphérique.|
| objectSID | X| Également appelé onPremisesSecurityIdentifier.|
| système d’exploitation | X| Également appelé deviceOSType.|
| operatingSystemVersion | X| Également appelé deviceOSVersion.|
| userCertificate | X| |

Ces attributs pour **l’utilisateur** sont en plus des autres applications que vous avez sélectionné.  

| Nom de l’attribut| Utilisateur| Commentaire |
| --- | :-: | --- |
| domainFQDN| X| Également appelé Nom_Domaine_Dns. Par exemple, contoso.com.|
| domainNetBios| X| Également appelé Nom_netbios. Par exemple, CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Écriture différée Exchange hybride
Les attributs sont réécrits à partir d’Azure AD à Active Directory local lorsque vous choisissez d’activer le **déploiement Exchange hybride**. Selon votre version d’Exchange, les attributs moins peuvent être synchronisés.

| Nom de l’attribut| Utilisateur| Contact| Groupe| Commentaire |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X|  |  | Dérivée de cloudAnchor dans Azure Active Directory. Cet attribut est une nouveauté dans Exchange 2016.|
| msExchArchiveStatus| X|  |  | Archive en ligne : Permet aux clients d’archiver les messages.|
| msExchBlockedSendersHash| X|  |  | Filtrage : Réinscrit local filtrage et données d’expéditeurs approuvés et bloqués en ligne à partir de clients.|
| msExchSafeRecipientsHash| X|  |  | Filtrage : Réinscrit local filtrage et données d’expéditeurs approuvés et bloqués en ligne à partir de clients.|
| msExchSafeSendersHash| X|  |  | Filtrage : Réinscrit local filtrage et données d’expéditeurs approuvés et bloqués en ligne à partir de clients.|
| msExchUCVoiceMailSettings| X|  |  | Activer la messagerie unifiée (MU) - messagerie vocale en ligne : utilisée par Microsoft Lync Server integration pour indiquer à Lync Server en local que l’utilisateur possède la messagerie vocale dans les services en ligne.|
| msExchUserHoldPolicies| X|  |  | Gel : Vous permettent de conserver les services en nuage pour déterminer les utilisateurs qui se trouvent sous litige maintenez la touche.|
| proxyAddresses| X| X| X| Uniquement la x500 adresse provenant d’Exchange Online est insérée.|

## <a name="device-writeback"></a>Écriture différée appareil
Les objets du périphérique sont créés dans Active Directory. Ces objets peuvent être des périphériques RELIES Azure AD ou à un domaine Windows 10 ordinateurs.

| Nom de l’attribut| APPAREIL| Commentaire |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| nom complet | X| |
| nom de domaine | X| |
| msDS-CloudAnchor | X| |
| ID de msDS périphérique | X| |
| msDS-DeviceObjectVersion | X| |
| msDS-DeviceOSType | X| |
| msDS-DeviceOSVersion | X| |
| msDS-DevicePhysicalIDs | X| |
| msDS-KeyCredentialLink | X| Uniquement avec schéma Active Directory de Windows Server 2016 |
| msDS IsCompliant | X| |
| msDS IsEnabled | X| |
| msDS IsManaged | X| |
| msDS RegisteredOwner | X| |


## <a name="notes"></a>Notes

- Lorsque vous utilisez un autre ID, en local attribut userPrincipalName est synchronisé avec l’onPremisesUserPrincipalName attribut Azure AD. L’attribut autre ID, du courrier électronique d’exemple, est synchronisé avec Azure AD attribut userPrincipalName.
- Dans la liste ci-dessus, le type d’objet **utilisateur** s’applique également à de type d’objet **iNetOrgPerson**.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
