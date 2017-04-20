<properties
    pageTitle="Gérer les Applications avec Azure Active Directory | Microsoft Azure"
    description="Cet article les avantages de l’intégration de Azure Active Directory local, au cloud et applications SaaS."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>Gérer les Applications avec Azure Active Directory

Au-delà des flux de travail réel ou du contenu, entreprises ont deux configuration minimale requise pour toutes les applications :

1. Pour accroître votre productivité, les applications doivent être faciles à découvrir et accéder à

2. Pour activer la sécurité et gouvernance, l’organisation a besoin de contrôle et une vue d’ensemble sur qui peut et réellement accède à chaque application

Dans le monde d’applications cloud cela peut être réalisé au mieux à l’aide d’identité pour contrôler «*qui est autorisé à faire quoi*».

Dans la terminologie de l’informatique :

- *Qui* est appelé *identité* - la gestion des utilisateurs et groupes

- *Quel* est connu sous le nom de *gestion de l’accès* – la gestion de l’accès aux ressources protégées

Les deux composants ensemble sont appelés *identité et gestion des accès (IAM)*, qui est défini par le groupe [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) en tant que «*la discipline de sécurité qui permet aux personnes appropriées accéder aux ressources droite en droite heures pour les bonnes raisons*».

OK, qu’est le problème ? Si IAM n’est *pas géré* dans un seul endroit avec une solution intégrée :

- Les administrateurs d’identité disposent individuellement créer et mettre à jour les comptes d’utilisateurs dans toutes les applications séparément, une activité redondante et beaucoup de temps.

- Les utilisateurs doivent mémoriser plusieurs informations d’identification pour accéder aux applications avec que dont elles ont besoin pour travailler. Par conséquent, les utilisateurs sont généralement Notez son mot de passe ou utiliser d’autres solutions de gestion de mot de passe qui introduit d’autres risques de sécurité des données.

- Activités redondantes, beaucoup de temps réduisent la quantité de fois que des utilisateurs et les administrateurs travaillent sur activités augmentent la ligne du bas de votre entreprise.

Par conséquent, ce qui empêche généralement organisations adopter de solutions IAM intégrées ?

- Solutions techniques plus sont basées sur les plateformes de logiciel qui doivent être déployée et adapté par chaque organisation dans leurs propres applications.

- Applications en nuage sont souvent adoptées à une fréquence plus élevée que le service informatique peut intégrer des solutions IAM existantes.

- Sécurité et outils de surveillance nécessitent une personnalisation supplémentaire et l’intégration à atteindre les scénarios E2E détaillés.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory intégré aux applications

Azure Active Directory est complète des identités de Microsoft comme une solution de Service (IDaaS) qui :

- Active IAM comme un service cloud 

- Fournit la gestion des accès centrale, authentification unique (SSO) et création de rapports 

