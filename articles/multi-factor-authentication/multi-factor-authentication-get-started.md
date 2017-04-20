<properties
    pageTitle="Serveur vs en nuage Azure l’authentification Multifacteur | Microsoft Azure"
    description="Choisissez la solution de sécurité de l’authentification multifacteur est droite pour vous en demandant à quels am i tentative de protection et où se trouvent mes utilisateurs situés.  Puis sélectionnez cloud, l’authentification Multifacteur serveur ou AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Choisir la solution d’authentification multifacteur Azure à votre place

Car il existe plusieurs types de Azure multifacteur l’authentification Multifacteur, nous devons répondre à quelques questions pour déterminer quelle version est l’outil à utiliser.  Ces questions sont les suivantes :

-   [Qu’est-ce essayez sécuriser](#what-am-i-trying-to-secure)
-   [Où se trouvent les utilisateurs](#where-are-the-users-located)
- [Quelles sont les fonctionnalités ont-ils besoin ?](#what-featured-do-i-need)

Les sections suivantes fournissent des instructions sur la détermination de chacun de ces réponses.

## <a name="what-am-i-trying-to-secure"></a>Qu’est-ce essayez sécuriser ?

Pour déterminer la solution vérification en deux étapes correct, tout d’abord nous devons répondre à la question de quels sont les vous essayez de sécuriser avec une deuxième méthode d’authentification.  Optez pour une application qui se trouve dans Azure ?  Ou un système d’accès à distance ?  En déterminant ce que nous essayons sécuriser, nous pouvons répondre à la question de l’endroit où l’authentification multifacteur doit être activé.  


Ce que vous essayez de sécuriser| Authentification multifacteur dans le cloud|Serveur de l’authentification multifacteur
------------- | :-------------: | :-------------: |
Applications Microsoft internes|● |● |
Applications SaaS dans la galerie d’application|● |● |
Applications IIS publiées via Azure AD application Proxy|● |● |
Applications IIS ne pas publiées via Azure AD application Proxy | |● |
Accès à distance comme VPN, distance| |● |



## <a name="where-are-the-users-located"></a>Où se trouvent les utilisateurs

Ensuite, consultant où se trouvent nos utilisateurs aide à déterminer la bonne solution à utiliser, dans le nuage ou sur site à l’aide du serveur de l’authentification Multifacteur.



Emplacement de l’utilisateur| Authentification multifacteur dans le cloud|Serveur de l’authentification multifacteur
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Azure AD et annonce à l’aide de la fédération avec AD FS local|● |● |
Azure AD et annonce à l’aide de la synchronisation d’annuaire, Azure AD Sync, Azure AD Connect - aucune synchronisation de mot de passe local|● |● |
Azure AD et annonce à l’aide de la synchronisation d’annuaire, Azure AD Sync, Azure AD Connect - avec la synchronisation de mot de passe local|● | |
Active Directory local| |● |

## <a name="what-features-do-i-need"></a>Quelles sont les fonctionnalités ont-ils besoin ?

Le tableau suivant présente une comparaison des fonctionnalités qui sont disponibles avec l’authentification multifacteur dans le cloud et avec le serveur de l’authentification multifacteur.

 | Authentification multifacteur dans le cloud | Serveur de l’authentification multifacteur
------------- | :-------------: | :-------------: |
Notification de l’application mobile comme un facteur deuxième | ● | ● |
Code de vérification de l’application mobile comme un facteur deuxième | ● | ●
Appel téléphonique comme deuxième facteur | ● | ●
SMS à sens unique comme deuxième facteur | ● | ●
SMS bidirectionnel comme deuxième facteur |  | ●
Jetons matériels comme deuxième facteur |  | ●
Mots de passe application pour les clients qui ne prennent pas en charge l’authentification Multifacteur | ● |  
Administrateur contrôler les méthodes d’authentification | ● | ●
Mode code confidentiel |  | ●
Alerte de fraude | ● | ●
Rapports de l’authentification Multifacteur | ● | ●
Contournement ponctuel |  | ●
Message d’accueil personnalisé pour les appels téléphoniques | ● | ●
ID d’appelant personnalisables pour les appels téléphoniques | ● | ●
Adresses IP autorisées | ● | ●
N’oubliez pas de l’authentification Multifacteur pour périphériques approuvés  | ● |  
Accès conditionnel | ● | ●
Cache |  | ●

À présent que nous avons déterminé si vous voulez utiliser l’authentification multifacteur cloud l’authentification Multifacteur serveur local, nous pouvons commencer la configuration et à l’aide de l’authentification multifacteur Azure. **Sélectionnez l’icône représentant votre scénario !**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
