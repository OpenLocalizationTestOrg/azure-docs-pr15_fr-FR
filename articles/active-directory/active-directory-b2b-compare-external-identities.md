<properties
   pageTitle="Comparaison des fonctionnalités de gestion des identités externes à l’aide d’Azure Active Directory | Microsoft Azure"
   description="Compare collaboration Azure Active Directory B2B, B2C et application client multiples de prise en charge d’authentification et autorisation pour les identités externes"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Comparaison des fonctionnalités de gestion des identités externes à l’aide d’Azure Active Directory

En plus de gestion de l’accès mobile effectifs aux applications SaaS, Azure Active Directory (AD Azure) peut aider votre entreprise à partager des ressources avec des partenaires et fournir des applications aux entreprises et aux particuliers.

## <a name="developing-applications-for-businesses"></a>Développement d’applications pour les entreprises

Vous fournir un service ou une application, tel que le service de paie, pour les entreprises ? Azure AD fournit la plateforme d’identité qui vous permet de créer des applications qui s’intègrent en toute transparence avec des millions d’organisations qui ont déjà configuré Azure AD dans le cadre de déploiement Office 365 ou d’autres services de l’entreprise.

**Exemple :** Distributeur PHARMACEUTIQUE fournit fournitures médicales et des systèmes d’information pour le secteur médical. Il faut une application analytique pratiques médicales et des clients voulus à gérer leurs propres identités de champ. Cette entreprise a choisi Azure AD en tant que la plateforme d’identité pour leurs applications de gestion des exercices pratiques fournissant des identités Azure AD à leurs clients de connexion vers le haut lorsque cela est nécessaire. Pour plus d’informations, voir le [guide du développeur Azure Active Directory](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Activation de l’entreprise partenaire accès à vos ressources d’entreprise

Avez-vous partenaires professionnels ou autres personnes en dehors de votre entreprise qui ont besoin pour accéder à vos ressources d’entreprise, par exemple un site SharePoint ou votre système de planification ? Azure AD permet aux administrateurs octroyer à des utilisateurs externes (qui peuvent exister ou non dans Azure AD) unique se concernant l’accès aux applications d’entreprise. Cela améliore la sécurité comme utilisateurs perdent l’accès lorsqu’ils quittent l’organisation du partenaire, pendant que vous contrôlez les stratégies d’accès au sein de votre organisation. Cela simplifie également l’administration que vous n’avez pas besoin de gérer un partenaire externe répertoire ou par des relations de fédération partenaire.

**Exemple :** Une société d’image propose des revendeurs d’image services photo et fonctionne le plus grand réseau de vente au détail de bornes d’impression. Ils ont choisi Azure AD pour activer des milliers d’utilisateurs à leurs partenaires professionnels de vente au détail à utiliser leurs propres informations d’identification pour télécharger le dernier matériel marketing partenaire et réorganiser fournitures fournisseur de la société extranet de retouche photo. Pour plus d’informations, voir [collaboration Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Développement d’applications pour les consommateurs

Vous devez sécurisée et économique publier des applications en ligne, par exemple une avant de magasin de vente au détail, des millions de consommateurs ? Azure Active Directory offre une plateforme activation sociaux ainsi que de connexion nom d’utilisateur et mot de passe libre-service à l’effigie s’inscrire et réinitialiser le mot de passe libre-service pour les consommateurs de votre application. Cela augmente commodité permettant à vos consommateurs tout en réduisant la charge sur vos développeurs.

**Exemple :** La \#1 sports franchise dans le monde nécessaire à discuter avec ses ventilateurs 450 millions. Pour ce faire, ils générés une application mobile à l’aide d’Azure AD pour le stockage d’authentification et de profil utilisateur. Ventilateurs obtenir enregistrement simplifié et se connecter à l’aide de comptes social tel que Facebook ou ils peuvent utiliser des nom d’utilisateur/mots de passe traditionnels d’une expérience transparente sur iOS, Android et Windows téléphones. S’appuyant sur l’établie plateforme Azure AD réduits code personnalisé tandis que donnant la franchise personnalisé de personnalisation et atténuer les problèmes de sécurité, violations de données et extensibilité élevées. Pour plus d’informations, voir [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Comparaison des fonctionnalités Azure AD

Chacun des scénarios déjà mentionnés dans cet article est adressé par capacités dans Azure AD. Ce tableau doit clarifier les fonctionnalités suivantes sont plus pertinentes pour vous :

| **Tenez compte de ce produit...**       | [Azure AD cliente multiples SaaS application](active-directory-developers-guide.md)    | [Collaboration AD B2B Azure](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Si j’ai besoin fournir...** | un service pour les entreprises | accès du partenaire à mes applications  | un service aux consommateurs |
| **Et je suis similaire à...**  | Pharma distributeur      | Création d’images d’entreprise            | Franchise Sports       |
| **Déploiement d’une application pour...**  | Gestion des exercices pratiques     | Fournisseur extranet          | Ventilateurs de football            |
| **Ciblage...**        | Bureaux médecin        | Partenaires agréés | Toute personne disposant de messagerie      |
| **Accessible quand...**      | Autorisations d’administrateur client | Mes invitations d’administration           | Le consommateur s’inscrit      |
