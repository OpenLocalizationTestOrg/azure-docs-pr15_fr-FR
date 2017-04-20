<properties
   pageTitle="Azure AD Connect : Concevoir concepts | Microsoft Azure"
   description="Cette rubrique décrit certaines zones de conception de l’implémentation"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect : Concepts de conception
L’objectif de cette rubrique consiste à décrire les zones qui doivent être représentées par le biais pendant la conception de l’implémentation d’Azure AD Connect. Cette rubrique concerne une approfondie dans certaines zones et ces concepts sont brièvement décrites dans d’autres rubriques également.

## <a name="sourceanchor"></a>sourceAnchor
L’attribut sourceAnchor est définie comme *un attribut immuable pendant la durée de vie d’un objet*. Elle identifie un objet comme étant le même objet en local et dans Azure Active Directory. L’attribut est également appelé **immutableId** et les deux noms servent interchangeable.

Le mot immuable, qui est « ne peut pas être modifié », est important de cette rubrique. Étant donné que la valeur de cet attribut ne peut pas être modifiée après que qu’il a été défini, il est important de choisir un modèle qui prend en charge votre scénario.

L’attribut est utilisé pour les scénarios suivants :

- Lorsqu’un nouveau serveur de moteur de synchronisation est créé ou reconstruit après un scénario de récupération d’urgence, cet attribut lie des objets existants dans Azure AD avec objets locaux.
- Si vous déplacez à partir d’une identité exclusivement le nuage, un modèle d’identité synchronisée, cet attribut permet aux objets aux objets existants « correspondance exacte » dans Azure Active Directory avec des objets en local.
- Si vous utilisez la fédération, cet attribut avec **userPrincipalName** est utilisé dans la demande de remboursement pour identifier un utilisateur.

Cette rubrique parle uniquement sourceAnchor par rapport aux utilisateurs. Les mêmes règles s’appliquent à tous les types d’objets, mais elle s’applique uniquement aux utilisateurs que ce problème est généralement un problème.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Sélection d’un attribut sourceAnchor bonne
La valeur de l’attribut doit respecter les règles suivantes :

- Moins de 60 caractères
    - Caractères n’est pas d’a à z, de A à Z ou 0-9 sont codés et comptés comme 3 caractères
- Ne contient pas un caractère spécial : & #92 ; ! # $ % & * + / = ? ^ & #96 ; { } | ~ < > () ' ; : , [ ] " @ _
- Doit être globalement unique
- Doit être une chaîne, entier ou binaire.
- Ne doit pas être basé sur le nom d’utilisateur, ces modifications
- Ne doit pas respecter la casse et éviter les valeurs qui peuvent varier selon le cas
- Doivent être affectées lorsque l’objet est créé.

Si la sourceAnchor sélectionné n’est pas de type string, puis Azure AD connecter Base64Encode la valeur de l’attribut pour ne garantir aucun caractère spécial n’apparaissent. Si vous utilisez un autre serveur de fédération qu’ADFS, vérifiez que votre serveur peut également Base64Encode l’attribut.

L’attribut sourceAnchor respecte la casse. Une valeur de « Durand » n’est pas différente de celle « Durand ». Mais vous ne devez pas deux objets distincts avec qu’une différence en cas de modification.

Si vous avez une seule forêt en local, puis l’attribut que vous devez utiliser est **GUID d’objet**. Il s’agit également l’attribut utilisé lorsque vous utilisez les paramètres express dans Azure AD Connect et également l’attribut utilisé par la synchronisation d’annuaire.

Si vous avez plusieurs forêts et ne pas déplacez les utilisateurs entre forêts et les domaines, **GUID d’objet** est un attribut bonne utiliser même dans ce cas.

Si vous déplacez des utilisateurs entre forêts et les domaines, vous devez trouver un attribut qui ne change pas ou peut être déplacé avec les utilisateurs lors du déplacement. Une approche recommandée est d’introduire un attribut synthétique. Un attribut qui peut contenir quelque chose qui ressemble à un GUID convient. Lors de la création de l’objet, un nouveau GUID est créé et affecté à l’utilisateur. Une règle de synchronisation personnalisée peut être créée dans le serveur de moteur de synchronisation pour créer cette valeur basée sur **GUID d’objet** et mettre à jour l’attribut sélectionné dans ajoute. Lorsque vous déplacez l’objet, veillez à également copier le contenu de cette valeur.

Une autre solution consiste à sélectionner un attribut existant que vous savez ne change pas. Les attributs fréquemment utilisés incluent **employeeID**. Si vous envisagez un attribut qui contient des lettres, vérifiez qu’il y a que risque aucun la casse (majuscules et minuscules) ne peut modifier pour l’attribut. Les attributs incorrectes qui ne doivent pas être utilisés incluent les attributs avec le nom de l’utilisateur. Dans un mariage ou divorce, le nom est supposé changer, qui n’est pas autorisé pour cet attribut. Il s’agit de l’une des raisons pour lesquelles attributs tels que **userPrincipalName** **courrier**et **targetAddress** ne sont pas même possibles sélectionner dans l’Assistant installation Azure AD Connect également. Ces attributs contiennent également la @-character, qui n’est pas autorisé dans la sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Modification de l’attribut sourceAnchor
La valeur de l’attribut sourceAnchor ne peuvent pas être modifiée une fois que l’objet a été créé dans Azure Active Directory et de l’identité est synchronisée.

