<properties
    pageTitle="Azure AD Connect synchronisation : compréhension des utilisateurs et des Contacts | Microsoft Azure"
    description="Explique les utilisateurs et des contacts dans Azure AD Connect synchroniser."
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
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure AD Connect synchronisation : compréhension des utilisateurs et des Contacts

Il existe plusieurs raisons différentes pourquoi vous devez plusieurs forêts Active Directory et il existe plusieurs topologies de déploiement différentes. Modèles courants incluent un déploiement compte ressource et liste d’adresses globale sync'ed forêts après une fusion et d’acquisition. Mais même s’il existe des modèles intégral, modèles hybrides sont également communs. La configuration par défaut dans Azure AD Connect synchroniser ne suppose pas n’importe quel modèle particulier, mais en fonction de comment utilisateur correspondant a été sélectionné dans le guide d’installation, des comportements différents peuvent être observées.

Dans cette rubrique, nous allons le comportement de la configuration par défaut dans certaines topologies. Nous allons grâce à la configuration et l’éditeur de règles de synchronisation peuvent servir à vérifier la configuration.

Il existe quelques règles générales que se base sur la configuration :

- Quelle que soit quel ordre nous importer à partir de la source annuaires Active Directory, le résultat final doit toujours être la même.
- Un compte actif contribuent toujours connexion informations, y compris **userPrincipalName** et **sourceAnchor**.
- Un compte désactivé contribue userPrincipalName et sourceAnchor, sauf s’il est une boîte aux lettres liée, s’il n’existe aucun compte actif à rechercher.
- Un compte avec une boîte aux lettres liée sera jamais utilisé pour userPrincipalName et sourceAnchor. Il est supposé égal que compte actif se trouve ultérieurement.
- Un objet de contact peut-être être mis en service à Azure Active Directory en tant que contact ou en tant qu’utilisateur. Vous ne savez pas vraiment jusqu'à ce que toutes les forêts Active Directory source ont été traitées.

## <a name="contacts"></a>Contacts

Présentant des contacts représentant un utilisateur dans une autre forêt est courante après une fusion & acquisition où une solution GALSync est pont deux ou plusieurs forêts Exchange. L’objet de contact est toujours rejoindre à partir de l’espace de connecteur au métaverse à l’aide de l’attribut mail. S’il existe déjà un objet contact ou utilisateur avec la même adresse de messagerie, les objets sont jointes. Cette option est configurée dans la règle **dans depuis Active Directory – Contact rejoindre**. Il existe également une règle nommée **dans depuis Active Directory – Contact courantes** avec un flux d’attribut au métaverse attribut **TypeObjetSource** avec la constante **Contact**. Cette règle a la priorité très faible si n’importe quel objet utilisateur est joint au même objet métaverse, puis la règle **dans depuis Active Directory – utilisateur courantes** contribue la valeur utilisateur à cet attribut. Avec cette règle, cet attribut a la valeur Contact si aucun utilisateur n’a pas été joint et la valeur utilisateur si au moins un utilisateur a été trouvé.

De mise en service un objet à Azure Active Directory, la règle sortante **les AAD – contactez rejoindre** créera un objet contact si l' attribut de méta-verse **TypeObjetSource** est défini sur **Contact**. Si cet attribut est défini sur **utilisateur**, puis la règle **les AAD – utilisateur joindre** créera un objet utilisateur à la place.
Il est possible qu’un objet est promu de Contact à utilisateur lorsque plusieurs annuaires Active Directory source sont importées et synchronisés.

Par exemple, dans une topologie GALSync nous recherche objets de contact pour tout le monde dans la deuxième forêt quand nous importer la première forêt. Cela s’effectuera nouveaux objets contact dans le connecteur AAD. Quand nous ultérieurement importer et synchroniser la deuxième forêt, nous rechercher les utilisateurs réels et les joindre aux objets métaverse existant. Nous sera ensuite supprimer l’objet de contact dans AAD et créez un nouvel objet utilisateur à la place.

Si vous disposez d’une topologie de l’endroit où les utilisateurs et représentée sous forme de contacts, assurez-vous de sélectionner pour faire correspondre les utilisateurs de l’attribut mail dans le guide d’installation. Si vous sélectionnez une autre option, vous devrez une configuration dépendant de l’ordre. Objets de contact rejoindront toujours sur l’attribut mail, mais les objets utilisateur rejoindront uniquement sur l’attribut mail si cette option a été activée dans le guide d’installation. Vous pouvez puis vous retrouver avec deux objets distincts dans le méta-verse avec l’attribut mail même si l’objet de contact a été importé avant que l’objet utilisateur. Lors de l’exportation vers Azure Active Directory, une erreur est levée. Ce comportement est voulu par la conception et indique les données incorrectes ou que la topologie n’était pas correctement identifiée pendant l’installation.

## <a name="disabled-accounts"></a>Comptes désactivés

Comptes désactivés sont également synchronisés avec Azure AD. Comptes désactivés sont communes à représenter des ressources dans Exchange, par exemple des salles de conférence. L’exception est utilisateurs avec une boîte aux lettres liée ; comme indiqué précédemment, ces configurera jamais un compte à Azure Active Directory.

L’hypothèse est que si un compte d’utilisateur désactivé est trouvé, nous ne trouve pas plus tard un autre compte actif puis appuyer sur l’objet est mis en service pour Azure AD avec l’userPrincipalName et sourceAnchor trouvé. En cas d’un autre compte actif rejoindront au même objet métaverse, ses userPrincipalName et sourceAnchor seront utilisés.

## <a name="changing-sourceanchor"></a>Modification de sourceAnchor

Lorsqu’un objet a été exporté à Azure Active Directory puis il n’est pas autorisé à modifier la sourceAnchor plus. Lorsque l’objet a été exporté l' attribut de méta-verse **cloudSourceAnchor** est définie avec la valeur **sourceAnchor** acceptée par Azure AD. Si **sourceAnchor** est modifié et ne correspond pas à **cloudSourceAnchor**, la règle **les AAD – utilisateur joindre** lève l’erreur **attribut sourceAnchor a changé**. Dans ce cas, la configuration ou les données doivent être corrigées afin que le même sourceAnchor est présent dans le méta-verse à nouveau avant que l’objet puisse être synchronisé à nouveau.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation Azure Active Directory se connecter : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
