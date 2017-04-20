
<properties
    pageTitle="Azure Active Directory Access conditionnelle techniques de référence | Microsoft Azure"
    description="Contrôle d’accès conditionnelle, Azure Active Directory vérifie les conditions spécifiques que vous avez choisi lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory Access conditionnelle techniques de référence

## <a name="services-enabled-with-conditional-access"></a>Services activés avec accès conditionnel
Règles d’accès conditionnelle sont pris en charge sur les différents types d’application Azure AD. Cette liste comprend :

- Applications fédérées à partir de la galerie des applications Azure AD
- Applications de l’authentification unique de mot de passe à partir de la galerie des applications Azure AD
- Applications enregistrées avec le Proxy d’Application Azure
- Développé par secteur d’activité et applications clients multiples enregistrées avec Azure AD
- Visual Studio en ligne
- Application Remote Azure
-   Dynamics CRM
- Microsoft Office 365 Yammer
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online (inclut OneDrive entreprise)


## <a name="enable-access-rules"></a>Activer les règles d’accès

Chaque règle peut être activée ou désactivée sur un par bases d’applications. Lorsque les règles sont **ON** elles seront activés et mises en œuvre pour les utilisateurs d’accéder à l’application. Lorsqu’elles sont **désactivé** , ils ne seront pas utilisées et aura aucun impact sur les utilisateurs de connexion expérience.

## <a name="applying-rules-to-specific-users"></a>Appliquer des règles à des utilisateurs spécifiques
Règles peuvent être appliquées à des ensembles de basé sur le groupe de sécurité en définissant **Appliquer à**des utilisateurs spécifiques. **Appliquer à** peuvent être définies pour **Tous les utilisateurs** ou **groupes**. Lorsqu’elle est définie à **Tous les utilisateurs** les règles seront appliquent à tous les utilisateurs d’accéder à l’application. L’option **groupes** permet de sécurité spécifiques et des groupes de distribution soit sélectionné, règles seront uniquement appliquées à ces groupes.

Lorsque vous déployez une règle, il est courant pour l’appliquer tout d’abord un jeu limité d’utilisateurs, qui sont membres des groupes d’une phase pilote. Une fois terminée la règle peut être appliquée à **Tous les utilisateurs**. Cela entraîne la règle soit appliquée pour tous les utilisateurs de l’organisation.

Sélectionner des groupes peuvent également être exemptés de stratégie à l’aide de l’option **à l’exception** . Tous les membres de ces groupes seront exempt même s’ils apparaissent dans un groupe inclus.

## <a name="at-work-networks"></a>Réseaux « au travail »


Règles d’accès conditionnelle qui utilisent un réseau « au travail », s’appuient sur approuvé plages d’adresses IP qui ont été configurés dans Azure Active Directory, ou l’utilisation de la demande « à l’intérieur du réseau d’entreprise » à partir d’AD FS. Les règles suivantes sont les suivantes :

- Nécessite l’authentification multifacteur pas au bureau
- Bloquer l’accès pas au bureau

Options pour spécifiant « au travail » réseaux

1. Configurer les plages d’adresses IP approuvés dans la [page de configuration de l’authentification multifacteur](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Stratégie d’accès conditionnelle utilise les plages configurées sur chaque demande d’authentification et d’émission du jeton évaluer les règles. 
2. Configurer l’utilisation de l’intérieur réclamer réseau d’entreprise, cette option peut être utilisée avec les répertoires fédérés, via AD FS. [En savoir plus sur l’intérieur revendications coronet](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configurer les plages d’adresses IP publics. Sous l’onglet Configurer, pour votre annuaire, vous pouvez définir des adresses IP publiques. Accès conditionnel utilisera ces comme 'at work' adresses IP, cela permet de plages de valeurs supplémentaires à configurer, au-dessus de la limite d’adresse IP 50 qui est appliquée à la page Paramètres de l’authentification Multifacteur.



## <a name="rules-based-on-application-sensitivity"></a>Règles basées sur le critère de diffusion application

Les règles sont configurées par application, ce qui permet des services à forte valeur pour être sécurisées sans avoir un impact sur l’accès à d’autres services. Règles d’accès conditionnelle peuvent être configurés sous l’onglet **configuration** de l’application. 

Règles proposé pour le moment :

- **Nécessite l’authentification multifacteur**
 - Cette stratégie est appliquée à tous les utilisateurs devront s’authentifier via l’authentification multifacteur au moins une fois.
 
- **Nécessite l’authentification multifacteur pas au bureau**
 - Si cette stratégie est appliquée, tous les utilisateurs devront avoir effectué au moins une fois l’authentification multifacteur s’ils accèdent au service à partir d’un emplacement distant non liés au travail. S’ils se déplacent à partir d’un travail à distance, ils doivent effectuer l’authentification multifacteur lors de l’accès au service.
 
- **Bloquer l’accès pas au bureau** 
 - Lorsque les utilisateurs placent du travail vers un emplacement distant, ils seront bloquées si la stratégie « Bloquer l’accès pas au bureau » leur est appliquée.  Ils seront nouveau autorisés à accéder depuis un emplacement de travail.


## <a name="related-topics"></a>Rubriques connexes

- [Sécuriser l’accès à Office 365 et d’autres applications connecté à Azure Active Directory](active-directory-conditional-access.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
