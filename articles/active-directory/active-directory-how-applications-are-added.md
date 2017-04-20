<properties
   pageTitle="Comment les applications sont ajoutées à Azure Active Directory."
   description="Cet article décrit comment les applications sont ajoutées à une instance d’Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Comment et pourquoi les applications sont ajoutées à Azure Active Directory

L’une des choses initialement curieux lors de l’affichage d’une liste d’applications dans votre instance d’Azure Active Directory consiste à comprendre d'où proviennent les applications et pourquoi elles sont disponibles.  Cet article offre un aperçu de haut niveau comment les applications sont représentées dans l’annuaire et vous fournissent contexte qui vous aideront à comprendre comment une application avez accédé à être dans votre annuaire.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>À quels services Azure AD fournit aux applications ?

Applications sont ajoutées à Azure AD pour mettre à profit une ou plusieurs des services fournis.  Ces services sont les suivantes :

* Authentification de l’application et d’autorisation
* L’autorisation et l’authentification des utilisateurs
* Session unique (SSO) à l’aide de la fédération ou mot de passe
* Configuration des utilisateurs et synchronisation
* Contrôle d’accès basé sur un rôle ; Utilisez l’annuaire pour définir les rôles d’application pour effectuer des rôles en fonction des contrôles d’autorisation dans une application.
* services d’autorisation oAuth (utilisés par Office 365 et d’autres applications Microsoft pour autoriser l’accès aux ressources/API).
* Publication d’applications et proxy ; Publier une application à partir d’un réseau privé à internet

## <a name="how-are-applications-represented-in-the-directory"></a>Comment les applications sont représentées dans l’annuaire ?

Applications sont représentées dans Azure Active Directory à l’aide de 2 objets : un objet application et un objet principal de service.  Il existe un objet application, enregistré dans un « domicile » / répertoire « propriétaire » ou « publication » et un ou plusieurs objets principal qui représente l’application dans chaque répertoire dans lequel il s’agit du service.  

L’objet application décrit l’application à Azure Active Directory (le service client à plusieurs) et peut inclure les éléments suivants : (*Remarque*: cette liste n’est pas exhaustive.)

* Nom, Logo et Publisher
* Secrets (clés symétriques et/ou asymétriques utilisées pour authentifier l’application)
* Dépendances API (jeton)
* API/ressources/étendues publiés (jeton)
* Rôles de l’application (RBAC)
* Configuration (SSO) et les métadonnées de l’authentification unique
* Approvisionnement métadonnées et la configuration de l’utilisateur
* Configuration et les métadonnées de proxy

L’entité de service est un enregistrement de l’application dans chaque répertoire, où l’application agit, y compris son répertoire de base.  L’entité de service :

* Fait référence à un objet application via la propriété id de l’application
* Utilisateur local enregistrements et les affectations de rôle de l’application de groupe
* Enregistrements accordé des autorisations d’utilisateur et d’administrateur locales à l’application
    * Par exemple : autorisations pour l’application d’accéder à une messagerie d’utilisateurs spécifiques
* Stratégies locales enregistrements, y compris la stratégie d’accès conditionnel
* Paramètres locaux enregistrements locales secondaire pour une application
    * Règles de transformation de revendications
    * Mappages d’attribut (approvisionnement de l’utilisateur)
    * Rôles de l’application spécifique du client (si l’application prend en charge les rôles personnalisés)
    * Nom/Logo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Un diagramme d’objets d’application et entités de service au sein des répertoires

![Diagramme illustrant comment application des objets et entités existant avec les instances Azure AD du service.][apps_service_principals_directory]

Comme vous pouvez voir dans le schéma ci-dessus.  Microsoft conserve les deux répertoires en interne (à gauche) qu’il utilise pour publier des applications.

* Une pour Microsoft Apps (répertoire de services Microsoft)
* Une pour applications préalable intégrées 3e partie (répertoire Galerie d’applications)

Application éditeurs/fournisseurs qui intègrent Azure AD doivent avoir un répertoire de publication.  (Certains répertoire SAAS).

Les applications que vous ajoutez vous-même :

* Applications que vous avez développées (intégrées à AAD)
* Applications vous connecté pour unique-authentification
* Applications que vous avez publié à l’aide de l’application proxy AD Azure.

### <a name="a-couple-of-notes-and-exceptions"></a>Quelques notes et exceptions

* Pas toutes les identités de service pointent vers objets application.  Non ? Lorsque Azure AD a été créé les services fournis aux applications ont été beaucoup plus limitées et l’entité du service était suffisante pour l’établissement d’une identité de l’application.  Le service d’origine principal n’a plus près de la forme au compte de service Windows Server Active Directory.  Pour cette raison, il est toujours possible de créer des identités de service à l’aide de la session PowerShell Azure AD sans commencer par créer un objet application.  L’API Graph nécessite un objet application avant de créer un service principal.
* Toutes les informations décrites ci-dessus est actuellement exposé par programme.  Les éléments suivants ne sont disponibles dans l’interface utilisateur :
    * Règles de transformation de revendications
    * Mappages d’attribut (approvisionnement de l’utilisateur)
