<properties
    pageTitle="Automatisé approvisionnement dans Azure Active Directory de l’utilisateur SaaS application | Microsoft Azure"
    description="Introduction à l’utilisation de Azure AD pour configurer automatiquement, mettre hors service et mettre à jour en permanence les comptes d’utilisateurs entre plusieurs applications SaaS tiers."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatiser utilisateur et annulation aux Applications SaaS avec Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>Qu’est automatisé d’approvisionnement de l’utilisateur pour les applications SaaS ?

Azure Active Directory (AD Azure) vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans des applications cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) comme Dropbox, Salesforce, ServiceNow, etc..

**Voici quelques exemples de ce que cette fonctionnalité permet de vous permettent d’effectuer :**

- Créer automatiquement des comptes dans les applications SaaS droite de nouvelles personnes lorsqu’ils participent à votre équipe.
- Désactiver automatiquement les comptes à partir des applications SaaS lorsque les utilisateurs quittent inévitablement l’équipe.
- Vous assurer que les identités dans vos applications SaaS sont mises à jour en fonction des modifications dans l’annuaire.
- Mise en service des objets non-utilisateur, tels que des groupes, aux applications SaaS qui prennent en charge les.

**Mise en service automatisé utilisateur inclut également les fonctionnalités suivantes :**

- La possibilité pour faire correspondre les identités existantes entre Azure AD et applications SaaS.
- Options de personnalisation à l’aide Azure Active Directory ajuster les configurations actuelles des applications SaaS que votre organisation utilise actuellement.
- Alertes par courrier électronique facultatif de mise en service des erreurs.
- Journaux d’activité et de création de rapports pour vous aider à analyser et résolution des problèmes.

##<a name="why-use-automated-provisioning"></a>Pourquoi utiliser la mise en service automatisé ?

Certains motivations courantes pour l’utilisation de cette fonctionnalité sont les suivantes :

- Pour éviter les coûts, efficacité et erreurs humaines manuel processus de configuration.
- Pour sécuriser votre organisation en supprimant instantanément identité des utilisateurs à partir des applications SaaS clées lorsqu’ils quittent l’organisation.
- Pour importer facilement un nombre en bloc d’utilisateurs dans une application SaaS particulière.
- Pour profiter de la possibilité pour votre solution de mise en service fonctionnent sur les mêmes stratégies d’accès application que vous avez défini pour Azure AD SSO.

##<a name="frequently-asked-questions"></a>Forum aux Questions

**À quelle fréquence Azure AD écrire modifications d’annuaire à l’application SaaS ?**

Azure AD vérifie les modifications 5 à 10 minutes. Si l’application SaaS retourne plusieurs erreurs (par exemple, comme dans le cas des informations d’identification d’administration non valides), Azure AD peuvent ralentir progressivement sa fréquence pour devenir une fois par jour jusqu'à ce que les erreurs sont résolus.

**Combien de temps faut-il pour la mise en service de mes utilisateurs ?**

Il arrive que les modifications incrémentielles presque instantanément mais si vous tentez de mise en service de la plupart des recherches dans l’annuaire, puis en fonction du nombre d’utilisateurs et groupes que vous avez. Petits répertoires prennent quelques minutes, moyennes répertoires peuvent prendre plusieurs minutes et annuaires très volumineux peuvent prendre quelques heures.

**Comment puis-je suivre la progression de la tâche mise à disponibilité actuelle ?**

Vous pouvez examiner le rapport de mise en service de compte sous la section rapports de votre annuaire. Une autre option consiste à visiter l’onglet tableau de bord pour l’application SaaS mise en service pour, puis regardez sous la section « Statut d’intégration » dans la partie inférieure de la page.

**Comment savoir si les utilisateurs ne parviennent pas à obtenir sa mise en service correctement ?**

À la fin de la configuration de mise en service Assistant il est une option pour vous abonner aux notifications par courrier électronique de mise en service des échecs. Vous pouvez également vérifier le rapport d’erreurs de mise en service pour afficher les utilisateurs qui n’a pas pu être mis en service et pourquoi.

**Pouvez Azure AD écrire les modifications de l’application SaaS dans l’annuaire ?**

