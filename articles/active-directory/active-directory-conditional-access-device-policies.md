<properties
    pageTitle="Les stratégies d’appareil conditionnelle accès pour les services Office 365 | Microsoft Azure"
    description="Plus d’informations sur les conditions basées sur les périphériques contrôlent l’accès aux services Office 365. Tandis que les travailleurs de l’Information (décisionnelle) choix pour accéder aux Office 365 services tels que Exchange et SharePoint Online au travail ou établissement scolaire dans leurs appareils personnels, votre administrateur informatique souhaite que le niveau d’accès à être administrateurs secure.IT peuvent prévoir les stratégies d’appareil accès conditionnelle pour sécuriser les ressources d’entreprise, tout en permettant sur appareils compatibles pour accéder aux services."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Stratégies d’appareil conditionnelle accès pour les services Office 365

Le terme « accès conditionnel » comporte plusieurs conditions associées comme utilisateur authentifié multifacteur, authentifié périphérique, périphérique compatible etc.. Cette rubrique focusses principalement sur conditions des périphériques pour contrôler l’accès aux services Office 365. Tandis que les travailleurs de l’Information (décisionnelle) choix pour accéder aux Office 365 services tels que Exchange et SharePoint Online au travail ou établissement scolaire dans leurs appareils personnels, votre administrateur informatique souhaite que le niveau d’accès sécurisé. Les administrateurs informatiques peuvent prévoir les stratégies d’appareil accès conditionnelle pour sécuriser les ressources d’entreprise, tout en permettant sur appareils compatibles pour accéder aux services. Les stratégies d’accès conditionnelle vers Office 365 peuvent être configurés à partir du portail d’accès conditionnel Intune Microsoft.

Azure Active Directory applique les stratégies d’accès conditionnel pour sécuriser l’accès aux services Office 365. Administrateur client peut créer une stratégie d’accès conditionnelle qui bloque un utilisateur sur un appareil non conformes d’accéder à un service d’Office 365. L’utilisateur doit être conformes aux stratégies de périphérique société avant d’accès au service. Vous pouvez également cliquer, l’administrateur peut également créer une stratégie nécessitant que les utilisateurs à simplement inscrire leur appareil pour accéder à un service Office 365. Stratégies peuvent être appliquées aux tous les utilisateurs d’une organisation, ou limités à quelques groupes cibles et améliorés au fil du temps pour inclure des groupes cible supplémentaire.

Requis pour appliquer des stratégies de l’appareil est destiné aux utilisateurs d’enregistrer leurs périphériques sur service Azure Active Directory périphérique d’enregistrement. Vous pouvez choisir d’activer l’authentification multifacteur (MFA) pour l’enregistrement des appareils mobiles avec service Azure Active Directory appareil d’enregistrement. L’authentification Multifacteur est recommandée pour service Azure Active Directory périphérique d’enregistrement. Lorsque l’authentification Multifacteur est activée, les utilisateurs inscrire leur appareil avec service Azure Active Directory appareil d’enregistrement sont invités à deuxième authentification multifacteur fournir.

##<a name="how-does-conditional-access-policy-work"></a>Comment fonctionne la stratégie d’accès conditionnelle ?

Lorsqu’un utilisateur demande l’accès au service Office 365 à partir d’une plateforme appareil pris en charge, Azure Active Directory authentifie l’utilisateur et l’appareil à partir duquel l’utilisateur lance la demande ; et accorde l’accès au service uniquement lorsque l’utilisateur est conforme à la stratégie définie pour le service. Les utilisateurs qui n’ont pas de leur appareil inscrit figurent instructions réparation comment s’inscrire et devenir compatibles pour accéder aux services Office 365 entreprises. Les utilisateurs sur des appareils iOS et Android seront invités à inscrire leur appareil utilisant application portail d’entreprise. Lorsqu’un utilisateur s’inscrit son appareil, l’appareil est enregistré avec Azure Active Directory et inscrit pour la conformité et gestion des appareils. Les clients doivent utiliser le service Azure Active Directory d’inscription pour appareils conjointement avec Microsoft Intune pour activer la gestion des appareils mobiles pour Office 365 service. Inscription d’un appareil est requis pour les utilisateurs d’accéder aux Office 365 services lorsque les stratégies de l’appareil sont appliqués.

Lorsqu’un utilisateur s’inscrit son appareil avec succès, le périphérique devient approuvé. Azure Active Directory offre unique-authentification pour accéder aux applications de société et applique la stratégie d’accès conditionnel pour accorder l’accès à un horaire de service pas uniquement la première l’utilisateur demande l’accès, mais chaque fois que l’utilisateur demande pour procéder au renouvellement access. Accès aux services sera refusé l’utilisateur lorsque les informations d’identification de connexion sont modifiées, appareil est perdu/de vol ou la stratégie n’est pas remplie au moment de la demande de renouvellement.

## <a name="deployment-considerations"></a>Considérations relatives au déploiement :
Devez utiliser service Azure Active Directory d’inscription pour appareils d’enregistrement de l’appareil.

Lorsque les utilisateurs doivent être authentifiés en local, Active Directory Federation Services (AD FS) (1.0 et versions ultérieures) est requis. L’authentification multifacteur pour participer à une espace de travail échoue quand le fournisseur d’identité n’est pas capable de l’authentification multifacteur. Par exemple, AD FS 2.0 n’est pas multifacteur prenant en charge l’authentification. Administrateur client doit s’assurer que les locaux AD FS est l’authentification Multifacteur capable et une méthode de l’authentification Multifacteur valide est activée, avant d’activer l’authentification Multifacteur sur service Azure Active Directory périphérique d’enregistrement. Par exemple, AD FS sur Windows Server 2012 R2 dispose de fonctionnalités de l’authentification Multifacteur. Vous devez également activer une méthode d’authentification valides supplémentaires (MFA) sur le serveur AD FS avant d’activer l’authentification Multifacteur sur service Azure Active Directory périphérique d’enregistrement. Pour plus d’informations sur les méthodes de l’authentification Multifacteur prises en charge dans AD FS, voir Configurer les autres méthodes d’authentification pour AD FS.

## <a name="frequently-asked-questions-faq"></a>Forum aux Questions (FAQ)

Q : quelle est la stratégie d’exclusion par défaut pour les plateformes d’appareil non pris en charge ?

R : à l’heure actuelle, les stratégies d’accès conditionnelle sont sélectivement appliquées aux utilisateurs sur des appareils iOS et Android. Les applications sur d’autres plateformes d’appareil sont, par défaut, ne sont pas affectée par la stratégie d’accès conditionnel pour appareils iOS et Android. Administrateur client peut, cependant, choisir de remplacer la stratégie globale pour interdire l’accès aux utilisateurs sur les plateformes non prises en charge.
Il se trouve sur la feuille de route pour étendre la stratégie d’accès conditionnelle aux utilisateurs sur d’autres plateformes, y compris des fenêtres.

Q : lorsque sera stratégie d’accès conditionnelle aux services Office 365 étendu aux applications basé sur le navigateur (par exemple, OWA, SharePoint via un navigateur).

R : à l’heure actuelle, accès conditionnel aux services Office 365 est limité aux applications riches sur appareil. Il se trouve sur la feuille de route pour étendre la stratégie d’accès conditionnelle aux utilisateurs l’accès à des services à partir de navigateurs.
