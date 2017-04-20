<properties
    pageTitle="Comment obtenir un client Azure AD | Microsoft Azure"
    description="Comment obtenir un client Azure Active Directory pour l’enregistrement et la création d’applications."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Comment obtenir un client Azure Active Directory

Dans Azure Active Directory (AD Azure), un [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) est représentant d’une organisation.  Il est une instance dédiée du service Azure AD qui une organisation reçoit et possède lorsqu’il s’inscrit à un service de nuage Microsoft comme Azure, Intune Microsoft ou Office 365.  Chaque client Azure AD est distinct et séparé à partir d’autres clients Azure AD.  

Un client héberge les utilisateurs de la société et les informations sur les - leurs mots de passe, données de profil utilisateur, autorisations et ainsi de suite.  Il contient également des groupes, applications et autres informations relatives à une organisation et sa sécurité.

Pour permettre aux utilisateurs Azure AD pour vous connecter à votre application, vous devez enregistrer votre application dans un client de votre choix.  Publication d’une application d’un client Azure AD est **totalement gratuit**.  En fait, la plupart des développeurs créera plusieurs clients et applications expérimenter, développement, mise en attente et à des fins de test.  Organisations qui vous inscrire à et utiliser votre application peuvent choisir d’acheter des licences s’ils souhaitent tirer parti des fonctionnalités avancées d’annuaire.

Par conséquent, comment procéder sur l’obtention d’un client Azure AD ?  Ce processus peut être un peu if différent vous :

- [Ont un abonnement Office 365](#use-an-existing-office-365-subscription)
- [Disposez d’un abonnement existant Azure associé à un Account Microsoft](#use-an-msa-azure-subscription)
- [Disposez d’un abonnement existant Azure associé à un compte professionnel](#use-an-organizational-azure-subscription)
- [Aucune des réponses précédentes ont et voulez commencer à partir de zéro](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Utiliser un abonnement Office 365
Si vous avez un abonnement Office 365, vous disposez déjà d’un client Azure AD ! Vous pouvez vous connecter au [portail Azure](https://portal.azure.com) avec votre compte Office 365 et commencer à utiliser Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Utiliser un abonnement MSA Azure
Si vous êtes déjà inscrit pour un abonnement Azure avec votre Account Microsoft individuels, vous possédez déjà un client !  Lorsque vous connecter au [Portail Azure](https://portal.azure.com), vous serez automatiquement connecté à votre client par défaut. Vous pouvez utiliser ce client selon vos besoins - mais que vous souhaitiez créer un compte d’administrateur d’organisation.

Pour ce faire, procédez comme suit.  Par ailleurs, vous souhaiterez peut-être créez un nouveau client et un administrateur dans ce client à suivre un processus similaire.

1.  Se connecter au [Portail Azure](https://portal.azure.com) avec votre compte individuel
2.  Accédez à la section « Azure Active Directory » du portail (figurant dans la barre de navigation gauche, sous **Autres Services**)
3.  Vous devez automatiquement être connecté à « Répertoire par défaut », dans le cas contraire vous pouvez basculer des répertoires en cliquant sur le nom de votre compte dans le coin supérieur droit.
4.  Dans la section **Tâches rapides** , cliquez sur **Ajouter un utilisateur**.
5.  Dans le formulaire utilisateur Ajouter, fournissent les informations suivantes :

    - Nom : (choisissez une valeur appropriée)
    - Nom d’utilisateur : (choisissez un nom d’utilisateur pour cet administrateur)
    - Profil : (renseigner les valeurs appropriées pour le prénom, nom, fonction et service)
    - Rôle : Administrateur Global

6.  Lorsque vous avez rempli le formulaire utilisateur Ajouter et recevez le mot de passe temporaire pour le nouvel utilisateur d’administration, veillez à enregistrer ce mot de passe que vous devez ouvrir une session avec ce nouvel utilisateur afin de modifier le mot de passe. Vous pouvez également envoyer le mot de passe directement à l’utilisateur, à l’aide d’un autre message électronique.
7.  Cliquez sur **créer** pour créer le nouvel utilisateur.
8.  Pour modifier le mot de passe temporaire, connectez-vous à [https://login.microsoftonline.com](https://login.microsoftonline.com) avec ce nouveau compte d’utilisateur et modifier le mot de passe lors d’une demande.


## <a name="use-an-organizational-azure-subscription"></a>Utiliser un abonnement Azure d’organisation
Si vous êtes déjà inscrit pour un abonnement Azure avec votre compte professionnel, vous possédez déjà un client !  Dans le [Portail Azure](https://portal.azure.com), vous devez trouver un client lorsque vous naviguez sur « Plus Services » et « Azure Active Directory. »  Vous pouvez utiliser ce client selon vos besoins. 


## <a name="start-from-scratch"></a>Démarrer à partir de zéro
Si tous les éléments ci-dessus est incompréhensible pour vous, ne vous inquiétez pas.  Visitez simplement [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) pour vous inscrire Azure avec une organisation.  Une fois que vous avez terminé le processus, vous aurez votre propre client Azure AD avec le nom de domaine que vous avez choisi lors de l’inscription vers le haut.  Dans le [Portail Azure](https://portal.azure.com), vous pouvez trouver votre client en accédant à « Azure Active Directory » dans la NAV gauche.

Dans le cadre du processus d’inscription pour Azure, vous devrez fournir les informations de carte de crédit.  Vous pouvez procéder en toute sécurité - vous n'êtes pas facturée pour publier des applications dans Azure AD ou la création de nouveaux clients.
