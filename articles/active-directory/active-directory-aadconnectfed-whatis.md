<properties
    pageTitle="Azure AD Connect et fédération | Microsoft Azure"
    description="Cette page est l’emplacement central pour tous les documents en ce qui concerne les opérations d’AD FS à l’aide de Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect et fédération

Azure AD Connect vous permet de configurer la fédération avec les locaux AD FS et Azure AD. Avec la fédération de connexion, vous pouvez activer les utilisateurs à signer aux services Azure AD en fonction de leur mot de passe en local et, tandis que sur le réseau d’entreprise, sans avoir à entrer leur mot de passe à nouveau. La fédération avec AD FS vous permet déployer une nouvelle ou spécifier un ADFS existant dans la batterie de serveurs Windows Server 2012 R2.

Cette rubrique concerne la page d’accueil pour plus d’informations sur la fédération aux fonctionnalités pour les liens Azure AD Connect et listes de toutes les autres rubriques qui lui sont associés. Pour des liens vers Azure AD Connect, voir l’intégration de vos identités locales avec Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - rubriques de fédération

| Rubrique | Il traite et quand lire |
|:------|:-----------|
| **Azure AD Connect connexion options utilisateur** ||
| [Présentation des options de connexion utilisateur](active-directory-aadconnect-user-signin.md) | Description des options de connexion utilisateur différents et leur incidence expérience utilisateur de connexion Azure |
| **Installation AD FS à l’aide d’Azure AD Connect**||
| [Conditions préalables](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Conditions préalables pour réussir votre installation AD FS via Azure AD Connect|
| [Configurer la batterie de serveurs AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Comment installer une nouvelle batterie AD FS à l’aide d’Azure AD Connect |
| **Modification de la configuration AD FS** | |
| [La réparation de la gestion de la confidentialité](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Comment réparer confiance actuel entre locaux AD FS et Office 365 / Azure |
| [Ajout d’un nouveau serveur AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Développer la batterie de serveurs AD FS avec AD FS server billet initiale installation supplémentaires |
| [Ajout d’un nouveau serveur AD FS WAP](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Développer la batterie de serveurs AD FS avec WAP server billet initiale installation supplémentaires |
| [Ajouter un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Ajouter un autre domaine soit fédéré avec Azure Active Directory |
|**Tâches postérieures à l’installation**||
| [Ajouter le logo de société personnalisé / illustration](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modifier l’expérience de connexion en spécifiant le logo personnalisé qui est affiché dans la page de connexion AD FS |
| [Ajouter une description de connexion](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Modification de la description de connexion dans la page de connexion AD FS | 
| [Modification AD FS réclamer règles](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modifier / ajouter des règles de réclamer dans AD FS correspondant à la configuration de synchronisation Azure AD Connect |


## <a name="additional-resources"></a>Ressources supplémentaires

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
* [Déploiement AD FS Azure](active-directory-aadconnect-azure-adfs.md)
* [Déploiement de FS disponibilité AD croisée géographique dans Azure avec le Gestionnaire de trafic Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


