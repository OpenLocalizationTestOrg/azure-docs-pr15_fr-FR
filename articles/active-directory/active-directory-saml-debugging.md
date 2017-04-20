<properties 
    pageTitle="Comment déboguer basée sur SAML SSO aux applications dans Azure Active Directory | Microsoft Azure" 
    description="Découvrez comment déboguer basée sur SAML SSO aux applications dans Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Comment déboguer basée sur SAML SSO aux applications dans Azure Active Directory

Lors du débogage d’une intégration des applications basés sur SAML, il est souvent utile d’utiliser un outil comme [Fiddler](http://www.telerik.com/fiddler) pour voir la demande SAML, la réponse SAML et le jeton SAML réel émis à l’application. En examinant le jeton SAML, vous pouvez vous assurer que tous les attributs obligatoires, le nom d’utilisateur dans l’objet SAML et l’émetteur URI sont traversant comme prévu.

![][1]

La réponse à partir d’Azure AD qui contient le jeton SAML est généralement celui qui se produit après un HTTP 302 rediriger de https://login.windows.net et est envoyé à l' **URL de réponse** configurée de l’application. 
 
Vous pouvez afficher le jeton SAML en sélectionnant cette ligne, puis en sélectionnant la **inspecteurs > Web Forms** onglet dans le volet de droite. À partir de là, avec le bouton droit de la valeur **SAMLResponse** et sélectionnez **Envoyer à TextWizard**. Puis sélectionnez **à partir d’en base 64** dans le menu **transformer** coder le jeton et afficher son contenu.
 
**Remarque**: pour afficher le contenu de cette demande HTTP, Fiddler peut vous inviter à configurer déchiffrement du trafic HTTPS, dont vous aurez besoin pour effectuer.

## <a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory](active-directory-saas-custom-apps.md)
- [La personnalisation des revendications émises dans le jeton SAML pour les applications déjà intégrées](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
