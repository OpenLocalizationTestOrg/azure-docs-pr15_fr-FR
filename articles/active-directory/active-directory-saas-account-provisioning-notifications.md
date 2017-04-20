<properties
    pageTitle="Compte les notifications de mise en service | Microsoft Azure"
    description="Découvrez comment vous assurer que vous êtes averti des problèmes liés à la configuration des utilisateurs qui nécessitent votre attention en activant les notifications de mise en service de compte."
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
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Notifications de mise en service du compte

Avec mise en service de l’utilisateur, vous pouvez automatiser le processus de gestion des utilisateurs dans les applications tierces de SaaS. <br>
Alors que c’est un processus automatisé, vos interactions avec ce processus exigeant de temps à autre requises. <br>
Il s’agit, par exemple le cas, lorsque le mot de passe du compte que vous avez configuré pour échanger des données avec une troisième partie SaaS application a expiré. 

En activant les notifications de mise en service de compte, vous pouvez vous assurer que vous êtes averti des problèmes liés à la configuration des utilisateurs qui nécessitent votre attention.

Vous activez ou désactivez les notifications de mise en service dans le cadre de votre configuration d’un fournisseur tiers SaaS application de mise en service de compte.

![Configuration des utilisateurs][1] 



Pour activer les notifications de mise en service de compte, activez la case à cocher associée dans la page de dialogue de **Confirmation** , puis tapez l’adresse de messagerie du destinataire.

![Notifications de mise en service du compte][2]
 


Vous pouvez entrer une liste de distribution en tant que destinataire ; Toutefois, il est important de noter que le message électronique de notification contienne des liens vers des rapports qui ne sont accessibles par les administrateurs Azure AD.

Si vous avez compte mise en service de notifications sont activées, vous recevrez par e-mail sur les principaux problèmes qui sont liées à la configuration des utilisateurs. Toutefois, pour éviter une surcharge de messagerie, vous ne recevrez qu’un message électronique de notification par jour pour chaque application SaaS pour que le message électronique de notification est activé.


##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser utilisateur mise en service / d’annulation aux applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs de mise en service de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Étendue de filtres de mise en service de l’utilisateur](active-directory-saas-scoping-filters.md)
- [À l’aide de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications](active-directory-scim-provisioning.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png