* Pour plus d’informations détaillées sur l’entité de service et objets d’application, consultez la documentation de référence API REST Azure AD Graph.  *Conseil*: le Azure AD Graph API documentation est l’élément le plus proche à la référence de schéma pour Azure AD qui n’est actuellement disponible.  
    * [Application](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Principaux de service](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Comment les applications sont ajoutées à ma instance Azure AD ?
Il existe plusieurs façons qu'une application peut être ajoutée à Azure AD :

* Ajouter une application à partir de la [Galerie d’application Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Signe haut/dans un 3e partie application intégrée à Azure Active Directory (par exemple : [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Lors de l’inscription haut/dans utilisateurs êtes invité à donner l’autorisation de l’application pour accéder à leur profil et les autres autorisations.  La première personne donner une autorisation entraîne un principal de service qui représente l’application à ajouter à l’annuaire.
* Se haut/Microsoft services en ligne tel [qu’Office 365](http://products.office.com/)
    * Lorsque vous vous abonner à Office 365 ou commencez une version d’évaluation d’une ou plusieurs entités de service sont créées dans l’annuaire représentant les différents services qui sont utilisés pour effectuer toutes les fonctionnalités associées à Office 365.
    * Certains services Office 365, tels que SharePoint créer entités de service de manière permanente pour sécuriser les communications entre les composants, y compris les flux de travail.
* Ajouter une application vous développez la voir portail de gestion Azure : https://msdn.microsoft.com/library/azure/dn132599.aspx
* Ajouter une application vous développez à l’aide de Visual Studio Voir :
    * [Méthodes d’authentification ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Services connectés](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Ajouter une application à utiliser pour utiliser le [Proxy d’Application Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Se connecter à une application pour l’authentification unique à l’aide de SAML ou l’authentification unique de mot de passe
* Nombre d’autres personnes, y compris les différentes expériences pour les développeurs dans Azure et/dans explorer API expériences dans tous les centres de développement

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Qui est autorisé à ajouter des applications à mon instance Azure AD ?

Seuls les administrateurs globaux peuvent :

* Ajouter des applications dans la galerie d’application Azure AD (applications intégrées préalable 3e partie)
* Publier une application à l’aide du Proxy d’Application Azure AD

Tous les utilisateurs dans votre annuaire disposent de droits pour ajouter des applications qu’ils sont développe et discrétion sur les applications ils partager/donnent accès à leurs données d’organisation.  *N’oubliez pas de connexion d’un utilisateur à distance/dans une application et octroyer des autorisations, peut entraîner une entité de service en cours de création.*

Cette peut initialement son concernant, mais conserver suivantes à l’esprit :

* Applications ont été en mesure de tirer parti d’Active Directory Windows Server pour l’authentification des utilisateurs de nombreuses années sans avoir besoin de l’application soit enregistré/enregistrées dans l’annuaire.  Maintenant l’organisation sera ont améliorée visibilité à exactement combien applications sont à l’aide de l’annuaire et quelles raisons.
* Aucune nécessité d’administrateur par l’effort de processus de publication / d’inscription de l’application.  Dans Active Directory Federation Services, il est probable qu’un administrateur devait ajouter une application comme une partie de confiance au nom de développeurs.  Désormais les développeurs peuvent libre-service.
* Les utilisateurs de signature dans/jusqu'à applications à l’aide de leurs comptes d’organisation à des fins commerciales sont une bonne chose.  S’ils quittent par la suite de l’organisation qu’ils perdront l’accès à leur propre compte dans l’application qu’ils utilisaient.
* Disposer d’un enregistrement de données a été partagées avec lequel application est une bonne chose.  Données peut être transférées plus que jamais et il est utile de disposer d’un enregistrement effacer d’ayant partagé quelles sont les données avec les applications.
* Applications qui utilisent Azure AD pour oAuth décider exactement quelles sont les autorisations dont les utilisateurs sont en mesure d’accorder aux applications et nécessitant des autorisations d’administrateur pour qu’il corresponde à.  Il doit être placé sans dire que seuls les administrateurs peuvent consentement vers les zones plus grande et autorisations plus importantes.
* Utilisateurs ajout et autoriser les applications à accéder à leurs données sont vérifiés événements afin d’afficher les rapports d’Audit au sein du portail de gestion Azure pour déterminer la façon dont une application a été ajoutée à l’annuaire.

**Remarque :** *Microsoft lui-même a été d’exploitation à l’aide de la configuration par défaut pour le nombre de mois maintenant.*

Avec tout cela dit il est possible d’empêcher les utilisateurs dans votre annuaire d’ajout d’applications et exercer discrétion quelles sont les informations qu’ils de partager avec les applications en modifiant la configuration du répertoire dans le portail de gestion Azure.  La configuration suivante est accessible dans le portail de gestion Azure sous l’onglet « Configurer » de votre annuaire.

![Capture d’écran de l’interface utilisateur pour la configuration des paramètres de l’application intégrée][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des applications à Azure AD et explique comment configurer des services pour les applications.

* Les développeurs : [Découvrez comment intégrer une application avec AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Les développeurs : [exemples de code de révision pour les applications intégrées à Azure Active Directory sur Github](https://github.com/AzureADSamples)
* Les développeurs et les professionnels de l’informatique : [révision la documentation API REST de l’API de Graph Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Les professionnels de l’informatique : [Découvrez comment utiliser les applications déjà intégrées Azure Active Directory à partir de la galerie d’applications](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Les professionnels de l’informatique : [découvrir les didacticiels pour la configuration des applications déjà intégrées spécifiques](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Informaticiens : [Découvrez comment publier une application à l’aide du Proxy d’Application Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Voir aussi

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
