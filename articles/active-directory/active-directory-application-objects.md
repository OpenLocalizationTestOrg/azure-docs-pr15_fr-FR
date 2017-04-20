<properties
pageTitle="Application Azure Active Directory et des objets de Principal du Service | Microsoft Azure"
description="Une discussion de la relation entre les applications et les objets principal de service dans Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Application et les objets principal de service dans Azure Active Directory
Lorsque vous lisez sur un Azure Active Directory (AD) « application », il n’est pas toujours effacer exactement ce qui est fait référence par l’auteur. L’objectif de cet article consiste à le rendre plus clair, en définissant les aspects conceptuelles et concrets Azure AD d’intégration des applications, avec un exemple de l’enregistrement et consentement pour une [application cliente multiples](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Vue d’ensemble
Une application Azure AD est plus large que simplement un élément du logiciel. Il s’agit d’un terme conceptuel, faisant référence pas uniquement à des logiciels d’application, mais également son enregistrement (aka : configuration d’identité) avec Azure Active Directory, qui permet de participer à l’authentification et l’autorisation « conversations » lors de l’exécution. Par définition, une application peut fonctionner dans un rôle de [client](active-directory-dev-glossary.md#client-application) (utilise une ressource), un rôle [serveur de ressources](active-directory-dev-glossary.md#resource-server) (API exposition aux clients) ou encore les deux. Le protocole de conversation est défini par un [flux accorder l’autorisation de 2.0 OAuth](active-directory-dev-glossary.md#authorization-grant), dans le but de permettre la ressource/client/protection d’accès aux données d’une ressource respectivement. Passons à un niveau inférieur et voir comment le modèle d’application Azure AD représente une application en interne. 

## <a name="application-registration"></a>Inscription de l’application
Lorsque vous enregistrez une application dans le [portail classique Azure][AZURE-Classic-Portal], deux objets sont créés dans votre client Azure AD : un objet application et un objet principal de service.

#### <a name="application-object"></a>Objet application
Une application Azure AD est *définie* par celle dont et objet application uniquement qui réside dans le client Azure AD où l’application a été enregistrée, appelées client « domicile » de l’application. L’objet application fournit des informations liées à l’identité pour une application et est le modèle à partir de laquelle son ou les objets principal service correspondante sont *dérivées* pour une utilisation au moment de l’exécution. 

Vous pouvez considérer l’application en tant que la représentation *globale* de votre application (pour une utilisation sur tous les clients) et l’entité du service en tant que la représentation *local* (pour une utilisation dans un client spécifique). L' Azure AD Graph [entité d’Application] [ AAD-Graph-App-Entity] définit le schéma pour un objet application. Par conséquent, un objet application a une relation 1:1 avec l’application et le caractère 1 : relation*n* avec son correspondante *n* service principal ou les objets.

#### <a name="service-principal-object"></a>Objet principal de service
L’objet principal service définit la stratégie et les autorisations pour une application, qui fournit la base d’une entité de sécurité représenter l’application pour accéder aux ressources au moment de l’exécution. L' Azure AD Graph [entité ServicePrincipal] [ AAD-Graph-Sp-Entity] définit le schéma pour un objet principal de service. 

Un objet principal de service est requis dans chaque client pour lequel une instance de l’utilisation de l’application doit être représentée, l’activation d’un accès sécurisé aux ressources appartenant à des comptes d’utilisateurs de ce client. Une application unique client aura qu’un seul principal du service (dans son client accueil). Un multi-client [application Web](active-directory-dev-glossary.md#web-client) a également un principal de service dans chaque client où un administrateur ou un ou plusieurs utilisateurs de ce client ont consentement, ce qui lui permet d’accéder à leurs ressources. Suivre consentement, l’objet principal service est consulté pour les demandes d’autorisation futures. 

> [AZURE.NOTE] Toutes les modifications apportées à votre objet application, sont également répercutées dans son objet principal de service dans accueil client l’application uniquement (le client dans laquelle il a été enregistré). Pour les applications clients multiples, les modifications apportées à l’objet application ne sont pas répercutées dans objets principal de service des clients de n’importe quel consommateur, jusqu'à ce que le client consommateur supprime l’accès et accorde l’accès à nouveau.

## <a name="example"></a>Exemple
Le diagramme suivant illustre la relation entre l’objet application d’une application et correspondante objets principal, dans le contexte d’une application client multiples nommée **HR application**de service. Il existe trois clients Azure AD dans ce scénario : 

- **Adatum** - le client utilisé par la société qui développe l' **application de ressources humaines**
- **Contoso** - le client utilisé par l’entreprise Contoso, ce qui correspond à un grand public de l' **application de ressources humaines**
- **Fabrikam** - le client utilisé par l’organisation Fabrikam, qui utilise également l' **application de ressources humaines**

![Relation entre un objet application et un objet principal de service](./media/active-directory-application-objects/application-objects-relationship.png)

Dans le diagramme précédent, étape 1 est le processus de création de l’application et les objets principal service client accueil de l’application.

À l’étape 2, lorsque les administrateurs de Contoso et Fabrikam effectuer consentement, un objet principal de service est créé dans le client Azure AD de leur société et affecté les autorisations accordée de l’administrateur. Notez également que l’application de ressources humaines peut être configuré/conçue pour autoriser consentement par des utilisateurs individuels.

Étape 3, les clients grand public de l’application HR (Contoso et Fabrikam) chaque ont leur propre objet principal de service. Chaque représente acceptée leur utilisation d’une instance de l’application en cours d’exécution, régis par les autorisations définies par l’administrateur correspondante.

## <a name="next-steps"></a>Étapes suivantes
Objet application d’une application est accessible via l’API Azure AD Graph, tel que représenté par son OData [entité d’Application][AAD-Graph-App-Entity]

Objet principal d’une application service sont accessibles via l’API Azure AD Graph, tel que représenté par son OData [entité ServicePrincipal][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com