<properties
  pageTitle="Gestion de l’accès aux applications à l’aide d’Azure AD |  Microsoft Azure"
  description="Décrit comment Azure Active Directory permet aux organisations de spécifier les applications à laquelle chaque utilisateur a accès."
  services="active-directory"
  documentationCenter=""
  authors="femila"
  manager="femila"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/13/2016"
  ms.author="femila"/>


# <a name="managing-access-to-apps"></a>Gestion de l’accès aux applications

Gestion de l’accès en continu, d’évaluation de l’utilisation et les rapports continuent à être compliqué après qu’une application est intégrée à système d’identité de votre organisation. Dans de nombreux cas, les administrateurs informatiques ou le support technique doivent prendre un rôle actif en cours dans la gestion de l’accès à vos applications. Parfois, affectation est effectuée par une équipe informatique générale ou des division. Souvent, la décision d’affectation est destinée à être déléguée aux décideurs métier, pour demander une leur approbation avant informatique facilite l’affectation.  D’autres organisations investissent dans l’intégration à un automatisé identités et des accès système de gestion, tels que le contrôle d’accès basé sur un rôle (RBAC) ou de contrôle d’accès basé sur un attribut (fichier ABAC). L’intégration et le développement de règle tendent à être spécialisées et coûteuses. Surveillance ou un rapport sur les deux approches de gestion est sa propre investissement distincte, coûteux et complexe.

## <a name="how-does-azure-active-directory-help"></a>Quel est l’Azure Active Directory ?

 Azure AD prend en charge la gestion des accès complet pour applications configurées, permettant aux organisations de réaliser facilement les stratégies d’accès droite allant automatique, basée sur les attributs d’affectation (fichier ABAC ou RBAC scénarios) via la délégation et y compris la gestion de l’administrateur. Avec Azure AD vous pouvez obtenir facilement stratégies complexes, qui combine plusieurs modèles de gestion d’une seule application et pouvez même réutiliser des règles de gestion dans applications avec les audiences mêmes.

 - [Ajout de nouveaux éléments ou les applications existantes](active-directory-sso-integrate-saas-apps.md)


 Affectation d’applications d’Azure AD se concentre sur deux modes affectation principale :

- **Des affectations** Un administrateur informatique avec des autorisations d’administrateur général répertoire pouvez sélectionner des comptes d’utilisateurs individuels et leur accorder l’accès à l’application.
- **Affectation basée sur le groupe (payée Azure AD uniquement)** Un administrateur informatique avec des autorisations d’administrateur général répertoire peut affecter un groupe à l’application. Accès des utilisateurs spécifiques est déterminé par s’ils sont membres du groupe au moment où qu'ils essaient d’accéder à l’application. En d’autres termes, un administrateur peut efficacement créer une règle d’affectation indiquant « n’importe quel membre du groupe affecté actuel a accès à l’application ». Utilisez cette option d’affectation, les administrateurs peuvent bénéficier d’une des options de gestion de groupe Azure AD, y compris [basée sur les attributs des groupes dynamiques](active-directory-accessmanagement-manage-groups.md), des groupes de système externe (par exemple, Active Directory local ou journée de travail) ou gérées par un administrateur ou automatique-service géré par des groupes. Un seul groupe peut être affecté facilement à plusieurs applications, garantissant que les applications avec affinité d’affectation peuvent partager des règles d’affectation, réduisant la complexité de la gestion globale des. Remarque : ce groupe imbriqué appartenances ne sont pas pris en charge pour affectation basée sur le groupe aux applications pour le moment.

À l’aide de ces modes de deux affectation, les administrateurs peuvent obtenir une approche de gestion des opportun d’affectation.

Avec Azure AD, l’utilisation et la création de rapports d’affectation est totalement intégré, permettant aux administrateurs de créer facilement des rapports sur l’utilisation de même, les erreurs d’affectation et état de l’affectation.

## <a name="complex-application-assignment-with-azure-ad"></a>Affectation d’applications complexes avec Azure Active Directory

