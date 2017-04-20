<properties
   pageTitle="Azure Active Directory Graph API | Microsoft Azure"
   description="Une vue d’ensemble et démarrage rapide pour le guide pour l’API Graph qui autorise l’accès par programme à Azure AD via les points de terminaison API REST."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [AZURE.IMPORTANT] Azure AD Graph API fonctionnalité est également disponible via [Microsoft Graph](https://graph.microsoft.io/), une API unifiée qui inclut des API à partir d’autres services Microsoft tels que Outlook OneDrive, OneNote, planificateur et Office Graph, accessible via un point de terminaison unique et avec un jeton d’accès unique.

L’API de Graph Azure Active Directory offre un accès par programme aux Azure AD via les points de terminaison API REST. Applications peuvent utiliser l’API Graph pour effectuer créer, lire, mettre à jour et supprimer les opérations sur les objets et les données de l’annuaire. Par exemple, l’API Graph prend en charge les opérations courantes suivantes pour un objet utilisateur :

- Créer un nouvel utilisateur dans un répertoire

- Obtenir les propriétés détaillées d’un utilisateur, tels que leurs groupes

- Mettre à jour les propriétés d’un utilisateur, tels que leur emplacement et le numéro de téléphone, ou modifier leur mot de passe

- Vérifier l’appartenance aux groupes d’un utilisateur pour l’accès basé sur un rôle

- Désactiver un compte d’utilisateur ou la supprimer entièrement

Outre les objets utilisateur, vous pouvez effectuer les opérations similaires sur d’autres objets tels que les groupes et les applications. Pour appeler l’API de graphique d’un répertoire, l’application doit être enregistrée avec Azure AD et être configurée pour autoriser l’accès à l’annuaire. En règle générale, cela, un flux de consentement utilisateur ou administrateur.

Pour commencer à l’aide de l’API de Graph Azure Active Directory, voir le [Guide de démarrage rapide de l’API de graphique](active-directory-graph-api-quickstart.md), ou consultez la [documentation de référence API Graph interactif](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Fonctionnalités

L’API graphique fournit les fonctionnalités suivantes :

- **Points de terminaison de l’API REST**: l’API Graph est un service RESTful composé de points de terminaison qui sont accessibles à l’aide de requêtes HTTP standards. L’API Graph prend en charge les types de contenu XML ou Notation JSON (Javascript Object) pour les demandes et les réponses. Pour plus d’informations, voir [référence de l’API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Authentification avec Azure AD**: chaque demande à l’API Graph doit être authentifié en ajoutant un JSON Web jeton (JWT) dans l’en-tête d’autorisation de la demande. Ce jeton est acquis par effectue une demande au point de terminaison jeton de Azure AD et fournir les informations d’identification valides. Vous pouvez utiliser le flux des informations d’identification des clients OAuth 2.0 ou le code d’autorisation accorder flux acquérir un jeton pour appeler le graphique. Pour plus d’informations, [OAuth 2.0 dans Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Autorisation basée sur les rôles (RBAC)**: groupes de sécurité sont utilisées pour effectuer RBAC dans l’API de graphique. Par exemple, si vous souhaitez déterminer si un utilisateur a accès à une ressource spécifique, l’application peut appeler l’opération de [Vérifier l’appartenance aux groupes (transitifs)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , ce qui retourne true ou false.

- **Requête différentielle**: Si vous souhaitez vérifier les modifications apportées dans un répertoire entre deux périodes de temps sans avoir à effectuer des requêtes fréquentes à l’API de graphique, vous pouvez faire une demande de requête différentielle. Ce type de requête renvoie uniquement les modifications apportées entre la demande de requête différentielle précédente et la requête actuelle. Pour plus d’informations, voir [Azure AD Graph API différentielle requête](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Extensions Directory**: Si vous développez une application qui doit pour lire ou écrire des propriétés uniques pour les objets d’annuaire, vous pouvez enregistrer et utiliser des valeurs d’extension à l’aide de l’API de graphique. Par exemple, si votre application requiert une propriété ID Skype pour chaque utilisateur, vous pouvez enregistrer la nouvelle propriété dans l’annuaire et qu’il soit disponible sur chaque objet utilisateur. Pour plus d’informations, voir [Extensions de schéma Azure AD Graph API Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Sécurisé par étendues d’autorisation**: AAD Graph API expose étendues d’autorisation qui permettent de sécuriser/accepté l’accès aux données AAD et prend en charge divers types d’application client, y compris :
 - ceux avec une interface utilisateur qui figurent délégué l’accès aux données via l’autorisation de l’utilisateur connecté dans (délégué)
  - ceux qui utilisent application-permet de définir le contrôle d’accès basé sur un rôle telles que les clients de service/processus (rôles application)

    Les délégués et application rôle d’autorisation étendues représente un privilège exposé par l’API Graph et peut être demandé par les applications clientes par le biais des autorisations d’inscription applications [fonctionnalités du portail classique Azure](https://manage.windowsazure.com). Clients peuvent vérifier les étendues autorisation octroyée leur en inspectant la réclamation étendue (« scp ») reçue dans le jeton d’accès pour les autorisations déléguées et les rôles (« rôles ») réclamer des autorisations de rôle d’application. Découvrez les [étendues Azure AD Graph API d’autorisation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Scénarios

L’API graphique permet de nombreux scénarios d’application. Les scénarios suivants sont les plus courants :

- **Application métier (client unique)**: dans ce scénario, un développeur d’entreprise fonctionne pour une organisation qui dispose d’un abonnement Office 365. Le développeur génère une application web qui interagit avec Azure AD pour effectuer des tâches telles attribuez une licence à un utilisateur. Cette tâche requiert l’accès à l’API Graph, afin que les historiques développeur simples application dans Azure Active Directory du client et configure lire et écrire des autorisations pour l’API de graphique. L’application est configuré pour utiliser ses propres informations d’identification ou celles de l’utilisateur actuellement connexion pour obtenir un jeton pour appeler l’API Graph.

- **Logiciel comme une Application de Service (à plusieurs client)**: dans ce scénario, un éditeur de logiciels (fil) développe application web multi-client hébergés qui fournit des fonctionnalités de gestion des utilisateurs pour d’autres organisations qui utilisent Azure AD. Ces fonctionnalités nécessitent un accès aux objets d’annuaire, puis l’application doit donc appeler l’API Graph. Le développeur enregistre l’application dans Azure AD, configure afin d’exiger en lecture et en écriture pour l’API Graph et puis autorise l’accès externe afin que d’autres organisations peuvent consentement pour utiliser l’application dans leur répertoire. Lorsqu’un utilisateur dans une autre organisation s’authentifie à l’application pour la première fois, une boîte de dialogue consentement avec les autorisations que requis par l’application sont affichées.  Octroi consentement donne ensuite l’application les autorisations demandées à l’API Graph dans l’annuaire de l’utilisateur. Pour plus d’informations sur l’infrastructure d’autorisation, voir [vue d’ensemble de l’infrastructure d’autorisation](active-directory-integrating-applications.md).

## <a name="see-also"></a>Voir aussi

[Guide de démarrage rapide Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Documentation présentée sous d’AD reste Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)
