
<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer les exigences de contrôle d’accès | Microsoft Azure"
    description="Traite les piliers d’identité et l’identification des conditions d’accès des ressources pour les utilisateurs dans un environnement hybride."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Déterminer les exigences de contrôle d’accès pour votre solution identité hybride
Lorsqu’une organisation consiste à concevoir leur solution identité hybride ils peuvent également utiliser cette opportunité passer en revue les conditions d’accès pour les ressources qu’ils souhaitent rendre disponibles pour les utilisateurs. L’accès aux données croisées tous les quatre piliers d’identité, qui sont :

- Administration
- Authentification
- Autorisation
- L’audit

Les sections qui suit aborde les authentification et autorisation de façon plus détaillée, administration et audit font partie du cycle de vie identité hybride. Lire les [tâches de gestion des identités déterminer hybride](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) pour plus d’informations sur ces fonctions.

>[AZURE.NOTE]
Pour plus d’informations sur chacun de ces piliers, lisez [Les quatre piliers de l’identité - gestion des identités dans l’âge de l’informatique hybride](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) .

## <a name="authentication-and-authorization"></a>Authentification et autorisation
Il existe différentes scénarios d’authentification et d’autorisation, ces scénarios aura des exigences spécifiques qui doivent être respectées par la solution d’identité hybride recevant adoptent l’entreprise. Scénarios de communication de (B2B) commerciaux peuvent ajouter un défi supplémentaire pour les administrateurs informatiques dans la mesure où elles devront pour vous assurer que la méthode d’authentification et d’autorisation utilisée par l’organisation peut communiquer avec leurs partenaires commerciaux. Au cours du processus de conception pour l’authentification et l’autorisation de configuration, vérifiez que les questions suivantes sont traitées :

- Votre organisation s’authentifier et autoriser uniquement les utilisateurs à leur système de gestion des identités ?
 - Existe-t-il des plans pour les scénarios B2B ?
 - Si Oui, procédez comme vous savez déjà quels sont les protocoles (SAML, jeton, Kerberos, jetons ou certificats) permet de vous connecter les deux entreprises ?
- Signifie la solution d’identité hybride que vous vous apprêtez à adopter prise en charge les protocoles ?

Un autre point important à prendre en considération est l’endroit où le référentiel d’authentification qui est utilisé par les utilisateurs et les partenaires seront situé et le modèle d’administration à utiliser. Consultez les options de deux principaux suivantes :
- Centralisation : dans ce modèle d’informations d’identification de l’utilisateur, stratégies et l’administration peuvent être centralisée en local ou sur le cloud.
- Hybride : dans ce modèle informations d’identification de l’utilisateur, stratégies et l’administration seront centralisée en local et un répliquée dans le cloud.

Le modèle de votre organisation adoptera varient en fonction de leurs besoins commerciaux, que vous souhaitez répondre aux questions suivantes pour identifier dans lequel se trouve le système de gestion des identités et le mode d’administration à utiliser :

- Votre organisation actuellement possède une gestion des identités en local ?
 - Si Oui, ils planifier conserver ?
 - Existe-t-il des conditions réglementaires ou de conformité que votre organisation doit suivre qui détermine l’endroit où le système de gestion des identités doit résider ?
- Votre organisation utilise-t-elle de l’authentification unique pour applications situées en local ou dans le cloud ?
 - Si Oui, l’adoption d’un modèle d’identité hybride affecte-t-elle ce processus ?

## <a name="access-control"></a>Contrôle d’accès
Tandis que l’authentification et autorisation sont des éléments fondamentaux pour activer l’accès aux données d’entreprise via la validation de l’utilisateur, il est également important de contrôler le niveau d’accès que ces utilisateurs disposeront et le niveau d’administrateurs d’accès créera des ressources qu’ils gèrent. Votre solution identité hybride doit être en mesure de fournir un accès précis aux ressources, de délégation et de contrôle d’accès de base un rôle. Assurez-vous que concernant le contrôle d’accès sont répond à la question suivante :

- Votre entreprise a-t-elle plusieurs utilisateurs avec privilèges élevés pour gérer votre système d’identité ?
 - Si Oui, chaque utilisateur doit-il le même niveau d’accès ?
- Votre entreprise devront déléguer l’accès aux utilisateurs pour gérer des ressources spécifiques ?
 - Si Oui, la fréquence cela se produit ?
- Votre entreprise nécessaires pour intégrer des fonctionnalités de contrôle d’accès entre en local et nuage de ressources ?
- Votre entreprise nécessaires pour limiter l’accès aux ressources en fonction de certaines conditions ?
- Votre entreprise aurait n’importe quelle application nécessitant un accès contrôle personnalisé vers des ressources ?
 - Si Oui, où ces applications se trouvent (en local ou sur le cloud) ?
 - Si Oui, où sont celles cibler ressources situées (en local ou sur le cloud) ?

>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et comprendre les raisons de la réponse. [Définir une stratégie de Protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) seront passez en revue les options disponibles et les avantages et inconvénients de chaque option.  En répondant à ces questions vous allez sélectionner l’option que le mieux à vos besoins professionnels.

## <a name="next-steps"></a>Étapes suivantes

[Déterminer les besoins de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