Pour cette raison, les restrictions suivantes s’appliquent aux Azure AD Connect :

- L’attribut sourceAnchor peut uniquement être définie pendant l’installation initiale. Si vous réexécutez l’Assistant installation, cette option est en lecture seule. Si vous devez modifier ce paramètre, vous devez désinstaller et réinstaller.
- Si vous installez un autre serveur Azure AD Connect, vous devez sélectionner le même attribut sourceAnchor précédemment utilisé. Si vous avez précédemment utilisé DirSync et atteindre Azure AD Connect, vous devez utiliser **GUID d’objet** car il s’agit de l’attribut utilisé par la synchronisation d’annuaire.
- Si la valeur de sourceAnchor est modifiée après l’objet ont été exportée vers Azure Active Directory, puis Azure AD Connect synchronisation génère une erreur et n’autorise pas les autres modifications sur qu’objet avant que le problème a été résolu et la sourceAnchor est modifié dans le répertoire source.

## <a name="azure-ad-sign-in"></a>Azure AD se connecter
Lors de l’intégration dans l’annuaire locales avec Azure Active Directory, il est important de comprendre comment les paramètres de synchronisation peuvent affecter la manière dont les utilisateurs authentifie. Azure AD utilise userPrincipalName (UPN) pour authentifier l’utilisateur. Toutefois, lorsque vous synchronisez vos utilisateurs, vous devez choisir l’attribut à utiliser avec soin pour valeur userPrincipalName.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Choix de l’attribut userPrincipalName
Lorsque vous sélectionnez l’attribut pour fournir la valeur de UPN à utiliser dans un Azure, nous recommandons

- Les valeurs d’attribut conforme à la syntaxe UPN (RFC 822), c'est-à-dire qu’il doit être au formatusername@domain
- Le suffixe dans les valeurs correspond à un des domaines personnalisés vérifiés dans Azure Active Directory

Dans les paramètres express, le choix par défaut pour l’attribut est userPrincipalName. Si l’attribut userPrincipalName ne contient pas la valeur que vous voulez que vos utilisateurs à se connecter à Azure, puis vous devez choisir **Installation personnalisée**.

### <a name="custom-domain-state-and-upn"></a>État de domaine personnalisé et UPN
Il est important de s’assurer qu’il existe un domaine vérifié pour le suffixe UPN.

Jean est un utilisateur dans contoso.com. Vous voulez John à utiliser le nom UPN local john@contoso.com à se connecter à Azure une fois que vous avez synchronisées utilisateurs votre contoso.onmicrosoft.com répertoire Azure AD. Pour ce faire, vous devez ajouter et vérifier contoso.com comme un domaine personnalisé dans Azure AD avant de commencer la synchronisation des utilisateurs. Si le suffixe UPN Jean, par exemple contoso.com, ne correspond pas à un domaine vérifié dans Azure AD, Azure AD remplace le suffixe UPN par contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domaines pouvant être routés non en local et UPN pour Azure AD
Certaines organisations ont des domaines non pouvant être routés, tels que contoso.local ou domaines étiquette unique simple tel que contoso. Vous n’êtes pas en mesure de vérifier un domaine non prenant dans Azure Active Directory. Azure AD Connect pouvez synchroniser uniquement un domaine vérifié dans Azure Active Directory. Lorsque vous créez un répertoire Azure AD, qu’il crée un domaine pouvant être routé devienne le domaine par défaut pour votre annonce Azure par exemple contoso.onmicrosoft.com. Par conséquent, il est nécessaire vérifier les autres domaines pouvant être routés dans ce cas, au cas où vous ne voulez pas synchroniser avec le domaine onmicrosoft.com par défaut.

Pour plus d’informations sur l’ajout et vérification de domaines, consultez [Ajouter votre nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md) .

Azure AD Connect détecte si vous exécutez dans un environnement de domaine non prenant et souhaitez vous avertit correctement à partir de poursuivre avec les paramètres express. Si vous êtes dans un domaine non prenant, il est probable que le nom UPN des utilisateurs sont trop suffixes non prenant. Par exemple, si vous exécutez sous contoso.local, Azure AD Connect suggère vous permettant d’utiliser des paramètres personnalisés plutôt que d’utiliser les paramètres express. À l’aide des paramètres personnalisés, vous ne pouvez spécifier l’attribut qui doit être utilisé comme UPN à se connecter à Azure une fois que les utilisateurs sont synchronisés avec Azure AD.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