- Gestion des accès intégré prend en charge pour [des milliers d’applications](https://azure.microsoft.com/marketplace/active-directory/) dans la galerie d’applications, notamment Salesforce, Google Apps, zone, Concur et plus encore. 


Avec Azure Active Directory, toutes les applications que vous publiez pour vos partenaires et clients (entreprise ou consommateur) ont la même identité et accéder aux fonctionnalités de gestion des.<br> Cela vous permet de réduire sensiblement vos coûts d’exploitation.

Que se passe-t-il si vous devez implémenter une application qui n’est pas encore répertoriée dans la galerie des applications ? Lorsque cette opération est un peu plus longue que de configurer l’authentification unique pour les applications à partir de la galerie d’applications, Azure AD vous fournit un Assistant qui vous aide à la configuration.

La valeur d’Azure AD au-delà des applications en nuage « uniquement ». Vous pouvez également l’utiliser avec les applications en local en fournissant un accès sécurisé à distance. Accès à distance sécurisé, vous pouvez éliminer la nécessaire de VPN ou autres mises en œuvre de la gestion d’accès à distance traditionnel.

Grâce à la gestion des accès central et authentification unique (SSO) pour toutes vos applications, Azure Active Directory offre la solution aux problèmes de sécurité et la productivité de données principale.

- Les utilisateurs peuvent accéder à plusieurs applications avec une authentification donnant plus de temps à revenus génération ou professionnels activités opérations traitées.

- Administrateurs d’identité peuvent gérer l’accès aux applications en un seul endroit.

L’avantage pour l’utilisateur et votre société est évident. Jetons observer les avantages pour un administrateur d’identité et de l’organisation.

## <a name="integrated-application-benefits"></a>Avantages de l’application intégrée

Le processus de l’authentification unique comporte deux étapes :

- Authentification, le processus de validation de l’identité de l’utilisateur.

- Autorisation, à la décision pour activer ou bloquer l’accès à une ressource à une stratégie d’accès.

Lorsque vous utilisez Azure AD pour gérer les applications et activer l’authentification unique :

- Sur l’utilisateur (par exemple AD) locales ou compte Azure AD, authentification est effectuée.

- L’autorisation s’exécute sur la stratégie Azure AD d’affectation et de protection assurant expérience utilisateur cohérente et ce qui vous permet d’ajouter d’affectation, les emplacements et les conditions de l’authentification Multifacteur sur n’importe quelle application, indépendamment de ses capacités internes.

Il est important de comprendre que la façon dont l’autorisation est activée dans l’application cible varie selon la façon dont l’application a été intégrée à Azure AD.

- **Les applications intégrées préalable par fournisseur de services** Comme pour Office 365 et Azure, il s’agit des applications créées directement sur Azure AD et s’appuyer dessus pour leurs fonctionnalités complètes de gestion des identités et des accès. Accès à ces applications est activé par le biais d’émission du jeton et les informations de l’annuaire.

- **Les applications intégrées préalable par Microsoft et les applications personnalisées** Il s’agit des applications cloud indépendant qui s’appuient sur un répertoire application interne et ils peuvent fonctionner indépendamment de celles Azure AD. Accès à ces applications est activé en lançant une application spécifique d’informations d’identification mappées à un compte de l’application. Selon les fonctionnalités de l’application, les informations d’identification peuvent être un jeton de fédération ou le nom d’utilisateur et le mot de passe pour un compte qui a été configuré précédemment dans l’application.

- **Applications local** Applications publiées via le proxy d’application Azure AD principalement permettant l’accès aux applications en local. Ces applications s’appuient sur une centrale dans l’annuaire de site comme Active Directory Windows Server. Accès à ces applications est activé par le serveur proxy de déclenchement pour fournir le contenu de l’application à l’utilisateur final tout en respectant l’exigence d’authentification en local.

Par exemple, si un utilisateur joint à votre organisation, vous devez créer un compte pour l’utilisateur Azure AD pour les principales opérations authentification. Si cet utilisateur requiert l’accès à une application managée comme Salesforce, vous devez également créer un compte pour cet utilisateur Salesforce et liez-le au compte Azure faire fonctionner l’authentification unique. Lorsque l’utilisateur quitte votre organisation, il est conseillé de supprimer le compte Azure AD et tous les comptes homologue dans la page IAM stocke les applications que l’utilisateur a accès à.

## <a name="access-detection"></a>Détection Access

Dans les entreprises modernes, les services informatiques connaissent souvent pas de toutes les applications cloud qui sont utilisées. Conjointement avec Cloud application découverte, Azure AD vous offre une solution pour détecter ces applications.

## <a name="account-management"></a>Gestion des comptes

En règle générale, la gestion des comptes dans les différentes applications est un processus manuel effectué par informatique ou une assistance technique personnel de l’organisation. Azure AD entièrement automatisée de gestion des comptes dans toutes les applications intégrée de fournisseur de service et ces applications intégrées préalables par Microsoft prenant en charge la mise en service automatisé utilisateur ou SAML JIT.

## <a name="automated-user-provisioning"></a>Mise en service automatisé utilisateur

Certaines applications fournissent des interfaces automation pour la création et suppression (ou désactivation) de comptes. Si un fournisseur propose une telle interface, il est exploité par Azure AD. Cela permet de réduire les coûts d’exploitation car tâches administratives s’exécutent automatiquement et améliore la sécurité de votre environnement, car elle diminue le risque d’accès non autorisé.

## <a name="access-management"></a>Gestion de l’accès

À l’aide d’Azure AD vous pouvez gérer les accès à des applications à l’aide d’individuels ou règle par l’effort affectations. Vous pouvez également déléguer accéder à la gestion pour les personnes appropriées dans l’organisation assurant le meilleure vue d’ensemble et réduction des tâches sur le support technique.

## <a name="on-premises-applications"></a>Applications en local

Intégré dans l’application de proxy vous permet de publier vos applications en local à vos utilisateurs résultant dans les deux cohérentes accéder expérience avec l’application en nuage moderne et les avantages de fonctionnalités de surveillance, création de rapports et sécurité Azure AD.

## <a name="reporting-and-monitoring"></a>Création de rapports et de surveillance

Azure AD vous offre rapports intégrés des fonctions et de surveillance qui vous permettent de savoir qui a accès aux applications et lorsqu’ils utilisés réellement les.

## <a name="related-capabilities"></a>Fonctions connexes

Avec Azure Active Directory, vous pouvez protéger vos applications avec les stratégies d’accès granulaires et l’authentification Multifacteur déjà intégrée. Pour en savoir que plus sur l’authentification Multifacteur Azure, consultez [L’authentification Multifacteur Azure](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Prise en main

Pour commencer à intégrer des applications Azure Active Directory, consultez le [guide de démarrage rapide intégration Azure Active Directory avec les applications prise](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Voir aussi

[Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
