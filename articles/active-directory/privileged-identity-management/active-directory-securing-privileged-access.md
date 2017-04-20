<properties
    pageTitle="Sécurisation de l’accès privilégié dans Azure AD | Microsoft Azure"
    description="Rubrique explique les approches pour sécuriser l’accès privilégié avec Azure, Azure Active Directory et Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Sécurisation de l’accès privilégié dans Azure Active Directory

Sécurisation de l’accès privilégié est une première étape essentielle pour vous aider à protéger les ressources d’entreprise dans une organisation moderne. Comptes dotés de privilèges sont ceux qui administrer et gérer des systèmes informatiques. Les pirates informatiques ciblent ces comptes pour accéder aux données et les systèmes d’une organisation. Pour sécuriser l’accès dotés de privilèges, vous devez isoler les comptes et les systèmes du risque d’exposition à un utilisateur malveillant.

Nombre d’utilisateurs commencent à obtenir un accès privilégié via les services de cloud. Cela peut inclure un administrateur global d’Office 365, abonnement Azure administrateurs et utilisateurs qui ont un accès administratif dans machines virtuelles ou dans des applications SaaS.

Microsoft recommande que vous suivez ce plan sur la [Sécurisation de l’accès privilégié](https://technet.microsoft.com/library/mt631194.aspx).

Pour les clients utilisant Azure Active Directory pour gérer l’accès à Azure, Office 365, ou d’autres services de Microsoft et les applications, ces principes s’appliquent si les comptes d’utilisateurs sont gérés et authentifiés par Active Directory ou dans Azure Active Directory. Les sections suivantes fournissent plus d’informations sur les fonctionnalités Azure AD pour prendre en charge la sécurisation de l’accès privilégié.

## <a name="multi-factor-authentication"></a>Authentification multifacteur

Pour accroître la sécurité de l’authentification administrateur, vous devez demander l’authentification multifacteur (MFA) avant d’octroyer des privilèges. L’authentification Multifacteur est une méthode de vérification qui vous êtes nécessite l’utilisation de plusieurs uniquement un nom d’utilisateur et un mot de passe. Il propose un second niveau de sécurité pour les compléments d’authentification utilisateur et les transactions.

Azure l’authentification multifacteur permet divulgation d’informations l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il propose une authentification renforcée via une plage d’options de vérification facile, y compris des appels téléphoniques, les messages en texte, les notifications de l’application mobile, ou code de vérification et des jetons de formule 3e partie.

Pour une vue d’ensemble du fonctionnement de l’authentification multifacteur Azure voir la vidéo suivante.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Pour plus d’informations, voir [l’authentification Multifacteur pour Office 365 et l’authentification Multifacteur pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilèges limitées dans le temps

Certaines organisations peuvent estimer qu’ils ont trop d’utilisateurs dans les rôles hautement privilégiés. Un utilisateur peut ont été ajouté au rôle pour une activité donnée, comme à s’inscrire à un service, mais vous n’avez pas utiliser ces privilèges fréquemment par la suite.

Pour réduire le temps d’exposition des privilèges et améliorer votre visibilité dans leur utilisation, limiter les utilisateurs à prendre uniquement sur leurs privilèges juste dans l’heure (juste), lorsque dont elles ont besoin pour effectuer une tâche. Pour Azure Active Directory et Microsoft Online Services, vous pouvez utiliser [Azure AD dotés de privilèges identité gestion](http://aka.ms/AzurePIM).


![Tableau de bord PIM][2]


## <a name="attack-detection"></a>Détection des attaques

[Protection d’identité Azure Active Directory](../active-directory-identityprotection.md) offre une vue globale en risque événements et vulnérabilités potentielles qui peuvent affecter des identités de votre organisation. Protection d’identité en fonction des événements de risque, calcule un niveau de risque d’utilisateur pour chaque utilisateur, vous permettant de configurer des risques stratégies pour protéger automatiquement les identités de votre organisation. Ces politiques, ainsi que d’autres contrôles d’accès conditionnelle fournies par Azure Active Directory et services, peuvent automatiquement bloquer l’utilisateur ou proposer des suggestions qui incluent le mot de passe et l’application de l’authentification multifacteur.

![Protection d’identité AD Azure][3]

## <a name="conditional-access"></a>Accès conditionnel

Contrôle d’accès conditionnelle, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification d’un utilisateur, avant d’autoriser l’accès à une application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.


![Définition des règles d’accès conditionnel avec l’authentification Multifacteur][4]


## <a name="related-articles"></a>Articles connexes

- Activer [l’authentification multifacteur Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Activer [la gestion des identités Azure AD privilégié](../active-directory-privileged-identity-management-configure.md)
- Activer la [Protection d’identité AD Azure](../active-directory-identityprotection.md)
- Activer les [contrôles d’accès conditionnel](../active-directory-conditional-access.md)


Pour plus d’informations sur la création d’une feuille de route globale de la sécurité, consultez la section « responsabilités du client et feuille de route » du document [Microsoft Cloud Security pour Enterprise Architects](http://aka.ms/securecustomer) . Pour plus d’informations sur attrayants services Microsoft pour vous aider à ces rubriques, contactez votre représentant Microsoft ou visitez la [page solutions de sécurité](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
