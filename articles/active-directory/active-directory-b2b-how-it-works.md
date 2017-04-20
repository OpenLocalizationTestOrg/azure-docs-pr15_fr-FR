<properties
   pageTitle="Aperçu de collaboration Azure AD B2B : comment cela fonctionne | Microsoft Azure"
   description="Décrit comment Azure Active Directory B2B collaboration prend en charge des relations de votre société croisée en activant partenaires professionnels de manière sélective accéder à vos applications d’entreprise"
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

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Aperçu de collaboration Azure AD B2B : mode de fonctionnement
Collaboration AD B2B Azure est basé sur une invitation et échanger modèle. Vous fournir les adresses de messagerie des parties que vous souhaitez utiliser, ainsi que les applications que vous souhaitez utiliser. Azure AD les envoie une invitation par courrier électronique avec un lien. L’utilisateur partenaire suit le lien et est invité à se connecter à l’aide de leur compte Azure AD ou signe pour une nouvelle annonce Azure du compte.

1. Votre administrateur invite utilisateurs partenaire en téléchargeant [un fichier .csv structurées](active-directory-b2b-references-csv-file-format.md) à l’aide du portail Azure.
2. Le portail envoie invite des messages électroniques à ces utilisateurs partenaire.
3. Les utilisateurs partenaire cliquez sur le lien dans le message électronique et êtes invités à se connecter à l’aide de leurs informations d’identification de travail (si elles sont déjà présentes dans Azure AD), ou pour vous inscrire en tant qu’utilisateur collaboration Azure AD B2B.
4. Les utilisateurs partenaire sont redirigés vers l’application qu’ils ont été invitées à, où ils ont accès.

## <a name="directory-operations"></a>Opérations d’annuaire
Les utilisateurs partenaire existent dans votre annonce Azure en tant qu’utilisateurs externes. Cela signifie que votre administrateur peut configurer des licences, attribuer l’appartenance aux groupes et plus accorder l’accès aux applications d’entreprise via le portail Azure ou à l’aide de PowerShell Azure comme pour les utilisateurs de votre entreprise.

Lors d’une annonce Azure payant abonnement (Basic ou Premium) n’est pas nécessaire d’utiliser Azure AD B2B, les clients qui possèdent une payant abonnement Azure AD (Basic ou Premium) recevoir les avantages supplémentaires suivants :

 - Les administrateurs peuvent attribuer les groupes aux applications, prévoyant simplification de la gestion de l’accès des utilisateurs invités.
 - Administrateur client de personnalisation est utilisée pour personnaliser les messages électroniques invitation et expérience de remboursement, fournir plus de contexte pour invités utilisateurs partenaire.

## <a name="related-articles"></a>Articles connexes
 Parcourir notre d’autres articles sur la collaboration Azure AD B2B

 - [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
 - [Référence des formats de fichier CSV](active-directory-b2b-references-csv-file-format.md)
 - [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
 - [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitations aperçu en cours](active-directory-b2b-current-preview-limitations.md)
 - [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
