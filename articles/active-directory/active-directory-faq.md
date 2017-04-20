<properties
    pageTitle="Forum aux questions sur Azure Active Directory | Microsoft Azure"
    description="Azure Active Directory Forum aux questions qui répond aux questions conjointement avec l’accès aux Azure et Azure Active Directory, accès de gestion et l’application de mot de passe."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Forum aux questions sur Azure Active Directory

Azure Active Directory est une identité complète comme une solution de Service (IDaaS) qui englobe tous les aspects d’identité, gestion de l’accès et la sécurité.


Pour plus d’informations, voir [Nouveautés Azure Active Directory ?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>Accéder à Azure et Azure Active Directory


**Q : pourquoi je reçois « aucun abonnement ne trouvé » lorsque j’essaie d’accéder à Azure AD dans le portail classique Azure (https://manage.windowsazure.com) ?**

**A:** Accéder au portail classique Azure, chaque utilisateur doit disposer d’autorisations sur un abonnement Azure. Si vous avez un Office 365 payant ou Azure AD atteindre [http://aka.ms/accessAAD](http://aka.ms/accessAAD) pour une étape d’activation, sinon vous devez activer un complète [Azure d’évaluation](https://azure.microsoft.com/pricing/free-trial/) ou un abonnement payant. 

Pour plus d’informations, voir :

- [Comment les abonnements Azure sont associées à Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Gérer l’annuaire pour votre abonnement Office 365 dans Azure](active-directory-manage-o365-subscription.md)

---

**Q : quelle est la relation entre Azure AD, Office 365 et Azure ?**

**A:** Azure Active Directory vous propose des possibilités d’identité et d’accès communes à tous les services en ligne Microsoft. Si vous utilisez Office 365, Microsoft Azure, Intune ou autres personnes, vous utilisez déjà une annonce Azure pour activer l’authentification et accéder à la gestion de tous ces services. 

En fait, tous les utilisateurs que vous avez activé pour Microsoft Online services sont définies en tant que comptes d’utilisateur dans une ou plusieurs instances Azure AD. Vous pouvez activer ces fonctionnalités comptes gratuitement Azure AD, telles que l’accès au cloud application.
 
En outre, Azure AD payé services (par exemple : Azure AD basic, Premium, services, etc.) compléter autres services tels que Office 365 et Microsoft Azure avec les solutions de gestion et la sécurité échelle entreprise complète en ligne.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Prise en main hybride Azure AD


**Q : Comment puis-je me connecter ma directory local Azure AD ?**

**A:** Vous pouvez vous connecter à votre annuaire local vers Azure Active Directory à l’aide **d’Azure AD Connect**. 

Pour plus d’informations, voir [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).


---

**Q : comment configurer l’authentification unique entre mon annuaire local et mes applications cloud ?**

**A:** Vous devez uniquement configurer l’authentification unique entre votre annuaire local et Azure AD. Dans la mesure où vous accédez à vos applications cloud via Azure AD, le service automatiquement les lecteurs vos utilisateurs de s’authentifier correctement avec leurs informations d’identification locales.

Mise en œuvre de l’authentification unique locales il est facile avec les solutions de fédération comme ADFS ou en configurant la synchronisation de hachage de mot de passe. Vous pouvez facilement déployer les deux options à l’aide de l’Assistant de configuration Azure AD Connect.
  

Pour plus d’informations, voir [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
  

---

**Q : ne Azure Active Directory fournit un portail libre-service pour les utilisateurs de mon organisation ?**

**A:** Oui, Azure Active Directory vous propose le [Panneau de configuration Azure AD Access](http://myapps.microsoft.com) pour l’accès des utilisateurs libre-service et application. Si vous êtes un client Office 365, vous pouvez trouver la plupart des mêmes fonctionnalités dans le portail Office 365. 

Pour plus d’informations, voir l' [Introduction pour le panneau d’accès](active-directory-saas-access-panel-introduction.md). 



---

**Q : ne Azure AD m’aide à gérer mes infrastructure en local ?**

**A:** Oui, c’est le cas. L’édition Azure AD Premium vous offre dont **l’État de connexion**. Azure AD connecter santé permet de contrôler et familiarisez-vous avec votre infrastructure d’identité en local et les services de synchronisation.  

Pour plus d’informations, voir [Moniteur vos localement infrastructure et la synchronisation services d’identité dans le cloud](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Gestion de mot de passe

**Q : puis-je utiliser la fonctionnalité écriture différée Azure AD mot de passe sans synchronisation de mot de passe ? (AKA, je souhaite utiliser Azure AD SSPR avec écriture différée mot de passe, mais je ne souhaite pas mes mots de passe stockés dans le cloud?)**

**A:** Vous n’avez pas besoin synchroniser vos mots de passe AD à Azure Active Directory afin de permettre l’écriture différée. Dans un environnement fédéré, Azure AD SSO s’appuie sur le répertoire local pour authentifier l’utilisateur. Ce scénario ne nécessite pas le mot de passe en local pour le suivi dans Azure AD.

---

**Q : combien de temps faut-il pour un mot de passe à écrire de nouveau vers Active Directory local ?**

**A:** Mot de passe écriture différée fonctionne en temps réel. 

Pour plus d’informations, voir [prise en main de gestion de mot de passe](active-directory-passwords-getting-started.md) 


---

**Q : puis-je utiliser le mot de passe écriture différée avec les mots de passe qui sont gérés par un administrateur ?**

**A:** Oui, si votre mot de passe en écriture différée activée, les opérations de mot de passe effectuées par un administrateur sont réécrits à votre environnement local.  

Pour obtenir des réponses plus au mot de passe questions connexes, voir [Forum aux Questions de mot de passe gestion](active-directory-passwords-faq.md).

---

## <a name="application-access"></a>Accès aux applications


**Q : où puis-je trouver une liste des applications qui sont déjà intégrées avec Azure Active Directory et de leurs fonctions ?**

**A:** Azure AD a plus 2600 applications déjà intégrées à partir de Microsoft, les fournisseurs de services d’application ou les partenaires. Toutes les applications déjà intégrées prennent en charge l’authentification unique. L’authentification unique vous permet d’utiliser vos informations d’identification d’organisation pour accéder à vos applications. Certaines applications prennent également en charge mise en service automatisé et désactivation des comptes

Pour une liste complète des applications déjà intégrées, consultez le [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**Q : que se passe-t-il si l’application que j’ai besoin ne figure pas dans le marché Azure AD ?**

**A:** Avec Azure AD Premium, vous pouvez ajouter et configurer n’importe quelle application souhaité. En fonction des capacités de votre application et vos préférences, vous pouvez configurer l’authentification unique et mise en service automatisé.  

Pour plus d’informations, voir :

- [Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory](active-directory-saas-custom-apps.md)
- [Utilisation de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications](active-directory-scim-provisioning.md) 


---

**Q : comment les utilisateurs vous connecter à des applications à l’aide d’Azure Active Directory ?**
 
**A:** Azure Active directory fournit plusieurs méthodes pour les utilisateurs à afficher et accéder à leurs applications telles que :

- Le panneau d’accès Azure AD

- Le Lanceur d’applications Office 365

- Authentification directe aux applications fédérées

- Liens approfondies fédérés, mot de passe ou applications existantes

Pour plus d’informations, voir [déploiement Azure AD intégré applications aux utilisateurs](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**Q : quelles sont les différentes façons Azure Active Directory permet l’authentification et l’authentification unique pour les applications ?**
 
**A:** Azure Active Directory prend en charge de nombreux protocoles standardisés pour l’authentification et l’autorisation tels que SAML 2.0 OpenID se connecter, OAuth 2.0 et la fédération Web. Azure AD prend également en charge le stockage de fichiers de mot de passe et les capacités de connexion automatiques pour les applications qui ne prennent en charge l’authentification basée sur les formulaires.  

Pour plus d’informations, voir :

- [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md)

- [Protocoles d’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Comment authentification unique avec travail Azure Active Directory ?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**Q : puis-je ajouter des applications que je suis en cours d’exécution en local ?**

**A:** Proxy d’Application Azure AD vous offre un accès facile et sécurisé aux applications web local que vous choisissez. Vous pouvez accéder à ces applications de la même façon que vous accédez à vos applications SaaS dans Azure Active Directory. Il est inutile d’un réseau privé virtuel ou de modifier votre infrastructure réseau.  

Pour plus d’informations, voir [comment fournir un accès à distance sécurisé aux applications en local](active-directory-application-proxy-get-started.md).


--- 

**Q : comment nécessitent l’authentification Multifacteur pour les utilisateurs qui accèdent à une application spécifique ?**

**A:** Avec l’accès Azure AD conditionnelle, vous pouvez attribuer une stratégie d’accès unique pour chaque application. Dans votre stratégie, vous pouvez demander l’authentification Multifacteur à tout moment, ou lorsque les utilisateurs n’êtes pas connectés au réseau local.  

Pour plus d’informations, voir [sécurisation l’accès à Office 365 et d’autres applications connectées à Azure Active Directory](active-directory-conditional-access.md).


---

**Q : quelle est automatisé d’approvisionnement de l’utilisateur pour les applications SaaS ?**

**A:** Azure Active Directory vous permet d’automatiser la création, la maintenance et la suppression des identités utilisateur dans de nombreuses applications cloud populaires (SaaS). 

Pour plus d’informations, voir [automatiser la mise en service des utilisateurs et Deprovisioning aux Applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)

---



