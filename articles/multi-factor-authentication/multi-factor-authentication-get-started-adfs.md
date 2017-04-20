<properties
    pageTitle="Azure FS l’authentification Multifacteur et AD | Microsoft Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui explique comment commencer à utiliser l’authentification Multifacteur Azure et AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Prise en main l’authentification multifacteur Azure et Active Directory Federation Services



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si votre organisation a fédéré votre locale d’Active Directory avec Azure Active Directory via AD FS, il existe deux possibilités pour l’utilisation de l’authentification multifacteur Azure.

- Sécuriser les ressources de cloud à l’aide de l’authentification multifacteur Azure ou Active Directory Federation Services
- Sécuriser les ressources de cloud et en local à l’aide de serveur de l’authentification multifacteur Azure

Le tableau suivant résume l’expérience de vérification entre sécurisation des ressources avec l’authentification multifacteur Azure et AD FS

|Expérience de vérification - applications basées sur Parcourir|Expérience de vérification - applications Non basés sur un navigateur
:------------- | :------------- | :------------- |
Sécuriser les ressources Azure AD à l’aide de l’authentification multifacteur Azure |<li>La première étape de vérification est exécutée en local via AD FS.</li> <li>La deuxième étape est une méthode basée sur le téléphone effectuée à l’aide de l’authentification cloud.</li>|Utilisateurs finaux peuvent utiliser les mots de passe application à se connecter.
Sécurisation des ressources Azure AD à l’aide d’Active Directory Federation Services |<li>La première étape de vérification est exécutée en local via AD FS.</li><li>La deuxième étape est exécutée en local par en respectant la demande.</li>|Utilisateurs finaux peuvent utiliser les mots de passe application à se connecter.

Avertissements par mot de passe de l’application pour les utilisateurs fédérés :

- Les mots de passe application sont vérifiées à l’aide de l’authentification cloud, afin qu’ils ignorer la fédération. Fédération est uniquement connue lors de la configuration d’un mot de passe d’application.
- Paramètres de contrôle d’accès Client local ne sont pas respectés par les mots de passe application.
- Vous perdez locaux fonctionnalité d’enregistrement de l’authentification des mots de passe d’application.
- Désactiver/suppression du compte peut prendre jusqu'à trois heures pour la synchronisation d’annuaires, retarder désactiver/suppression de mots de passe d’application dans l’identité du cloud.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la configuration de l’authentification multifacteur Azure ou le serveur de l’authentification multifacteur Azure avec AD FS, consultez les articles suivants :

- [Sécuriser les ressources de cloud à l’aide de l’authentification multifacteur Azure et AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Sécuriser les ressources de cloud et en local à l’aide de serveur de l’authentification multifacteur Azure avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Sécuriser les ressources de cloud et en local à l’aide de serveur de l’authentification multifacteur Azure avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
