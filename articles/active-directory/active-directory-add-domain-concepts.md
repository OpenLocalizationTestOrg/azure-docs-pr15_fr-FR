<properties
    pageTitle="Vue d’ensemble conceptuelle de noms de domaine personnalisé dans Azure Active Directory | Microsoft Azure"
    description="Décrit le cadre conceptuel d’utilisation des noms de domaine personnalisé dans Azure Active directory, y compris la fédération pour l’authentification unique"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Vue d’ensemble conceptuelle de noms de domaine personnalisé dans Azure Active Directory

Un nom de domaine est une partie importante de l’identificateur pour de nombreuses ressources annuaire : il fait partie d’une adresse de messagerie ou le nom d’utilisateur pour un utilisateur, partie de l’adresse d’un groupe et peut faire partie de l’application URI ID pour une application. Une ressource dans Azure Active Directory (AD Azure) peut inclure un nom de domaine est vérifié déjà appartenir par le répertoire qui contient la ressource. Seul un administrateur global peut effectuer les tâches de gestion de domaine dans Azure AD.

Noms de domaine dans Azure AD sont globalement uniques. Un nom de domaine peut être utilisé par une annonce Azure unique. Si un répertoire Azure AD a vérifié un nom de domaine, puis aucune autre annuaire Azure AD permettre de vérifier ou utiliser ce même nom de domaine.

## <a name="initial-and-custom-domain-names"></a>Noms de domaine initial et personnalisés

Chaque nom de domaine dans Azure Active Directory est un nom de domaine initial, ou un nom de domaine personnalisé.

Chaque Azure AD est fourni avec un nom de domaine initial dans le formulaire contoso.onmicrosoft.com. Ce nom de domaine de niveau troisième, dans cet exemple, « contoso.onmicrosoft.com », défini lors de l’annuaire a été créé, par l’administrateur qui a créé l’annuaire. Le nom de domaine initial d’un répertoire ne peut pas être modifié ou supprimé. Le nom de domaine initial, et bien qu’entièrement fonctionnel, est destiné principalement à utiliser comme mécanisme bootstrapping jusqu'à ce qu’un nom de domaine personnalisé est vérifié.

Dans la plupart des environnements de production, un répertoire comporte au moins un domaine personnalisé vérifié, tel que « contoso.com », ainsi que ce domaine personnalisé est visible aux utilisateurs finaux. Un nom de domaine personnalisé est un nom de domaine appartenant à et utilisé par cette organisation, tel que « contoso.com », pour des utilisations telles que son site web d’hébergement. Ce nom de domaine est familier aux employés car elle fait partie du nom d’utilisateur qu’ils utilisent pour vous connecter au réseau d’entreprise, ou pour envoyer et extraire messagerie.

Avant qu’il peut être utilisée par Azure Active Directory, le nom de domaine personnalisé doit être ajouté à votre répertoire et vérifié.

## <a name="verified-and-unverified-domain-names"></a>Noms de domaine vérifié et non vérifié

Le nom de domaine initial d’un répertoire est évalué implicitement comme vérifié par Azure AD. Lorsqu’un administrateur ajoute un nom de domaine personnalisé pour une annonce Azure, il est initialement dans un état non vérifié. Azure AD ne permet pas de toutes les ressources annuaire utiliser un nom de domaine non vérifiés. Ainsi qu’un seul répertoire peut utiliser un nom de domaine, et que votre organisation utilise le nom de domaine propriétaire ce nom de domaine.

Azure AD vérifie la propriété de nom de domaine en recherchant une entrée particulière dans le fichier de zone domaine nom service (DNS) pour le nom de domaine. Pour vérifier la propriété d’un nom de domaine, un administrateur Obtient l’entrée DNS à partir d’Azure AD qu’Azure AD recherche et ajoute cette entrée dans le fichier de zone DNS pour le nom de domaine. Le fichier de zone DNS est géré par le bureau d’enregistrement de nom de domaine pour ce domaine. Les étapes pour vérifier un domaine sont représentées dans l’article permettant [d’Ajouter un domaine personnalisé à votre annuaire Azure AD](active-directory-add-domain.md).

Ajout d’une entrée DNS pour le fichier de zone du nom de domaine n’affecte pas les autres services de domaine tels que la messagerie électronique ou d’hébergement de sites web.

## <a name="federated-and-managed-domain-names"></a>Noms de domaine fédéré et gérées

Un nom de domaine personnalisé dans Azure AD peut être configuré pour permettre aux utilisateurs de connexion fédérée dans expérience entre votre Active Directory local et Azure AD. Configuration d’un domaine pour la fédération nécessite des mises à jour sur les ressources dotés de privilèges d’Azure AD et également à Windows Server Active Directory. Configurer qu'un domaine fédéré doit être effectuée à partir d’Azure AD Connect ou à l’aide de PowerShell. Fédération de domaine personnalisé ne peut pas être initiée à partir du portail classique Azure. [Regardez cette vidéo pour en savoir plus sur la configuration AD FS pour vous connecter utilisateur avec Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domaines qui ne sont pas fédérés sont parfois appelés domaines gérés. Le domaine initial d’un répertoire Azure AD est évalué implicitement comme un domaine géré.

## <a name="primary-domain-names"></a>Noms de domaine principal

Le nom de domaine principal d’un répertoire est le nom de domaine préalablement sélectionné est la valeur par défaut pour la partie 'domaine' du nom d’utilisateur, lorsqu’un administrateur crée un nouvel utilisateur dans le [portail classique Azure](https://manage.windowsazure.com/) ou un autre portail tels que le portail d’administration Office 365. Un répertoire peut avoir qu’un seul nom de domaine principal. Un administrateur peut modifier le nom de domaine principal pour les vérifier un domaine personnalisé qui n’est pas fédéré, ou sur le domaine initial.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Noms de domaine dans Azure AD et d’autres Services en ligne Microsoft

Un nom de domaine doit être vérifié dans Azure AD avant il peut être utilisé par un autre Service en ligne Microsoft comme Intune, Exchange Online et SharePoint Online. Ces autres services nécessitent généralement un administrateur d’ajouter un ou plusieurs des entrées DNS sont spécifiques au service.

Une application web Azure utilise son propre mécanisme pour vérifier la propriété d’un domaine. Un domaine doit être vérifié pour une utilisation avec Azure AD même si elle a été précédemment vérifiée pour une utilisation par une application web Azure dans un abonnement qui s’appuie sur ce Azure AD. Une application web Azure peut utiliser un nom de domaine qui a été vérifié dans un répertoire différent à partir du répertoire qui permet de sécuriser l’application web.

## <a name="managing-domain-names"></a>Gestion des noms de domaine

Tâches de gestion de domaine peuvent être effectuées à partir du portail classique Azure et à partir de PowerShell. De nombreuses tâches peuvent être effectuées à l’aide de l’API Azure AD Graph (dans la version d’évaluation).

-   [Ajout et vérification de nom de domaine personnalisé](active-directory-add-domain.md)

-   [Gestion des domaines dans le portail classique Azure](active-directory-add-manage-domain-names.md)

-   [Utilisation de PowerShell pour gérer des noms de domaine dans Azure Active Directory](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [À l’aide de l’API Azure AD Graph pour gérer les noms de domaine dans Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
