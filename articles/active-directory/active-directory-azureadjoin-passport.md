<properties
    pageTitle="Authentification des identités sans mot de passe via Microsoft Passport | Microsoft Azure"
    description="Fournit une vue d’ensemble de Microsoft Passport et des informations supplémentaires sur le déploiement Microsoft Passport."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Authentification des identités sans mot de passe via Microsoft Passport

Les méthodes d’authentification par mot de passe uniquement en cours ne sont pas suffisantes pour empêcher les utilisateurs approuvés. Utilisateurs réutiliser et oublient pas les mots de passe. Les mots de passe sont pouvant, phishable, propice aux fissures et à deviner. Ils obtiennent également difficile à mémoriser et propice aux attaques de type «[passer du hachage](https://technet.microsoft.com/dn785092.aspx)».

## <a name="about-microsoft-passport"></a>À propos de Microsoft Passport
Microsoft Passport est une clé publique/privée ou une approche de l’authentification par certificat pour les entreprises et les consommateurs qui dépasse les mots de passe. Cette forme d’authentification repose sur les informations d’identification paire de clés qui peuvent remplacer les mots de passe et qui sont résistant aux violations, vols et le hameçonnage.

 Microsoft Passport permet à un utilisateur de s’authentifier à un compte Microsoft, un compte Active Directory de Windows Server, un compte Microsoft Azure Active Directory (AD Azure) ou un service non Microsoft qui prend en charge l’authentification rapidement identité en ligne (Rex). Après une vérification en deux étapes initial lors de l’inscription de Microsoft Passport, Microsoft Passport a été configurée sur périphérique de l’utilisateur et l’utilisateur définit un mouvement, qui peut être Windows Hello ou un code confidentiel. L’utilisateur fournit le mouvement afin de vérifier son identité. Windows utilise ensuite Microsoft Passport pour authentifier l’utilisateur et les aider à accéder aux services et ressources protégés.

La clé privée est rendue disponible uniquement via un mouvement « utilisateur » comme un code confidentiel, biométrie ou un périphérique distant comme une carte à puce par l’utilisateur pour se connecter à l’appareil. Ces informations sont liées à un certificat ou une paire de clés asymétrique. La clé privée est matériel attestée si le périphérique possède une puce de Module (plateforme sécurisée). La clé privée quitte jamais le périphérique.

La clé publique est enregistrée avec Azure Active Directory et Windows Server Active Directory (pour en local). Fournisseurs d’identité (IDPs) valider l’utilisateur en mappant la clé publique de l’utilisateur à la clé privée et fournissent des informations de connexion via une fois votre mot de passe unique, PhoneFactor ou un mécanisme de notification différents.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Pourquoi les entreprises doivent adopter Microsoft Passport

En activant Microsoft Passport, les entreprises peuvent renforcer leurs ressources même par :

* La configuration de Microsoft Passport avec une option préféré matériel. Cela signifie que clés seront générées sur plateforme sécurisée 1.2 ou 2.0 plateforme sécurisée lorsqu’elles sont disponibles. Lors de la plateforme sécurisée n’est pas disponible, logiciel génère la clé.

* Définition de la complexité et la longueur du code confidentiel, et l’utilisation de Bonjour est activée ou non dans votre organisation.

* Configuration de Microsoft Passport pour prendre en charge les scénarios j’aime une carte à puce à l’aide de confiance basés sur le certificat.

## <a name="how-microsoft-passport-works"></a>Fonctionnement de Microsoft Passport
1. Clés sont générées par le module de plateforme sécurisée ou logiciels sur le matériel. Grand nombre d’appareils ont une puce plateforme sécurisée intégrée qui permet de sécuriser le matériel via l’intégration des clés de chiffrement dans appareils. Module de plateforme sécurisée 1.2 ou 2.0 plateforme sécurisée génère clés ou certificats créés à partir des clés générées.

2. Le module atteste ces touches liées aux matériel.

3. Un mouvement de déverrouiller unique déverrouille l’appareil. Cette entrée permet d’accéder à plusieurs ressources si le périphérique est à un domaine ou Azure AD-rejoint.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Fonctionne de la politique de Microsoft Passport

![Cycle de vie Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Le diagramme précédent illustre la paire de clés publique/privée et la validation par le fournisseur d’identité. Chacune de ces étapes est décrite en détail ici :

1. L’utilisateur s’avère son identité via plusieurs méthodes de vérification linguistiques intégrés (mouvements, physique des cartes à puce, l’authentification multifacteur) et envoie ces informations à un fournisseur identité (IDP) comme Azure Active Directory ou Active Directory local.

2. Le périphérique puis crée la clé atteste la clé, prend la partie de cette clé publique, attache avec les instructions de poste, se connecte et envoie à la IDP pour enregistrer la clé.

4. Dès que la IDP enregistre la partie de la clé publique, la IDP défis l’appareil pour vous connecter avec la partie de la clé privée.

5. La IDP puis valide et émet le jeton d’authentification qui vous permet de l’utilisateur et l’accès des appareils les ressources protégées. IDPs pouvez écrire des applications de disponibilité sur plusieurs plateformes ou navigateurs pris en charge (via des API JavaScript/Webcrypto) permet de créer et utiliser des informations d’identification Microsoft Passport pour leurs utilisateurs.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>La configuration requise de déploiement pour Microsoft Passport
### <a name="at-the-enterprise-level"></a>Au niveau de l’entreprise

* L’entreprise a un abonnement Azure.

### <a name="at-the-user-level"></a>Au niveau utilisateur

* Ordinateur de l’utilisateur s’exécute Windows 10 Professional ou entreprise.

Pour obtenir des instructions détaillées de déploiement, voir [Activer Microsoft Passport pour le travail de l’organisation](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Informations supplémentaires

* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
