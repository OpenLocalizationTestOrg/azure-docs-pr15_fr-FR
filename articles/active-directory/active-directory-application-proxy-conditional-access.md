<properties
    pageTitle="Accès conditionnel pour les Applications publiées avec Azure AD Proxy de l’Application"
    description="Explique comment configurer l’accès pour les applications que Web pour accéder à distance en utilisant le Proxy d’Application Azure AD conditionnelle."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>Utilisation de l’accès conditionnel

Vous pouvez configurer des règles d’accès pour accorder l’accès conditionnelle aux applications publiées à l’aide de Proxy de l’Application. Cela vous permet de :

- Nécessite l’authentification multifacteur par application
- Exiger l’authentification multifacteur uniquement lorsque les utilisateurs ne sont pas au bureau
- Empêcher les utilisateurs d’accéder à l’application lorsqu’ils ne sont pas au travail

Les règles suivantes peuvent être appliqués à tous les utilisateurs et groupes ou uniquement aux utilisateurs et groupes spécifiques. Par défaut, la règle s’applique à tous les utilisateurs qui ont accès à l’application. Toutefois, la règle peut également être limitée aux utilisateurs qui sont membres de groupes de sécurité spécifiés.  

Règles d’accès sont évaluées lorsqu’un utilisateur accède à une application fédérée qui utilise 2.0 OAuth, OpenID se connecter, SAML ou la fédération de Web. En outre, les règles d’accès sont évaluées avec OAuth 2.0 et OpenID se connecter lorsqu’un jeton d’actualisation est utilisé pour acquérir un jeton d’accès.

## <a name="conditional-access-prerequisites"></a>Conditions préalables pour les accès conditionnel

- Abonnement à Premium Azure Active Directory
- Un client Azure Active Directory fédéré ou géré
- Les clients fédérés requièrent que l’authentification multifacteur (MFA) est activée  
    ![Configurer des règles d’accès - nécessite l’authentification multifacteur](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurer l’authentification multifacteur par application
1. Se connecter en tant qu’administrateur dans le portail classique Azure.
2. Accédez à Active Directory et sélectionnez le répertoire dans lequel vous souhaitez activer le Proxy d’Application.
3. Cliquez sur **Applications** et faites défiler jusqu'à la section **Règles d’accès** . La section règles d’accès s’affiche uniquement pour les applications publiées à l’aide de Proxy d’Application qui utilisent l’authentification fédérée.
4. Activez la règle en sélectionnant **Activer les règles d’accès** sur **activé**.
5. Spécifiez les utilisateurs et groupes auxquels les règles seront appliquent. Utilisez le bouton **Ajouter un groupe** pour sélectionner un ou plusieurs groupes auxquels s’applique la règle d’accès. Cette boîte de dialogue peut également être utilisé pour supprimer les groupes sélectionnés.  Lorsque les règles sont sélectionnés à appliquer aux groupes, les règles d’accès s’appliquent uniquement aux utilisateurs qui appartiennent à l’un des groupes de sécurité spécifiés.  

  - Pour exclure explicitement des groupes de sécurité de la règle, vérifiez **à l’exception** et spécifiez un ou plusieurs groupes. Les utilisateurs qui font partie d’un groupe dans la liste Except devront pas effectuer l’authentification multifacteur.  

  - Si un utilisateur a été configuré à l’aide de la fonctionnalité de l’authentification multifacteur par utilisateur, ce paramètre ont priorité sur les règles de l’authentification multifacteur d’application. Cela signifie qu’un utilisateur qui a été configuré pour l’authentification multifacteur par utilisateur seront requis pour procéder à l’authentification multifacteur même s’ils ont été exemptés de règles de l’authentification multifacteur de l’application. En savoir plus sur [l’authentification multifacteur et paramètres d’utilisateur](../multi-factor-authentication/multi-factor-authentication.md).

6. Sélectionnez la règle d’accès que vous souhaitez définir :
    - **L’authentification multifacteur nécessitent**: les utilisateurs auxquels les règles d’accès s’appliquent seront requises pour l’authentification multifacteur complète avant d’accéder à l’application à laquelle s’applique la règle.
    - **Authentification multifacteur nécessitent pas au bureau**: les utilisateurs essaient d’accéder à l’application d’une adresse IP approuvée pas devront effectuer l’authentification multifacteur. Les plages d’adresses IP approuvés peuvent être configurés dans la page Paramètres de l’authentification multifacteur.
    - **Bloquer l’accès pas au bureau**: les utilisateurs essaient d’accéder à l’application à partir d’à l’extérieur de votre réseau d’entreprise ne seront pas en mesure d’accéder à l’application.


## <a name="configuring-mfa-for-federation-services"></a>Configuration de l’authentification Multifacteur pour les services AD FS.
Pour les clients fédérés, l’authentification multifacteur (MFA) peut être effectuée par Azure Active Directory ou par le local serveur AD FS. Par défaut, l’authentification Multifacteur produira sur n’importe quelle page hébergée par Azure Active Directory. Afin de configurer l’authentification Multifacteur en local, exécutez Windows PowerShell et utiliser la propriété – SupportsMFA pour configurer le module Azure Active Directory.

L’exemple suivant montre comment activer l’authentification Multifacteur locales à l’aide de l' [applet de commande Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) sur le client contoso.com :`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Outre la définition de cet indicateur, l’instance FS fédérés client AD doit être configuré pour exécuter l’authentification multifacteur. Suivez les instructions pour le [déploiement de Microsoft Azure l’authentification multifacteur en local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Voir aussi

- [Utilisation des applications prenant en charge sur les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Publier des applications avec le Proxy d’Application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
