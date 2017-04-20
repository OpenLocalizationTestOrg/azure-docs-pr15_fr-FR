<properties
   pageTitle="Azure Active Directory B2B collaboration | Microsoft Azure"
   description="Azure Active Directory B2B collaboration permet aux partenaires professionnels pour accéder à vos applications d’entreprise, avec chacun de ses utilisateurs représentés par une annonce Azure du compte"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B collaboration

Azure Active Directory (AD Azure) B2B collaboration vous permet d’activer l’accès à vos applications d’entreprise des identités gérés par des partenaires. Vous pouvez créer des relations de société croisée en invitant et autoriser les utilisateurs à partir de partenaires accéder à vos ressources. La complexité est réduite, car chaque société se fédère une seule fois avec Azure Active Directory et de chaque utilisateur est représenté par un seul compte Azure Active Directory. Si vos partenaires professionnels gérer leurs comptes dans Azure Active Directory, car l’accès est révoqué lorsque les utilisateurs partenaire sont terminées à partir de leur organisation et accès involontaire via l’appartenance à répertoires internes ne peut pas la sécurité est augmentée. Pour les partenaires professionnels qui n’ont pas déjà Azure AD, collaboration B2B a une expérience agréable d’abonnement pour fournir les comptes Azure Active Directory à vos partenaires professionnels.

-   Vos partenaires professionnels utilisent leurs propre connexion informations d’identification, qui vous évite de la gestion d’un répertoire des partenaires externes et à partir de la nécessité de supprimer l’accès, les utilisateurs ont quitté l’organisation du partenaire.

-   Vous gérez l’accès à vos applications indépendamment de celles du cycle de vie compte de votre partenaire d’entreprise. Par exemple, cela signifie que vous pouvez révoquer l’accès sans avoir à demander le service informatique de votre partenaire commercial rien à faire.

## <a name="capabilities"></a>Fonctionnalités

Collaboration B2B simplifie la gestion et améliore la sécurité d’accès du partenaire à des ressources d’entreprise, notamment applications SaaS par le cloud Office 365, Salesforce, Azure Services et chaque mobile et d’application prenant en charge les revendications en local. B2B collaboration permet aux partenaires gérer leurs propres comptes et les entreprises peuvent appliquer des stratégies de sécurité à accès du partenaire.

Azure B2B annuaire Active collaboration est facile à configurer avec simplifié d’abonnement pour les partenaires de toutes tailles même si elle n’a pas leurs propres Azure Active Directory via un processus de vérification de messagerie. Il est également facile à mettre à jour avec aucun des annuaires externes ou par configurations des partenaires de fédération.

## <a name="b2b-collaboration-process"></a>Processus de collaboration B2B

1. Azure AD B2B collaboration permet à un administrateur de société inviter et autoriser un ensemble d’utilisateurs externes en téléchargeant un fichier de valeurs séparées par des virgules (CSV) de 2000 pas plus de lignes vers le portail de collaboration B2B.

  ![Boîte de dialogue Téléchargement de fichier CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. Le portail vous envoie des invitations électroniques à ces utilisateurs externes.

3. L’utilisateur invité sera soit se connecter à un compte de travail existant avec Microsoft (géré dans Azure AD), ou obtenez un nouveau compte de travail dans Azure Active Directory.

4. Une fois connecté, l’utilisateur sera redirigé à l’application qui a été partagée avec eux.

Invitations aux adresses de messagerie consommateur (par exemple, Gmail ou [*comcast.net*](http://comcast.net/)) ne sont actuellement pas pris en charge.

Pour plus d’informations sur le fonctionne de la collaboration B2B, consultez [cette vidéo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Étapes suivantes
Parcourir notre d’autres articles sur la collaboration Azure AD B2B.

- [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence des formats de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitations aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
