<properties
    pageTitle="Référence au protocole Azure AD SAML | Microsoft Azure"
    description="Cet article fournit une vue d’ensemble des profils de l’authentification unique et SAML Sign-Out unique dans Azure Active Directory."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Comment Azure Active Directory utilise le protocole SAML

Protocole d’utilise la SAML 2.0 Azure Active Directory (AD Azure) pour permettre aux applications fournir une expérience d’authentification unique pour leurs utilisateurs. Les profils [De l’authentification unique](active-directory-single-sign-on-protocol-reference.md) et SAML [unique déconnexion](active-directory-single-sign-out-protocol-reference.md) d’Azure AD expliquent comment assertions SAML, protocoles et liaisons sont utilisés dans le service fournisseur d’identité.

Protocole SAML nécessite le fournisseur d’identité (Azure AD) et le fournisseur de services (l’application) pour échanger des informations sur eux-mêmes.

Lorsqu’une application est enregistrée avec Azure AD, le développeur de l’application enregistre les informations relatives à la fédération avec Azure AD. Cela inclut les **Rediriger URI** et **URI de métadonnées** de l’application.

Azure AD utilise l' **URI de métadonnées** du service cloud pour récupérer la clé de signature et de la déconnexion URI du service cloud. Si l’application ne prend pas en charge un URI de métadonnées, le développeur doit contacter le support technique Microsoft pour offrir la déconnexion URI et la clé de signature.

Azure Active Directory expose spécifiques au client et courantes (client indépendante) unique authentification unique déconnexion points de terminaison et. Ces URL représente des emplacements dédiés : elles ne sont pas simplement une identificateurs--afin que vous pouvez accéder au point de terminaison pour lire les métadonnées.

 - Le point de terminaison spécifiques au client se trouve dans `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  La <TenantDomainName> espace réservé représente un nom de domaine enregistré ou TenantID GUID d’un client Azure AD. Par exemple, les métadonnées de fédération du client contoso.com sont situe : https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- Le point de terminaison client indépendante se trouve dans `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Dans cette adresse de point de terminaison **courantes** s’affiche, au lieu d’un nom de domaine client ou ID.

Pour plus d’informations sur les documents de métadonnées de fédération AD Azure publie, voir [Métadonnées de fédération](active-directory-federation-metadata.md).
