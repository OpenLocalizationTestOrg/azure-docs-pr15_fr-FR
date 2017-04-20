<properties
   pageTitle="Modifications de l’attribut objet utilisateur externe pour un aperçu de collaboration Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Aperçu de collaboration Azure AD B2B : modifications de l’attribut objet utilisateur externe

Chaque utilisateur dans un répertoire Azure AD est représenté par un objet utilisateur. L’objet utilisateur dans Active Directory Azure est soumis à des modifications d’attributs de diverses étapes de la collaboration B2B échanger inviter flux. L’objet utilisateur qui représente l’utilisateur partenaire dans le répertoire dispose des attributs de modifier à récupérer temps, lorsque l’utilisateur partenaire clique sur le lien dans le message d’invitation. Plus précisément :

- Les attributs **SignInName** et **AltSecId** sont remplis
- L’attribut **DisplayName** modifie à partir du nom d’utilisateur Principal (user_fabrikam.com#EXT#@contoso.com) le nom de connexion(user@fabrikam.com)

Suivi de ces attributs dans Azure AD peut vous aider à résoudre les problèmes d’un utilisateur partenaire a échangé leur invitation de collaboration B2B ou non.

## <a name="related-articles"></a>Articles connexes
Visitez notre d’autres articles sur la collaboration Azure AD B2B :

- [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence des formats de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Limitations aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