Pour la plupart des applications SaaS, mise en service est sortant seule, ce qui signifie que les utilisateurs sont écrits à partir du répertoire à l’application, et les modifications de l’application ne peut pas être revenir à l’annuaire. Pour la [journée de travail](https://msdn.microsoft.com/library/azure/dn762434.aspx), cependant, mise en service est entrant seule, ce qui signifie que les utilisateurs sont importés dans le répertoire à partir de la journée de travail, et même, changements dans l’annuaire ne pas obtenir écrites revenir dans journée de travail.

**Comment puis-je envoyer des commentaires à l’équipe technique ?**

Contactez-nous via le [forum de commentaires Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>Quels sont les tâches de dimensionnement automatique ?

Azure AD dispositions aux utilisateurs d’applications SaaS en vous connectant à la mise en service des points de terminaison fournis par chaque fournisseur de l’application. Ces points de terminaison autoriser Azure AD créer, mettre à jour et supprimer des utilisateurs par programme. Voici un bref aperçu des différentes phases qui conduit les Azure AD pour automatiser la mise en service.

1. Lorsque vous activez la mise en service pour une application pour la première fois, les actions suivantes sont exécutées :
 - Azure AD tentera de correspondre à tous les utilisateurs existants dans l’application SaaS avec leurs identités correspondantes dans l’annuaire. Lorsqu’un utilisateur est mise en correspondance, ils sont *pas* automatiquement activés pour l’authentification unique. Dans l’ordre d’un utilisateur à accéder à l’application, ils doivent être explicitement affectées à l’application dans Azure Active Directory, directement ou via l’appartenance aux groupes.
 - Si vous avez déjà spécifié les utilisateurs qui doivent être affectées à l’application, et si Azure AD ne parvient pas à trouver des comptes existants pour les utilisateurs, Azure AD configurera nouveaux comptes les recherchant dans l’application.
2. Une fois la synchronisation initiale terminée comme décrit ci-dessus, Azure AD doit vérifier toutes les 10 minutes pour que les modifications suivantes :
 - Si de nouveaux utilisateurs ont été affectées à l’application (directement ou via l’appartenance aux groupes), puis ils seront mis en service un nouveau compte dans l’application SaaS.
 - Si l’accès d’un utilisateur a été supprimé, puis leur propre compte dans l’application SaaS est marqué comme désactivé (les utilisateurs ne sont jamais complètement supprimés, qui vous protège contre la perte de données dans le cas d’une configuration incorrecte).
 - Si un utilisateur a été récemment attribué à l’application et qu’ils ont déjà un compte dans l’application SaaS, ce compte sera marqué comme étant activés et certaines propriétés de l’utilisateur peuvent être mis à jour s’ils sont obsolètes par rapport à l’annuaire.
 - Si les informations d’un utilisateur (par exemple, numéro de téléphone, emplacement du bureau, etc.) ont été modifiées dans le répertoire, ces informations met également à jour dans l’application SaaS.

Pour plus d’informations sur la façon dont les attributs sont mappés entre Azure AD et votre application SaaS, consultez l’article sur la [Personnalisation des mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Liste des applications qui prennent en charge l’attribution automatisée

Cliquez sur une application pour afficher un didacticiel sur la configuration d’une mise en service automatisé pour qu’elle :

- [Zone](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Approuvent](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox pour les entreprises](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Force de vente](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Force de vente Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [SERIE.jour.ouvre](http://go.microsoft.com/fwlink/?LinkId=690250) (mise en service entrant)

Dans l’ordre d’une application prendre en charge la mise en service des utilisateurs automatisé, elle doit d’abord fournir les points de terminaison nécessaires permettant de détecter des programmes automatiser la création, la maintenance et la suppression des utilisateurs externes. Par conséquent, pas toutes les applications SaaS sont compatibles avec cette fonctionnalité. Pour les applications qui prennent en charge cela, l’équipe d’ingénierie Azure AD puis sera en mesure de créer un connecteur de mise en service pour ces applications, et ce travail est prioritaire par les besoins de clients actuels et à venir.

Pour contacter l’équipe d’ingénierie Azure AD pour demander la prise en charge mise en service des applications supplémentaires, veuillez envoyer un message via le [forum de commentaires Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Personnalisation des mappages d’attributs de mise en service de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Étendue de filtres de mise en service de l’utilisateur](active-directory-saas-scoping-filters.md)
- [À l’aide de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications](active-directory-scim-provisioning.md)
- [Notifications de mise en service du compte](active-directory-saas-account-provisioning-notifications.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)
