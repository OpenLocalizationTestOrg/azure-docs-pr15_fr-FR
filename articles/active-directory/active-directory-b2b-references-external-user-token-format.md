<properties
   pageTitle="Format du jeton utilisateur externe pour un aperçu de collaboration Azure Active Directory B2B | Microsoft Azure"
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

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Aperçu de collaboration Azure AD B2B : format du jeton utilisateur externe

Les réclamations pour une annonce Azure standard jeton sont décrits dans l’article [jeton pris en charge et les Types de revendications](active-directory-token-and-claims.md) sur azure.microsoft.com.

Les revendications sont différentes pour un utilisateur externe d’authentifiés B2B collaboration sont les suivantes :<br/>
- **OID :** l’ID d’objet à partir du client de ressources<br/>
- **TID**: ID du client à partir du client de ressources<br/>
- **Émetteur**: il s’agit du client de ressources<br/>
- **IDP**: il s’agit du client accueil de l’utilisateur<br/>
- **AltSecId**: il s’agit de l’ID de sécurité de remplacement, qui est opaque pour vous<br/>

## <a name="related-articles"></a>Articles connexes
Visitez notre d’autres articles sur la collaboration Azure AD B2B :

- [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence des formats de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitations aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
