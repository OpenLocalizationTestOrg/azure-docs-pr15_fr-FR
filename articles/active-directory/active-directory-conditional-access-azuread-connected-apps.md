<properties
    pageTitle="Azure accès conditionnel pour les applications SaaS | Microsoft Azure"
    description="Accès conditionnel dans Azure AD permet de vous permettent de configurer des règles d’accès de l’authentification multifacteur par application et la possibilité de bloquer l’accès des utilisateurs se trouvent pas sur un réseau approuvé. "
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
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Prise en main avec un accès conditionnelle Azure Active Directory

Azure conditionnelle accès au répertoire actif pour les applications [SaaS](https://azure.microsoft.com/overview/what-is-saas/) et Azure AD connecté vous configurez l’accès conditionnelle en fonction de groupe, l’emplacement et sensibilité application vous permet d’applications. 

Avec l’accès conditionnelle en fonction de critères de diffusion application, vous pouvez définir des règles d’accès de l’authentification multifacteur (MFA) par application. L’authentification Multifacteur par application offre la possibilité de bloquer l’accès pour les utilisateurs qui se trouvent pas sur un réseau approuvé. Vous pouvez appliquer des règles de l’authentification Multifacteur à tous les utilisateurs affectés à l’application, ou uniquement pour les utilisateurs au sein de groupes de sécurité spécifiés.  Les utilisateurs peuvent être exclues l’exigence de l’authentification Multifacteur si ils accèdent à l’application à partir d’une adresse IP à l’intérieur du réseau de l’organisation.

Ces fonctionnalités sont disponibles pour les clients qui ont acheté une licence Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Conditions préalables de scénario
* Licence pour Premium Azure Active Directory

* Client Azure Active Directory fédéré ou gérée

* Les clients fédérés requièrent que l’authentification multifacteur être activée.

## <a name="configure-per-application-access-rules"></a>Configurer des règles d’accès de l’application

Cette section décrit comment configurer des règles d’accès de l’application.

1. Connectez-vous au portail classique Azure à l’aide d’un compte qui est un administrateur global pour Azure AD.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet annuaire, sélectionnez votre répertoire.
4. Sélectionnez l’onglet **Applications** .
5. Sélectionnez l’application est définie pour la règle.
6. Sélectionnez l’onglet **configurer** .
7. Faites défiler jusqu'à la section règles d’accès. Sélectionnez la règle d’accès souhaité.
8. Spécifier les utilisateurs d’à que la règle s’applique.
9. Activer la stratégie, sélectionnez **activé pour être**.

##<a name="understanding-access-rules"></a>Présentation des règles access

Cette section fournit une description détaillée des règles d’accès pris en charge dans l’Application conditionnelle Azure accès.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Spécifier les utilisateurs les règles d’accès s’appliquent aux

Par défaut, la stratégie s’appliquent à tous les utilisateurs qui ont accès à l’application. Toutefois, vous pouvez également limiter la stratégie aux utilisateurs qui sont membres de groupes de sécurité spécifiés. Le bouton **Ajouter un groupe** est utilisé pour sélectionner un ou plusieurs groupes dans la boîte de dialogue de sélection de groupe auquel la règle d’accès s’applique. Cette boîte de dialogue peut également être utilisé pour supprimer les groupes sélectionnés. Lorsque les règles sont sélectionnés à appliquer aux groupes, les règles d’accès s’appliquent uniquement aux utilisateurs qui appartiennent à l’un des groupes de sécurité spécifiés.

Groupes de sécurité peuvent également être explicitement exclus de la stratégie en sélectionnant l’option **à l’exception** et en spécifiant un ou plusieurs groupes. Les utilisateurs qui ne sont membres d’un groupe dans la liste **à l’exception** ne seront pas soumis à l’exigence de l’authentification multifacteur, même s’ils sont un membre d’un groupe auquel la règle d’accès s’applique à.
La règle d’accès ci-dessous exiger que tous les utilisateurs dans le groupe de gestionnaires d’utiliser l’authentification multifacteur lorsque vous accédez à l’application.

![Définition des règles d’accès conditionnel avec l’authentification Multifacteur](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Règles d’accès conditionnelle avec l’authentification Multifacteur
Si un utilisateur a été configuré à l’aide de la fonctionnalité de l’authentification multifacteur par utilisateur, ce paramètre sur l’utilisateur regroupe dont les règles de l’authentification multifacteur de l’application. Cela signifie que d’un utilisateur qui a été configuré pour l’authentification multifacteur par utilisateur devront effectuer l’authentification multifacteur même si elles ont été exemptés des règles de l’authentification multifacteur d’application. En savoir plus sur les paramètres d’authentification et par utilisateur multifacteur.

### <a name="access-rule-options"></a>Options de règle d’accès
Les options suivantes sont prises en charge :

* **Nécessite l’authentification multifacteur**: les utilisateurs auxquels les règles d’accès s’appliquer à, seront requises pour l’authentification multifacteur complète avant d’accéder à l’application qui s’applique à la stratégie.

* **Nécessite l’authentification multifacteur pas au bureau**: un utilisateur qui provenance d’une adresse IP fiable pas devront effectuer l’authentification multifacteur. Les plages d’adresses IP approuvés peuvent être configurés dans la page Paramètres de l’authentification multifacteur.

* **Bloquer l’accès pas au bureau**: un utilisateur qui n’arrive pas à partir d’une adresse IP approuvée est bloqué. Les plages d’adresses IP approuvés peuvent être configurés dans la page Paramètres de l’authentification multifacteur.

### <a name="setting-rule-status"></a>Définir le statut de la règle
État de la règle Access permet d’activation ou désactivation des règles. Lorsque les règles d’accès sont désactivée, l’exigence de l’authentification multifacteur n’est pas appliqué.

### <a name="access-rule-evaluation"></a>Évaluation de la règle Access

Règles d’accès sont évaluées lorsqu’un utilisateur accède à une application fédérée qui utilise 2.0 OAuth, OpenID se connecter, SAML ou la fédération de Web. En outre, les règles d’accès sont évaluées lorsque le jeton 2.0 et OpenID se connecter utilisent un jeton actualisation acquérir un jeton d’accès. Si l’évaluation de la stratégie échoue lorsqu’un jeton d’actualisation est utilisé, l' erreur **invalid_grant** sera renvoyé, cela signifie que l’utilisateur doit s’authentifier à nouveau pour le client.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurer les services de fédération pour offrir l’authentification multifacteur

Pour les clients fédérés, l’authentification Multifacteur peut être effectuée par Azure Active Directory ou par le local serveur AD FS.

Par défaut, l’authentification Multifacteur se produit à une page hébergée par Azure Active Directory. Pour configurer l’authentification Multifacteur en local, vous devez définir la propriété **– SupportsMFA** **true** dans Azure Active Directory, à l’aide du module Azure Active Directory pour Windows PowerShell.

L’exemple suivant montre comment activer l’authentification Multifacteur locales à l’aide de l' [applet de commande Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) sur le client contoso.com :

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Outre la définition de cet indicateur, l’instance FS fédérés client AD doit être configuré pour exécuter l’authentification multifacteur. Suivez les instructions pour le [déploiement de l’authentification multifacteur Azure locaux](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Articles connexes

- [Sécuriser l’accès à Office 365 et d’autres applications connecté à Azure Active Directory](active-directory-conditional-access.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
