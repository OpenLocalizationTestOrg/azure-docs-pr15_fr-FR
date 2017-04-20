<properties
    pageTitle="Vulnérabilités détectées par Azure Active Directory identité Protection | Microsoft Azure"
    description="Vue d’ensemble des problèmes détectés par la Protection Azure Active Directory identité."
    services="active-directory"
    keywords="protection d’identité Azure AD, la découverte application cloud, gestion des applications, sécurité, risque, le niveau de risque, vulnérabilité, stratégie de sécurité"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnérabilités détectées par Azure Active Directory identité Protection 

Vulnérabilités sont faiblesses dans votre environnement qui peuvent être exploitées par un utilisateur malveillant. Nous vous recommandons de résoudre ces problèmes pour améliorer la sécurité de votre organisation et d’empêcher les pirates d’exploiter les.
<br><br>
![vulnérabilités](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

Les sections suivantes vous fournissent une vue d’ensemble des problèmes de sécurité signalés par la Protection d’identité.

## <a name="multi-factor-authentication-registration-not-configured"></a>Inscription de l’authentification multifacteur ne pas configurée 

Celle-ci permet de contrôler le déploiement de l’authentification multifacteur Azure dans votre organisation. 

L’authentification multifacteur Azure fournit un second niveau de sécurité pour l’authentification des utilisateurs. Il vous aide à protéger l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il propose une authentification renforcée via une plage d’options de vérification facile — appel téléphonique, un message texte ou l’application mobile notification ou la vérification de code et 3e partie formule jetons.

Nous vous recommandons d’exiger l’authentification multifacteur Azure pour les compléments d’authentification utilisateur. L’authentification multifacteur joue un rôle clé dans les stratégies d’accès conditionnelle risque disponibles via la Protection d’identité.

Pour plus d’informations, voir [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Applications cloud non géré

Celle-ci permet d’identifier les applications cloud non géré dans votre organisation.
 
Dans les entreprises modernes, aux services informatiques connaissent pas souvent de toutes les applications cloud utilisateurs de leur organisation sont à l’aide de leur travail. Il est facile de voir pourquoi administrateurs auront inquiétudes tout accès non autorisé aux données d’entreprise, fuite éventuelle de données et aux autres risques de sécurité. 

Nous vous recommandons que votre organisation déploie découverte d’application Cloud pour découvrir les applications en nuage non géré et pour gérer ces applications à l’aide d’Azure Active Directory.

Pour plus d’informations, voir [trouver les applications en nuage non géré avec la découverte de l’application Cloud](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Alertes de sécurité à partir de la gestion des identités dotés de privilèges

Celle-ci permet de découvrir et résoudre les alertes sur les identités dotés de privilèges dans votre organisation.  

Pour permettre aux utilisateurs d’effectuer les opérations dotés de privilèges, les organisations doivent octroyer un accès privilégié utilisateurs permanents ou temporaires dans Azure Active Directory, aux ressources Azure ou Office 365 ou autres applications SaaS. Chacun de ces utilisateurs dotés de privilèges augmente la surface d’attaque de votre organisation. Celle-ci vous aide à identifier les utilisateurs avec un accès privilégié inutile et effectuez l’action appropriée pour réduire ou éliminer le risque qu’ils représentent. 

Nous vous recommandons que votre organisation utilise la gestion des identités Azure AD dotés de privilèges pour gérer, de contrôler et surveiller privilégié identités et leur accès aux ressources dans Active Directory Azure ainsi que d’autres services en ligne Microsoft, tels que Office 365 ou Microsoft Intune.

Pour plus d’informations, voir [Gestion des identités Azure AD dotés de privilèges](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Voir aussi

 - [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)
