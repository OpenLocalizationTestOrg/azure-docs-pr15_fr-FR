<properties
   pageTitle="Format de fichier CSV pour preview de collaboration Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B annuaire Active prend en charge les relations de votre société croisée en activant partenaires professionnels de manière sélective accéder à vos applications d’entreprise"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Aperçu de collaboration Azure AD B2B : format de fichier CSV

La version de collaboration Azure AD B2B nécessite un fichier CSV spécifiant les informations utilisateur sur le partenaire à être téléchargés à partir du portail Azure AD. Le fichier CSV doit contenir les étiquettes requis ci-dessous et les champs facultatifs comme bon vous semble. Modifier l’exemple de fichier CSV (voir ci-dessous) sans modifier l’orthographe des étiquettes dans la première ligne.

>[AZURE.NOTE] La première ligne d’étiquettes (par exemple, messagerie, DisplayName et ainsi de suite) est nécessaire pour le fichier CSV pour être analysé correctement. L’orthographe doit correspondre les champs spécifiés ci-dessous. Ces étiquettes identifient le contenu en dessous. Pour les champs facultatifs qui ne sont pas nécessaires, leurs étiquettes peuvent être supprimés à partir du fichier CSV. La colonne entière peut être vide.

## <a name="required-fields-br"></a>Champs requis : <br/>
**Messagerie :** Adresse de messagerie de l’utilisateur invité. <br/>
**DisplayName :** Nom complet de l’utilisateur invité (en règle générale, prénom et nom).<br/>


## <a name="optional-fields-br"></a>Champs facultatifs : <br/>

**InvitationText :** Personnaliser le courrier électronique d’invitation après l’application de personnalisation et avant le lien de remboursement.<br/>
**InvitedToApplications :** AppID aux applications d’entreprise pour attribuer aux utilisateurs. AppID est récupérables dans PowerShell en appelant`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups :** ObjectID pour les groupes pour ajouter utilisateur. ObjectID est récupérables dans PowerShell en appelant`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL :** URL pour diriger un utilisateur invité après l’acceptation d’invitation. Il s’agit d’une URL spécifique à la société (par exemple, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si ce champ facultatif n’est pas spécifié, l’utilisateur invité est dirigé vers le panneau d’accès de l’application pour accéder à vos applications d’entreprise choisies. L’URL du panneau d’application Access est de la forme `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: adresse pour copier l’invitation envoyées par courrier électronique. Si le champ CcEmailAddress est utilisé, cette invitation ne peut pas être utilisée pour utilisateur vérifié de messagerie ou la création de client.<br/>
**Langue :** Langue pour une invitation électronique et remboursement expérience, avec « US » (en anglais) par défaut lorsque n’est pas spécifié. Les autres 10 langage codes sont pris en charge :<br/>
1. de : allemand<br/>
2. es : espagnol<br/>
3. fr : Français<br/>
4. il : italien<br/>
5. ja : japonais<br/>
6. Ko : coréen<br/>
7. pt-BR : portugais (Brésil)<br/>
8. demandeur : russe<br/>
9. zh-HANS : chinois simplifié<br/>
10. zh-HANT : chinois traditionnel<br/>

## <a name="sample-csv-file"></a>Exemple de fichier CSV
Voici un exemple de contenu CSV que vous pouvez modifier.

>[AZURE.NOTE] Copier et coller dans le bloc-notes et enregistrez-le avec une extension de fichier « .csv ». Puis le modifier dans Excel. Il sera structuré dans une table avec les étiquettes dans la première ligne.

> Ajouter d’autres champs facultatifs dans Excel en spécifiant l’étiquette et remplissage de la colonne en dessous.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Articles connexes
Parcourir notre d’autres articles sur la collaboration Azure AD B2B

- [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitations aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