Envisagez d’une application telle que Salesforce. Dans de nombreuses organisations Salesforce est principalement utilisé par les organisations ventes et marketing. Souvent, les membres de l’équipe marketing ont hautement privilégié accès à force de vente, tandis que les membres de l’équipe de vente ont un accès limité. Dans de nombreux cas, un large de remplissage des travailleurs de l’information ont un accès limité à l’application. Les exceptions à ces règles à des problèmes compliquent. Il est souvent la prérogative des équipes leadership commerciaux ou marketing pour accorder un accès utilisateur ou de modifier leur rôle indépendamment de celles ces règles génériques.

Azure AD, applications telles que Salesforce peuvent être préconfigurées pour l’authentification unique (SSO) et la mise en service automatisé. Une fois que l’application est configurée, un administrateur peut prendre la mesure unique pour créer et affecter des groupes appropriés. Dans cet exemple, un administrateur peut exécuter les affectations suivantes :

- [Groupes dynamiques](active-directory-accessmanagement-manage-groups.md) peuvent être définis pour représenter automatiquement tous les membres des équipes ventes et marketing à l’aide des attributs tels que le service ou rôle :

    - Tous les membres de groupes marketing sera attribuées au rôle « marketing » dans Salesforce

    - Tous les membres d’équipe commerciale groupes sera attribuées au rôle « vente » dans Salesforce. Une amélioration peut utiliser plusieurs groupes qui représentent les équipes de ventes régionales attribuées aux rôles Salesforce différents.

- Pour activer le mécanisme d’exception, un groupe libre-service peut être créé pour chaque rôle. Par exemple, le groupe « Salesforce marketing exception » peut être créé en libre-service du groupe. Le groupe peut être affecté au rôle marketing Salesforce et propriétaires peut être effectuée à l’équipe du leadership marketing. Membres de l’équipe marketing leadership peuvent ajouter ou supprimer des utilisateurs, défini une stratégie de jointure, ou même approuver ou refuser des demandes d’utilisateurs individuels à rejoindre. Ceci est pris en charge par le biais une expérience approprié de travail d’informations qui ne nécessite pas de formation spécialisée pour les propriétaires et membres.

Dans ce cas, tous les utilisateurs affectés seraient être configurés automatiquement pour Salesforce, lorsqu’elles sont ajoutées à différents groupes que leur attribution de rôle sera actualisée dans Salesforce. Les utilisateurs pourront découvrir et accéder aux Salesforce via le panneau d’accès application Microsoft, les clients Office web, ou même en accédant à sa page de connexion Salesforce d’organisation. Les administrateurs pourront facilement afficher état de l’utilisation et d’affectation à l’aide de création de rapports AD Azure.

Les administrateurs peuvent recourir à [accès conditionnel Azure AD](active-directory-conditional-access.md) pour définir les stratégies d’accès pour des rôles spécifiques. Ces règles peuvent inclure si l’accès est autorisé à l’extérieur de l’environnement d’entreprise en matière de même l’authentification multifacteur ou appareil pour obtenir l’accès dans différents cas.

## <a name="how-can-i-get-started"></a>Comment puis-je par où commencer ?

Tout d’abord, si vous n’utilisez pas encore Azure AD et que vous êtes un administrateur informatique :

 - [Essayez-le !](https://azure.microsoft.com/trial/get-started-active-directory/) -peuvent s’inscrire à un essai gratuit de 30 jours aujourd'hui et déployer votre première solution cloud dans sous 5 minutes à l’aide de ce lien

Fonctionnalités Azure AD activer le partage de compte sont les suivantes :

- [Affectation de groupe](active-directory-accessmanagement-self-service-group-management.md)
- Ajout d’applications à Azure Active Directory
- Prise en main d’affectation
- Affectation d’applications Forum aux questions
- [Application du tableau de bord/des rapports d’utilisation](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Où puis-je obtenir plus d’informations ?

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Protection des applications avec accès conditionnel](active-directory-conditional-access.md)
- [Groupe libre-service gestion/SSA](active-directory-accessmanagement-self-service-group-management.md)
