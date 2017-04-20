<properties
    pageTitle="Configuration de la Azure AD joindre pour vos utilisateurs | Microsoft Azure"
    description="Explique comment les administrateurs peuvent configurer Azure AD joindre d’annuaire local et d’enregistrement de périphérique."
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>La configuration d’Azure AD rejoindre dans votre organisation

Avant de configurer Azure Active Directory rejoindre (Azure AD rejoindre), vous devez créer manuellement les comptes gérés dans Azure AD ou synchroniser votre annuaire local d’utilisateurs dans le cloud.

Obtenir des instructions détaillées pour la synchronisation de vos utilisateurs locaux vers Azure Active Directory est traité lors de [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).


Pour créer et gérer les utilisateurs dans Azure AD manuellement, reportez-vous à [Gestion des utilisateurs dans Azure Active Directory](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurer l’enregistrement de dispositifs
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet **annuaire** , sélectionnez votre répertoire.
4. Sélectionnez l’onglet **configurer** .
5. Accédez à la section **Devices** .
6. Sous l’onglet **périphériques** , définissez les éléments suivants :  
   * **Maximale nombre d’appareils par utilisateur**: sélectionnez le nombre maximal de périphériques qu’un utilisateur dans Active Directory Azure.  Si un utilisateur atteint ce quota, ils ne seront pas en mesure d’ajouter des périphériques supplémentaires jusqu'à ce qu’une ou plusieurs des leurs appareils existants sont supprimés.
   * **Exiger MULTIFACTEUR AUTH pour participer à des appareils**: définir si les utilisateurs doivent fournir un second facteur d’authentification pour participer à leur appareil à Azure Active Directory. Pour plus d’informations sur l’authentification multifacteur Azure, voir [prise en main de l’authentification multifacteur Azure dans le cloud](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **Les utilisateurs peuvent AZURE AD jointure appareils**: sélectionnez les utilisateurs et groupes qui sont autorisés à participer à des appareils à Azure Active Directory.
   * **Autres administrateurs ON AZURE AD rejoint appareils**: avec Azure AD Premium ou la Suite de mobilité Enterprise (EM), vous pouvez choisir les utilisateurs disposez des droits d’administrateur local à l’appareil. Les administrateurs globaux et propriétaires de l’appareil sont accordées droits d’administrateur local par défaut.

<center>![Configurer le périphérique enregistrement](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Après avoir configuré Azure AD joindre pour vos utilisateurs, ils peuvent se connecter à Active Directory Azure via leurs périphériques d’entreprise ou personnels.

Voici les trois scénarios, que vous pouvez utiliser pour permettre à vos utilisateurs à configurer Azure AD rejoindre :

- Les utilisateurs rejoindre un appareil société propriétaire directement à Azure AD.
- Un périphérique appartenant à l’entreprise dans Active Directory local de jointure de domaines utilisateurs puis étendre le périphérique à Azure Active Directory.
- Utilisateurs ajouter des comptes professionnel ou scolaire à Windows sur un appareil personnel

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
