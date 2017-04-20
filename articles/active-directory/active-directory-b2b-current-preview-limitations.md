<properties
   pageTitle="Limitations preview actuel pour la collaboration Azure Active Directory B2B | Microsoft Azure"
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

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Aperçu de collaboration Azure AD B2B : limitations afficher l’aperçu en cours

- Authentification multifacteur (MFA) non prises en charge sur les utilisateurs externes. Par exemple, si Contoso a l’authentification Multifacteur, mais n’est pas le cas de partenaire organigramme, puis utilisateurs partenaire organigramme n’est pas possible l’authentification Multifacteur grâce à la collaboration B2B.
- Invitations sont possibles uniquement via CSV ; invitations individuelles et accès à l’API ne sont pas pris en charge.
- Seuls les administrateurs globaux Azure AD peuvent télécharger des fichiers .csv.
- Invitations aux adresses de messagerie consommateur (par exemple, hotmail.com, Gmail.com ou comcast.net) ne sont actuellement pas pris en charge.
- Accès des utilisateurs externes à locale ne pas testées les applications.
- Les utilisateurs externes ne sont pas automatiquement nettoyés lorsque l’utilisateur réel est supprimé de son propre répertoire.
- Invitations aux listes de distribution ne sont pas pris en charge.
- Nombre maximal de 2 000 enregistrements peut être téléchargé via CSV.

## <a name="related-articles"></a>Articles connexes
Visitez notre d’autres articles sur la collaboration Azure AD B2B :

- [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence des formats de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
